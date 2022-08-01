# LPIC-1: Linux Installation and Package Management

## Lesson 102-1: Design hard disk layout

To succeed in this objective, you need to understand the relationship between disks, partitions, filesystems and volumes.

Think of a disk (or storage device, since modern devices do not contain any “disks” at all) as a “physical container” for you data.

Before a disk can be used by a computer it needs to be partitioned. A partition is a logical subset of the physical disk, like a logical “fence”. Partitioning is a way to “compartmentalize” information stored on the disk, separating, for example, operating system data from user data.

Every disk needs at least one partition, but can have multiple partitions if needed, and information about them is stored in a partition table. This table includes information about the first and last sectors of the partition and its type, as well as further details on each partition.

Inside each partition there is a filesystem. The filesystem describes the way the information is actually stored on the disk. This information includes how the directories are organized, what is the relationship between them, where is the data for each file, etc.

Partitions cannot span multiple disks. But using the Logical Volume Manager (LVM) multiple partitions can be combined, even across disks, to form a single logical volume.

Logical volumes abstract the limitations of the physical devices and let your work with “pools” of disk space that can be combined or distributed in a much more flexible way than traditional partitions. LVM is useful in situations where you would need to add more space to a partition without having to migrate the data to a larger device.

In this objective you will learn how to design a disk partitioning scheme for a Linux system, allocating filesystems and swap space to separate partitions or disks when needed.

How to create and manage partitions and filesystems will be discussed in other lessons. We will discuss an overview of LVM in this objective, but a detailed explanation is out of the scope.

### Mount Points
Before a filesystem can be accessed on Linux it needs to be mounted. This means attaching the filesystem to a specific point in your system’s directory tree, called a mount point.

When mounted, the contents of the filesystem will be available under the mount point. For example, imagine you have a partition with your users' personal data (their home directories), containing the directories `/john`, `/jack` and `/carol`. When mounted under `/home`, the contents of those directories will be available under `/home/john`, `/home/jack` and `/home/carol`.

The mount point must exist before mounting the filesystem. You cannot mount a partition under `/mnt/userdata` if this directory does not exist. However if the directory does exist and contains files, those files will be unavailable until you unmount the filesystem. If you list the contents of the directory, you will see the files stored on the mounted filesystem, not the original contents of the directory.

Filesystems can be mounted anywhere you want. However, there are some good practices that should be followed to make system administration easier.

Traditionally, `/mnt` was the directory under which all external devices would be mounted and a number of pre-configured anchor points for common devices, like CD-ROM drives (`/mnt/cdrom`) and floppy disks (`/mnt/floppy`) existed under it.

This has been superseded by `/media`, which is now the default mount point for any user-removable media (e.g. external disks, USB flash drives, memory card readers, optical disks, etc.) connected to the system.

On most modern Linux distributions and desktop environments, removable devices are automatically mounted under `/media/USER/LABEL` when connected to the system, where `USER` is the username and `LABEL` is the device label. For example, a USB flash drive with the label `FlashDrive` connected by the user `john` would be mounted under `/media/john/FlashDrive/`. The way this is handled is different depending on the desktop environment.

That being said, whenever you need to manually mount a filesystem, it is good practice to mount it under `/mnt`. The specific commands to control the mounting and unmounting of filesystems under Linux will be discussed in another lesson.

### Keeping Things Separated
On Linux, there are some directories that you should consider keeping on separate partitions. There are many reasons for this: for example, by keeping bootloader-related files (stored on `/boot`) on a boot partition, you ensure your system will still be able to boot in case of a crash on the root filesystem.

Keeping user’s personal directories (under `/home`) on a separate partition makes it easier to reinstall the system without the risk of accidentally touching user data. Keeping data related to a web or database server (usually under `/var`) on a separate partition (or even a separate disk) makes system administration easier should you need to add more disk space for those use cases.

There may even be performance reasons to keep certain directories on separate partitions. You may want to keep the root filesystem (`/`) on a speedy SSD unit, and bigger directories like `/home` and `/var` on slower hard disks which offer much more space for a fraction of the cost.

 ### The Boot Partition (`/boot`)
The boot partition contains files used by the bootloader to load the operating system. On Linux systems the bootloader is usually GRUB2 or, on older systems, GRUB Legacy. The partition is usually mounted under /boot and its files are stored in `/boot/grub`.

Technically a boot partition is not needed, since in most cases GRUB can mount the root partition (`/`) and load the files from a separate `/boot` directory.

However, a separate boot partition may be desired for safety (ensuring the system will boot even in case of a root filesystem crash), or if you wish to use a filesystem which the bootloader cannot understand in the root partition, or if it uses an unsupported encryption or compression method.

The boot partition is usually the first partition on the disk. This is because the original IBM PC BIOS addressed disks using cylinders, heads and sectors (CHS), with a maximum of 1024 cylinders, 256 heads and 63 sectors, resulting in a maximum disk size of 528 MB (504 MB under MS-DOS). This means that anything past this mark would not be accessible on legacy systems, unless a different disk addressing scheme (like Logical Block Addressing, LBA) was used.

So for maximum compatibility, the boot partition is usually located at the start of the disk and ends before cylinder 1024 (528 MB), ensuring that no matter what, the machine will be always able to load the kernel.

Since the boot partition only stores the files needed by the bootloader, the initial RAM disk and kernel images, it can be quite small by today’s standards. A good size is around 300 MB.

### The EFI System Partition (ESP)
The EFI System Partition (ESP) is used by machines based on the Unified Extensible Firmware Interface (UEFI) to store boot loaders and kernel images for the operating systems installed.

This partition is formatted in a FAT-based filesystem. On a disk partitioned with a GUID Partition Table it has a globally unique identifier of `C12A7328-F81F-11D2-BA4B-00A0C93EC93B`. If the disk was formatted under the MBR partitioning scheme the partition ID is `0xEF`.

On machines running Microsoft Windows this partition is usually the first one on the disk, although this is not required. The ESP is created (or populated) by the operating system upon installation, and on a Linux system is mounted under `/boot/efi`.

### The `/home` Partition
Each user in the system has a home directory to store personal files and preferences, and most of them are located under `/home`. Usually the home directory is the same as the username, so the user John would have his directory under `/home/john`.

However there are exceptions. For example the home directory for the root user is `/root` and some system services may have associated users with home directories elsewhere.

There is no rule to determine the size of a partition for the `/home` directory (the home partition). You should take into account the number of users in the system and how it will be used. A user which only does web browsing and word processing will require less space than one who works with video editing, for example.

### Variable Data (`/var`)
This directory contains “variable data”, or files and directories the system must be able to write to during operation. This includes system logs (in `/var/log`), temporary files (`/var/tmp`) and cached application data (in `/var/cache`).

`/var/www/html` is also the default directory for the data files for the Apache Web Server and `/var/lib/mysql` is the default location for database files for the MySQL server. However, both of these can be changed.

One good reason for putting `/var` in a separate partition is stability. Many applications and processes write to /var and subdirectories, like `/var/log` or `/var/tmp`. A misbehaved process may write data until there is no free space left on the filesystem.

If `/var` is under `/` this may trigger a kernel panic and filesystem corruption, causing a situation that is difficult to recover from. But if `/var` is kept under a separate partition, the root filesystem will be unaffected.

Like in `/home` there is no universal rule to determine the size of a partition for `/var`, as it will vary with how the system is used. On a home system, it may take only a few gigabytes. But on a database or web server much more space may be needed. In such scenarios, it may be wise to put `/var` on a partition on a different disk than the root partition adding an extra layer of protection against physical disk failure.

### Swap
The swap partition is used to swap memory pages from RAM to disk as needed. This partition needs to be of a specific type, and set-up with a proper utility called `mkswap` before it can be used.

The swap partition cannot be mounted like the others, meaning that you cannot access it like a normal directory and peek at its contents.

A system can have multiple swap partitions (though this is uncommon) and Linux also supports the use of swap files instead of partitions, which can be useful to quickly increase swap space when needed.

The size of the swap partition is a contentious issue. The old rule from the early days of Linux (“twice the amount of RAM”) may not apply anymore depending on how the system is being used and the amount of physical RAM installed.

On the documentation for Red Hat Enterprise Linux 7, Red Hat recommends the following:

Amount of RAM | Recommended Swap Size | Recommended Swap Size with Hibernation
--- | --- | ---
< 2 GB of RAM | 2x the amount of RAM | 3x the amount of RAM
2-8 GB of RAM | Equal to the amount of RAM | 2x the amount of RAM
8-64 GB of RAM | At least 4 GB | 1.5x the amount of RAM
> 64 GB of RAM | At least 4 GB | Not recommended

Of course the amount of swap can be workload dependent. If the machine is running a critical service, such as a database, web or SAP server, it is wise to check the documentation for these services (or your software vendor) for a recommendation.

```
Note
For more on creating and enabling swap partitions and swap files, see Objective 104.1 of LPIC-1.
```

### LVM
We have already discussed how disks are organized into one or more partitions, with each partition containing a filesystem which describes how files and associated metadata are stored. One of the downsides of partitioning is that the system administrator has to decide beforehand how the available disk space on a device will be distributed. This can present some challenges later, if a partition requires more space than originally planned. Of course partitions can be resized, but this may not be possible if, for example, there is no free space on the disk.

Logical Volume Management (LVM) is a form of storage virtualization that offers system administrators a more flexible approach to managing disk space than traditional partitioning. The goal of LVM is to facilitate managing the storage needs of your end users. The basic unit is the Physical Volume (PV), which is a block device on your system like a disk partition or a RAID array.

PVs are grouped into Volume Groups (VG) which abstract the underlying devices and are seen as a single logical device, with the combined storage capacity of the component PVs.

Each volume in a Volume Group is subdivided into fixed-sized pieces called extents. Extents on a PV are called Physical Extents (PE), while those on a Logical Volume are Logical Extents (LE). Generally, each Logical Extent is mapped to a Physical Extent, but this can change if features like disk mirroring are used.

Volume Groups can be subdivided into Logical Volumes (LVs), which functionally work in a similar way to partitions but with more flexibility.

The size of a Logical Volume, as specified during its creation, is in fact defined by the size of the physical extents (4 MB by default) multiplied by the number of extents on the volume. From this it is easy to understand that to grow a Logical Volume, for example, all that the system administrator has to do is add more extents from the pool available in the Volume Group. Likewise, extents can be removed to shrink the LV.

After a Logical Volume is created it is seen by the operating system as a normal block device. A device will be created in `/dev`, named as `/dev/VGNAME/LVNAME`, where `VGNAME` is the name of the Volume Group, and `LVNAME` is the name of the Logical Volume.

These devices can be formatted with a desired filesystem using standard utilities (like `mkfs.ext4`, for example) and mounted using the usual methods, either manually with the `mount` command or automatically by adding them to the `/etc/fstab` file.

____

### Lesson 102-2: Install a Boot Manager

When a computer is powered on the first software to run is the boot loader. This is a piece of code whose sole purpose is to load an operating system kernel and hand over control to it. The kernel will load the necessary drivers, initialize the hardware and then load the rest of the operating system.

GRUB is the boot loader used on most Linux distributions. It can load the Linux kernel or other operating systems, such as Windows, and can handle multiple kernel images and parameters as separate menu entries. Kernel selection at boot is done via a keyboard-driven interface, and there is a command-line interface for editing boot options and parameters.

Most Linux distributions install and configure GRUB (actually, GRUB 2) automatically, so a regular user does not need to think about that. However, as a system administrator, it is vital to know how to control the boot process so you can recover the system from a boot failure after a failed kernel upgrade, for example.

In this lesson you will learn about how to install, configure and interact with GRUB.

### GRUB Legacy vs. GRUB 2
The original version of GRUB (Grand Unified Bootloader), now known as GRUB Legacy was developed in 1995 as part of the GNU Hurd project, and later was adopted as the default boot loader of many Linux distributions, replacing earlier alternatives such as LILO.

GRUB 2 is a complete rewrite of GRUB aiming to be cleaner, safer, more robust, and more powerful. Among the many advantages over GRUB Legacy are a much more flexible configuration file (with many more commands and conditional statements, similar to a scripting language), a more modular design and better localization/internationalization.

There is also support for themes and graphical boot menus with splash screens, the ability to boot LiveCD ISOs directly from the hard drive, better support for non-x86 architectures, universal support for UUIDs (making it easier to identify disks and partitions) and much more.

GRUB Legacy is no longer under active development (the last release was 0.97, in 2005), and today most major Linux distributions install GRUB 2 as the default boot loader. However, you may still find systems using GRUB Legacy, so it is important to know how to use it and where it is different from GRUB 2.

### Where is the Bootloader?
Historically, hard disks on IBM PC compatible systems were partitioned using the MBR partitioning scheme, created in 1982 for IBM PC-DOS (MS-DOS) 2.0.

In this scheme, the first 512-byte sector of the disk is called the Master Boot Record and contains a table describing the partitions on the disk (the partition table) and also bootstrap code, called a bootloader.

When the computer is turned on, this very minimal (due to size restrictions) bootloader code is loaded, executed and passes control to a secondary boot loader on disk, usually located in a 32 KB space between the MBR and the first partition, which in turn will load the operating system(s).

On an MBR-partitioned disk, the boot code for GRUB is installed to the MBR. This loads and passes control to a “core” image installed between the MBR and the first partition. From this point, GRUB is capable of loading the rest of the needed resources (menu definitions, configuration files and extra modules) from disk.

However, MBR has limitations on the number of partitions (originally a maximum of 4 primary partitions, later a maximum of 3 primary partitions with 1 extended partition subdivided into a number of logical partitions) and maximum disk sizes of 2 TB. To overcome these limitations a new partitioning scheme called GPT (GUID Partition Table), part of the UEFI (Unified Extensible Firmware Interface) standard, was created.

GPT-partitioned disks can be used either with computers with the traditional PC BIOS or ones with UEFI firmware. On machines with a BIOS, the second part of GRUB is stored in a special BIOS boot partition.

