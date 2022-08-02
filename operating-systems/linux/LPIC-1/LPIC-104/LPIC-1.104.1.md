# LPIC-1.104: Devices, Linux Filesystems, Filesystem Hierarchy Standard

## Lesson 104.1: Create partitions and filesystems
On any operating system, a disk needs to be partitioned before it can be used. A partition is a logical subset of the physical disk, and information about partitions are stored in a partition table. This table includes information about the first and last sectors of the partition and its type, and further details on each partition.

Usually each partition is seen by an operating system as a separate “disk”, even if they all reside in the same physical media. On Windows systems they are assigned letters like `C:` (historically the main disk), `D:` and so on. On Linux each partition is assigned to a directory under `/dev`, like `/dev/sda1` or `/dev/sda2`.

In this lesson, you will learn how to create, delete, restore and resize partitions using the three most common utilities (`fdisk`, `gdisk` and `parted`), how to create a filesystem on them and how to create and set up a swap partition or swap file to be used as virtual memory.

```
Note
For historical reasons, through this lesson we will refer to storage media as “disks”, even though modern storage systems, like SSDs and Flash Storage, do not contain any “disks” at all.
```

### Understanding MBR and GPT
There are two main ways of storing partition information on hard disks. The first one is MBR (Master Boot Record), and the second one is GPT (GUID Partition Table).

`MBR`
- This is a remnant from the early days of MS-DOS (more specifically, PC-DOS 2.0 from 1983) and for decades was the standard partitioning scheme on PCs. The partition table is stored on the first sector of a disk, called the Boot Sector, along with a boot loader, which on Linux systems is usually the GRUB bootloader. But MBR has a series of limitations that hinder its use on modern systems, like the inability to address disks of more than 2 TB in size, and the limit of only 4 primary partitions per disk.

`GUID`
- A partitioning system that addresses many of the limitations of MBR. There is no practical limit on disk size, and the maximum number of partitions are limited only by the operating system itself. It is more commonly found on more modern machines that use UEFI instead of the old PC BIOS.

During system administration tasks it is highly possible that you will find both schemes in use, so it is important to know how to use the tools associated with each one to create, delete or modify partitions.

### Managing MBR Partitions with FDISK
The standard utility for managing MBR partitions on Linux is `fdisk`. This is an interactive, menu-driven utility. To use it, type `fdisk` followed by the device name corresponding to the disk you want to edit. For example, the command

```
# fdisk /dev/sda
```

would edit the partition table of the first SATA-connected device (`sda`) on the system. Keep in mind that you need to specify the device corresponding to the physical disk, not one of its partitions (like `/dev/sda1`).

```
Note
All disk-related operations in this lesson need to be done as the user root (the system administrator), or with root privileges using sudo.
```

When invoked, `fdisk` will show a greeting, then a warning and it will wait for your commands.

```
# fdisk /dev/sda
Welcome to fdisk (util-linux 2.33.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help):
```

The warning is important. You can create, edit or delete partitions at will, but nothing will be written to disk unless you use the write (`w`) command. So you can “practice” without risk of losing data, as long as you stay clear of the `w` key. To exit `fdisk` without saving changes, use the `q` command.

```
Note
That being said, you should never practice on an important disk, as there are always risks. Use a spare external disk, or a USB flash drive instead.
```

#### Printing the Current Partition Table
The command `p` is used to print the current partition table. The output is something like this:

```
Command (m for help): p
Disk /dev/sda: 111.8 GiB, 120034123776 bytes, 234441648 sectors
Disk model: CT120BX500SSD1
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x97f8fef5

Device     Boot     Start       End   Sectors   Size Id Type
/dev/sda1            4096 226048942 226044847 107.8G 83 Linux
/dev/sda2       226048944 234437550   8388607     4G 82 Linux swap / Solaris
```

Here is the meaning of each column:

`Device`
- The device assigned to the partition.

`Boot`
- Shows whether the partition is “bootable” or not.

`Start`
- The sector where the partition starts.

`End`
- The sector where the partition ends.

`Sectors`
- The total number of sectors in the partition. Multiply it by the sector size to get the partition size in bytes.

`Size`
- The size of the partition in “human readable” format. In the example above, values are in gigabytes.

`Id`
- The numerical value representing the partition type.

`Type`
- The description for the partition type.

#### Primary vs Extended Partitions
On an MBR disk, you can have 2 main types of partitions, primary and extended. Like we said before, you can have only 4 primary partitions on the disk, and if you want to make the disk “bootable”, the first partition must be a primary one.

One way to work around this limitation is to create an extended partition that acts as a container for logical partitions. You could have, for example, a primary partition, an extended partition occupying the remainder of the disk space and five logical partitions inside it.

For an operating system like Linux, primary and extended partitions are treated exactly in the same way, so there are no “advantages” of using one over the other.

#### Creating a Partition
To create a partition, use the `n` command. By default, partitions will be created at the start of unallocated space on the disk. You will be asked for the partition type (primary or extended), first sector and last sector.

For the first sector, you can usually accept the default value suggested by `fdisk`, unless you need a partition to start at a specific sector. Instead of specifying the last sector, you can specify a size followed by the letters `K`, `M`, `G`, `T` or `P` (Kilo, Mega, Giga, Tera or Peta). So, if you want to create a 1 GB partition, you could specify `+1G` as the `Last sector`, and `fdisk` will size the partition accordingly. See this example for the creation of a primary partition:

