# LPIC-1: GNU and Unix Commands

## Lesson 103.3: Perform basic file management
Everything in Linux is a file, so knowing how to manipulate them is very important. In this lesson, we shall cover basic operations on files.

In general, as a Linux user, you will be called upon to navigate through the file system, copy files from one location to another and delete files. We shall also cover the commands associated with file management.

A file is an entity that stores data and programs. It consists of content and meta data (file size, owner, creation date, permissions). Files are organized in directories. A directory is a file that stores other files.

The different types of files include:

Regular files
- which store data and programs.

Directories
- which contain other files.

Special files
- which are used for input and output.

Of course, other kinds of files exist but are beyond the scope of this lesson. Later, we shall discuss how to identify these different file types.

### Manipulating Files

### Using `ls` to List Files
The `ls` command is one of the most important command line tools you should learn in order to navigate the file system.

In its basic form, `ls` will list file and directory names only:

```
$ ls
Desktop Downloads   emp_salary  file1   Music   Public  Videos
Documents   emp_name    examples.desktop    file2   Pictures    Templates
```

When used with `-l` , referred to as “long listing” format, it shows file or directory permissions, owner, size, modified date, time and name:

```
$ ls -l
total 60
drwxr-xr-x  2   frank   frank   4096    Apr 1   2018    Desktop
drwxr-xr-x  2   frank   frank   4096    Apr 1   2018    Documents
drwxr-xr-x  2   frank   frank   4096    Apr 1   2018    Downloads
-rw-r--r--  1   frank   frank     21    Sep 7   12:59   emp_name
-rw-r--r--  1   frank   frank     20    Sep 7   13:03   emp_salary
-rw-r--r--  1   frank   frank   8980    Apr 1   2018    examples.desktop
-rw-r--r--  1   frank   frank     10    Sep 1   2018    file1
-rw-r--r--  1   frank   frank     10    Sep 1   2018    file2
drwxr-xr-x  2   frank   frank   4096    Apr 1   2018    Music
drwxr-xr-x  2   frank   frank   4096    Apr 1   2018    Pictures
drwxr-xr-x  2   frank   frank   4096    Apr 1   2018    Public
drwxr-xr-x  2   frank   frank   4096    Apr 1   2018    Templates
drwxr-xr-x  2   frank   frank   4096    Apr 1   2018    Videos
```

The first character in the output indicates the file type:

`-`
- for a regular file.

`d`
- for a directory.

`c`
- for a special file.

To show the file sizes in a human readable format add the option `-h`:

```
$ ls -lh
total 60K
drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Desktop
drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Documents
drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Downloads
-rw-r--r--  1   frank   frank     21    Sep 7   12:59   emp_name
-rw-r--r--  1   frank   frank     20    Sep 7   13:03   emp_salary
-rw-r--r--  1   frank   frank   8.8K    Apr 1   2018    examples.desktop
-rw-r--r--  1   frank   frank     10    Sep 1   2018    file1
-rw-r--r--  1   frank   frank     10    Sep 1   2018    file2
drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Music
drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Pictures
drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Public
drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Templates
drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Videos
```

To list all files including hidden files (those starting with `.`) use the option `-a`:

```
$ ls -a
.               .dbus   file1   .profile
..              Desktop file2   Public
.bash_history   .dmrc  .gconf  .sudo_as_admin_successful
```
Configuration files such as `.bash_history` which are by default hidden are now visible.

In general, the `ls` command syntax is given by:

```
ls OPTIONS FILE
```

Where `OPTIONS` are any of the options shown previously (to view all the possible options run man `ls`), and `FILE` is the name of the file or directory’s details you wish to list.

```
Note
When FILE is not specified, the current directory is implied.
```

### Creating, Copying, Moving and Deleting Files
#### Creating files with `touch`
The `touch` command is the easiest way to create new, empty files. You can also use it to change the timestamps (i.e., modification time) of existing files and directories. The syntax for using `touch` is:

```
touch OPTIONS FILE_NAME(S)
```

Without any options, `touch` would create new files for any file names that are supplied as arguments, provided that files with such names do not already exist. `touch` can create any number of files simultaneously:

```
$ touch file1 file2 file3
```

This would create three new empty files named `file1`, `file2` and `file3`.

Several `touch` options are specifically designed to allow the user to change the timestamps for files. For example, the `-a` option changes only the access time, while the `-m` option changes only the modification time. The use of both options together changes the access and also the modification times to the current time:

```
$ touch -am file3
```

#### Copying Files with `cp`
As a Linux user, you will often copy files from one location to another. Whether it is moving a music file from one directory to another or a system file, use `cp` for all copy tasks:

```
$ cp file1 dir2
```

This command can be literally interpreted as copy `file1` into directory `dir2`. The result is the presence of `file1` inside `dir2`. For this command to be executed successfully `file1` should be existent in the user’s current directory. Otherwise, the system reports an error with the message `No such file or directory`.

```
$ cp dir1/file1 dir2
```

In this case, observe that the path to `file1` is more explicit. The source path can be expressed either as a relative or absolute path. Relative paths are given in reference to a specific directory, while absolute paths are not given with a reference. Below we shall further clarify this notion.

For the moment, just observe that this command copies `file1` into the directory `dir2`. The path to `file1` is given with more detail since the user is currently not located in `dir1`.

```
$ cp /home/frank/Documents/file2 /home/frank/Documents/Backup
```

In this third case, `file2` located at `/home/frank/Documents` is copied into the directory `/home/frank/Documents/Backup`. The source path provided here is absolute. In the two examples above, the source paths are relative. When a path starts with the character `/` it is an absolute path, otherwise it is a relative path.

The general syntax for `cp` is:

```
cp OPTIONS SOURCE DESTINATION
```

`SOURCE` is the file to copy and `DESTINATION` the directory into which the file would be copied. `SOURCE` and `DESTINATION` can be specified either as absolute or relative paths.

#### Moving Files with `mv`
Just like `cp` for copying, Linux provides a command for moving and renaming files. It is called `mv`.

The move operation is analogue to the cut and paste operation you generally perform through a Graphical User Interface (GUI).

If you wish to move a file into a new location, use `mv` in the following way:

```
mv FILENAME DESTINATION_DIRECTORY
```

Here is an example:

```
$ mv myfile.txt /home/frank/Documents
```

The result is that `myfile.txt` is moved into destination `/home/frank/Documents`.

To rename a file, `mv` is used in the following way:

```
$ mv old_file_name new_file_name
```

This changes the name of the file from `old_file_name` to `new_file_name`.

By default, `mv` would not seek your confirmation (technically said “would not prompt”) if you wish to overwrite (rename) an existing file. However, you can allow the system to prompt, by using the option `-i`:

```
$ mv -i old_file_name new_file_name
mv: overwrite 'new_file_name'?
```

This command would ask the user’s permission before overwriting `old_file_name` to `new_file_name`.

Conversely, using the `-f`:

```
$ mv -f old_file_name new_file_name
```

would forcefully overwrite the file, without asking any permission.

#### Deleting Files with `rm`
`rm` is used to delete files. Think of it as an abbreviated form of the word “remove”. Note that the action of removing a file is usually irreversible thus this command should be used with caution.

```
$ rm file1
```

This command would delete `file1`.

```
$ rm -i file1
rm: remove regular file 'file1'?
```

This command would request the user for confirmation before deleting `file1`. Remember, we saw the `-i` option when using `mv` above.

```
$ rm -f file1
```

This command forcefully deletes `file1` without seeking your confirmation.

Multiple files can be deleted at the same time:

```
$ rm file1 file2 file3
```

In this example `file1`, `file2` and `file3` are deleted simultaneously.

The syntax for `rm` is generally given by:

```
rm OPTIONS FILE
```

### Creating and Deleting Directories
### Creating Directories with mkdir
Creating directories is critical to organizing your files and folders. Files may be grouped together in a logical way by keeping them inside a directory. To create a directory, use `mkdir`:

```
mkdir OPTIONS DIRECTORY_NAME
```

where `DIRECTORY_NAME` is the name of the directory to be created. Any number of directories can be created simultaneously:

```
$ mkdir dir1
```

would create the directory `dir1` in the user’s current directory.

```
$ mkdir dir1 dir2 dir3
```

The preceding command would create three directories `dir1`, `dir2` and `dir3` at the same time.

To create a directory together with its subdirectories use the option `-p` (“parents”):

```
$ mkdir -p parents/children
```

This command would create the directory structure `parents/children`, i.e. it would create the directories `parents` and `children`. `children` would be located inside `parents`.

### Removing Directories with rmdir
`rmdir` deletes a directory if it is empty. Its syntax is given by:

```
rmdir OPTIONS DIRECTORY
```

where `DIRECTORY` could be a single argument or a list of arguments.

```
$ rmdir dir1
```

This command would delete `dir1`.

```
$ rmdir dir1 dir2
```

This command would simultaneously delete `dir1` and `dir2`.

You may remove a directory with its subdirectory:

```
$ rmdir -p parents/children
```

This would remove the directory structure `parents/children`. Note that if any of the directories are not empty, they will not be deleted.

### Recursive Manipulation of Files and Directories
To manipulate a directory and its contents, you need to apply recursion. Recursion means, do an action and repeat that action all down the directory tree. In Linux, the options `-r` or `-R` or `--recursive` are generally associated with recursion.

The following scenario would help you better understand recursion:

You list the contents of a directory `students`, which contains two subdirectories `level 1` and `level 2` and the file named `frank`. By applying recursion, the `ls` command would list the content of `students` i.e. `level 1`, `level 2` and `frank`, but would not end there. It would equally enter subdirectories level 1 and level 2 and list their contents and so on down the directory tree.

### Recursive Listing with ls -R
`ls -R` is used to list the contents of a directory together with its subdirectories and files.

```
$ ls -R mydirectory
mydirectory/:
file1   newdirectory

mydirectory/newdirectory:
```

In the listing above, `mydirectory` including all its content are listed. You can observe `mydirectory` contains the subdirectory `newdirectory` and the file `file1`. `newdirectory` is empty that is why no content is shown.

In general, to list the contents of a directory including its subdirectories, use:

```
ls -R DIRECTORY_NAME
```

Adding a trailing slash to `DIRECTORY_NAME` has no effect:

```
$ ls -R animal
```

is similar to

```
$ ls -R animal/
```

### Recursive Copy with cp -r
`cp -r` (or `-R` or `--recursive`) allows you to copy a directory together with its all subdirectories and files.

```
$ tree mydir
mydir
|_file1
|_newdir
    |_file2
    |_insidenew
        |_lastdir


3 directories, 2 files
$ mkdir newcopy
$ cp mydir newcopy
cp: omitting directory 'mydir'
$ cp -r mydir newcopy
* tree newcopy
newcopy
|_mydir
    |_file1
    |_newdir
        |_file2
        |_insidenew
            |_lastdir

4 directories, 2 files
```

In the listing above, we observe that trying to copy `mydir` into `newcopy`, using `cp` without `-r`, the system displays the message `cp: omitting directory 'mydir'`. However, by adding the option `-r` all the contents of `mydir` including itself are copied into `newcopy`.

To copy directories and subdirectories use:

```
cp -r SOURCE DESTINATION
```

### Recursive Deletion with rm -r
`rm -r` will remove a directory and all its contents (subdirectories and files).

```
Warning
Be very careful with either the -r or the option combination of -rf when used with the rm command. A recursive remove command on an important system directory could render the system unusable. Employ the recursive remove command only when absolutely certain that the contents of a directory are safe to remove from a computer.
```

