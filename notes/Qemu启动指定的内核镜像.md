Tags: #qemu #kernel 

---

# Qemu 启动指定 Linux 内核镜像

通常情况下，当我们修改一个程序时，我们想运行它来验证我们的修改。在实际硬件上启动编译好的 Linux 内核镜像之前，在 QEMU 这样的虚拟机中做一个快速启动作为理智的检查，可以节省我们的时间和潜在的头痛。如果你的内核在 QEMU 中启动，并不能保证它能在裸机上启动，但它可以快速保证内核镜像没有完全损坏。下面的内容记录了如何进行操作。

```shell
# one time setup
$ sudo mkinitramfs -o ramdisk.img
$ echo "add-auto-load-safe-path path/to/linux/scripts/gdb/vmlinux-gdb.py" >> ~/.gdbinit

# one time kernel setup
$ cd linux
$ ./script/config -e DEBUG_INFO -e GDB_SCRIPTS
$ <make kernel image>

# each debug session run
$ qemu-system-x86_64 \
	-kernel arch/x86_64/boot/bzImage \
	-nographic \
	-append "console=ttyS0 nokaslr" \
	-initrd ramdisk.img \
	-m 512 \
	-enable-kvm \
	-cpu host \
	-s -S &
```

## 在 Qemu 中启动

先尝试下直接指定新的 kernel:

```shell
$ qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage
```

弹出一新窗口，观察到一些输出，觉得这些窗口比较难看，所以我们来内容输出到终端。

```shell
$ qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic
```

这里缺失了一个重要的 flag, 让我们看看缺失了这个  flag 会发生什么。终端里看上去啥都没有，qemu 对 `ctrl + c` 也没响应。不过 qemu 确实跑起来了。那么如何退出呢，两个方法：

1. 先按 `ctrl + a`, 然后按 `a`, 终端会进入 (qemu)，这时候可以输入 `q`, 然后按回车就可以退出了。
2. 先按 `ctrl + a`, 然后按 `x` 就可以直接退出了。

接下来，我们给 kernel 传一个启动参数。Kernel 就像用户态的可执行程序一样，也可以接受参数，只不过这通常是通过 [[bootloader]] 来完成的。

```shell
$ qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0"
```

```shell
...
[    1.711025] Please append a correct "root=" boot option; here are the available partitions:
[    1.711298] 0b00         1048575 sr0
[    1.711424]  driver: sr
[    1.711686] Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)
[    1.713290] CPU: 0 PID: 1 Comm: swapper/0 Not tainted 5.19.0-rc2+ #3
[    1.713674] Hardware name: QEMU Standard PC (i440FX + PIIX, 1996), BIOS rel-1.16.0-0-gd239552ce722-prebuilt.qemu.org 04/01/2014
[    1.714260] Call Trace:
[    1.715023]  <TASK>
[    1.715268]  dump_stack_lvl+0x49/0x5f
[    1.715752]  dump_stack+0x10/0x12
[    1.715871]  panic+0x10c/0x2b2
[    1.716027]  mount_block_root+0x14b/0x1f1
[    1.716204]  mount_root+0x147/0x153
[    1.716353]  prepare_namespace+0x13f/0x170
[    1.716509]  kernel_init_freeable+0x25e/0x284
[    1.716682]  ? rest_init+0xe0/0xe0
[    1.716831]  kernel_init+0x1a/0x130
[    1.716979]  ret_from_fork+0x22/0x30
[    1.717193]  </TASK>
[    1.718118] Kernel Offset: 0xfe00000 from 0xffffffff81000000 (relocation range: 0xffffffff80000000-0xffffffffbfffffff)
[    1.718929] ---[ end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0) ]---
```

这次终端里终于有内容了。这时候，我们发生了 panic, 这是因为没有根文件系统（[[rootfs]]），所以，没有 `init` 可以运行。我们可以用裸机[[创建一个自定义的文件系统镜像]]，但是创建一个[[ramdisk]] 是最直接的方法。好吧，让我们来创建 [[ramdisk]]，然后把它添加到 QEMU 的参数中。

```shell
$ sudo mkinitramfs -o ramdisk.img
$ qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0" -initrd ramdisk.img
```

