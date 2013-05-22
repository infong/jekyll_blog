---
layout: post
title: Change the partition number on Linux
author: Infong
category: os
tags: [linux, partition, testdisk, fdisk]
---


When we operating partition on Linux (such as delete partitions, create partition, change partition sizes), the partition number is not in accordance with the disk partition.

As my disk partition, it's the case above. As normal, the partition '/dev/sda2' should be '/dev/sda3', '/dev/sda3' should be '/dev/sda4' and '/dev/sda4' should be '/dev/sda2', the extended partition needn't change.

    Disk /dev/sda: 320.1 GB, 320072933376 bytes
    255 heads, 63 sectors/track, 38913 cylinders, total 625142448 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0xa9943039

       Device Boot      Start         End      Blocks   Id  System
    /dev/sda1   *          63    39070079    19535008+  83  Linux
    /dev/sda2       132825420   234522623    50848602   83  Linux
    /dev/sda3       234522624   625141759   195309568   83  Linux
    /dev/sda4        39070080   132825419    46877670    5  Extended
    /dev/sda5        39070143    42973874     1951866   82  Linux swap / Solaris
    /dev/sda6        42973938    44933804      979933+  83  Linux
    /dev/sda7        44933868    74236364    14651248+  83  Linux
    /dev/sda8        74236428    93755339     9759456   83  Linux
    /dev/sda9        93755403   132825419    19535008+  83  Linux

    Partition table entries are not in disk order

SO, how to fix this issue?

There are two ways at this time:

1.  Use command 'f' under fdisk:

        # fidsk /dev/sda
        Command (m for help):  x #(extra functionality)
        Expert command (m for help): f #(fix partition order)
        Expert command (m for help): w #(write table to disk)

2.  Rebuild partition table

    Recommended to use testdisk, is a good partition table recovery program, on how to rebuild the partition table using testdisk, please Google to get related-information.

After fix the partition order, we should modify /etc/fstab and /boot/grub/menu.lst, so the system can find the correct partition after next reboot.
