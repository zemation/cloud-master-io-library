# Linux Essentials Section 3

## Lesson 3-1 Archiving Files on the Command Line

Compression is used to reduce the amount of space a specific set of data consumes. Compression is commonly used for reducing the amount of space that is needed to store a file. Another common use is to reduce the amount of data sent over a network connection.

Compression works by replacing repetitive patterns in data. Suppose you have a novel. Some words are extremely common but have multiple characters, such as the word “the”. You could reduce the size of the novel significantly if you were to replace these common multi character words and patterns with single character replacements. E.g., replace “the” with a greek letter that is not used elsewhere in the text. Data compression algorithms are similar to this but more complex.

Compression comes in two varieties, lossless and lossy. Things compressed with a lossless algorithm can be decompressed back into their original form. Data compressed with a lossy algorithm cannot be recovered. Lossy algorithms are often used for images, video, and audio where the quality loss is imperceptible to humans, irrelevant to the context, or the loss is worth the saved space or network throughput.

Archiving tools are used to bundle up files and directories into a single file. Some common uses are backups, bundling software source code, and data retention.

Archive and compression are commonly used together. Some archiving tools even compress their contents by default. Others can optionally compress their contents. A few archive tools must be used in conjunction with stand-alone compression tools if you wish to compress the contents.

The most common tool for archiving files on Linux systems is `tar`. Most Linux distributions ship with the GNU version of `tar`, so it is the one that will be covered in this lesson. `tar` on its own only manages the archiving of files but does not compress them.

There are lots of compression tools available on Linux. Some common lossless ones are `bzip2`, `gzip`, and `xz`. You will find all three on most systems. You may encounter an old or very minimal system where `xz` or `bzip` is not installed. If you become a regular Linux user, you will likely encounter files compressed with all three of these. All three of them use different algorithms, so a file compressed with one tool can’t be decompressed by another. Compression tools have a trade off. If you want a high compression ratio, it will take longer to compress and decompress the file. This is because higher compression requires more work finding more complex patterns. All of these tools compress data but can not create archives containing multiple files.

Stand-alone compression tools aren’t typically available on Windows systems. Windows archiving and compression tools are usually bundled together. Keep this in mind if you have Linux and Windows systems that need to share files.

Linux systems also have tools for handling `.zip` files commonly used on Windows system. They are called `zip` and `unzip`. These tools are not installed by default on all systems, so if you need to use them you may have to install them. Fortunately, they are typically found in distributions' package repositories.

### Compression Tools
How much disk space is saved by compressing files depends on a few factors. The nature of the data you are compressing, the algorithm used to compress the data, and the compression level. Not all algorithms support different compression levels.

Let’s start with setting up some test files to compress:

```
$ mkdir ~/linux_essentials-3.1
$ cd ~/linux_essentials-3.1
$ mkdir compression archiving
$ cd compression
$ cat /etc/* > bigfile 2> /dev/null
```
Now we create three copies of this file:

```
$ cp bigfile bigfile2
$ cp bigfile bigfile3
$ cp bigfile bigfile4
$ ls -lh
total 2.8M
-rw-r--r-- 1 emma emma 712K Jun 23 08:08 bigfile
-rw-r--r-- 1 emma emma 712K Jun 23 08:08 bigfile2
-rw-r--r-- 1 emma emma 712K Jun 23 08:08 bigfile3
-rw-r--r-- 1 emma emma 712K Jun 23 08:08 bigfile4
```
Now we are going to compress the files with each aforementioned compression tool:

```
$ bzip2 bigfile2
$ gzip bigfile3
$ xz bigfile4
$ ls -lh
total 1.2M
-rw-r--r-- 1 emma emma 712K Jun 23 08:08 bigfile
-rw-r--r-- 1 emma emma 170K Jun 23 08:08 bigfile2.bz2
-rw-r--r-- 1 emma emma 179K Jun 23 08:08 bigfile3.gz
-rw-r--r-- 1 emma emma 144K Jun 23 08:08 bigfile4.xz
```
Compare the sizes of the compressed files to the uncompressed file named bigfile. Also notice how the compression tools added extensions to the file names and removed the uncompressed files.

Use `bunzip2`, `gunzip`, or `unxz` to decompress the files:

```
$ bunzip2 bigfile2.bz2
$ gunzip bigfile3.gz
$ unxz bigfile4.xz
$ ls -lh
total 2.8M
-rw-r--r-- 1 emma emma 712K Jun 23 08:20 bigfile
-rw-r--r-- 1 emma emma 712K Jun 23 08:20 bigfile2
-rw-r--r-- 1 emma emma 712K Jun 23 08:20 bigfile3
-rw-r--r-- 1 emma emma 712K Jun 23 08:20 bigfile4
```
Notice again that now the compressed file is deleted once it is decompressed.

