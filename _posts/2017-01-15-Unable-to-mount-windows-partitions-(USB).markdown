---
layout: post
date: 2017-01-15 21:45 -0500
title: Unable to mount windows partitions (USB)
categories: 
---
I rebooted my laptop to enable some useful bios features
(default options seem to be to leave support for virtualization disabled),
which applied an update at the same time to the kernel.

I'm left in a fun position where the current vfat.ko kernel module fails to load,
so usb connected, dos formatted devices do not load. Even in a very linux
centric world, the default is still to leave usb devices as vfat for interoperability.
I have a few ext2 devices, mainly for better support for things like file ownership,
but the files I want access to right now are on a dos formatted disk.

Well, here's the output:

{% highlight bash %}
djuber@laptop:~$ sudo mount --verbose -t vfat /dev/sdc1 /mnt/usb
mount: unknown filesystem type 'vfat'

djuber@laptop:~$ sudo modinfo vfat
filename:       /lib/modules/3.16.0-4-amd64/kernel/fs/fat/vfat.ko
author:         Gordon Chaffee
description:    VFAT filesystem support
license:        GPL
alias:          fs-vfat
depends:        fat
intree:         Y
vermagic:       3.16.0-4-amd64 SMP mod_unload modversions 

djuber@laptop:~$ uname -a
Linux laptop 3.16.0-4-amd64 #1 SMP Debian 3.16.36-1+deb8u2 (2016-10-19) x86_64 GNU/Linux

djuber@laptop:~$ sudo insmod /lib/modules/3.16.0-4-amd64/kernel/fs/fat/vfat.ko
insmod: ERROR: could not insert module /lib/modules/3.16.0-4-amd64/kernel/fs/fat/vfat.ko: Unknown symbol in module

djuber@laptop:~$ sudo modprobe vfat
modprobe: ERROR: could not insert 'vfat': Unknown symbol in module, or unknown parameter (see dmesg)

djuber@laptop:~$ dmesg  |tail -n 2 
[60461.855987] fat: Unknown symbol setattr_prepare (err 0)
[60519.247057] fat: Unknown symbol setattr_prepare (err 0)

{% endhighlight %}

Fun times to be had by all. I expect that either waiting a week for a new kernel, or tracking down a buglisting for this, is all I need. The one low activity technique I have not tried is to reinstall the kernel modules using apt.