```
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-3903577, default 2048): 2048
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-3903577, default 3903577): +1G
```

#### Checking for Unallocated Space
If you do not know how much free space there is on the disk, you can use the `F` command to show the unallocated space, like so:

```
Command (m for help): F
Unpartitioned space /dev/sdd: 881 MiB, 923841536 bytes, 1804378 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes

  Start     End Sectors  Size
2099200 3903577 1804378  881M
```

#### Deleting Partitions
To delete a partition, use the `d` command. `fdisk` will ask you for the number of the partition you want to delete, unless there is only one partition on the disk. In this case, this partition will be selected and deleted immediately.

Be aware that if you delete an extended partition, all the logical partitions inside it will also be deleted.

#### Mind the Gap!
Keep in mind that when creating a new partition with `fdisk`, the maximum size will be limited to the maximum amount of contiguous unallocated space on the disk. Say, for example, that you have the following partition map:

```
Device     Boot   Start     End Sectors  Size Id Type
/dev/sdd1          2048 1050623 1048576  512M 83 Linux
/dev/sdd2       1050624 2099199 1048576  512M 83 Linux
/dev/sdd3       2099200 3147775 1048576  512M 83 Linux
```

Then you delete partition 2 and check for free space:

```
Command (m for help): F
Unpartitioned space /dev/sdd: 881 MiB, 923841536 bytes, 1804378 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes

  Start     End Sectors  Size
1050624 2099199 1048576  512M
3147776 3903577  755802  369M
```

Adding up the size of the unallocated space, in theory we have 881 MB available. But see what happens when we try to create a 700 MB partition:

```
Command (m for help): n
Partition type
   p   primary (2 primary, 0 extended, 2 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2,4, default 2): 2
First sector (1050624-3903577, default 1050624):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1050624-2099199, default 2099199): +700M
Value out of range.
```

That happens because the largest contiguous unallocated space on the disk is the 512 MB block that belonged to partition 2. Your new partition cannot “reach over” partition 3 to use some of the unallocated space after it.

### Changing the Partition Type
Occasionally, you may need to change the partition type, especially when dealing with disks that will be used on other operating systems and platforms. This is done with the command `t`, followed by the number of the partition you wish to change.

The partition type must be specified by its corresponding hexadecimal code, and you can see a list of all the valid codes by using the command `l`.

Do not confuse the partition type with the filesystem used on it. Although at first there was a relation between them, today you cannot assume this to be true. A Linux partition, for example, can contain any Linux-native filesystem, like ext4 or ReiserFS.

```
Tip
Linux partitions are type 83 (Linux). Swap partitions are type 82 (Linux Swap).
```

### Managing GUID Partitions with GDISK
The utility `gdisk` is the equivalent of `fdisk` when dealing with GPT partitioned disks. In fact, the interface is modeled after `fdisk`, with an interactive prompt and the same (or very similar) commands.

#### Printing the Current Partition Table
The command `p` is used to print the current partition table. The output is something like this:

```
Command (? for help): p
Disk /dev/sdb: 3903578 sectors, 1.9 GiB
Model: DataTraveler 2.0
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): AB41B5AA-A217-4D1E-8200-E062C54285BE
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 3903544
Partitions will be aligned on 2048-sector boundaries
Total free space is 1282071 sectors (626.0 MiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         2099199   1024.0 MiB  8300  Linux filesystem
   2         2623488         3147775   256.0 MiB   8300  Linux filesystem
```

Right from the start, we notice a few different things:

- Each disk has a unique Disk Identifier (GUID). This is a 128 bit hexadecimal number, assigned randomly when the partition table is created. Since there are 3.4 × 1038 possible values to this number, the chances that 2 random disks have the same GUID are pretty slim. The GUID can be used to identify which filesystems to mount at boot time (and where), eliminating the need to use the device path to do so (like /dev/sdb).

- See the phrase Partition table holds up to 128 entries? That’s right, you can have up to 128 partitions on a GPT disk. Because of this, there is no need for primary and extended partitions.

- The free space is listed on the last line, so there is no need for an equivalent of the F command from fdisk.

#### Creating a Partition
The command to create a partition is `n`, just as in `fdisk`. The main difference is that besides the partition number and the first and last sector (or size), you can also specify the partition type during the creation. GPT partitions support many more types than MBR. You can check a list of all the supported types by using the `l` command.

### Deleting a Partition
To delete a partition, type `d` and the partition number. Unlike `fdisk`, the first partition will not be automatically selected if it is the only one on the disk.

On GPT disks, partitions can be easily reordered, or “sorted”, to avoid gaps in the numbering sequence. To do this, simply use the `s` command. For example, imagine a disk with the following partition table:

```
Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         2099199   1024.0 MiB  8300  Linux filesystem
   2         2099200         2361343   128.0 MiB   8300  Linux filesystem
   3         2361344         2623487   128.0 MiB   8300  Linux filesystem
```

If you delete the second partition, the table would become:

```
Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         2099199   1024.0 MiB  8300  Linux filesystem
   3         2361344         2623487   128.0 MiB   8300  Linux filesystem
```

If you use the `s` command, it would become:

```
Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         2099199   1024.0 MiB  8300  Linux filesystem
   2         2361344         2623487   128.0 MiB   8300  Linux filesystem
```

Notice that the third partition became the second one.

