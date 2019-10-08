[TOC]



# Linux 查看系统硬件信息(实例详解)

linux查看系统的硬件信息，并不像windows那么直观，这里我罗列了查看系统信息的实用命令，并做了分类，实例解说。

## **cpu**

lscpu命令，查看的是cpu的统计信息.



```
blue@blue-pc:~$ lscpu
Architecture:          i686            #cpu架构
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian   #小尾序
CPU(s):                4               #总共有4核
On-line CPU(s) list:   0-3
Thread(s) per core:    1               #每个cpu核，只能支持一个线程，即不支持超线程
Core(s) per socket:    4               #每个cpu，有4个核
Socket(s):             1               #总共有1一个cpu
Vendor ID:             GenuineIntel    #cpu产商 intel
CPU family:            6
Model:                 42
Stepping:              7
CPU MHz:               1600.000
BogoMIPS:              5986.12
Virtualization:        VT-x            #支持cpu虚拟化技术
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              6144K
```



 

查看/proc/cpuinfo,可以知道每个cpu信息，如每个CPU的型号，主频等。



```
#cat /proc/cpuinfo
processor    : 0
vendor_id    : GenuineIntel
cpu family    : 6
model        : 42
model name    : Intel(R) Core(TM) i5-2320 CPU @ 3.00GHz
.....
```



上面输出的是第一个cpu部分信息，还有3个cpu信息省略了。

 

## **内存**

概要查看内存情况

```
free -m
             total       used       free     shared    buffers     cached
Mem:          3926       3651        274          0         12        404
-/+ buffers/cache:       3235        691
Swap:         9536         31       9505
```

这里的单位是MB，总共的内存是3926MB。

 

查看内存详细使用



```
# cat /proc/meminfo 
MemTotal:        4020868 kB
MemFree:          230884 kB
Buffers:            7600 kB
Cached:           454772 kB
SwapCached:          836 kB
.....
```



 

查看内存硬件信息



```
dmidecode -t memory
# dmidecode 2.11
SMBIOS 2.7 present.

Handle 0x0008, DMI type 16, 23 bytes
Physical Memory Array
    Location: System Board Or Motherboard
....
    Maximum Capacity: 32 GB
....

Handle 0x000A, DMI type 17, 34 bytes
....
Memory Device
    Array Handle: 0x0008
    Error Information Handle: Not Provided
    Total Width: 64 bits
    Data Width: 64 bits
    Size: 4096 MB
.....
```



我的主板有4个槽位，只用了一个槽位，上面插了一条4096MB的内存。

 

## **磁盘**

查看硬盘和分区分布



```
# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 465.8G  0 disk 
├─sda1   8:1    0     1G  0 part /boot
├─sda2   8:2    0   9.3G  0 part [SWAP]
├─sda3   8:3    0  74.5G  0 part /
├─sda4   8:4    0     1K  0 part 
├─sda5   8:5    0 111.8G  0 part /home
└─sda6   8:6    0 269.2G  0 part 
```



显示很直观

 

如果要看硬盘和分区的详细信息



```
# fdisk -l

Disk /dev/sda: 500.1 GB, 500107862016 bytes
255 heads, 63 sectors/track, 60801 cylinders, total 976773168 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0x00023728

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2148351     1073152   83  Linux
/dev/sda2         2148352    21680127     9765888   82  Linux swap / Solaris
/dev/sda3        21680128   177930239    78125056   83  Linux
/dev/sda4       177932286   976771071   399419393    5  Extended/dev/sda5       177932288   412305407   117186560   83  Linux
/dev/sda6       412307456   976771071   282231808   83  Linux
```



 

## **网卡**

查看网卡硬件信息

```
# lspci | grep -i 'eth'
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168B PCI Express Gigabit Ethernet controller (rev 06)
```

 

查看系统的所有网络接口

```
# ifconfig -a
eth0      Link encap:以太网  硬件地址 b8:97:5a:17:b3:8f  
          .....

lo        Link encap:本地环回  
          .....
```

或者是

