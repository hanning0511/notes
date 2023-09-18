Tags: #cron #scheduler
Ref: https://github.com/jhuckaby/Cronicle

---

## 安装

在安装 **cronicle** 之前，系统需要预先安装好了 **Node.js LTS**，参考: [[使用 nvm 安装 nodejs]]。

执行下面的命令安装最新的稳定版本 cronicle:

```shell
curl -s https://raw.githubusercontent.com/jhuckaby/Cronicle/master/bin/install.js | node
```

>安装需要以 root 身份进行。

如果想要手动安装，执行下面这些命令：

```shell
mkdir -p /opt/cronicle
cd /opt/cronicle
curl -L https://github.com/jhuckaby/Cronicle/archive/v1.0.0.tar.gz | tar zxvf - --strip-components 1
npm install
node bin/build.js dist
```

把 `v1.0.0` 替换成任意其他[版本](https://github.com/jhuckaby/Cronicle/releases)。

## 配置

