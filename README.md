# VyOS - (Embedded)

Welcome to `vy-project`. This Git repository serves as superproject to
assemble an embedded version of VyOS by making use of several Yocto
layers.

**Note:** _This project is far away from an Alpha state!_

## About
VyOS is an open source network operating system that can be installed on
physical hardware or a virtual machine on your own server, or a cloud
platform .

It is based on GNU/Linux and joins multiple applications such as Quagga,
ISC DHCPD, OpenVPN, StrongS/WAN and others under a single management
interface.

We strive to make VyOS useful in any network, from a small office to a
data center.

Unlike OpenWRT or pfSense, VyOS is more similar to traditional hardware
routers, with a focus on comprehensive support for advanced routing
features such as dynamic routing protocols and command line interface.
However, we do not neglect other features such as VPN and firewalls.

## Submodules
A list of submodules and a brief description
* `sources/poky` : The Yocto `poky` reference distribution
* `sources/meta-openembedded` : Collection of layers for the OE-core universe
* `sources/meta-vyos` : Layer to bring VyOS router functionality to OE
* `sources/meta-swupdate`:  Software Update for Embedded Systems (non x86)

## Branches
Branch `master` only holds this notice, please use appropriate Yocto
branch name convention.

_Active branche(s):_
* `rocko`

## Building

### Required Packages
* **Ubuntu and Debian**
```
   $ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
     build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
     xz-utils debianutils iputils-ping libsdl1.2-dev xterm
```

* **Fedora**
```
  $ sudo dnf install gawk make wget tar bzip2 gzip python3 unzip perl patch \
    diffutils diffstat git cpp gcc gcc-c++ glibc-devel texinfo chrpath \
    ccache perl-Data-Dumper perl-Text-ParseWords perl-Thread-Queue perl-bignum socat \
    python3-pexpect findutils which file cpio python python3-pip xz SDL-devel xterm
```
                            
* **OpenSUSE**
```
   $ sudo zypper install python gcc gcc-c++ git chrpath make wget python-xml \
     diffstat makeinfo python-curses patch socat python3 python3-curses tar python3-pip \
     python3-pexpect xz which libSDL-devel xterm
```     
                            
* **CentOS**
```
   $ sudo yum install -y epel-release
   $ sudo yum makecache
   $ sudo yum install gawk make wget tar bzip2 gzip python unzip perl patch \
     diffutils diffstat git cpp gcc gcc-c++ glibc-devel texinfo chrpath socat \
     perl-Data-Dumper perl-Text-ParseWords perl-Thread-Queue python34-pip xz \
     which SDL-devel xterm
```

### Compile (x86)
```
  $ git clone -b <branch> --recursive https://github.com/c-po/vy-project.git
```

```
  $ cd vy-project
  $ . init-build-env build
  $ bitbake vyos-image
    Loading cache: 100% |######################################| Time: 0:00:00
    Loaded 2926 entries from dependency cache.
    Parsing recipes: 100% |######################################| Time: 0:00:01
    Parsing of 2063 .bb files complete (2062 cached, 1 parsed). 2925 targets, 274 skipped, 0 masked, 0 errors.
    NOTE: Resolving any missing task queue dependencies

    Build Configuration:
    BB_VERSION           = "1.36.0"
    BUILD_SYS            = "x86_64-linux"
    NATIVELSBSTRING      = "debian-8"
    TARGET_SYS           = "i586-oe-linux"
    MACHINE              = "qemux86"
    DISTRO               = "vyos"
    DISTRO_VERSION       = "0.0.1"
    TUNE_FEATURES        = "m32 i586"
    TARGET_FPU           = ""
    meta
    meta-poky
    meta-yocto-bsp       = "rocko:fdeecc901196bbccd7c5b1ea4268a2cf56764a62"
    meta-vyos            = "rocko:e375f8405854a4d03d2d6adc9bef1a0a749c8c18"
    meta-oe
    meta-networking
    meta-python
    meta-perl
    meta-filesystems     = "rocko:dacfa2b1920e285531bec55cd2f08743390aaf57"
    meta-swupdate        = "rocko:ca89e377482015ff9807179fc224cc2f5f9c65a6"

    Initialising tasks: 100% |######################################| Time: 0:00:08
    NOTE: Executing SetScene Tasks
    NOTE: Executing RunQueue Tasks
    ...
 ```
 
 During compilation you can get some cups of coffee ...

