# LPIC-1: Devices, Linux Filesystems, Filesystem Hierarchy Standard

## Lesson 104.3: Control mounting and unmounting of filesystems
Up until now, you learned how to partition disks and how to create and maintain filesystems on them. However before a filesystem can be accessed on Linux, it needs to be mounted.

This means attaching the filesystem to a specific point in your system’s directory tree, called a mount point. Filesystems can be mounted manually or automatically and there are many ways to do this. We will learn about some of them in this lesson.

### Mounting and Unmounting Filesystems
The command to manually mount a filesystem is called `mount` and its syntax is:

```
mount -t TYPE DEVICE MOUNTPOINT
```

Where:

`TYPE`
- The type of the filesystem being mounted (e.g. ext4, btrfs, exfat, etc.).

`DEVICE`
- The name of the partition containing the filesystem (e.g. `/dev/sdb1`)

`MOUNTPOINT`
- Where the filesystem will be mounted. The mounted-on directory need not be empty, although it must exist. Any files in it, however, will be inaccessible by name while the filesystem is mounted.

For example, to mount a USB flash drive containing an exFAT filesystem located on `/dev/sdb1` to a directory called `flash` under your home directory, you could use:

```
# mount -t exfat /dev/sdb1 ~/flash/
```

```
Tip
Many Linux systems use the Bash shell, and on those the tilde ~ on the path to the mountpoint is a shorthand for the current user’s home directory. If the current user is named john, for example, it will be replaced by /home/john.
```

After mounting, the contents of the filesystem will be accessible under the `~/flash` directory:

```
$  ls -lh ~/flash/
total 469M
-rwxrwxrwx 1 root root 454M jul 19 09:49 lineage-16.0-20190711-MOD-quark.zip
-rwxrwxrwx 1 root root  16M jul 19 09:44 twrp-3.2.3-mod_4-quark.img
```

### Listing Mounted Filesystems
If you type just `mount`, you will get a list of all the filesystems currently mounted on your system. This list can be quite large, because in addition to the disks attached to your system, it also contains a number of run-time filesystems in memory that serve various purposes. To filter the output, you can use the `-t` parameter to list only filesystems of the corresponding type, like below:

```
# mount -t ext4
/dev/sda1 on / type ext4 (rw,noatime,errors=remount-ro)
```

You can specify multiple filesystems at once by separating them with a comma:

```
# mount -t ext4,fuseblk
/dev/sda1 on / type ext4 (rw,noatime,errors=remount-ro)
/dev/sdb1 on /home/carol/flash type fuseblk (rw,nosuid,nodev,relatime,user_id=0,group_id=0,default_permissions,allow_other,blksize=4096) [DT_8GB]
```

The output in the examples above can be described in the format:

```
SOURCE on TARGET type TYPE OPTIONS
```

Where `SOURCE` is the partition which contains the filesystem, `TARGET` is the directory where it is mounted, `TYPE` is the filesystem type and `OPTIONS` are the options passed to the `mount` command at mount time.

### Additional Command Line Parameters
There are many command line parameters that can be used with `mount`. Some of the most used ones are:

`-a`
- This will mount all filesystems listed in the file /etc/fstab (more on that in the next section).

`-o` or `--options`
- This will pass a list of comma-separated mount options to the mount command, which can change how the filesystem will be mounted. These will also be discussed alongside `/etc/fstab`.

`-r` or `-ro`
This will mount the filesystem as read-only.

`-w` or `-rw`
This will the mount filesystem as writable.

To unmount a filesystem, use the `umount` command, followed by the device name or the mount point. Considering the example above, the commands below are interchangeable:

```
# umount /dev/sdb1
# umount ~/flash
```

Some of the command line parameters to `umount` are:

`-a`
- This will unmount all filesystems listed in `/etc/fstab`.

`-f`
- This will force the unmounting of a filesystem. This may be useful if you mounted a remote filesystem that has become unreachable.

`-r`
- If the filesystem cannot be unmounted, this will try to make it read-only.

### Dealing with Open Files
When unmounting a filesystem, you may encounter an error message stating that the `target is busy`. This will happen if any files on the filesystem are open. However, it may not be immediately obvious where an open file is located, or what is accessing the filesystem.

In such cases you may use the `lsof` command, followed by the name of the device containing the filesystem, to see a list of processes accessing it and which files are open. For example:

```
# umount /dev/sdb1
umount: /media/carol/External_Drive: target is busy.

# lsof /dev/sdb1
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
evince  3135 carol   16r   REG   8,17 21881768 5195 /media/carol/External_Drive/Documents/E-Books/MagPi40.pdf
```

`COMMAND` is the name of the executable that opened the file, and `PID` is the process number. `NAME` is the name of the file that is open. In the example above, the file `MagPi40.pdf` is opened by the program `evince` (a PDF viewer). If we close the program, then we will be able to unmount the filesystem.

