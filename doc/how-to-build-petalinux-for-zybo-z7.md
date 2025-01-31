# 1. Download Petalinux Installer

Download Petalinux Installer from https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/embedded-design-tools.html

# 2. Install Ubuntu 20.04 on WSL2 

```
> wsl --install Ubuntu-20.04
```

# 3. Build Petalinux

1. Create directories: `/opt/petalinux` and `/opt/petalinux/build`.

```
$ sudo mkdir -p /opt/petalinux/build
```

2. Make `/opt/petalinux` and its below accessible from anyone

```
$ sudo chmod 777 /opt/petalinux -R
```

3. Change current directory to `/opt/petalinux`

```
$ cd /opt/petalinux
```

4. Copy Petalinux Installer

```
$ cp ~/petalinux-v2024.2-11062026-installer.run /opt/petalinux/
```

5. Make Petalinux Installer executable

```
$ chmod +x petalinux-v2024.2-11062026-installer.run
```

6. Add i386 architecture support

```
$ sudo dpkg --add-architecture i386
```

7. Change symbolic links: `/usr/bin/sh` and `/bin/sh`

```
$ sudo unlink /usr/bin/sh
$ sudo ln -s /usr/bin/bash /usr/bin/sh
$ sudo unlink /bin/sh
$ sudo ln -s /bin/bash /bin/sh
$ ls -l /usr/bin/sh /bin/sh
lrwxrwxrwx 1 root root 13 Jan 30 20:07 /bin/sh -> /usr/bin/bash
lrwxrwxrwx 1 root root 13 Jan 30 20:07 /usr/bin/sh -> /usr/bin/bash
```

8. Install required packages

```
$ sudo apt update
$ sudo apt install tofrodos gawk xvfb git libncurses5-dev tftp-hpa zlib1g-dev zlib1g-dev:i386 libssl-dev flex bison chrpath socat autoconf libtool texinfo gcc-multilib libsdl1.2-dev libglib2.0-dev screen pax xterm build-essential libtinfo5 xzip locales-all bind9-dnsutils
```

9. Restart WSL2

```
> wsl --shutdown
> wsl
```

10. Change current directory to `/opt/petalinux`

```
cd /opt/petalinux
```

11. Run Petalinux Installer

```
$ ./petalinux-v2024.2-11062026-installer.run /opt/petalinux/build/
PetaLinux CMD tools installer version 2024.2
============================================
[WARNING] This is not a supported OS
[INFO] Checking free disk space
[INFO] Checking installed tools
[INFO] Checking installed development libraries
[INFO] Checking network and other services
[WARNING] No tftp server found - please refer to "UG1144  PetaLinux Tools Documentation Reference Guide" for its impact and solution

LICENSE AGREEMENTS

PetaLinux SDK contains software from a number of sources.  Please review
the following licenses and indicate your acceptance of each to continue.

You do not have to accept the licenses, however if you do not then you may
not use PetaLinux SDK.

Use PgUp/PgDn to navigate the license viewer, and press 'q' to close

Press Enter to display the license agreements
Do you accept Xilinx End User License Agreement? [y/N] > y
Do you accept Third Party End User License Agreement? [y/N] > y
Enter target directory for SDK (default: /opt/petalinux): /opt/petalinux/build
[INFO] Installing PetaLinux SDK...done
[INFO] Setting it up...done
[INFO] Extracting xsct tarball...done
[INFO] PetaLinux SDK has been successfully set up and is ready to be used.
```

12. Import settings

```
$ source build/settings.sh
*************************************************************************************************************************************************
The PetaLinux source code and images provided/generated are for demonstration purposes only.
Please refer to https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/2741928025/Moving+from+PetaLinux+to+Production+Deployment
for more details
*************************************************************************************************************************************************
PetaLinux environment set to '/opt/petalinux/build'
[WARNING] This is not a supported OS
[INFO] Checking free disk space
[INFO] Checking installed tools
[INFO] Checking installed development libraries
[INFO] Checking network and other services
[WARNING] No tftp server found - please refer to "UG1144 2024.2 PetaLinux Tools Documentation Reference Guide" for its impact and solution
```

13. Create Petalinux project

```
$ petalinux-create --type project --template zynq --name SampleProject
[NOTE] Argument: "--type project" has been deprecated. It is recommended to start using new python command line Argument.
[NOTE] Use: petalinux-create project [OPTIONS]
[INFO] Create project: SampleProject
[INFO] New project successfully created in /opt/petalinux/SampleProject
```

14. Change current directory to the created project directory

```
$ cd /opt/petalinux/SampleProject
```

15. Copy hardware design file

```
$ cp ~/design_1.xsa /opt/petalinux/
```

16. Configure the Petalinux project

