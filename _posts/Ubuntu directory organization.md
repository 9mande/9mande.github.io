---
title: Linux directory organization
date: 2023-01-30 15:47:00 +0900
categories: [Linux]
tags: [linux, ubuntu]
---

Many Linux OSes(like Ubuntu) follows Linux FSSTND(FileSystem STaNDard) or FHS(File system Hierarchy Standard).

Below is the result of '$ tree' at the root directory in my case (ubuntu).

```
/ (root)
├── backup (empty)
├── bin -> usr/bin : Essential user command binaries
├── boot (empty) : Static files of the boot loader
├── dev : Device files
├── etc : Host-Specific system configuration
├── home : User home directories
├── lib -> usr/lib : Essential shared libraries and kernel modules
├── lib32 -> usr/lib32
├── lib64 -> usr/lib64
├── libx32 -> usr/libx32
├── lost+found (empty)
├── media : Mount point for removable media
├── mnt : Mount point for a temporarliy mounted filesystems
├── opt : Add-On application software packages
├── proc : Virtual filesystem documenting kernel and process status as text files
├── root : Home directory for the root user
├── run : 
├── sbin -> usr/sbin : System binaries
├── snap : 
├── srv : Data for services provided by this system
├── sys : 
├── tmp : Temporary files
├── usr : (Multi)User Utilities and applications
└── var : Variable files
```

## /bin

Contains essential commands like

- apt
- arch
- bash
- cat
- chgrp, chmod, choom, chown
- clear
- cp
- curl
- dir
- kill
- nslookup
- nstat
...

Never have a directory below.
Only commands, or symbolic link of the commands.


## /boot

Contains boot loader

Was empty in WSL...
In WSL, systemd is not used.


## /dev

For system devices(which called nodes).
A node can be a real device or a pseudo device.

```
/dev
├── autofs
├── block
├── bsg
│	 ├── 0:0:0:0
│	 ├── 0:0:0:1
│	 ├── 0:0:0:2
│	 └── 0:0:0:3
├── btrfs-control
├── console
├── cpu_dma_latency
├── cuse
├── dxg
├── fd -> /proc/self/fd
├── full
├── fuse
├── hvc0
├── hvc1
├── hvc2
├── hvc3
├── hvc4
├── hvc5
├── hvc6
├── hvc7
├── kmsg
├── kvm
├── loop-control
├── loop0
├── loop1
├── loop2
├── loop3
├── loop4
├── loop5
├── loop6
├── loop7
├── mapper
│	 └── control
├── mem
├── net
│	 └── tun
├── null
├── nvram
├── ppp
├── ptmx
├── pts
│	 ├── 0
│	 ├── 1
│	 └── ptmx
├── ram0
├── ram1
├── ram10
├── ram11
├── ram12
├── ram13
├── ram14
├── ram15
├── ram2
├── ram3
├── ram4
├── ram5
├── ram6
├── ram7
├── ram8
├── ram9
├── random
├── rtc0
├── sda
├── sdb
├── sdc
├── sdd
├── sg0
├── sg1
├── sg2
├── sg3
├── shm -> /run/shm
├── stderr -> /proc/self/fd/2
├── stdin -> /proc/self/fd/0
├── stdout -> /proc/self/fd/1
├── tty
├── tty0
├── tty1
├── tty10
├── tty11
├── (and many tty_s)
├── tty9
├── ttyS0
├── ttyS1
├── ttyS2
├── ttyS3
├── urandom
├── vcs
├── vcs1
├── vcsa
├── vcsa1
├── vcsu
├── vcsu1
├── vfio
│	 └── vfio
├── vhost-net
├── vport0p0
├── vsock
└── zero
```
### device types

- cdrom : CD, DVD-ROM drive
- fd : floppy disk drive
- hd : IDE connected device like hard drive / CD-ROM
- md : metadisk or RAID device like hard drive
- ram : RAM disk
- sd : SCSI connected massive storage device
- usb : USB connected device

### speacial features

- /dev/null

  + is a null device.
  + if you write, it will always success.
  + if you read, it will get nothing.


- /dev/random

  + is a random number generator.
  + collects noise of input signals occured by IO device driver, called 'entropy'.
  + when 'entropy' get fulled, generates random number.
  + uses blocking-pool (needs further study).

- /dev/urandom
  + it generates random number.
  + uses nonblocking-pool (needs further study).
  + which makes it faster than 'random'.
  + but random is more secure.

- /dev/full

  + is a full device.
  + which is considered always full.
  + if you write, it returns error.
  + if you read, it returns infinite nulls.

- /dev/zero

  + is a zero device, similar to null device.
  + if you write, it always success.
  + if you read, it returns infinite nulls.
  + useful for device formatting.

### other searches

- tty : teletype
  
  + ttyS_ : Serial

## /etc

Contains every setting files of most systems.

- system environment setting files
- service configuration setting files
- user/group information file

### subdirectories

- /etc/ssh

  + is about SSH service.
  + sshd daemon uses these setting files.

- /etc/squid

  + has init files about user account creation.
 
- /etc/rc_.d

  + _ is numbers like 0~6, and letter S.
  + contains boot scripts on each booting levels.
  + I found that they are actually symbolic links to the scripts in /init.d

- /etc/pam.d

  + has PAM(Pluggable Authentication Modules) setting files.


## /lib

Symbolic link connected to /usr/lib.
Contains kernel module files, library files, and any other library files.

## /media

Mount point for devices like DVD, CD-ROM, USB.

## /mnt

Temporary mount point.

## /proc

Contains all processes of memory with a format of file. Is is not really in disk, but in memory so called 'Virtual filesystem'.

## /sbin

Contains commands for system managers like ifconfig, ethtool.

## /var

Temporarily contains data generated while running system.
The data can be changed.

/usr directory is mounted read-only.
/var has to contain everything written in /usr directory while running system.

Moving /var into /usr is also good.
/var cannot be connected to /usr with link.
So usually link /var with /usr/var.

It is not a general way to add a directory in /var.

### subdirectories

- /var/cache

  + contains cached data from applications.
  + these data is from the result of I/O.
  + caches can be removed.

- /var/lib

  + application and system related status info
  + status info generally used to keep status of applications.
  + It has to be available after reboot.
  + No output logging, no data spooling.
  + Application uses /var/lib for subdirectory.

- /var/local

  + is about all softwares' variable infos connected to /usr/local.
  + this directory is generated by admin.
  + any infos that included in other categories cannot be in /var/local.

- /var/lock

  + is for lock files.
  + the naming is LCK..(device name)
  + for example, LCK..ttyS0 for /dev/ttyS0

- /var/opt

  + has variable datas related to opt
  + all packages in /opt installs its variable data in /var/opt

- /var/run

  + is for run time variable datas.
  + Contains system information data written after system boot.
  + The files have to be removed when system boots.


- /var/tmp

  + is for temporary files that is preserved while system reboots.
  + After reboot, all files and directories are removed.

- /var/log

  + contains system log files.

- /var/ftp

- /var/spool

  + includes data will be processed later.
  + usually removed after the process.

  + /var/spool/mail

    * Each users' mail files.

  + /var/spool/lpd

    * A temporary directory for printing


## /lost+found

It is not only at /.
Can exist at each file system.
Generated after file system check or executing recovering utilities.
inodes disconnected are here.