In trying to delete a directory without using -r the system would report an error:

```
$ rm newcopy/
rm: cannot remove 'newcopy/': Is a directory
$ rm -r newcopy/
```

You have to add `-r` as in the second command for the deletion to take effect.

```
Note
You may be wondering why we do not use rmdir in this case. There is a subtle difference between the two commands. rmdir would succeed in deleting only if the given directory is empty whereas rm -r can be used irrespective of whether this directory is empty or not.
```

Add the option `-i` to seek confirmation before the file is deleted:

```
$ rm -ri mydir/
rm: remove directory 'mydir/'?
```
The system prompts before trying to delete `mydir`.

### File Globbing and Wildcards
File globbing is a feature provided by the Unix/Linux shell to represent multiple filenames by using special characters called wildcards. Wildcards are essentially symbols which may be used to substitute for one or more characters. They allow, for example, to show all files that start with the letter A or all files that end with the letters `.conf`.

Wildcards are very useful as they can be used with commands such as `cp`, `ls` or `rm`.

The following are some examples of file globbing:

`rm *`
- Delete all files in current working directory.

`ls l?st`
- List all files with names beginning with `l` followed by any single character and ending with `st`.

`rmdir [a-z]*`
- Remove all directories whose name starts with a letter.

#### Types of Wildcards
There are three characters that can be used as wildcards in Linux:

`*` (asterisk)
- which represents zero, one or more occurrences of any character.

`?` (question mark)
- which represents a single occurrence of any character.

`[ ]` (bracketed characters)
which represents any occurrence of the character(s) enclosed in the square brackets. It is possible to use different types of characters whether numbers, letters, other special characters. For example, the expression `[0-9]` matches all digits.

# The Asterisk
An asterisk (`*`) matches zero, one or more occurrences of any character.

For example:

```
$ find /home -name *.png
```

This would find all files that end with `.png` such as `photo.png`, `cat.png`, `frank.png`. The `find` command will be explored further in a following lesson.

Similarly:

```
$ ls lpic-*.txt
```

would list all text files that start with the characters `lpic-` followed by any number of characters and end with `.txt`, such as `lpic-1.txt` and `lpic-2.txt`.

The asterisk wildcard can be used to manipulate (copy, delete or move) all the contents of a directory:

```
$ cp -r animal/* forest
```

In this example, all the contents of `animal` is copied into `forest`.

In general to copy all the contents of a directory we use:

```
cp -r SOURCE_PATH/* DEST_PATH
```

where `SOURCE_PATH` can be omitted if we are already in the required directory.

The asterisk, just as any other wildcard, could be used repeatedly in the same command and at any position:

```
$ rm *ate* 
```

Filenames prefixed with zero, one or more occurrence of any character, followed by the letters `ate` and ending with zero, one or more occurrence of any character will be removed.

### The Question Mark
The question mark (`?`) matches a single occurrence of a character.

Consider the listing:

```
$ ls
last.txt    lest.txt    list.txt    third.txt   past.txt
```

To return only the files that start with `l` followed by any single character and the characters `st.txt`, we use the question mark (`?`) wildcard:

```
$ ls l?st.txt
last.txt    lest.txt    list.txt
```

Only the files `last.txt`, `lest.txt` and `list.txt` are displayed as they match the given criteria.

Similarly,

```
$ ls ??st.txt
last.txt    lest.txt    list.txt    past.txt
```

output files that are prefixed with any two characters followed by the text `st.txt`.

### Bracketed Characters
The bracketed wildcards matches any occurrence of the character(s) enclosed in the square brackets:

```
$ ls l[aef]st.txt
last.txt    lest.txt
```

This command would list all files starting with `l` followed by any of the characters in the set `aef` and ending with `st.txt`.

The square brackets could also take ranges:

```
$ ls l[a-z]st.txt
last.txt    lest.txt    list.txt
```

