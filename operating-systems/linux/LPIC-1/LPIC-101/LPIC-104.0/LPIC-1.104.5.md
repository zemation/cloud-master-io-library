# LPIC-1: Devices, Linux Filesystems, Filesystem Hierarchy Standard

## Lesson 104.5: Manage file permissions and ownership

Being a multi-user system, Linux needs some way to track who owns each file and whether or not a user is allowed to perform actions on a file. This is to ensure the privacy of users who might want to keep the contents of their files confidential, as well as to ensure collaboration by making certain files accessible to multiple users.

This is done through a three-level permissions system. Every file on disk is owned by a user and a user group and has three sets of permissions: one for its owner, one for the group who owns the file and one for everyone else. In this lesson, you will learn how to query the permissions for a file, the meaning of these permissions and how to manipulate them.

### Querying Information about Files and Directories
The command `ls` is used to get a listing of the contents of any directory. In this basic form, all you get are the filenames:

```
$ ls
Another_Directory  picture.jpg  text.txt
```

But there is much more information available for each file, including its type, size, ownership and more. To see this information you must ask `ls` for a “long form” listing, using the `-l` parameter:

```
$ ls -l
total 536
drwxrwxr-x 2 carol carol   4096 Dec 10 15:57 Another_Directory
-rw------- 1 carol carol 539663 Dec 10 10:43 picture.jpg
-rw-rw-r-- 1 carol carol   1881 Dec 10 15:57 text.txt
```

Each column on the output above has a meaning. Let us have a look at the columns relevant for this lesson.

- The first column on the listing shows the file type and permissions. For example, on `drwxrwxr-x`:
  - The first character, `d`, indicates the file type.
  - The next three characters, `rwx`, indicate the permissions for the owner of the file, also referred to as user or `u`.
  - The next three characters, `rwx`, indicate the permissions of the group owning the file, also referred to as `g`.
  - The last three characters, `r-x`, indicate the permissions for anyone else, also known as others or `o`.

```
Tip
It is also common to hear the others permission set referred to as world permissions, as in “Everyone else in the world has these permissions”.
```

- The third and fourth columns show ownership information: respectively the user and group that own the file.
- The seventh and last column shows the file name.

The second column indicates the number of hard links pointing to that file. The fifth column shows the filesize. The sixth column shows the date and time the file was last modified. But these columns are not relevant for the current topic.

### What about Directories?
If you try to query information about a directory using `ls -l`, it will show you a listing of the directory’s contents instead:

```
$ ls -l Another_Directory/
total 0
-rw-r--r-- 1 carol carol 0 Dec 10 17:59 another_file.txt
```

To avoid this and query information about the directory itself, add the `-d` parameter to `ls`:

```
$ ls -l -d Another_Directory/
drwxrwxr-x 2 carol carol 4096 Dec 10 17:59 Another_Directory/
```

### Viewing Hidden Files
The directory listing we have retrieved using ls -l before is incomplete:

```
$ ls -l
total 544
drwxrwxr-x 2 carol carol   4096 Dec 10 17:59 Another_Directory
-rw------- 1 carol carol 539663 Dec 10 10:43 picture.jpg
-rw-rw-r-- 1 carol carol   1881 Dec 10 15:57 text.txt
```

There are three other files in that directory, but they are hidden. On Linux, files whose name starts with a period (`.`) are automatically hidden. To see them we need to add the `-a` parameter to `ls`:

```
$ ls -l -a
total 544
drwxrwxr-x 3 carol carol   4096 Dec 10 16:01 .
drwxrwxr-x 4 carol carol   4096 Dec 10 15:56 ..
drwxrwxr-x 2 carol carol   4096 Dec 10 17:59 Another_Directory
-rw------- 1 carol carol 539663 Dec 10 10:43 picture.jpg
-rw-rw-r-- 1 carol carol   1881 Dec 10 15:57 text.txt
-rw-r--r-- 1 carol carol      0 Dec 10 16:01 .thisIsHidden
```

The file `.thisIsHidden` is simply hidden because its name starts with `.`.

