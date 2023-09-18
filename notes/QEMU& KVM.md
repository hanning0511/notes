## Share directory with the host without networking in qemu.

If the guest has [9p](https://zh.wikipedia.org/wiki/9P) support (like Linux, or of course, Plan 9) and [virtio](https://docs.oasis-open.org/virtio/virtio/v1.2/virtio-v1.2.html), try adding the following switch.

```shell
-virtfs local,path=/path/to/share/folder/on/host,mount_tag=host0,security_model=mapped,fmode='0755',id=host0
```

_mount_tag_ is what the guest sees, so the fstab on the guest should look like this:

```shell
host0   /wherever    9p      trans=virtio,version=9p2000.L   0 0
```

Either add that to your /etc/fstab, or invoke the appropriate command manually or using your init system, whatever it may be.

Links:
1. https://superuser.com/questions/628169/how-to-share-a-directory-with-the-host-without-networking-in-qemu
2. https://askubuntu.com/questions/1313547/linux-kvm-make-mapped-shared-folder-permissions-rw-for-host-and-guest
3. [Introduction to VirtIO](https://blogs.oracle.com/linux/post/introduction-to-virtio#:~:text=Formally%2C%20VirtIO%2C%20or%20virtual%20input,host%27s%20devices%20for%20virtual%20machines.)

## Install Ubuntu 22.04 