On systems with UEFI firmware, GRUB is loaded by the firmware from the files `grubia32.efi` (for 32-Bit systems) or `grubx64.efi` (for 64-Bit systems) from a partition called the ESP (EFI System Partition).

### The `/boot` Partition
On Linux the files necessary for the boot process are usually stored on a boot partition, mounted under the root file system and colloquially referred to as `/boot`.

A boot partition is not needed on current systems, as boot loaders such as GRUB can usually mount the root file system and look for the needed files inside a `/boot` directory, but it is good practice as it separates the files needed for the boot process from the rest of the filesystem.

This partition is usually the first one on the disk. This is because the original IBM PC BIOS addressed disks using Cylinders, Heads and Sectors (CHS), with a maximum of 1024 cylinders, 256 heads and 63 sectors, resulting in a maximum disk size of 528 MB (504 MB under MS-DOS). This means that anything past this mark would not be accessible, unless a different disk addressing scheme (like LBA, Logical Block Addressing) was used.

So for maximum compatibility, the `/boot` partition is usually located at the start of the disk and ends before cylinder 1024 (528 MB), ensuring that the machine will always be able to load the kernel. The recommended size for this partition on a current machine is 300 MB.

Other reasons for a separate `/boot` partition are encryption and compression since some methods may not be supported by GRUB 2 yet, or if you need to have the system root partition (`/`) formatted using an unsupported file system.

### Contents of the Boot Partition
The contents of the `/boot` partition may vary with system architecture or the boot loader in use, but on a x86-based system you will usually find the files below. Most of these are named with a `-VERSION` suffix, where `-VERSION` is the version of the corresponding Linux kernel. So, for example, a configuration file for the Linux kernel version `4.15.0-65-generic` would be called `config-4.15.0-65-generic`.

`Config file`
- This file, usually called `config-VERSION` (see example above), stores configuration parameters for the Linux kernel. This file is generated automatically when a new kernel is compiled or installed and should not be directly modified by the user.

`System map`
- This file is a look-up table matching symbol names (like variables or functions) to their corresponding position in memory. This is useful when debugging a kind of system failure known as a kernel panic, as it allows the user to know which variable or function was being called when the failure occurred. Like the config file, the name is usually `System.map-VERSION` (e.g. `System.map-4.15.0-65-generic`).

`Linux kernel`
- This is the operating system kernel proper. The name is usually `vmlinux-VERSION` (e.g. `vmlinux-4.15.0-65-generic`). You may also find the name `vmlinuz` instead of `vmlinux`, the `z` at the end meaning that the file has been compressed.

` Initial RAM disk`
- This is usually called `initrd.img-VERSION` and contains a minimal root file system loaded into a RAM disk, containing utilities and kernel modules needed so the kernel can mount the real root filesystem.

`Boot loader related files`
- On systems with GRUB installed, these are usually located on `/boot/grub` and include the GRUB configuration file (`/boot/grub/grub.cfg` for GRUB 2 or `/boot/grub/menu.lst` in case of GRUB Legacy), modules (in `/boot/grub/i386-pc`), translation files (in `/boot/grub/locale`) and fonts (in `/boot/grub/fonts`).

### GRUB 2

### Installing GRUB 2
GRUB 2 can be installed using the `grub-install` utility. If you have a non-booting system you will need to boot using a Live CD or rescue disc, find out which is the boot partition for your system, mount it and then run the utility.

```
Note
The commands below assume that you are logged in as root. If not, first run sudo su - to “become” root. When finished, type exit to log out and return to a regular user.
```

The first disk on a system is usually the boot device and you may need to know whether there is a boot partition on the disk. This can be done with the `fdisk` utility. To list all the partitions on the first disk of your machine, use:

```
# fdisk -l /dev/sda
Disk /dev/sda: 111,8 GiB, 120034123776 bytes, 234441648 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x97f8fef5

Device     Boot    Start       End   Sectors   Size Id Type
/dev/sda1  *        2048   2000895   1998848   976M 83 Linux
/dev/sda2        2002942 234440703 232437762 110,9G  5 Extended
/dev/sda5        2002944  18008063  16005120   7,6G 82 Linux swap / Solaris
/dev/sda6       18010112 234440703 216430592 103,2G 83 Linux
```

The boot partition is identified with the `*` under the boot column. In the example above, it is `/dev/sda1`.

Now, create a temporary directory under `/mnt` and mount the partition under it:

```
# mkdir /mnt/tmp
# mount /dev/sda1 /mnt/tmp
```

Then run `grub-install`, pointing it to the boot device (not the partition) and the directory where the boot partition is mounted. If your system does have a dedicated boot partition, the command is:

```
# grub-install --boot-directory=/mnt/tmp /dev/sda
```

If you are installing to a system which does not have a boot partition, but just a `/boot` directory on the root filesystem, point `grub-install` to it. So, the command is:

```
# grub-install --boot-directory=/boot /dev/sda
```

### Configuring GRUB 2
The default configuration file for GRUB 2 is `/boot/grub/grub.cfg`. This file is automatically generated and manual editing is not recommended. To make changes to the GRUB configuration, you need to edit the file `/etc/default/grub` and then run the `update-grub` utility to generate a compliant file.

```
Note
update-grub is usually a shortcut to grub-mkconfig -o /boot/grub/grub.cfg, so they produce the same results.
```

There are some options in the file `/etc/default/grub` that control the behaviour of GRUB 2, like the default kernel to boot, timeout, extra command line parameters, etc. The most important ones are:

`GRUB_DEFAULT=`
- The default menu entry to boot. This can be a numeric value (like `0`, `1`, etc.), the name of a menu entry (like `debian`) or `saved`, which is used in conjunction with `GRUB_SAVEDEFAULT=`, explained below. Keep in mind that menu entries start at zero, so the first menu entry is `0`, the second is `1`, etc.

`GRUB_SAVEDEFAULT=`
- If this option is set to `true` and `GRUB_DEFAULT=` is set to `saved`, then the default boot option will always be the last one selected in the boot menu.

`GRUB_TIMEOUT=`
- The timeout, in seconds, before the default menu entry is selected. If set to `0` the system will boot the default entry without showing a menu. If set to `-1` the system will wait until the user selects an option, no matter how long it takes.

`GRUB_CMDLINE_LINUX=`
- This lists command line options that will be added to entries for the Linux kernel.

`GRUB_CMDLINE_LINUX_DEFAULT=`
- By default, two menu entries are generated for each Linux kernel, one with the default options and one entry for recovery. With this option you can add extra parameters that will be added only to the default entry.

`GRUB_ENABLE_CRYPTODISK=`
- If set to `y`, commands like `grub-mkconfig`, `update-grub` and `grub-install` will look for encrypted disks and add the commands needed to access them during boot. This disables automatic booting (`GRUB_TIMEOUT=` with any value other than `-1`) because a passphrase is needed to decrypt the disks before they can be accessed.

### Managing Menu Entries
When `update-grub` is run, GRUB 2 will scan for kernels and operating systems on the machine and generate the corresponding menu entries on the `/boot/grub/grub.cfg` file. New entries can be manually added to the script files within the `/etc/grub.d` directory.

These files should be executable, and are processed in numerical order by `update-grub`. Therefore `05_debian_theme` is processed before `10_linux` and so on. Custom menu entries are usually added to the `40_custom` file.

The basic syntax for a menu entry is shown below:

```
menuentry "Default OS" {
    set root=(hd0,1)
    linux /vmlinuz root=/dev/sda1 ro quiet splash
    initrd /initrd.img
}
```

The first line always starts with `menuentry` and ends with `{`. The text between quotes will be shown as the entry label on the GRUB 2 boot menu.

The `set root` parameter defines the disk and partition where the root file system for the operating system is located. Please note that on GRUB 2 disks are numbered from zero, so `hd0` is the first disk (`sda` under Linux), `hd1` the second, and so on. Partitions, however, are numbered starting from one. In the example above the root file system is located at the first disk (`hd0`), first partition (`,1`), or `sda1`.

Instead of directly specifying the device and partition you can also have GRUB 2 search for a file system with a specific label or UUID (Universally Unique Identifier). For that, use the parameter search `--set=root` followed by the `--label` parameter and file system label to search for, or `--fs-uuid` followed by the UUID of the file system.

You can find the UUID of a filesystem with the command below:

```
$ ls -l /dev/disk/by-uuid/
total 0
lrwxrwxrwx 1 root root 10 nov  4 08:40 3e0b34e2-949c-43f2-90b0-25454ac1595d -> ../../sda5
lrwxrwxrwx 1 root root 10 nov  4 08:40 428e35ee-5ad5-4dcb-adca-539aba6c2d84 -> ../../sda6
lrwxrwxrwx 1 root root 10 nov  5 19:10 56C11DCC5D2E1334 -> ../../sdb1
lrwxrwxrwx 1 root root 10 nov  4 08:40 ae71b214-0aec-48e8-80b2-090b6986b625 -> ../../sda1
```

In the example above, the UUID for `/dev/sda1` is `ae71b214-0aec-48e8-80b2-090b6986b625`. Should you wish to set it as the root device for GRUB 2, the command would be `search --set=root --fs-uuid ae71b214-0aec-48e8-80b2-090b6986b625`.

When using the `search` command it is common to add the `--no-floppy` parameter so GRUB will not waste time searching on floppy disks.

The `linux` line indicates where the kernel for the operating system is located (in this case, the `vmlinuz` file at the root of the file system). After that, you can pass command line parameters to the kernel.

In the example above we specified the root partition (`root=/dev/sda1`) and passed three kernel parameters: the root partition should be mounted read-only (`ro`), most of the log messages should be disabled (`quiet`) and a splash screen should be shown (`splash`).

The `initrd` line indicates where the initial RAM disk is located. In the example above the file is `initrd.img`, located at the root of the file system.

```
Note
Most Linux distributions do not actually put the kernel and initrd at the root directory of the root file system. Instead, these are links to the actual files inside the /boot directory or partition.
```

The last line of a menu entry should contain only the character `}`.

### Interacting with GRUB 2
When booting a system with GRUB 2 you will see a menu of options. Use the arrow keys to select an option and `Enter` to confirm and boot the selected entry.

```
Tip
If you see just a countdown, but not a menu, press Shift to bring up the menu.
```

To edit an option, select it with the arrow keys and press `E`. This will show an editor window with the contents of the `menuentry` associated with that option, as defined in `/boot/grub/grub.cfg`.

After editing an option, type `Ctrl`+`X` or `F10` to boot, or `Esc` to return to the menu.

To enter the GRUB 2 shell, press `C` on the menu screen (or `Ctrl`+`C`) on the editing window). You will see a command prompt like this: `grub >`

Type `help` to see a list of all available commands, or press `Esc` to exit the shell and return to the menu screen.

```
Note
Remember this menu will not appear if GRUB_TIMEOUT is set to 0 in /etc/default/grub.
```

### Booting from the GRUB 2 Shell
You can use the GRUB 2 shell to boot the system in case a misconfiguration in a menu entry causes it to fail.

The first thing you should do is find out where the boot partition is. You can do that with the command `ls`, which will show you a list of the partitions and disks GRUB 2 has found.

```
grub> ls
(proc) (hd0) (hd0,msdos1)
```

In the example above, things are easy. There is only one disk (`hd0`) with only one partition on it: (`hd0,msdos1`).

The disks and partitions listed will be different in your system. In our example the first partition of `hd0` is called `msdos1` because the disk was partitioned using the MBR partitioning scheme. If it were partitioned using GPT, the name would be `gpt1`.

To boot Linux, we need a kernel and initial RAM disk (initrd). Let us check the contents of (`hd0,msdos1`):

```
grub> ls (hd0,msdos1)/
lost+found/ swapfile etc/ media/ bin/ boot/ dev/ home/ lib/ lib64/ mnt/ opt/ proc/ root/ run/ sbin/ srv/ sys/ tmp/ usr/ var/ initrd.img initrd.img.old vmlinuz cdrom/
```

You can add the `-l` parameter to `ls` to get a long listing, similar to what you would get on a Linux terminal. Use `Tab` to autocomplete disk, partition and file names.