```
Note
Before the lsof output appears GNOME users may see a warning message in the terminal window.

lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1000/gvfs
Output information may be incomplete.
lsof tries to process all mounted filesystems. This warning message is raised because lsof has encountered a GNOME Virtual file system (GVFS). This is a special case of a filesystem in user space (FUSE). It acts as a bridge between GNOME, its APIs and the kernel. No one—not even root—can access one of these file systems, apart from the owner who mounted it (in this case, GNOME). You can ignore this warning.
```

### Where to Mount?
You can mount a filesystem anywhere you want. However, there are some good practices that should be followed to make system administration easier.

Traditionally, `/mnt` was the directory under which all external devices would be mounted and a number of pre-configured “anchor points” for common devices, like CD-ROM drives (`/mnt/cdrom`) and floppy disks (`/mnt/floppy`) existed under it.

This has been superseded by `/media`, which is now the default mount point for any user-removable media (e.g. external disks, USB flash drives, memory card readers, etc.) connected to the system.

On most modern Linux distributions and desktop environments, removable devices are automatically mounted under `/media/USER/LABEL` when connected to the system, where `USER` is the username and `LABEL` is the device label. For example, a USB flash drive with the label `FlashDrive` connected by the user john would be mounted under `/media/john/FlashDrive/`. The way this is handled is different depending on the desktop environment.

That being said, whenever you need to manually mount a filesystem, it is good practice to mount it under `/mnt`.

### Mounting Filesystems on Bootup
The file `/etc/fstab` contains descriptions about the filesystems that can be mounted. This is a text file, where each line describes a filesystem to be mounted, with six fields per line in the following order:

```
FILESYSTEM MOUNTPOINT TYPE OPTIONS DUMP PASS
```

Where:

`FILESYSTEM`
- The device containing the filesystem to be mounted. Instead of the device, you can specify the UUID or label of the partition, something which we will discuss later on.

`MOUNTPOINT`
- Where the filesystem will be mounted.

`TYPE`
- The filesystem type.

`OPTIONS`
- Mount options that will be passed to `mount`.

`DUMP`
- Indicates whether any ext2, ext3 or ext4 filesystems should be considered for backup by the `dump` command. Usually it is zero, meaning they should be ignored.

`PASS`
- When non-zero, defines the order in which the filesystems will be checked on bootup. Usually it is zero.

For example, the first partition on the first disk of a machine could be described as:

```
/dev/sda1  /  ext4  noatime,errors
```

The mount options on `OPTIONS` are a comma-separated list of parameters, which can be generic or filesystem specific. Among the generic ones we have:

`atime` and `noatime`
- By default, every time a file is read the access time information is updated. Disabling this (with `noatime`) can speed up disk I/O. Do not confuse this with the modification time, which is updated every time a file is written to.

`auto` and `noauto`
- Whether the filesystem can (or can not) be mounted automatically with mount -a.

`defaults`
- This will pass the options `rw`, `suid`, `dev`, `exec`, `auto`, `nouser` and `async` to `mount`.

`dev` and `nodev`
- Whether character or block devices in the mounted filesystem should be interpreted.

`exec` and `noexec`
- Allow or deny permission to execute binaries on the filesystem.

`user` and `nouser`
- Allows (or not) an ordinary user to mount the filesystem.

`group`
- Allows a user to mount the filesystem if the user belongs to the same group which owns the device containing it.

`owner`
- Allows a user to mount a filesystem if the user owns the device containing it.

`suid and nosuid`
- Allow, or not, SETUID and SETGID bits to take effect.

`ro` and `rw`
- Mount a filesystem as read-only or writable.

`remount`
This will attempt to remount an already mounted filesystem. This is not used on `/etc/fstab`, but as a parameter to `mount -o`. For example, to remount the already mounted partition `/dev/sdb1` as read-only, you could use the command `mount -o remount,ro /dev/sdb1`. When remounting, you do not need to specify the filesystem type, only the device name or the mount point.

`sync` and `async`
Whether to do all I/O operations to the filesystem synchronously or asynchronously. `async` is usually the default. The manual page for `mount` warns that using `sync` on media with a limited number of write cycles (like flash drives or memory cards) may shorten the life span of the device.

### Using UUIDs and Labels
Specifying the name of the device containing the filesystem to mount may introduce some problems. Sometimes the same device name may be assigned to another device depending on when, or where, it was connected to your system. For example, a USB flash drive on `/dev/sdb1` may be assigned to `/dev/sdc1` if plugged on another port, or after another flash drive.

One way to avoid this is to specify the label or UUID (Universally Unique Identifier) of the volume. Both are specified when the filesystem is created and will not change, unless the filesystem is destroyed or manually assigned a new label or UUID.

The command `lsblk` can be used to query information about a filesystem and find out the label and UUID associated to it. To do this, use the `-f` parameter, followed by the device name:

```
$ lsblk -f /dev/sda1
NAME FSTYPE LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda1 ext4         6e2c12e3-472d-4bac-a257-c49ac07f3761   64,9G    33% /
```

