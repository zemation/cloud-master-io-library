# LPIC-1: Devices, Linux Filesystems, Filesystem Hierarchy Standard

### Lesson 104.7: Find system files and place files in the correct location

Linux distributions come in all shapes and sizes, but one thing that almost all of them share is that they follow the Filesystem Hierarchy Standard (FHS), which defines a “standard layout” for the filesystem, making interoperation and system administration much easier. In this lesson, you will learn more about this standard, and how to find files on a Linux system.

### The Filesystem Hierarchy Standard
The Filesystem Hierarchy Standard (FHS) is an effort by the Linux Foundation to standardize the directory structure and directory contents on Linux systems. Compliance with the standard is not mandatory, but most distributions follow it.

```
Note
Those interested in the details of filesystem organization can read the FHS 3.0 specification, available in multiple formats at: http://refspecs.linuxfoundation.org/fhs.shtml
```

According to the standard, the basic directory structure is as follows:

`/`
- This is the root directory, the topmost directory in the hierarchy. Every other directory is located inside it. A filesystem is often compared to a “tree”, so this would be the “trunk” to which all branches are connected.

`/bin`
- Essential binaries, available to all users.

`/boot`
- Files needed by the boot process, including the Initial RAM Disk (initrd) and the Linux kernel itself.

`/dev`
- Device files. These can be either physical devices connected to the system (for example, `/dev/sda` would be the first SCSI or SATA disk) or virtual devices provided by the kernel.

`/etc`
- Host-specific configuration files. Programs may create subdirectories under `/etc` to store multiple configuration files if needed.

`/home`
- Each user in the system has a “home” directory to store personal files and preferences, and most of them are located under `/home`. Usually, the home directory is the same as the username, so the user John would have his directory under `/home/john`. The exceptions are the superuser (root), which has a separate directory (`/root`) and some system users.

`/lib`
- Shared libraries needed to boot the operating system and to run the binaries under `/bin` and `/sbin`.

`/media`
- User-mountable removable media, like flash drives, CD and DVD-ROM readers, floppy disks, memory cards and external disks are mounted under here.

`/mnt`
- Mount point for temporarily mounted filesystems.

`/opt`
- Application software packages.

`/root`
- Home directory for the superuser (root).

`/run`
- Run-time variable data.

`/sbin`
- System binaries.

`/srv`
Data served by the system. For example, the pages served by a web server could be stored under `/srv/www`.

`/tmp`
- Temporary files.

`/usr`
- Read-only user data, including data needed by some secondary utilities and applications.

`/proc`
- Virtual filesystem containing data related to running processes.

`/var`
- Variable data written during system operation, including print queue, log data, mailboxes, temporary files, browser cache, etc.

Keep in mind that some of those directories, like `/etc`, `/usr` and `/var`, contain a whole hierarchy of subdirectories under them.

### Temporary Files
Temporary files are files used by programs to store data that are only needed for a short time. This can be the data of running processes, crash logs, scratch files from an autosave, intermediary files used during a file conversion, cache files and such.

#### Location of Temporary Files
Version 3.0 of the Filesystem Hierarchy Standard (FHS) defines standard locations for temporary files on Linux systems. Each location has a different purpose and behavior, and it is recommended that developers follow the conventions set by the FHS when writing temporary data to disk.

`/tmp`
- According to the FHS, programs should not assume that files written here will be preserved between invocations of a program. The recommendation is that this directory be cleared (all files erased) during system boot-up, although this is not mandatory.

`/var/tmp`
- Another location for temporary files, but this one should not be cleared during the system boot-up. Files stored here will usually persist between reboots.

`/run`
- This directory contains run-time variable data used by running processes, such as process identifier files (`.pid`). Programs that need more than one run-time file may create subdirectories here. This location must be cleared during system boot-up. The purpose of this directory was once served by `/var/run`, and on some systems `/var/run` may be a symbolic link to `/run`.

Note that there is nothing which prevents a program from creating temporary files elsewhere on the system, but it is good practice to respect the conventions set by the FHS.

