+++
date = ""
draft = true
tags = []
title = "Adding USB3 NVME Storage to my Kubernetes Raspberry Pi Cluster"

+++
1x [WD Blue SN550 500GB NVMe Internal SSD](https://www.amazon.com/gp/product/B07YFF3JCN/ "WD Blue SN550 500GB NVMe Internal SSD") - $65  
1x [USB 3.1 Gen 2 NVME SSD Enclosure Adapter](https://www.amazon.com/gp/product/B07MNFH1PX/ "USB 3.1 Gen 2 NVME SSD Enclosure Adapter") - $26

![](/uploads/CrystalDiskMark 7.0.0 x64 NVME USB3_1.png)

**Adding local storage**

https://jamesachambers.com/raspberry-pi-4-usb-boot-config-guide-for-ssd-flash-drives/

    $ lsusb
    
    Bus 002 Device 002: ID 152d:0562 JMicron Technology Corp. / JMicron USA Technology Corp.
    Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
    Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    
    $ sudo dmesg | grep usb
    
    [    1.088658] usb 2-1: new SuperSpeed Gen 1 USB device number 2 using xhci_hcd
    [    1.119552] usb 2-1: New USB device found, idVendor=152d, idProduct=0562, bcdDevice= 2.06
    [    1.119592] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
    [    1.119620] usb 2-1: Product: SSK Storage
    [    1.119643] usb 2-1: Manufacturer: SSK
    [    1.119665] usb 2-1: SerialNumber: DF56419883A7E

$ sudo fdisk /dev/sda

Welcome to fdisk (util-linux 2.33.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xc42cf7ff.

Command (m for help): p
Disk /dev/sda: 465.8 GiB, 500107862016 bytes, 976773168 sectors
Disk model:
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: dos
Disk identifier: 0xc42cf7ff
Command (m for help): n
Partition type
p   primary (0 primary, 0 extended, 4 free)
e   extended (container for logical partitions)
Select (default p):

Using default response p.
Partition number (1-4, default 1):
First sector (2048-976773167, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-976773167, default 976773167):

Created a new partition 1 of type 'Linux' and of size 465.8 GiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

$ sudo mkfs.ext4 /dev/sda1
mke2fs 1.44.5 (15-Dec-2018)
Creating filesystem with 122096390 4k blocks and 30531584 inodes
Filesystem UUID: 71c11ccf-df60-4233-851e-9ddd3f931a7f
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
102400000

Allocating group tables: done
Writing inode tables: done
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done

Export NFS
define PV https://docs.docker.com/ee/ucp/kubernetes/storage/use-nfs-volumes/

sudo nano /boot/cmdline.txt

usb-storage.quirks=XXXX:XXXX:u

usb-storage.quirks=152d:0562:u

Future plans - NUC nested VCF, k8s RPi WLD