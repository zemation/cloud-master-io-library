# LPIC-1: GNU and Unix Commands

## Lesson 103.2: Process text streams using filters
Dealing with text is a major part of every systems administrator’s job. Doug McIlroy, a member of the original Unix development team, summarized the Unix philosophy and said (among other important things): “Write programs to handle text streams, because that is a universal interface.” Linux is inspired by the Unix operating system and it firmly adopts its philosophy, so an administrator must expect lots of text manipulation tools within a Linux distribution.

### A Quick Review on Redirections and Pipes
Also from the Unix philosophy:

- Write programs that do one thing and do it well.

- Write programs to work together.

One major way of making programs work together is through piping and redirections. Pretty much all of your text manipulation programs will get text from a standard input (stdin), output it to a standard output (stdout) and send eventual errors to a standard error output (stderr). Unless you specify otherwise, the standard input will be what you type on your keyboard (the program will read it after you press the Enter key). Similarly, the standard output and errors will be displayed in your terminal screen. Let us see how this works.

In your terminal, type `cat` and then hit the Enter key. Then type some random text.

```
$ cat
This is a test
This is a test
Hey!
Hey!
It is repeating everything I type!
It is repeating everything I type!
(I will hit ctrl+c so I will stop this nonsense)
(I will hit ctrl+c so I will stop this nonsense)
^C
```

For more information about the `cat` command (the term comes from “concatenate”) please refer to the man pages.

```
Note
If you are working on a really plain installation of a Linux server, some commands such as info and less might not be available. If this is the case, install these tools using the proper procedure in your system as described in the corresponding lessons.
```

As demonstrated above if you do not specify where `cat` should read from, it will read from the standard input (whatever you type) and output whatever it reads to your terminal window (its standard output).

Now try the following:

```
$ cat > mytextfile
This is a test
I hope cat is storing this to mytextfile as I redirected the output
I will hit ctrl+c now and check this
^C

$ cat mytextfile
This is a test
I hope cat is storing this to mytextfile as I redirected the output
I will hit ctrl+c now and check this
```

The `>` (greater than) tells `cat` to direct its output to the `mytextfile` file, not the standard output. Now try this:

```
$ cat mytextfile > mynewtextfile
$ cat mynewtextfile
This is a test
I hope cat is storing this to mytextfile as I redirected the output
I will hit ctrl+c now and check this
```

This has the effect of copying `mytextfile` to `mynewtextfile`. You can actually verify that these two files have the same content by performing a `diff`:

```
$ diff mynewtextfile mytextfile
```

As there is no output, the files are equal. Now try the append redirection operator (`>>`):

```
$ echo 'This is my new line' >> mynewtextfile
$ diff mynewtextfile mytextfile
4d3
< This is my new line
```

So far we have used redirections to create and manipulate files. We can also use pipes (represented by the symbol `|`) to redirect the output of one program to another program. Let us find the lines where the word “this” is found:

```
$ cat mytextfile | grep this
I hope cat is storing this to mytextfile as I redirected the output
I will hit ctrl+c now and check this

$ cat mytextfile | grep -i this
This is a test
I hope cat is storing this to mytextfile as I redirected the output
I will hit ctrl+c now and check this
```

Now we have piped the output of `cat` to another command: `grep`. Notice when we ignore the case (using the `-i` option) we get an extra line as a result.

### Processing Text Streams

### Reading a Compressed File
We will create a file called `ftu.txt` containing a list of the following commands:

```
bzcat
cat
cut
head
less
md5sum
nl
od
paste
sed
sha256sum
sha512sum
sort
split
tail
tr
uniq
wc
xzcat
zcat
```

Now we will use the `grep` command to print all of the lines containing the string `cat`:

```
$ cat ftu.txt | grep cat
bzcat
cat
xzcat
zcat
```

Another way to get this information is to just use the `grep` command to filter the text directly, without the need to use another application to send the text stream to `stdout`.

```
$ grep cat ftu.txt
bzcat
cat
xzcat
zcat
```

```
Note
Remember there are many ways to perform the same task using Linux.
```

There are other commands that handle compressed files (`bzcat` for `bzip` compressed files, `xzcat` for `xz` compressed files and `zcat` for `gzip` compressed files) and each one is used to view the contents of a compressed file based on the compression algorithm used.