### Test (QEMU)
As initially stated this is far from an _Alpha_ release. To test the compiled result you can spin up QEMU and login to test.

```
  $ runqemu nographic
    runqemu - INFO - Running bitbake -e...
    runqemu - INFO - Continuing with the following parameters:

    KERNEL: [/home/cpo/vy-project/build/tmp-glibc/deploy/images/qemux86/bzImage--4.14.24+git0+5f6c3e3236_edc90f45a7-r0-qemux86-20180418200122.bin]
    MACHINE: [qemux86]
    FSTYPE: [ext4]
    ROOTFS: [/home/cpo/vy-project/build/tmp-glibc/deploy/images/qemux86/vyos-image-qemux86-20180419081222.rootfs.ext4]
    CONFFILE: [/home/cpo/vy-project/build/tmp-glibc/deploy/images/qemux86/vyos-image-qemux86-20180419081222.qemuboot.conf]

    runqemu - INFO - Setting up tap interface under sudo
    runqemu - INFO - Network configuration: 192.168.7.2::192.168.7.1:255.255.255.0
    runqemu - INFO - Running /home/cpo/vy-project/build/tmp-glibc/work/x86_64-linux/qemu-helper-native/1.0-r1/recipe-sysroot-native/usr/bin/qemu-system-i386 -device virtio-net-pci,netdev=net0,mac=52:54:00:12:34:02 -netdev tap,id=net0,ifname=tap0,script=no,downscript=no -drive file=/home/cpo/vy-project/build/tmp-glibc/deploy/images/qemux86/vyos-image-qemux86-20180419081222.rootfs.ext4,if=virtio,format=raw -vga vmware -show-cursor -usb -device usb-tablet -device virtio-rng-pci  -nographic  -cpu pentium2 -m 256 -serial mon:stdio -serial null -kernel /home/cpo/vy-project/build/tmp-glibc/deploy/images/qemux86/bzImage--4.14.24+git0+5f6c3e3236_edc90f45a7-r0-qemux86-20180418200122.bin -append 'root=/dev/vda rw highres=off  console=ttyS0 mem=256M ip=192.168.7.2::192.168.7.1:255.255.255.0 vga=0 uvesafb.mode_option=640x480-32 oprofile.timer=1 uvesafb.task_timeout=-1 '

    [    0.000000] Linux version 4.14.24meta-vyos (oe-user@oe-host) (gcc version 7.3.0 (GCC)) #1 SMP Wed Apr 18 22:16:10 CEST 2018
    [    0.000000] x86/fpu: x87 FPU will use FXSAVE
    [    0.000000] e820: BIOS-provided physical RAM map:
    [    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
    [    0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
    [    0.000000] BIOS-e820: [mem 0x00000000000f0000-0x00000000000fffff] reserved
    [    0.000000] BIOS-e820: [mem 0x0000000000100000-0x000000000ffdbfff] usable
    ...
    Mounting VyOS Config...done.
    Starting VyOS router: migrate rl-system firewall[   25.532656] ip_tables: (C) 2000-2006 Netfilter Core Team
    [   26.136253] ip6_tables: (C) 2000-2006 Netfilter Core Team
     configure.
    Starting vyos-intfwatchd: vyos-intfwatchd.

    Welcome to VyOS - vyos-qemux86 /dev/ttyS0

    vyos-qemux86 login:
```

### Login
Please use username `vyos` with password `vyos`. This is valid for every build.
