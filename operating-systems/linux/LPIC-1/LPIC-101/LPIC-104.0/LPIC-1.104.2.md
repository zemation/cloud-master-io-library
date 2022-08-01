# LPIC-1: Devices, Linux Filesystems, Filesystem Hierarchy Standard

## Lesson 104.2: Maintain the integrity of filesystems

Modern Linux filesystems are journaled. This means that every operation is registered in an internal log (the journal) before it is executed. If the operation is interrupted due to a system error (like a kernel panic, power failure, etc.) it can be reconstructed by checking the journal, avoiding filesystem corruption and loss of data.

This greatly reduces the need for manual filesystem checks, but they may still be needed. Knowing the tools used for this (and the corresponding parameters) may represent the difference between dinner at home with your family or an all-nighter in the server room at work.

In this lesson, we will discuss the tools available to monitor filesystem usage, optimize its operation and how to check and repair damage.

### Checking Disk Usage
There are two commands that can be used to check how much space is being used and how much is left on a filesystem. The first one is `du`, which stands for “disk usage”.

`du` is recursive in nature. In its most basic form, the command will simply show how many 1 Kilobyte blocks are being used by the current directory and all its subdirectories:

```
$ du
4816	.
```

This is not very helpful, so we can request more “human readable” output by adding the `-h` parameter:

```
$ du -h
4.8M	.
```

By default, `du` only shows the usage count for directories (considering all files and subdirectories inside it). To show an individual count for all files in the directory, use the `-a` parameter:

```
$ du -ah
432K	./geminoid.jpg
508K	./Linear_B_Hero.jpg
468K	./LG-G8S-ThinQ-Mirror-White.jpg
656K	./LG-G8S-ThinQ-Range.jpg
60K	./Stranger3_Titulo.png
108K	./Baidu_Banho.jpg
324K	./Xiaomi_Mimoji.png
284K	./Mi_CC_9e.jpg
96K	./Mimoji_Comparativo.jpg
32K	./Xiaomi FCC.jpg
484K	./geminoid2.jpg
108K	./Mimoji_Abre.jpg
88K	./Mi8_Hero.jpg
832K	./Tablet_Linear_B.jpg
332K	./Mimoji_Comparativo.png
4.8M	.
```

The default behaviour is to show the usage of every subdirectory, then the total usage of the current directory, including subdirectories:

```
$ du -h
4.8M	./Temp
6.0M	.
```

In the example above, we can see that the subdirectory `Temp` occupies 4.8 MB and the current directory, including `Temp`, occupies 6.0 MB. But how much space do the files in the current directory occupy, excluding the subdirectories? For that we have the `-S` parameter:

```
$ du -Sh
4.8M	./Temp
1.3M	.
```

```
Tip
Keep in mind that command line parameters are case-sensitive: -s is different from -S.
```

If you want to keep this distinction between the space used by the files in the current directory and the space used by subdirectories, but also want a grand total at the end, you can add the `-c` parameter:

```
$ du -Shc
4.8M	./Temp
1.3M	.
6.0M	total
```

You can control how “deep” the output of `du` should go with the `-d N` parameter, where `N` describes the levels. For example, if you use the `-d 1` parameter, it will show the current directory and its subdirectories, but not the subdirectories of those.

See the difference below. Without `-d`:

```
$ du -h
216K	./somedir/anotherdir
224K	./somedir
232K	.
```

And limiting the depth to one level with `-d 1`:

```
$ du -h -d1
224K	./somedir
232K	.
```

Please note that even if `anotherdir` is not being shown, its size is still being taken into account.

You may wish to exclude some types of files from the count, which you can do with `--exclude="PATTERN"`, where `PATTERN` is the pattern against which you wish to match. Consider this directory:

```
$ du -ah
124K	./ASM68K.EXE
2.0M	./Contra.bin
36K	./fixheadr.exe
4.0K	./README.txt
2.1M	./Contra_NEW.bin
4.0K	./Built.bat
8.0K	./Contra_Main.asm
4.2M	.
```

Now, we will use `--exclude` to filter out every file with the `.bin` extension:

```
$ du -ah --exclude="*.bin"
124K	./ASM68K.EXE
36K	./fixheadr.exe
4.0K	./README.txt
4.0K	./Built.bat
8.0K	./Contra_Main.asm
180K	.
```

