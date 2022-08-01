# LPIC-1: Devices, Linux Filesystems, Filesystem Hierarchy Standard

### Lesson 104.6: Create and change hard and symbolic links
On Linux some files get a special treatment either because of the place they are stored in, such as temporary files, or the way they interact with the filesystem, like links. In this lesson you will learn what links are and how to manage them.

### Understanding Links
As already mentioned, on Linux everything is treated as a file. But there is a special kind of file, called a link, and there are two types of links on a Linux system:

`Symbolic links`
- Also called soft links, they point to the path of another file. If you delete the file the link points to (called target) the link will still exist, but it “stops working”, as it now points to “nothing”.

`Hard links`
- Think of a hard link as a second name for the original file. They are not duplicates, but instead are an additional entry in the filesystem pointing to the same place (inode) on the disk.

```
Tip
An inode is a data structure that stores attributes for an object (like a file or directory) on a filesystem. Among those attributes are permissions, ownership and on which blocks of the disk the data for the object is stored. Think of it as an entry on an index, hence the name, which comes from “index node”.
```

### Working with Hard Links
#### Creating Hard Links
The command to create a hard link on Linux is `ln`. The basic syntax is:

```
$ ln TARGET LINK_NAME
```

The `TARGET` must exist already (this is the file the link will point to), and if the target is not on the current directory, or if you want to create the link elsewhere, you must specify the full path to it. For example, the command:

```
$ ln target.txt /home/carol/Documents/hardlink
```

will create a file named `hardlink` on the directory `/home/carol/Documents/`, linked to the file `target.txt` on the current directory.

If you leave out the last parameter (`LINK_NAME`), a link with the same name as the target will be created in the current directory.

#### Managing Hard Links
Hard links are entries in the filesystem which have different names but point to the same data on disk. All such names are equal and can be used to refer to a file. If you change the contents of one of the names, the contents of all other names pointing to that file change since all these names point to the very same data. If you delete one of the names, the other names will still work.

This happens because when you “delete” a file the data is not actually erased from the disk. The system simply deletes the entry on the filesystem table pointing to the inode corresponding to the data on the disk. But if you have a second entry pointing to the same inode, you can still get to the data. Think of it as two roads converging on the same point. Even if you block or redirect one of the roads, you can still reach the destination using the other.

You can check this by using the `-i` parameter of `ls`. Consider the following contents of a directory:

```
$ ls -li
total 224
3806696 -r--r--r-- 2 carol carol 111702 Jun  7 10:13 hardlink
3806696 -r--r--r-- 2 carol carol 111702 Jun  7 10:13 target.txt
```

The number before the permissions is the inode number. See that both the file `hardlink` and the file `target.txt` have the same number (`3806696`)? This is because one is a hard link of the other.

But which one is the original and which one is the link? You cannot really tell, as for all practical purposes they are the same.

Note that every hard link pointing to a file increases the link count of the file. This is the number right after the permissions on the output of `ls -l`. By default, every file has a link count of `1` (directories have a count of `2`), and every hard link to it increases the count by one. So, that is the reason for the link count of `2` on the files in the listing above.

In contrast to symbolic links, you can only create hard links to files, and both the link and target must reside in the same filesystem.

#### Moving and Removing Hard Links
Since hard links are treated as regular files, they can be deleted with `rm` and renamed or moved around the filesystem with `mv`. And since a hard link points to the same inode of the target, it can be moved around freely, without fear of “breaking” the link.

### Symbolic Links
#### Creating Symbolic Links
The command used to create a symbolic link is also `ln`, but with the `-s` parameter added. Like so:

```
$ ln -s target.txt /home/carol/Documents/softlink
```

This will create a file named `softlink` in the directory `/home/carol/Documents/`, pointing to the file target.txt on the current directory.

As with hard links, you can omit the link name to create a link with the same name as the target in the current directory.

### Managing Symbolic Links
Symbolic links point to another path in the filesystem. You can create soft links to files and directories, even on different partitions. It is pretty easy to spot a symbolic link with the output of the `ls` command:

```
$ ls -lh
total 112K
-rw-r--r-- 1 carol carol 110K Jun  7 10:13 target.txt
lrwxrwxrwx 1 carol carol   12 Jun  7 10:14 softlink -> target.txt
```

In the example above, the first character on the permissions for the file `softlink` is `l`, indicating a symbolic link. Furthermore, just after the filename you see the name of the target the link points to, the file `target.txt`.

Note that on file and directory listings, soft links themselves always show the permissions `rwx` for the user, the group and others, but in practice the access permissions for them are the same as those for the target.

#### Moving and Removing Symbolic Links
Like hard links, symbolic links can be removed using `rm` and moved around or renamed using `mv`. However, special care should be taken when creating them, to avoid “breaking” the link if it is moved from its original location.

When creating symbolic links you should be aware that unless a path is fully specified the location of the target is interpreted as relative to the location of the link. This may create problems if the link, or the file it points to, is moved.

This is easier to understand with an example. Say you have a file named `original.txt` in the current directory, and you want to create a symbolic link to it called `softlink`. You could use:

```
$ ln -s original.txt softlink
```

And apparently all would be well. Let us check with `ls`:

```
$ ls -lh
total 112K
-r--r--r-- 1 carol carol 110K Jun  7 10:13 original.txt
lrwxrwxrwx 1 carol carol   12 Jun  7 19:23 softlink -> original.txt
```

See how the link is constructed: `softlink` points to (`→`) `original.txt`. However, let’s see what happens if you move the link to the previous directory and try to display its contents using the command `less`:

```
$ mv softlink ../
$ less ../softlink
../softlink: No such file or directory
```

Since the path to `original.txt` was not specified, the system assumes that it is on the same directory as the link. When this is no longer true, the link stops working.

The way to prevent this is to always specify the full path to the target when creating the link:

```
$ ln -s /home/carol/Documents/original.txt softlink
```

This way, no matter where you move the link to it will still work, because it points to the absolute location of the target. Check with `ls`:

```
$ ls -lh
total 112K
lrwxrwxrwx 1 carol carol   40 Jun  7 19:34 softlink -> /home/carol/Documents/original.txt
```


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)