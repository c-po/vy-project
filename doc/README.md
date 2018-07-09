The main intention about this project is porting VyOS to the Ubuquity EdgeRouter
PRO platform b/c it's a cheap and powerful platform.

# Ubiquity EdgeRouter PRO (ERPro-8)

## Serial Console

RJ45 serial console port for Command Line Interface (CLI) management. Use an
RJ45-to-DB9, serial console cable, also known as a rollover cable, to connect
the Console port to your computer. (If your computer does not have a DB9 port,
then you will also need a DB9 adapter.) Then configure the following settings as
needed:

* Baud rate: `115200`
* Data bits: `8`
* Parity: `NONE`
* Stop bits: `1`
* Flow control: `NONE`

## U-Boot

```
U-Boot 2012.04.01 (UBNT Build ID: 4605996-gd120a44) (Build time: Oct 14 2013 - 18:14:14)

Skipping PCIe port 0 BIST, in EP mode, can't tell if clocked.
Skipping PCIe port 1 BIST, reset not done. (port not configured)
BIST check passed.
UBNT_E200 r1:0, r2:14, serial #: xxxxxxxxxxxx
Core clock: 1000 MHz, IO clock: 600 MHz, DDR clock: 533 MHz (1066 Mhz DDR)
Base DRAM address used by u-boot: 0x8f800000, size: 0x800000
DRAM: 2 GiB
Clearing DRAM...... done
Flash: 8 MiB
Net:   octeth0, octeth1, octeth2, octeth3, octeth4, octeth5, octeth6, octeth7
MMC:   Octeon MMC/SD0: 0
USB:   USB EHCI 1.00
scanning bus for devices... 1 USB Device(s) found
Type the command 'usb start' to scan for USB storage devices.

Hit any key to stop autoboot:  0
```

### Environment
```
Octeon ubnt_e200# printenv
autoload=n
baudrate=115200
boardname=ubnt_e200
bootcmd=fatload mmc 0 $(loadaddr) vmlinux.64;bootoctlinux $(loadaddr) numcores=2 endbootargs mem=0 root=/dev/mmcblk0p2 rootdelay=10 rw rootsqimg=squashfs.img rootsqwdir=w mtdparts=phys_mapped_flash:640k(boot0),640k(boot1),64k(eeprom)
bootdelay=0
bootloader_flash_update=bootloaderupdate
env_addr=0x1fbfe000
env_size=0x2000
ethact=octeth0
flash_base_addr=0x1f400000
flash_size=0x800000
flash_unused_addr=0x1f530000
flash_unused_size=0x6d0000
loadaddr=0x20000000
mtdparts=phys_mapped_flash:640k(boot0),640k(boot1),64k(eeprom)
nuke_env=protect off $(env_addr) +$(env_size);erase $(env_addr) +$(env_size)
numcores=2
octeon_failsafe_mode=0
octeon_ram_mode=0
serial#=xxxxxxxxxxxx
stderr=serial
stdin=serial
stdout=serial
uboot_flash_addr=0x1f4a0000
uboot_flash_size=0x90000
ver=U-Boot 2012.04.01 (UBNT Build ID: 4605996-gd120a44) (Build time: Oct 14 2013 - 18:14:14)

Environment size: 961/8188 bytes
```

### Debug startup

* Skip `init` processd
```bash
U-Boot> setenv bootcmd fatload mmc 0 $(loadaddr) vmlinux.64\;bootoctlinux $(loadaddr) numcores=2 endbootargs mem=0 root=/dev/mmcblk0p2 rootdelay=10 rw rootsqimg=squashfs.img rootsqwdir=w mtdparts=phys_mapped_flash:640k(boot0),640k(boot1),64k(eeprom) init=/bin/sh
U-Boot> run bootcmd
```

* Single user mode
```bash
U-Boot> setenv bootcmd fatload mmc 0 $(loadaddr) vmlinux.64\;bootoctlinux $(loadaddr) numcores=2 endbootargs mem=0 root=/dev/mmcblk0p2 rootdelay=10 rw rootsqimg=squashfs.img rootsqwdir=w mtdparts=phys_mapped_flash:640k(boot0),640k(boot1),64k(eeprom) single
U-Boot> run bootcmd
```

## Firmware v1.10.3