This outputs all files with names starting with l followed by any lower case letter in the range a to z and ending with st.txt.

Multiple ranges could also be applied in the square brackets:

```
$ ls
student-1A.txt  student-2A.txt  student-3.txt
$ ls student-[0-9][A-Z].txt
student-1A.text student-2A.txt
```

The listing shows a school directory with a list of registered students. To list only those students whose registration numbers meet the following criteria:

- begin with `student-`
- followed by a number, and an uppercase character
- and end with .txt

### Combining Wildcards
Wildcards can be combined as in:

```
$ ls
last.txt    lest.txt    list.txt    third.txt   past.txt
$ ls [plf]?st* 
last.txt    lest.txt    list.txt    past.txt
```

The first wildcard component ([`plf`]) matches any of the characters `p`, `l` or `f`. The second wildcard component (`?`) matches any single character. The third wildcard component (`*`) matches zero, one or many occurrences of any character.

```
$ ls
file1.txt file.txt file23.txt fom23.txt
$ ls f*[0-9].txt
file1.txt file23.txt fom23.txt
```

The previous command displays all files that begin with the letter `f`, followed by any set of letters, at least one occurrence of a digit and ends with `.txt`. Note that `file.txt` is not displayed as it does not match this criteria.
___

### How to Find Files
As you use your machine, files progressively grow in number and size. Sometimes it becomes difficult to locate a particular file. Fortunately, Linux provides find to quickly search and locate files. `find` uses the following syntax:

```
find STARTING_PATH OPTIONS EXPRESSION
```

`STARTING_PATH`
- defines the directory where the search begins.

`OPTIONS`
- controls the behavior and adds specific criteria to optimize the search process.

`EXPRESSION`
- defines the search query.

```
$ find . -name "myfile.txt"
./myfile.txt
```

The starting path in this case is the current directory. The option `-name` specifies that the search is based on the name of the file. `myfile.txt` is the name of the file to search. When using file globbing, be sure to include the expression in quotation marks:

```
$ find /home/frank -name "*.png"
/home/frank/Pictures/logo.png
/home/frank/screenshot.png
```

This command finds all files ending with `.png` starting from `/home/frank/` directory and beneath. If you do not understand the usage of the asterisk (`*`), it is covered in the previous lesson.

### Using Criteria to Speed Search
Use `find` to locate files based on type, size or time. By specifying one or more options, the desired results are obtained in less time.

Switches to finding files based on type include:

`-type f`
- file search.

`-type d`
- directory search.

`-type l`
- symbolic link search.

```
$ find . -type d -name "example"
```

This command finds all directories in the current directory and below, that have the name `example`.

Other criteria which could be used with `find` include:

`-name`
- performs a search based on the given name.

`-iname`
- searches based on the name, however, the case is not important (i.e. the test case myFile is similar to MYFILE).

`-not`
- returns those results that do not match the test case.

`-maxdepth N`
- searches the current directory as well as subdirectories N levels deep.

### Locating Files by Modification Time
`find` also allows to filter a directory hierarchy based on when the file was modified:

```
$ sudo find / -name "*.conf" -mtime 7
/etc/logrotate.conf
```

This command would search for all files in the entire file system (the starting path is the root directory, i.e. `/`) that end with the characters `.conf` and have been modified in the last seven days. This command would require elevated privileges to access directories starting at the base of the system’s directory structure, hence the use of `sudo` here. The argument passed to `mtime` represents the number of days since the file was last modified.

### Locating Files by Size
`find` can also locate files by size. For example, searching for files larger than `2G` in `/var`:

```
$ sudo find /var -size +2G
/var/lib/libvirt/images/debian10.qcow2
/var/lib/libvirt/images/rhel8.qcow2
```

The `-size` option displays files of sizes corresponding to the argument passed. Some example arguments include:

`-size 100b`
- files which are exactly 100 bytes.

`-size +100k`
- files taller than 100 kilobytes.