Verify that the newly created file `ftu.txt` is the only one in the directory, then create a `gzip` compressed version of the file:

```
$ ls ftu*
ftu.txt

$ gzip ftu.txt
$ ls ftu*
ftu.txt.gz
```

Next, use the `zcat` command to view the contents of the gzipped compressed file:

```
$ zcat ftu.txt.gz
bzcat
cat
cut
head
less
md5sum
nl
od
paste
sed
sha256sum
sha512sum
sort
split
tail
tr
uniq
wc
xzcat
zcat
```

Note that `gzip` will compress `ftu.txt` into `ftu.txt.gz` and it will remove the original file. By default, no output from the `gzip` command will be displayed. However, if you do want `gzip` to tell you what it is doing, use the `-v` option for the “verbose” output.

### Viewing a File in a Pager
You know `cat` concatenates a file to the standard output (once a file is provided after the command). The file `/var/log/syslog` is where your Linux system stores everything important going on in your system. Using the `sudo` command to elevate privileges so as to be able to read the `/var/log/syslog file`:

```
$ sudo cat /var/log/syslog
```

…​you will see messages scrolling very fast within your terminal window. You can pipe the output to the program `less` so the results will be paginated. By `using` less you can use the arrow keys to navigate through the output and also use `vi` like commands to navigate and search throughout the text.

However, rather than pipe the `cat` command into a pagination program it is more pragmatic to just use the pagination program directly:

```
$ sudo less /var/log/syslog
... (output omitted for clarity)
```

### Getting a Portion of a Text File
If only the start or end of a file needs to be reviewed, there are other methods available. The command `head` is used to read the first ten lines of a file by default, and the command `tail` is used to read the last ten lines of a file by default. Now try:

```
$ sudo head /var/log/syslog
Nov 12 08:04:30 hypatia rsyslogd: [origin software="rsyslogd" swVersion="8.1910.0" x-pid="811" x-info="https://www.rsyslog.com"] rsyslogd was HUPed
Nov 12 08:04:30 hypatia systemd[1]: logrotate.service: Succeeded.
Nov 12 08:04:30 hypatia systemd[1]: Started Rotate log files.
Nov 12 08:04:30 hypatia vdr: [928] video directory scanner thread started (pid=882, tid=928, prio=low)
Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'A - ATSC'
Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'C - DVB-C'
Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'S - DVB-S'
Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'T - DVB-T'
Nov 12 08:04:30 hypatia vdr[882]: vdr: no primary device found - using first device!
Nov 12 08:04:30 hypatia vdr: [929] epg data reader thread started (pid=882, tid=929, prio=high)
$ sudo tail /var/log/syslog
Nov 13 10:24:45 hypatia kernel: [ 8001.679238] mce: CPU7: Core temperature/speed normal
Nov 13 10:24:46 hypatia dbus-daemon[2023]: [session uid=1000 pid=2023] Activating via systemd: service name='org.freedesktop.Tracker1.Miner.Extract' unit='tracker-extract.service' requested by ':1.73' (uid=1000 pid=2425 comm="/usr/lib/tracker/tracker-miner-fs ")
Nov 13 10:24:46 hypatia systemd[2004]: Starting Tracker metadata extractor...
Nov 13 10:24:47 hypatia dbus-daemon[2023]: [session uid=1000 pid=2023] Successfully activated service 'org.freedesktop.Tracker1.Miner.Extract'
Nov 13 10:24:47 hypatia systemd[2004]: Started Tracker metadata extractor.
Nov 13 10:24:54 hypatia kernel: [ 8010.462227] mce: CPU0: Core temperature above threshold, cpu clock throttled (total events = 502907)
Nov 13 10:24:54 hypatia kernel: [ 8010.462228] mce: CPU4: Core temperature above threshold, cpu clock throttled (total events = 502911)
Nov 13 10:24:54 hypatia kernel: [ 8010.469221] mce: CPU0: Core temperature/speed normal
Nov 13 10:24:54 hypatia kernel: [ 8010.469222] mce: CPU4: Core temperature/speed normal
Nov 13 10:25:03 hypatia systemd[2004]: tracker-extract.service: Succeeded.
```

To help illustrate the number of lines displayed, we can pipe the output of the `head` command to the `nl` command, which will display the number of lines of text streamed into the command:

```
$ sudo head /var/log/syslog | nl
1	Nov 12 08:04:30 hypatia rsyslogd: [origin software="rsyslogd" swVersion="8.1910.0" x-pid="811" x-info="https://www.rsyslog.com"] rsyslogd was HUPed
2	Nov 12 08:04:30 hypatia systemd[1]: logrotate.service: Succeeded.
3	Nov 12 08:04:30 hypatia systemd[1]: Started Rotate log files.
4	Nov 12 08:04:30 hypatia vdr: [928] video directory scanner thread started (pid=882, tid=928, prio=low)
5	Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'A - ATSC'
6	Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'C - DVB-C'
7	Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'S - DVB-S'
8	Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'T - DVB-T'
9	Nov 12 08:04:30 hypatia vdr[882]: vdr: no primary device found - using first device!
10	Nov 12 08:04:30 hypatia vdr: [929] epg data reader thread started (pid=882, tid=929, prio=high)
```

And we can do the same by piping the output of the `tail` command to the `wc` command, which by default will count the number of words within a document, and using the `-l` switch to print out the number of lines of text that the command has read:

```
$ sudo tail /var/log/syslog | wc -l
10
```

Should an administrator need to review more (or less) of the beginning or end of a file, the `-n` option can be used to limit the commands' output:

```
$ sudo tail -n 5 /var/log/syslog
Nov 13 10:37:24 hypatia systemd[2004]: tracker-extract.service: Succeeded.
Nov 13 10:37:42 hypatia dbus-daemon[2023]: [session uid=1000 pid=2023] Activating via systemd: service name='org.freedesktop.Tracker1.Miner.Extract' unit='tracker-extract.service' requested by ':1.73' (uid=1000 pid=2425 comm="/usr/lib/tracker/tracker-miner-fs ")
Nov 13 10:37:42 hypatia systemd[2004]: Starting Tracker metadata extractor...
Nov 13 10:37:43 hypatia dbus-daemon[2023]: [session uid=1000 pid=2023] Successfully activated service 'org.freedesktop.Tracker1.Miner.Extract'
Nov 13 10:37:43 hypatia systemd[2004]: Started Tracker metadata extractor.
$ sudo head -n 12 /var/log/syslog
Nov 12 08:04:30 hypatia rsyslogd: [origin software="rsyslogd" swVersion="8.1910.0" x-pid="811" x-info="https://www.rsyslog.com"] rsyslogd was HUPed
Nov 12 08:04:30 hypatia systemd[1]: logrotate.service: Succeeded.
Nov 12 08:04:30 hypatia systemd[1]: Started Rotate log files.
Nov 12 08:04:30 hypatia vdr: [928] video directory scanner thread started (pid=882, tid=928, prio=low)
Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'A - ATSC'
Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'C - DVB-C'
Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'S - DVB-S'
Nov 12 08:04:30 hypatia vdr: [882] registered source parameters for 'T - DVB-T'
Nov 12 08:04:30 hypatia vdr[882]: vdr: no primary device found - using first device!
Nov 12 08:04:30 hypatia vdr: [929] epg data reader thread started (pid=882, tid=929, prio=high)
Nov 12 08:04:30 hypatia vdr: [882] no DVB device found
Nov 12 08:04:30 hypatia vdr: [882] initializing plugin: vnsiserver (1.8.0): VDR-Network-Streaming-Interface (VNSI) Server
```

### The Basics of sed, the Stream Editor
Let us take a look at the other files, terms and utilities that do not have `cat` in their names. We can do this by passing the `-v` option to `grep`, which instructs the command to output only the lines not containing `cat`:

```
$ zcat ftu.txt.gz | grep -v cat
cut
head
less
md5sum
nl
od
paste
sed
sha256sum
sha512sum
sort
split
tail
tr
uniq
wc
```

Most of what we can do with `grep` we can also do with `sed` — the stream editor for filtering and transforming text (as stated in the `sed` manual page). First we will recover our `ftu.txt` file by decompressing our `gzip` archive of the file:

```
$ gunzip ftu.txt.gz
$ ls ftu*
ftu.txt
```

Now, we can use `sed` to list only the lines containing the string `cat`:

```
$ sed -n /cat/p < ftu.txt
bzcat
cat
xzcat
zcat
```