Note that we have a kernel (`vmlinuz`) and initrd (`initrd.img`) images right on the root directory. If not, we could check the contents of `/boot` with list (`hd0,msdos1)/boot/`.

Now, set the boot partition:

```
grub> set root=(hd0,msdos1)
```

Load the Linux kernel with the `linux` command, followed by the path to the kernel and the `root=` option to tell the kernel where the root filesystem for the operating system is located.

```
grub> linux /vmlinuz root=/dev/sda1
```

Load the initial RAM disk with `initrd`, followed by the full path to the `initrd.img` file:

```
grub> initrd /initrd.img
```

Now, boot the system with `boot`.

### Booting from the Rescue Shell
In case of a boot failure GRUB 2 may load a rescue shell, a simplified version of the shell we mentioned before. You will recognize it by the command prompt, which displays as `grub rescue`>.

The process to boot a system from this shell is almost the same as shown before. However, you will need to load a few GRUB 2 modules to make things work.

After finding out which partition is the boot partition (with `ls`, as shown before), use the `set prefix=` command, followed by the full path to the directory containing the GRUB 2 files. Usually `/boot/grub`. In our example:

```
grub rescue> set prefix=(hd0,msdos1)/boot/grub
```

Now, load the modules `normal` and `linux` with the `insmod` command:

```
grub rescue> insmod normal
grub rescue> insmod linux
```

Next, set the boot partition with `set root=` as directed before, load the linux kernel (with `linux`), the initial RAM disk (`initrd`) and try to boot with `boot`.

### GRUB Legacy
### Installing GRUB Legacy from a Running System
To install GRUB Legacy on a disk from a running system we will use the `grub-install` utility. The basic command is `grub-install DEVICE` where `DEVICE` is the disk where you wish to install GRUB Legacy. An example would be `/dev/sda`.

```
# grub-install /dev/sda
```

Note that you need to specify the device where GRUB Legacy will be installed, like `/dev/sda/`, not the partition as in `/dev/sda1`.

By default GRUB will copy the needed files to the `/boot` directory on the specified device. If you wish to copy them to another directory, use the `--boot-directory=` parameter, followed by the full path to where the files should be copied to.

### Installing GRUB Legacy from a GRUB Shell
If you cannot boot the system for some reason and need to reinstall GRUB Legacy, you can do so from the GRUB shell on a GRUB Legacy boot disk.

From the GRUB shell (type `c` at the boot menu to get to the `grub>` prompt), the first step is to set the boot device, which contains the `/boot` directory. For example, if this directory is in the first partition of the first disk, the command would be:

```
grub> root (hd0,0)
```

If you do not know which device contains the `/boot` directory, you can ask GRUB to search for it with the find command, like below:

```
grub> find /boot/grub/stage1
 (hd0,0)
```

Then, set the boot partition as instructed above and use the `setup` command to install GRUB Legacy to the MBR and copy the needed files to the disk:

```
grub> setup (hd0)
```

When finished reboot the system and it should boot normally.

### Configuring GRUB Legacy Menu Entries and Settings
GRUB Legacy menu entries and settings are stored in the file `/boot/grub/menu.lst`. This is a simple text file with a list of commands and parameters, which can be edited directly with your favourite text editor.

Lines starting with `#` are considered comments, and blank lines are ignored.

A menu entry has at least three commands. The first one, `title`, sets the title of the operating system on the menu screen. The second one, `root`, tells GRUB Legacy which device or partition to boot from.

The third entry, `kernel`, specifies the full path to the kernel image that should be loaded when the corresponding entry is selected. Note that this path is relative to the device specified on the `root` parameter.

A simple example follows:

```
# This line is a comment
title My Linux Distribution
root (hd0,0)
kernel /vmlinuz root=/dev/hda1
```

Unlike GRUB 2, in GRUB Legacy both disks and partitions are numbered from zero. So, the command root (`hd0,0`) will set the boot partition as the first partition (`0`) of the first disk (`hd0`).

You can omit the `root` statement if you specify the boot device before the path on the `kernel` command. The syntax is the same, so:

```
kernel (hd0,0)/vmlinuz root=dev/hda1
```

is equivalent to:

```
root (hd0,0)
kernel /vmlinuz root=/dev/hda1
```

Both will load the file `vmlinuz` from the root directory (`/`) of the first partition of the first disk (`hd0,0`).

The parameter `root=/dev/hda1` after the `kernel` command tells the Linux kernel which partition should be used as the root filesystem. This is a Linux kernel parameter, not a GRUB Legacy command.

```
Note
To learn more about kernel parameters, see https://www.kernel.org/doc/html/v4.14/admin-guide/kernel-parameters.html.
```

You may need to specify the location of the initial RAM Disk image for the operating system with the `initrd` parameter. The full path to the file can be specified like in the `kernel` parameter, and you can also specify a device or partition before the path, e.g.:

```
# This line is a comment
title My Linux Distribution
root (hd0,0)
kernel /vmlinuz root=/dev/hda1
initrd /initrd.img
```

GRUB Legacy has a modular design, where modules (usually stored as `.mod` files in `/boot/grub/i386-pc`) can be loaded to add extra features, like support for unusual hardware, filesystems or new compression algorithms.

Modules are loaded using the `module` command, followed by the full path to the corresponding `.mod` file. Keep in mind that, like kernels and initrd images, this path is relative to the device specified in the `root` command.

The example below will load the module `915resolution`, needed to correctly set the framebuffer resolution on systems with Intel 800 or 900 series video chipsets.

```
module /boot/grub/i386-pc/915resolution.mod
```

### Chainloading other Operating Systems
GRUB Legacy can be used to load unsupported operating systems, like Windows, using a process called chainloading. GRUB Legacy is loaded first, and when the corresponding option is selected the bootloader for the desired system is loaded.

A typical entry for chainloading Windows would look like the one below:

```
# Load Windows
title Windows XP
root (hd0,1)
makeactive
chainload +1
boot
```

Let us walk through each parameter. As before, `root (hd0,1)` specifies the device and partition where the boot loader for the operating system we wish to load is located. In this example, the second partition of the first disk.

`makeactive`
- will set a flag indicating that this is an active partition. This only works on DOS primary partitions.

`chainload +1`
- tells GRUB to load the first sector of the boot partition. This is where bootloaders are usually located.

`boot`
- will run the bootloader and load the corresponding operating system.

___

## Lesson 102-3 Manage shared libraries

In this lesson we will be discussing shared libraries, also known as shared objects: pieces of compiled, reusable code like functions or classes, that are recurrently used by various programs.

To start with, we will explain what shared libraries are, how to identify them and where they are found. Next, we will go into how to configure their storage locations. Finally, we will show how to search for the shared libraries on which a particular program depends.

### Concept of Shared Libraries
Similar to their physical counterparts, software libraries are collections of code that are intended to be used by many different programs; just as physical libraries keep books and other resources to be used by many different people.

To build an executable file from a program’s source code, two important steps are necessary. First, the compiler turns the source code into machine code that is stored in so-called object files. Secondly, the linker combines the object files and links them to libraries in order to generate the final executable file. This linking can be done statically or dynamically. Depending on which method we go for, we will be talking about static libraries or, in case of dynamic linking, about shared libraries. Let us explain their differences.

`Static libraries`
- A static library is merged with the program at link time. A copy of the library code is embedded into the program and becomes part of it. Thus, the program has no dependencies on the library at run time because the program already contains the libraries code. Having no dependencies can be seen as an advantage since you do not have to worry about making sure the used libraries are always available. On the downside, statically linked programs are heavier.

`Shared (or dynamic) libraries`
- In the case of shared libraries, the linker simply takes care that the program references libraries correctly. The linker does, however, not copy any library code into the program file. At run time, though, the shared library must be available to satisfy the program’s dependencies. This is an economical approach to managing system resources as it helps reduce the size of program files and only one copy of the library is loaded in memory, even when it is used by multiple programs.

### Shared Object File Naming Conventions
The name of a shared library, also known as soname, follows a pattern which is made up of three elements:

- Library name (normally prefixed by `lib`)
- `so` (which stands for “shared object”)
- Version number of the library

Here an example: `libpthread.so.0`

By contrast, static library names end in `.a`, e.g. `libpthread.a`.

```
Note
Because the files containing shared libraries must be available when the program is executed, most Linux systems contain shared libraries. Since static libraries are only required in a dedicated file when a program is linked, they might not be present on an end user system.
```

`glibc` (GNU C library) is a good example of a shared library. On a Debian GNU/Linux 9.9 system, its file is named `libc.so.6`. Such rather general file names are normally symbolic links that point to the actual file containing a library, whose name contains the exact version number. In case of `glibc`, this symbolic link looks like this:

```
$ ls -l /lib/x86_64-linux-gnu/libc.so.6
lrwxrwxrwx 1 root root 12 feb  6 22:17 /lib/x86_64-linux-gnu/libc.so.6 -> libc-2.24.so
```

This pattern of referencing shared library files named by a specific version by more general file names is common practice.

Other examples of shared libraries include `libreadline` (which allows users to edit command lines as they are typed in and includes support for both Emacs and vi editing modes), `libcrypt` (which contains functions related to encryption, hashing, and encoding), or `libcurl` (which is a multiprotocol file transfer library).

Common locations for shared libraries in a Linux system are:

- `/lib`
- `/lib32`
- `/lib64`
- `/usr/lib`
- `/usr/local/lib`

```
Note
The concept of shared libraries is not exclusive to Linux. In Windows, for example, they are called DLL which stands for dynamic linked libraries.
```

### Configuration of Shared Library Paths
The references contained in dynamically linked programs are resolved by the dynamic linker (`ld.so` or `ld-linux.so`) when the program is run. The dynamic linker searches for libraries in a number of directories. These directories are specified by the library path. The library path is configured in the `/etc` directory, namely in the file `/etc/ld.so.conf` and, more common nowadays, in files residing in the `/etc/ld.so.conf.d` directory. Normally, the former includes just a single `include` line for `*.conf` files in the latter:

```
$ cat /etc/ld.so.conf
include /etc/ld.so.conf.d/*.conf
```

The `/etc/ld.so.conf.d` directory contains `*.conf` files:

```
$ ls /etc/ld.so.conf.d/
libc.conf  x86_64-linux-gnu.conf
```

These `*.conf` files must include the absolute paths to the shared library directories:

```
$ cat /etc/ld.so.conf.d/x86_64-linux-gnu.conf
# Multiarch support
/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu
```

The `ldconfig` command takes care of reading these config files, creating the aforementioned set of symbolic links that help to locate the individual libraries and finally of updating the cache file `/etc/ld.so.cache`. Thus, `ldconfig` must be run every time configuration files are added or updated.

Useful options for `ldconfig` are:

`-v, --verbose`
- Display the library version numbers, the name of each directory, and the links that are created:

```
$ sudo ldconfig -v
/usr/local/lib:
/lib/x86_64-linux-gnu:
    libnss_myhostname.so.2 -> libnss_myhostname.so.2
	libfuse.so.2 -> libfuse.so.2.9.7
	libidn.so.11 -> libidn.so.11.6.16
	libnss_mdns4.so.2 -> libnss_mdns4.so.2
	libparted.so.2 -> libparted.so.2.0.1
	(...)
```

So we can see, for example, how `libfuse.so.2` is linked to the actual shared object file `libfuse.so.2.9.7`.

`-p, --print-cache`
- Print the lists of directories and candidate libraries stored in the current cache:

```
$ sudo ldconfig -p
1094 libs found in the cache `/etc/ld.so.cache'
    libzvbi.so.0 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libzvbi.so.0
	libzvbi-chains.so.0 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libzvbi-chains.so.0
	libzmq.so.5 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libzmq.so.5
	libzeitgeist-2.0.so.0 (libc6,x86-64) => /usr/lib/x86_64-linux-gnu/libzeitgeist-2.0.so.0
	(...)
```

Note how the cache uses the fully qualified soname of the links:

```
$ sudo ldconfig -p |grep libfuse
	  libfuse.so.2 (libc6,x86-64) => /lib/x86_64-linux-gnu/libfuse.so.2
```

If we long list `/lib/x86_64-linux-gnu/libfuse.so.2`, we will find the reference to the actual shared object file `libfuse.so.2.9.7` which is stored in the same directory:

```
$ ls -l /lib/x86_64-linux-gnu/libfuse.so.2
lrwxrwxrwx 1 root root 16 Aug 21  2018 /lib/x86_64-linux-gnu/libfuse.so.2 -> libfuse.so.2.9.7
```

```
Note
Since it requires write access to /etc/ld.so.cache (owned by root), you must either be root or use sudo to invoke ldconfig. For more information about ldconfig switches, refer to its manual page.
```

In addition to the configuration files described above, the `LD_LIBRARY_PATH` environment variable can be used to add new paths for shared libraries temporarily. It is made up of a colon-separated (`:`) set of directories where libraries are looked up. To add, for example, `/usr/local/mylib` to the library path in the current shell session, you could type:

```
$ LD_LIBRARY_PATH=/usr/local/mylib
```

Now you can check its value:

```
$ echo $LD_LIBRARY_PATH
/usr/local/mylib
```

To add `/usr/local/mylib` to the library path in the current shell session and have it exported to all child processes spawned from that shell, you would type:

```
$ export LD_LIBRARY_PATH=/usr/local/mylib
```

To remove the `LD_LIBRARY_PATH` environment variable, just type:

```
$ unset LD_LIBRARY_PATH
```

To make the changes permanent, you can write the line

```
export LD_LIBRARY_PATH=/usr/local/mylib
```

in one of Bash’s initialization scripts such as `/etc/bash.bashrc` or `~/.bashrc`.

```
Note
LD_LIBRARY_PATH is to shared libraries what PATH is to executables. For more information about environment variables and shell configuration, refer to the respective lessons.
```

### Searching for the Dependencies of a Particular Executable
To look up the shared libraries required by a specific program, use the `ldd` command followed by the absolute path to the program. The output shows the path of the shared library file as well as the hexadecimal memory address at which it is loaded:

```
$ ldd /usr/bin/git
    linux-vdso.so.1 =>  (0x00007ffcbb310000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f18241eb000)
	libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f1823fd1000)
	libresolv.so.2 => /lib/x86_64-linux-gnu/libresolv.so.2 (0x00007f1823db6000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f1823b99000)
	librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f1823991000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f18235c7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f182445b000)
```

Likewise, we use `ldd` to search for the dependencies of a shared object:

```
$ ldd /lib/x86_64-linux-gnu/libc.so.6
	/lib64/ld-linux-x86-64.so.2 (0x00007fbfed578000)
	linux-vdso.so.1 (0x00007fffb7bf5000)
```

With the `-u` (or `--unused`) option `ldd` prints the unused direct dependencies (if they exist):

```
$ ldd -u /usr/bin/git
Unused direct dependencies:
	/lib/x86_64-linux-gnu/libz.so.1
	/lib/x86_64-linux-gnu/libpthread.so.0
	/lib/x86_64-linux-gnu/librt.so.1