### Finding Files
To search for files on a Linux system, you can use the `find` command. This is a very powerful tool, full of parameters that can suit its behaviour and modify output exactly to your needs.

To start, `find` needs two arguments: a starting point and what to look for. For example, to search for all files in the current directory (and subdirectories) whose name ends in `.jpg` you can use:

```
$ find . -name '*.jpg'
./pixel_3a_seethrough_1.jpg
./Mate3.jpg
./Expert.jpg
./Pentaro.jpg
./Mate1.jpg
./Mate2.jpg
./Sala.jpg
./Hotbit.jpg
```

This will match any file whose last four characters of the name are `.jpg`, no matter what comes before it, as `*` is a wildcard for “anything”. However, see what happens if another `*` is added at the end of the pattern:

```
$ find . -name '*.jpg*'
./pixel_3a_seethrough_1.jpg
./Pentaro.jpg.zip
./Mate3.jpg
./Expert.jpg
./Pentaro.jpg
./Mate1.jpg
./Mate2.jpg
./Sala.jpg
./Hotbit.jpg
```

The file `Pentaro.jpg.zip` (highlighted above) was not included in the previous listing, because even if it contains `.jpg` on its name, it did not match the pattern as there where extra characters after it. The new pattern means “anything `.jpg` anything”, so it matches.

```
Tip
Keep in mind that the -name parameter is case sensitive. If you wish to do a case-insensitive search, use -iname.
```

The `*.jpg` expression must be placed inside single quotes, to avoid the shell from interpreting the pattern itself. Try without the quotes and see what happens.

By default, `find` will begin at the starting point and descend through any subdirectories (and subdirectories of those subdirectories) found. You can restrict this behaviour with the `-maxdepth N` parameters, where `N` is the maximum number of levels.

To search only the current directory, you would use `-maxdepth 1`. Suppose you have the following directory structure:

```
directory
├── clients.txt
├── partners.txt -> clients.txt
└── somedir
    ├── anotherdir
    └── clients.txt
```

To search inside `somedir`, you would need to use `-maxdepth 2` (the current directory +1 level down). To search inside `anotherdir`, `-maxdepth 3` would be needed (the current directory +2 levels down). The parameter `-mindepth N` works in the opposite way searching only in directories at least `N` levels down.

The `-mount` parameter can be used to avoid find going down inside mounted filesystems. You can also restrict the search to specific filesystem types using the `-fstype` parameter. So `find /mnt -fstype exfat -iname "*report*`" would only search inside exFAT filesystems mounted under `/mnt`.

### Searching for Attributes
You can use the parameters below to search for files with specific attributes, like ones that are writable by your user, have a specific set of permissions or are of a certain size:

`-user USERNAME`
- Matches files owned by the user `USERNAME`.

`-group GROUPNAME`
- Matches files owned by the group `GROUPNAME`.

`-readable`
- Matches files that are readable by the current user.

`-writable`
- Matches files that are writable by the current user.

`-executable`
- Matches files that are executable by the current user. In the case of directories, this will match any directory that the user can enter (`x` permission).

`-perm NNNN`
- This will match any files that have exactly the `NNNN` permission. For example, `-perm 0664` will match any files which the user and group can read and write to and which others can read (or `rw-rw-r--`).

You can add a `-` before `NNNN` to check for files that have at least the permission specified. For example, `-perm -644` would match files that have at least `644` (`rw-r—​r--`) permissions. This includes a file with `664` (`rw-rw-r--`) or even `775` (`rwxrwx-r-x`).

`-empty`
- Will match empty files and directories.

`-size N`
- Will match any files of `N` size, where `N` by default is a number of 512-byte blocks. You can add suffixes to `N` for other units: `Nc` will count the size in bytes, `Nk` in kibibytes (KiB, multiples of 1024 bytes), `NM` in mebibytes (MiB, multiples of 1024 * 1024) and `NG` for gibibytes (GiB, multiples of 1024 * 1024 * 1024).