Note that the total no longer reflects the size of the excluded files.

### Checking for Free Space
`du` works at the files level. There is another command that can show you disk usage, and how much space is available, at the filesystem level. This command is `df`.

The command `df` will provide a list of all of the available (already mounted) filesystems on your system, including their total size, how much space has been used, how much space is available, the usage percentage and where it is mounted:

```
$ df
Filesystem     1K-blocks      Used Available Use% Mounted on
udev             2943068         0   2943068   0% /dev
tmpfs             595892      2496    593396   1% /run
/dev/sda1      110722904  25600600  79454800  25% /
tmpfs            2979440    951208   2028232  32% /dev/shm
tmpfs               5120         0      5120   0% /run/lock
tmpfs            2979440         0   2979440   0% /sys/fs/cgroup
tmpfs             595888        24    595864   1% /run/user/119
tmpfs             595888       116    595772   1% /run/user/1000
/dev/sdb1          89111      1550     80824   2% /media/carol/part1
/dev/sdb3          83187      1550     75330   3% /media/carol/part3
/dev/sdb2          90827      1921     82045   3% /media/carol/part2
/dev/sdc1      312570036 233740356  78829680  75% /media/carol/Samsung Externo
```

However, showing the size in 1 KB blocks is not very user-friendly. Like on `du`, you can add the `-h` parameters to get a more “human readable” output:

```
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            2.9G     0  2.9G   0% /dev
tmpfs           582M  2.5M  580M   1% /run
/dev/sda1       106G   25G   76G  25% /
tmpfs           2.9G  930M  2.0G  32% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           2.9G     0  2.9G   0% /sys/fs/cgroup
tmpfs           582M   24K  582M   1% /run/user/119
tmpfs           582M  116K  582M   1% /run/user/1000
/dev/sdb1        88M  1.6M   79M   2% /media/carol/part1
/dev/sdb3        82M  1.6M   74M   3% /media/carol/part3
/dev/sdb2        89M  1.9M   81M   3% /media/carol/part2
/dev/sdc1       299G  223G   76G  75% /media/carol/Samsung Externo
```

You can also use the `-i` parameter to show used/available inodes instead of blocks:

```
$ df -i
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
udev            737142    547  736595    1% /dev
tmpfs           745218    908  744310    1% /run
/dev/sda6      6766592 307153 6459439    5% /
tmpfs           745218    215  745003    1% /dev/shm
tmpfs           745218      4  745214    1% /run/lock
tmpfs           745218     18  745200    1% /sys/fs/cgroup
/dev/sda1        62464    355   62109    1% /boot
tmpfs           745218     43  745175    1% /run/user/1000
```

One useful parameter is `-T`, which will also print the type of each filesystem:

```
$ df -hT
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  2.9G     0  2.9G   0% /dev
tmpfs          tmpfs     582M  2.5M  580M   1% /run
/dev/sda1      ext4      106G   25G   76G  25% /
tmpfs          tmpfs     2.9G  930M  2.0G  32% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     2.9G     0  2.9G   0% /sys/fs/cgroup
tmpfs          tmpfs     582M   24K  582M   1% /run/user/119
tmpfs          tmpfs     582M  116K  582M   1% /run/user/1000
/dev/sdb1      ext4       88M  1.6M   79M   2% /media/carol/part1
/dev/sdb3      ext4       82M  1.6M   74M   3% /media/carol/part3
/dev/sdb2      ext4       89M  1.9M   81M   3% /media/carol/part2
/dev/sdc1      fuseblk   299G  223G   76G  75% /media/carol/Samsung Externo
```

Knowing the type of the filesystem you can filter the output. You can show only filesystems of a given type with `-t TYPE`, or exclude filesystems of a given type with `-x TYPE`, like in the examples below.

Excluding `tmpfs` filesystems:

```
$ df -hx tmpfs
Filesystem      Size  Used Avail Use% Mounted on
udev            2.9G     0  2.9G   0% /dev
/dev/sda1       106G   25G   76G  25% /
/dev/sdb1        88M  1.6M   79M   2% /media/carol/part1
/dev/sdb3        82M  1.6M   74M   3% /media/carol/part3
/dev/sdb2        89M  1.9M   81M   3% /media/carol/part2
/dev/sdc1       299G  223G   76G  75% /media/carol/Samsung Externo
```

