# LPIC-1: Linux Installation and Package Management

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
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)