### Linux Kernel
```
reading vmlinux.64

5529728 bytes read
argv[2]: numcores=2
argv[3]: endbootargs
Allocating memory for ELF segment: addr: 0xffffffff80800000 (adjusted to: 0x800000), size 0x655fd0
## Loading big-endian Linux kernel with entry point: 0xffffffff80bc1660 ...
Bootloader: Done loading app on coremask: 0x3
Starting cores 0x3
Linux version 3.10.107-UBNT (root@3c6805bec4ec) (gcc version 4.7.0 (Cavium Inc. Version: SDK_BUILD build 51) ) #1 SMP Thu Apr 26 17:23:18 UTC 2018
CVMSEG size: 2 cache lines (256 bytes)
Cavium Inc. SDK-3.1.2
bootconsole [early0] enabled
CPU revision is: 000d9301 (Cavium Octeon II)
Checking for the multiply/shift bug... no.
Checking for the daddiu bug... no.
Determined physical RAM map:
 memory: 0000000000400000 @ 0000000000300000 (usable)
 memory: 0000000000505000 @ 0000000000800000 (kernel data and code)
 memory: 000000000004b000 @ 0000000000d05000 (usable after init)
 memory: 0000000000106000 @ 0000000000d50000 (kernel data and code)
 memory: 000000000e000000 @ 0000000001100000 (usable)
 memory: 0000000000c00000 @ 000000000f300000 (usable)
 memory: 000000006f800000 @ 0000000020000000 (usable)
Using passed Device Tree <8000000000080000>.
software IO TLB [mem 0x02d10000-0x02d50000] (0MB) mapped at [8000000002d10000-8000000002d4ffff]
Zone ranges:
  DMA32    [mem 0x00300000-0xefffffff]
  Normal   empty
Movable zone start for each node
Early memory node ranges
  node   0: [mem 0x00300000-0x006fffff]
  node   0: [mem 0x00800000-0x00e55fff]
  node   0: [mem 0x01100000-0x0f0fffff]
  node   0: [mem 0x0f300000-0x0fefffff]
  node   0: [mem 0x20000000-0x8f7fffff]
Primary instruction cache 37kB, virtually tagged, 37 way, 8 sets, linesize 128 bytes.
Primary data cache 32kB, 32-way, 8 sets, linesize 128 bytes.
Secondary unified cache 1024kB, 16-way, 512 sets, linesize 128 bytes.
PERCPU: Embedded 10 pages/cpu @8000000002d75000 s10880 r8192 d21888 u40960
Built 1 zonelists in Zone order, mobility grouping on.  Total pages: 512659
Kernel command line:  root=/dev/mmcblk0p2 rootdelay=10 rw rootsqimg=squashfs.img rootsqwdir=w mtdparts=phys_mapped_flash:640k(boot0),640k(boot1),64k(eeprom) console=ttyS0,115200
PID hash table entries: 4096 (order: 3, 32768 bytes)
Dentry cache hash table entries: 262144 (order: 9, 2097152 bytes)
Inode-cache hash table entries: 131072 (order: 8, 1048576 bytes)
Memory: 2040272k/2072876k available (3885k kernel code, 32604k reserved, 1252k data, 300k init, 0k highmem)
Hierarchical RCU implementation.
        Additional per-CPU info printed with stalls.
NR_IRQS:511
Calibrating delay loop (skipped) preset value.. 2000.00 BogoMIPS (lpj=10000000)
pid_max: default: 32768 minimum: 501
Security Framework initialized
Mount-cache hash table entries: 256
Checking for the daddi bug... no.
SMP: Booting CPU01 (CoreId  1)...
CPU revision is: 000d9301 (Cavium Octeon II)
Brought up 2 CPUs
NET: Registered protocol family 16
PTP Clock: Using sclk reference at 600000000 Hz
bio: create slab <bio-0> at 0
usbcore: registered new interface driver usbfs
usbcore: registered new interface driver hub
usbcore: registered new device driver usb
Switching to clocksource OCTEON_CVMCOUNT
NET: Registered protocol family 2
TCP established hash table entries: 16384 (order: 6, 262144 bytes)
TCP bind hash table entries: 16384 (order: 6, 262144 bytes)
TCP: Hash tables configured (established 16384 bind 16384)
TCP: reno registered
UDP hash table entries: 1024 (order: 3, 32768 bytes)
UDP-Lite hash table entries: 1024 (order: 3, 32768 bytes)
NET: Registered protocol family 1
octeon_pci_console: Console not created.
HugeTLB registered 2 MB page size, pre-allocated 0 pages
squashfs: version 4.0 (2009/01/31) Phillip Lougher
Registering unionfs 2.5.13 (for 3.10.34)
aufs 3.10.x-20141215
msgmni has been set to 3984
io scheduler noop registered
io scheduler cfq registered (default)
Serial: 8250/16550 driver, 6 ports, IRQ sharing disabled
1180000000800.serial: ttyS0 at MMIO 0x1180000000800 (irq = 34) is a OCTEON
console [ttyS0] enabled, bootconsole disabled
console [ttyS0] enabled, bootconsole disabled
1180000000c00.serial: ttyS1 at MMIO 0x1180000000c00 (irq = 35) is a OCTEON
loop: module loaded
ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
octeon-ehci 16f0000000000.ehci: Octeon EHCI
octeon-ehci 16f0000000000.ehci: new USB bus registered, assigned bus number 1
octeon-ehci 16f0000000000.ehci: irq 56, io mem 0x16f0000000000
octeon-ehci 16f0000000000.ehci: USB 2.0 started, EHCI 1.00
hub 1-0:1.0: USB hub found
hub 1-0:1.0: 2 ports detected
ohci_hcd: USB 1.1 'Open' Host Controller (OHCI) Driver
octeon-ohci 16f0000000400.ohci: Octeon OHCI
octeon-ohci 16f0000000400.ohci: new USB bus registered, assigned bus number 2
octeon-ohci 16f0000000400.ohci: irq 56, io mem 0x16f0000000400
hub 2-0:1.0: USB hub found
hub 2-0:1.0: 2 ports detected
i2c-octeon 1180000001000.i2c: version 2.6
i2c-octeon 1180000001200.i2c: version 2.6
octeon_wdt: Initial granularity 5 Sec
TCP: cubic registered
NET: Registered protocol family 17
NET: Registered protocol family 15
L2 lock: TLB refill 256 bytes
L2 lock: General exception 128 bytes
L2 lock: low-level interrupt 128 bytes
L2 lock: interrupt 640 bytes
L2 lock: memcpy 1152 bytes
Bootbus flash: Setting flash for 8MB flash at 0x1f400000
usb 1-1: new high-speed USB device number 2 using octeon-ehci
phys_mapped_flash: Found 1 x16 devices at 0x0 in 8-bit bank. Manufacturer ID 0x0000c2 Chip ID 0x0000c9
Amd/Fujitsu Extended Query Table at 0x0040
  Amd/Fujitsu Extended Query version 1.1.
phys_mapped_flash: Swapping erase regions for top-boot CFI table.
number of CFI chips: 1
3 cmdlinepart partitions found on MTD device phys_mapped_flash
Creating 3 MTD partitions on "phys_mapped_flash":
0x000000000000-0x0000000a0000 : "boot0"
0x0000000a0000-0x000000140000 : "boot1"
mmc0: BKOPS_EN bit is not set
mmc0: new high speed DDR MMC card at address 0001
mmcblk0: mmc0:0001 SEM04G 3.68 GiB
0x000000140000-0x000000150000 : "eeprom"
mmcblk0boot0: mmc0:0001 SEM04G partition 1 2.00 MiB
Waiting 10sec before mounting root device...
mmcblk0boot1: mmc0:0001 SEM04G partition 2 2.00 MiB
mmcblk0rpmb: mmc0:0001 SEM04G partition 3 2.00 MiB
 mmcblk0: p1 p2
 mmcblk0boot1: unknown partition table
 mmcblk0boot0: unknown partition table
kjournald starting.  Commit interval 3 seconds
EXT3-fs (mmcblk0p2): using internal journal
EXT3-fs (mmcblk0p2): mounted filesystem with journal data mode
VFS: Mounted root (aufs filesystem) on device 0:11.
Freeing unused kernel memory: 300K (ffffffff80d05000 - ffffffff80d50000)
Algorithmics/MIPS FPU Emulator v1.5
INIT: version 2.88 booting

INIT: Entering runlevel: 2
[....] Starting SSH recovery service in the background
ssh-recovery: starting...
ssh-recovery: if=(all) port=(60257) terminate-timeout=(60)
ssh-recovery: enabling link on interfaces...
ssh-recovery: eth0 :: mac=(24:a4:3c:06:d7:86)
ssh-recovery: eth1 :: mac=(24:a4:3c:06:d7:87)
ssh-recovery: eth2 :: mac=(24:a4:3c:06:d7:88)
ssh-recovery: eth3 :: mac=(24:a4:3c:06:d7:89)
ssh-recovery: eth4 :: mac=(24:a4:3c:06:d7:8a)
ssh-recovery: eth5 :: mac=(24:a4:3c:06:d7:8b)
ssh-recovery: eth6 :: mac=(24:a4:3c:06:d7:8c)
ssh-recovery: eth7 :: mac=(24:a4:3c:06:d7:8d)
[....] Starting daemon monitor: monitssh-recovery: service started :: pid=(734)
. ok
[ ok ] Starting routing daemon: rib nsm ribd.
/opt/vyatta/etc/config.boot.default created
[....] Starting EdgeOS router: migrate rl-systemsync: ignoring all arguments
[ ok igure.
Starting network plug daemon: netplugd.

Welcome to EdgeOS ubnt ttyS0

By logging in, accessing, or using the Ubiquiti product, you
acknowledge that you have read and understood the Ubiquiti
License Agreement (available in the Web UI at, by default,
http://192.168.1.1) and agree to be bound by its terms.

ubnt login:
```