Showing only `ext4` filesystems:

```
$ df -ht ext4
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       106G   25G   76G  25% /
/dev/sdb1        88M  1.6M   79M   2% /media/carol/part1
/dev/sdb3        82M  1.6M   74M   3% /media/carol/part3
/dev/sdb2        89M  1.9M   81M   3% /media/carol/part2
```

You can also customize the output of `df`, selecting what should be displayed and in which order, using the `--output=` parameter followed by a comma separated list of fields you wish to display. Some of the available fields are:

`source`
- The device corresponding to the filesystem.

`fstype`
- The filesystem type.

`size`
- The total size of the filesystem.

`used`
- How much space is being used.

`avail`
- How much space is available.

`pcent`
- The usage percentage.

`target`
- Where the filesystem is mounted (mount point).

If you want an output showing the target, source, type and usage, you can use:

```
$ df -h --output=target,source,fstype,pcent
Mounted on                    Filesystem     Type     Use%
/dev                          udev           devtmpfs   0%
/run                          tmpfs          tmpfs      1%
/                             /dev/sda1      ext4      25%
/dev/shm                      tmpfs          tmpfs     32%
/run/lock                     tmpfs          tmpfs      0%
/sys/fs/cgroup                tmpfs          tmpfs      0%
/run/user/119                 tmpfs          tmpfs      1%
/run/user/1000                tmpfs          tmpfs      1%
/media/carol/part1            /dev/sdb1      ext4       2%
/media/carol/part3            /dev/sdb3      ext4       3%
/media/carol/part2            /dev/sdb2      ext4       3%
/media/carol/Samsung Externo  /dev/sdc1      fuseblk   75%
```

`df` can also be used to check inode information, by passing the following fields to `--output=`:

`itotal`
- The total number of inodes in the filesystem.

`iused`
- The number of used inodes in the filesystem.

`iavail`
- The number of available inodes in the filesystem.

`ipcent`
- The percentage of used inodes in the filesystem.

For example:

```
$ df --output=source,fstype,itotal,iused,ipcent
Filesystem     Type      Inodes  IUsed IUse%
udev           devtmpfs  735764    593    1%
tmpfs          tmpfs     744858   1048    1%
/dev/sda1      ext4     7069696 318651    5%
tmpfs          tmpfs     744858    222    1%
tmpfs          tmpfs     744858      3    1%
tmpfs          tmpfs     744858     18    1%
tmpfs          tmpfs     744858     22    1%
tmpfs          tmpfs     744858     40    1%
```

### Maintaining ext2, ext3 and ext4 Filesystems
To check a filesystem for errors (and hopefully fix them), Linux provides the `fsck` utility (think of “filesystem check” and you will never forget the name). In its most basic form, you can invoke it with `fsck` followed by the filesystem’s location you want to check:

```
# fsck /dev/sdb1
fsck from util-linux 2.33.1
e2fsck 1.44.6 (5-Mar-2019)
DT_2GB: clean, 20/121920 files, 369880/487680 blocks
```

```
Warning
NEVER run fsck (or related utilities) on a mounted filesystem. If this is done anyway, data may be lost.
```

`fsck` itself will not check the filesystem, it will merely call the appropriate utility for the filesystem type to do so. In the example above, since a filesystem type was not specified, `fsck` assumed an ext2/3/4 filesystem by default, and called `e2fsck`.

To specify a filesystem, use the `-t` option, followed by the filesystem name, like in `fsck -t vfat /dev/sdc`. Alternatively, you may call a filesystem-specific utility directly, like `fsck.msdos` for FAT filesystems.

```
Tip
Type fsck followed by Tab twice to see a list of all related commands on your system.
```

`fsck` can take some command-line arguments. These are some of the most common:

`-A`
- This will check all filesystems listed in `/etc/fstab`.

`-C`
- Displays a progress bar when checking a filesystem. Currently only works on ext2/3/4 filesystems.

`-N`
- This will print what would be done and exit, without actually checking the filesystem.

`-R`
- When used in conjunction with `-A`, this will skip checking the root filesystem.

`-V`
- Verbose mode, prints more information than usual during operation. This is useful for debugging.

