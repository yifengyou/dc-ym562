# 定昌 DC-YM562开发板玩耍

* 官方B站: <https://space.bilibili.com/514513449?spm_id_from=333.788.upinfo.detail.click>


```shell
Linux rk3562 6.1.99 #78 SMP Wed Aug  6 09:39:26 CST 2025 aarch64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu May 29 17:04:06 2025
root@rk3562:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: can0: <NOARP,ECHO> mtu 16 qdisc noop state DOWN group default qlen 10
    link/can 
3: end0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether d2:cd:75:cb:40:53 brd ff:ff:ff:ff:ff:ff
    inet 192.168.33.31/24 brd 192.168.33.255 scope global dynamic noprefixroute end0
       valid_lft 6846sec preferred_lft 6846sec
    inet6 fe80::9776:ab20:5648:6a71/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
4: wlx28f52b2cdfb1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 28:f5:2b:2c:df:b1 brd ff:ff:ff:ff:ff:ff
5: wlx2af52b2cdfb1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 2a:f5:2b:2c:df:b1 brd ff:ff:ff:ff:ff:ff
root@rk3562:~# uname -a
Linux rk3562 6.1.99 #78 SMP Wed Aug  6 09:39:26 CST 2025 aarch64 GNU/Linux
root@rk3562:~# lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
mmcblk0      179:0    0  7.3G  0 disk 
├─mmcblk0p1  179:1    0    4M  0 part 
├─mmcblk0p2  179:2    0    4M  0 part 
├─mmcblk0p3  179:3    0   64M  0 part 
├─mmcblk0p4  179:4    0  128M  0 part 
├─mmcblk0p5  179:5    0   32M  0 part 
├─mmcblk0p6  179:6    0  128M  0 part /oem
├─mmcblk0p7  179:7    0    1G  0 part /userdata
└─mmcblk0p8  179:8    0  5.7G  0 part /
mmcblk0boot0 179:32   0    4M  1 disk 
mmcblk0boot1 179:64   0    4M  1 disk 
zram0        254:0    0    0B  0 disk 
root@rk3562:~# blkid
/dev/mmcblk0p7: LABEL="userdata" UUID="ab05ae32-6cf1-46dd-8ca1-31f680961f17" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="userdata" PARTUUID="c9410000-0000-4118-8000-364500003ad8"
/dev/mmcblk0p5: PARTLABEL="backup" PARTUUID="a73a0000-0000-415a-8000-6dbc00002e56"
/dev/mmcblk0p3: PARTLABEL="boot" PARTUUID="916f0000-0000-4301-8000-6cc900005e0d"
/dev/mmcblk0p1: PARTLABEL="uboot" PARTUUID="49240000-0000-462c-8000-6e2a000063bd"
/dev/mmcblk0p8: LABEL="linuxroot" UUID="1f0f5087-f05f-4cfe-97fd-c19994604b19" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="rootfs" PARTUUID="614e0000-0000-4b53-8000-1d28000054a9"
/dev/mmcblk0p6: LABEL="oem" UUID="ad260d73-2388-44ab-81e0-0587b9a19e57" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="oem" PARTUUID="524f0000-0000-4f7c-8000-792a00005b2c"
/dev/mmcblk0p4: PARTLABEL="recovery" PARTUUID="be120000-0000-4001-8000-31f400003ece"
/dev/mmcblk0p2: PARTLABEL="misc" PARTUUID="176b0000-0000-4232-8000-46330000228a"
root@rk3562:~# 

```

镜像做的不够完美
1. 缺少内核模块支持
2. 缺少内核头文件，无法直接使用
3. 分区冗余严重


```shell
/dev/mmcblk0p7: LABEL="userdata" UUID="ab05ae32-6cf1-46dd-8ca1-31f680961f17" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="userdata" PARTUUID="c9410000-0000-4118-8000-364500003ad8"
/dev/mmcblk0p8: LABEL="linuxroot" UUID="1f0f5087-f05f-4cfe-97fd-c19994604b19" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="rootfs" PARTUUID="614e0000-0000-4b53-8000-1d28000054a9"
/dev/mmcblk0p6: LABEL="oem" UUID="ad260d73-2388-44ab-81e0-0587b9a19e57" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="oem" PARTUUID="524f0000-0000-4f7c-8000-792a00005b2c"
/dev/mmcblk0p5: PARTLABEL="backup" PARTUUID="a73a0000-0000-415a-8000-6dbc00002e56"
/dev/mmcblk0p3: PARTLABEL="boot" PARTUUID="916f0000-0000-4301-8000-6cc900005e0d"
/dev/mmcblk0p1: PARTLABEL="uboot" PARTUUID="49240000-0000-462c-8000-6e2a000063bd"
/dev/mmcblk0p4: PARTLABEL="recovery" PARTUUID="be120000-0000-4001-8000-31f400003ece"
/dev/mmcblk0p2: PARTLABEL="misc" PARTUUID="176b0000-0000-4232-8000-46330000228a"
```


```text
# dumpimage -l boot.img 
FIT description: U-Boot FIT source file for arm
Created:         Wed Aug 27 11:42:26 2025
 Image 0 (fdt)
  Description:  unavailable
  Created:      Wed Aug 27 11:42:26 2025
  Type:         Flat Device Tree
  Compression:  uncompressed
  Data Size:    148933 Bytes = 145.44 KiB = 0.14 MiB
  Architecture: AArch64
  Load Address: 0xffffff00
  Hash algo:    sha256
  Hash value:   e6c4bcefea8786946807d80ff81ebfa7d505eb1f170c834294a7354af37ad35e
 Image 1 (kernel)
  Description:  unavailable
  Created:      Wed Aug 27 11:42:26 2025
  Type:         Kernel Image
  Compression:  uncompressed
  Data Size:    41826816 Bytes = 40846.50 KiB = 39.89 MiB
  Architecture: AArch64
  OS:           Linux
  Load Address: 0xffffff01
  Entry Point:  0xffffff01
  Hash algo:    sha256
  Hash value:   de045955fe7a3d120e04b152e2e85c68eba5d02c8638cc0b3c9e6555b9a8d05f
 Image 2 (resource)
  Description:  unavailable
  Created:      Wed Aug 27 11:42:26 2025
  Type:         Multi-File Image
  Compression:  uncompressed
  Data Size:    186880 Bytes = 182.50 KiB = 0.18 MiB
  Hash algo:    sha256
  Hash value:   5a8be15b3a1dda7c30bcccd82580e4583a89e466bb77d67d1065bfc7c7556229
 Default Configuration: 'conf'
 Configuration 0 (conf)
  Description:  unavailable
  Kernel:       kernel
  FDT:          fdt
  Sign algo:    sha256,rsa2048:dev
  Sign padding: pss
  Sign value:   unavailable
  Timestamp:    unavailable

```

```text
# dumpimage  boot.img -T flat_dt -p 0 -o boot-unpack/boot_fdt
Extracted:
 Image 0 (fdt)
  Description:  unavailable
  Created:      Wed Aug 27 11:42:26 2025
  Type:         Flat Device Tree
  Compression:  uncompressed
  Data Size:    148933 Bytes = 145.44 KiB = 0.14 MiB
  Architecture: AArch64
  Load Address: 0xffffff00
  Hash algo:    sha256
  Hash value:   e6c4bcefea8786946807d80ff81ebfa7d505eb1f170c834294a7354af37ad35e

```





















---