### User-Space
```bash
ubnt@ubnt:~$ uname -a
Linux ubnt 3.10.20-UBNT #1 SMP Fri Jul 29 17:07:24 PDT 2016 mips64 GNU/Linux
```

```bash
ubnt@ubnt:~$ cat /proc/cpuinfo
system type             : UBNT_E200
machine                 : Unknown
processor               : 0
cpu model               : Cavium Octeon II V0.1
BogoMIPS                : 2000.00
wait instruction        : yes
microsecond timers      : yes
tlb_entries             : 128
extra interrupt vector  : yes
hardware watchpoint     : yes, count: 2, address/irw mask: [0x0ffc, 0x0ffb]
isa                     : mips1 mips2 mips3 mips4 mips5 mips64r2
ASEs implemented        :
shadow register sets    : 1
kscratch registers      : 3
core                    : 0
VCED exceptions         : not available
VCEI exceptions         : not available

processor               : 1
cpu model               : Cavium Octeon II V0.1
BogoMIPS                : 2000.00
wait instruction        : yes
microsecond timers      : yes
tlb_entries             : 128
extra interrupt vector  : yes
hardware watchpoint     : yes, count: 2, address/irw mask: [0x0ffc, 0x0ffb]
isa                     : mips1 mips2 mips3 mips4 mips5 mips64r2
ASEs implemented        :
shadow register sets    : 1
kscratch registers      : 3
core                    : 1
VCED exceptions         : not available
VCEI exceptions         : not available
```