#### Gap? What Gap?
Unlike MBR disks, when creating a partition on GPT disks the size is not limited by the maximum amount of contiguous unallocated space. You can use every last bit of a free sector, no matter where it is located on the disk.

#### Recovery Options
GPT disks store backup copies of the GPT header and partition table, making it easy to recover disks in case this data has been damaged. `gdisk` provides features to aid in those recovery tasks, accessed with the `r` command.

You can rebuild a corrupt main GPT header or partition table with `b` and `c`, respectively, or use the main header and table to rebuild a backup with `d` and `e`. You can also convert a MBR to a GPT with `f`, and do the opposite with g, among other operations. Type `?` in the recovery menu to get a list of all the available recovery commands and descriptions about what they do.

### Creating File Systems
Partitioning the disk is only the first step towards being able to use a disk. After that, you will need to format the partition with a filesystem before using it to store data.

A filesystem controls how the data is stored and accessed on the disk. Linux supports many filesystems, some native, like the ext (Extended Filesystem) family, while others come from other operating systems like FAT from MS-DOS, NTFS from Windows NT, HFS and HFS+ from Mac OS, etc.

The standard tool used to create a filesystem on Linux is `mkfs`, which comes in many “flavors” according to the filesystem it needs to work with.

### Creating an ext2/ext3/ext4 Filesystem
The Extended Filesystem (ext) was the first filesystem for Linux, and through the years was replaced by new versions called ext2, ext3 and ext4, which is currently the default filesystem for many Linux distributions.

The utilities `mkfs.ext2`, `mkfs.ext3` and `mkfs.ext4` are used to create ext2, ext3 and ext4 filesystems. In fact, all of these “utilities” exist only as symbolic links to another utility called `mke2fs`. `mke2fs` alters its defaults according to the name it is called by. As such, they all have the same behavior and command line parameters.

The most simple form of usage is:

```
# mkfs.ext2 TARGET
```

Where `TARGET` is the name of the partition where the filesystem should be created. For example, to create an ext3 filesystem on `/dev/sdb1` the command would be:

```
# mkfs.ext3 /dev/sdb1
```

Instead of using the command corresponding to the filesystem you wish to create, you can pass the `-t` parameter to `mke2fs` followed by the filesystem name. For example, the following commands are equivalent, and will create an ext4 filesystem on `/dev/sdb1`.

```
# mkfs.ext4 /dev/sdb1
# mke2fs -t ext4 /dev/sdb1
```

#### Command Line Parameters
`mke2fs` supports a wide range of command line parameters and options. Here are some of the most significant ones. All of them also apply to `mkfs.ext2`, `mkfs.ext3` and `mkfs.ext4`:

`-b SIZE`
- Sets the size of the data blocks in the device to `SIZE`, which can be 1024, 2048 or 4096 bytes per block.

`-c`
- Checks the target device for bad blocks before creating the filesystem. You can run a thorough, but much slower check by passing this parameter twice, as in `mkfs.ext4 -c -c TARGET`.

`-d DIRECTORY`
- Copies the contents of the specified directory to the root of the new filesystem. Useful if you need to “pre-populate” the disk with a predefined set of files.

`-F`
- Danger, Will Robinson! This option will force mke2fs to create a filesystem, even if the other options passed to it or the target are dangerous or make no sense at all. If specified twice (as in `-F` `-F`) it can even be used to create a filesystem on a device which is mounted or in use, which is a very, very bad thing to do.

`-L VOLUME_LABEL`
- Will set the volume label to the one specified in `VOLUME_LABEL`. This label must be at most 16 characters long.

`-n`
This is a truly useful option that simulates the creation of the filesystem, and displays what would be done if executed without the `n` option. Think of it as a “trial” mode. Good to check things out before actually committing any changes to disk.

`-q`
Quiet mode. `mke2fs` will run normally, but will not produce any output to the terminal. Useful when running `mke2fs` from a script.

`-U ID`
- This will set the UUID (Universally Unique Identifier) of a partition to the value specified as ID. UUIDs are 128 bit numbers in hexadecimal notation that serve to uniquely identify a partition to the operating system. This number is specified as a 32-digit string in the format 8-4-4-4-12, meaning 8 digits, hyphen, 4 digits, hyphen, 4 digits, hyphen, 4 digits, hyphen, 12 digits, like `D249E380-7719-45A1-813C-35186883987E`. Instead of an ID you can also specify parameters like `clear` to clear the filesystem UUID, `random`, to use a randomly generated UUID, or `time` to create a time-based UUID.

`-V`
- Verbose mode, prints much more information during operation than usual. Useful for debugging purposes.

### Creating an XFS Filesystem
XFS is a high-performance filesystem originally developed by Silicon Graphics in 1993 for its IRIX operating system. Due to its performance and reliability features, it is commonly used for servers and other environments that require high (or guaranteed) filesystem bandwidth.

Tools for managing XFS filesystems are part of the `xfsprog`s package. This package may need to be installed manually, as it is not included by default in some Linux distributions. Others, like Red Hat Enterprise Linux 7, use XFS as the default filesystem.

XFS filesystems are divided into at least 2 parts, a log section where a log of all filesystem operations (commonly called a Journal) are maintained, and the data section. The log section may be located inside the data section (the default behavior), or even on a separate disk altogether, for better performance and reliability.

