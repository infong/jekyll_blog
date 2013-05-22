---
layout: post
title: Upgrade kernel failed on Debian Squeeze vps
author: Infong
category: [virtualization, os]
tags: [upgrade, kernel, debian, vps]
---

I've got some problems when upgrading the kernel on my VPS:

    warning: grub-probe can't find drive for /dev/xvda. grub-probe: error: cannot stat `/dev/xvda'. run-parts: /etc/kernel/postinst.d/zz-update-grub exited with return code 1

So I have resolved this problem like this:

1.  First let's create /dev/xvda block device:

        $ sudo mknod /dev/xvda b 202 0

2.  Edit /boot/grub/device.map file:

    Change

    > (hd0) /dev/sda

    To

    > (hd0) /dev/xvda

3.  Edit /usr/sbin/update-grub file:

    Change

        find_device ()
        {
            if ! test -e ${device_map} ; then
                echo quit | grub --batch --no-floppy --device-map=${device_map} > /dev/null
            fi
            grub-probe --device-map=${device_map} -t device $1 2> /dev/null
        }

    to

        find_device ()
        {
                if ! test -e ${device_map} ; then
                        echo quit | grub --batch --no-floppy --device-map=${device_map} > /dev/null
                fi
                #grub-probe --device-map=${device_map} -t device $1 2> /dev/null

                echo /dev/xvda
        }

4.  Run ``update-grub 0``

5.  Assuming your root disk is /dev/xvda1, run:

        sed -i "s/xvda/xvda1/g" /boot/grub/menu.lst

    The run "``apt-get upgrade``" now.