Some compression tools support different compression levels. A higher compression level usually requires more memory and CPU cycles, but results in a smaller compressed file. The opposite is true for a lower level. Below is a demonstration with `xz` and `gzip`:

```
$ cp bigfile bigfile-gz1
$ cp bigfile bigfile-gz9
$ gzip -1 bigfile-gz1
$ gzip -9 bigfile-gz9
$ cp bigfile bigfile-xz1
$ cp bigfile bigfile-xz9
$ xz -1 bigfile bigfile-xz1
$ xz -9 bigfile bigfile-xz9
$ ls -lh bigfile bigfile-* *
total 3.5M
-rw-r--r-- 1 emma emma 712K Jun 23 08:08 bigfile
-rw-r--r-- 1 emma emma 205K Jun 23 13:14 bigfile-gz1.gz
-rw-r--r-- 1 emma emma 178K Jun 23 13:14 bigfile-gz9.gz
-rw-r--r-- 1 emma emma 156K Jun 23 08:08 bigfile-xz1.xz
-rw-r--r-- 1 emma emma 143K Jun 23 08:08 bigfile-xz9.xz
```

It is not necessary to decompress a file every time you use it. Compression tools typically come with special versions of common tools used to read text files. For example, `gzip` has a version of `cat`, `grep`, `diff`, `less`, `more`, and a few others. For `gzip`, the tools are prefixed with a `z`, while the prefix `bz` exists for `bzip2` and `xz` exists for `xz`. Below is an example of using `zcat` to read display a file compressed with `gzip`:

```
$ cp /etc/hosts ./
$ gzip hosts
$ zcat hosts.gz
127.0.0.1	localhost

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

### Archiving Tools
The `tar` program is probably the most widely used archiving tool on Linux systems. In case you are wondering why it is named how it is, it as an abbreviation for “tape archive”. Files created with `tar` are often called tar balls. It is very common for applications distributed as source code to be in tar balls.

The GNU version of `tar` that Linux distributions ship with has a lot of options. This lesson is going to cover the most commonly used subset.

Let’s start off by creating an archive of the files used for compression:

```
$ cd ~/linux_essentials-3.1
$ tar cf archiving/3.1.tar compression
```

The `c` option instructs `tar` to create a new archive file and the `f` option is the name of the file to create. The argument immediately following the options is always going to be the name of the file to work on. The rest of the arguments are the paths to any files or directories you wish to add to, list, or extract from the file. In the example, we are adding the directory `compression` and all of its contents to the archive.

To view the contents of a tar ball, use the `t` option of `tar`:

```
$ tar -tf 3.1.tar
compression/
compression/bigfile-xz1.xz
compression/bigfile-gz9.gz
compression/hosts.gz
compression/bigfile2
compression/bigfile
compression/bigfile-gz1.gz
compression/bigfile-xz9.xz
compression/bigfile3
compression/bigfile4
```

Notice how the options are preceded with `-`. Unlike most programs, with `tar`, the `-` isn’t required when specifying options, although it doesn’t cause any harm if it is used.

```
Note
> You can use the -v option to let tar output the names of files it operates on when creating or extracting an archive.
```
Now let’s extract the file:

```
$ cd ~/linux_essentials-3.1/archiving
$ ls
3.1.tar
$ tar xf 3.1.tar
$ ls
3.1.tar  compression
```

Suppose you only need one file out of the archive. If this is the case, you can specify it after the archive’s file name. You can specify multiple files if necessary:

```
$ cd ~/linux_essentials-3.1/archiving
$ rm -rf compression
$ ls
3.1.tar
$ tar xvf 3.1.tar compression/hosts.gz
compression/
compression/bigfile-xz1.xz
compression/bigfile-gz9.gz
compression/hosts.gz
compression/bigfile2
compression/bigfile
compression/bigfile-gz1.gz
compression/bigfile-xz9.xz
compression/bigfile3
compression/bigfile4
$ ls
3.1.tar  compression
$ ls compression
hosts.gz
```

With the exception of absolute paths (paths beginning with `/` ), `tar` files preserve the entire path to files when they are created. Since the file `3.1.tar` was created with a single directory, that directory will be created relative to your current working directory when extracted. Another example should clarify this:

```
$ cd ~/linux_essentials-3.1/archiving
$ rm -rf compression
$ cd ../compression
$ tar cf ../tar/3.1-nodir.tar *
$ cd ../archiving
$ mkdir untar
$ cd untar
$ tar -xf ../3.1-nodir.tar
$ ls
bigfile   bigfile3  bigfile-gz1.gz  bigfile-xz1.xz  hosts.gz
bigfile2  bigfile4  bigfile-gz9.gz  bigfile-xz9.xz
```

```
Tip
> If you wish to use the absolute path in a tar file, you must use the P option. Be aware that this may overwrite important files and might cause errors on your system.
```

The `tar` program can also manage compression and decompression of archives on the fly. `tar` does so by calling one of the compression tools discussed earlier in this section. It is as simple as adding the option appropriate to the compression algorithm. The most commonly used ones are `j`, `J`, and `z` for `bzip2`, `xz`, and `gzip`, respectively. Below are examples using the aforementioned algorithms:

```
$ cd ~/linux_essentials-3.1/compression
$ ls
bigfile   bigfile3  bigfile-gz1.gz  bigfile-xz1.xz  hosts.gz
bigfile2  bigfile4  bigfile-gz9.gz  bigfile-xz9.xz
$ tar -czf gzip.tar.gz bigfile bigfile2 bigfile3
$ tar -cjf bzip2.tar.bz2 bigfile bigfile2 bigfile3
$ tar -cJf xz.tar.xz bigfile bigfile2 bigfile3
$ ls -l | grep tar
-rw-r--r-- 1 emma emma  450202 Jun 27 05:56 bzip2.tar.bz2
-rw-r--r-- 1 emma emma  548656 Jun 27 05:55 gzip.tar.gz
-rw-r--r-- 1 emma emma  147068 Jun 27 05:56 xz.tar.xz
```

Notice how in the example the `.tar` files have different sizes. This shows that they were successfully compressed. If you create compressed `.tar` archives, you should always add a second file extension denoting the algorithm you used. They are `.xz`, `.bz`, and `.gz` for `xz`, `bzip2`, and `gzip`, respectively. Sometimes shortened extensions such as `.tgz` are used.

It is possible to add files to already existing uncompressed tar archives. Use the `u` option to do this. If you attempt to add to a compressed archive, you will get an error.

```
$ cd ~/linux_essentials-3.1/compression
$ ls
bigfile   bigfile3  bigfile-gz1.gz  bigfile-xz1.xz  bzip2.tar.bz2  hosts.gz
bigfile2  bigfile4  bigfile-gz9.gz  bigfile-xz9.xz  gzip.tar.gz    xz.tar.xz
$ tar cf plain.tar bigfile bigfile2 bigfile3
$ tar tf plain.tar
bigfile
bigfile2
bigfile3
$ tar uf plain.tar bigfile4
$ tar tf plain.tar
bigfile
bigfile2
bigfile3
bigfile4
$ tar uzf gzip.tar.gz bigfile4
tar: Cannot update compressed archives
Try 'tar --help' or 'tar --usage' for more information.
```

### Managing ZIP files
Windows machines often don’t have applications to handle tar balls or many of the compression tools commonly found on Linux systems. If you need to interact with Windows systems, you can use ZIP files. A ZIP file is an archive file similar to a compressed `tar` file.

The `zip` and `unzip` programs can be used to work with ZIP files on Linux systems. The example below should be all you need to get started using them. First we create a set of files:

```
$ cd ~/linux_essentials-3.1
$ mkdir zip
$ cd zip/
$ mkdir dir
$ touch dir/file1 dir/file2
```

Now we use `zip` to pack these files into a ZIP file:

```
$ zip -r zipfile.zip dir
  adding: dir/ (stored 0%)
  adding: dir/file1 (stored 0%)
  adding: dir/file2 (stored 0%)
$ rm -rf dir
```

Finally, we unpack the ZIP file again:

```
$ ls
zipfile.zip
$ unzip zipfile.zip
Archive:  zipfile.zip
   creating: dir/
 extracting: dir/file1
 extracting: dir/file2