`-size -20M`
- files smaller than 20 megabytes.

`-size +2G`
- files larger than 2 gigabytes.

```
Note
To find empty files we can use: find . -size 0b  or  find . -empty.
```

### Acting on the Result Set
Once a search is done, it is possible to perform an action on the resulting set by using `-exec`:

```
$ find . -name "*.conf" -exec chmod 644 '{}' \;
```

This filters every object in the current directory (`.`) and below for file names ending with `.conf` and then executes the `chmod 644` command to modify file permissions on the results.

For now, do not bother with the meaning of `'{}' \`; as it will be discussed later.

### Using `grep` to Filter for Files Based on Content
`grep` is used to search for the occurrence of a keyword.

Consider a situation where we are to find files based on content:

```
$ find . -type f -exec grep "lpi" '{}' \; -print
./.bash_history
Alpine/M
helping/M
```

This would search every object in the current directory hierarchy (`.`) that is a file (`-type f`) and then executes the command `grep "lpi"` for every file that satisfies the conditions. The files that match these conditions are printed on the screen (`-print`). The curly braces (`{}`) are a placeholder for the `find` match results. The `{}` are enclosed in single quotes (`'`) to avoid passing `grep` files with names containing special characters. The `-exec` command is terminated with a semicolon (`;`), which should be escaped (`\;`) to avoid interpretation by the shell.

Adding the option `-delete` to the end of an expression would delete all files that match. This option should be used when you are certain that the results only match the files that you wish to delete.

In the example below, `find` locates all files in the hierarchy starting at the current directory then deletes all files that end with the characters `.bak`:

```
$ find . -name "*.bak" -delete
```

### Archiving Files
### The `tar` Command (Archiving and Compresssion)
The `tar` command, short for “tape archive(r)”, is used to create tar archives by converting a group of files into an archive. Archives are created so as to easily move or backup a group of files. Think of `tar` as a tool that creates a glue onto which files can be attached, grouped and easily moved.

`tar` also has the ability to extract tar archives, display a list of the files included in the archive as well as add additional files to an existing archive.

The `tar` command syntax is as follows:

```
tar [OPERATION_AND_OPTIONS] [ARCHIVE_NAME] [FILE_NAME(S)]
```

`OPERATION`
- Only one operation argument is allowed and required. The most frequently used operations are:

  - `--create (-c)`
    - Create a new tar archive.
  - `--extract (-x)`
    - Extract the entire archive or one or more files from an archive.
  - `--list (-t)`
    - Display a list of the files included in the archive.

`OPTIONS`
- The most frequently used options are:
  - `--verbose (-v)`
    - Show the files being processed by the `tar` command.
  - `--file=archive-name (-f archive-name)`
    - Specifies the archive file name.

`ARCHIVE_NAME`
- The name of the archive.

`FILE_NAME(S)`
- A space-separated list of file names to be extracted. If not provided the entire archive is extracted.

### Creating an Archive
Let’s say we have a directory named `stuff` in the current directory and we want to save it to a file named `archive.tar`. We would run the following command:

```
$ tar -cvf archive.tar stuff
stuff/
stuff/service.conf
```

Here’s what those switches actually mean:

`-c`
- Create an archive.

`-v`
- Display progress in the terminal while creating the archive, also known as “verbose” mode. The -v is always optional in these commands, but it is helpful.

`-f`
- Allows to specify the filename of the archive.

In general to archive a single directory or a single file on Linux, we use:

```
tar -cvf NAME-OF-ARCHIVE.tar /PATH/TO/DIRECTORY-OR-FILE
```

```
Note
tar works recursively. It will perform the required action on every subsequent directory inside the directory specified.
```

To archive multiple directories at once, we list all the directories delimiting them by a space in the section `/PATH/TO/DIRECTORY-OR-FILE`:

```
$ tar -cvf archive.tar stuff1 stuff2
```