```
$ petalinux-config --get-hw-description=../design_1.xsa
[INFO] Getting hardware description
[INFO] Renaming design_1.xsa to system.xsa
[INFO] Bitbake is not available, some functionality may be reduced.
[INFO] Using HW file: /opt/petalinux/SampleProject/project-spec/hw-description/system.xsa
[INFO] Getting Platform info from HW file
[INFO] Menuconfig project
[INFO] Silentconfig rootfs
[INFO] Generating configuration files
Image Packaging Configuration > Root filesystem type: EXT4 (SD/eMMC/SATA/USB)

[INFO] Generating machine conf file
[INFO] Generating plnxtool conf file
[INFO] Generating workspace directory
NOTE: Starting bitbake server...
NOTE: Started PRServer with DBfile: /opt/petalinux/SampleProject/build/cache/prserv.sqlite3, Address: 127.0.0.1:45383, PID: 5400
INFO: Specified workspace already set up, leaving as-is
INFO: Enabling workspace layer in bblayers.conf
[INFO] Successfully configured project
```

17. Build the Petalinux project

```
$ petalinux-build
[INFO] Building project
[INFO] Bitbake is not available, some functionality may be reduced.
[INFO] Using HW file: /opt/petalinux/SampleProject/project-spec/hw-description/system.xsa
[INFO] Getting Platform info from HW file
[INFO] Silentconfig project
[INFO] Silentconfig rootfs
[INFO] Generating configuration files
[INFO] Generating workspace directory
NOTE: Starting bitbake server...
NOTE: Started PRServer with DBfile: /opt/petalinux/SampleProject/build/cache/prserv.sqlite3, Address: 127.0.0.1:45207, PID: 5172
INFO: Specified workspace already set up, leaving as-is
[INFO] bitbake petalinux-image-minimal
NOTE: Started PRServer with DBfile: /opt/petalinux/SampleProject/build/cache/prserv.sqlite3, Address: 127.0.0.1:37313, PID: 5232
WARNING: XSCT has been deprecated. It will still be available for several releases. In the future, it's recommended to start new projects with SDT workflow.
WARNING: You are running bitbake under WSLv2, this works properly but you should optimize your VHDX file eventually to avoid running out of storage space
Loading cache: 100% |                                                                                                           | ETA:  --:--:--
Loaded 0 entries from dependency cache.
Parsing recipes: 100% |##########################################################################################################| Time: 0:03:08
Parsing of 5800 .bb files complete (0 cached, 5800 parsed). 8454 targets, 1106 skipped, 27 masked, 0 errors.
NOTE: Resolving any missing task queue dependencies
NOTE: Fetching uninative binary shim file:///opt/petalinux/SampleProject/components/yocto/downloads/uninative/6bf00154c5a7bc48adbf63fd17684bb87eb07f4814fbb482a3fbd817c1ccf4c5/x86_64-nativesdk-libc-4.6.tar.xz;sha256sum=6bf00154c5a7bc48adbf63fd17684bb87eb07f4814fbb482a3fbd817c1ccf4c5 (will check PREMIRRORS first)
Checking sstate mirror object availability: 100% |###############################################################################| Time: 0:01:00
Sstate summary: Wanted 2724 Local 0 Mirrors 2516 Missed 208 Current 0 (92% match, 0% complete)
NOTE: Executing Tasks
NOTE: Tasks Summary: Attempted 6008 tasks of which 5251 didn't need to be rerun and all succeeded.

Summary: There were 2 WARNING messages.
[INFO] Failed to copy built images to tftp dir: /tftpboot
[INFO] Successfully built project
```

18. Generate boot image

```
$ petalinux-package boot --force --fsbl images/linux/zynq_fsbl.elf --fpga images/linux/system.bit --u-boot
[INFO] File in BOOT BIN: "/opt/petalinux/SampleProject/images/linux/zynq_fsbl.elf"
[INFO] File in BOOT BIN: "/opt/petalinux/SampleProject/images/linux/system.bit"
[INFO] File in BOOT BIN: "/opt/petalinux/SampleProject/images/linux/u-boot.elf"
[INFO] File in BOOT BIN: "/opt/petalinux/SampleProject/images/linux/system.dtb"
[INFO] Generating zynq binary package BOOT.BIN...
[INFO]

****** Bootgen v2024.2
 **** Build date : Oct 21 2024-10:58:34
   ** Copyright 1986-2022 Xilinx, Inc. All Rights Reserved.
   ** Copyright 2022-2024 Advanced Micro Devices, Inc. All Rights Reserved.

[INFO]   : Bootimage generated successfully

[INFO] Binary is ready.
[INFO] Successfully Generated BIN File
[WARNING] Unable to access the TFTPBOOT folder /tftpboot!!!
[WARNING] Skip file copy to TFTPBOOT folder!!!
```

# 4. Create bootable microSD

Write the boot image and root filesystem (rootfs) to the microSD.
However, it is not easy for WSL2 to directly create partitions on the microSD.
Create a disk image with a boot partition and a rootfs partition on WSL2.
The created disk image is written to a microSD using Rufus on the host computer (Windows 11).