Again, you can add the `+` or `-` prefixes (here meaning bigger than and smaller than) to search for relative sizes. For example, `-size -10M` will match any file less than 10 MiB in size.

For example, to search for files under your home directory which contain the case insensitive pattern `report` in any part of the name, have `0644` permissions, have been accessed 10 days ago and which size is at least 1 Mib, you could use

```
$ find ~ -iname "*report*" -perm 0644 -atime 10 -size +1M
```

### Searching by Time
Besides searching for attributes you can also perform searches by time, finding files that were accessed, had their attributes changed or were modified during a specific period of time. The parameters are:

`-amin N, -cmin N, -mmin N`
- This will match files that have been accessed, had attributes changed or were modified (respectively) `N` minutes ago.

`-atime N, -ctime N, -mtime N`
- This will match files that were accessed, had attributes changed or were modified `N*24` hours ago.

For `-cmin N` and `-ctime N`, any attribute change will cause a match, including a change in permissions, reading or writing to the file. This makes these parameters especially powerful, since practically any operation involving the file will trigger a match.

The following example would match any file in the current directory which has been modified less than 24 hours ago and is bigger than 100 MiB:

```
$ find . -mtime -1 -size +100M
```

### Using `locate` and `updatedb`
`locate` and `updatedb` are commands that can be used to quickly find a file matching a given pattern on a Linux system. But unlike `find`, `locate` will not search the filesystem for the pattern: instead, it looks it up on a database built by running the `updatedb` command. This gives you very quick results, but they may be imprecise depending on when the database was last updated.

The simplest way to use `locate` is to just give it a pattern to search for. For example, to find every JPEG image on your system, you would use `locate jpg`. The list of results can be quite lengthy, but should look like this:

```
$ locate jpg
/home/carol/Downloads/Expert.jpg
/home/carol/Downloads/Hotbit.jpg
/home/carol/Downloads/Mate1.jpg
/home/carol/Downloads/Mate2.jpg
/home/carol/Downloads/Mate3.jpg
/home/carol/Downloads/Pentaro.jpg
/home/carol/Downloads/Sala.jpg
/home/carol/Downloads/pixel_3a_seethrough_1.jpg
/home/carol/Downloads/jpg_specs.doc
```

When asked for the `jpg` pattern, `locate` will show anything that contains this pattern, no matter what comes before or after it. You can see an example of this in the file `jpg_specs.doc` in the listing above: it contains the pattern, but the extension is not `jpg`.

```
Tip
Remember that with locate you are matching patterns, not file extensions.
```

By default the pattern is case sensitive. This means that files containing `.JPG` would not be shown since the pattern is in lowercase. To avoid this, pass the `-i` parameter to `locate`. Repeating our previous example:

```
$ locate -i .jpg
/home/carol/Downloads/Expert.jpg
/home/carol/Downloads/Hotbit.jpg
/home/carol/Downloads/Mate1.jpg
/home/carol/Downloads/Mate1_old.JPG
/home/carol/Downloads/Mate2.jpg
/home/carol/Downloads/Mate3.jpg
/home/carol/Downloads/Pentaro.jpg
/home/carol/Downloads/Sala.jpg
/home/carol/Downloads/pixel_3a_seethrough_1.jpg
```

Notice that the file `Mate1_old.JPG`, in bold above, was not present in the previous listing.

You can pass multiple patterns to `locate`, just separate them with spaces. The example below would do a case-insensitive search for any files matching the `zip` and `jpg` patterns:

```
$ locate -i zip jpg
/home/carol/Downloads/Expert.jpg
/home/carol/Downloads/Hotbit.jpg
/home/carol/Downloads/Mate1.jpg
/home/carol/Downloads/Mate1_old.JPG
/home/carol/Downloads/Mate2.jpg
/home/carol/Downloads/Mate3.jpg
/home/carol/Downloads/OPENMSXPIHAT.zip
/home/carol/Downloads/Pentaro.jpg
/home/carol/Downloads/Sala.jpg
/home/carol/Downloads/gbs-control-master.zip
/home/carol/Downloads/lineage-16.0-20190711-MOD-quark.zip
/home/carol/Downloads/pixel_3a_seethrough_1.jpg
/home/carol/Downloads/jpg_specs.doc
```