The most basic command to create an XFS filesystem is `mkfs.xfs TARGET`, where `TARGET` is the partition you want the filesystem to be created in. For example: `mkfs.xfs /dev/sda1`.

As in `mke2fs`, `mkfs.xfs` supports a number of command line options. Here are some of the most common ones.

`-b size=VALUE`
- Sets the block size on the filesystem, in bytes, to the one specified in `VALUE`. The default value is 4096 bytes (4 KiB), the minimum is 512, and the maximum is 65536 (64 KiB).

`-m crc=VALUE`
- Parameters starting with `-m` are metadata options. This one enables (if `VALUE` is `1`) or disables (if `VALUE` is `0`) the use of CRC32c checks to verify the integrity of all metadata on the disk. This enables better error detection and recovery from crashes related to hardware issues, so it is enabled by default. The performance impact of this check should be minimal, so normally there is no reason to disable it.

`-m uuid=VALUE`
- Sets the partition UUID to the one specified as VALUE. Remember that UUIDs are 32-character (128 bits) numbers in hexadecimal base, specified in groups of 8, 4, 4, 4 and 12 digits separated by dashes, like `1E83E3A3-3AE9-4AAC-BF7E-29DFFECD36C0`.

`-f`
- Force the creation of a filesystem on the target device even if a filesystem is detected on it.

`-l logdev=DEVICE`
- This will put the log section of the filesystem on the specified device, instead of inside the data section.

`-l size=VALUE`
- This will set the size of the log section to the one specified in `VALUE`. The size can be specified in bytes, and suffixes like `m` or `g` can be used. `-l size=10m`, for example, will limit the log section to 10 Megabytes.

`-q`
- Quiet mode. In this mode, `mkfs.xfs` will not print the parameters of the file system being created.

`-L LABEL`
- Sets the filesystem label, which can be at most 12 characters long.

`-N`
- Similar to the `-n` parameter of `mke2fs`, will make `mkfs.xfs` print all the parameters for the creation of the file system, without actually creating it.

### Creating a FAT or VFAT Filesystem
The FAT filesystem originated from MS-DOS, and through the years has received many revisions culminating on the FAT32 format released in 1996 with Windows 95 OSR2.

VFAT is an extension of the FAT16 format with support for long (up to 255 characters) file names. Both filesystems are handled by the same utility, `mkfs.fat`. `mkfs.vfat` is an alias to it.

The FAT filesystem has important drawbacks which restrict its use on large disks. FAT16, for example, supports volumes of at most 4 GB and a maximum file size of 2 GB. FAT32 ups the volume size to up to 2 PB, and the maximum file size to 4 GB. Because of this, FAT filesystems are today more commonly used on small flash drives or memory cards (up to 2 GB in size), or legacy devices and OSs that do not support more advanced filesystems.

The most basic command for the creation of a FAT filesystem is `mkfs.fat TARGET`, where `TARGET` is the partition you want the filesystem to be created in. For example: `mkfs.fat /dev/sdc1`.

Like other utilities, `mkfs.fat` supports a number of command line options. Below are the most important ones. A full list and description of every option can be read in the manual for the utility, with the command `man mkfs.fat`.

`-c`
- Checks the target device for bad blocks before creating the filesystem.

`-C FILENAME BLOCK_COUNT`
- Will create the file specified in `FILENAME` and then create a FAT filesystem inside it, effectively creating an empty “disk image”, that can be later written to a device using a utility such as `dd` or mounted as a loopback device. When using this option, the number of blocks in the filesystem (`BLOCK_COUNT`) must be specified after the device name.

`-F SIZE`
- Selects the size of the FAT (File Allocation Table), between 12, 16 or 32, i.e., between FAT12, FAT16 or FAT32. If not specified, `mkfs.fat` will select the appropriate option based on the filesystem size.

`-n NAME`
- Sets the volume label, or name, for the filesystem. This can be up to 11 characters long, and the default is no name.

`-v`
- Verbose mode. Prints much more information than usual, useful for debugging.

```
Note
mkfs.fat cannot create a “bootable” filesystem. According to the manual page, “this isn’t as easy as you might think” and will not be implemented.
```

### Creating an exFAT Filesystem
exFAT is a filesystem created by Microsoft in 2006 that addresses one of the most important limitations of FAT32: file and disk size. On exFAT, the maximum file size is 16 exabytes (from 4 GB on FAT32), and the maximum disk size is 128 petabytes.

As it is well supported by all three major operating systems (Windows, Linux and mac OS), it is a good choice where interoperability is needed, like on large capacity flash drives, memory cards and external disks. In fact, it is the default filesystem, as defined by the SD Association, for SDXC memory cards larger than 32 GB.

The default utility for creating exFAT filesystems is `mkfs.exfat`, which is a link to `mkexfatfs`. The most basic command is `mkfs.exfat TARGET`, where `TARGET` is the partition you want the filesystem to be created in. For example: `mkfs.exfat /dev/sdb2`.

Contrary to the other utilities discussed in this lesson, `mkfs.exfat` has very few command line options. They are:

`-i VOL_ID`
- Sets the Volume ID to the value specified in `VOL_ID`. This is a 32-Bit hexadecimal number. If not defined, an ID based on the current time is set.

`-n NAME`
- Sets the volume label, or name. This can have up to 15 characters, and the default is no name.

`-p SECTOR`
- Specifies the first sector of the first partition on the disk. This is an optional value, and the default is zero.