$ find
.
./zipfile.zip
./dir
./dir/file1
./dir/file2
```

When adding directories to ZIP files, the `-r` option causes `zip` to include a directory’s contents. Without it, you would have an empty directory in the ZIP file.


## Lesson 3.2 Searching and Extracting Data from Files
In this lab we will be focusing on redirecting or transmitting information from one source to another with the help of specific tools. The Linux command line redirects the information through specific standard channels. The standard input (stdin or channel 0) of the command is considered to be the keyboard and the standard output (stdout or channel 1) is considered the screen. There is also another channel that is meant to redirect error output (stderr or channel 2) of a command or a program’s error messages. The input and/or output can be redirected.

When running a command, sometimes we want to transmit certain information to the command or redirect the output to a specific file. Each of these functionalities will be discussed in the next two sections.

### I/O Redirection
I/O redirection enables the user to redirect information from or to a command by using a text file. As described earlier, the standard input, output and error output can be redirected, and the information can be taken from text files.

### Redirecting Standard Output
To redirect standard output to a file, instead of the screen, we need to use the `>` operator followed by the name of the file. If the file doesn’t exist, a new one will be created, otherwise, the information will overwrite the existing file.

In order to see the contents of the file that we just created, we can use the `cat` command. By default, this command displays the contents of a file on the screen. Consult the manual page to find out more about its functionalities.

The example below demonstrates the functionality of the operator. In the first instance, a new file is created containing the text “Hello World!”:

```
$ echo "Hello World!" > text
$ cat text
Hello World!
```

In the second invocation, the same file is overwritten with the new text:

```
$ echo "Hello!" > text
$ cat text
Hello!
```

If we want to add new information at the end of the file, we need to use the `>>` operator. This operator also creates a new file if it cannot find an existing one.

The first example shows the addition of the text. As it can be seen, the new text was added on the following line:

```
$ echo "Hello to you too!" >> text
$ cat text
Hello!
Hello to you too!
```

The second example demonstrates that a new file will be created:

```
$ echo "Hello to you too!" >> text2
$ cat text2
Hello to you too!
```

### Redirecting Standard Error
In order to redirect just the error messages, a user will need to employ the `2>` operator followed by the name of the file in which the errors will be written. If the file doesn’t exist, a new one will be created, otherwise the file will be overwritten.

As explained, the channel for redirecting the standard error is channel 2. When redirecting the standard error, the channel must be specified, contrary to the other standard output where channel 1 is set by default. For example, the following command searches for a file or directory named `games` and only writes the error into the `text-error` file, while displaying the standard output on the screen:

```
$ find /usr games 2> text-error
/usr
/usr/share
/usr/share/misc
---------Omitted output----------
/usr/lib/libmagic.so.1.0.0
/usr/lib/libdns.so.81
/usr/games
$ cat text-error
find: `games': No such file or directory
```

```
Note
> For more information about the find command, consult its man page.
```

For example, the following command will run without errors, therefore no information will be written in the file text-error:

```
$ sort /etc/passwd 2> text-error
$ cat text-error
```

As well as the standard output, the standard error can also be appended to a file with the `2>>` operator. This will add the new error at the end of the file. If the file doesn’t exist, a new one will be created. The first example shows the addition of the new information into the file, whereas the second example shows that the command creates a new file where an existing one can’t be found with the same name:

```
$ sort /etc 2>> text-error
$ cat text-error
sort: read failed: /etc: Is a directory
```

```
$ sort /etc/shadow 2>> text-error2
$ cat text-error2
sort: open failed: /etc/shadow: Permission denied
```

Using this type of redirection, only the error messages will be redirected to the file, the normal output will be written on the screen or go through standard output or stdout.

There is one particular file that technically is a bit bucket (a file that accepts input and doesn’t do anything with it): `/dev/null`. You can redirect any irrelevant information that you might not want displayed or redirected into an important file, as shown in the example below:
```
$ sort /etc 2> /dev/null
```

### Redirecting Standard Input
This type of redirection is used to input data to a command, from a specified file instead of a keyboard. In this case the `<` operator is used as shown in the example:

```
$ cat < text
Hello!
Hello to you too!
```

Redirecting standard input is usually used with commands that don’t accept file arguments. The `tr` command is one of them. This command can be used to translate file contents by modifying the characters in a file in specific ways, like deleting any particular character from a file, the example below shows the deletion of the character `l`:

```
$ tr -d "l" < text
Heo!
Heo to you too!
```

For more information, consult the man page of `tr`.

### Here Documents
Unlike the output redirections, the `<<` operator acts in a different way compared to the other operators. This input stream is also called here document. Here document represents the block of code or text which can be redirected to the command or the interactive program. Different types of scripting languages, like `bash`, `sh` and `csh` are able to take input directly from the command line, without using any text files.

As can be seen in the example below, the operator is used to input data into the command, while the word after doesn’t specify the file name. The word is interpreted as the delimiter of the input and it will not be taken in consideration as content, therefore `cat` will not display it:

```
$ cat << hello
> hey
> ola
> hello
hey
ola
```

Consult the man page of the `cat` command to find more information.

### Combinations
The first combination that we will explore combines the redirection of the standard output and standard error output to the same file. The `&>` and `&>>` operators are used, `&` representing the combination of channel 1 and channel 2. The first operator will overwrite the existing contents of the file and the second one will append or add the new information at the end of the file. Both operators will enable the creation of the new file if it doesn’t exist, just like in the previous sections:

```
$ find /usr admin &> newfile
$ cat newfile
/usr
/usr/share
/usr/share/misc
---------Omitted output----------
/usr/lib/libmagic.so.1.0.0
/usr/lib/libdns.so.81
/usr/games
find: `admin': No such file or directory
$ find /etc/calendar &>> newfile
$ cat newfile
/usr
/usr/share
/usr/share/misc
---------Omitted output----------
/usr/lib/libmagic.so.1.0.0
/usr/lib/libdns.so.81
/usr/games
find: `admin': No such file or directory
/etc/calendar
/etc/calendar/default
```

Let’s take a look at an example using the `cut` command:

```
$ cut -f 3 -d "/" newfile
$ cat newfile

share
share
share
---------Omitted output----------
lib
games
find: `admin': No such file or directory
calendar
calendar
find: `admin': No such file or directory
```

The `cut` command cuts specified fields from the input file by using the `-f` option, the 3rd field in our case. In order for the command to find the field, a delimiter needs to be specified as well with the `-d` option. In our case the delimiter will be the `/` character.

To find more about the `cut` command, consult its man page.

### Command Line Pipes
Redirection is mostly used to store the result of a command, to be processed by a different command. This type of intermediate process can become very tedious and complicated if you want the data to go through multiple processes. In order to avoid this, you can link the command directly via pipes. In other words, the first command’s output automatically becomes the second command’s input. This connection is made by using the `|` (vertical bar) operator:

```
$ cat /etc/passwd | less
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
:
```

In the example above, the `less` command after the pipe operator modifies the way that the file is displayed. The `less` command displays the text file allowing the user to scroll up and down a line at the time. `less` is also used by default to display the man pages, as discussed in the previous lessons.

It is possible to use multiple pipes at the same time. The intermediate commands that receive input then change it and produce output are called filters. Let’s take the `ls -l` command and try to count the number of words from the first 10 lines of the output. In order to do this, we will have to use the `head` command that by default displays the first 10 lines of a file and then count the words using the `wc` command:

```
$ ls -l | head | wc -w
10
```

As mentioned earlier, by default, `head` only displays the first 10 lines of the text file specified. This behaviour can be modified by using specific options. Check the command’s man page to find more.

There is another command that displays the end of a file: `tail`. By default, this command selects the last 10 lines and displays them, but as `head` the number can also be modified. Check `tail`’s man page for more details.

```
Note
> The -f option can show the last lines of a file while it’s being updated. This feature can become very useful when monitoring a file like syslog for ongoing activity.
```
The `wc` (word count) command counts by default the lines, words and bytes of a file. As shown in the exercise, the `-w` option causes the command to only count the words within the selected lines. The most common options that you can use with this command are: `-l`, which specifies to the command to only count the lines, and `-c`, which is used to count only the bytes. More variations and options of the command, as well as more information about `wc` can be found within the command’s man page.

___

In this lesson, we are going to look at tools that are used to manipulate text. These tools are frequently used by system administrators or programs to automatically monitor or identify specific recurring information.

### Searching within Files with `grep`
The first tool that we will discuss in this lesson is the `grep` command. `grep` is the abbreviation of the phrase “global regular expression print” and its main functionality is to search within files for the specified pattern. The command outputs the line containing the specified pattern highlighted in red.

```
$ grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
user:x:1001:1001:User,,,,:/home/user:/bin/bash
```

`grep`, as most commands, can also be tweaked by using options. Here are the most common ones:

`-i`
-the search is case insensitive

`-r`
- the search is recursive (it searches into all files within the given directory and its subdirectories)

`-c`
- the search counts the number of matches

`-v`
invert the match, to print lines that do not match the search term

`-E`
turns on extended regular expressions (needed by some of the more advanced meta-characters like `|` , `+` and `?`)

`grep` has many other useful options. Consult the man page to find out more about it.

### Regular Expressions
The second tool is very powerful. It is used to describe bits of text within files, also called regular expressions. Regular expressions are extremely useful in extracting data from text files by constructing patterns. They are commonly used within scripts or when programming with high level languages, such as Perl or Python.

When working with regular expressions, it is very important to keep in mind that every character counts and the pattern is written with the purpose of matching a specific sequence of characters, known as a string. Most patterns use the normal ASCII symbols, such as letters, digits, punctuation or other symbols, but it can also use Unicode characters in order to match any other type of text.

The following list explains the regular expressions meta-characters that are used to form the patterns.

`.`
- Match any single character (except newline)

`[abcABC]`
- Match any one character within the brackets

`[^abcABC]`
- Match any one character except the ones in the brackets

`[a-z]`
- Match any character in the range

`[^a-z]`
- Match any character except the ones in the range

`sun|moon`
- Find either of the listed strings

`^`
- Start of a line

`$`
-End of a line

