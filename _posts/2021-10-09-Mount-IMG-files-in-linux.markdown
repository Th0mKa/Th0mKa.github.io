---
layout: post
title:  "Mount IMG files in Linux"
date:   2021-10-09 15:00:00 +0200
author: Thomas
categories: linux mount img
---

The problem in mounting an .img file compared to an .iso file is that the .img files are not images of a partition, but instead of a whole disk. That means they start with a bootloader plus partition table and you have to find out the offset of the partition and mount it with the offset option of mount.

To find out the offset you can do a 

fdisk -l /path/to/image.img

as a result you get the block size and the start block.

root@lx01:/home/thomas# fdisk -l /home/thomas/Downloads/ubuntu-20.04.3-preinstalled-server-arm64+raspi.img
Disk /home/thomas/Downloads/ubuntu-20.04.3-preinstalled-server-arm64+raspi.img: 3,14 GiB, 3368303616 bytes, 6578718 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xf66f0719

Device                                                                     Boot  Start     End Sectors  Size Id Type
/home/thomas/Downloads/ubuntu-20.04.3-preinstalled-server-arm64+raspi.img1 *      2048  526335  524288  256M  c W95 FAT3
/home/thomas/Downloads/ubuntu-20.04.3-preinstalled-server-arm64+raspi.img2      526336 6578683 6052348  2,9G 83 Linux

With that information you can do simple math and use the offset value in the mount command.
512 * 526336 = 269484032

mount -t auto -o loop,offset=269484032 image.img /mnt/tmp