```bash
ubnt@ubnt:~$ lsmod
Module                  Size  Used by
iptable_filter          1480  0
ip_tables              18022  1 iptable_filter
x_tables               20531  2 ip_tables,iptable_filter
ip_set_hash_net        22330  4
ip_set                 23898  1 ip_set_hash_net
nfnetlink               3997  1 ip_set
cvm_ipsec_kame         38319  0
ipv6                  381814  35 cvm_ipsec_kame
imq                     6736  0
cavium_ip_offload     173021  0
octeon3_ethernet       68827  1 cavium_ip_offload
ptp                     8980  1 octeon3_ethernet
pps_core                7680  1 ptp
octeon_bgx_port         7891  1 octeon3_ethernet
octeon_bgx_nexus        4612  1 octeon_bgx_port
octeon_common_nexus     1320  1 octeon_bgx_nexus
ubnt_nf_app            10668  1 cavium_ip_offload
tdts                  572293  2 cavium_ip_offload,ubnt_nf_app
octeon_rng              1890  0
rng_core                4168  2 octeon_rng
octeon_ethernet        57580  1 cavium_ip_offload
mdio_octeon             3851  1 octeon_ethernet
of_mdio                 2982  3 octeon_ethernet,mdio_octeon,octeon_bgx_port
ethernet_mem            4232  1 octeon_ethernet
octeon_common           2480  1 octeon_ethernet
ubnt_platform         118121  0
libphy                 20647  5 ubnt_platform,octeon_ethernet,mdio_octeon,of_mdio,octeon_bgx_port
```

```bash
ubnt@ubnt:~$ mount
/dev/root on /root.dev type ext3 (rw,noatime,errors=continue,user_xattr,acl,barrier=1,data=journal)
unionfs on / type unionfs (rw,noatime,dirs=/root.dev/w=rw:/=ro)
proc on /proc type proc (rw,relatime)
sysfs on /sys type sysfs (rw,relatime)
tmpfs on /run type tmpfs (rw,nosuid,relatime,mode=755)
tmpfs on /run type tmpfs (rw,nosuid,relatime,mode=755)
tmpfs on /var/log type tmpfs (rw,nosuid,nodev,noexec,relatime,mode=755)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev,relatime)
none on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620)
tmpfs on /tmp type tmpfs (rw,relatime,nr_inodes=300000)
none on /opt/vyatta/config type tmpfs (rw,nosuid,nodev,relatime,nr_inodes=300000,mode=775)
proc on /run/netns/zebosfib0 type proc (rw,relatime)
proc on /run/netns/zebosfib1 type proc (rw,relatime)
```