When using multiple patterns, you can request locate to show only files that match all of them. This is done with the `-A` option. The following example would show any file matching the `.jpg` and the `.zip` patterns:

```
$ locate -A .jpg .zip
/home/carol/Downloads/Pentaro.jpg.zip
```

If you wish to count the number of files that match a given pattern instead of showing their full path you can use the `-c` option. For example, to count the number of `.jpg` files on a system:

```
$ locate -c .jpg
1174
```

One problem with `locate` is that it only shows entries present in the database generated by updatedb (located in `/var/lib/mlocate.db`). If the database is outdated, the output could show files that have been deleted since the last time it was updated. One way to avoid this is to add the `-e` parameter, which will make it check to see if the file still exists before showing it on the output.

Of course, this will not solve the problem of files created after the last database update not showing up. For this you will have to update the database with the command `updatedb`. How long this will take will depend on the amount of files of your disk.

#### Controlling the Behavior of `updatedb`
The behavior of `updatedb` can be controlled by the file `/etc/updatedb.conf`. This is a text file where each line controls one variable. Blank likes are ignored and lines that start with the `#` character are treated as comments.

`PRUNEFS=`
- Any filesystem types indicated after this parameter will not be scanned by `updatedb`. The list of types should be separated by spaces, and the types themselves are case-insensitive, so `NFS` and `nfs` are the same.

`PRUNENAMES=`
- This is a space-separated list of directory names that should not be scanned by `updatedb`.

`PRUNEPATHS=`
- This is a list of path names that should be ignored by `updatedb`. The path names must be separated by spaces and specified in the same way they would be shown by `updatedb` (for example, `/var/spool` `/media`)

`PRUNE_BIND_MOUNTS=`
- This is a simple `yes` or `no` variable. If set to yes bind mounts (directories mounted elsewhere with the `mount --bind` command) will be ignored.

### Finding Binaries, Manual Pages and Source Code
`which` is a very useful command that shows the full path to an executable. For example, if you want to locate the executable for `bash`, you could use:

```
$ which bash
/usr/bin/bash
```

If the `-a` option is added the command will show all pathnames that match the executable. Observe the difference:

```
$ which mkfs.ext3
/usr/sbin/mkfs.ext3

$ which -a mkfs.ext3
/usr/sbin/mkfs.ext3
/sbin/mkfs.ext3
```

```
Tip
To find which directories are in the PATH use the echo $PATH command. This will print (echo) the contents of the variable PATH ($PATH) to your terminal.
```

`type` is a similar command which will show information about a binary, including where it is located and its `type`. Just use type followed by the command name:

```
$ type locate
locate is /usr/bin/locate
```

The `-a` parameter works in the same way as in `which`, showing all pathnames that match the executable. Like so:

```
$ type -a locate
locate is /usr/bin/locate
locate is /bin/locate
```

And the `-t` parameter will show the file type of the command which can be `alias`, `keyword`, `function`, `builtin` or `file`. For example:

```
$ type -t locate
file

$ type -t ll
alias

$ type -t type
type is a built-in shell command
```

The command `whereis` is more versatile and besides binaries can also be used to show the location of man pages or even source code for a program (if available in your system). Just type `whereis` followed by the binary name:

```
$ whereis locate
locate: /usr/bin/locate /usr/share/man/man1/locate.1.gz
```

The results above include binaries (`/usr/bin/locate`) and compressed manual pages (`/usr/share/man/man1/locate.1.gz`).

You can quickly filter the results using commandline switches like `-b`, which will limit them to only the binaries, `-m`, which will limit them to only man pages, or `-s`, which will limit them to only the source code. Repeating the example above, you would get:

```
$ whereis -b locate
locate: /usr/bin/locate

$ whereis -m locate
locate: /usr/share/man/man1/locate.1.gz
```
___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)