`-s SECTORS`
- Defines the number of physical sectors per cluster of allocation. This must be a power of two, like 1, 2, 4, 8, and so on.

### Getting to Know the Btrfs Filesystem
Btrfs (officially the B-Tree Filesystem, pronounced as “Butter FS”, “Better FS” or even “Butterfuss”, your pick) is a filesystem that has been in development since 2007 specifically for Linux by the Oracle Corporation and other companies, including Fujitsu, Red Hat, Intel and SUSE, among others.

There are many features that make Btrfs attractive on modern systems where massive amounts of storage are common. Among these are multiple device support (including striping, mirroring and striping+mirroring, as in a RAID setup), transparent compression, SSD optimizations, incremental backups, snapshots, online defragmentation, offline checks, support for subvolumes (with quotas), deduplication and much more.

As it is a copy-on-write filesystem it is very resilient to crashes. And on top of that, Btrfs is simple to use, and well supported by many Linux distributions. And some of them, like SUSE, use it as the default filesystem.

```
Note
On a traditional filesystem, when you want to overwrite part of a file the new data is put directly over the old data that it is replacing. On a copy-on-write filesystem the new data is written to free space on disk, then the file’s original metadata is updated to refer to the new data and only then the old data is freed up, as it is no longer needed. This reduces the chances of data loss in case of a crash, as the old data is only discarded after the filesystem is absolutely sure that it is no longer needed and the new data is in place.
```

#### Creating a Btrfs Filesystem
The utility `mkfs.btrfs` is used to create a Btrfs filesystem. Using the command without any options creates a Btrfs filesystem on a given device, like so:

```
# mkfs.btrfs /dev/sdb1
```

```
Tip
If you do not have the mkfs.btrfs utility on your system, look for btrfs-progs in your distribution’s package manager.
```

You can use the `-L` to set a label (or name) for your filesystem. Btrfs labels can have up to 256 characters, except for newlines:

```
# mkfs.btrfs /dev/sdb1 -L "New Disk"
```

```
Tip
Enclose the label in quotes (like above) if it contains spaces.
```

Note this peculiar thing about Btrfs: you can pass multiple devices to the `mkfs.btrfs` command. Passing more than one device will span the filesystem over all the devices which is similar to a RAID or LVM setup. To specify how metadata will be distributed in the disk array, use the `-m` parameter. Valid parameters are `raid0`, `raid1`, `raid5`, `raid6`, `raid10`, single and dup.

For example, to create a filesystem spanning `/dev/sdb1` and `/dev/sdc1`, concatenating the two partitions into one big partition, use:

```
# mkfs.btrfs -d single -m single /dev/sdb /dev/sdc
```

```
Warning
Filesystems spanning multiple partitions such as above might look advantageous at first but are not a good idea from a data safety standpoint, as a failure on a single disk of the array means certain data loss. The risk gets bigger the more disks you use, as you also have more possible points of failure.
```

#### Managing Subvolumes
Subvolumes are like filesystems inside filesystems. Think of them as a directory which can be mounted as (and treated like) a separate filesystem. Subvolumes make organization and system administration easier, as each one of them can have separate quotas or snapshot rules.

```
Note
Subvolumes are not partitions. A partition allocates a fixed space on a drive. This can lead to problems further down the line, like one partition running out of space when another one has plenty of space left. Not so with subvolumes, as they “share” the free space from their root filesystem, and grow as needed.
```

Suppose you have a Btrfs filesystem mounted on `/mnt/disk`, and you wish to create a subvolume inside it to store your backups. Let’s call it `BKP`:

```
# btrfs subvolume create /mnt/disk/BKP
```

Next we list the contents of the `/mnt/disk` filesystem. You will see that we have a new directory, named after our subvolume.

```
$ ls -lh /mnt/disk/
total 0
drwxr-xr-x 1 root   root     0 jul 13 17:35 BKP
drwxrwxr-x 1 carol carol 988 jul 13 17:30 Images
```

```
Note
Yes, subvolumes can also be accessed like any other directory.
```

We can check that the subvolume is active, with the command:

```
# btrfs subvolume show /mnt/disk/BKP/
	Name: 			BKP
	UUID: 			e90a1afe-69fa-da4f-9764-3384f66fa32e
	Parent UUID: 		-
	Received UUID: 		-
	Creation time: 		2019-07-13 17:35:40 -0300
	Subvolume ID: 		260
	Generation: 		23
	Gen at creation: 	22
	Parent ID: 		5
	Top level ID: 		5
	Flags: 			-
	Snapshot(s):
```

You can mount the subvolume on  `/mnt/BKP` by passing the `-t btrfs -o subvol=NAME` parameter to the `mount` command:

```
# mount -t btrfs -o subvol=BKP /dev/sdb1 /mnt/bkp
```

```
Note
The -t parameter specifies the filesystem type to be mounted.
```

#### Working with Snapshots
Snapshots are just like subvolumes, but pre-populated with the contents from the volume from which the snapshot was taken.

When created, a snapshot and the original volume have exactly the same content. But from that point in time, they will diverge. Changes made to the original volume (like files added, renamed or deleted) will not be reflected on the snapshot, and vice-versa.

Keep in mind that a snapshot does not duplicate the files, and initially takes almost no disk space. It simply duplicates the filesystem tree, while pointing to the original data.