```

The reason for unused dependencies is related to the options used by the linker when building the binary. Although the program does not need an unused library, it was still linked and labelled as `NEEDED` in the information about the object file. You can investigate this using commands such as `readelf` or `objdump`, which you will soon use in the explorational exercise.

___

## Lesson 102-4 Use Debian package management

A long time ago, when Linux was still in its infancy, the most common way to distribute software was a compressed file (usually a `.tar.gz` archive) with source code, which you would unpack and compile yourself.

However, as the amount and complexity of software grew, the need for a way to distribute pre-compiled software became clear. After all, not everyone had the resources, both in time and computing power, to compile large projects like the Linux kernel or an X Server.

Soon, efforts to standardize a way to distribute these software “packages” grew, and the first package managers were born. These tools made it much easier to install, configure or remove software from a system.

One of those was the Debian package format (`.deb`) and its package tool (`dpkg`). Today, they are widely used not only on Debian itself, but also on its derivatives, like Ubuntu and those derived from it.

Another package management tool that is popular on Debian-based systems is the Advanced Package Tool (`apt`), which can streamline many of the aspects of the installation, maintenance and removal of packages, making it much easier.

In this lesson, we will learn how to use both `dpkg` and `apt` to obtain, install, maintain and remove software on a Debian-based Linux system.

### The Debian Package Tool (dpkg)
The Debian Package (`dpkg`) tool is the essential utility to install, configure, maintain and remove software packages on Debian-based systems. The most basic operation is to install a `.deb` package, which can be done with:

```
# dkpg -i PACKAGENAME
```

Where `PACKAGENAME` is the name of the `.deb` file you want to install.

Package upgrades are handled the same way. Before installing a package, `dpkg` will check if a previous version already exists in the system. If so, the package will be upgraded to the new version. If not, a fresh copy will be installed.

### Dealing with Dependencies
More often than not, a package may depend on others to work as intended. For example, an image editor may need libraries to open JPEG files, or another utility may need a widget toolkit like Qt or GTK for its user interface.

`dpkg` will check if those dependencies are installed on your system, and will fail to install the package if they are not. In this case, `dpkg` will list which packages are missing. However it cannot solve dependencies by itself. It is up to the user to find the `.deb` packages with the corresponding dependencies and install them.

In the example below, the user tries to install the OpenShot video editor package, but some dependencies were missing:

```
# dpkg -i openshot-qt_2.4.3+dfsg1-1_all.deb
(Reading database ... 269630 files and directories currently installed.)
Preparing to unpack openshot-qt_2.4.3+dfsg1-1_all.deb ...
Unpacking openshot-qt (2.4.3+dfsg1-1) over (2.4.3+dfsg1-1) ...
dpkg: dependency problems prevent configuration of openshot-qt:
 openshot-qt depends on fonts-cantarell; however:
  Package fonts-cantarell is not installed.
 openshot-qt depends on python3-openshot; however:
  Package python3-openshot is not installed.
 openshot-qt depends on python3-pyqt5; however:
  Package python3-pyqt5 is not installed.
 openshot-qt depends on python3-pyqt5.qtsvg; however:
  Package python3-pyqt5.qtsvg is not installed.
 openshot-qt depends on python3-pyqt5.qtwebkit; however:
  Package python3-pyqt5.qtwebkit is not installed.
 openshot-qt depends on python3-zmq; however:
  Package python3-zmq is not installed.

dpkg: error processing package openshot-qt (--install):
 dependency problems - leaving unconfigured
Processing triggers for mime-support (3.60ubuntu1) ...
Processing triggers for gnome-menus (3.32.0-1ubuntu1) ...
Processing triggers for desktop-file-utils (0.23-4ubuntu1) ...
Processing triggers for hicolor-icon-theme (0.17-2) ...
Processing triggers for man-db (2.8.5-2) ...
Errors were encountered while processing:
 openshot-qt
```

As shown above, OpenShot depends on the `fonts-cantarell`, `python3-openshot`, `python3-pyqt5`, `python3-pyqt5.qtsvg`, `python3-pyqt5.qtwebkit` and `python3-zmq` packages. All of those need to be installed before the installation of OpenShot can succeed.

### Removing Packages
To remove a package, pass the `-r` parameter to `dpkg`, followed by the package name. For example, the following command will remove the unrar package from the system:

```
# dpkg -r unrar
(Reading database ... 269630 files and directories currently installed.)
Removing unrar (1:5.6.6-2) ...
Processing triggers for man-db (2.8.5-2) ...
```

The removal operation also runs a dependency check, and a package cannot be removed unless every other package that depends on it is also removed. If you try to do so, you will get an error message like the one below:

```
# dpkg -r p7zip
dpkg: dependency problems prevent removal of p7zip:
 winetricks depends on p7zip; however:
  Package p7zip is to be removed.
 p7zip-full depends on p7zip (= 16.02+dfsg-6).

dpkg: error processing package p7zip (--remove):
 dependency problems - not removing
Errors were encountered while processing:
 p7zip
```

You can pass multiple package names to `dpkg -r`, so they will all be removed at once.

When a package is removed, the corresponding configuration files are left on the system. If you want to remove everything associated with the package, use the `-P` (purge) option instead of `-r`.

```
Note
You can force dpkg to install or remove a package, even if dependencies are not met, by adding the --force parameter like in dpkg -i --force PACKAGENAME. However, doing so will most likely leave the installed package, or even your system, in a broken state. Do not use --force unless you are absolutely sure of what you are doing.
```

### Getting Package Information
To get information about a `.deb` package, such as its version, architecture, maintainer, dependencies and more, use the `dpkg` command with the `-I` parameter, followed by the filename of the package you want to inspect:

```
# dpkg -I google-chrome-stable_current_amd64.deb
 new Debian package, version 2.0.
 size 59477810 bytes: control archive=10394 bytes.
    1222 bytes,    13 lines      control
   16906 bytes,   457 lines   *  postinst             #!/bin/sh
   12983 bytes,   344 lines   *  postrm               #!/bin/sh
    1385 bytes,    42 lines   *  prerm                #!/bin/sh
 Package: google-chrome-stable
 Version: 76.0.3809.100-1
 Architecture: amd64
 Maintainer: Chrome Linux Team <chromium-dev@chromium.org>
 Installed-Size: 205436
 Pre-Depends: dpkg (>= 1.14.0)
 Depends: ca-certificates, fonts-liberation, libappindicator3-1, libasound2 (>= 1.0.16), libatk-bridge2.0-0 (>= 2.5.3), libatk1.0-0 (>= 2.2.0), libatspi2.0-0 (>= 2.9.90), libc6 (>= 2.16), libcairo2 (>= 1.6.0), libcups2 (>= 1.4.0), libdbus-1-3 (>= 1.5.12), libexpat1 (>= 2.0.1), libgcc1 (>= 1:3.0), libgdk-pixbuf2.0-0 (>= 2.22.0), libglib2.0-0 (>= 2.31.8), libgtk-3-0 (>= 3.9.10), libnspr4 (>= 2:4.9-2~), libnss3 (>= 2:3.22), libpango-1.0-0 (>= 1.14.0), libpangocairo-1.0-0 (>= 1.14.0), libuuid1 (>= 2.16), libx11-6 (>= 2:1.4.99.1), libx11-xcb1, libxcb1 (>= 1.6), libxcomposite1 (>= 1:0.3-1), libxcursor1 (>> 1.1.2), libxdamage1 (>= 1:1.1), libxext6, libxfixes3, libxi6 (>= 2:1.2.99.4), libxrandr2 (>= 2:1.2.99.3), libxrender1, libxss1, libxtst6, lsb-release, wget, xdg-utils (>= 1.0.2)
 Recommends: libu2f-udev
 Provides: www-browser
 Section: web
 Priority: optional
 Description: The web browser from Google
  Google Chrome is a browser that combines a minimal design with sophisticated technology to make the web faster, safer, and easier.
```

### Listing Installed Packages and Package Contents
To get a list of every package installed on your system, use the `--get-selections` option, as in `dpkg --get-selections`. You can also get a list of every file installed by a specific package by passing the `-L PACKAGENAME` parameter to `dpkg`, like below:

```
# dpkg -L unrar
/.
/usr
/usr/bin
/usr/bin/unrar-nonfree
/usr/share
/usr/share/doc
/usr/share/doc/unrar
/usr/share/doc/unrar/changelog.Debian.gz
/usr/share/doc/unrar/copyright
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/unrar-nonfree.1.gz
```

### Finding Out Which Package Owns a Specific File
Sometimes you may need to find out which package owns a specific file in your system. You can do so by using the `dpkg-query` utility, followed by the `-S` parameter and the path to the file in question:

```
# dpkg-query -S /usr/bin/unrar-nonfree
unrar: /usr/bin/unrar-nonfree
```

### Reconfiguring Installed Packages
When a package is installed there is a configuration step called post-install where a script runs to set-up everything needed for the software to run such as permissions, placement of configuration files, etc. This may also ask some questions of the user to set preferences on how the software will run.

Sometimes, due to a corrupt or malformed configuration file, you may wish to restore a package’s settings to its “fresh” state. Or you may wish to change the answers you gave to the initial configuration questions. To do this run the `dpkg-reconfigure` utility, followed by the package name.

This program will back-up the old configuration files, unpack the new ones in the correct directories and run the post-install script provided by the package, as if the package had been installed for the first time. Try to reconfigure the `tzdata` package with the following example:

```
# dpkg-reconfigure tzdata
```

### Advanced Package Tool (apt)
The Advanced Package Tool (APT) is a package management system, including a set of tools, that greatly simplifies package installation, upgrade, removal and management. APT provides features like advanced search capabilities and automatic dependency resolution.

APT is not a “substitute” for `dpkg`. You may think of it as a “front end”, streamlining operations and filling gaps in `dpkg` functionality, like dependency resolution.

APT works in concert with software repositories which contain the packages available to install. Such repositories may be a local or remote server, or (less common) even a CD-ROM disc.

Linux distributions, such as Debian and Ubuntu, maintain their own repositories, and other repositories may be maintained by developers or user groups to provide software not available from the main distribution repositories.

There are many utilities that interact with APT, the main ones being:

`apt-get`
- used to download, install, upgrade or remove packages from the system.

`apt-cache`
- used to perform operations, like searches, in the package index.

`apt-file`
- used for searching for files inside packages.

There is also a “friendlier” utility named simply `apt`, combining the most used options of `apt-get` and `apt-cache` in one utility. Many of the commands for `apt` are the same as the ones for `apt-get`, so they are in many cases interchangeable. However, since `apt` may not be installed on a system, it is recommended to learn how to use `apt-get` and `apt-cache`.

```
Note
apt and apt-get may require a network connection, because packages and package indexes may need to be downloaded from a remote server.
```

### Updating the Package Index
Before installing or upgrading software with APT, it is recommended to update the package index first in order to retrieve information about new and updated packages. This is done with the `apt-get` command, followed by the `update` parameter:

```
# apt-get update
Ign:1 http://dl.google.com/linux/chrome/deb stable InRelease
Hit:2 https://repo.skype.com/deb stable InRelease
Hit:3 http://us.archive.ubuntu.com/ubuntu disco InRelease
Hit:4 http://repository.spotify.com stable InRelease
Hit:5 http://dl.google.com/linux/chrome/deb stable Release
Hit:6 http://apt.pop-os.org/proprietary disco InRelease
Hit:7 http://ppa.launchpad.net/system76/pop/ubuntu disco InRelease
Hit:8 http://us.archive.ubuntu.com/ubuntu disco-security InRelease
Hit:9 http://us.archive.ubuntu.com/ubuntu disco-updates InRelease
Hit:10 http://us.archive.ubuntu.com/ubuntu disco-backports InRelease
Reading package lists... Done
```

```
Tip
Instead of apt-get update, you can also use apt update.
```

### Installing and Removing Packages
With the package index updated you may now install a package. This is done with `apt-get install`, followed by the name of the package you wish to install:

```
# apt-get install xournal
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  xournal
0 upgraded, 1 newly installed, 0 to remove and 75 not upgraded.
Need to get 285 kB of archives.
After this operation, 1041 kB of additional disk space will be used.
```

Similarly, to remove a package use `apt-get remove`, followed by the package name:

```
# apt-get remove xournal
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages will be REMOVED:
  xournal
0 upgraded, 0 newly installed, 1 to remove and 75 not upgraded.
After this operation, 1041 kB disk space will be freed.
Do you want to continue? [Y/n]
```

Be aware that when installing or removing packages, APT will do automatic dependency resolution. This means that any additional packages needed by the package you are installing will also be installed, and that packages that depend on the package you are removing will also be removed. APT will always show what will be installed or removed before asking if you want to continue:

```
# apt-get remove p7zip
Reading package lists... Done
Building dependency tree
The following packages will be REMOVED:
  android-libbacktrace android-libunwind android-libutils
  android-libziparchive android-sdk-platform-tools fastboot p7zip p7zip-full
0 upgraded, 0 newly installed, 8 to remove and 75 not upgraded.
After this operation, 6545 kB disk space will be freed.
Do you want to continue? [Y/n]
```

Note that when a package is removed the corresponding configuration files are left on the system. To remove the package and any configuration files, use the `purge` parameter instead of `remove` or the `remove` parameter with the `--purge` option:

```
# apt-get purge p7zip
```

or

```
# apt-get remove --purge p7zip
```

```
Tip
You can also use apt install and apt remove.
```


### Fixing Broken Dependencies
It is possible to have “broken dependencies” on a system. This means that one or more of the installed packages depend on other packages that have not been installed, or are not present anymore. This may happen due to an APT error, or because of a manually installed package.

To solve this, use the `apt-get install -f` command. This will try to “fix” the broken packages by installing the missing dependencies, ensuring that all packages are consistent again.

```
Tip
You can also use apt install -f.
```

### Upgrading Packages
APT can be used to automatically upgrade any installed packages to the latest versions available from the repositories. This is done with the `apt-get upgrade` command. Before running it, first update the package index with `apt-get update`:

```
# apt-get update
Hit:1 http://us.archive.ubuntu.com/ubuntu disco InRelease
Hit:2 http://us.archive.ubuntu.com/ubuntu disco-security InRelease
Hit:3 http://us.archive.ubuntu.com/ubuntu disco-updates InRelease
Hit:4 http://us.archive.ubuntu.com/ubuntu disco-backports InRelease
Reading package lists... Done

# apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  gnome-control-center
The following packages will be upgraded:
  cups cups-bsd cups-client cups-common cups-core-drivers cups-daemon
  cups-ipp-utils cups-ppdc cups-server-common firefox-locale-ar (...)