Here is the meaning of each column:

`NAME`
- Name of the device containing the filesystem.

`FSTYPE`
- Filesystem type.

`LABEL`
- Filesystem label.

`UUID`
- Universally Unique Identifier (UUID) assigned to the filesystem.

`FSAVAIL`
- How much space is available in the filesystem.

`FSUSE%`
- Usage percentage of the filesystem.

`MOUNTPOINT`
- Where the filesystem is mounted.

In `/etc/fstab` a device can be specified by its UUID with the `UUID=` option, followed by the UUID, or with `LABEL=`, followed by the label. So, instead of:

```
/dev/sda1  /  ext4  noatime,errors
```

You would use:

```
UUID=6e2c12e3-472d-4bac-a257-c49ac07f3761  /  ext4  noatime,errors
```

Or, if you have a disk labeled `homedisk`:
```
LABEL=homedisk  /home ext4  defaults
```

The same syntax can be used with the `mount` command. Instead of the device name, pass the UUID or label. For example, to mount an external NTFS disk with the UUID `56C11DCC5D2E1334` on `/mnt/external`, the command would be:

```
$ mount -t ntfs UUID=56C11DCC5D2E1334 /mnt/external
```

### Mounting Disks with Systemd
Systemd is the init process, the first process to run on many Linux distributions. It is responsible for spawning other processes, starting services and bootstraping the system. Among many other tasks, systemd can also be used to manage the mounting (and automounting) of filesystems.

To use this feature of systemd, you need to create a configuration file called a mount unit. Each volume to be mounted gets its own mount unit and they need to be placed in `/etc/systemd/system/`.

Mount units are simple text files with the `.mount` extension. The basic format is shown below:

```
[Unit]
Description=

[Mount]
What=
Where=
Type=
Options=

[Install]
WantedBy=
```

`Description=`
- Short description of the mount unit, something like Mounts the `backup disk`.

`What=`
- What should be mounted. The volume must be specified as `/dev/disk/by-uuid/VOL_UUID` where `VOL_UUID` is the UUID of the volume.

`Where=`
Should be the full path to where the volume should be mounted.

`Type=`
- The filesystem type.

`Options=`
- Mount options that you may wish to pass, these are the same used with the `mount` command or in `/etc/fstab`.

`WantedBy=`
 - Used for dependency management. In this case, we will use `multi-user.target`, which means that whenever the system boots into a multi-user environment (a normal boot) the unit will be mounted.

Our previous example of the external disk could be written as:

```
[Unit]
Description=External data disk

[Mount]
What=/dev/disk/by-uuid/56C11DCC5D2E1334
Where=/mnt/external
Type=ntfs
Options=defaults

[Install]
WantedBy=multi-user.target
```

But we are not done yet. To work correctly, the mount unit must have the same name as the mount point. In this case, the mount point is `/mnt/external`, so the file should be named `mnt-external.mount`.

After that, you need to restart the systemd daemon with the `systemctl` command, and start the unit:

```
# systemctl daemon-reload
# systemctl start mnt-external.mount
```

Now the contents of the external disk should be available on `/mnt/external`. You can check the status of the mounting with the command `systemctl status mnt-external.mount`, like below:

```
# systemctl status mnt-external.mount
● mnt-external.mount - External data disk
   Loaded: loaded (/etc/systemd/system/mnt-external.mount; disabled; vendor pres
   Active: active (mounted) since Mon 2019-08-19 22:27:02 -03; 14s ago
    Where: /mnt/external
     What: /dev/sdb1
    Tasks: 0 (limit: 4915)
   Memory: 128.0K
   CGroup: /system.slice/mnt-external.mount

ago 19 22:27:02 pop-os systemd[1]: Mounting External data disk...
ago 19 22:27:02 pop-os systemd[1]: Mounted External data disk.
```

The `systemctl start mnt-external.mount` command will only enable the unit for the current session. If you want to enable it on every boot, replace `start` with `enable`:

```
# systemctl enable mnt-external.mount
```

### Automounting a Mount Unit
Mount units can be automounted whenever the mount point is accessed. To do this, you need an `.automount` file, alongside the `.mount` file describing the unit. The basic format is:

```
[Unit]
Description=

[Automount]
Where=

[Install]
WantedBy=multi-user.target
```

Like before, `Description=` is a short description of the file, and `Where=` is the mountpoint. For example, an `.automount` file for our previous example would be:

```
[Unit]
Description=Automount for the external data disk

[Automount]
Where=/mnt/external

[Install]
WantedBy=multi-user.target
```

Save the file with the same name as the mount point (in this case, `mnt-external.automount`), reload systemd and start the unit:

```
# systemctl daemon-reload
# systemctl start mnt-external.automount
```

Now whenever the `/mnt/external` directory is accessed, the disk will be mounted. Like before, to enable the automount on every boot you would use:

```
# systemctl enable mnt-external.automount
```

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)