The directories `. and `..` however are special. `.` is a pointer to the current directory. And `..` is a pointer to the parent directory, the one which contains the current one. In Linux, every directory contains at least these two directories.

```
Tip
You can combine multiple parameters for ls (and many other Linux commands). ls -l -a can, for example, be written as ls -la.
```

### Understanding Filetypes
We have already mentioned that the first letter in each output of `ls -l` describes the type of the file. The three most common file types are:

`-` (normal file)
- A file can contain data of any kind and help to manage this data. Files can be modified, moved, copied and deleted.

`d` (directory)
- A directory contains other files or directories and helps to organize the file system. Technically, directories are a special kind of file.

`l` (symbolic link)
- This “file” is a pointer to another file or directory elsewhere in the filesystem.

In addition to these, there are three other file types that you should at least know about, but are out of scope for this lesson:

`b` (block device)
- This file stands for a virtual or physical device, usually disks or other kinds of storage devices, such as the first hard disk which might be represented by `/dev/sda`.

`c` (character device)
- This file stands for a virtual or physical device. Terminals (like the main terminal on `/dev/ttyS0`) and serial ports are common examples of character devices.

`s` (socket)
- Sockets serve as “conduits” passing information between two programs.

```
Warning
Do not alter any of the permissions on block devices, character devices or sockets, unless you know what you are doing. This may prevent your system from working!
```

### Understanding Permissions
In the output of `ls -l` the file permissions are shown right after the filetype, as three groups of three characters each, in the order `r`, `w` and `x`. Here is what they mean. Keep in mind that a dash `-` represents the lack of a permission.

### Permissions on Files
`r`
- Stands for read and has an octal value of `4` (do not worry, we will discuss octals shortly). This means permission to open a file and read its contents.

`w`
- Stands for write and has an octal value of `2`. This means permission to edit or delete a file.

`x`
- Stands for execute and has an octal value of `1`. This means that the file can be run as an executable or script.

So, for example, a file with permissions `rw-` can be read and written to, but cannot be executed.

### Permissions on Directories
`r`
- Stands for read and has an octal value of `4`. This means permission to read the directory’s contents, like filenames. But it does not imply permission to read the files themselves.

`w`
- Stands for write and has an octal value of `2`. This means permission to create or delete files in a directory.

Keep in mind that you cannot make these changes with write permissions alone, but also need execute permission (`x`) to change to the directory.

`x`
- Stands for execute and has an octal value of `1`. This means permission to enter a directory, but not to list its files (for that `r` is needed).

The last bit about directories may sound a bit confusing. Let us imagine, for example, that you have a directory named `Another_Directory`, with the following permissions:

```
$ ls -ld Another_Directory/
d--x--x--x 2 carol carol 4,0K Dec 20 18:46 Another_Directory
```

Also imagine that inside this directory you have a shell script called `hello.sh`:

```
-rwxr-xr-x 1 carol carol 33 Dec 20 18:46 hello.sh
```

If you are the user `carol` and try to list the contents of `Another_Directory`, you will get an error message, as your user lacks read permission for that directory:

```
$ ls -l Another_Directory/
ls: cannot open directory 'Another_Directory/': Permission denied
```

However, the user `carol` does have execute permissions, which means that she can enter the directory. Therefore, the user `carol` can access files inside the directory, as long as she has the correct permissions for the respective file. Let us assume the user has full permissions (`rwx`) for the script `hello.sh`. Then she can run the script, even though she cannot read the contents of the directory containing it if she knows the complete filename:

```
$ sh Another_Directory/hello.sh
Hello LPI World!
```

As we said before, permissions are specified in sequence: first for the owner of the file, then for the owning group, and then for other users. Whenever someone tries to perform an action on the file, the permissions are checked in the same fashion.

First the system checks if the current user owns the file, and if this is true it applies the first set of permissions only. Otherwise, it checks if the current user belongs to the group owning the file. In that case, it applies the second set of permissions only. In any other case, the system will apply the third set of permissions.

This means that if the current user is the owner of the file, only the owner permissions are effective, even if the group or other permissions are more permissive than the owner’s permissions.

### Modifying File Permissions
The command `chmod` is used to modify the permissions for a file, and takes at least two parameters: the first one describes which permissions to change, and the second one points to the file or directory where the change will be made. Keep in mind that only the owner of the file, or the system administrator (root) can change the permissions on a file.

The permissions to change can be described in two different ways, or “modes”.

The first one, called symbolic mode offers fine grained control, allowing you to add or revoke a single permission without modifying others on the set. The other mode, called octal mode, is easier to remember and quicker to use if you wish to set all permission values at once.

Both modes will lead to the same end result. So, for example, the commands:

```
$ chmod ug+rw-x,o-rwx text.txt
```

and

```
$ chmod 660 text.txt
```

will produce exactly the same output, a file with the permissions set:

```
-rw-rw---- 1 carol carol  765 Dec 20 21:25 text.txt
```

Now, let us see how each mode works.

### Symbolic Mode
When describing which permissions to change in symbolic mode the first character(s) indicate(s) whose permissions you will alter: the ones for the user (`u`), for the group (`g`), for others (`o`) and/or for everyone (`a`).

Then you need to tell the command what to do: you can grant a permission (`+`), revoke a permission (`-`), or set it to a specific value (`=`).

Lastly, you specify which permission you wish to act on: read (`r`), write (`w`), or execute (`x`).

For example, imagine we have a file called `text.txt` with the following permission set:

```
$ ls -l text.txt
-rw-r--r-- 1 carol carol 765 Dec 20 21:25 text.txt
```

If you wish to grant write permissions to members of the group owning the file, you would use the `g+w` parameter. It is easier if you think about it this way: “For the group (`g`), grant (`+`) write permissions (`w`)”. So, the command would be:

```
$ chmod g+w text.txt
```

Let us check the result with `ls`:

```
$ ls -l text.txt
-rw-rw-r-- 1 carol carol 765 Dec 20 21:25 text.txt
```

Do you wish to remove read permissions for the owner of the same file? Think about it as: “For the user (`u`), revoke (`-`) read permissions (`r`)”. So the parameter is `u-r`, like so:

```
$ chmod u-r text.txt
$ ls -l text.txt
--w-rw-r-- 1 carol carol 765 Dec 20 21:25 text.txt
```

What if we want to set the permissions exactly as `rw-` for everyone? Then think of it as: “For all (`a`), set exactly (`=`) read (`r`), write (`w`), and no execute (`-`)”. So:

```
$ chmod a=rw- text.txt
$ ls -l text.txt
-rw-rw-rw- 1 carol carol 765 Dec 20 21:25 text.txt
```

Of course, it is possible to modify multiple permissions at the same time. In this case, separate them with a comma (`,`):

```
$ chmod u+rwx,g-x text.txt
$ ls -lh text.txt
-rwxrw-rw- 1 carol carol 765 Dec 20 21:25 text.txt
```

The example above can be read as: “For the user (`u`), grant (`+`) read, write and execute (`rwx`) permissions, for the group (`g`), revoke (`-`) execute permissions (`x`)”.

When run on a directory, `chmod` modifies only the directory’s permissions. `chmod` also has a recursive mode, which is useful for when you want to change the permissions for “all files inside a directory and its subdirectories”. To use this, add the parameter `-R` after the command name, before the permissions to change:

```
$ chmod -R u+rwx Another_Directory/
```

This command can be read as: “Recursively (`-R`), for the user (`u`), grant (`+`) read, write and execute (`rwx`) permissions”.

```
Warning
Be careful and think twice before using the -R switch, as it is easy to change permissions on files and directories which you do not want to change, especially on directories with a large number of files and subdirectories.
```

### Octal Mode
In octal mode, the permissions are specified in a different way: as a three-digit value on octal notation, a base-8 numeric system.

Each permission has a corresponding value, and they are specified in the following order: first comes read (`r`), which is `4`, then write (`w`), which is `2` and last is execute (`x`), represented by `1`. If there is no permission, use the value zero (`0`). So, a permission of `rwx` would be `7` (`4+2+1`) and `r-x` would be `5` (`4+0+1`).

The first of the three digits on the permission set represents the permissions for the user (`u`), the second for the group (`g`) and the third for others (`o`). If we wanted to set the permissions for a file to `rw-rw----`, the octal value would be `660`:

```
$ chmod 660 text.txt
$ ls -l text.txt
-rw-rw---- 1 carol carol 765 Dec 20 21:25 text.txt
```

Besides this, the syntax in octal mode is the same as in symbolic mode, the first parameter represents the permissions you wish to change, and the second parameter points to the file or directory where the change will be made.

```
Tip
If a permission value is odd, the file surely is executable!
```

Which syntax should you use? The octal mode is recommended if you want to change the permissions to a specific value, for example `640` (`rw- r-- ---`).

The symbolic mode is more useful if you want to flip just a specific value, regardless of the current permissions for the file. For example, you can add execute permissions for the user using just `chmod u+x script.sh` without regard to, or even touching, the current permissions for the group and others.

### Modifying File Ownership
The command `chown` is used to modify the ownership of a file or directory. The syntax is quite simple:

```
chown USERNAME:GROUPNAME FILENAME
```

For example, let us check a file called `text.txt`:

```
$ ls -l text.txt
-rw-rw---- 1 carol carol 1881 Dec 10 15:57 text.txt
```

The user who owns the file is `carol`, and the group is also `carol`. Now, we will change the group owning the file to some other group, like `students`:

```
$ chown carol:students text.txt
$ ls -l text.txt
-rw-rw---- 1 carol students 1881 Dec 10 15:57 text.txt
```

Keep in mind that the user who owns a file does not need to belong to the group who owns a file. In the example above, the user `carol` does not need to be a member of the `students` group.

The user or group permission set can be omitted if you do not wish to change them. So, to change just the group owning a file you would use `chown :students text.txt`. To change just the user, the command would be `chown carol: text.txt` or just `chown carol text.txt`. Alternatively, you could use the command `chgrp students text.txt`.

Unless you are the system administrator (root), you cannot change ownership of a file to another user or group you do not belong to. If you try to do this, you will get the error message `Operation not permitted`.

### Querying Groups
Before changing the ownership of a file, it might be useful to know which groups exist on the system, which users are members of a group and to which groups a user belongs.

To see which groups exist on your system, type `getent group`. The output will be similar to this one (the output has been abbreviated):

```
$ getent group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,rigues
tty:x:5:rigues
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:rigues
```

If you want to know to which groups a user belongs, add the username as a parameter to `groups`:

```
$ groups carol
carol : carol students cdrom sudo dip plugdev lpadmin sambashare
```

To do the reverse (see which users belong to a group) use `groupmems`. The parameter `-g` specifies the group, and `-l` will list all of its members:

```
# groupmems -g cdrom -l
carol
```

```
Tip
groupmems can only be run as root, the system administrator. If you are not currently logged in as root, add sudo before the command.
```

### Default Permissions
Let us try an experiment. Open a terminal window and create an empty file with the following command:

```
$ touch testfile
```

Now, let us take a look at the permissions for this file. They may be different on your system, but let us assume they look like the following:

```
$ ls -lh testfile
-rw-r--r-- 1 carol carol 0 jul 13 21:55 testfile
```

The permissions are `rw-r—​r--`: read and write for the user, and read for the group and others, or `644` in octal mode. Now, try creating a directory:

```
$ mkdir testdir
$ ls -lhd testdir
drwxr-xr-x 2 carol carol 4,0K jul 13 22:01 testdir
```

Now the permissions are `rwxr-xr-x` : read, write and execute for the user, read and execute for the group and others, or `755` in octal mode.

No matter where you are in the filesystem every file or directory you create will get those same permissions. Did you ever wonder where they come from?

They come from the user mask or `umask`, which sets the default permissions for every file created. You can check the current values with the `umask` command:

```
$ umask
0022
```

But that does not look like `rw-r—​r--`, or even `644`. Maybe we should try with the `-S` parameter, to get an output in symbolic mode:

```
$ umask -S
u=rwx,g=rx,o=rx
```

Those are the same permissions our test directory got in one of the examples above. But, why is it that when we created a file the permissions were different?

Well, it makes no sense to set global execute permissions for everyone on any file by default, right? Directories need execute permissions (otherwise you cannot enter them), but files do not, so they do not get them. Hence the `rw-r—​r--`.

Besides displaying the default permissions, `umask` can also be used to change them for your current shell session. For example, if we use the command:

```
$ umask u=rwx,g=rwx,o=
```

Every new directory will inherit the permissions `rwxrwx---`, and every file `rw-rw----` (as they do not get execute permissions). If you repeat the examples above to create a `testfile` and `testdir` and check the permissions, you should get:

```
$ ls -lhd test*
drwxrwx--- 2 carol carol 4,0K jul 13 22:25 testdir
-rw-rw---- 1 carol carol    0 jul 13 22:25 testfile
```

And if you check the `umask` without the `-S` (symbolic mode) parameter, you get:

```
$ umask
0007
```

The result does not look familiar because the values used are different. Here is a table with every value and its respective meaning:

Value |	Permission for Files | Permission for Directories
--- | --- | ---
0 | rw- | rwx
1 | rw- | rw-
2 | r-- | r-x
3 | r-- | r--
4 | -w- | -wx
5 | -w- | -w-
6 | \--- | --x
7 | \--- | \---

As you can see, `007` corresponds to `rwxrwx---`, exactly as we requested. The leading zero can be ignored.

### Special Permissions
Besides the read, write and execute permissions for user, group and others, each file can have three other special permissions which can alter the way a directory works or how a program runs. They can be specified either in symbolic or octal mode, and are as follows:

### Sticky Bit
The sticky bit, also called the restricted deletion flag, has the octal value `1` and in symbolic mode is represented by a `t` within the other’s permissions. This applies only to directories, and has no effect on normal files. On Linux it prevents users from removing or renaming a file in a directory unless they own that file or directory.

Directories with the sticky bit set show a `t` replacing the `x` on the permissions for others on the output of `ls -l`:

```
$ ls -ld Sample_Directory/
drwxr-xr-t 2 carol carol 4096 Dec 20 18:46 Sample_Directory/
```

In octal mode, the special permissions are specified using a 4-digit notation, with the first digit representing the special permission to act upon. For example, to set the sticky bit (value `1`) for the directory `Another_Directory` in octal mode, with permissions `755`, the command would be:

```
$ chmod 1755 Another_Directory
$ ls -ld Another_Directory
drwxr-xr-t 2 carol carol 4,0K Dec 20 18:46 Another_Directory
```

### Set GID
Set GID, also known as SGID or Set Group ID bit, has the octal value `2` and in symbolic mode is represented by an `s` on the group permissions. This can be applied to executable files or directories. On files, it will make the process run with the privileges of the group who owns the file. When applied to directories, it will make every file or directory created under it inherit the group from the parent directory.

Files and directories with SGID bit show an `s` replacing the `x` on the permissions for the group on the output of `ls -l`:

```
$ ls -l test.sh
-rwxr-sr-x 1 carol root 33 Dec 11 10:36 test.sh
```

To add SGID permissions to a file in symbolic mode, the command would be:

```
$ chmod g+s test.sh
$ ls -l test.sh
-rwxr-sr-x 1 carol root     33 Dec 11 10:36 test.sh
```

The following example will help you better understand the effects of SGID on a directory. Suppose we have a directory called `Sample_Directory`, owned by the user `carol` and the group `users`, with the following permission structure:

```
$ ls -ldh Sample_Directory/
drwxr-xr-x 2 carol users 4,0K Jan 18 17:06 Sample_Directory/
```

Now, let us change to this directory and, using the command `touch`, create an empty file inside of it. The result would be:

```
$ cd Sample_Directory/
$ touch newfile
$ ls -lh newfile
-rw-r--r-- 1 carol carol 0 Jan 18 17:11 newfile
```

As we can see, the file is owned by the user `carol` and group `carol`. But, if the directory had the SGID permission set, the result would be different. First, let us add the SGID bit to the `Sample_Directory` and check the results:

```
$ sudo chmod g+s Sample_Directory/
$ ls -ldh Sample_Directory/
drwxr-sr-x 2 carol users 4,0K Jan 18 17:17 Sample_Directory/
```

The `s` on the group permissions indicates that the SGID bit is set. Now, we will change to this directory and, again, create an empty file with the `touch` command:

```
$ cd Sample_Directory/
$ touch emptyfile
$ ls -lh emptyfile
 -rw-r--r-- 1 carol users 0 Jan 18 17:20 emptyfile