This would produce an archive of `stuff1` and `stuff2` in `archive.tar`

### Extracting an Archive
We can extract an archive using `tar`:

```
$ tar -xvf archive.tar
stuff/
stuff/service.conf
```

This will extract the contents of `archive.tar` to the current directory.

This command is the same as the archive creation command used above, except the `-x` switch that replaces the `-c` switch.

To extract the contents of the archive to a specific directory we use `-C`:

```
$ tar -xvf archive.tar -C /tmp
```

This will extract the contents of `archive.tar` to the `/tmp` directory.

```
$ ls /tmp
stuff
```

### Compressing with `tar`
The GNU `tar` command included with Linux distributions can create a `.tar` archive and then compress it with `gzip` or `bzip2` compression in a single command:

```
$ tar -czvf name-of-archive.tar.gz stuff
```

This command would create a compressed file using the `gzip` algorithm (`-z`).

While `gzip` compression is most frequently used to create `.tar.gz` or `.tgz` files, `tar` also supports `bzip2` compression. This allows the creation of `bzip2` compressed files, often named `.tar.bz2`, `.tar.bz` or `.tbz` files.

To do so, we replace `-z` for `gzip` with `-j` for `bzip2`:

```
$ tar -cjvf name-of-archive.tar.bz stuff
```

To decompress the file, we replace `-c` with `-x`, where `x` stands for “extract”:

```
$ tar -xzvf archive.tar.gz
```

`gzip` is faster, but it generally compresses a bit less, so you get a somewhat larger file. `bzip2` is slower, but it compresses a bit more, so you get a somewhat smaller file. In general, though, `gzip` and `bzip2` are practically the same thing and both will work similarly.

Alternatively we may apply `gzip` or `bzip2` compression using `gzip` command for `gzip` compressions and the `bzip` command for `bzip` compressions. For example, to apply `gzip` compression, use:

```
gzip FILE-TO-COMPRESS
```

`gzip`
- creates the compressed file with the same name but with a `.gz` ending.

`gzip`
- removes the original files after creating the compressed file.

The `bzip2` command works in a similar fashion.

To uncompress the files we use either `gunzip` or `bunzip2` depending on the algorithm used to compressed a file.

### The `cpio` Command
`cpio` stands for “copy in, copy out”. It is used to process archive files such as `*.cpio` or `*.tar files`.

`cpio` performs the following operations:

- Copying files to an archive.

- Extracting files from an archive.

It takes the list of files from the standard input (mostly output from `ls`).

To create a `cpio` archive, we use:

```
$ ls | cpio -o > archive.cpio
```

The `-o` option instructs `cpio` to create an output. In this case, the output file created is `archive.cpio`. The `ls` command lists the contents of the current directory which are to be archived.

To extract the archive we use :

```
$ cpio -id < archive.cpio
```

The `-i` option is used to perform the extract. The `-d` option would create the destination folder. The character < represents standard input. The input file to be extracted is `archive.cpio`.

### The `dd` Command
`dd` copies data from one location to another. The command line syntax of `dd` differs from many other Unix programs, it uses the syntax `option=value` for its command line options rather than the GNU standard `-option value` or `--option=value` formats:

```
$ dd if=oldfile of=newfile
```

This command would copy the content of `oldfile` into `newfile`, where `if=` is the input file and `of=` refers to the output file.

```
Note
The dd command typically will not output anything to the screen until the command has finished. By providing the status=progress option, the console will display the amount of work getting done by the command. For example: dd status=progress if=oldfile of=newfile.
```

`dd` is also used in changing data to upper/lower case or writing directly to block devices such as `/dev/sdb`:

```
$ dd if=oldfile of=newfile conv=ucase
```

This would copy all the contents of `oldfile` into `newfile` and capitalise all of the text.

The following command will backup the whole hard disk located at `/dev/sda` to a file named `backup.dd`:

```
$ dd if=/dev/sda of=backup.dd bs=4096
```

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)