The specific utility for ext2, ext3 and ext4 filesystems is `e2fsck`, also called `fsck.ext2`, `fsck.ext3` and `fsck.ext4` (those three are merely links to `e2fsck`). By default, it runs in interactive mode: when a filesystem error is found, it stops and asks the user what to do. The user must type `y` to fix the problem, `n` to leave it unfixed or `a` to fix the current problem and all subsequent ones.

Of course sitting in front of a terminal waiting for `e2fsck` to ask what to do is not a productive use of your time, especially if you are dealing with a big filesystem. So, there are options that cause `e2fsck` to run in non-interactive mode:

`-p`
- This will attempt to automatically fix any errors found. If an error that requires intervention from the system administrator is found, `e2fsck` will provide a description of the problem and exit.

`-y`
- This will answer `y` (yes) to all questions.

`-n`
- The opposite of `-y`. Besides answering `n` (no) to all questions, this will cause the filesystem to be mounted read-only, so it cannot be modified.

`-f`
- Forces `e2fsck` to check a filesystem even if is marked as “clean”, i.e. has been correctly unmounted.

### Fine Tuning an ext Filesystem
ext2, ext3 and ext4 filesystems have a number of parameters that can be adjusted, or “tuned”, by the system administrator to better suit the system needs. The utility used to display or modify these parameters is called `tune2fs`.

To see the current parameters for any given filesystem, use the `-l` parameter followed by the device representing the partition. The example below shows the output of this command on the first partition of the first disk (`/dev/sda1`) of a machine:

```
# tune2fs -l /dev/sda1
tune2fs 1.44.6 (5-Mar-2019)
Filesystem volume name:   <none>
Last mounted on:          /
Filesystem UUID:          6e2c12e3-472d-4bac-a257-c49ac07f3761
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent 64bit flex_bg sparse_super large_file huge_file dir_nlink extra_isize metadata_csum
Filesystem flags:         signed_directory_hash
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              7069696
Block count:              28255605
Reserved block count:     1412780
Free blocks:              23007462
Free inodes:              6801648
First block:              0
Block size:               4096
Fragment size:            4096
Group descriptor size:    64
Reserved GDT blocks:      1024
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         8192
Inode blocks per group:   512
Flex block group size:    16
Filesystem created:       Mon Jun 17 13:49:59 2019
Last mount time:          Fri Jun 28 21:14:38 2019
Last write time:          Mon Jun 17 13:53:39 2019
Mount count:              8
Maximum mount count:      -1
Last checked:             Mon Jun 17 13:49:59 2019
Check interval:           0 (<none>)
Lifetime writes:          20 GB
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:	          256
Required extra isize:     32
Desired extra isize:      32
Journal inode:            8
First orphan inode:       5117383
Default directory hash:   half_md4
Directory Hash Seed:      fa95a22a-a119-4667-a73e-78f77af6172f
Journal backup:           inode blocks
Checksum type:            crc32c
Checksum:                 0xe084fe23
```

ext filesystems have mount counts. The count is increased by 1 each time the filesystem is mounted, and when a threshold value (the maximum mount count) is reached the system will be automatically checked with `e2fsck` on the next boot.

The maximum mount count can be set with the `-c N` parameter, where `N` is the number of times the filesystem can be mounted without being checked. The `-C N` parameter sets the number of times the system has been mounted to the value of `N`. Note that command line parameters are case-sensitive, so `-c` is different from `-C`.

It is also possible to define a time interval between checks, with the `-i` parameter, followed by a number and the letters `d` for days, `m` for months and `y` for years. For example, `-i 10d` would check the filesystem at the next reboot every 10 days. Use zero as the value to disable this feature.

`-L` can be used to set a label for the filesystem. This label can have up to 16 characters. The `-U` parameter sets the UUID for the filesystem, which is a 128 bit hexadecimal number. In the example above, the UUID is `6e2c12e3-472d-4bac-a257-c49ac07f3761`. Both the label and UUID can be used instead of the device name (like `/dev/sda1`) to mount the filesystem.

The option `-e BEHAVIOUR` defines the kernel behaviour when a filesystem error is found. There are three possible behaviours:

`continue`
- Will continue execution normally.

`remount-ro`
- Will remount the filesystem as read-only.

`panic`
- Will cause a kernel panic.