All functionalities of the regular expressions can be implemented through `grep` as well. You can see that in the example above, the word is not surrounded by double quotes. To prevent the shell from interpreting the meta-character itself, it is recommended that the more complex pattern be between double quotes (" "). For the purpose of practice, we will be using double quotes when implementing regular expressions. The other quotation marks keep their normal functionality, as discussed in previous lessons.

The following examples emphasize the functionality of the regular expressions. We will need data within the file, therefore the next set of commands just appends different strings to the `text.txt` file.

```
$ echo "aaabbb1" > text.txt
$ echo "abab2" >> text.txt
$ echo "noone2" >> text.txt
$ echo "class1" >> text.txt
$ echo "alien2" >> text.txt
$ cat text.txt
aaabbb1
abab2
noone2
class1
alien2
```

The first example is a combination of searching through the file without and with regular expressions. In order to fully understand regular expressions, it is very important to show the difference. The first command searches for the exact string, anywhere in the line, whereas the second command searches for sets of characters that contain any of the characters between the brackets. Therefore, the results of the commands are different.

```
$ grep "ab" text.txt
aaabbb1
abab2
$ grep "[ab]" text.txt
aaabbb1
abab2
class1
alien2
```

The second set of examples shows the application of the beginning and the end of the line meta-character. It is very important to specify the need to put the 2 characters at the right place in the expression. When specifying the beginning of the line, the meta-character needs to be before the expression, whereas, when specifying the end of the line, the meta-character needs to be after the expression.

```
$ grep "^a" text.txt
aaabbb1
abab2
alien2
$ grep "2$" text.txt
abab2
noone2
alien2
On top of the previous explained meta-characters, regular expressions also have meta-characters that enable multiplication of the previously specified pattern:
```

`*`
- Zero or more of the preceding pattern

`+`
- One or more of the preceding pattern

`?`
- Zero or one of the preceding pattern

For the multiplier meta-characters, the command below searches for a string that contains `ab`, a single character and one or more of the characters previously found. The result shows that `grep` found the `aaabbb1` string, matching the `abbb` part as well as `abab2`. Since the `+` character is an extended regular expression character, we need to pass the `-E` option to the `grep` command.

```
$ grep -E "ab.+" text.txt
aaabbb1
abab2
```

Most of the meta-characters are self-explanatory, but they can become tricky when used for the first time. The previous examples represent a small part of the regular expressions’ functionality. Try all meta-characters from the above table to understand more on how they work.

## Lesson 3.3 Turning Commands into a Script
We have been learning to execute commands from the shell thus far, but we can also enter commands into a file, and then set that file to be executable. When the file is executed, those commands are run one after the other. These executable files are called scripts, and they are an absolutely crucial tool for any Linux system administrator. Essentially, we can consider Bash to be a programming language as well as a shell.

Printing Output
Let’s begin by demonstrating a command that you may have seen in previous lessons: echo will print an argument to standard output.

$ echo "Hello World!"
Hello World!
Now, we will use file redirection to send this command to a new file called new_script.

$ echo 'echo "Hello World!"' > new_script
$ cat new_script
echo "Hello World!"
The file new_script now contains the same command as before.

Making a Script Executable
Let’s demonstrate some of the steps required to make this file execute the way we expect it to. A user’s first thought might be to simply type the name of the script, the way they might type in the name of any other command:

$ new_script
/bin/bash: new_script: command not found
We can safely assume that new_script exists in our current location, but notice that the error message isn’t telling us that the file doesn’t exist, it is telling us that the command doesn’t exist. It would be useful to discuss how Linux handles commands and executables.

Commands and PATH
When we type the ls command into the shell, for example, we are executing a file called ls that exists in our filesystem. You can prove this by using which:

$ which ls
/bin/ls
It would quickly become tiresome to type in the absolute path of ls every time we wish to look at the contents of a directory, so Bash has an environment variable which contains all the directories where we might find the commands we wish to run. You can view the contents of this variable by using echo.

$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
Each of these locations is where the shell expects to find a command, delimited with colons (:). You will notice that /bin is present, but it is safe to assume that our current location is not. The shell will search for new_script in each of these directories, but it will not find it and therefore will throw the error we saw above.

There are three solutions to this issue: we can move new_script into one of the PATH directories, we can add our current directory to PATH, or we can change the way we attempt to call the script. The latter solution is easiest, it simply requires us to specify the current location when calling the script using dot slash (./).

$ ./new_script
/bin/bash: ./new_script: Permission denied
The error message has changed, which indicates that we have made some progress.

Execute Permissions
The first investigation a user should do in this case is to use ls -l to look at the file:

$ ls -l new_script
-rw-rw-r-- 1 user user 20 Apr 30 12:12 new_script
We can see that the permissions for this file are set to 664 by default. We have not set this file to have execute permissions yet.