The command to create a snapshot is the same used to create a subvolume, just add the `snapshot` parameter after `btrfs subvolume`. The command below will create a snapshot of the Btrfs filesysten mounted in `/mnt/disk` in `/mnt/disk/snap`:

```
# btrfs subvolume snapshot /mnt/disk /mnt/disk/snap
```

Now, imagine you have the following contents in `/mnt/disk`:

```
$ ls -lh
total 2,8M
-rw-rw-r-- 1 carol carol 109K jul 10 16:22 Galaxy_Note_10.png
-rw-rw-r-- 1 carol carol 484K jul  5 15:01 geminoid2.jpg
-rw-rw-r-- 1 carol carol 429K jul  5 14:52 geminoid.jpg
-rw-rw-r-- 1 carol carol 467K jul  2 11:48 LG-G8S-ThinQ-Mirror-White.jpg
-rw-rw-r-- 1 carol carol 654K jul  2 11:39 LG-G8S-ThinQ-Range.jpg
-rw-rw-r-- 1 carol carol  94K jul  2 15:43 Mimoji_Comparativo.jpg
-rw-rw-r-- 1 carol carol 112K jul 10 16:20 Note10Plus.jpg
drwx------ 1 carol carol  366 jul 13 17:56 snap
-rw-rw-r-- 1 carol carol 118K jul 11 16:36 Twitter_Down_20190711.jpg
-rw-rw-r-- 1 carol carol 324K jul  2 15:22 Xiaomi_Mimoji.png
```

Notice the snap directory, containing the snapshot. Now let us remove some files, and check the directory contents:

```
$ rm LG-G8S-ThinQ-*
$ ls -lh
total 1,7M
-rw-rw-r-- 1 carol carol 109K jul 10 16:22 Galaxy_Note_10.png
-rw-rw-r-- 1 carol carol 484K jul  5 15:01 geminoid2.jpg
-rw-rw-r-- 1 carol carol 429K jul  5 14:52 geminoid.jpg
-rw-rw-r-- 1 carol carol  94K jul  2 15:43 Mimoji_Comparativo.jpg
-rw-rw-r-- 1 carol carol 112K jul 10 16:20 Note10Plus.jpg
drwx------ 1 carol carol  366 jul 13 17:56 snap
-rw-rw-r-- 1 carol carol 118K jul 11 16:36 Twitter_Down_20190711.jpg
-rw-rw-r-- 1 carol carol 324K jul  2 15:22 Xiaomi_Mimoji.png
```

However, if you check inside the snap directory, the files you deleted are still there and can be restored if needed.

```
$ ls -lh snap/
total 2,8M
-rw-rw-r-- 1 carol carol 109K jul 10 16:22 Galaxy_Note_10.png
-rw-rw-r-- 1 carol carol 484K jul  5 15:01 geminoid2.jpg
-rw-rw-r-- 1 carol carol 429K jul  5 14:52 geminoid.jpg
-rw-rw-r-- 1 carol carol 467K jul  2 11:48 LG-G8S-ThinQ-Mirror-White.jpg
-rw-rw-r-- 1 carol carol 654K jul  2 11:39 LG-G8S-ThinQ-Range.jpg
-rw-rw-r-- 1 carol carol  94K jul  2 15:43 Mimoji_Comparativo.jpg
-rw-rw-r-- 1 carol carol 112K jul 10 16:20 Note10Plus.jpg
-rw-rw-r-- 1 carol carol 118K jul 11 16:36 Twitter_Down_20190711.jpg
-rw-rw-r-- 1 carol carol 324K jul  2 15:22 Xiaomi_Mimoji.png
```

It is also possible to create read-only snapshots. They work exactly like writable snapshots, with the difference that the contents of the snapshot cannot be changed, they are “frozen” in time. Just add the -r parameter when creating the snapshot:

```
# btrfs subvolume snapshot -r /mnt/disk /mnt/disk/snap
```

#### A Few Words on Compression
Btrfs supports transparent file compression, with three different algorithms available to the user. This is done automatically on a per-file basis, as long as the filesystem is mounted with the `-o compress` option. The algorithms are smart enough to detect incompressible files and will not try to compress them, saving system resources. So on a single directory you may have compressed and uncompressed files together. The default compression algorithm is ZLIB, but LZO (faster, worse compression ratio) or ZSTD (faster than ZLIB, comparable compression) are available, with multiple compression levels (see the cooresponding objective on mount options).

### Managing Partitions with GNU Parted
GNU Parted is a very powerful partition editor (hence the name) that can be used to create, delete, move, resize, rescue and copy partitions. It can work with both GPT and MBR disks, and cover almost all of your disk management needs.

There are many graphical front-ends that make working with `parted` much easier, like GParted for GNOME-based desktop environments and the KDE Partition Manager for KDE Desktops. However, you should learn how to use parted on the command line, since in a server setting you can never count on a graphical desktop environment being available.

```
Warning
Unlike fdisk and gdisk, parted makes changes to the disk immediately after the command is issued, without waiting for another command to write the changes to disk. When practicing, it is wise to do so on an empty or spare disk or flash drive, so there is no risk of data loss should you make a mistake.
```

The simplest way to start using parted is by typing `parted DEVICE`, where `DEVICE` is the device you want to manage (`parted /dev/sdb`). The program starts an interactive command line interface like `fdisk` and `gdisk` with a (`parted`) prompt for you to enter commands.