The default behaviour is to `continue`. `remount-ro` might be useful in data-sensitive applications, as it will immediately stop writes to the disk, avoiding more potential errors.

ext3 filesystems are basically ext2 filesystems with a journal. Using `tune2fs` you can add a journal to an ext2 filesystem, thus converting it to ext3. The procedure is simple, just pass the `-j` parameter to `tune2fs`, followed by the device containing the filesystem:

```
# tune2fs -j /dev/sda1
```

Afterwards, when mounting the converted filesystem, do not forget to set the type to `ext3` so the journal can be used.

When dealing with journaled filesystems, the `-J` parameter allows you to use extra parameters to set some journal options, like `-J size=` to set the journal size (in megabytes), `-J location=` to specify where the journal should be stored (either a specific block, or a specific position on the disk with suffixes like `M` or `G`) and even put the journal on an external device with `-J device=`.

You can specify multiple parameters at once by separating them with a comma. For example: `-J size=10,location=100M,device=/dev/sdb1` will create a 10 MB Journal at the 100 MB position on the device `/dev/sdb1`.

```
Warning
tune2fs has a “brute force” option, -f, which will force it to complete an operation even if errors are found. Needless to say, this should be only used with extreme caution.
```

### Maintaining XFS Filesystems
For XFS filesystems, the equivalent of `fsck` is `xfs_repair`. If you suspect that something is wrong with the filesystem, the first thing to do is to scan it for damage.

This can be done by passing the `-n` parameter to `xfs_repair`, followed by the device containing the filesystem. The `-n` parameter means “no modify”: the filesystem will be checked, errors will be reported but no repairs will be made:

```
# xfs_repair -n /dev/sdb1
Phase 1 - find and verify superblock...
Phase 2 - using internal log
        - zero log...
        - scan filesystem freespace and inode maps...
        - found root inode chunk
Phase 3 - for each AG...
        - scan (but do not clear) agi unlinked lists...
        - process known inodes and perform inode discovery...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
        - process newly discovered inodes...
Phase 4 - check for duplicate blocks...
        - setting up duplicate extent list...
        - check for inodes claiming duplicate blocks...
        - agno = 1
        - agno = 3
        - agno = 0
        - agno = 2
No modify flag set, skipping phase 5
Phase 6 - check inode connectivity...
        - traversing filesystem ...
        - traversal finished ...
        - moving disconnected inodes to lost+found ...
Phase 7 - verify link counts...
No modify flag set, skipping filesystem flush and exiting.
```

If errors are found, you can proceed to do the repairs without the `-n` parameter, like so: `xfs_repair /dev/sdb1`.

`xfs_repair` accepts a number of command line options. Among them:

`-l LOGDEV` and `-r RTDEV`
- These are needed if the filesystem has external log and realtime sections. In this case, replace `LOGDEV` and `RTDEV` with the corresponding devices.

`-m N`
- Is used to limit the memory usage of `xfs_repair` to `N` megabytes, something which can be useful on server settings. According to the man page, by default `xfs_repair` will scale its memory usage as needed, up to 75% of the system’s physical RAM.

`-d`
- The “dangerous” mode will enable the repair of filesystems that are mounted read-only.

`-v`
- You may have guessed it: verbose mode. Each time this parameter is used, the “verbosity” is increased (for example, `-v -v` will print more information than just `-v`).

Note that `xfs_repair` is unable to repair filesystems with a “dirty” log. You can “zero out” a corrupt log with the `-L` parameter, but keep in mind that this is a last resort as it may result in filesystem corruption and data loss.

To debug an XFS filesystem, the utility `xfs_db` can be used, like in `xfs_db /dev/sdb1`. This is mostly used to inspect various elements and parameters of the filesystem.

This utility has an interactive prompt, like `parted`, with many internal commands. A help system is also available: type `help` to see a list of all commands, and `help` followed by the command name to see more information about the command.

Another useful utility is `xfs_fsr`, which can be used to reorganize (“defragment”) an XFS filesystem. When executed without any extra arguments it will run for two hours and try to defragment all mounted, writable XFS filesystems listed on the `/etc/mtab/` file. You may need to install this utility using the package manager for your Linux distribution, as it may not be part of a default install. For more information consult the corresponding man page.

___

This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)