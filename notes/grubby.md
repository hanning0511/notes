Tag: #tool 

---

## 设置默认内核

```shell
$ grubby --set-default /boot/vmlinuz-`uname -r`
```

## 修改内核启动参数

```shell
# 添加启动参数
$ grubby --update-kernel=/boot/vmlinuz-`uname -r` --args="console=ttyS0"
```

```shell
# 删除启动参数
$ grubby --update-kernel=/boot/vmlinuz-`name -r` --remove-args="console=ttyS0"
```

## 修复grubenv 文件

```shell
$ grub2-editenv create
```