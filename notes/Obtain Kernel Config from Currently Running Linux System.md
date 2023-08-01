Tags: #Linux #Kernel #Kconfigs

---
## 1. Overview

In a Linux system, a kernel config file is an important component of building the kernel. Using this file, we can modify our system configuration as per our requirement or check the currently configured devices.

In this tutorial, we’ll see how can we get the kernel configuration for our currently running Linux system using two simple methods.

## 2. Kernel Configuration

In a Linux system, a kernel configuration file is used to build a kernel. It contains a list of kernel options, a list of devices, and device options. We can consider it a template that the _make_ command uses to build the kernel.

**As part of the kernel building process, we can set up our config file** using different configuration options like `make config` or  `make menuconfig`. Using these options, we can customize the kernel configuration and generate the files required to compile and link the kernel.

Almost all Linux distributions provide kernel configuration files as part of their package. We can look into distribution-specific documentation in order to find such configurations. The Linux kernel source will have the default configuration provided.

## 3. Obtaining Kernel Configuration

The method of obtaining the kernel configuration will vary depending on the Linux distribution. For most of the known Linux distributions, we can get the kernel configuration using one of two methods.

### 3.1. From the _/proc_ Directory

In Linux, the _/proc_ (process information) pseudo-filesystem contains the files that represent the current state of the kernel.

To obtain the currently running kernel config from _/proc_, we can decompress the _config.gz_ file. However, **this is only possible if access to the _.config_ file through _/proc/config.gz_ was enabled** during the generation of the initial configuration. This option comes under “_General setup_” in kernel configuration:

```
General setup --->
      ...
      [*] Kernel .config support
          [*] Enable access to .config through /proc/config.gz
      ...
```

Using the [_zcat_](https://linux.die.net/man/1/zcat) command, we can decompress the _config.gz_ file. Let’s capture the output as a file to see the contents easily:

```shell
$ zcat /proc/config.gz > filename.config
```

On successful execution of the above command, a config file with a _.config_ extension will be generated in the current directory:

```shell
cat filename.config

# Automatically generated file; DO NOT EDIT.
# Linux/x86_64 3.16.3 Kernel Configuration
#
CONFIG_64BIT=y
CONFIG_X86_64=y
CONFIG_X86=y
CONFIG_INSTRUCTION_DECODER=y
CONFIG_OUTPUT_FORMAT="elf64-x86-64"
CONFIG_ARCH_DEFCONFIG="arch/x86/configs/x86_64_defconfig"
CONFIG_LOCKDEP_SUPPORT=y
        ....
```

As an alternative, we can first capture the configuration from the _/proc_ directory using the _[cat](https://man7.org/linux/man-pages/man1/cat.1.html)_ command. Then, using the _[gunzip](https://linux.die.net/man/1/gunzip)_ tool, we can decompress the configuration in a _.config_ file. This will achieve the same result as _zcat_:

```shell
$ cat /proc/config.gz | gunzip > filename.config
```

### 3.2. From the _/boot_ Directory

In Linux, the /_boot_ directory contains important files required for the boot process of the operating system, a config file, and the map installer. Here, we can find the config file for our respective Linux kernel versions:

```shell
$ cat /boot/config-$(uname -r) > filename.config
```

Moreover, we can get the configuration for specific devices using the combination of the pipe operator and the [_grep_](https://www.baeldung.com/linux/common-text-search) command. For instance, to check the modules related to USB, we can run:

```shell
$ cat /boot/config-$(uname -r) | grep -i USB
 CAN USB interfaces
CONFIG_CAN_EMS_USB=m
CONFIG_CAN_ESD_USB2=m
CONFIG_CAN_GS_USB=m
CONFIG_CAN_KVASER_USB=m
CONFIG_CAN_PEAK_USB=m
CONFIG_CAN_8DEV_USB=m
CONFIG_USB_IRDA=m
        ....
```