```
ip link show
1: lo: <LOOPBACK> mtu 16436 qdisc noqueue state DOWN 
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
link/ether b8:97:5a:17:b3:8f brd ff:ff:ff:ff:ff:ff
```

 

如果要查看某个网络接口的详细信息，例如eth0的详细参数和指标



```
# ethtool eth0
Settings for eth0:
    Supported ports: [ TP MII ]
    Supported link modes:   10baseT/Half 10baseT/Full 
                            100baseT/Half 100baseT/Full 
                            1000baseT/Half 1000baseT/Full #支持千兆半双工，全双工模式
    Supported pause frame use: No 
    Supports auto-negotiation: Yes #支持自适应模式，一般都支持
    Advertised link modes:  10baseT/Half 10baseT/Full 
                            100baseT/Half 100baseT/Full 
                            1000baseT/Half 1000baseT/Full
    Advertised pause frame use: Symmetric Receive-only
    Advertised auto-negotiation: Yes #默认使用自适应模式
    Link partner advertised link modes:  10baseT/Half 10baseT/Full 
                                         100baseT/Half 100baseT/Full 
    .....
    Speed: 100Mb/s #现在网卡的速度是100Mb,网卡使用自适应模式，所以推测路由是100Mb，导致网卡从支持千兆，变成要支持百兆
    Duplex: Full   #全双工
    .....
    Link detected: yes    #表示有网线连接，和路由是通的
```



 

 

## **其他**

查看pci信息，即主板所有硬件槽信息。

```
lspci
00:00.0 Host bridge: Intel Corporation 2nd Generation Core Processor Family DRAM Controller (rev 09) #主板芯片
00:02.0 VGA compatible controller: Intel Corporation 2nd Generation Core Processor Family Integrated Graphics Controller (rev 09) #显卡
00:14.0 USB controller: Intel Corporation Panther Point USB xHCI Host Controller (rev 04) #usb控制器
00:16.0 Communication controller: Intel Corporation Panther Point MEI Controller #1 (rev 04)
00:1a.0 USB controller: Intel Corporation Panther Point USB Enhanced Host Controller #2 (rev 04)
00:1b.0 Audio device: Intel Corporation Panther Point High Definition Audio Controller (rev 04) #声卡
00:1c.0 PCI bridge: Intel Corporation Panther Point PCI Express Root Port 1 (rev c4) #pci 插槽
00:1c.2 PCI bridge: Intel Corporation Panther Point PCI Express Root Port 3 (rev c4)
00:1c.3 PCI bridge: Intel Corporation Panther Point PCI Express Root Port 4 (rev c4)
00:1d.0 USB controller: Intel Corporation Panther Point USB Enhanced Host Controller #1 (rev 04)
00:1f.0 ISA bridge: Intel Corporation Panther Point LPC Controller (rev 04)
00:1f.2 IDE interface: Intel Corporation Panther Point 4 port SATA Controller [IDE mode] (rev 04) #硬盘接口
00:1f.3 SMBus: Intel Corporation Panther Point SMBus Controller (rev 04)
00:1f.5 IDE interface: Intel Corporation Panther Point 2 port SATA Controller [IDE mode] (rev 04) #硬盘接口
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168B PCI Express Gigabit Ethernet controller (rev 06) #网卡
03:00.0 PCI bridge: Integrated Technology Express, Inc. Device 8893 (rev 41)
```



如果要更详细的信息:lspci -v 或者 lspci -vv

如果要看设备树:lscpi -t

 

查看bios信息



```
# dmidecode -t bios
......
BIOS Information
    Vendor: American Megatrends Inc.
    Version: 4.6.5
    Release Date: 04/25/2012
    .......
    BIOS Revision: 4.6
......
```



dmidecode以一种可读的方式dump出机器的DMI(Desktop Management Interface)信息。这些信息包括了硬件以及BIOS，既可以得到当前的配置，也可以得到系统支持的最大配置，比如说支持的最大内存数等。

如果要查看所有有用信息

```
dmidecode -q
```

里面包含了很多硬件信息。

 



转载自<http://www.cnblogs.com/ggjucheng/archive/2013/01/14/2859613.html> 