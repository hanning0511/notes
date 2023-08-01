Tags: #ssh #network
Refs: https://www.redhat.com/sysadmin/ssh-proxy-bastion-proxyjump

# SSH to remote hosts through a proxy or bastion with ProxyJump

[堡垒机](https://en.wikipedia.org/wiki/Bastion_host)的概念对计算机来说并不新鲜。堡垒机通常是面向公众的加固系统，作为防火墙或其他受限位置后的系统的入口，随着云计算的兴起，它们特别受欢迎。

`ssh` 命令有一个简单的方法，可以利用堡垒机，用一个命令连接到远程主机。`ssh` 可以通过使用`ProxyJump` 自己创建初始和第二个连接，而不是先通过 `ssh` 连接到堡垒主机，然后在堡垒机上使用 `ssh` 来连接到远程主机。

## ProxyJump
`ProxyJump`，即 `-J` 标志，是在 **ssh7.3** 版本中引入的。要使用它，在 `-J` 标志后指定要连接的堡垒机，然后加上远程主机。

```shell
ssh -J <bastion-host> <remote-host>
```

也可以指定用户名和端口

```shell
ssh -J <user@bastion-host:port> <user@remote-host:port>
```

`ssh` 手册上提到，可以通过逗号分隔的方式指定多个堡垒机

```shell
ssh -J <bastion-host1>,<bastion-host2> <remote-host>
```

### Hard-coding proxy hosts in `~/.ssh/config`

`-J` 标志提供了灵活性，可以根据需要方便地指定代理和远程主机，但如果一个特定的堡垒机经常被用来连接特定的远程主机，`ProxyJump` 配置可以在 `~/.ssh/config` 中设置，在前往远程主机的途中自动与堡垒进行连接。

```
### The Bastion Host
Host bastion-host-nickname
	HostName bastion-hostname
	
### The Remote Host
Host remote-host-nickname
	HostName remote-hostname
	ProxyJump bastion-host-nickname
```

使用上面的配置示例，当像这样进行 SSH 连接时。

```shell
ssh remote-host-nickname
```

`ssh` 命令在连接到远程主机之前，首先创建一个连接到堡垒机 **bastion-hostname**（在远程主机的 `ProxyJump` 设置中通过昵称引用的主机）。