74 upgraded, 0 newly installed, 0 to remove and 1 not upgraded.
Need to get 243 MB of archives.
After this operation, 30.7 kB of additional disk space will be used.
Do you want to continue? [Y/n]
```

The summary at the bottom of the output shows how many packages will be upgraded, how many will be installed, removed or kept back, the total download size and how much extra disk space will be needed to complete the operation. To complete the upgrade, just answer `Y` and wait for `apt-get` to finish the task.

To upgrade a single package, just run `apt-get upgrade` followed by the package name. As in `dpkg`, `apt-get` will first check if a previous version of a package is installed. If so, the package will be upgraded to the newest version available in the repository. If not, a fresh copy will be installed.

```
Tip
You can also use apt upgrade and apt update.
```

### The Local Cache
When you install or update a package, the corresponding `.deb` file is downloaded to a local cache directory before the package is installed. By default, this directory is `/var/cache/apt/archives`. Partially downloaded files are copied to `/var/cache/apt/archives/partial/`.

As you install and upgrade packages, the cache directory can get quite large. To reclaim space, you can empty the cache by using the `apt-get clean` command. This will remove the contents of the `/var/cache/apt/archives` and `/var/cache/apt/archives/partial/` directories.

```
Tip
You can also use apt clean.
```

### Searching for Packages
The `apt-cache` utility can be used to perform operations on the package index, such as searching for a specific package or listing which packages contain a specific file.

To conduct a search, use `apt-cache search` followed by a search pattern. The output will be a list of every package that contains the pattern, either in its package name, description or files provided.

```
# apt-cache search p7zip
liblzma-dev - XZ-format compression library - development files
liblzma5 - XZ-format compression library
forensics-extra - Forensics Environment - extra console components (metapackage)
p7zip - 7zr file archiver with high compression ratio
p7zip-full - 7z and 7za file archivers with high compression ratio
p7zip-rar - non-free rar module for p7zip
```

In the example above, the entry `liblzma5 - XZ-format compression library` does not seem to match the pattern. However, if we show the full information, including description, for the package using the show parameter, we will find the pattern there:

```
# apt-cache show liblzma5
Package: liblzma5
Architecture: amd64
Version: 5.2.4-1
Multi-Arch: same
Priority: required
Section: libs
Source: xz-utils
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Jonathan Nieder <jrnieder@gmail.com>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 259
Depends: libc6 (>= 2.17)
Breaks: liblzma2 (<< 5.1.1alpha+20110809-3~)
Filename: pool/main/x/xz-utils/liblzma5_5.2.4-1_amd64.deb
Size: 92352
MD5sum: 223533a347dc76a8cc9445cfc6146ec3
SHA1: 8ed14092fb1caecfebc556fda0745e1e74ba5a67
SHA256: 01020b5a0515dbc9a7c00b464a65450f788b0258c3fbb733ecad0438f5124800
Homepage: https://tukaani.org/xz/
Description-en: XZ-format compression library
 XZ is the successor to the Lempel-Ziv/Markov-chain Algorithm
 compression format, which provides memory-hungry but powerful
 compression (often better than bzip2) and fast, easy decompression.
 .
 The native format of liblzma is XZ; it also supports raw (headerless)
 streams and the older LZMA format used by lzma. (For 7-Zip's related
 format, use the p7zip package instead.)
```

You can use regular expressions with the search pattern, allowing for very complex (and precise) searches. However, this topic is out of scope for this lesson.

```
Tip
You can also use apt search instead of apt-cache search and apt show instead of apt-cache show.
```

### The Sources List
APT uses a list of sources to know where to get packages from. This list is stored in the file `sources.list`, located inside the `/etc/apt` directory. This file can be edited directly with a text editor, like `vi`, `pico` or `nano`, or with graphical utilities like `aptitude` or `synaptic`.

A typical line inside `sources.list` looks like this:

```
deb http://us.archive.ubuntu.com/ubuntu/ disco main restricted universe multiverse
```

The syntax is archive type, URL, distribution and one or more components, where:

`Archive type`
- A repository may contain packages with ready-to-run software (binary packages, type `deb`) or with the source code to this software (source packages, type `deb-src`). The example above provides binary packages.

`URL`
- The URL for the repository.

`Distribution`
- The name (or codename) for the distribution for which packages are provided. One repository may host packages for multiple distributions. In the example above, `disco` is the codename for Ubuntu 19.04 Disco Dingo.

`Components`
- Each component represents a set of packages. These components may be different on different Linux distributions. For example, on Ubuntu and derivatives, they are:

- `main`
  - contains officially supported, open-source packages.

- `restricted`
  - contains officially supported, closed-source software, like device drivers for graphic cards, for example.

- `universe`
  - contains community maintained open-source software.

- `multiverse`
  - contains unsupported, closed-source or patent-encumbered software.

- On Debian, the main components are:

- `main`
  - consists of packages compliant with the Debian Free Software Guidelines (DFSG), which do not rely on software outside this area to operate. Packages included here are considered to be part of the Debian distribution.

- `contrib`
  - contains DFSG-compliant packages, but which depend on other packages that are not in `main`.

- `non-free`
  - contains packages that are not compliant with the DFSG.

- `security`
  - contains security updates.

- `backports`
  - contains more recent versions of packages that are in `main`. The development cycle of the stable versions of Debian is quite long (around two years), and this ensures that users can get the most up-to-date packages without having to modify the `main` core repository.

```
Note
You can learn more about the Debian Free Software Guidelines at: https://www.debian.org/social_contract#guidelines
```

To add a new repository to get packages from, you can simply add the corresponding line (usually provided by the repository maintainer) to the end of `sources.list`, save the file and reload the package index with `apt-get update`. After that, the packages in the new repository will be available for installation using `apt-get install`.

Keep in mind that lines starting with the `#` character are considered comments, and are ignored.

#### The /etc/apt/sources.list.d Directory
Inside the `/etc/apt/sources.list.d` directory you can add files with additional repositories to be used by APT, without the need to modify the main `/etc/apt/sources.list` file. These are simple text files, with the same syntax described above and the `.list` file extension.

Below you see the contents of a file called `/etc/apt/sources.list.d/buster-backports.list`:

```
deb http://deb.debian.org/debian buster-backports main contrib non-free
deb-src http://deb.debian.org/debian buster-backports main contrib non-free
```

#### Listing Package Contents and Finding Files
A utility called `apt-file` can be used to perform more operations in the package index, like listing the contents of a package or finding a package that contains a specific file. This utility may not be installed by default in your system. In this case, you can usually install it using `apt-get`:

```
# apt-get install apt-file
```

After installation, you will need to update the package cache used for `apt-file`:

```
# apt-file update
```

This usually takes only a few seconds. After that, you are ready to use `apt-file`.

To list the contents of a package, use the `list` parameter followed by the package name:

```
# apt-file list unrar
unrar: /usr/bin/unrar-nonfree
unrar: /usr/share/doc/unrar/changelog.Debian.gz
unrar: /usr/share/doc/unrar/copyright
unrar: /usr/share/man/man1/unrar-nonfree.1.gz
```

```
Tip
You can also use apt list instead of apt-file list.
```

You can search all packages for a file using the `search` parameter, followed by the file name. For example, if you wish to know which package provides a file called `libSDL2.so`, you can use:

```
# apt-file search libSDL2.so
libsdl2-dev: /usr/lib/x86_64-linux-gnu/libSDL2.so
```

The answer is the package `libsdl2-dev`, which provides the file `/usr/lib/x86_64-linux-gnu/libSDL2.so`.

The difference between `apt-file search` and `dpkg-query` is that `apt-file search` will also search uninstalled packages, while `dpkg-query` can only show files that belong to an installed package.

___

## Lesson 102-5 Use RPM and YUM package management

A long time ago, when Linux was still in its infancy, the most common way to distribute software was a compressed file (usually as a `.tar.gz` archive) with source code, which you would unpack and compile yourself.

However, as the amount and complexity of software grew, the need for a way to distribute pre-compiled software became clear. After all, not everyone had the resources, both in time and computing power, to compile large projects like the Linux kernel or an X Server.

Soon, efforts to standardize a way to distribute these software “packages” grew, and the first package managers were born. These tools made it much easier to install, configure or remove software from a system.

One of those was the RPM Package Manager and its corresponding tool (`rpm`), developed by Red Hat. Today, they are widely used not only on Red Hat Enterprise Linux (RHEL) itself, but also on its descendants, like Fedora, CentOS and Oracle Linux, other distributions like openSUSE and even other operating systems, like IBM’S AIX.

Other package management tools popular on Red Hat compatible distros are `yum` (YellowDog Updater Modified), `dnf` (Dandified YUM) and `zypper`, which can streamline many of the aspects of the installation, maintenance and removal of packages, making package management much easier.

In this lesson, we will learn how to use `rpm`, `yum`, `dnf` and `zypper` to obtain, install, manage and remove software on a Linux system.

```
Note
Despite using the same package format, there are internal differences between distributions so a package made specifically for openSUSE might not work on a RHEL system, and vice-versa. When searching for packages, always check for compatibility and try to find one tailored for your specific distribution, if possible.
```

### The RPM Package Manager (rpm)
The RPM Package Manager (`rpm`) is the essential tool for managing software packages on Red Hat-based (or derived) systems.

### Installing, Upgrading and Removing Packages
The most basic operation is to install a package, which can be done with:

```
# rpm -i PACKAGENAME
```

Where `PACKAGENAME` is the name of the `.rpm` package you wish to install.

If there is a previous version of a package on the system, you can upgrade to a newer version using the `-U` parameter:

```
# rpm -U PACKAGENAME
```

If there is no previous version of `PACKAGENAME` installed, then a fresh copy will be installed. To avoid this and only upgrade an installed package, use the `-F` option.

In both operations you can add the `-v` parameter to get a verbose output (more information is shown during the installation) and `-h` to get hash signs (`#`) printed as a visual aid to track installation progress. Multiple parameters can be combined into one, so `rpm -i -v -h` is the same as `rpm -ivh`.

To remove an installed package, pass the `-e` parameter (as in “erase”) to `rpm`, followed by the name of the package you wish to remove:

```
# rpm -e wget
```

If an installed package depends on the package being removed, you will get an error message:

```
# rpm -e unzip
error: Failed dependencies:
	/usr/bin/unzip is needed by (installed) file-roller-3.28.1-2.el7.x86_64
```

To complete the operation, first you will need to remove the packages that depend on the one you wish to remove (in the example above, `file-roller`). You can pass multiple package names to `rpm -e` to remove multiple packages at once.

### Dealing with Dependencies
More often than not, a package may depend on others to work as intended. For example, an image editor may need libraries to open JPG files, or a utility may need a widget toolkit like Qt or GTK for its user interface.

`rpm` will check if those dependencies are installed on your system, and will fail to install the package if they are not. In this case, `rpm` will list what is missing. However it cannot solve dependencies by itself.

In the example below, the user tried to install a package for the GIMP image editor, but some dependencies were missing:

```
# rpm -i gimp-2.8.22-1.el7.x86_64.rpm
error: Failed dependencies:
	babl(x86-64) >= 0.1.10 is needed by gimp-2:2.8.22-1.el7.x86_64
	gegl(x86-64) >= 0.2.0 is needed by gimp-2:2.8.22-1.el7.x86_64
	gimp-libs(x86-64) = 2:2.8.22-1.el7 is needed by gimp-2:2.8.22-1.el7.x86_64
	libbabl-0.1.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgegl-0.2.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimp-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpbase-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpcolor-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpconfig-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpmath-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpmodule-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpthumb-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpui-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpwidgets-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libmng.so.1()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libwmf-0.2.so.7()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libwmflite-0.2.so.7()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
```

It is up to the user to find the `.rpm` packages with the corresponding dependencies and install them. Package managers such as `yum`, `zypper` and `dnf` have tools that can tell which package provides a specific file. Those will be discussed later in this lesson.

### Listing Installed Packages
To get a list of all installed packages on your system, use the `rpm -qa` (think of “query all”).

```
# rpm -qa
selinux-policy-3.13.1-229.el7.noarch
pciutils-libs-3.5.1-3.el7.x86_64
redhat-menus-12.0.2-8.el7.noarch
grubby-8.28-25.el7.x86_64
hunspell-en-0.20121024-6.el7.noarch
dejavu-fonts-common-2.33-6.el7.noarch
xorg-x11-drv-dummy-0.3.7-1.el7.1.x86_64
libevdev-1.5.6-1.el7.x86_64
[...]
```

### Getting Package Information
To get information about an installed package, such as its version number, architecture, install date, packager, summary, etc., use `rpm` with the `-qi` (think of “query info”) parameters, followed by the package name. For example:

```
# rpm -qi unzip
Name        : unzip
Version     : 6.0
Release     : 19.el7
Architecture: x86_64
Install Date: Sun 25 Aug 2019 05:14:39 PM EDT
Group       : Applications/Archiving
Size        : 373986
License     : BSD
Signature   : RSA/SHA256, Wed 25 Apr 2018 07:50:02 AM EDT, Key ID 24c6a8a7f4a80eb5
Source RPM  : unzip-6.0-19.el7.src.rpm
Build Date  : Wed 11 Apr 2018 01:24:53 AM EDT
Build Host  : x86-01.bsys.centos.org
Relocations : (not relocatable)
Packager    : CentOS BuildSystem <http://bugs.centos.org>
Vendor      : CentOS
URL         : http://www.info-zip.org/UnZip.html
Summary     : A utility for unpacking zip files
Description :
The unzip utility is used to list, test, or extract files from a zip
archive. Zip archives are commonly found on MS-DOS systems. The zip
utility, included in the zip package, creates zip archives. Zip and
unzip are both compatible with archives created by PKWARE(R)'s PKZIP
for MS-DOS, but the programs' options and default behaviors do differ
in some respects.

Install the unzip package if you need to list, test or extract files from
a zip archive.
```

To get a list of what files are inside an installed package use the `-ql` parameters (think of “query list”) followed by the package name:

```
# rpm -ql unzip
/usr/bin/funzip
/usr/bin/unzip
/usr/bin/unzipsfx
/usr/bin/zipgrep
/usr/bin/zipinfo
/usr/share/doc/unzip-6.0
/usr/share/doc/unzip-6.0/BUGS
/usr/share/doc/unzip-6.0/LICENSE
/usr/share/doc/unzip-6.0/README
/usr/share/man/man1/funzip.1.gz
/usr/share/man/man1/unzip.1.gz
/usr/share/man/man1/unzipsfx.1.gz
/usr/share/man/man1/zipgrep.1.gz
/usr/share/man/man1/zipinfo.1.gz
```

If you wish to get information or a file listing from a package that has not been installed yet, just add the `-p` parameter to the commands above, followed by the name of the RPM file (`FILENAME`). So `rpm -qi PACKAGENAME` becomes `rpm -qip FILENAME`, and `rpm -ql PACKAGENAME` becomes `rpm -qlp FILENAME`, as shown below.

```
# rpm -qip atom.x86_64.rpm
Name        : atom
Version     : 1.40.0
Release     : 0.1
Architecture: x86_64
Install Date: (not installed)
Group       : Unspecified
Size        : 570783704
License     : MIT
Signature   : (none)
Source RPM  : atom-1.40.0-0.1.src.rpm
Build Date  : sex 09 ago 2019 12:36:31 -03
Build Host  : b01bbeaf3a88
Relocations : /usr
URL         : https://atom.io/
Summary     : A hackable text editor for the 21st Century.
Description :
A hackable text editor for the 21st Century.
```

```
# rpm -qlp atom.x86_64.rpm
/usr/bin/apm
/usr/bin/atom
/usr/share/applications/atom.desktop
/usr/share/atom
/usr/share/atom/LICENSE
/usr/share/atom/LICENSES.chromium.html
/usr/share/atom/atom
/usr/share/atom/atom.png
/usr/share/atom/blink_image_resources_200_percent.pak
/usr/share/atom/content_resources_200_percent.pak
/usr/share/atom/content_shell.pak

(listing goes on)
```

### Finding Out Which Package Owns a Specific File
To find out which installed package owns a file, use the `-qf` (think “query file”) followed by the full path to the file:

```
# rpm -qf /usr/bin/unzip
unzip-6.0-19.el7.x86_64
```

In the example above, the file `/usr/bin/unzip` belongs to the `unzip-6.0-19.el7.x86_64` package.

### YellowDog Updater Modified (YUM)
`yum` was originally developed as the Yellow Dog Updater (YUP), a tool for package management on the Yellow Dog Linux distribution. Over time, it evolved to manage packages on other RPM based systems, such as Fedora, CentOS, Red Hat Enterprise Linux and Oracle Linux.

Functionally, it is similar to the `apt` utility on Debian-based systems, being able to search for, install, update and remove packages and automatically handle dependencies. `yum` can be used to install a single package, or to upgrade a whole system at once.

### Searching for Packages
In order to install a package, you need to know its name. For this you can perform a search with `yum search PATTERN`, where `PATTERN` is the name of the package you are searching for. The result is a list of packages whose name or summary contain the search pattern specified. For example, if you need a utility to handle 7Zip compressed files (with the `.7z` extension) you can use:

```
# yum search 7zip
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
=========================== N/S matchyutr54ed: 7zip ============================
p7zip-plugins.x86_64 : Additional plugins for p7zip
p7zip.x86_64 : Very high compression ratio file archiver
p7zip-doc.noarch : Manual documentation and contrib directory
p7zip-gui.x86_64 : 7zG - 7-Zip GUI version

  Name and summary matches only, use "search all" for everything.
```

### Installing, Upgrading and Removing Packages
To install a package using `yum`, use the command `yum install PACKAGENAME`, where `PACKAGENAME` is the name of the package. `yum` will fetch the package and corresponding dependencies from an online repository, and install everything in your system.

```
# yum install p7zip
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
Resolving Dependencies
--> Running transaction check
---> Package p7zip.x86_64 0:16.02-10.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================
 Package        Arch            Version               Repository     Size
==========================================================================
Installing:
 p7zip          x86_64          16.02-10.el7          epel          604 k

Transaction Summary
==========================================================================
Install  1 Package

Total download size: 604 k
Installed size: 1.7 M
Is this ok [y/d/N]:
```

To upgrade an installed package, use `yum update PACKAGENAME`, where `PACKAGENAME` is the name of the package you want to upgrade. For example:

```
# yum update wget
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
Resolving Dependencies
--> Running transaction check
---> Package wget.x86_64 0:1.14-18.el7 will be updated
---> Package wget.x86_64 0:1.14-18.el7_6.1 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================
 Package     Arch          Version                   Repository      Size
==========================================================================
Updating:
 wget        x86_64        1.14-18.el7_6.1           updates        547 k

Transaction Summary
==========================================================================
Upgrade  1 Package

Total download size: 547 k
Is this ok [y/d/N]:
```
If you omit the name of a package, you can update every package on the system for which an update is available.

To check if an update is available for a specific package, use `yum check-update PACKAGENAME`. As before, if you omit the package name, `yum` will check for updates for every installed package on the system.

To remove an installed package, use `yum remove PACKAGENAME`, where `PACKAGENAME` is the name of the package you wish to remove.

### Finding Which Package Provides a Specific File
In a previous example we showed an attempt to install the `gimp` image editor, which failed because of unmet dependencies. However, `rpm` shows which files are missing, but does not list the name of the packages that provide them.

For example, one of the dependencies missing was `libgimpui-2.0.so.0`. To see what package provides it, you can use `yum whatprovides`, followed by the name of the file you are searching for:

```
# yum whatprovides libgimpui-2.0.so.0
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
2:gimp-libs-2.8.22-1.el7.i686 : GIMP libraries
Repo        : base
Matched from:
Provides    : libgimpui-2.0.so.0
```

The answer is `gimp-libs-2.8.22-1.el7.i686`. You can then install the package with the command `yum install gimp-libs`.

This also works for files already in your system. For example, if you wish to know where the file `/etc/hosts` came from, you can use:

```
# yum whatprovides /etc/hosts
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
setup-2.8.71-10.el7.noarch : A set of system configuration and setup files
Repo        : base
Matched from:
Filename    : /etc/hosts
```

The answer is `setup-2.8.71-10.el7.noarch`.

### Getting Information About a Package
To get information about a package, such as its version, architecture, description, size and more, use `yum info PACKAGENAME` where `PACKAGENAME` is the name of the package you want information for:

```
# yum info firefox
Last metadata expiration check: 0:24:16 ago on Sat 21 Sep 2019 02:39:43 PM -03.
Installed Packages
Name         : firefox
Version      : 69.0.1
Release      : 3.fc30
Architecture : x86_64
Size         : 268 M
Source       : firefox-69.0.1-3.fc30.src.rpm
Repository   : @System
From repo    : updates
Summary      : Mozilla Firefox Web browser
URL          : https://www.mozilla.org/firefox/
License      : MPLv1.1 or GPLv2+ or LGPLv2+
Description  : Mozilla Firefox is an open-source web browser, designed
             : for standards compliance, performance and portability.
```

### Managing Software Repositories
For `yum` the “repos” are listed in the directory `/etc/yum.repos.d/`. Each repository is represented by a `.repo` file, like `CentOS-Base.repo`.

Additional, extra repositories can be added by the user by adding a `.repo` file in the directory mentioned above, or at the end of `/etc/yum.conf`. However, the recommended way to add or manage repositories is with the `yum-config-manager tool`.

To add a repository, use the `--add-repo` parameter, followed by the URL to a `.repo` file.

```
# yum-config-manager --add-repo https://rpms.remirepo.net/enterprise/remi.repo
Loaded plugins: fastestmirror, langpacks
adding repo from: https://rpms.remirepo.net/enterprise/remi.repo
grabbing file https://rpms.remirepo.net/enterprise/remi.repo to /etc/yum.repos.d/remi.repo
repo saved to /etc/yum.repos.d/remi.repo
```

To get a list of all available repositories use `yum repolist all`. You will get an output similar to this:

```
# yum repolist all
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
repo id                       repo name                    status
updates/7/x86_64              CentOS-7 - Updates           enabled:  2,500
updates-source/7              CentOS-7 - Updates Sources   disabled
```

`disabled` repositories will be ignored when installing or upgrading software. To enable or disable a repository, use the `yum-config-manager` utility, followed by the repository id.

In the output above, the repository id is shown on the first column (`repo id`) of each line. Use only the part before the first `/` , so the id for the `CentOS-7 - Updates` repo is `updates`, and not `updates/7/x86_64`.

```
# yum-config-manager --disable updates
```

The command above will disable the `updates` repo. To re-enable it use:

```
# yum-config-manager --enable updates
```

```
Note
Yum stores downloaded packages and associated metadata in a cache directory (usually /var/cache/yum). As the system gets upgraded and new packages are installed, this cache can get quite large. To clean the cache and reclaim disk space you can use the yum clean command, followed by what to clean. The most useful parameters are packages (yum clean packages) to delete downloaded packages and metadata (yum clean metadata) to delete associated metadata. See the manual page for yum (type man yum) for more information.
```

### DNF
`dnf` is the package management tool used on Fedora, and is a fork of `yum`. As such, many of the commands and parameters are similar. This section will give you just a quick overview of `dnf`.

Searching for packages
- `dnf search PATTERN`, where `PATTERN` is what you are searching for. For example, `dnf search unzip` will show all packages that contain the word `unzip` in the name or description.

Getting information about a package
- `dnf info PACKAGENAME`

Installing packages
- `dnf install PACKAGENAME`, where `PACKAGENAME` is the name of the package you wish to install. You can find the name by performing a search.

Removing packages
- `dnf remove PACKAGENAME`

Upgrading packages
- `dnf upgrade PACKAGENAME` to update only one package. Omit the package name to upgrade all the packages in the system.

Finding out which package provides a specific file
- `dnf provides FILENAME`

Getting a list of all the packages installed in the system
- `dnf list --installed`

Listing the contents of a package
- `dnf repoquery -l PACKAGENAME`

```
Note
dnf has a built-in help system, which shows more information (such as extra parameters) for each command. To use it, type dnf help followed by the command, like dnf help install.
```

### Managing Software Repositories
Just as with `yum` and `zypper`, `dnf` works with software repositories (repos). Each distribution has a list of default repositories, and administrators can add or remove repos as needed.

To get a list of all available repositories, use `dnf repolist`. To list only enabled repositories, add the `--enabled` option, and to list only disabled repositories, add the `--disabled` option.

```
# dnf repolist
Last metadata expiration check: 0:20:09 ago on Sat 21 Sep 2019 02:39:43 PM -03.
repo id                    repo name                                      status
*fedora                    Fedora 30 - x86_64                             56,582
*fedora-modular            Fedora Modular 30 - x86_64                        135
*updates                   Fedora 30 - x86_64 - Updates                   12,774
*updates-modular           Fedora Modular 30 - x86_64 - Updates              145
```

To add a repository, use `dnf config-manager --add_repo URL`, where `URL` is the full URL to the repository. To enable a repository, use `dnf config-manager --set-enabled REPO_ID`.

Likewise, to disable a repository use `dnf config-manager --set-disabled REPO_ID`. In both cases `REPO_ID` is the unique ID for the repository, which you can get using `dnf repolist`. Added repositories are enabled by default.

Repositories are stored in `.repo` files in the directory `/etc/yum.repos.d/`, with exactly the same syntax used for `yum`.

### Zypper
`zypper` is the package management tool used on SUSE Linux and OpenSUSE. Feature-wise it is similar to `apt` and `yum`, being able to install, update and remove packages from a system, with automated dependency resolution.

### Updating the Package Index
Like other package management tools, `zypper` works with repositories containing packages and metadata. This metadata needs to be refreshed from time to time, so that the utility will know about the latest packages available. To do a refresh, simply type:

```
# zypper refresh
Repository 'Non-OSS Repository' is up to date.
Repository 'Main Repository' is up to date.
Repository 'Main Update Repository' is up to date.
Repository 'Update Repository (Non-Oss)' is up to date.
All repositories have been refreshed.
```

`zypper` has an auto-refresh feature that can be enabled on a per-repository basis, meaning that some repos may be refreshed automatically before a query or package installation, and others may need to be refreshed manually. You will learn how to control this feature shortly.

### Searching for Packages
To search for a package, use the `search` (or `se`) operator, followed by the package name:

```
# zypper se gnumeric
Loading repository data...
Reading installed packages...

S | Name           | Summary                           | Type
--+----------------+-----------------------------------+--------
  | gnumeric       | Spreadsheet Application           | package
  | gnumeric-devel | Spreadsheet Application           | package
  | gnumeric-doc   | Documentation files for Gnumeric  | package
  | gnumeric-lang  | Translations for package gnumeric | package
```

The search operator can also be used to obtain a list of all the installed packages in the system. To do so, use the `-i` parameter without a package name, as in `zypper se -i`.

To see if a specific package is installed, add the package name to the command above. For example, the following command will search among the installed packages for any containing “firefox” in the name:

```
# zypper se -i firefox
Loading repository data...
Reading installed packages...

S | Name                               | Summary                 | Type
--+------------------------------------+-------------------------+--------
i | MozillaFirefox                     | Mozilla Firefox Web B-> | package
i | MozillaFirefox-branding-openSUSE   | openSUSE branding of -> | package
i | MozillaFirefox-translations-common | Common translations f-> | package
```

To search only among non-installed packages, add the `-u` parameter to the `se` operator.

### Installing, Upgrading and Removing Packages
To install a software package, use the `install` (or `in`) operator, followed by the package name. Like so:

```
# zypper in unrar
zypper in unrar
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following NEW package is going to be installed:
  unrar

1 new package to install.
Overall download size: 141.2 KiB. Already cached: 0 B. After the operation, additional 301.6 KiB will be used.
Continue? [y/n/v/...? shows all options] (y): y
Retrieving package unrar-5.7.5-lp151.1.1.x86_64
                                     (1/1), 141.2 KiB (301.6 KiB unpacked)
Retrieving: unrar-5.7.5-lp151.1.1.x86_64.rpm .......................[done]
Checking for file conflicts: .......................................[done]
(1/1) Installing: unrar-5.7.5-lp151.1.1.x86_64 .....................[done]
```

`zypper` can also be used to install an RPM package on disk, while trying to satisfy its dependencies using packages from the repositories. To do so, just provide the full path to the package instead of a package name, like `zypper in /home/john/newpackage.rpm`.

To update packages installed on the system, use `zypper update`. As in the installation process, this will show a list of packages to be installed/upgraded before asking if you want to proceed.

If you wish to only list the available updates, without installing anything, you can use `zypper list-updates`.

To remove a package, use the `remove` (or `rm`) operator, followed by the package name:

```
# zypper rm unrar
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following package is going to be REMOVED:
  unrar

1 package to remove.
After the operation, 301.6 KiB will be freed.
Continue? [y/n/v/...? shows all options] (y): y
(1/1) Removing unrar-5.7.5-lp151.1.1.x86_64 ........................[done]
Keep in mind that removing a package also removes any other packages that depend on it. For example:

# zypper rm libgimp-2_0-0
Loading repository data...
Warning: No repositories defined. Operating only with the installed resolvables. Nothing can be installed.
Reading installed packages...
Resolving package dependencies...

The following 6 packages are going to be REMOVED:
  gimp gimp-help gimp-lang gimp-plugins-python libgimp-2_0-0
  libgimpui-2_0-0

6 packages to remove.
After the operation, 98.0 MiB will be freed.
Continue? [y/n/v/...? shows all options] (y):
```

### Finding Which Packages Contain a Specific File
To see which packages contain a specific file, use the search operator followed by the `--provides` parameter and the name of the file (or full path to it). For example, if you want to know which packages contain the file `libgimpmodule-2.0.so.0` in `/usr/lib64/` you would use:

```
# zypper se --provides /usr/lib64/libgimpmodule-2.0.so.0
Loading repository data...
Reading installed packages...

S | Name          | Summary                                      | Type
--+---------------+----------------------------------------------+--------
i | libgimp-2_0-0 | The GNU Image Manipulation Program - Libra-> | package
```

### Getting Package Information
To see the metadata associated with a package, use the `info` operator followed by the package name. This will provide you with the origin repository, package name, version, architecture, vendor, installed size, if it is installed or not, the status (if it is up-to-date), the source package and a description.

```
# zypper info gimp
Loading repository data...
Reading installed packages...

Information for package gimp:
 -----------------------------
Repository     : Main Repository
Name           : gimp
Version        : 2.8.22-lp151.4.6
Arch           : x86_64
Vendor         : openSUSE
Installed Size : 29.1 MiB
Installed      : Yes (automatically)
Status         : up-to-date
Source package : gimp-2.8.22-lp151.4.6.src
Summary        : The GNU Image Manipulation Program
Description    :
    The GIMP is an image composition and editing program, which can be
    used for creating logos and other graphics for Web pages. The GIMP
    offers many tools and filters, and provides a large image
    manipulation toolbox, including channel operations and layers,
    effects, subpixel imaging and antialiasing, and conversions, together
    with multilevel undo. The GIMP offers a scripting facility, but many
    of the included scripts rely on fonts that we cannot distribute.
```

### Managing Software Repositories
`zypper` can also be used to manage software repositories. To see a list of all the repositories currently registered in your system, use `zypper repos`:

```
# zypper repos
Repository priorities are without effect. All enabled repositories share the same priority.

#  | Alias                     | Name                               | Enabled | GPG Check | Refresh
---+---------------------------+------------------------------------+---------+-----------+--------
 1 | openSUSE-Leap-15.1-1      | openSUSE-Leap-15.1-1               | No      | ----      | ----
 2 | repo-debug                | Debug Repository                   | No      | ----      | ----
 3 | repo-debug-non-oss        | Debug Repository (Non-OSS)         | No      | ----      | ----
 4 | repo-debug-update         | Update Repository (Debug)          | No      | ----      | ----
 5 | repo-debug-update-non-oss | Update Repository (Debug, Non-OSS) | No      | ----      | ----
 6 | repo-non-oss              | Non-OSS Repository                 | Yes     | (r ) Yes  | Yes
 7 | repo-oss                  | Main Repository                    | Yes     | (r ) Yes  | Yes
 8 | repo-source               | Source Repository                  | No      | ----      | ----
 9 | repo-source-non-oss       | Source Repository (Non-OSS)        | No      | ----      | ----
10 | repo-update               | Main Update Repository             | Yes     | (r ) Yes  | Yes
11 | repo-update-non-oss       | Update Repository (Non-Oss)        | Yes     | (r ) Yes  | Yes
```

See in the `Enabled` column that some repositories are enabled, while others are not. You can change this with the `modifyrepo` operator, followed by the `-e` (enable) or `-d` (disable) parameter and the repository alias (the second column in the output above).

```
# zypper modifyrepo -d repo-non-oss
Repository 'repo-non-oss' has been successfully disabled.

# zypper modifyrepo -e repo-non-oss
Repository 'repo-non-oss' has been successfully enabled.
```

Previously we mentioned that `zypper` has an auto refresh capability that can be enabled on a per-repository basis. When enabled, this flag will make `zypper` run a refresh operation (the same as running `zypper refresh`) before working with the specified repo. This can be controlled with the `-f` and `-F` parameters of the `modifyrepo` operator:

```
# zypper modifyrepo -F repo-non-oss
Autorefresh has been disabled for repository 'repo-non-oss'.

# zypper modifyrepo -f repo-non-oss
Autorefresh has been enabled for repository 'repo-non-oss'.
```

#### Adding and Removing Repositories
To add a new software repository for `zypper`, use the `addrepo` operator followed by the repository URL and repository name, like below:

```
# zypper addrepo http://packman.inode.at/suse/openSUSE_Leap_15.1/ packman
Adding repository 'packman' ........................................[done]
Repository 'packman' successfully added

URI         : http://packman.inode.at/suse/openSUSE_Leap_15.1/
Enabled     : Yes
GPG Check   : Yes
Autorefresh : No
Priority    : 99 (default priority)

Repository priorities are without effect. All enabled repositories share the same priority.
```

While adding a repository, you can enable auto-updates with the `-f` parameter. Added repositories are enabled by default, but you can add and disable a repository at the same time by using the `-d` parameter.

To remove a repository, use the `removerepo` operator, followed by the repository name (Alias). To remove the repository added in the example above, the command would be:

```
# zypper removerepo packman
Removing repository 'packman' ......................................[done]
Repository 'packman' has been removed.
```
___

## Lesson 102-6 Linux as a virtualization guest

One of the great strengths of Linux is its versatility. One aspect of this versatility is the ability to use Linux as a means of hosting other operating systems, or individual applications, in a completely isolated and secure environment. This lesson will focus on the concepts of virtualization and container technologies, along with some technical details that should be taken into consideration when deploying a virtual machine on a cloud platform.

`Virtualization Overview`
Virtualization is a technology that allows for a software platform, called a hypervisor, to run processes that contain a fully emulated computer system. The hypervisor is responsible for managing the physical hardware’s resources that can be used by individual virtual machines. These virtual machines are called guests of the hypervisor. A virtual machine has many aspects of a physical computer emulated in software, such as a system’s BIOS and hard drive disk controllers. A virtual machine will often use hard disk images that are stored as individual files, and will have access to the host machine’s RAM and CPU through the hypervisor software. The hypervisor separates the access to the host system’s hardware resources among the guests, thus allowing for multiple operating systems to run on a single host system.

Commonly used hypervisors for Linux include:

Xen
- Xen is an open source Type-1 hypervisor, meaning that it does not rely on an underlying operating system to function. A hypervisor of this sort is known as a bare-metal hypervisor since the computer can boot directly into the hypervisor.

KVM
- The Kernel Virtual Machine is a Linux kernel module for virtualization. KVM is a hypervisor of both Type-1 and Type-2 hypervisor because, although it needs a generic Linux operating system to work, it is able to perform as a hypervisor perfectly well by integrating with a running Linux installation. Virtual machines deployed with KVM use the `libvirt` daemon and associated software utilities to be created and managed.

VirtualBox
- A popular desktop application that makes it easy to create and manage virtual machines. Oracle VM VirtualBox is cross-platform, and will work on Linux, macOS, and Microsoft Windows. Since VirtualBox requires an underlying operating system to run, it is a Type-2 hypervisor.

Some hypervisors allow for the dynamic relocation of a virtual machine. The process of moving a virtual machine from one hypervisor installation to another is called a migration, and the techniques involved differ between hypervisor implementations. Some migrations can only be performed when the guest system has been completely shut down, and other can be performed while the guest is running (called a live migration). Such techniques can be of aid during maintenance windows for hypervisors, or for system resiliency when a hypervisor becomes non-functional and the guest can be moved to a hypervisor that is working.

### Types of Virtual Machines
There are three main types of virtual machines, the fully virtualized guest, the paravirtualized guest and the hybrid guest.

Fully Virtualized
- All instructions that a guest operating system is expected to execute must be able to run within a fully virtualized operating system installation. The reason for this is that no additional software drivers are installed within the guest to translate the instructions to either simulated or real hardware. A fully virtualized guest is one where the guest (or HardwareVM) is unaware that it is a running virtual machine instance. In order for this type of virtualization to take place on x86 based hardware the Intel VT-x or AMD-V CPU extensions must be enabled on the system that has the hypervisor installed. This can be done from a BIOS or UEFI firmware configuration menu.

Paravirtualized
- A paravirtualized guest (or PVM) is one where the guest operating system is aware that it is a running virtual machine instance. These types of guests will make use of a modified kernel and special drivers (known as guest drivers) that will help the guest operating system utilize software and hardware resources of the hypervisor. The performance of a paravirtualized guest is often better than that of the fully virtualized guest due to the advantage that these software drivers provide.

Hybrid
- Paravirtualization and full virtualization can be combined to allow unmodified operating systems to receive near native I/O performance by using paravirtualized drivers on fully virtualized operating systems. The paravirtualized drivers contain storage and network device drivers with enhanced disk and network I/O performance.

Virtualization platforms often provide packaged guest drivers for virtualized operating systems. The KVM utilizes drivers from the Virtio project, whereas Oracle VM VirtualBox uses Guest Extensions available from a downloadable ISO CD-ROM image file.

### Example `libvirt` Virtual Machine
We will look at an example virtual machine that is managed by `libvirt` and uses the KVM hypervisor. A virtual machine often consists of a group of files, primarily an XML file that defines the virtual machine (such as its hardware configuration, network connectivity, display capabilities, and more) and an associated hard disk image file that contains the installation of the operating system and its software.

First, let us start to examine an example XML configuration file for a virtual machine and its network environment:

```
$ ls /etc/libvirt/qemu
total 24
drwxr-xr-x 3 root root 4096 Oct 29 17:48 networks
-rw------- 1 root root 5667 Jun 29 17:17 rhel8.0.xml
```

```
Note
The qemu portion of the directory path refers to the underlying software that KVM-based virtual machines rely on. The QEMU project provides software for the hypervisor to emulate hardware devices that the virtual machine will use, such as disk controllers, access to the host CPU, network card emulation, and more.
```

Note that there is a directory named `networks`. This directory contains definition files (also using XML) that create network configurations that the virtual machines can use. This hypervisor is only using one network, and so there is only one definition file that contains a configuration for a virtual network segment that these systems will utilize.

```
$ ls -l /etc/libvirt/qemu/networks/
total 8
drwxr-xr-x 2 root root 4096 Jun 29 17:15 autostart
-rw------- 1 root root  576 Jun 28 16:39 default.xml
$ sudo cat /etc/libvirt/qemu/networks/default.xml
<!--
WARNING: THIS IS AN AUTO-GENERATED FILE. CHANGES TO IT ARE LIKELY TO BE
OVERWRITTEN AND LOST. Changes to this xml configuration should be made using:
  virsh net-edit default
or other application using the libvirt API.
-->

<network>
  <name>default</name>
  <uuid>55ab064f-62f8-49d3-8d25-8ef36a524344</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='52:54:00:b8:e0:15'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>
```

This definition includes a Class C private network and an emulated hardware device to act as a router for this network. There is also a range of IP addresses for the hypervisor to use with a DHCP server implementation that can be assigned to the virtual machines that use this network. This network configuration also utilizes network address translation (NAT) to forward packets to other networks, such as the hypervisor’s LAN.

Now we turn our attention to a Red Hat Enterprise Linux 8 virtual machine definition file. (sections of special note are in bold text):