```
# parted /dev/sdb
GNU Parted 3.2
Using /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.

(parted)
```

```
Warning
Be careful! If you do not specify a device, parted will automatically select the primary disk (usually /dev/sda) to work with.
```

### Selecting Disks
To switch to a different disk than the one specified on the command line, you can use the `select` command, followed by the device name:

```
(parted) select /dev/sdb
Using /dev/sdb
```

### Getting Information
The `print` command can be used to get more information about a specific partition or even all of the block devices (disks) connected to your system.

To get information about the currently selected partition, just type `print`:

```
(parted) print
Model: ATA CT120BX500SSD1 (scsi)
Disk /dev/sda: 120GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End    Size    Type     File system     Flags
 1      2097kB  116GB  116GB   primary  ext4
 2      116GB   120GB  4295MB  primary  linux-swap(v1)
```

You can get a list of all block devices connected to your system using `print devices`:

```
(parted) print devices
/dev/sdb (1999MB)
/dev/sda (120GB)
/dev/sdc (320GB)
/dev/mapper/cryptswap (4294MB)
```

To get information about all connected devices at once, you can use `print all`. If you wish to know how much free space there is in each one of them, you can use `print free`:

```
(parted) print free
Model: ATA CT120BX500SSD1 (scsi)
Disk /dev/sda: 120GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system     Flags
        32.3kB  2097kB  2065kB           Free Space
 1      2097kB  116GB   116GB   primary  ext4
        116GB   116GB   512B             Free Space
 2      116GB   120GB   4295MB  primary  linux-swap(v1)
        120GB   120GB   2098kB           Free Space
```

### Creating a Partition Table on an Empty Disk
To create a partition table on an empty disk, use the `mklabel` command, followed by the partition table type that you want to use.

There are many supported partition table types, but the main types you should know of are msdos which is used here to refer to an MBR partition table, and `gpt` to refer to a GPT partition table. To create an MBR partition table, type:

```
(parted) mklabel msdos
```

And to create a GPT partition table, the command is:

```
(parted) mklabel gpt
```

### Creating a Partition
To create a partition the command `mkpart` is used, using the syntax `mkpart PARTTYPE FSTYPE START END`, where:

`PARTTYPE`
- Is the partition type, which can be `primary`, `logical` or `extended` in case an MBR partition table is used.

`FSTYPE`
- Specifies which filesystem will be used on this partition. Note that `parted` will not create the filesystem. It just sets a flag on the partition which tells the OS what kind of data to expect from it.

`START`
- Specifies the exact point on the device where the partition begins. You can use different units to specify this point. `2s` can be used to refer to the second sector of the disk, while `1m` refers to the beginning of the first megabyte of the disk. Other common units are `B` (bytes) and `%` (percentage of the disk).

`END`
Specifies the end of the partition. Note that this is not the size of the partition, this is the point on the disk where it ends. For example, if you specify 100m the partition will end `100 MB` after the start of the disk. You can use the same units as in the `START` parameter.

So, the command:

```
(parted) mkpart primary ext4 1m 100m
```

Creates a primary partition of type `ext4`, starting at the first megabyte of the disk, and ending after the 100th megabyte.

### Removing a Partition
To remove a partition, use the command `rm` followed by the partition number, which you can display using the `print` command. So, `rm 2` would remove the second partition on the currently selected disk.

### Recovering Partitions
`parted` can recover a deleted partition. Consider you have the following partition structure:

```
Number  Start   End     Size    File system  Name     Flags
 1      1049kB  99.6MB  98.6MB  ext4         primary
 2      99.6MB  200MB   100MB   ext4         primary
 3      200MB   300MB   99.6MB  ext4         primary
```

By accident, you removed partition 2 using `rm 2`. To recover it, you can use the `rescue` command, with the syntax `rescue START END`, where `START` is the approximate location where the partition started, and `END` the approximate location where it ended.

`parted` will scan the disk in search of partitions, and offer to restore any that are found. In the example above the partition `2` started at 99,6 MB and ended at 200 MB. So you can use the following command to recover the partition:

```
(parted) rescue 90m 210m
Information: A ext4 primary partition was found at 99.6MB -> 200MB.
Do you want to add it to the partition table?

Yes/No/Cancel? y
```

This will recover the partition and its contents. Note that `rescue` can only recover partitions that have a filesystem installed on them. Empty partitions are not detected.

### Resizing ext2/3/4 Partitions
`parted` can be used to resize partitions to make them bigger or smaller. However, there are some caveats:

- During resizing the partition must be unused and unmounted.
- You need enough free space after the partition to grow it to the size you want.

The command is `resizepart`, followed by the partition number and where it should end. For example, if you have the following partition table:

```
Number  Start   End     Size    File system  Name     Flags
 1      1049kB  99.6MB  98.6MB  ext4         primary
 2      99.6MB  200MB   100MB   ext4
 3      200MB   300MB   99.6MB  ext4         primary
```

Trying to grow partition `1` using `resizepart` would trigger an error message, because with the new size partition `1` would overlap with partition `2`. However partition `3` can be resized as there is free space after it, which can be verified with the command `print free`:

```
(parted) print free
Model: Kingston DataTraveler 2.0 (scsi)
Disk /dev/sdb: 1999MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

 Number  Start   End     Size    File system  Name     Flags
        17.4kB  1049kB  1031kB  Free Space
 1      1049kB  99.6MB  98.6MB  ext4         primary
 2      99.6MB  200MB   100MB   ext4
 3      200MB   300MB   99.6MB  ext4         primary
        300MB   1999MB  1699MB  Free Space
```

