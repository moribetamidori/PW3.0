---
title: Linux Kernel Development (EN|CH) (Translation) | Linux内核编译 (中|英) (译) PART 1
date: 2020-05-18 16:10:42
tags:
- note
- linux
- kernel
- code
- 笔记
- Linux内核编译
---
<font color=#FF1493	><b>All Contents are Credited to <a  href = "mailto:lq56@scarletmail.rutgers.edu">@Longfei Qiu</a></b></font>

<b>INTRO: </b> It's all started with a conversation I had with Longfei, my 314 course TA, who is a very talented programmer. After final I was trying to ask him if I could learn anything from him, and he asked if I want to learn linux kernel programming, a topic he is very interested in that touches the very bottom of computers' operating system. I was like why not? And here it is. The original tutorial he wrote is in Chinese and I'll post the original tutorial below. The translation of the original tutorial is authorized, and I'll sometimes add comments colored in blue in the following content to note some tips and things I have learned by following his tutorial. His tutorial is both technical and entertaining as you would not only learn the technical part of his tutorial but also the rich histroy behind linux kernel programming. Hopefully whoever is also interested in this topic would benefit from this post. All discussions are welcomed! 

<b>中文版请戳这: <a  href = "https://drive.google.com/file/d/1kY_sYgNFSVtNNaCzAEDUJ8EIk3_QlY3x/view?usp=sharing">Linux Kernel Programming Part 1</a></b>

## 1 Introduction to Linux 
Linux is a set of open source Unix operating system kernels led by Finnish Linus Torvalds. LINUX itself is not a complete operating system and needs to be accompanied by complete User-Mode components. The most popular Linux User-Mode component pacakge is the GNU Project led by Richard Stallman. A complete operating system that consists of the Linux kernel and the User-Mode components is called Linux Distribution. Linux distributions that use GNU project are strictly called GNU/Linx. There are six main branches of Linux distributions in the world: RHEL, Debian, Slackware, Gentoo, Arch Linux, and Android, all of which use GNU project excpet Android. 

### 1.1 Unix-Like Operating System 
Unix was an operating system developed by Ken Thompson and Dennis Ritchie in the 1960s and first used in PDP-7 and PDP-11 systems. Unix is copyrighted by AT7T, but under the antitrust laws of the time, AT&T was unable to use Unix to generate profit. Therefore at that time all universities and companies can access Unix's source code by giving a very low "material fee" to AT&T. Unix quickly became the most popular operating system of the time. In 1980s, the government forced AT&T to break up and subsequently a large number of commercial operating systems that are compatible with Unix's interface emerged in the market, and this period known as Unix Wars. 

By the mid 1980s, Unix-Like products had split into several imcompatible factions, the most influential of which were SysV and BSD. The modern Unix-Like systems do not strictly stand on either side. 

### 1.2 Linux Distributions
There are currently six main branches of Linux Distribution: RHEL, Debian, Slackware, Gentoo, Arch Linux, Android.

<b>RHEL(Red Hat Enterprise Linux):</b> a distribution led by Red Hat company, mainly used for web servers and is known for its stability. CentOS is a free version of RHEL, but is not supported by Red Hat. The versions of the sofware used by RHEL are usually very old, as the new versions' stability are not time proved yet. CentOS is a free version of RHEL, but is not supported by Red Hat.

<b>Debian:</b> Debian is a distribution dominated by the open source community. The Canonical company has developed the famous Ubuntu distribution based on Debian. The software used by Debian generally updated faster than RHEL, but slower than Arch Linux. 

<b>Slackware:</b> The oldest linux distribution, known for its simple interface but difficult to get started. RHEL's competitor SUSE is a distribution based on Slackware. OpenSUSE is the free version of SUSE.

<b>Arch Linux:</b> A distribution similar to Slackware, both known for its simplicity. Arch Linux uses Rolling Release, and therefore the sofware is being updated extremely fast. Usually, the Arch Linux version of a sofware's new version would be availiable within one week of the release of the source code. In this way, technology enthusiasts are able to quickly experience the features of the new version. But it also makes Arch Linux less mature and stable to be used on servers and the like. The offical Arch Linux website has a Wiki called Arch Wiki, which has extremely detailed descriptions for all the common software on Linux, often called the "Bible" of Linux. 

<b>Gentoo Linux:</b> A distribution known for its pursuit of speed. It compiles all software locally from the source code in order to maximize the optimization based on the specific situation in a user's system. The Chromium OS Google developed for Netbook is based on Gentoo. 

<b>Android:</b> A linux distribution developed by Google for mobile dvices, did not use GNU at all. 

## 2 Learning Linux using RISC-V
There is a strict distinciton between "architecture-dependent" and "architecture independent" program in Linux source code. The architecture refers to the CPU architecture. The most supported platform for Linux are Intel x86 and x64. However, the historical baggage of x86 architecture is too heavy such that there are numerous patches in the x86 architecture-related code, designed to get around the quirks of certain Intel (or AMD) chips. These historical baggages are not conductive to the study of Linux source code. Therefore I think the best way to learn linux code is to experiment on a simple CPU architecture, not on a x86 architecture. 

RISC-V is a set of open source CPU instruction set led by UC Berkeley, and is known for its small number of instructions, a clear design concept, and the use of a relatively weak memory model to improve execution efficiency. SiFive is by far the most successful designer of RISC-V chips. On an x86 computer, the RISC-V chip can be simulated with Qemu. Currently, most of the components of Linux and GNU project (especially the GCC compiler and GDB debugger) have been ported to the RISC-V architecture. 

## 3 Install x86 Linux Virtual Machine
We are going to program the linux kernel that runs on RISC-V. However, the Linux kernel can only be programmed under Linux's GCC. Therefore, we're going to start by installing a Linux virtual machine on an x86 platform, with a GCC compiler and GDB debugger whose target architecture is RISC-V. 

For personal research and experimental purpose, I recommend installing the Arch Linux distribution. In fact, only Arch Linux currently offers the latest versions of GCC and GDB with target architecture of RISC-V. For convenience, both of our x86 virutal machine and RISC-V virtual machine will use the Qemu emulator.

First, download the latest version of Qemu from Qemu's official website: https://www.qemu.org/
Qemu supports both Windows and Mac platform. 

### For Windows

Updating...