```
$ sudo cat /etc/libvirt/qemu/rhel8.0.xml
<!--
WARNING: THIS IS AN AUTO-GENERATED FILE. CHANGES TO IT ARE LIKELY TO BE
OVERWRITTEN AND LOST. Changes to this xml configuration should be made using:
  virsh edit rhel8.0
or other application using the libvirt API.
-->

<domain type='kvm'>
  <name>rhel8.0</name>
  <uuid>fadd8c5d-c5e1-410e-b425-30da7598d0f6</uuid>
  <metadata>
    <libosinfo:libosinfo xmlns:libosinfo="http://libosinfo.org/xmlns/libvirt/domain/1.0">
      <libosinfo:os id="http://redhat.com/rhel/8.0"/>
    </libosinfo:libosinfo>
  </metadata>
  <memory unit='KiB'>4194304</memory>
  <currentMemory unit='KiB'>4194304</currentMemory>
  <vcpu placement='static'>2</vcpu>
  <os>
    <type arch='x86_64' machine='pc-q35-3.1'>hvm</type>
    <boot dev='hd'/>
  </os>
  <features>
    <acpi/>
    <apic/>
    <vmport state='off'/>
  </features>
  <cpu mode='host-model' check='partial'>
    <model fallback='allow'/>
  </cpu>
  <clock offset='utc'>
    <timer name='rtc' tickpolicy='catchup'/>
    <timer name='pit' tickpolicy='delay'/>
    <timer name='hpet' present='no'/>
  </clock>
  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>destroy</on_crash>
  <pm>
    <suspend-to-mem enabled='no'/>
    <suspend-to-disk enabled='no'/>
  </pm>
  <devices>
    <emulator>/usr/bin/qemu-system-x86_64</emulator>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/rhel8'/>
      <target dev='vda' bus='virtio'/>
      <address type='pci' domain='0x0000' bus='0x04' slot='0x00' function='0x0'/>
    </disk>
    <controller type='usb' index='0' model='qemu-xhci' ports='15'>
      <address type='pci' domain='0x0000' bus='0x02' slot='0x00' function='0x0'/>
    </controller>
    <controller type='sata' index='0'>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x1f' function='0x2'/>
    </controller>
    <controller type='pci' index='0' model='pcie-root'/>
    <controller type='pci' index='1' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='1' port='0x10'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x0' multifunction='on'/>
    </controller>
    <controller type='pci' index='2' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='2' port='0x11'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x1'/>
    </controller>
    <controller type='pci' index='3' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='3' port='0x12'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x2'/>
    </controller>
    <controller type='pci' index='4' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='4' port='0x13'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x3'/>
    </controller>
    <controller type='pci' index='5' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='5' port='0x14'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x4'/>
    </controller>
    <controller type='pci' index='6' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='6' port='0x15'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x5'/>
    </controller>
    <controller type='pci' index='7' model='pcie-root-port'>
      <model name='pcie-root-port'/>
      <target chassis='7' port='0x16'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x6'/>
    </controller>
    <controller type='virtio-serial' index='0'>
      <address type='pci' domain='0x0000' bus='0x03' slot='0x00' function='0x0'/>
    </controller>
    <interface type='network'>
      <mac address='52:54:00:50:a7:18'/>
      <source network='default'/>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x01' slot='0x00' function='0x0'/>
    </interface>
    <serial type='pty'>
      <target type='isa-serial' port='0'>
        <model name='isa-serial'/>
      </target>
    </serial>
    <console type='pty'>
      <target type='serial' port='0'/>
    </console>
    <channel type='unix'>
      <target type='virtio' name='org.qemu.guest_agent.0'/>
      <address type='virtio-serial' controller='0' bus='0' port='1'/>
    </channel>
    <channel type='spicevmc'>
      <target type='virtio' name='com.redhat.spice.0'/>
      <address type='virtio-serial' controller='0' bus='0' port='2'/>
    </channel>
    <input type='tablet' bus='usb'>
      <address type='usb' bus='0' port='1'/>
    </input>
    <input type='mouse' bus='ps2'/>
    <input type='keyboard' bus='ps2'/>
    <graphics type='spice' autoport='yes'>
      <listen type='address'/>
      <image compression='off'/>
    </graphics>
    <sound model='ich9'>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x1b' function='0x0'/>
    </sound>
    <video>
      <model type='virtio' heads='1' primary='yes'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x0'/>
    </video>
    <redirdev bus='usb' type='spicevmc'>
      <address type='usb' bus='0' port='2'/>
    </redirdev>
    <redirdev bus='usb' type='spicevmc'>
      <address type='usb' bus='0' port='3'/>
    </redirdev>
    <memballoon model='virtio'>
      <address type='pci' domain='0x0000' bus='0x05' slot='0x00' function='0x0'/>
    </memballoon>
    <rng model='virtio'>
      <backend model='random'>/dev/urandom</backend>
      <address type='pci' domain='0x0000' bus='0x06' slot='0x00' function='0x0'/>
    </rng>
  </devices>
</domain>
```

This file defines a number of hardware settings that are used by this guest, such as the amount of RAM that it will have assigned to it, the number of CPU cores from the hypervisor that the guest will have access to, the hard disk image file that is associated with this guest (under the `disk` stanza), its display capabilities (via the SPICE protocol), and the guest’s access to USB devices as well as emulated keyboard and mice input.

### Example Virtual Machine Disk Storage
This virtual machine’s hard disk image resides at `/var/lib/libvirt/images/rhel8`. Here is the disk image itself on this hypervisor:

```
$ sudo ls -lh /var/lib/libvirt/images/rhel8
-rw------- 1 root root 5.5G Oct 25 15:57 /var/lib/libvirt/images/rhel8
```

The current size of this disk image consumes only 5.5 GB of space on the hypervisor. However, the operating system within this guest sees a 23.3 GB sized disk, as evidenced by the output of the following command from within the running virtual machine:

```
$ lsblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
vda           252:0    0 23.3G  0 disk
├─vda1        252:1    0    1G  0 part /boot
└─vda2        252:2    0 22.3G  0 part
  ├─rhel-root 253:0    0   20G  0 lvm  /
  └─rhel-swap 253:1    0  2.3G  0 lvm  [SWAP]
```

This is due to the type of disk provisioning used for this guest. There are multiple types of disk images that a virtual machine can use but the two primary types are:

COW
- Copy-on-write (also referred to as thin-provisioning or sparse images) is a method where a disk file is created with a pre-defined upper size limit. The disk image size only increases as new data is written to the disk. Just like the previous example, the guest operating system sees the predefined disk limit of 23.3 GB, but has only written 5.5 GB of data to the disk file. The disk image format used for the example virtual machine is qcow2 which is a QEMU COW file format.

RAW
- A raw or full disk type is a file that has all of its space pre-allocated. For example, a 10 GB raw disk image file consumes 10 GB of actual disk space on the hypervisor. There is a performance benefit to this style of disk as all of the needed disk space already exists, so the underlying hypervisor can just write data to the disk without the performance hit of monitoring the disk image to ensure that it has not yet reached its limit and extending the size of the file as new data is written to it.

There are other virtualization management platforms such as Red Hat Enterprise Virtualization and oVirt that can make use of physical disks to act as backing storage locations for a virtual machine’s operating system. These systems can utilize storage area network (SAN) or network attached storage (NAS) devices to write their data to, and the hypervisor keeps track of what storage locations belong to which virtual machines. These storage systems can use technologies such as logical volume management (LVM) to grow or shrink the size of a virtual machine’s disk storage as needed, and to aid in the creation and management of storage snapshots.

### Working with Virtual Machine Templates
Since virtual machines are typically just files running on a hypervisor, it is easy to create templates that can be customized for particular deployment scenarios. Often a virtual machine will have a basic operating system installation and some pre-configured authentication configuration settings set up to ease future system launches. This cuts down on the amount of time it takes to build a new system by reducing the amount of work that is often repeated, such as base package installation and locale settings.

This virtual machine template could then later get copied to a new guest system. In this case, the new guest would get renamed, a new MAC address generated for its network interface, and other modifications can be made depending on its intended use.

### The D-Bus Machine ID
Many Linux installations will utilize a machine identification number generated at install time, called the D-Bus machine ID. However, if a virtual machine is cloned to be used as a template for other virtual machine installations, a new D-Bus machine ID would need to be created to ensure that system resources from the hypervisor get directed to the appropriate guest system.

The following command can be used to validate that a D-Bus machine ID exists for the running system:

```
$ dbus-uuidgen --ensure
```

If no error messages are displayed, then an ID exists for the system. To view the current D-Bus machine ID, run the following:

```
$ dbus-uuidgen --get
17f2e0698e844e31b12ccd3f9aa4d94a
```

The string of text that is displayed is the current ID number. No two Linux systems running on a hypervisor should have the same D-Bus machine ID.

The D-Bus machine ID is located at `/var/lib/dbus/machine-id` and is symbolically linked to `/etc/machine-id`. Changing this ID number on a running system is discouraged as system instability and crashes are likely to occur. If two virtual machines do have the same D-Bus machine ID, follow the procedure below to generate a new one:

```
$ sudo rm -f /etc/machine-id
$ sudo dbus-uuidgen --ensure=/etc/machine-id
```

In the event that `/var/lib/dbus/machine-id` is not a symbolic link back to `/etc/machine-id`, then `/var/lib/dbus/machine-id` will need to be removed.

### Deploying Virtual Machines to the Cloud
There are a multitude of IaaS (infrastructure as a service) providers available that run hypervisor systems and that can deploy virtual guest images for an organization. Practically all of these providers have tools in place that allows an administrator to build, deploy and configure custom virtual machines based on a variety of Linux distributions. Many of these companies also have systems in place that allow for the deployment and migrations of virtual machines built from within a customer’s organization.

When assessing a deployment of a Linux system in an IaaS environment, there are some key elements that an administrator should be cognizant of:

`Computing Instances`
- Many cloud providers will charge usage rates based on “computing instances”, or how much CPU time your cloud-based infrastructure will use. Careful planning of how much processing time applications will actually require will aid in keeping the costs of a cloud solution manageable.

- Computing instances will also often refer to the number of virtual machines that are provisioned in a cloud environment. Again, the more instances of systems that are running at one time will also factor into how much overall CPU time an organization will be charged for.

`Block Storage`
- Cloud providers also have various levels of block storage available for an organization to use. Some offerings are simply meant to be web-based network storage for files, and other offerings relate to external storage for a cloud provisioned virtual machine to use for hosting files.

- The cost for such offerings will vary based on the amount of storage used, and the speed of the storage within the provider’s data centers. Faster storage access typically will cost more, and conversely data “at rest” (as in archival storage) is often very inexpensive.

`Networking`
- One of the main components of working with a cloud solutions provider is how the virtual network will be configured. Many IaaS providers will have some form of web-based utilities that can be utilized for the design and implementation of different network routes, subnetting, and firewall configurations. Some will even provide DNS solutions so that publicly accessible FQDN (fully qualified domain names) can be assigned to your internet facing systems. There are even “hybrid” solutions available that can connect an existing, on-premise network infrastructure to a cloud-based infrastructure through the means of a VPN (virtual private network), thus tying the two infrastructures together.

### Securely Accessing Guests in the Cloud
The most prevalent method in use for accessing a remote virtual guest on a cloud platform is through the use of OpenSSH software. A Linux system that resides in the cloud would have the OpenSSH server running, while an administrator would use an OpenSSH client with pre-shared keys for remote access.

An administrator would run the following command

```
$ ssh-keygen
```

and follow the prompts to create a public and private SSH key pair. The private key remains on the administrator’s local system (stored in `~/.ssh/`) and the public key gets copied to the remote cloud system, the exact same method one would use when working with networked machines on a corporate LAN.

The administrator would then run the following command:

```
$ ssh-copy-id -i <public_key> user@cloud_server
```

This will copy the public SSH key from the key pair just generated to the remote cloud server. The public key will be recorded in the `~/.ssh/ authorized_keys` file of the cloud server, and set the appropriate permissions on the file.

```
Note
If there is only one public key file in the ~/.ssh/ directory, then the -i switch can be omitted, as the ssh-copy-id command will default to the public key file in the directory (typically the file ending with the .pub extension).
```

Some cloud providers will automatically generate a key pair when a new Linux system is provisioned. The administrator will then need to download the private key for the new system from the cloud provider and store it on their local system. Note that the permissions for SSH keys must be 0600 for a private key, and 0644 for a public key.

### Preconfiguring Cloud Systems
A useful tool that simplifies the deployments of cloud-based virtual machine is the `cloud-init` utility. This command, along with the associated configuration files and pre-defined virtual machine image, is a vendor-neutral method for deploying a Linux guest across a plethora of IaaS providers. Utilizing YAML (YAML Ain’t Markup Language) plain-text files an administrator can pre-configure network settings, software package selections, SSH key configuration, user account creations, locale settings, along with a myriad of other options to quickly build out new systems.

During a new system’s initial boot up, `cloud-init` will read in the settings from YAML configurations files and apply them. This process only needs to apply to a system’s initial setup, and makes deploying a fleet of new systems on a cloud provider’s platform easy.

The YAML file syntax used with `cloud-init` is called cloud-config. Here is a sample `cloud-config` file:

```
#cloud-config
timezone: Africa/Dar_es_Salaam
hostname: test-system

# Update the system when it first boots up
apt_update: true
apt_upgrade: true

# Install the Nginx web server
packages:
 - nginx
```

Note that on the top line there is no space between the hash symbol (#) and the term cloud-config.

```
Note
cloud-init is not just for virtual machines. The cloud-init tool suite can also be used to pre-configure containers (such as LXD Linux containers) prior to deployment.
```

### Containers
Container technology is similar in some aspects to a virtual machine, where you get an isolated environment to easily deploy an application. Whereas with a virtual machine an entire computer is emulated, a container uses just enough software to run an application. In this way, there is far less overhead.

Containers allow for greater flexibility over that of a virtual machine. An application container can be migrated from one host to another, just as a virtual machine can be migrated from one hypervisor to another. However, sometimes a virtual machine will need to be powered off before it could be migrated, whereas with a container the application is always running while it is getting migrated. Containers also make it easy to deploy new versions of applications in tandem with an existing version. As users close their sessions with running containers, these containers can get automatically removed from the system by the container orchestration software and replaced with the new version thus reducing downtime.

```
Note
There are numerous container technologies available for Linux, such as Docker, Kubernetes, LXD/LXC, systemd-nspawn, OpenShift and more. The exact implementation of a container software package is beyond the scope of the LPIC-1 exam.
```

Containers make use of the control groups (better known as cgroups) mechanism within the Linux kernel. The cgroup is a way to partition system resources such as memory, processor time as well as disk and network bandwidth for an individual application. An administrator can use cgroups directly to set system resource limits on an application, or a group of applications that could exist within a single cgroup. In essence this is what container software does for the administrator, along with providing tools that ease the management and deployment of cgroups.

```
Note
Currently, knowledge of cgroups are not necessary for passing the LPIC-1 exam. The concept of the cgroup is mentioned here so that the candidate would at least have some background knowledge of how an application is segregated for the sake of system resource utilization.
```


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)