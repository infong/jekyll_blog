---
layout: post
title: Mounting a hard disk image including partitions using Linux
author: Infong
category: os
tags: []
---

A while ago I thought it would be a good idea to make a backup of my Linux server by just dumping the complete disk to a file. In retrospect, it would have been much easier had I just dumped the individual filesystems.

When I finally got around to using this backup, long after the 10GB disk had perished I realized that to use the loopback device to mount a filesystem it actually needs a filesystem to mount. What I had was a disk image, including partition table and individual partitions. To further complicate matters the data partition was also not the first partition inside this image.

For reference, I created this image using the Unix "dd" tool:

{% highlight bash %}
$ sudo dd if=/dev/had of=hda.img
30544113+0 records in
30544113+0 records out
$ sudo ls -lh
-rw-r--r-- 1 root    root  9.6G 2008-01-22 14:12 hda.img
{% endhighlight %}

I followed the instructions on [http://www.trekweb.com/~jasonb/articles/linux_loopback.html](http://www.trekweb.com/~jasonb/articles/linux_loopback.html) to try and mount the partitions inside the disk image, but ran into two problems.

To mount a partition inside the disk image you need to calculate the offset of where the partition starts. You can use fdisk to show this information to you, but you need to specify the number of cylinders if you are using a disk image.

You then also need to multiply the start and end numbers with the calculated sectors to get a byte offset.

I found another tool more useful for this task, called parted. If you are using Ubuntu, you can install it with "apt-get install parted"

{% highlight bash %}
$ sudo parted hda.img
GNU Parted 1.7.1
Using /data/rabbit/disk_image/test2
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) unit
Unit?  [compact]? B
(parted) print
Disk /data/rabbit/disk_image/test2: 10262568959B
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start        End           Size         Type     File system  Flags
1      32256B       106928639B    106896384B   primary  ext3         boot
2      106928640B   1184440319B   1077511680B  primary  linux-swap
3      1184440320B  10256924159B  9072483840B  primary  ext3
(parted) quit
{% endhighlight %}

Now we have the offsets and we can use those to mount the filesystems using the loopback device:

{% highlight bash %}
$ mount -o loop,ro,offset=32256 hda.img /mnt/rabbit
{% endhighlight %}

That mounted the first partition, the "boot" partition, but this didn't have the data on it that I was looking for. Lets try to mount partition number 3.

{% highlight bash %}
$ sudo umount /mnt/rabbit
$ sudo mount -o loop,ro,offset=1184440320 test2 /mnt/rabbit
$ sudo mount: wrong fs type, bad option, bad superblock on /dev/loop0,
missing codepage or helper program, or other error In some cases useful info is found in syslog - try dmesg | tail  or so
{% endhighlight %}

Oops, that doesn't look right. According the article referred to above if you are using a util-linux below v2.12b then you cannot specify an offset higher than 32bits. I'm using util-inux 2.13 which shouldn't have that problem, and besides, my offset is well below the 32bit limit.

The article also offers an alternative loopback implementation that supports mounting partitions within an image, but that requires patching and recompiling your kernel which I would rather not do.

Instead I decided to extra ct the filesystem from the image which would then allow me to mount it without specifying an offset.
Doing this is quite straightforward with "dd". You need to give "dd" a skip count, or, how far into the source to start copying, and a count, how much to copy.

Here you can either use the single byte offsets retrieved with parted or divide them by 512 and let "dd" use 512 byte blocks. Copying just one byte at a time takes a very long time, so I suggest using a larger block size.

Here is the command I used to extract my filesystem. Skip is 2313360 (1184440320/512) and Count is 17719695 (9072483840/4)

{% highlight bash %}
$ sudo dd if=hda.img of=hda3.img bs=512 skip=2313360 count=17719695
17719695+0 records in
17719695+0 records out
9072483840 bytes (9.1 GB) copied, 485.679 seconds, 18.7 MB/s
{% endhighlight %}

After extracting the filesystem I was able to mount it without any problems.

{% highlight bash %}
$ sudo mount -o loop hda3.img /mnt/rabbit/
$ sudo df -h /mnt/rabbit
Filesystem            Size  Used Avail Use% Mounted on
/data/rabbit/image/hda3.img
8.4G  6.3G  1.7G  80% /mnt/rabbit
{% endhighlight %}