```

The group who owns the file is `users`. This is because the SGID bit made the file inherit the group owner of its parent directory, which is `users`.

### Set UID
SUID, also known as Set User ID, has the octal value `4` and is represented by an `s` on the user permissions in symbolic mode. It only applies to files and has no effect on directories. Its behavior is similar to the SGID bit, but the process will run with the privileges of the user who owns the file. Files with the SUID bit show a `s` replacing the `x` on the permissions for the user on the output of `ls -l`:

```
$ ls -ld test.sh
-rwsr-xr-x 1 carol carol 33 Dec 11 10:36 test.sh
```

You can combine multiple special permissions on one parameter. So, to set SGID (value `2`) and SUID (value `4`) in octal mode for the script `test.sh` with permissions `755`, you would type:

```
$ chmod 6755 test.sh
```

And the result would be:

```
$ ls -lh test.sh
-rwsr-sr-x 1 carol carol 66 Jan 18 17:29 test.sh
```

```
Tip
If your terminal supports color, and these days most of them do, you can quickly see if these special permissions are set by glancing at the output of ls -l. For the sticky bit, the directory name might be shown in a black font with blue background. The same applies for files with the SGID (yellow background) and SUID (red background) bits. Colors may be different depending on which Linux distribution and terminal settings you use.
```




___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)