```bash
# fdisk -l

Disk /dev/mtdblock0: 0 MB, 655360 bytes
128 heads, 63 sectors/track, 0 cylinders, total 1280 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/mtdblock0 doesn't contain a valid partition table

Disk /dev/mtdblock1: 0 MB, 655360 bytes
128 heads, 63 sectors/track, 0 cylinders, total 1280 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/mtdblock1 doesn't contain a valid partition table

Disk /dev/mtdblock2: 0 MB, 65536 bytes
128 heads, 63 sectors/track, 0 cylinders, total 128 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xffffffff

Disk /dev/mtdblock2 doesn't contain a valid partition table

Disk /dev/mmcblk0: 3959 MB, 3959422976 bytes
4 heads, 16 sectors/track, 120832 cylinders, total 7733248 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xbc250f00

        Device Boot      Start         End      Blocks   Id  System
/dev/mmcblk0p1            2048      292863      145408    c  W95 FAT32 (LBA)
/dev/mmcblk0p2          292864     7421951     3564544   83  Linux
```

```bash
root@ubnt:/home/ubnt# ls -la /mnt/mmcblk0p1/
total 11692
drwxr-xr-x    2 root     root           512 Jan  1  1970 .
drwxr-xr-x    8 root     root          4096 May 21 10:27 ..
-rwxr-xr-x    1 root     root       5529728 Apr 26 17:24 vmlinux.64
-rwxr-xr-x    1 root     root            33 Apr 26 17:33 vmlinux.64.md5
-rwxr-xr-x    1 root     root       6435976 Jul 29  2016 vmlinux.64o
-rwxr-xr-x    1 root     root            33 Aug  4  2016 vmlinux.64o.md5

root@ubnt:/home/ubnt# ls -la /mnt/mmcblk0p2/
total 157280
drwxr-xr-x    6 root     root          4096 May 21 10:17 .
drwxr-xr-x    8 root     root          4096 May 21 10:27 ..
drwx------    2 root     root         16384 Jan  1  1970 lost+found
-rw-r--r--    1 root     root      80498688 Apr 26 17:33 squashfs.img
-rw-r--r--    1 root     root            33 Apr 26 17:33 squashfs.img.md5
-rw-r--r--    1 root     root      80330752 Aug  4  2016 squashfs.o
-rw-r--r--    1 root     root            33 Aug  4  2016 squashfs.o.md5
-rw-r--r--    1 root     root            42 Apr 26 17:26 version
-rw-r--r--    1 root     root            41 Aug  4  2016 version.o
drwxr-xr-x   13 root     root          4096 May 21 10:27 w
drwxr-xr-x    9 root     root          4096 Jan  1  2015 w.o
drwxr-xr-x    2 www-data root          4096 Jun 19  2011 www
```

# DRAM upgrade

DRAM (DDR3 SO-DIMM) can be upgraded to e.g. 8 GiB using a Micron MT16KTF1G64HZ-1G6E1 module.

```bash
$ uname -a
Linux ER.mucI 3.10.107-UBNT #1 SMP Thu Apr 26 17:23:18 UTC 2018 mips64 GNU/Linux

$ free -m
             total       used       free     shared    buffers     cached
Mem:          7975        188       7786          0         23         88
-/+ buffers/cache:         76       7899
Swap:            0          0          0
```

# Images
![Edgerouter Pro 8](images/erpro8_chassis.png)
![Edgerouter Pro 8 - Open](images/erpro8_chassis_open.png)
![Edgerouter Pro 8 - Open w/ RAM and PSU](images/erpro8_mb_w_ram_and_psu_chassis.png)
![Edgerouter Pro 8 - Mainboard w/ RAM w/o PSU](images/erpro8_mb_w_ram_chassis.png)
![Edgerouter Pro 8 - Mainboard only w/ RAM](images/erpro8_mb_w_ram.png)
![Edgerouter Pro 8 - Mainboard only w/o RAM](images/erpro8_mb_wo_ram.png)
![Edgerouter Pro 8 - PSU](images/erpro8_psu.png)
![Edgerouter Pro 8 - RAM](images/erpro8_stock_ram.png)