```shell
...
[    1.097904] Call Trace:
[    1.097904]  <TASK>
[    1.097904]  dump_stack_lvl+0x49/0x5f
[    1.097904]  dump_stack+0x10/0x12
[    1.097904]  panic+0x10c/0x2b2
[    1.097904]  out_of_memory.cold+0x61/0x83
[    1.097904]  __alloc_pages_slowpath.constprop.0+0xc61/0xde0
[    1.097904]  __alloc_pages+0x300/0x350
[    1.097904]  alloc_pages+0x90/0x120
[    1.097904]  new_slab+0x2fa/0x540
[    1.097904]  ? kmem_cache_alloc_lru+0x3d7/0x540
[    1.097904]  ___slab_alloc+0x685/0x810
[    1.097904]  ? alloc_inode+0x97/0xb0
[    1.097904]  ? __wake_up+0x13/0x20
[    1.097904]  ? alloc_inode+0x97/0xb0
[    1.097904]  __slab_alloc.isra.0+0x4f/0x90
[    1.097904]  kmem_cache_alloc_lru+0x4b2/0x540
[    1.097904]  ? alloc_inode+0x97/0xb0
[    1.097904]  alloc_inode+0x97/0xb0
[    1.097904]  new_inode_pseudo+0x12/0x60
[    1.097904]  new_inode+0x17/0x30
[    1.097904]  tracefs_get_inode+0x10/0x60
[    1.097904]  tracefs_create_file+0x75/0x1a0
[    1.097904]  trace_create_file+0x13/0x30
[    1.097904]  event_create_dir+0x139/0x4e0
[    1.097904]  __trace_early_add_event_dirs+0x2f/0x50
[    1.097904]  event_trace_init+0x91/0xda
[    1.097904]  tracer_init_tracefs_work_func+0xa/0x1ce
[    1.097904]  process_one_work+0x21d/0x3f0
[    1.097904]  worker_thread+0x4a/0x3b0
[    1.097904]  ? process_one_work+0x3f0/0x3f0
[    1.097904]  kthread+0xff/0x130
[    1.097904]  ? kthread_complete_and_exit+0x20/0x20
[    1.097904]  ret_from_fork+0x22/0x30
[    1.097904]  </TASK>
[    1.097904] ---[ end Kernel panic - not syncing: System is deadlocked on memory ]---
```

不幸的是，我们（很可能）会遇到同样的 panic，panic 没有提供足够的信息，但 QEMU 默认使用的最大内存过于有限。`-m 2G` 将使我们的虚拟机有足够的内存来启动内核，并得到一个基于 [[busybox]] 的 shell。

```shell
$ qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0" -initrd ramdisk.img -m 2G
```

```shell
...
Loading, please wait...
Starting version 245.4-4ubuntu3.17
Begin: Loading essential drivers ... done.
Begin: Running /scripts/init-premount ... done.
Begin: Mounting root file system ... Begin: Running /scripts/local-top ... done.
Begin: Running /scripts/local-premount ... done.
No root device specified. Boot arguments must include a root= parameter.


BusyBox v1.30.1 (Ubuntu 1:1.30.1-4ubuntu6.4) built-in shell (ash)
Enter 'help' for a list of built-in commands.

(initramfs) uname -r
5.19.0-rc2+
```

加上 `-enable-kvm` 可以获得更好的性能。

```shell
$ qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0" -initrd ramdisk.img -m 2G -enable-kvm
```

如果我们看到下面的告警信息，可以加上 `-cpu host` 参数。

```shell
qemu-system-x86_64: warning: host doesn't support requested feature: CPUID.80000001H:ECX.svm [bit 2]
```

```shell
$ qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0" -initrd ramdisk.img -m 2G -enable-kvm -cpu host
```

