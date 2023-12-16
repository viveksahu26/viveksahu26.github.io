+++
title = "How kernel boots up !!"
description = "Hugo, the world's fastest framework for building websites"
date = "2019-02-28"
aliases = ["about-us", "about-hugo", "contact"]
author = "Hugo Authors"
+++

Hey tech lovers. This time back with one of the interesting topic of Linux, **how Operating System starts**. The reason behind writing this blog is curiousity within me pulls down to know what's going under the hood when OS starts. After knowing can stop myself to share with you all. What I believe that an individual can not learn everything but on sharing things its become easier for learner to understand things properly within short spam of time. Always try to share your knowledge. It enhances  your knowledge as well as saves time of others.

### How the Linux Kernel Boots?

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cj7raa00deltn9so74hy.png)

Let's begin with the topic "how Linux System starts or boots up".

### When happens after pressing Power ON button of the computer

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d0zch33kzrtg869omsq6.jpg)

As the user Power ON its machine, Power supply goes to motherboard. Motherboard tries to start CPU. After CPU  starts, first off all it clears old data from registers. After that CPU goes to its memory location(0xffff0000→ address sector on the hard disk) to see if any instruction is present or not. By the way that address is the home or location of BIOS. Basically, CPU executes BIOS program. And that's how BIOS starts. 

**Note:**-
> Basically, BIOS or UEFI represents firmware. The general difference between them is that BIOS is traditional and UEFI is modern one. Your system may have either of them.
> To check which one is present in your system, run `efibootmgr` command.

### BIOS(basic input/output system) performs POST (Power On Self Test) checks

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nxzcyq7wmspv3fsembv5.jpg)

After BIOS starts, it perform various checks or test on many components of hardware such as CPU test, RAM test, etc. The whole testing process is known as POST(Power On Self Test). Basically, the main task of BIOS is to ensure that the computer hardware are functioning correctly or not. If POST fails, the computer may not be usable and so the boot process does not continue. After successfully completion of POST operation. The next job of BIOS is to search the boot sector from the attached disks. And do you know, what boot sector contains?  It's Boot record popularly  knows as  Master Boot Record (MBR). Simply, BIOS loads MBR instructions code 
to RAM, execute it and handle over control to the MBR. 

### What MBR does?

The first 512-byte sector on the hard drive along with the partition table. The total amount of space allocated for the actual MBR is 446 bytes and remaining are assigned for Partition table. 
Because the boot record must be so small, it is also not very smart and does not understand filesystem structures. Therefore the sole purpose of MBR is to locate and load GRUB. In order to accomplish this, GRUB must be located in the space between MBR till first partition on the drive. After loading GRUB into RAM, MBR handles control over to GRUB program.

```

At sector 0 ---> MBR is located of size 512 bytes
Between sector 0 and 2048  ---> GRUB is located of size which is of size 1MiB(2048*512 bytes = 1024KiB = 1MiB)
From 2049 sectors onwards ---> Partitioning of disk starts
```

**Note**: Hard disk is divided into sector. Sector is the smallest unit of Hard disk. 1 Sector = 512 byte

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ooel4sfg2kiavee01b4y.png)

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qumju2w6xag4b47y62kq.jpeg)

### GRUB(Grand Unified Boot Loader)

> Do You Know !!
#### What is the use of GRUB?

_To know the answers you have to know what is booting first of all. 
In technical terms, booting means copying the kernel image from hard disk to memory and afterwards executing it. So, copying the kernel image from disk to memory is performed by the GRUB program. Along with the kernel image GRUB also helps to pass some parameters. This is where GRUB comes into use. 

But the biggest problem for GRUB is how to search the kernel image in the disk. Listen carefully don't get confuse. I know you are thinking that searching any file in Linux is very easy, yes 100% right no doubt in that, but it is when your OS is in running state(i.e. Kernel is running + necessary drivers and programs are running). 
But currently only GRUB is running, you system is not yet started. What it mean is, neither kernel is running nor required drivers and necessary programs are running. So, here searching any file is like impossible task. Then how GRUB searches kernel image from disk.

Let’s see how GRUB solves these problems.

#### GRUB has to find the answer of 2 question:-

1) What is Kernel and its parameter?
2) And how to find or search it in the hard disk ?

To load the Kernel image into memory GRUB has to search Kernel image along with parameter from disk. To access hard disk and file system for searching Kernel image. GRUB takes help of  BIOS or UEFI. Btw, disk allows BIOS or UEFI to access hardware storage via Logical Block Addressing(LBA). Are you able to memorise how BIOS searched for MBR from disks.  LBA is a universal, simple way to access data from any disk. One more thing, most Boot Loader Program, GRUB, can read partition table( index page which maps files to their their corresponding address in the hard disk) which is built-in support for read-only access to the file system. Therefore, GRUB solves the problem of searching kernel images from the disk. Once it gets Kernel image(i.e. **.iso** file) and it's parameter it loads to memory along with that it also loads **initramfs**.

Below, BOOT_IMAGE refers to kernel image as well as it's parameter.

```BOOT_IMAGE=/boot/vmlinuz-4.15-generic  root=UUID=179hfy4955  ro  quiet splash  vt.handoff=1
```

kernel image ---> `/boot/vmlinuz-4.15-generic`
Parameters ---> `root, ro(read only), vt.handoff,`

**initramfs** is an archive containing the kernel modules for  the hardware required during the boot time, initialization scripts, drivers, and more. These are modules which connect with hardware as well as software.  May be this question arises to your mind why Kernel image is not directly attached with it's modules. Why these modules are kept separately in form of `initramfs`. Let's contradict our point, so let's say all kernel modules are present inside Kernel image. Basically what will happen, as the CPU start executing Kernel image then along with that all modules will also starts executing whether it is needed by the Kernel or not. And this leads to bottleneck of RAM or overflow of RAM. As a result memory may crash. So, that's why Kernel modules are separated and only those modules are taken from `initramfs` which are required by the Kernel at that particular time. For example, we all know that there is module or driver for printer which is used when printer is attached. But during the boot time the printers driver module is not required. But if it is too executed then it's totally wastage of your RAM and CPU. To know more about `initramfs` [refer](https://wiki.ubuntu.com/Initramfs).
**Once the GRUB load kernel image and execute/start it. The control is transfer from GRUB to Kernel and from here Kernel work starts**

High level idea regarding the function of the kernel:

1. Kernel initialization
2. CPU inspection
3. Memory inspection
4. Device bus discovery
5. Device discovery
6. Auxiliary kernel subsystem setup
7. Root filesystem mount
8. User space start(systemd)

During the initialization process, Kernel must mount a root file system before starting init or systemd. We don't need to go in detail of it. Finally kernel last job is to start Systemd and also hand over control to Systemd.  This is the end of the boot process. At this point, the Linux kernel and systemd are running but unable to perform any productive tasks for the end user because nothing else is running.

**Systemd** is a system as well as service manager in the Linux. It is the mother of all process and programs.  From here all your services and user space programs are managed as well as controlled by it. In next blog will know about Systemd in detail.

#### flowchart:-

https://lucid.app/lucidchart/4345e110-7988-438b-a961-a39113f2d341/edit?invitationId=inv_d794a834-d90d-4931-a62d-94b2220a8c71

### References

https://opensource.com/article/17/2/linux-boot-and-startup
https://learning.oreilly.com/library/view/hands-on-booting-learn/9781484258903/
https://learning.oreilly.com/library/view/how-linux-works/9781098128913/

Thanks for reading it. Hope you liked it and gain some knowledge. Tried my best to make you under the topic step by step. Thank you guys. Will see you in next blog.