1. Change current directory to the created project directory

```
$ cd /opt/petalinux/SampleProject
```

2. Create 3.5 GB disk image

```
$ sudo truncate -s 3584M image.img
```

3. Assign the disk image as loopback device

```
$ sudo losetup -f
/dev/loop0
$ sudo losetup /dev/loop0 image.img
```

4. Create a boot partition and a rootfs partition

```
$ sudo parted /dev/loop0
(parted) unit MiB

(parted) mklabel msdos
Warning: The existing disk label on /dev/loop0 will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? Y

(parted) mkpart primary 0MiB 100MiB
Warning: You requested a partition from 0.00MiB to 100MiB (sectors 0..204799).
The closest location we can manage is 0.02MiB to 100MiB (sectors 34..204799).
Is this still acceptable to you?
Yes/No? Y
Warning: The resulting partition is not properly aligned for best performance: 34s % 2048s != 0s
Ignore/Cancel? I
Error: Partition(s) 1 on /dev/loop0 have been written, but we have been unable to inform the kernel of the change, probably because it/they are
in use.  As a result, the old partition(s) will remain in use.  You should reboot now before making further changes.
Ignore/Cancel? I

(parted) mkpart primary 100MiB 100%
Error: Partition(s) 1, 2 on /dev/loop0 have been written, but we have been unable to inform the kernel of the change, probably because it/they
are in use.  As a result, the old partition(s) will remain in use.  You should reboot now before making further changes.
Ignore/Cancel? I

(parted) p
Model: Loopback device (loopback)
Disk /dev/loop0: 3584MiB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:
Number  Start    End      Size     Type     File system  Flags
1      1.00MiB  100MiB   99.0MiB  primary  fat32        lba
2      100MiB   3584MiB  3484MiB  primary  ext4
```

5. Unassign the disk image

```
$ sudo losetup -d /dev/loop0
```

6. Re-assign the disk image

```
$ sudo losetup -P -f --show image.img
/dev/loop0
$ ls /dev/loop0*
/dev/loop0  /dev/loop0p1  /dev/loop0p2
```

7. Format the filesystems

```
$ sudo mkfs.vfat -F 32 /dev/loop0p1
$ sudo mkfs.ext4 /dev/loop0p2
mke2fs 1.45.5 (07-Jan-2020)
Discarding device blocks: done
Creating filesystem with 891648 4k blocks and 223104 inodes
Filesystem UUID: 0ae84993-f9a7-4dec-b66f-6ee4b8b382c8
Superblock backups stored on blocks:
       32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

8. Mount the filesystems

```
$ sudo mkdir /mnt/loop0p1 /mnt/loop0p2
$ sudo mount -t vfat /dev/loop0p1 /mnt/loop0p1
$ sudo mount -t ext4 /dev/loop0p2 /mnt/loop0p2
```

9. Copy boot image

```
$ cd /opt/petalinux/SampleProject/images/linux
$ sudo cp BOOT.BIN boot.scr image.ub /mnt/loop0p1
```

10. Create Debian rootfs

```
$ sudo apt install debootstrap binfmt-support qemu qemu-user-static
$ sudo debootstrap --arch=armhf --foreign bookworm /mnt/loop0p2
I: Keyring file not available at /usr/share/keyrings/debian-archive-keyring.gpg; switching to https mirror https://deb.debian.org/debian
I: Retrieving InRelease
I: Retrieving Packages
I: Validating Packages
I: Resolving dependencies of required packages...
I: Resolving dependencies of base packages...
I: Checking component main on https://deb.debian.org/debian...
I: Retrieving libacl1 2.3.1-3
I: Validating libacl1 2.3.1-3
...
I: Chosen extractor for .deb packages: dpkg-deb
I: Extracting libacl1...
I: Extracting adduser...
...
```

11. Setup the Debian base system

```
$ sudo cp /usr/bin/qemu-arm-static /mnt/loop0p2/usr/bin
$ sudo update-binfmts --import qemu-arm
$ sudo chroot /mnt/loop0p2
# export LANG=C
# /debootstrap/debootstrap --second-stage
I: Installing core packages...
I: Unpacking required packages...
I: Unpacking libacl1:armhf...
...
I: Configuring the base system...
...
I: Base system installed successfully.
```

12. Setup user and exit

```
# su
# passwd
# adduser ubuntu
# apt update
# exit
```

13. Unmount the filesystems

```
$ sudo umount /mnt/loop0p1
$ sudo umount /mnt/loop0p2 
```

14. Unassign the disk image

```
$ sudo losetup -d /dev/loop0
```

15. Write the disk image to microSD by using Rufus (https://rufus.ie/en/)

# References

- https://qiita.com/iwatake2222/items/ece0cdf83e6e1908fad8
- https://qiita.com/iwatake2222/items/6e6915f7318689818368
- https://zenn.dev/gnico/articles/2aef82b7adef44