We have used the less-than sign `<` to direct the contents of the file `ftu.txt` into into our `sed` command. The word enclosed between slashes (i.e. `/cat/`) is the term we are searching for. The `-n` option instructs `sed` to produce no output (unless the ones later instructed by the `p` command). Try running this same command without the `-n` option to see what happens. Then try this:

```
$ sed /cat/d < ftu.txt
cut
head
less
md5sum
nl
od
paste
sed
sha256sum
sha512sum
sort
split
tail
tr
uniq
wc
```

If we do not use the `-n` option, `sed` will print everything from the file except for what the `d` instructs `sed` to delete from its output.

A common use of `sed` is to find and replace text within a file. Suppose you want to change every occurrence of `cat` to `dog`. You can use `sed` to do this by supplying the `s` option to swap out each instance of the first term, `cat`, for the second term, `dog`:

```
$ sed s/cat/dog/ < ftu.txt
bzdog
dog
cut
head
less
md5sum
nl
od
paste
sed
sha256sum
sha512sum
sort
split
tail
tr
uniq
wc
xzdog
zdog
```

Rather than using a redirection operator (`<`) to pass the `ftu.txt` file into our `sed` command, we can just have the `sed` command operate on the file directly. We will try that next, while simultaneously creating a backup of the original file:

```
$ sed -i.backup s/cat/dog/ ftu.txt
$ ls ftu*
ftu.txt  ftu.txt.backup
```

The `-i` option will perform an in-place `sed` operation on your original file. If you do not use the `.backup` after the `-i` parameter, you would just have rewritten your original file. Whatever you use as text after the `-i` parameter will be the name the original file will be saved to prior to the modifications you asked `sed` to perform.

### Ensuring Data Integrity
We have demonstrated how easy it is to manipulate files in Linux. There are times where you may wish to distribute a file to someone else, and you want to be sure that the recipient ends up with a true copy of the original file. A very common use of this technique is practiced when Linux distribution servers host downloadable CD or DVD images of their software along with files that contain the calculated checksum values of those disc images. Here is an example listing from a Debian download mirror:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[PARENTDIR] Parent Directory                                      -
[SUM]       MD5SUMS                              2019-09-08 17:46 274
[CRT]       MD5SUMS.sign                         2019-09-08 17:52 833
[SUM]       SHA1SUMS                             2019-09-08 17:46 306
[CRT]       SHA1SUMS.sign                        2019-09-08 17:52 833
[SUM]       SHA256SUMS                           2019-09-08 17:46 402
[CRT]       SHA256SUMS.sign                      2019-09-08 17:52 833
[SUM]       SHA512SUMS                           2019-09-08 17:46 658
[CRT]       SHA512SUMS.sign                      2019-09-08 17:52 833
[ISO]       debian-10.1.0-amd64-netinst.iso      2019-09-08 04:37 335M
[ISO]       debian-10.1.0-amd64-xfce-CD-1.iso    2019-09-08 04:38 641M
[ISO]       debian-edu-10.1.0-amd64-netinst.iso  2019-09-08 04:38 405M
[ISO]       debian-mac-10.1.0-amd64-netinst.iso  2019-09-08 04:38 334M
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

In the listing above, the Debian installer image files are accompanied by text files that contain checksums of the files from the various algorithms (MD5, SHA1, SHA256 and SHA512).

```
Note
A checksum is a value derived from a mathematical computation, based on a cryptographic hash function, against a file. There are different types of cryptographic hash functions that vary in strength. The exam will expect you to be familiar with using md5sum, sha256sum and sha512sum.
```

Once you download a file (for example, the `debian-10.1.0-amd64-netinst.iso` image) you would then compare the checksum of the file that was downloaded against a checksum value that was provided for you.

Here is an example to illustrate the point. We will calculate the SHA256 value of the `ftu.txt` file using the `sha256sum` command:

```
$ sha256sum ftu.txt
345452304fc26999a715652543c352e5fc7ee0c1b9deac6f57542ec91daf261c  ftu.txt
```

The long string of characters preceding the file name is the SHA256 checksum value of this text file. Let us create a file that contains that value, so that we can use it to verify the integrity of our original text file. We can do this with the same `sha256sum` command and redirect the output to a file:

```
$ sha256sum ftu.txt > sha256.txt
```

Now, to verify the `ftu.txt` file, we just use the same command and supply the filename that contains our checksum value along with the `-c` switch:

```
$ sha256sum -c sha256.txt
ftu.txt: OK
```

The value contained within the file matches the calculated SHA256 checksum for our `ftu.txt` file, just as we would expect. However, if the original file were modified (such as a few bytes lost during a file download, or someone had deliberately tampered with it) the value check would fail. In such cases we know that our file is bad or corrupted, and we can not trust the integrity of its contents. To prove the point, we will add some text at the end of the file:

```
$ echo "new entry" >> ftu.txt
```

Now we will make an attempt to verify the file’s integrity:

```
$ sha256sum -c sha256.txt
ftu.txt: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
```

And we see that the checksum does not match what was expected for the file. Therefore, we could not trust the integrity of this file. We could attempt to download a new copy of a file, report the failure of the checksum to the sender of the file, or report it to a data center security team depending on the importance of the file.

### Looking Deeper into Files
The octal dump (`od`) command is often used for debugging applications and various files. By itself, the `od` command will just list out a file’s contents in octal format. We can use our `ftu.txt` file from earlier to practice with this command:

```
$ od ftu.txt
0000000 075142 060543 005164 060543 005164 072543 005164 062550
0000020 062141 066012 071545 005163 062155 071465 066565 067012
0000040 005154 062157 070012 071541 062564 071412 062145 071412
0000060 060550 032462 071466 066565 071412 060550 030465 071462
0000100 066565 071412 071157 005164 070163 064554 005164 060564
0000120 066151 072012 005162 067165 070551 073412 005143 075170
0000140 060543 005164 061572 072141 000012
0000151
```

The first column of output is the byte offset for each line of output. Since `od` prints out information in octal format by default, each line begins with the byte offset of eight bits, followed by eight columns, each containing the octal value of the data within that column.

```
Tip
Recall that a byte is 8 bits in length.
```

Should you need to view a file’s contents in hexadecimal format, use the -x option:

```
$ od -x ftu.txt
0000000 7a62 6163 0a74 6163 0a74 7563 0a74 6568
0000020 6461 6c0a 7365 0a73 646d 7335 6d75 6e0a
0000040 0a6c 646f 700a 7361 6574 730a 6465 730a
0000060 6168 3532 7336 6d75 730a 6168 3135 7332
0000100 6d75 730a 726f 0a74 7073 696c 0a74 6174
0000120 6c69 740a 0a72 6e75 7169 770a 0a63 7a78
0000140 6163 0a74 637a 7461 000a
0000151
```

Now each of the eight columns after the byte offset are represented by their hexadecimal equivalents.

One handy use of the `od` command is for debugging scripts. For example, the `od` command can show us characters that are not normally seen that exist within a file, such as newline entries. We can do this with the `-c` option, so that instead of displaying the numerical notation for each byte, these column entries will instead be shown as their character equivalents:

```
$ od -c ftu.txt
0000000   b   z   c   a   t  \n   c   a   t  \n   c   u   t  \n   h   e
0000020   a   d  \n   l   e   s   s  \n   m   d   5   s   u   m  \n   n
0000040   l  \n   o   d  \n   p   a   s   t   e  \n   s   e   d  \n   s
0000060   h   a   2   5   6   s   u   m  \n   s   h   a   5   1   2   s
0000100   u   m  \n   s   o   r   t  \n   s   p   l   i   t  \n   t   a
0000120   i   l  \n   t   r  \n   u   n   i   q  \n   w   c  \n   x   z
0000140   c   a   t  \n   z   c   a   t  \n
0000151
```

All of the newline entries within the file are represented by the hidden `\n` characters. If you just want to view all of the characters within a file, and do not need to see the byte offset information, the byte offset column can be removed from the output like so:

```
$ od -An -c ftu.txt
   b   z   c   a   t  \n   c   a   t  \n   c   u   t  \n   h   e
   a   d  \n   l   e   s   s  \n   m   d   5   s   u   m  \n   n
   l  \n   o   d  \n   p   a   s   t   e  \n   s   e   d  \n   s
   h   a   2   5   6   s   u   m  \n   s   h   a   5   1   2   s
   u   m  \n   s   o   r   t  \n   s   p   l   i   t  \n   t   a
   i   l  \n   t   r  \n   u   n   i   q  \n   w   c  \n   x   z
   c   a   t  \n   z   c   a   t  \n
```
___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)