So you can use the following command to resize partition 3 to 350 MB:

```
(parted) resizepart 3 350m

(parted) print
Model: Kingston DataTraveler 2.0 (scsi)
Disk /dev/sdb: 1999MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  99.6MB  98.6MB  ext4         primary
 2      99.6MB  200MB   100MB   ext4
 3      200MB   350MB   150MB   ext4         primary
```

Remember that the new end point is specified counting from the start of the disk. So, because partition `3` ended at 300 MB, now it needs to end at 350 MB.

But resizing the partition is only one part of the task. You also need to resize the filesystem that resides in it. For ext2/3/4 filesystems this is done with the `resize2fs` command. In the case of the example above, partition 3 still shows the “old” size when mounted:

```
$ df -h /dev/sdb3
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb3        88M  1.6M   80M   2% /media/carol/part3
```

To adjust the size the command `resize2fs DEVICE SIZE` can be used, where `DEVICE` corresponds to the partition you want to resize, and `SIZE` is the new size. If you omit the size parameter, it will use all of the available space of the partition. Before resizing, it is advised to unmount the partition.

In the example above:

```
$ sudo resize2fs /dev/sdb3
resize2fs 1.44.6 (5-Mar-2019)
Resizing the filesystem on /dev/sdb3 to 146212 (1k) blocks.
The filesystem on /dev/sdb3 is now 146212 (1k) blocks long.

$ df -h /dev/sdb3
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb3       135M  1.6M  123M   2% /media/carol/part3
```

To shrink a partition, the process needs to be done in the reverse order. First you resize the filesystem to the new, smaller size, then you resize the partition itself using `parted`.

```
Warning
Pay attention when shrinking partitions. If you get the order of things wrong, you will lose data!
```

In our example:

```
# resize2fs /dev/sdb3 88m
resize2fs 1.44.6 (5-Mar-2019)
Resizing the filesystem on /dev/sdb3 to 90112 (1k) blocks.
The filesystem on /dev/sdb3 is now 90112 (1k) blocks long.

# parted /dev/sdb3
(parted) resizepart 3 300m
Warning: Shrinking a partition can cause data loss, are you sure
you want to continue?

Yes/No? y

(parted) print
Model: Kingston DataTraveler 2.0 (scsi)
Disk /dev/sdb: 1999MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  99.6MB  98.6MB  ext4         primary
 2      99.6MB  200MB   100MB   ext4
 3      200MB   300MB   99.7MB  ext4         primary
```

```
Tip
Instead of specifying a new size, you can use the -M parameter of resize2fs to adjust the size of the filesystem so it is just big enough for the files on it.
```

### Creating Swap Partitions
On Linux, the system can swap memory pages from RAM to disk as needed, storing them on a separate space usually implemented as a separate partition on a disk, called the swap partition or simply swap. This partition needs to be of a specific type, and set-up with a proper utility (`mkswap`) before it can be used.

To create the swap partition using `fdisk` or `gdisk`, just proceed as if you were creating a regular partition, as explained before. The only difference is that you will need to change the partition type to Linux swap.

- On `fdisk` use the `t` command. Select the partition you want to use and change its type to `82`. Write changes to disk and quit with `w`.

On `gdisk` the command to change the partition type is also `t`, but the code is `8200`. Write changes to disk and quit with `w`.

If you are using `parted`, the partition should be identified as a swap partition during creation, just use `linux-swap` as the filesystem type. For example, the command to create a 500 MB swap partition, starting at 300 MB on disk is:

```
(parted) mkpart primary linux-swap 301m 800m
```

Once the partition is created and properly identified, just use `mkswap` followed by the device representing the partition you want to use, like:

```
# mkswap /dev/sda2
```

To enable swap on this partition, use `swapon` followed by the device name:

```
# swapon /dev/sda2
```

Similarly, `swapoff`, followed by the device name, will disable swap on that device.

Linux also supports the use of swap files instead of partitions. Just create an empty file of the size you want using dd and then use `mkswap` and `swapon` with this file as the target.

The following commands will create a 1 GB file called `myswap` in the current directory, filled with zeroes, and than set-up and enable it as a swap file.

Create the swap file:

```
$ dd if=/dev/zero of=myswap bs=1M count=1024
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 7.49254 s, 143 MB/s
```

`if=` is the input file, the source of the data that will be written to the file. In this case it is the `/dev/zero` device, which provides as many `NULL` characters as requested. of= is the output file, the file that will be created. `bs=` is the size of the data blocks, here specified in Megabytes, and `count=` is the amount of blocks to be written to the output. 1024 blocks of 1 MB each equals 1 GB.

```
# mkswap myswap
Setting up swapspace version 1, size = 1024 MiB (1073737728 bytes)
no label, UUID=49c53bc4-c4b1-4a8b-a613-8f42cb275b2b

# swapon myswap
```

Using the commands above, this swap file will be used only during the current system session. If the machine is rebooted, the file will still be available, but will not be automatically loaded. You can automate that by adding the new swap file to `/etc/fstab`, which we will discuss in a later lesson.

```
Tip
Both mkswap and swapon will complain if your swap file has insecure permissions. The recommended file permission flag is 0600. Owner and group should be root.
```

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)