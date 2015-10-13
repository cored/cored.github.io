---
layout: post
title: "Til: Getting Into Rescue Mode With a Linux System"
date: 2015-10-13T17:51:41-04:00
comments: true
categories: ["Linux", "DevOps"]
---

I think this is old and to be honest I partially knew it but either way I think
it would be better documented now. What I learned today was a better way to fix my Arch
Linux system after forgetting about mounting the boot partition and upgrading
the kernel; needless to say that you don't want to do that. So, with further
ado here are the steps:

- In grub press e - to edit the menu configuration options
- Go to the line in which the command linux is written
- Go the end of that line and type init=single
- Hit ctrl-x

At the new prompt type the following:

- mount /dev/sda1 /new_root/boot -> If sda1 is the partition in which you have
boot
- mount <any_other_relevant_partitions>
- mount -t proc proc /new_root/proc
- mount -t sysfs sys /new_root/sys
- mount -o bind /dev /new_root/dev
- chroot /new_root bash

And voila, you will be in your more or less normal system waiting for you to
fix it.
