## Linux 命令

### grubby

> `grubby` is a command line tool for updating and display information about the configuration files for the grub2 and zipl boot loaders. It is primaryily designed to be used from scripts which install new kernels and need to find information about the current boot environment.

- 查看安装的所有内核的信息

```shell
$ grubby --info=ALL
```

- 查看默认内核

```shell
$ grubby --default-kernel
```

- 查看当前使用的内核信息

```shell
$ grubby --info=`grubby --default-kernel`
```

- 修改内核启动参数
```shell
# 添加启动参数
$ grubby --update-kernel=/boot/vmlinuz-`uname -r` --args="console=ttyS0"

# 删除启动参数
$ grubby --update-kernel=/boot/vmlinuz-`name -r` --remove-args="console=ttyS0"
```

- 为内核设置 **initrd**

```shell
$ grubby --update-kernel=/boot/<kernel image> --initrd=<path/to/initrd>
```