$ chmod +x new_script
$ ls -l new_script
-rwxrwxr-x 1 user user 20 Apr 30 12:12 new_script
This command has given execute permissions to all users. Be aware that this might be a security risk, but for now this is an acceptable level of permission.

$ ./new_script
Hello World!
We are now able to execute our script.

Defining the Interpreter
As we have demonstrated, we were able to simply enter text into a file, set it as an executable, and run it. new_script is functionally still a normal text file, but we managed to have it be interpreted by Bash. But what if it is written in Perl, or Python?

It is very good practice to specify the type of interpreter we want to use in the first line of a script. This line is called a bang line or more commonly a shebang. It indicates to the system how we want this file to be executed. Since we are learning Bash, we will be using the absolute path to our Bash executable, once again using which:

$ which bash
/bin/bash
Our shebang starts with a hash sign and exclamation mark, followed by the absolute path above. Let’s open new_script in a text editor and insert the shebang. Let’s also take the opportunity to insert a comment into our script. Comments are ignored by the interpreter. They are written for the benefit of other users wishing to understand your script.

#!/bin/bash

# This is our first comment. It is also good practice to document all scripts.

echo "Hello World!"
We will make one additional change to the filename as well: we will save this file as new_script.sh. The file suffix ".sh" does not change the execution of the file in any way. It is a convention that bash scripts be labelled with .sh or .bash in order to identify them more easily, the same way that Python scripts are usually identified with the suffix .py.

Common Text Editors
Linux users often have to work in an environment where graphical text editors are not available. It is therefore highly recommended to develop at least some familiarity with editing text files from the command line. Two of the most common text editors are vi and nano.

vi
vi is a venerable text editor and is installed by default on almost every Linux system in existence. vi spawned a clone called vi IMproved or vim which adds some functionality but maintains the interface of vi. While working with vi is daunting for a new user, the editor is popular and well-loved by users who learn its many features.

The most important difference between vi and applications such as Notepad is that vi has three different modes. On startup, the keys H, J, K and L are used to navigate, not to type. In this navigation mode, you can press I to enter insert mode. At this point, you may type normally. To exit insert mode, you press Esc to return to navigation mode. From navigation mode, you can press : to enter command mode. From this mode, you can save, delete, quit or change options.

While vi has a learning curve, the different modes can in time allow a savvy user to become more efficient than with other editors.

nano
nano is a newer tool, built to be simple and easier to use than vi. nano does not have different modes. Instead, a user on startup can begin typing, and uses Ctrl to access the tools printed at the bottom of the screen.

                         [ Welcome to nano.  For basic help, type Ctrl+G. ]
^G Get Help   ^O Write Out  ^W Where Is   ^K Cut Text   ^J Justify    ^C Cur Pos    M-U Undo
^X Exit       ^R Read File  ^\ Replace    ^U Uncut Text ^T To Spell   ^_ Go To Line M-E Redo
Text editors are a matter of personal preference, and the editor that you choose to use will have no bearing on this lesson. But becoming familiar and comfortable with one or more text editors will pay off in the future.

Variables
Variables are an important part of any programming language, and Bash is no different. When you start a new session from the terminal, the shell already sets some variables for you. The PATH variable is an example of this. We call these environment variables, because they usually define characteristics of our shell environment. You can modify and add environment variables, but for now let’s focus on setting variables inside our script.

We will modify our script to look like this:

#!/bin/bash

# This is our first comment. It is also good practice to comment all scripts.

username=Carol

echo "Hello $username!"
In this case, we have created a variable called username and we have assigned it the value of Carol. Please note that there are no spaces between the variable name, the equals sign, or the assigned value.

In the next line, we have used the echo command with the variable, but there is a dollar sign ($) in front of the variable name. This is important, since it indicates to the shell that we wish to treat username as a variable, and not just a normal word. By entering $username in our command, we indicate that we want to perform a substitution, replacing the name of a variable with the value assigned to that variable.

Executing the new script, we get this output:

$ ./new_script.sh
Hello Carol!
Variable names must contain only alphanumeric characters or underscores, and are case sensitive. Username and username will be treated as separate variables.

Variable substitution may also have the format ${username}, with the addition of the { }. This is also acceptable.

Variables in Bash have an implicit type, and are considered strings. This means that performing math functions in Bash is more complicated than it would be in other programming languages such as C/C++:

#!/bin/bash

# This is our first comment. It is also good practice to comment all scripts.

username=Carol
x=2
y=4
z=$x+$y
echo "Hello $username!"
echo "$x + $y"
echo "$z"
$ ./new_script.sh
Hello Carol!
2 + 4
2+4
Using Quotes with Variables
Let’s make the following change to the value of our variable username:

#!/bin/bash

# This is our first comment. It is also good practice to comment all scripts.

username=Carol Smith

echo "Hello $username!"
Running this script will give us an error:

$ ./new_script.sh
./new_script.sh: line 5: Smith: command not found
Hello !
Keep in mind that Bash is an interpreter, and as such it interprets our script line-by-line. In this case, it correctly interprets username=Carol to be setting a variable username with the value Carol. But it then interprets the space as indicating the end of that assignment, and Smith as being the name of a command. In order to have the space and the name Smith be included as the new value of our variable, we will put double quotes (") around the name.

#!/bin/bash

# This is our first comment. It is also good practice to comment all scripts.

username="Carol Smith"

echo "Hello $username!"
$ ./new_script.sh
Hello Carol Smith!
One important thing to note in Bash is that double quotes and single quotes (') behave very differently. Double quotes are considered “weak”, because they allow the interpreter to perform substitution inside the quotes. Single quotes are considered “strong”, because they prevent any substitution from occurring. Consider the following example:

#!/bin/bash

# This is our first comment. It is also good practice to comment all scripts.

username="Carol Smith"

echo "Hello $username!"
echo 'Hello $username!'
$ ./new_script.sh
Hello Carol Smith!
Hello $username!
In the second echo command, the interpreter has been prevented from substituting $username with Carol Smith, and so the output is taken literally.

Arguments
You are already familiar with using arguments in the Linux core utilities. For example, rm testfile contains both the executable rm and one argument testfile. Arguments can be passed to the script upon execution, and will modify how the script behaves. They are easily implemented.

#!/bin/bash

# This is our first comment. It is also good practice to comment all scripts.

username=$1

echo "Hello $username!"
Instead of assigning a value to username directly inside the script, we are assigning it the value of a new variable $1. This refers to the value of the first argument.

$ ./new_script.sh Carol
Hello Carol!
The first nine arguments are handled in this way. There are ways to handle more than nine arguments, but that is outside the scope of this lesson. We will demonstrate an example using just two arguments:

#!/bin/bash

# This is our first comment. It is also good practice to comment all scripts.

username1=$1
username2=$2
echo "Hello $username1 and $username2!"
$ ./new_script.sh Carol Dave
Hello Carol and Dave!
There is an important consideration when using arguments: In the example above, there are two arguments Carol and Dave, assigned to $1 and $2 respectively. If the second argument is missing, for example, the shell will not throw an error. The value of $2 will simply be null, or nothing at all.

$ ./new_script.sh Carol
Hello Carol and !
In our case, it would be a good idea to introduce some logic to our script so that different conditions will affect the output that we wish to print. We will start by introducing another helpful variable and then move on to creating if statements.

Returning the Number of Arguments
While variables such as $1 and $2 contain the value of positional arguments, another variable $# contains the number of arguments.

#!/bin/bash

# This is our first comment. It is also good practice to comment all scripts.

username=$1

echo "Hello $username!"
echo "Number of arguments: $#."
$ ./new_script.sh Carol Dave
Hello Carol!
Number of arguments: 2.
Conditional Logic
The use of conditional logic in programming is a vast topic, and won’t be covered deeply in this lesson. We will focus on the syntax of conditionals in Bash, which differs from most other programming languages.

Let’s begin by reviewing what we hope to achieve. We have a simple script which should be able to print a greeting to a single user. If there is anything other than one user, we should print an error message.

The condition we are testing is the number of users, which is contained in the variable $#. We would like to know if the value of $# is 1.

If the condition is true, the action we will take is to greet the user.

If the condition is false, we will print an error message.

Now that the logic is clear, we will focus on the syntax required to implement this logic.

#!/bin/bash

# A simple script to greet a single user.

if [ $# -eq 1 ]
then
  username=$1

  echo "Hello $username!"
else
  echo "Please enter only one argument."
fi
echo "Number of arguments: $#."
The conditional logic is contained between if and fi. The condition to test is located between square brackets [ ], and the action to take should the condition be true is indicated after then. Note the spaces between the square brackets and the logic contained. Omitting this space will cause errors.

This script will output either our greeting, or the error message. But it will always print the Number of arguments line.

$ ./new_script.sh
Please enter only one argument.
Number of arguments: 0.
$ ./new_script.sh Carol
Hello Carol!
Number of arguments: 1.
Take note of the if statement. We have used -eq to do a numerical comparison. In this case, we are testing that the value of $# is equal to one. The other comparisons we can perform are:

-ne
Not equal to

-gt
Greater than

-ge
Greater than or equal to

-lt
Less than

-le
Less than or equal to









___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/010-160/)