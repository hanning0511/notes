Tags: #Kubernetes
Refs: # [基于docker和cri-dockerd部署k8sv1.26.3](https://www.cnblogs.com/qiuhom-1874/p/17279199.html)
# Kubernetes


## 概念

### 服务（Service）

Kubernetes 中 Service 是将运行在一个或一组 [Pod](https://kubernetes.io/zh-cn/docs/concepts/workloads/pods/) 上的网络应用程序公开为网络服务的方法。

Kubernetes 中 Service 的一个关键目标是让你无需修改现有应用程序就能使用不熟悉的服务发现机制。 你可以在 Pod 中运行代码，无需顾虑这是为云原生世界设计的代码，还是为已容器化的老应用程序设计的代码。 你可以使用 Service 让一组 Pod 在网络上可用，让客户端能够与其交互。

Service API 是 Kubernetes 的组成部分，它是一种抽象，帮助你通过网络暴露 Pod 组合。 每个 Service 对象定义一个逻辑组的端点（通常这些端点是 Pod）以及如何才能访问这些 Pod 的策略。

#### 类型

##### ClusterIP

通过集群的内部 IP 暴露服务，选择该值时服务只能够在集群内部访问。 这也是你没有为服务显式指定 `type` 时使用的默认值。 你可以使用 [Ingress](https://kubernetes.io/zh-cn/docs/concepts/services-networking/ingress/) 或者 [Gateway API](https://gateway-api.sigs.k8s.io/) 向公众暴露服务。

##### NodePort

通过每个节点上的 IP 和静态端口（`NodePort`）暴露服务。 为了让节点端口可用，Kubernetes 设置了集群 IP 地址，这等同于你请求 `type: ClusterIP` 的服务。
##### LoadBalancer

使用云提供商的负载均衡器向外部暴露服务。 Kubernetes 不直接提供负载均衡组件；你必须提供一个，或者将你的 Kubernetes 集群与云提供商集成。

##### ExternalName

将服务映射到 `externalName` 字段的内容（例如，映射到主机名 `api.foo.bar.example`）。 该映射将集群的 DNS 服务器配置为返回具有该外部主机名值的 `CNAME` 记录。 无需创建任何类型代理。


## 基于 Ubuntu 22.04 搭建 kubernetes (v1.28) 集群


### 控制节点和工作节点

这部分的内容对控制节点（control plane) 和工作节点（work node）均适用。

#### 安装容器运行时（CRI)

在所有的节点上都需要安装容器运行时，这里以 [Docker Engine](https://docs.docker.com/engine/install/ubuntu/) 为例：

安装容器运行时之前，需要进行一些前置的配置：

1. Forwarding IPv4 and letting iptables see bridged traffic

```shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

执行下面的命令来安装 Docker Engine：

```shell
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

执行下面的命令来验证 Docker Engine 已经安装成功。

```shell
docker run hello-world
```

安装完 Docker Engine 之后检查 cgroup driver 是否为 **systemd**，如果不是，需要设置为 **systemd**，具体方法可以往上搜索。

```shell
docker info
...
Cgroup Driver: systemd
...
```


容器运行时需要实现 [CRI](https://kubernetes.io/docs/concepts/architecture/cri/) 才能和Kubernetes 一起工作，而 Docker Engine 没有实现 [CRI](https://kubernetes.io/docs/concepts/architecture/cri/), 所以我们需要额外安装 [cri-dockerd](https://github.com/Mirantis/cri-dockerd)。cri-dockerd 原来是被 kubelet 原生支持的，从 kubelete 1.24 版本开始原生支持被移除。

使用下面的命令安装 cri-dockerd：

```shell
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.4/cri-dockerd_0.3.4.3-0.ubuntu-jammy_amd64.deb
sudo dpkg -i cri-dockerd_0.3.4.3-0.ubuntu-jammy_amd64.deb
```

安装完 cri-dockerd 之后需要配置其使用指定的网络插件，修改 _/usr/lib/systemd/system/cri-docker.service_, 

```
ExecStart=/usr/bin/cri-dockerd --container-runtime-endpoint fd:// --network-plugin=cni --cni-bin-dir=/opt/cni/bin --cni-cache-dir=/var/lib/cni/cache --cni-conf-dir=/etc/cni/net.d
```

然后执行：

```shell
systemctl daemon-reload
systemctl restart cri-dockerd.service
```


#### 安装 kubernetes


```shell
sudo apt update
sudo apt-get install -y apt-transport-https ca-certificates curl

curl -fsSL https://dl.k8s.io/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubeadm kubelet kubectl
```



### 安装 Nginx Ingress Controller

Ref: https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-manifests/



## Misc


### 标记一个节点为 **worker**

```shell
kubectl label node <node> node-role.kubernetes.io/worker=worker
```

### 创建一个 token 并打印 join command

```shell
kubeadm token create --print-join-command
```

### 滚动更新

用户希望应用程序始终可用，而开发人员则需要每天多次部署它们的新版本。在 Kubernetes 中，这些是通过滚动更新（Rolling Updates）完成的。 **滚动更新** 允许通过使用新的实例逐步更新 Pod 实例，零停机进行 Deployment 更新。新的 Pod 将在具有可用资源的节点上进行调度。

与应用程序扩展类似，如果 Deployment 是公开的，服务将在更新期间仅对可用的 pod 进行负载均衡。可用 Pod 是应用程序用户可用的实例。

滚动更新允许以下操作：

- 将应用程序从一个环境提升到另一个环境（通过容器镜像更新）
- 回滚到以前的版本
- 持续集成和持续交付应用程序，无需停机

### 优雅地从集群中移除工作节点

```shell
kubectl cordon <node>
kubectl drain <node> --ignore-daemonsets
kubectl delete nodes <node>
```

## Helm

- https://devopspilot.com/content/kubernetes/tutorials/k8s/ingress/how-to-install-nginx-ingress-controller.html