最后，我们可以通过 `-s` 来启动一个 [gdbserver](https://en.wikipedia.org/wiki/Gdbserver)，访问的端口是 **1234**。另外，通过 `-S` 来暂停内核，直到我们在 [[gdb]] 中继续。

## Attaching GDB to QEMU

现在我们可以用 Qemu 启动内核了，我们来通过 [[gdb]] 来调试内核。

```shell
gdb vmlinux
```

如果看到下面这个告警信息

```shell
warning: File "/home/nick/linux/scripts/gdb/vmlinux-gdb.py" auto-loading has been declined by your `auto-load safe-path' set to "$debugdir:$datadir/auto-load".
To enable execution of this file add 
	add-auto-load-safe-path /path/to/linux/scripts/gdb/vmlinux-gdb.py 
line to your configuration file "/home/<username>/.gdbinit".
```

执行下面的一次性命令，之后每次加载使用 [[gdb]] 的时候都会加载这些 [[gdb ]] 脚本。

```shell
$ cd linux
$ echo "add-auto-load-safe-path `pwd`/scripts/gdb/vmlinux-gdb.py" >> ~/.gdbinit
```

现在 QEMU 正在监听 1234 端口（通过 `-s`），让我们连接到它，并在内核初始化的早期设置一个断点。注意 `hbreak` 的使用。

```shell
...
Reading symbols from linux/vmlinux...
(gdb) target remote :1234
Remote debugging using :1234
0x000000000000fff0 in exception_stacks ()
(gdb) hbreak start_kernel
Hardware assisted breakpoint 1 at 0xffffffff82b60ff3: file init/main.c, line 929.
(gdb) c
Continuing.

Breakpoint 1, start_kernel () at init/main.c:929
929     {
(gdb)
```

我们可以用 `c` 启动/恢复内核，用 `ctrl + c` 暂停内核。内核通过 `CONFIG_GDB_SCRIPTS` 提供的 [[gdb]] 脚本可以用 `propos lx` 查看。`lx-dmesg` 对于查看内核的 `dmesg` 缓冲区来说非常方便，特别是在串口驱动被调出之前内核发生 panic 的情况下（在这种情况下，QEMU 的输出会到 stdout）。

```shell
(gdb) apropos lx
function lx_clk_core_lookup -- Find struct clk_core by name
function lx_current -- Return current task.
function lx_device_find_by_bus_name -- Find struct device by bus and name (both strings)
function lx_device_find_by_class_name -- Find struct device by class and name (both strings)
function lx_module -- Find module by name and return the module variable.
function lx_per_cpu -- Return per-cpu variable.
function lx_rb_first -- Lookup and return a node from an RBTree
function lx_rb_last -- Lookup and return a node from an RBTree.
function lx_rb_next -- Lookup and return a node from an RBTree.
function lx_rb_prev -- Lookup and return a node from an RBTree.
function lx_task_by_pid -- Find Linux task by PID and return the task_struct variable.
function lx_thread_info -- Calculate Linux thread_info from task variable.
function lx_thread_info_by_pid -- Calculate Linux thread_info from task variable found by pid
lx-clk-summary -- Print clk tree summary
lx-cmdline --  Report the Linux Commandline used in the current kernel.
lx-configdump -- Output kernel config to the filename specified as the command
lx-cpus -- List CPU status arrays
lx-device-list-bus -- Print devices on a bus (or all buses if not specified)
lx-device-list-class -- Print devices in a class (or all classes if not specified)
lx-device-list-tree -- Print a device and its children recursively
lx-dmesg -- Print Linux kernel log buffer.
lx-fdtdump -- Output Flattened Device Tree header and dump FDT blob to the filename
lx-genpd-summary -- Print genpd summary
lx-iomem -- Identify the IO memory resource locations defined by the kernel
lx-ioports -- Identify the IO port resource locations defined by the kernel
lx-list-check -- Verify a list consistency
lx-lsmod -- List currently loaded modules.
lx-mounts -- Report the VFS mounts of the current process namespace.
lx-ps -- Dump Linux tasks.
lx-symbols -- (Re-)load symbols of Linux kernel and currently loaded modules.
lx-timerlist -- Print /proc/timer_list
lx-version --  Report the Linux Version of the current kernel.
(gdb) lx-version
Linux version 5.19.0-rc2+ (ninghan@ninghan-desk) (gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0, GNU ld (GNU Binutils for Ubuntu) 2.34) #4 SMP PREEMPT_DYNAMIC Fri Sep 23 16:47:37 CST 2022
```


---

References:
- [Booting a Custom Linux Kernel in QEMU and Debugging It With GDB](http://nickdesaulniers.github.io/blog/2018/10/24/booting-a-custom-linux-kernel-in-qemu-and-debugging-it-with-gdb/) 
- [Debugging kernel and modules via gdb](https://docs.kernel.org/dev-tools/gdb-kernel-debugging.html)
- [buildroot](https://buildroot.org/)