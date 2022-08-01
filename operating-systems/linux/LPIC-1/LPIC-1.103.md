# LPIC-1: GNU and Unix Commands

## Lesson 1.3-1: Work on the Command Line

Newcomers to the world of Linux administration and the Bash shell often feel a bit lost without the reassuring comforts of a GUI interface. They are used to having right-click access to the visual cues and contextual information that graphic file manager utilities make available. So it is important to quickly learn and master the relatively small set of command line tools through which you can instantly tap into all the data offered by your old GUI — and more.

### Getting System Information
While staring at the flashing rectangle of a command line prompt, your first question will probably be “Where am I?” Or, more precisely, “Where in the Linux filesystem am I right now and if, say, I created a new file, where would it live?” What you are after here is your present work directory, and the `pwd` command will tell you what you want to know:

```
$ pwd
/home/frank
```

Assuming that Frank is currently logged in to the system and he is now in his home directory: `/home/frank/`. Should Frank create an empty file using the `touch` command without specifying any other location in the filesystem, the file will be created within `/home/frank/`. Listing the directory contents using `ls` will show us that new file:

```
$ touch newfile
$ ls
newfile
```

Besides your location in the filesystem, you will often want information about the Linux system you are running. This might include the exact release number of your distribution or the Linux kernel version that is currently loaded. The `uname` tool is what you are after here. And, in particular, `uname` using the `-a` (“all”) option.

```
$ uname -a
Linux base 4.18.0-18-generic #19~18.04.1-Ubuntu SMP Fri Apr 5 10:22:13 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```

Here, `uname` shows that Frank’s machine has the Linux kernel version 4.18.0 installed and is running Ubuntu 18.04 on a 64-bit (`x86_64`) CPU.

### Getting Command Information
You will often come across documentation talking about Linux commands with which you are not yet familiar. The command line itself offers all kinds of helpful information on what commands do and how to effectively use them. Perhaps the most useful information is found within the many files of the `man` system.

As a rule, Linux developers write `man` files and distribute them along with the utilities they create. `man` files are highly structured documents whose contents are intuitively divided by standard section headings. Typing `man` followed by the name of a command will give you information that includes the command name, a brief usage synopsis, a more detailed description, and some important historical and licensing background. Here is an example:

```
$ man uname
UNAME(1)             User Commands            UNAME(1)
NAME
   uname - print system information
SYNOPSIS
   uname [OPTION]...
DESCRIPTION
   Print certain system information.  With no OPTION, same as -s.
   -a, --all
      print  all information, in the following order, except omit -p
      and -i if unknown:
   -s, --kernel-name
      print the kernel name
   -n, --nodename
      print the network node hostname
   -r, --kernel-release
      print the kernel release
   -v, --kernel-version
      print the kernel version
   -m, --machine
      print the machine hardware name
   -p, --processor
      print the processor type (non-portable)
   -i, --hardware-platform
      print the hardware platform (non-portable)
   -o, --operating-system
      print the operating system
   --help display this help and exit
   --version
      output version information and exit
AUTHOR
   Written by David MacKenzie.
REPORTING BUGS
   GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
   Report uname translation bugs to
   <http://translationproject.org/team/>
COPYRIGHT
   Copyright©2017 Free Software Foundation, Inc. License  GPLv3+: GNU
   GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
   This is free software: you are free to change and redistribute it.
   There is NO WARRANTY, to the extent permitted by law.
SEE ALSO
   arch(1), uname(2)
   Full documentation at: <http://www.gnu.org/software/coreutils/uname>
   or available locally via: info '(coreutils) uname invocation'
GNU coreutils 8.28       January 2018             UNAME(1)
```

`man` only works when you give it an exact command name. If, however, you are not sure about the name of the command you are after, you can use the `apropos` command to search through the `man` page names and descriptions. Assuming, for instance, that you cannot remember that it is `uname` that will give you your current Linux kernel version, you can pass the word `kernel` to `apropros`. You will probably get many lines of output, but they should include these:

```
$ apropos kernel
systemd-udevd-kernel.socket (8) - Device event managing daemon
uname (2)            - get name and information about current kernel
urandom (4)          - kernel random number source devices
```

If you do not need a command’s full documentation, you can quickly get basic data about a command using `type`. This example uses `type` to query four separate commands at once. The results show us that `cp` (“copy”) is a program that lives in `/bin/cp` and that kill (change the state of a running process) is a shell builtin — meaning that it is actually a part of the Bash shell itself:

```
$ type uname cp kill which
uname is hashed (/bin/uname)
cp is /bin/cp
kill is a shell builtin
which is /usr/bin/which
```

Notice that, besides being a regular binary command like `cp`, `uname` is also “hashed.” That is because Frank recently used `uname` and, to increase system efficiency, it was added to a hash table to make it more accessible the next time you run it. If he would run `type uname` after a system boot, Frank would find that `type` once again describes `uname` as a regular binary.

```
Note
A quicker way to clean up the hash table is to run the command hash -d.
```

Sometimes — particularly when working with automated scripts — you will need a simpler source of information about a command. The `which` command that our previous `type` command traced for us, will return nothing but the absolute location of a command. This example locates both the `uname` and `which` commands.

```
$ which uname which
/bin/uname
/usr/bin/which
```

```
Note
If you want to display information about “builtin” commands, you can use the help command.
```

### Using Your Command History
You will often carefully research the proper usage for a command and successfully run it along with a complicated trail of options and arguments. But what happens a few weeks later when you need to run the same command with the same options and arguments but cannot remember the details? Rather than having to start your research over again from scratch, you will often be able to recover the original command using `history`.

Typing `history` will return the most recent commands you have executed with the most recent of those appearing last. You can easily search through those commands by piping a specific string to the `grep` command. This example will search for any command that included the text `bash_history`:

```
$ history | grep bash_history
1605 sudo find /home -name ".bash_history" | xargs grep sudo
```

Here a single command is returned along with is sequence number, 1605.

And speaking of `bash_history`, that is actually the name of a hidden file you should find within your user’s home directory. Since it is a hidden file (designated as such by the dot that precedes its filename), it will only be visible by listing the directory contents using `ls` with the `-a` argument:

```
$ ls /home/frank
newfile
$ ls -a /home/frank
.  ..  .bash_history  .bash_logout  .bashrc  .profile  .ssh  newfile
```

What is in the `.bash_history` file? Take a look for yourself: you will see hundreds and hundreds of your most recent commands. You might, however to surprised to find that some of your most recent commands are missing. That is because, while they are instantly added to the dynamic history database, the latest additions to your command `history` are not written to the `.bash_history` file until you exit your session.

You can leverage the contents of `history` to make your command line experience much faster and more efficient using the up and down arrow keys on your keyboard. Hitting the up key multiple times will populate the command line with recent commands. When you get to the one you would like to execute a second time, you can run it by pressing Enter. This makes it easy to recall and, if desired, modify commands multiple times during a shell session.

___

An operating system environment includes the basic tools — like command line shells and sometimes a GUI — that you will need in order to get stuff done. But your environment will also come with a catalog of shortcuts and preset values. Here is where we will learn how to list, invoke, and manage those values.

### Finding Your Environment Variables
So just how do we identify the current values for each of our environment variables? One way is through the `env` command:

```
$ env
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
XDG_RUNTIME_DIR=/run/user/1000
XAUTHORITY=/run/user/1000/gdm/Xauthority
XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
GJS_DEBUG_TOPICS=JS ERROR;JS LOG
[...]
```

You will get a lot of output — much more than what is included in the above excerpt. But for now note the `PATH` entry, which contains the directories where your shell (and other programs) will look for other programs without having to specify a complete path. With that set, you could run a binary program that lives, say, in `/usr/local/bin` from within your home directory and it would run just as though the file was local.

Let us change the subject for a moment. The `echo` command will print to the screen whatever you tell it to. Believe it or not, there will be many times when getting `echo` to literally repeat something will be very useful.

```
$ echo "Hi. How are you?"
Hi. How are you?
```

But there is something else you can do with `echo`. When you feed it the name of an environment variable — and tell it that this is a variable by prefixing the variable name with a `$` — then, instead of just printing the variable’s name, the shell will expand it giving you the value. Not sure whether your favorite directory is currently in the path? You can quickly check by running it through `echo`:

```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

### Creating New Environment Variables
You can add your own custom variables to your environment. The simplest way is to use the `=` character. The string to the left will be the name of your new variable, and the string to the right will be its value. You can now feed the variable name to `echo` to confirm it worked:

```
$ myvar=hello
$ echo $myvar
hello
```

```
Note
Notice that there is no space on either side of the equal sign during variable assignment.
```

But did it really work? Type bash into the terminal to open a new shell. This new shell looks exactly like the one you were just in, but it is actually a child of the original one (which we call the parent). Now, inside this new child shell, try to get echo to do its magic the way it did before. Nothing. What’s going on?

```
$ bash
$ echo $myvar

$
```

A variable you create the way we just did is only going to be available locally — within the immediate shell session. If you start up a new shell — or close down the session using `exit` — the variable will not go along with you. Typing `exit` here will take you back to your original parent shell which, right now, is where we want to be. You can run `echo $myvar` once again if you like just to confirm that the variable is still valid. Now type `export myvar` to pass the variable to any child shells that you may subsequently open. Try it out: type bash for a new shell and then `echo`:

```
$ exit
$ export myvar
$ bash
$ echo $myvar
hello
```

All this may feel a bit silly when we are creating shells for no real purpose. But understanding how shell variables are propagated through your system will become very important once you start writing serious scripts.

### Deleting Environment Variables
Want to know how to clean up all those ephemeral variables you have created? One way is to simply close your parent shell — or reboot your computer. But there are simpler ways. Like, for instance, `unset`. Typing `unset` (without the `$`) will kill the variable. `echo` will now prove that.

```
$ unset myvar
$ echo $myvar

$
```

If there is an `unset` command, then you can bet there must be a `set` command to go with it. Running `set` by itself will display lots of output, but it is really not all that different from what `env` gave you. Look at the first line of output you will get when you filter for `PATH`:

```
$ set | grep PATH
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
[...]
```

What is the difference between `set` and `env`? For our purposes, the main thing is that `set` will output all variables and functions. Let us illustrate that. We will create a new variable called `mynewvar` and then confirm it is there:

```
$ mynewvar=goodbye
$ echo $mynewvar
goodbye
```

Now, running `env` while using `grep` to filter for the string `mynewvar` will not display any output. But running set the same way will show us our local variable.

```
$ env | grep mynewvar

$ set | grep mynewvar
mynewvar=goodbye
```

### Quoting to Escape Special Characters
Now is as good a time as any other to introduce you to the problem of special characters. Alphanumeric characters (a-z and 0-9) will normally be read literally by Bash. If you try to create a new file called `myfile` you would just type `touch` followed by `myfile` and Bash will know what to do with it. But if you want to include a special character in your filename, you will need to do a bit more work.

To illustrate this, we will type `touch` and follow it by the title: `my big file`. The problem is that there are two spaces there between words which Bash will interpret. While, technically, you would not call a space a “character,” it is like one in the sense that Bash will not read it literally. If you list the contents of your current directory, rather than one file called `my big file`, you will see three files named, respectively, `my`, `big`, and `file`. That is because Bash thought you wanted to create multiple files whose names you were passing in a list:

```
$ touch my big file
$ ls
my big file
```

The spaces will be interpreted the same way if you delete (`rm`) the three files all in one command:

```
$ rm my big file
```

Now let us try it the right way. Type `touch` and the three parts of your filename but this time enclose the name in quotation marks. This time it worked. Listing the directory contents will show you a single file with the proper name.

```
$ touch "my big file"
$ ls
'my big file'
```

There are other ways to get the same effect. Single quotes, for instance, work just as well as double quotes. (Note that single quotes will preserve the literal value of all characters, while double quotes will preserve all characters except for `$`, \`, `\` and, on certain cases, `!`.)

```
$ rm 'my big file'
```

Prepending each special character with the backslash will “escape” the specialness of the character and cause Bash to read it literally.

```
$ touch my\ big\ file
```

## Lesson 3-2: Process text streams using filters
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

## Lesson 3-3: Perform basic file management
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

### Lesson 3-4: Use streams, pipes, redirectes
All computer programs follow the same general principle: data received from some source is transformed to generate an intelligible outcome. In Linux shell context, the data source can be a local file, a remote file, a device (like a keyboard), etc. The program’s output is usually rendered on a screen, but is also common to store the output data in a local filesystem, send it to a remote device, play it through audio speakers, etc.

Operating systems inspired by Unix, like Linux, offer a great variety of input/output methods. In particular, the method of file descriptors allows to dynamically associate integer numbers with data channels, so that a process can reference them as its input/output data streams.

Standard Linux processes have three communication channels opened by default: the standard input channel (most times simply called stdin), the standard output channel (stdout) and the standard error channel (stderr). The numerical file descriptors assigned to these channels are `0` to stdin, `1` to stdout and `2` to stderr. Communication channels are also accessible through the special devices `/dev/stdin`, `/dev/stdout` and `/dev/stderr`.

These three standard communication channels allow programmers to write code that reads and writes data without worrying about the kind of media it’s coming from or going to. For example, if a program needs a set of data as its input, it can just ask for data from the standard input and whatever is being used as the standard input will provide that data. Likewise, the simplest method a program can use to display its output is to write it in the standard output. In a standard shell session, the keyboard is defined as the stdin and the monitor screen is defined as the stdout and stderr.

The Bash shell has the ability to reassign the communication channels when loading a program. It allows, for example, to override the screen as the standard output and use a file in the local filesystem as stdout.

### Redirects
The reassignment of a channel’s file descriptor in the shell environment is called a redirect. A redirect is defined by a special character within the command line. For example, to redirect the standard output of a process to a file, the greater than symbol `>` is positioned at the end of the command and followed by the path to the file that will receive the redirected output:

```
$ cat /proc/cpuinfo >/tmp/cpu.txt
```

By default, only the content coming to stdout is redirected. That happens because the numerical value of the file descriptor should be specified just before the greater than symbol and, when not specified, Bash redirects the standard output. Therefore, using `>` is equivalent to use `1>` (the value of stdout’s file descriptor is `1`).

To capture the content of stderr, the redirect `2>` should be used instead. Most command-line programs send debug information and error messages to the standard error channel. It is possible, for example, to capture the error message triggered by an attempt to read a non-existent file:

```
$ cat /proc/cpu_info 2>/tmp/error.txt
$ cat /tmp/error.txt
cat: /proc/cpu_info: No such file or directory
```

Both stdout and stderr are redirected to the same target with `&>` or `>&`. It’s important to not place any spaces beside the ampersand, otherwise Bash will take it as the instruction to run the process in background and not to perform the redirect.

The target must be a path to a writable file, like `/tmp/cpu.txt`, or a writable file descriptor. A file descriptor target is represented by an ampersand followed by the file descriptor’s numerical value. For example, `1>&2` redirects stdout to stderr. To do the opposite, stderr to stdout, `2>&1` should be used instead.

Although not very useful, given that there is a shorter way to do the same task, it is possible to redirect stderr to stdout and then redirect it to a file. For example, a redirect to write both stderr and stdout to a file named `log.txt` can be written as `>log.txt 2>&1`. However, the main reason for redirecting stderr to stdout is to allow parsing of debug and error messages. It is possible to redirect the standard output of a program to the standard input of another program, but it is not possible to directly redirect the standard error to the standard input of another program. Thus, program’s messages sent to stderr first need to be redirected to stdout in order to be read by another program’s stdin.

To just discard the output of a command, its content can be redirected to the special file `/dev/null`. For example, `>log.txt 2>/dev/null` saves the contents of stdout in the file `log.txt` and discards the stderr. The file `/dev/null` is writable by any user but no data can be recovered from it, as it is not stored anywhere.

An error message is presented if the specified target is not writable (if the path points to a directory or a read-only file) and no modification to the target is made. However, an output redirect overwrites an existing writable target without any confirmation. Files are overwritten by output redirects unless Bash option `noclobber` is enabled, which can be done for the current session with the command `set -o noclobber` or `set -C`:

```
$ set -o noclobber
$ cat /proc/cpu_info 2>/tmp/error.txt
-bash: /tmp/error.txt: cannot overwrite existing file
```

To unset the `noclobber` option for the current session, run `set +o noclobber` or `set +C`. To make the `noclobber` option persistent, it must be included in the user’s Bash profile or in the system-wide profile.

Even with the `noclobber` option enabled it is possible to append redirected data to existent content. This is accomplished with a redirection written with two greater than symbols `>>`:

```
$ cat /proc/cpu_info 2>>/tmp/error.txt
$ cat /tmp/error.txt
cat: /proc/cpu_info: No such file or directory
cat: /proc/cpu_info: No such file or directory
```

In the previous example, the new error message was appended to the existing one in file `/tmp/error.txt`. If the file does not exist yet, it will be created with the new data.

The data source of the standard input of a process can be reassigned as well. The less than symbol `<` is used to redirect the content of a file to the stdin of a process. In this case, data flows from right to left: the reassigned descriptor is assumed to be 0 at the left of the less than symbol and the data source (a path to a file) must be at the right of the less than symbol. The command `uniq`, like most command line utilities for processing text, accepts data sent to stdin by default:

```
$ uniq -c </tmp/error.txt
      2 cat: /proc/cpu_info: No such file or directory
```

The `-c` option makes `uniq` display how many times a repeated line appears in the text. As the numeric value of the redirected file descriptor was suppressed, the example command is equivalent to `uniq -c 0</tmp/error.txt`. To use a file descriptor other than `0` in an input redirect only makes sense in specific contexts, because it is possible for a program to ask for data at file descriptors `3`, `4`, etc. Indeed, programs can use any integer above 2 as new file descriptors for data input/output. For example, the following C code reads data from file descriptor `3` and just replicates it to file descriptor `4`:

```
Note
The program must handle such file descriptors correctly, otherwise it could attempt an invalid read or write operation and crash.
```

```
#include <stdio.h>

int main(int argc, char **argv){
  FILE *fd_3, *fd_4;
  // Open file descriptor 3
  fd_3 = fdopen(3, "r");
  // Open file descriptor 4
  fd_4 = fdopen(4, "w");
  // Read from file descriptor 3
  char buf[32];
  while ( fgets(buf, 32, fd_3) != NULL ){
    // Write to file descriptor 4
    fprintf(fd_4, "%s", buf);
  }
  // Close both file descriptors
  fclose(fd_3);
  fclose(fd_4);
}
```

To test it, save the sample code as `fd.c` and compile it with `gcc -o fd fd.c`. This program needs file descriptors 3 and 4 to be available so it can read and write to them. As an example, the previously created file `/tmp/error.txt` can be used as the source for file descriptor `3` and the file descriptor `4` can be redirected to stdout:

```
$ ./fd 3</tmp/error.txt 4>&1
cat: /proc/cpu_info: No such file or directory
cat: /proc/cpu_info: No such file or directory
```

From a programmer’s perspective, using file descriptors avoids having to deal with option parsing and filesystem paths. The same file descriptor can even be used as input and output. In this case, the file descriptor is defined in the command line with both less than and greater than symbols, like in `3<>/tmp/error.txt`.

### Here Document and Here String
Another way to redirect input involve the Here document and Here string methods. The Here document redirect allows to type multi-line text that will be used as the redirected content. Two less than symbols `<<` indicate a Here document redirect:

```
$ wc -c <<EOF
> How many characters
> in this Here document?
> EOF
43
```

At the right of the two less than symbols `<<` is the ending term `EOF`. The insertion mode will finish as soon as a line containing only the ending term is entered. Any other term can be used as the ending term, but it is important to not put blank characters between the less than symbol and the ending term. In the example above, the two lines of text were sent to the stdin of `wc -c` command, which displays the characters count. As with input redirects for files, the stdin (file descriptor `0`) is assumed if the redirected file descriptor is suppressed.

The Here string method is much like the Here document method, but for one line only:

```
$ wc -c <<<"How many characters in this Here string?"
41
```

In this example, the string at the right of the three less than signs is sent to the stdin of `wc -c`, which counts the number of characters. Strings containing spaces must be inside quotes, otherwise only the first word will be used as the Here string and the remaining ones will be passed as arguments to the command.

___

One aspect of the Unix philosophy states that each program should have a specific purpose and should not try to incorporate features outside its scope. But keeping things simple does not mean less elaborated results, because different programs can be chained together to produce a combined output. The vertical bar character `|`, also known as the pipe symbol, can be used to create a pipeline connecting the output of a program directly into the input of another program, whereas command substitution allows to store the output of a program in a variable or use it directly as an argument to another command.

Pipes
Unlike redirects, with pipes data flows from left to right in the command line and the target is another process, not a filesystem path, file descriptor or Here document. The pipe character `|` tells the shell to start all distinct commands at the same time and to connect the output of the previous command to the input of the following command, left to right. For example, instead of using redirects, the content of the file `/proc/cpuinfo` sent to the standard output by `cat` can be piped to the stdin of `wc` with the following command:

```
$ cat /proc/cpuinfo | wc
   208    1184    6096
```

In the absence of a path to a file, `wc` counts the number of lines, words and characters it receives on its stdin, as is the case in the example. Many pipes can be present in a compound command. In the following example, two pipes are used:

```
$ cat /proc/cpuinfo | grep 'model name' | uniq
model name      : Intel(R) Xeon(R) CPU           X5355  @ 2.66GHz
```

The content of file `/proc/cpuinfo` produced by `cat /proc/cpuinfo` was piped to the command `grep 'model name'`, which then selects only the lines containing the term `model name`. The machine running the example has many CPUs, so there are repeated lines with `model name`. The last pipe connects `grep 'model name'` to `uniq`, which is responsible for skipping any line equal to the previous one.

Pipes can be combined with redirects in the same command line. The previous example can be rewritten to a simpler form:

```
$ grep 'model name' </proc/cpuinfo | uniq
model name      : Intel(R) Xeon(R) CPU           X5355  @ 2.66GHz
```

The input redirect for `grep` is not strictly necessary as `grep` accepts a file path as argument, but the example demonstrates how to build such combined commands.

Pipes and redirects are exclusive, that is, one source can be mapped to only one target. Yet, it is possible to redirect an output to a file and still see it on the screen with program `tee`. To do it, the first program sends its output to the stdin of `tee` and a file name is provided to the latter to store the data:

```
$ grep 'model name' </proc/cpuinfo | uniq | tee cpu_model.txt
model name      : Intel(R) Xeon(R) CPU           X5355  @ 2.66GHz
$ cat cpu_model.txt
model name      : Intel(R) Xeon(R) CPU           X5355  @ 2.66GHz
```

The output of the last program in the chain, generated by `uniq`, is displayed and stored in the file `cpu_model.txt`. To not overwrite the content of the provided file but to append data to it, the option `-a` must be provided to `tee`.

Only the standard output of a process is captured by a pipe. Let’s say you must to go through a long compilation process on the screen and at the same time save both the standard output and the standard error to a file for later inspection. Assuming your current directory does not have a Makefile, the following command will output an error:

```
$ make | tee log.txt
make: *** No targets specified and no makefile found.  Stop.
```

Although shown on the screen, the error message generated by `make` was not captured by `tee` and the file log.txt was created empty. A redirect needs to be done before a pipe can capture the stderr:

```
$ make 2>&1 | tee log.txt
make: *** No targets specified and no makefile found.  Stop.
$ cat log.txt
make: *** No targets specified and no makefile found.  Stop.
```

In this example the stderr of `make` was redirected to the stdout, so `tee` was able to capture it with a pipe, display it on the screen and save it in the file `log.txt`. In cases like this, it may be useful to save the error messages for later inspection.

### Command Substitution
Another method to capture the output of a command is command substitution. By placing a command inside backquotes, Bash replaces it with its standard output. The following example shows how to use the stdout of a program as an argument to another program:

```
$ mkdir `date +%Y-%m-%d`
$ ls
2019-09-05
```

The output of the program `date`, the current date formatted as year-month-day, was used as an argument to create a directory with `mkdir`. An identical result is obtained by using `$()` instead of backquotes:

```
$ rmdir 2019-09-05
$ mkdir $(date +%Y-%m-%d)
$ ls
2019-09-05
```

The same method can be used to store the output of a command as a variable:

```
$ OS=`uname -o`
$ echo $OS
GNU/Linux
```

The command `uname -o` outputs the generic name of the current operating system, which was stored in the session variable `OS`. To assign the output of a command to a variable is very useful in scripts, making it possible to store and evaluate the data in many distinct ways.

Depending on the output generated by the replaced command, the builtin command substitution may not be appropriate. A more sophisticated method to use the output of a program as the argument of another program employs an intermediate called `xargs`. The program `xargs` uses the contents it receives via stdin to run a given command with the contents as its argument. The following example shows `xargs` running the program `identify` with arguments provided by program `find`:

```
$ find /usr/share/icons -name 'debian*' | xargs identify -format "%f: %wx%h\n"
debian-swirl.svg: 48x48
debian-swirl.png: 22x22
debian-swirl.png: 32x32
debian-swirl.png: 256x256
debian-swirl.png: 48x48
debian-swirl.png: 16x16
debian-swirl.png: 24x24
debian-swirl.svg: 48x48
```

The program `identify` is part of ImageMagick, a set of command-line tools to inspect, convert and edit most image file types. In the example, `xargs` took all paths listed by `find` and put them as arguments to `identify`, which then shows the information for each file formatted as required by the option `-format`. The files found by `find` in the example are images containing the distribution logo in a Debian filesystem. `-format` is a parameter to identify, not to xargs.

Option `-n 1` requires `xargs` to run the given command with only one argument at a time. In the example’s case, instead of passing all paths found by `find` as a list of arguments to `identify`, using `xargs -n 1` would execute command `identify` for each path separately. Using `-n 2` would execute `identify` with two paths as arguments, `-n 3` with three paths as arguments and so on. Similarly, when `xargs` process multiline contents — as is the case with input provided by `find` — the option `-L` can be used to limit how many lines will be used as arguments per command execution.

```
Note
Using xargs with option -n 1 or -L 1 to process output generated by find may be unnecessary. Command find has the option -exec to run a given command for each search result item.
```

If the paths have space characters, it is important to run `find` with the option `-print0`. This option instructs `find` to use a null character between each entry so the list can be correctly parsed by `xargs` (the output was suppressed):

```
$ find . -name '*avi' -print0 -o -name '*mp4' -print0 -o -name '*mkv' -print0 | xargs -0 du | sort -n
```

The option `-0` tells `xargs` the null character should be used as the separator. That way the file paths given by `find` are correctly parsed even if they have blank or other special characters in it. The previous example shows how to use the command `du` to find out the disk usage of every file found and then sort the results by size. The output was suppressed for conciseness. Note that for each search criteria it is necessary to place the `-print0` option for `find`.

By default, `xargs` places the arguments of the executed command last. To change that behavior, the option `-I` should be used:

```
$ find . -mindepth 2 -name '*avi' -print0 -o -name '*mp4' -print0 -o -name '*mkv' -print0 | xargs -0 -I PATH mv PATH ./
```

In the last example, every file found by `find` is moved to the current directory. As the source path(s) must be informed to `mv` before the target path, a substitution term is given to the option `-I` of `xargs` which is then appropriately placed alongside `mv`. By using the null character as separator, it is not necessary to enclose the substitution term with quotes.

___

## Lesson 3-5 Create, monitor and kill processes
Every time we invoke a command, one or more processes are started. A well-trained system administrator not only needs to create processes, but also be able to keep track of them and send them different types of signals if and when required. In this lesson we will look at job control and how to monitor processes.

### Job Control
Jobs are processes that have been started interactively through a terminal, sent to the background and have not yet finished execution. You can find out about the active jobs (and their status) in your Linux system by running `jobs`:

```
$ jobs
```

The `jobs` command above did not produce any output, which means there are no active jobs at the moment. Let us create our first job by running a command that takes some time to finish executing (the `sleep` command with a parameter of `60`) and — while running — press `Ctrl`+`Z`:

```
$ sleep 60
^Z
[1]+  Stopped                 sleep 60
```

The execution of the command has been stopped (or — rather — suspended) and the command prompt is available again. You can look for jobs a second time and will find the suspended one now:

```
$ jobs
[1]+  Stopped                 sleep 60
```

Let us explain the output:

`[1]`
- This number is the job ID and can be used — preceded by a percentage symbol (`%`) — to change the job status by the `fg`, `bg` and `kill` utilities (as you will be shown later).

`+`
- The plus sign indicates the current, default job (that is, the last one being suspended or sent to the background). The previous job is flagged with a minus sign (`-`). Any other prior jobs are not flagged.

`Stopped`
- Description of the job status.

`sleep 60`
- The command or job itself.

With the `-l` option, jobs will additionally display the process ID (PID) right before the status:

```
$ jobs -l
[1]+  1114 Stopped                 sleep 60
```

The remaining possible options of jobs are:

`-n`
- Lists only processes that have changed status since the last notification. Possible status include, `Running`, `Stopped`, `Terminated` or `Done`.

`-p`
- Lists process IDs.

`-r`
- Lists only running jobs.

`-s`
- Lists only stopped (or suspended) jobs.

```
Note
Remember, a job has both a job ID and a process ID (PID).
```

### Job Specification
The `jobs` command as well as other utilities such as `fg`, `bg` and `kill` (that you will see in the next section) need a job specification (or `jobspec`) to act upon a particular job. As we have just seen, this can be — and normally is — the job ID preceded by `%`. However, other job specifications are also possible. Let us have a look at them:

`%n`
- Job whose id number is n:

```
$ jobs %1
[1]+  Stopped                 sleep 60
```

`%str`

- Job whose command line starts with str:

```
$ jobs %sl
[1]+  Stopped                 sleep 60
```

`%?str`
- Job whose command line contains str:
```
$ jobs %?le
[1]+  Stopped                 sleep 60
```

`%+ or %%`
- Current job (the one that was last started in the background or suspended from the foreground):
```
$ jobs %+
[1]+  Stopped                 sleep 60
```

`%-`
- Previous job (the one that was %+ before the default, current one):

```
$ jobs %-
[1]+  Stopped                 sleep 60
```

In our case, since there is only one job, it is both current and previous.

### Job Status: Suspension, Foreground and Background
Once a job is in the background or has been suspended, we can do any of three things to it:

- Take it to the foreground with `fg`:

```
$ fg %1
sleep 60
```

`fg` moves the specified job to the foreground and makes it the current job. Now we can wait until it finishes, stop it again with `Ctrl`+`Z` or terminate it with `Ctrl`+`C`.

Take it to the background with `bg`:

```
$ bg %1
[1]+ sleep 60 &
```

Once in the background, the job can be brought back into the foreground with `fg` or killed (see below). Note the ampersand (`&`) meaning the job has been sent to the background. As a matter of fact, you can also use the ampersand to start a process directly in the background:

```
$ sleep 100 &
[2] 970
```

Together with the job ID of the new job (`[2]`), we now get its process ID (`970`) too. Now both jobs are running in the background:

```
$ jobs
[1]-  Running                 sleep 60 &
[2]+  Running                 sleep 100 &
```

A bit later the first job finishes execution:

```
$ jobs
[1]-  Done                    sleep 60
[2]+  Running                 sleep 100 &
---
```

Terminate it through a `SIGTERM` signal with `kill`:

```
$ kill %2
```

To make sure the job has been terminated, run `jobs` again:

```
$ jobs
[2]+  Terminated                 sleep 100
```

```
Note
If no job is specified, fg and bg will act upon the current, default one. kill, however, always needs a job specification.
```

### Detached Jobs: `nohup`
The jobs we have seen in the previous sections were all attached to the session of the user who invoked them. That means that if the session is terminated, the jobs are gone. However, it is possible to detach jobs from sessions and have them run even after the session is closed. This is achieved with the `nohup` (“no hangup”) command. The syntax is as follows:

`nohup COMMAND &`
Remember, the `&` sends the process into the background and frees up the terminal you are working at.

Let us detach the background job `ping localhost` from the current session:

```
$ nohup ping localhost &
[1] 1251
$ nohup: ignoring input and appending output to 'nohup.out'
^C
```

The output shows us the job ID (`[1]`) and the PID (`1251`), followed by a message telling us about the file `nohup.out`. This is the default file where `stdout` and `stderr` will be saved. Now we can press `Ctrl`+`C` to free up the command prompt, close the session, start another one as `root` and use `tail -f` to check if the command is running and output is being written to the default file:

```
$ exit
logout
$ tail -f /home/carol/nohup.out
64 bytes from localhost (::1): icmp_seq=3 ttl=64 time=0.070 ms
64 bytes from localhost (::1): icmp_seq=4 ttl=64 time=0.068 ms
64 bytes from localhost (::1): icmp_seq=5 ttl=64 time=0.070 ms
^C
```

```
Tip
Instead of using the default nohup.out you could have specified the output file of your choice with nohup ping localhost > /path/to/your/file &.
```

If we want to kill the process, we should specify its PID:

```
# kill 1251
```

### Process Monitoring
A process or task is an instance of a running program. Thus, you create new processes every time you type in commands into the terminal.

The `watch` command executes a program periodically (2 seconds by default) and allows us to watch the program’s output change over time. For instance, we can monitor how the load average changes as more processes are run by typing `watch uptime`:

```
Every  2.0s: uptime          debian: Tue Aug 20 23:31:27 2019

 23:31:27 up 21 min,  1 user,  load average: 0.00, 0.00, 0.00
```

The command runs until interrupted so we would have to stop it with `Ctrl`+`C`. We get two lines as output: the first one corresponds to `watch` and tells us how often the command will be run (`Every 2.0s: uptime`), what command/program to watch (`uptime`) as well as the hostname and date (`debian: Tue Aug 20 23:31:27 2019`). The second line of output is uptime’s and includes the time (`23:31:27`), how much time the system has been up (`up 21 min`), the number of active users (`1 user`) and the average system load or number of processes in execution or in waiting state for the last 1, 5 and 15 minutes (`load average: 0.00, 0.00, 0.00`).

Similarly, you can check on memory use as new processes are created with `watch free`:


```
Every  2.0s: free            debian: Tue Aug 20 23:43:37 2019

 23:43:37 up 24 min,  1 user,  load average: 0.00, 0.00, 0.00
              total        used        free      shared  buff/cache   available
Mem:       16274868      493984    14729396       35064     1051488    15462040
Swap:      16777212           0    16777212
```

To change the update interval for `watch` use the `-n` or `--interval` options plus the number of seconds as in:

```
$ watch -n 5 free
```

Now the `free` command will run every 5 seconds.

For more information about the options for `uptime`, `free` and `watch`, please refer to their manual pages.

```
Note
The information provided by uptime and free is also integrated in the more comprehensive tools top and ps (see below).
```

### Sending Signals to Processes: kill
Every single process has a unique process identifier or PID. One way of finding out the PID of a process is by using the `pgrep` command followed by the process' name:

```
$ pgrep sleep
1201
```

```
Note
A process' identifier can also be discovered through the pidof command (e.g. pidof sleep).
```

Similar to `pgrep`, `pkill` command kills a process based on its name:

```
$ pkill sleep
[1]+  Terminated              sleep 60
```

To kill multiple instances of the same process, the `killall` command can be used:

```
$ sleep 60 &
[1] 1246
$ sleep 70 &
[2] 1247
$ killall sleep
[1]-  Terminated              sleep 60
[2]+  Terminated              sleep 70
```

Both `pkill` and `killall` work much in the same way as `kill` in that they send a terminating signal to the specified process(es). If no signal is provided, the default of `SIGTERM` is sent. However, `kill` only takes either a job or a process ID as an argument.

Signals can be specified either by:

- Name:
```
$ kill -SIGHUP 1247
```
- Number:
```
$ kill -1 1247
```
Option:
```
$ kill -s SIGHUP 1247
```

To have `kill` work in a similar way to `pkill` or `killall` (and save ourselves the commands to find out PIDs first) we can use command substitution:

```
$ kill -1 $(pgrep sleep)
```

As you should already know, an alternative syntax is `kill -1 pgrep sleep`.

```
Tip
For an exhaustive list of all kill signals and their codes, type kill -l into the terminal. Use -KILL (-9 or -s KILL) to kill rebel processes when any other signal fails.
```

### `top` and `ps`
When it comes to process monitoring, two invaluable tools are `top` and `ps`. Whilst the former produces output dynamically, the latter does it statically. In any case, both are excellent utilities to have a comprehensive view of all processes in the system.

#### Interacting with top
To invoke `top`, simply type `top`:

```
$ top

top - 11:10:29 up  2:21,  1 user,  load average: 0,11, 0,20, 0,14
Tasks:  73 total,   1 running,  72 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,0 us,  0,3 sy,  0,0 ni, 99,7 id,  0,0 wa,  0,0 hi,  0,0 si,  0,0 st
KiB Mem :  1020332 total,   909492 free,    38796 used,    72044 buff/cache
KiB Swap:  1046524 total,  1046524 free,        0 used.   873264 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
   436 carol     20   0   42696   3624   3060 R  0,7  0,4   0:00.30 top
     4 root      20   0       0      0      0 S  0,3  0,0   0:00.12 kworker/0:0
   399 root      20   0   95204   6748   5780 S  0,3  0,7   0:00.22 sshd
     1 root      20   0   56872   6596   5208 S  0,0  0,6   0:01.29 systemd
     2 root      20   0       0      0      0 S  0,0  0,0   0:00.00 kthreadd
     3 root      20   0       0      0      0 S  0,0  0,0   0:00.02 ksoftirqd/0
     5 root       0 -20       0      0      0 S  0,0  0,0   0:00.00 kworker/0:0H
     6 root      20   0       0      0      0 S  0,0  0,0   0:00.00 kworker/u2:0
     7 root      20   0       0      0      0 S  0,0  0,0   0:00.08 rcu_sched
     8 root      20   0       0      0      0 S  0,0  0,0   0:00.00 rcu_bh
     9 root      rt   0       0      0      0 S  0,0  0,0   0:00.00 migration/0
    10 root       0 -20       0      0      0 S  0,0  0,0   0:00.00 lru-add-drain
    (...)
```

`top` allows the user some interaction. By default, the output is sorted by the percentage of CPU time used by each process in descending order. This behavior can be modified by pressing the following keys from within `top`:

`M`
- Sort by memory usage.

`N`
- Sort by process ID number.

`T`
- Sort by running time.

`P`
- Sort by percentage of CPU usage.

```
Tip
To switch between descending/ascending order just press R.
```

Other interesting keys to interact with `top` are:

`?` or `h`
- Help.

`k`
- Kill a process. `top` will ask for the `PID` of the process to be killed as well as for the signal to be sent (by default `SIGTERM` or `15`).

`r`
- Change the priority of a process (`renice`). `top` will ask you for the `nice` value. Possible values range from -20 through 19, but only the superuser (`root`) can set it to a value which is negative or lower than the current one.

`u`
- List processes from a particular user (by default processes from all users are shown).

`c`
- Show programs' absolute paths and differentiate between userspace processes and kernelspace processes (in square brackets).

`V`
- Forest/hierarchy view of processes.

`t` and `m`
Change the look of CPU and memory readings respectively in a four-stage cycle: the first two presses show progress bars, the third hides the bar and the fourth brings it back.

`W`
Save configuration settings to `~/.toprc`.

```
Tip
A fancier and more user-friendly version of top is htop. Another — perhaps more exhaustive — alternative is atop. If not already installed in your system, use your package manager to install them and give them a try.
```

#### An Explanation of the output of `top`
`top` output is divided into two areas: the summary area and the task area.

#### The Summary Area in `top`
The summary area is made up of the the five top rows and gives us the following information:

`top - 11:10:29 up 2:21, 1 user, load average: 0,11, 0,20, 0,14`
- current time (in 24-hour format): 11:20:29
- uptime (how much time the system has been up and running): `up 2:21`
- number of users logged in and load average of the CPU for the last 1, 5 and 15 minutes, respectively: `load average: 0,11, 0,20, 0,14`

`Tasks: 73 total, 1 running, 72 sleeping, 0 stopped, 0 zombie (information about the processes)`
- total number of processes in active mode: `73 total`
- running (those being executed): `1 running`
- sleeping (those waiting to resume execution): `72 sleeping`
- stopped (by a job control signal): `0 stopped`
- zombie (those which have completed execution but are still waiting for their parent process to remove them from the process table): `0 zombie`

`%Cpu(s): 0,0 us, 0,3 sy, 0,0 ni, 99,7 id, 0,0 wa, 0,0 hi, 0,0 si, 0,0 st (percentage of CPU time spent on)`
- user processes: `0,0 us`
- system/kernel processes: `0,4 sy`
- processes set to a nice value — the nicer the value, the lower the priority: `0,0 ni`
- nothing — idle CPU time: `99,7 id`
- processes waiting for I/O operations: `0,0 wa`
- processes serving hardware interrupts — peripherals sending the processor signals that require attention: `0,0 hi`
- processes serving software interrupts: `0,0 si`
- processes serving other virtual machine’s tasks in a virtual environment, hence steal time: `0,0 st`

`KiB Mem : 1020332 total, 909492 free, 38796 used, 72044 buff/cache (memory information in kilobytes)`
- the total amount of memory: `1020332 total`
- unused memory: `909492 free`
- memory in use: `38796 used`
- the memory which is buffered and cached to avoid excessive disk access: `72044 buff/cache`
  - Notice how the total is the sum of the other three values — `free`, `used` and `buff/cache` — (roughly 1 GB in our case).

`KiB Swap: 1046524 total, 1046524 free, 0 used. 873264 avail Mem (swap information in kilobytes)`
- the total amount of swap space: `1046524 total`
- unused swap space: `1046524 free`
- swap space in use: `0 used
- the amount of swap memory that can be allocated to processes without causing more swapping: `873264 avail Mem`

#### The Task Area in `top`: Fields and Columns
Below the summary area there comes the task area, which includes a series of fields and columns reporting information about the running processes:

`PID`
- Process identifier.

`USER`
- User who issued the command that generated the process.

`PR`
- Priority of process to the kernel.

`NI`
- Nice value of process. Lower values have a higher priority than higher ones.

`VIRT`
- Total amount of memory used by process (including Swap).

`RES`
- RAM memory used by process.

`SHR`
- Shared memory of the process with other processes.

`S`
- Status of process. Values include: `S` (interruptible sleep — waiting for an event to finish), `R` (runnable — either executing or in the queue to be executed) or `Z` (zombie — terminated child processes whose data structures have not yet been removed from the process table).

`%CPU`
- Percentage of CPU used by process.

`%MEM`
- Percentage of RAM used by process, that is, the RES value expressed as a percentage.

`TIME+`
- Total time of activity of process.

`COMMAND`
- Name of command/program which generated the process.

#### Viewing processes statically: `ps`
As said above, `ps` shows a snapshot of processes. To see all processes with a terminal (tty), type `ps a`:

```
$ ps a
  PID TTY      STAT   TIME COMMAND
  386 tty1     Ss+    0:00 /sbin/agetty --noclear tty1 linux
  424 tty7     Ssl+   0:00 /usr/lib/xorg/Xorg :0 -seat seat0 (...)
  655 pts/0    Ss     0:00 -bash
 1186 pts/0    R+     0:00 ps a
 (...)
```

#### An Explanation of `ps` Option Syntax and Output
Concerning options, `ps` can accept three different styles: BSD, UNIX and GNU. Let us see how each of these styles would work when reporting information about a particular process ID:

`BSD`
- Options do not follow any leading dash:
```
$ ps p 811
  PID TTY      STAT   TIME COMMAND
  811 pts/0    S      0:00 -su
```

`UNIX`
- Options do follow a leading dash:
```
$ ps -p 811
  PID TTY          TIME CMD
  811 pts/0    00:00:00 bash
```

`GNU`
- Options are followed by double leading dashes:
```
$ ps --pid 811
  PID TTY          TIME CMD
  811 pts/0    00:00:00 bash
```

In all three cases, `ps` reports information about the process whose `PID` is `811` — in this case, `bash`.

Similarly, you can use `ps` to search for the processes started by a particular user:
- `ps U carol` (BSD)
- `ps -u carol` (UNIX)
- `ps --user carol` (GNU)

Let us check on the processes started by `carol`:

```
$ ps U carol
  PID TTY      STAT   TIME COMMAND
  811 pts/0    S      0:00 -su
  898 pts/0    R+     0:00 ps U carol
```

She started two processes: `bash` (`-su`) and `ps` (`ps U carol`). The `STAT` column tells us the state of the process (see below).

We can get the best out of `ps` by combining some of its options. A very useful command (producing an output similar to that of `top`) is `ps aux` (BSD style). In this case, processes from all shells (not only the current one) are shown. The meaning of the switches are the following:

`a`
- Show processes that are attached to a tty or terminal.

`u`
- Display user-oriented format.

`x`
- Show processes that are not attached to a tty or terminal.

```
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 204504  6780 ?        Ss   14:04   0:00 /sbin/init
root         2  0.0  0.0      0     0 ?        S    14:04   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    14:04   0:00 [ksoftirqd/0]
root         5  0.0  0.0      0     0 ?        S<   14:04   0:00 [kworker/0:0H]
root         7  0.0  0.0      0     0 ?        S    14:04   0:00 [rcu_sched]
root         8  0.0  0.0      0     0 ?        S    14:04   0:00 [rcu_bh]
root         9  0.0  0.0      0     0 ?        S    14:04   0:00 [migration/0]
(...)
```

Let us explain the columns:

`USER`
- Owner of process.

`PID`
- Process identifier.

`%CPU`
- Percentage of CPU used.

`%MEM`
- Percentage of physical memory used.

`VSZ`
- Virtual memory of process in KiB.

`RSS`
- Non-swapped physical memory used by process in KiB.

`TT`
- Terminal (`tty`) controlling the process.

`STAT`
Code representing the state of process. Apart from `S`, `R` and `Z` (that we saw when describing the output of `top`), other possible values include: `D` (uninterruptible sleep — usually waiting for I/O), `T` (stopped — normally by a control signal). Some extra modifier include: `<` (high-priority — not nice to other processes), `N` (low-priority — nice to other processes), or `+` (in the foreground process group).

`STARTED`
- Time at which the process started.

`TIME`
- Accumulated CPU time.

`COMMAND`
- Command that started the process.
___

The tools and utilities seen in the previous lesson are very useful for process monitoring at large. However, a system administrator may need to go one step further. In this lesson we will discuss the concept of terminal multiplexers and learn about GNU Screen and tmux as — despite today’s modern and great terminal emulators — multiplexers still preserve some cool, powerful features for a productive system administrator.

### Features of Terminal Multiplexers
In electronics, a multiplexer (or mux) is a device that allows for multiple inputs to be connected to a single output. Thus, a terminal multiplexer gives us the ability to switch between different inputs as required. Although not exactly the same, `screen` and `tmux` share a series of common features:

- Any successful invocation will result in at least a session which — in turn — will include at least a window. Windows contain programs.
- Windows can be split into regions or panes — which can aid in productivity when working with various programs simultaneously.
- Ease of control: to run most commands, they use a key combination — the so-called command prefix or command key — followed by another character.
- Sessions can be detached from their current terminal (that is, programs are sent to the background and continue to run). This guarantees complete execution of programs no matter if we accidentally close a terminal, experience an occasional terminal freeze or even remote connection loss.
- Socket connection.
- Copy mode.
- They are highly customizable.

### GNU Screen
In the earlier days of Unix (1970s - 80s) computers basically consisted of terminals connected to a central computer. That was all, no multiple windows or tabs. And that was the reason behind the creation of GNU Screen in 1987: emulate multiple independent VT100 screens on a single physical terminal.

### Windows
GNU Screen is invoked just by typing `screen` into the terminal. You will first see a welcome message:

```
GNU Screen version 4.05.00 (GNU) 10-Dec-16

Copyright (c) 2010 Juergen Weigert, Sadrul Habib Chowdhury
Copyright (c) 2008, 2009 Juergen Weigert, Michael Schroeder, Micah Cowan, Sadrul Habib Chowdhury
Copyright (c) 1993-2002, 2003, 2005, 2006, 2007 Juergen Weigert, Michael Schroeder
Copyright (c) 1987 Oliver Laumann
(...)
```

Press Space or Enter to close the message and you will be shown a command prompt:

```
$
```

It may seem nothing has happened but the fact is that `screen` has already created and managing its first session and window. Screen’s command prefix is `Ctrl`+`a`. To see all windows at the bottom of the terminal display, type `Ctrl`+`a`-`w`:

```
0*$ bash
```

There it is, our one and only window so far! Note that the count starts at 0, though. To create another window, type `Ctrl`+`a`-`c`. You will see a new prompt. Let us start `ps` in that new window:

```
$ ps
  PID TTY          TIME CMD
  974 pts/2    00:00:00 bash
  981 pts/2    00:00:00 ps
```

and type `Ctrl`+`a`-`w` again:

```
0-$ bash  1*$ bash
```

There we have our two windows (note the asterisk indicating the one that is being shown at the moment). However, as they were started with Bash, they are both given the same name. Since we invoked `ps` in our current window, let us rename it with that same name. For that, you have to type `Ctrl`+`a`-`A` and write the new window name (`ps`) when prompted:

```
Set window's title to: ps
```

Now, let us create another window but provide it a name from the start: `yetanotherwindow`. This is done by invoking `screen` with the `-t` switch:

```
$ screen -t yetanotherwindow
```

You can move between windows in different ways:

- By using Ctrl+a-n (go to next window) and Ctrl+a-p (go to previous window).
- By using Ctrl+a-number (go to window number number).
- By using Ctrl+a-" to see a list of all windows. You can move up and down with the arrow keys and select the one you want with Enter:

```
Num Name                                                Flags

   0 bash                                                   $
   1 ps                                                     $
   2 yetanotherwindow
```

While working with windows it is important to remember the following:

- Windows run their programs completely independent of each other.
- Programs will continue to run even if their window is not visible (also when the screen session is detached as we will see shortly).

To remove a window, simply terminate the program running in it (once the last window is removed, `screen` will itself terminate). Alternatively, use `Ctrl`+`a`-`k` while in the window you want to remove; you will be asked for confirmation:

```
Really kill this window [y/n]

Window 0 (bash) killed.
```

### Regions
`screen` can divide a terminal screen up into multiple regions in which to accommodate windows. These divisions can be either horizontal (`Ctrl`+`a`-`S`) or vertical (`Ctrl`+`a`-`|`).

The only thing the new region will show is just `--` at the bottom meaning it is empty:

```
   1 ps                                                               --
```

To move to the new region, type `Ctrl`+`a`-`Tab`. Now you can add a window by any of the methods that we have already seen, for example: `Ctrl`+`a`-`2`. Now the `--` should have turned into `2 yetanotherwindow`:

```
$ ps                                                  $
  PID TTY          TIME CMD
 1020 pts/2    00:00:00 bash
 1033 pts/2    00:00:00 ps
$ screen -t yetanotherwindow




   1 ps                                                                2 yetanotherwindow
```
   
Important aspects to keep in mind when working with regions are:

- You move between regions by typing `Ctrl`+`a`-`Tab`.
- You can terminate all regions except the current one with `Ctrl`+`a`-`Q`.
- You can terminate the current region with `Ctrl`+`a`-`X`.
- Terminating a region does not terminate its associated window.

### Sessions
So far we have played with a few windows and regions, but all belonging to the same and only session. It is time to start playing with sessions. To see a list of all sessions, type `screen -list` or `screen -ls`:

```
$ screen -list
There is a screen on:
        1037.pts-0.debian       (08/24/19 13:53:35)     (Attached)
1 Socket in /run/screen/S-carol.
```

That is our only session so far:

PID
- `1037`

Name
- `pts-0.debian` (indicating the terminal — in our case a pseudo terminal slave — and the hostname).

Status
- `Attached`

Let us create a new session giving it a more descriptive name:

```
$ screen -S "second session"
```

The terminal display will be cleared and you will be given a new prompt. You can check for sessions once more:

```
$ screen -ls
There are screens on:
        1090.second session     (08/24/19 14:38:35)     (Attached)
        1037.pts-0.debian       (08/24/19 13:53:36)     (Attached)
2 Sockets in /run/screen/S-carol.
```

To kill a session, quit out of all its windows or just type the command `screen -S SESSION-PID -X quit` (you can also provide the session name instead). Let us get rid of our first session:

```
$ screen -S 1037 -X quit
```

You will be sent back to your terminal prompt outside of `screen`. But remember, our second session is still alive:

```
$ screen -ls
There is a screen on:
	1090.second session	(08/24/19 14:38:35)	(Detached)
1 Socket in /run/screen/S-carol.
```

However, since we killed its parent session, it is given a new label: `Detached`.

### Session Detachment
For a number of reasons you may want to detach a screen session from its terminal:
- To let your computer at work do its business and connect remotely later from home.
- To share a session with other users.

You detach a session with the key combination `Ctrl`+`a`-`d`. You will be taken back into your terminal:

```
[detached from 1090.second session]
$
```

To attach again to the session, you use the command `screen -r SESSION-PID`. Alternatively, you can use the `SESSION-NAME` as we saw above. If there is only one detached session, neither is compulsory:

```
$ screen -r
```

This command suffices to reattach to our second session:

```
$ screen -ls
There is a screen on:
        1090.second session      (08/24/19 14:38:35)     (Attached)
1 Socket in /run/screen/S-carol.
```

Important options for session reattaching:

`-d -r`
- Reattach a session and — if necessary — detach it first.

`-d -R`
- Same as `-d -r` but `screen` will even create the session first if it does not exist.

`-d -RR`
- Same as `-d -R`. However, use the first session if more than one is available.

`-D -r`
- Reattach a session. If necessary, detach and logout remotely first.

`-D -R`
- If a session is running, then reattach (detaching and logging out remotely first if necessary). If it was not running create it and notify the user.

`-D -RR`
- Same as `-D -R` — only stronger.

`-d -m`
- Start `screen` in detached mode. This creates a new session but does not attach to it. This is useful for system startup scripts.

`D -m`
- Same as `-d -m`, but does not fork a new process. The command exits if the session terminates.

Read the manual pages for `screen` to find out about other options.

### Copy & Paste: Scrollback Mode
GNU Screen features a copy or scrollback mode. Once entered, you can move the cursor in the current window and through the contents of its history using the arrow keys. You can mark text and copy it across windows. The steps to follow are:

- Enter copy/scrollback mode: Ctrl+a-[.
- Move to the beginning of the piece of text you want to copy using the arrow keys.
- Mark the beginning of the piece of text you want to copy: Space.
- Move to the end of the piece of text you want to copy using the arrow keys.
- Mark the end of the piece of text you want to copy: Space.
- Go to the window of your choice and paste the piece of text: `Ctrl`+`a`-`]`.

### Customization of screen
The system-wide configuration file for screen is `/etc/screenrc`. Alternatively, a user-level `~/.screenrc` can be used. The file includes four main configuration sections:

`SCREEN SETTINGS`
- You can define general settings by specifying the directive followed by a space and the value as in: `defscrollback 1024`.

`SCREEN KEYBINDINGS`
This section is quite interesting as it allows you to redefine keybindings that perhaps interfere with your everyday use of the terminal. Use the keyword `bind` followed by a Space, the character to use after the command prefix, another Space and the command as in: `bind l kill` (this setting will change the default way of killing a window to `Ctrl`+`a`-`l`).

To display all of screen’s bindings, type `Ctrl`+`a`-`?` or consult the manual page.

```
Tip
Of course, you can also change the command prefix itself. For example to go from Ctrl+a to Ctrl+b, just add this line: escape ^Bb.
```

`TERMINAL SETTINGS`
- This section includes settings related to terminal window sizes and buffers — amongst others. To enable non-blocking mode to better cope with unstable ssh connections, for instance, the following configuration is used: `defnonblock 5`.

`STARTUP SCREENS`
You can include commands to have various programs running on `screen` startup; for example: `screen -t top top` (screen will open a window named `top` with `top` inside).

### tmux
`tmux` was released in 2007. Although very similar to screen, it includes a few notable differences:

- Client-server model: the server supplies a number of sessions, each of which may have a number of windows linked to it that can, in turn, be shared by various clients.
- Interactive selection of sessions, windows and clients via menus.
- The same window can be linked to a number of sessions.
- Availability of both vim and Emacs key layouts.
- UTF-8 and 256-colour terminal support.

### Windows
`tmux` can be invoked by simply typing `tmux` at the command prompt. You will be shown a shell prompt and a status bar at the bottom of the window:

```
[0] 0:bash*                                                        "debian" 18:53 27-Aug-19
```

Other than the `hostname`, the time and the date, the status bar provides the following information:

Session name
- `[0]`

Window number
- `0:`

Window name
- `bash*`. By default this is the name of the program running inside the window and — unlike `screen` — `tmux` will automatically update it to reflect the current running program. Note the asterisk indicating that this is the current, visible window.

You can assign session and window names when invoking `tmux`:

```
$ tmux new -s "LPI" -n "Window zero"
```

The status bar will change accordingly:

```
[LPI] 0:Window zero*                                                 "debian" 19:01 27-Aug-19
```

tmux’s command prefix is `Ctrl`+`b`. To create a new window, just type `Ctrl`+`b`-`c`; you will be taken to a new prompt and the status bar will reflect the new window:

```
[LPI] 0:Window zero- 1:bash*                                         "debian" 19:02 27-Aug-19
```

As Bash is the underlying shell, the new window is given that name by default. Start `top` and see how the name changes to `top`:

```
[LPI] 0:Window zero- 1:top*                                         "debian" 19:03 27-Aug-19
```

In any case, you can rename a window with `Ctrl`+`b`-`,`. When prompted, supply the new name and hit Enter:

```
(rename-window) Window one
```

You can have all windows displayed for selection with `Ctrl`+`b`-`w` (use the arrow keys to move up and down and `enter` to select):

```
(0)  0: Window zero- "debian"
(1)  1: Window one* "debian"
```

In a similar fashion to `screen`, we can jump from one window to another with:

`Ctrl`+`b`-`n`
- go to next window.

`Ctrl`+`b`-`p`
- go to previous window.

`Ctrl`+`b`-`number`
- go to window number number.

To get rid of a window, use `Ctrl`+`b`-`&`. You will be asked for confirmation:

```
kill-window Window one? (y/n)
```

Other interesting window commands include:

`Ctrl`+`b`-`f`
- find window by name.

`Ctrl`+`b`-`.`
change window index number.

To read the whole list of commands, consult the manual page.

### Panes
The window-splitting facility of `screen` is also present in `tmux`. The resulting divisions are not called regions but panes, though. The most important difference between regions and panes is that the latter are complete pseudo-terminals linked to a window. This means that killing a pane will also kill its pseudo-terminal and any associated programs running within.

To split a window horizontally, we use `Ctrl`+`b`-`"`:

```
Tasks:  93 total,   1 running,  92 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  4050960 total,  3730920 free,   114880 used,   205160 buff/cache
KiB Swap:  4192252 total,  4192252 free,        0 used.  3716004 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1340 carol     20   0   44876   3400   2800 R  0.3  0.1   0:00.24 top
    1 root      20   0  139088   6988   5264 S  0.0  0.2   0:00.50 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
    3 root      20   0       0      0      0 S  0.0  0.0   0:00.04 ksoftirqd/0
    4 root      20   0       0      0      0 S  0.0  0.0   0:01.62 kworker/0:0
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H
    7 root      20   0       0      0      0 S  0.0  0.0   0:00.06 rcu_sched
    8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh
    9 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
   10 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 lru-add-drain
   11 root      rt   0       0      0      0 S  0.0  0.0   0:00.01 watchdog/0
   12 root      20   0       0      0      0 S  0.0  0.0   0:00.00 cpuhp/0
$
───────────────────────────────────────────────────────────────────────────────────────────────
$






[LPI] 0:Window zero- 1:Window one*                                      "debian" 19:05 27-Aug-19
```

To split it vertically, use `Ctrl`+`b`-`%`:

```

                                                                               │$
    1 root      20   0  139088   6988   5264 S  0.0  0.2   0:00.50 systemd     │
                                                                               │
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd    │
                                                                               │
    3 root      20   0       0      0      0 S  0.0  0.0   0:00.04 ksoftirqd/0 │
    4 root      20   0       0      0      0 S  0.0  0.0   0:01.62 kworker/0:0 │
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H│
    7 root      20   0       0      0      0 S  0.0  0.0   0:00.06 rcu_sched   │
    8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh      │
    9 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0 │
                                                                               │
   10 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 lru-add-drai│
n                                                                              │
   11 root      rt   0       0      0      0 S  0.0  0.0   0:00.01 watchdog/0  │
                                                                               │
   12 root      20   0       0      0      0 S  0.0  0.0   0:00.00 cpuhp/0     │
                                                                               │
$                                                                              │
───────────────────────────────────────────────────────────────────────────────┴───────────────
$




[LPI] 0:Window zero- 1:Window one*                                      "debian" 19:05 27-Aug-19
```

To destroy the current pane (along with its pseudo-terminal running within it, along with any associated programs), use `Ctrl`+`b`-`x`. You will be asked for confirmation on the status bar:

```
kill-pane 1? (y/n)
```

Important pane commands:

`Ctrl`+`b`-`↑`,`↓`,`←`,`→`
- move between panes.

`Ctrl`+`b`-`;`
- move to the last active pane.

`Ctrl`+`b`-`Ctrl`+`arrow key`
- resize pane by one line.

`Ctrl`+`b`-`Alt`+`arrow key`
- resize pane by five lines.

`Ctrl`+`b`-`{`
- swap panes (current to previous).

`Ctrl`+`b`-`}`
- swap panes (current to next).

`Ctrl`+`b`-`z`
- zoom in/out panel.

`Ctrl`+`b`-`t`
- tmux displays a fancy clock inside the pane (kill it by pressing `q`).

`Ctrl`+`b`-`!`
- turn pane into window.

To read the whole list of commands, consult the manual page.

### Sessions
To list sessions in `tmux` you can use `Ctrl`+`b`-`s`:

```
(0) + LPI: 2 windows (attached)
```

Alternatively, you can use the `tmux ls` command:

```
$ tmux ls
LPI: 2 windows (created Tue Aug 27 19:01:49 2019) [158x39] (attached)
```

There is only one session (`LPI`) which includes two windows. Let us create a new session from within our current session. This can be achieved by using `Ctrl`+`b`, type in `:new` at the prompt, then press Enter. You will be sent to the new session as can be observed on the status bar:

```
[2] 0:bash*                                                       "debian" 19:15 27-Aug-19
By default tmux named the session 2. To rename it, use Ctrl+b-$. When prompted, supply the new name and hit Enter:
```

```
(rename-session) Second Session
```

You can switch sessions with `Ctrl`+`b`-`s` (use the arrow keys and enter):

```
(0) + LPI: 2 windows
(1) + Second Session: 1 windows (attached)
```

To kill a session, you can use the command `tmux kill-session -t SESSION-NAME`. If you type the command from within the current, attached session, you will be taken out of `tmux` and back to your initial terminal session:

```
$ tmux kill-session -t "Second Session"
[exited]
$
```

### Sessions Detachment
By killing `Second Session` we were thrown outside `tmux`. However, we still have an active session. Ask `tmux` for a listing of sessions and you will surely find it there:

```
$ tmux ls
LPI: 2 windows (created Tue Aug 27 19:01:49 2019) [158x39]
```

However this session is detached from its terminal. We can attach it with `tmux attach -t SESSION-NAME` (`attach` can be replaced by `at` or — simply — `a`). When there is only a single session, the name specification is optional:

```
$ tmux a
```

You are now back in your session; to detach from it, press `Ctrl`+`b`-`d`:

```
[detached (from session LPI)]
$
```

```
Tip
The same session can be attached to more than one terminal. If you want to attach a session making sure it is first detached from any other terminals, use the -d switch: tmux attach -d -t SESSION-NAME.
```

Important commands for session attaching/detaching:

`Ctrl`+`b`-`D`
- select what client to detach.

`Ctrl`+`b`-`r`
- refresh the client’s terminal.

To read the whole list of commands, consult the manual page.

### Copy & Paste: Scrollback Mode
`tmux` also features copy mode in basically the same fashion as `screen` (remember to use tmux’s command prefix and not screen’s!). The only difference command-wise is that you use `Ctrl` + `Space` to mark the beginning of the selection and `Alt`+`w` to copy the selected text.

### Customization of tmux
The configuration files for `tmux` are typically located at `/etc/tmux.conf` and `~/.tmux.conf`. When started, `tmux` sources these files if they exist. There is also the possibility of starting `tmux` with the `-f` switch to supply an alternate configuration file. You can find a `tmux` configuration file example located at `/usr/share/doc/tmux/example_tmux.conf`. The level of customization that you can achieve is really high. Some of the things you can do include:

- Change the prefix key

```
# Change the prefix key to C-a
set -g prefix C-a
unbind C-b
bind C-a send-prefix
```

- Set extra key bindings for windows higher than 9

```
# Some extra key bindings to select higher numbered windows
bind F1 selectw -t:10
bind F2 selectw -t:11
bind F3 selectw -t:12
```

For a comprehensive list of all bindings, type `Ctrl`+`b`-`?` (press `q` to quit) or consult the manual page.
___

### Lesson 3-6: Modify process execution priorities

Operating systems able to run more than one process at the same time are called multi-tasking or multi-processing systems. Whilst true simultaneity only happens when more than one processing unit is available, even single processor systems can mimic simultaneity by switching between processes very quickly. This technique is also employed in systems with many equivalent CPUs, or symmetric multi-processor (SMP) systems, given that the number of potential concurrent processes greatly exceeds the number of available processor units.

In fact, only one process at a time can control the CPU. However, most process activities are system calls, that is, the running process transfers CPU control to an operating system’s process so it performs the requested operation. System calls are in charge of all inter-device communication, like memory allocations, reading and writing on filesystems, printing text on the screen, user interaction, network transfers, etc. Transferring CPU control during a system call allows the operating system to decide whether to return CPU control to the previous process or to hand it to another process. As modern CPUs can execute instructions much faster than most external hardware can communicate with each other, a new controlling process can do a lot of CPU work while previously requested hardware responses are still unavailable. To ensure maximum CPU harnessing, multi-processing operating systems keep a dynamic queue of active processes waiting for a CPU time slot.

Although they allow to significantly improve CPU time utilization, relying solely on system calls to switch between processes is not enough to achieve satisfactory multi-tasking performance. A process that makes no system calls could control the CPU indefinitely. This is why modern operating systems are also preemptive, that is, a running process can be put back in the queue so a more important process can control the CPU, even if the running process has not made a system call.

### The Linux Scheduler
Linux, as a preemptive multi-processing operating system, implements a scheduler that organizes the process queue. More precisely, the scheduler also decides which queued thread will be executed — a process can branch out many independent threads — but process and thread are interchangeable terms in this context. Every process has two predicates that intervene on its scheduling: the scheduling policy and the scheduling priority.

There are two main types of scheduling policies: real-time policies and normal policies. Processes under a real-time policy are scheduled by their priority values directly. If a more important process becomes ready to run, a less important running process is preempted and the higher priority process takes control of the CPU. A lower priority process will gain CPU control only if higher priority processes are idle or waiting for hardware response.

Any real-time process has higher priority than a normal process. As a general purpose operating system, Linux runs just a few real-time processes. Most processes, including system and user programs, run under normal scheduling policies. Normal processes usually have the same priority value, but normal policies can define execution priority rules using another process predicate: the nice value. To avoid confusion with the dynamic priorities derived from nice values, scheduling priorities are usually called static scheduling priorities.

The Linux scheduler can be configured in many different ways and even more intricate ways of establishing priorities exist, but these general concepts always apply. When inspecting and tuning process scheduling, it is important to keep in mind that only processes under normal scheduling policy will be affected.

### Reading Priorities
Linux reserves static priorities ranging from 0 to 99 for real-time processes and normal processes are assigned to static priorities ranging from 100 to 139, meaning that there are 39 different priority levels for normal processes. Lower values mean higher priority. The static priority of an active process can be found in the `sched` file, located in its respective directory inside the `/proc` filesystem:

```
$ grep ^prio /proc/1/sched
prio                   :       120
```

As shown in the example, the line beginning with `prio` gives the priority value of the process (the PID 1 process is the init or the systemd process, the first process the kernel starts during system initialization). The standard priority for normal processes is 120, so that it can be decreased to 100 or increased to 139. The priorities of all running process can be verified with the command `ps -Al` or `ps -el`:

```
$ ps -el
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0     1     0  0  80   0 -  9292 -      ?        00:00:00 systemd
4 S     0    19     1  0  80   0 -  8817 -      ?        00:00:00 systemd-journal
4 S   104    61     1  0  80   0 - 64097 -      ?        00:00:00 rsyslogd
4 S     0    63     1  0  80   0 -  7244 -      ?        00:00:00 cron
1 S     0   126     1  0  80   0 -  4031 -      ?        00:00:00 dhclient
4 S     0   154     1  0  80   0 -  3937 -      pts/0    00:00:00 agetty
4 S     0   155     1  0  80   0 -  3937 -      pts/1    00:00:00 agetty
4 S     0   156     1  0  80   0 -  3937 -      pts/2    00:00:00 agetty
4 S     0   157     1  0  80   0 -  3937 -      pts/3    00:00:00 agetty
4 S     0   158     1  0  80   0 -  3937 -      console  00:00:00 agetty
4 S     0   160     1  0  80   0 - 16377 -      ?        00:00:00 sshd
4 S     0   280     0  0  80   0 -  5301 -      ?        00:00:00 bash
0 R     0   392   280  0  80   0 -  7221 -      ?        00:00:00 ps
```

The `PRI` column indicates the static priority assigned by the kernel. Note, however, that the priority value displayed by `ps` differs from that obtained in the previous example. Due to historical reasons, priorities displayed by `ps` range from -40 to 99 by default, so the actual priority is obtained by adding 40 to it (in particular, 80 + 40 = 120).

It is also possible to continuously monitor processes currently being managed by the Linux kernel with program `top`. As with `ps`, `top` also displays the priority value differently. To make it easier to identify real-time processes, top subtracts the priority value by 100, thus making all real-time priorities negative, with a negative number or rt identifying them. Therefore, normal priorities displayed by `top` range from 0 to 39.

```
Note
To get more details from the ps command, additional options can be used. Compare the output from this command to the one from our previous example:

$ ps -e -o user,uid,comm,tty,pid,ppid,pri,pmem,pcpu --sort=-pcpu | head
```

### Process Niceness
Every normal process begins with a default nice value of 0 (priority 120). The nice name comes from the idea that “nicer” processes allow other processes to run before them in a particular execution queue. Nice numbers range from -20 (less nice, high priority) to 19 (more nice, low priority). Linux also allows the ability to assign different nice values to threads within the same process. The `NI` column in `ps` output indicates the nice number.

Only the root user can decrease the niceness of a process below zero. It’s possible to start a process with a non-standard priority with the command `nice`. By default, `nice` changes the niceness to 10, but it can be specified with option `-n`:

```
$ nice -n 15 tar czf home_backup.tar.gz /home
```

In this example, the command `tar` is executed with a niceness of 15. The command `renice` can be used to change the priority of a running process. The option `-p` indicates the PID number of the target process. For example:

```
# renice -10 -p 2164
2164 (process ID) old priority 0, new priority -10
```

The options `-g` and `-u` are used to modify all the processes of a specific group or user, respectively. With `renice +5 -g users`, the niceness of processes owned by users of the group users will be raised in five.

Besides `renice`, the priority of processes can be modified with other programs, like the program `top`. On the top main screen, the niceness of a process can be modified by pressing `r` and then the PID number of the process:

```
top - 11:55:21 up 23:38,  1 user,  load average: 0,10, 0,04, 0,05
Tasks:  20 total,   1 running,  19 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,5 us,  0,3 sy,  0,0 ni, 99,0 id,  0,0 wa,  0,2 hi,  0,0 si,  0,0 st
KiB Mem :  4035808 total,   774700 free,  1612600 used,  1648508 buff/cache
KiB Swap:  7999828 total,  7738780 free,   261048 used.  2006688 avail Mem
PID to renice [default pid = 1]
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
    1 root      20   0   74232   7904   6416 S 0,000 0,196   0:00.12 systemd
   15 root      20   0   67436   6144   5568 S 0,000 0,152   0:00.03 systemd-journal
   21 root      20   0   61552   5628   5000 S 0,000 0,139   0:00.01 systemd-logind
   22 message+  20   0   43540   4072   3620 S 0,000 0,101   0:00.03 dbus-daemon
   23 root      20   0   45652   6204   4992 S 0,000 0,154   0:00.06 wickedd-dhcp4
   24 root      20   0   45648   6276   5068 S 0,000 0,156   0:00.06 wickedd-auto4
   25 root      20   0   45648   6272   5060 S 0,000 0,155   0:00.06 wickedd-dhcp6
```

The message `PID to renice [default pid = 1]` appears with the first listed process selected by default. To change the priority of another process, type its PID and press Enter. Then, the message Renice `PID 1 to value` will appear (with the requested PID number) and a new nice value can be assigned.

___

## Lesson 3-7: Search text files using regular expressions

String-searching algorithms are widely used by several data-processing tasks, so much that Unix-like operating systems have their own ubiquitous implementation: Regular expressions, often abbreviated to REs. Regular expressions consist of character sequences that make up a generic pattern used to locate and sometimes modify a corresponding sequence in a larger string of characters. Regular expressions greatly expand the ability to:

- Write parsing rules to requests in HTTP servers, nginx in particular.
- Write scripts that convert text-based datasets to another format.
- Search for occurrences of interest in journal entries or documents.
- Filter markup documents, keeping semantic content.

The simplest regular expression contains at least one atom. An atom, so named because it’s the basic element of a regular expression, is just a character that may or may not have special meaning. Most ordinary characters are unambiguous, they retain their literal meaning, while others have special meaning:

`.` (dot)
- Atom matches with any character.

`^` (caret)
- Atom matches with the beginning of a line.

`$` (dollar sign)
- Atom matches with the end of a line.

For example, the regular expression `bc`, composed by the literal atoms `b` and `c`, can be found in the string `abcd`, but can not be found in the string `a1cd`. On the other hand, the regular expression `.c` can be found in both strings `abcd` and `a1cd`, as the dot `.` matches with any character.

The caret and dollar sign atoms are used when only matches at the beginning or at the end of the string are of interest. For that reason they are also called anchors. For example, `cd` can be found in `abcd`, but `^cd` can not. Similarly, `ab` can be found in abcd, but `ab$` can not. The caret `^` is a literal character except when at the beginning and `$` is a literal character except when at the end of the regular expression.

### Bracket Expression
There is another type of atom named bracket expression. Although not a single character, brackets `[]` (including their content) are considered a single atom. A bracket expression usually is just a list of literal characters enclosed by `[]`, making the atom match any single character from the list. For example, the expression `[1b]` can be found in both strings `abcd` and `a1cd`. To specify characters the atom should not correspond to, the list must begin with `^`, as in `[^1b]`. It is also possible to specify ranges of characters in bracket expressions. For example, `[0−9]` matches digits 0 to 9 and `[a−z]` matches any lowercase letter. Ranges must be used with caution, as they might not be consistent across distinct locales.

Bracket expression lists also accept classes instead of just single characters and ranges. Traditional character classes are:

`[:alnum:]`
- Represents an alphanumeric character.

`[:alpha:]`
- Represents an alphabetic character.

`[:ascii:]`
- Represents a character that fits into the ASCII character set.

`[:blank:]`
- Represents a blank character, that is, a space or a tab.

`[:cntrl:]`
- Represents a control character.

`[:digit:]`
- Represents a digit (0 through 9).

`[:graph:]`
- Represents any printable character except space.

`[:lower:]`
- Represents a lowercase character.

`[:print:]`
- Represents any printable character including space.

`[:punct:]`
- Represents any printable character which is not a space or an alphanumeric character.

`[:space:]`
- Represents white-space characters: space, form-feed (\f), newline (\n), carriage return (\r), horizontal tab (\t), and vertical tab (\v).

`[:upper:]`
- Represents an uppercase letter.

`[:xdigit:]`
- Represents hexadecimal digits (0 through F).

Character classes can be combined with single characters and ranges, but may not be used as an endpoint of a range. Also, character classes may be used only in bracket expressions, not as an independent atom outside the brackets.

### Quantifiers
The reach of an atom, either a single character atom or a bracket atom, can be adjusted using an atom quantifier. Atom quantifiers define atom sequences, that is, matches occur when a contiguous repetition for the atom is found in the string. The substring corresponding to the match is called a piece. Notwithstanding, quantifiers and other features of regular expressions are treated differently depending on which standard is being used.

As defined by POSIX, there are two forms of regular expressions: “basic” regular expressions and “extended” regular expressions. Most text related programs in any conventional Linux distribution support both forms, so it is important to know their differences in order to avoid compatibility issues and to pick the most suitable implementation for the intended task.

The `*` quantifier has the same function in both basic and extended REs (atom occurs zero or more times) and it’s a literal character if it appears at the beginning of the regular expression or if it’s preceded by a backslash `\`. The plus sign quantifier `+` will select pieces containing one or more atom matches in sequence. With the question mark quantifier `?`, a match will occur if the corresponding atom appears once or if it doesn’t appear at all. If preceded by a backslash `\`, their special meaning is not considered. Basic regular expressions also support `+` and `?` quantifiers, but they need to be preceded by a backslash. Unlike extended regular expressions, `+` and `?` by themselves are literal characters in basic regular expressions.

### Bounds
A bound is an atom quantifier that, as the name implies, allows a user to specify precise quantity boundaries for an atom. In extended regular expressions, a bound may appear in three forms:

`{i}`
- The atom must appear exactly `i` times (`i` an integer number). For example, `[[:blank:]]{2}` matches with exactly two blank characters.

`{i,}`
- The atom must appear at least `i` times (`i` an integer number). For example, `[[:blank:]]{2,}` matches with any sequence of two or more blank characters.

`{i,j}`
The atom must appear at least `i` times and at most `j` times (`i` and `j` integer numbers, `j` greater then `i`). For example, `xyz{2,4}` matches the `xy` string followed by two to four of the `z` character.

In any case, if a substring matches with a regular expression and a longer substring starting at the same point also matches, the longer substring will be considered.

Basic regular expressions also support bounds, but the delimiters must be preceded by `\`: `\{` and `\}`. By themselves, `{` and `}` are interpreted as literal characters. A `\{` followed by a character other than a digit is a literal character, not the beginning of a bound.

### Branches and Back References
Basic regular expressions also differ from extended regular expressions in another important aspect: an extended regular expression can be divided into branches, each one an independent regular expression. Branches are separated by `|` and the combined regular expression will match anything that corresponds to any of the branches. For example, `he|him` will match if either substring `he` or `him` are found in the string being examined. Basic regular expressions interpret `|` as a literal character. However, most programs supporting basic regular expressions will allow branches with `\|`.

An extended regular expression enclosed in `()` can be used in a back reference. For example, `([[:digit:]])\1` will match any regular expression that repeats itself at least once, because the `\1` in the expression is the back reference to the piece matched by the first parenthesized subexpression. If more than one parenthesized subexpression exist in the regular expression, they can be referenced with `\2`, `\3` and so on.

For basic REs, subexpressions must be enclosed by `\(` and `\)`, with `(` and `)` by themselves ordinary characters. The back reference indicator is used like in extended regular expressions.

### Searching with Regular Expressions
The immediate benefit offered by regular expressions is to improve searches on filesystems and in text documents. The `-regex` option of command `find` allows to test every path in a directory hierarchy against a regular expression. For example,

```
$ find $HOME -regex '.*/\..*' -size +100M
```

searches for files greater than 100 megabytes (100 units of 1048576 bytes), but only in paths inside the user’s home directory that do contain a match with `.*/\..*`, that is, a `/.` surrounded by any other number of characters. In other words, only hidden files or files inside hidden directories will be listed, regardless of the position of `/.` in the corresponding path. For case insensitive regular expressions, the `-iregex` option should be used instead:

```
$ find /usr/share/fonts -regextype posix-extended -iregex '.*(dejavu|liberation).*sans.*(italic|oblique).*'
/usr/share/fonts/dejavu/DejaVuSansCondensed-BoldOblique.ttf
/usr/share/fonts/dejavu/DejaVuSansCondensed-Oblique.ttf
/usr/share/fonts/dejavu/DejaVuSans-BoldOblique.ttf
/usr/share/fonts/dejavu/DejaVuSans-Oblique.ttf
/usr/share/fonts/dejavu/DejaVuSansMono-BoldOblique.ttf
/usr/share/fonts/dejavu/DejaVuSansMono-Oblique.ttf
/usr/share/fonts/liberation/LiberationSans-BoldItalic.ttf
/usr/share/fonts/liberation/LiberationSans-Italic.ttf
```

In this example, the regular expression contains branches (written in extended style) to list only specific font files under the `/usr/share/fonts` directory hierarchy. Extended regular expressions are not supported by default, but find allows for them to be enabled with `-regextype posix-extended` or `-regextype egrep`. The default RE standard for `find` is findutils-default, which is virtually a basic regular expression clone.

It is often necessary to pass the output of a program to command `less` when it doesn’t fit on the screen. Command `less` splits its input in pages, one screenful at a time, allowing the user to easily navigate the text up and down. In addition, `less` also allows a user to perform regular expression based searches. This feature is notably important because `less` is the default paginator used for many everyday tasks, like inspecting journal entries or consulting manual pages. When reading a manual page, for instance, pressing the `/` key will open a search prompt. This is a typical scenario in which regular expressions are useful, as command options are listed just after a page margin in the general manual page layout. However, the same option might appear many times through the text, making literal searches unfeasible. Regardless of that, typing `^[[:blank:]]*-o` — or more simply: `^ *-o` —  in the search prompt will jump immediately to option the -o section (if it exists) after pressing Enter, thus allowing one to consult an option description more rapidly.
___

Streaming data through a chain of piped commands allows for the application of compound filters based on regular expressions. Regular expressions are an important technique used not only in system administration, but also in data mining and related areas. Two commands are specially suited to manipulate files and text data using regular expressions: `grep` and `sed`. `grep` is a pattern finder and `sed` is a stream editor. They are useful by themselves, but it is when working together with other processes that they stand out.

### The Pattern Finder: `grep`
One of the most common uses of `grep` is to facilitate the inspection of long files, using the regular expression as a filter applied to each line. It can be used to show only the lines starting with a certain term. For example, `grep` can be used to investigate a configuration file for kernel modules, listing only option lines:

```
$ grep '^options' /etc/modprobe.d/alsa-base.conf
options snd-pcsp index=-2
options snd-usb-audio index=-2
options bt87x index=-2
options cx88_alsa index=-2
options snd-atiixp-modem index=-2
options snd-intel8x0m index=-2
options snd-via82xx-modem index=-2
```

The pipe `|` character can be employed to redirect the output of a command directly to `grep`'s input. The following example uses a bracket expression to select lines from `fdisk -l` output, starting with `Disk /dev/sda` or `Disk /dev/sdb`:

```
# fdisk -l | grep '^Disk /dev/sd[ab]'
Disk /dev/sda: 320.1 GB, 320072933376 bytes, 625142448 sectors
Disk /dev/sdb: 7998 MB, 7998537728 bytes, 15622144 sectors
```

The mere selection of lines with matches may not be appropriate for a particular task, requiring adjustments to `grep`'s behavior through its options. For example, option `-c` or `--count` tells grep to show how many lines had matches:

```
# fdisk -l | grep '^Disk /dev/sd[ab]' -c
2
```

The option can be placed before or after the regular expression. Other important `grep` options are:

`-c` or `--count`
- Instead of displaying the search results, only display the total count for how many times a match occurs in any given file.

`-i` or `--ignore-case`
- Turn the search case-insensitive.

`-f FILE` or `--file=FILE`
- Indicate a file containing the regular expression to use.

`-n` or `--line-number`
- Show the number of the line.

`-v` or `--invert-match`
- Select every line, except those containing matches.

`-H` or `--with-filename`
- Print also the name of the file containing the line.

`-z` or `--null-data`
- Rather than have `grep` treat input and output data streams as separate lines (using the newline by default) instead take the input or output as a sequence of lines. When combining output from the `find` command using its `-print0` option with the `grep` command, the `-z` or `--null-data` option should be used to process the stream in the same manner.

Although activated by default when multiple file paths are given as input, the option `-H` is not activated for single files. That may be critical in special situations, like when `grep` is called directly by `find`, for instance:

```
$ find /usr/share/doc -type f -exec grep -i '3d modeling' "{}" \; | cut -c -100
artistic aspects of 3D modeling. Thus this might be the application you are
This major approach of 3D modeling has not been supported
oce is a C++ 3D modeling library. It can be used to develop CAD/CAM softwares, for instance [FreeCad
```

In this example, `find` lists every file under `/usr/share/doc` then passes each one to `grep`, which in turn performs a case-insensitive search for `3d modeling` inside the file. The pipe to `cut` is there just to limit output length to 100 columns. Note, however, that there is no way of knowing from which file the lines came from. This issue is solved by adding `-H` to `grep`:

```
$ find /usr/share/doc -type f -exec grep -i -H '3d modeling' "{}" \; | cut -c -100
/usr/share/doc/openscad/README.md:artistic aspects of 3D modeling. Thus this might be the applicatio
/usr/share/doc/opencsg/doc/publications.html:This major approach of 3D modeling has not been support
```

Now it is possible to identify the files where each match was found. To make the listing even more informative, leading and trailing lines can be added to lines with matches:

```
$ find /usr/share/doc -type f -exec grep -i -H -1 '3d modeling' "{}" \; | cut -c -100
/usr/share/doc/openscad/README.md-application Blender), OpenSCAD focuses on the CAD aspects rather t
/usr/share/doc/openscad/README.md:artistic aspects of 3D modeling. Thus this might be the applicatio
/usr/share/doc/openscad/README.md-looking for when you are planning to create 3D models of machine p
/usr/share/doc/opencsg/doc/publications.html-3D graphics library for Constructive Solid Geometry (CS
/usr/share/doc/opencsg/doc/publications.html:This major approach of 3D modeling has not been support
/usr/share/doc/opencsg/doc/publications.html-by real-time computer graphics until recently.
```

The option `-1` instructs `grep` to include one line before and one line after when it finds a line with a match. These extra lines are called context lines and are identified in the output by a minus sign after the file name. The same result can be obtained with `-C 1` or `--context=1` and other context line quantities may be indicated.

There are two complementary programs to grep: `egrep` and `fgrep`. The program `egrep` is equivalent to the command `grep -E`, which incorporates extra features other than the basic regular expressions. For example, with `egrep` it is possible to use extended regular expression features, like branching:

```
$ find /usr/share/doc -type f -exec egrep -i -H -1 '3d (modeling|printing)' "{}" \; | cut -c -100
/usr/share/doc/openscad/README.md-application Blender), OpenSCAD focuses on the CAD aspects rather t
/usr/share/doc/openscad/README.md:artistic aspects of 3D modeling. Thus this might be the applicatio
/usr/share/doc/openscad/README.md-looking for when you are planning to create 3D models of machine p
/usr/share/doc/openscad/RELEASE_NOTES.md-* Support for using 3D-Mouse / Joystick / Gamepad input dev
/usr/share/doc/openscad/RELEASE_NOTES.md:* 3D Printing support: Purchase from a print service partne
/usr/share/doc/openscad/RELEASE_NOTES.md-* New export file formats: SVG, 3MF, AMF
/usr/share/doc/opencsg/doc/publications.html-3D graphics library for Constructive Solid Geometry (CS
/usr/share/doc/opencsg/doc/publications.html:This major approach of 3D modeling has not been support
/usr/share/doc/opencsg/doc/publications.html-by real-time computer graphics until recently.
```

In this example either `3D modeling` or `3D printing` will match the expression, case-insensitive. To display only the parts of a text stream that match the expression used by `egrep`, use the `-o` option.

The program `fgrep` is equivalent to `grep -F`, that is, it does not parse regular expressions. It is useful in simple searches where the goal is to match a literal expression. Therefore, special characters like the dollar sign and the dot will be taken literally and not by their meanings in a regular expression.

### The Stream Editor: `sed`
The purpose of the `sed` program is to modify text-based data in a non-interactive way. It means that all the editing is made by predefined instructions, not by arbitrarily typing directly into a text displayed on the screen. In modern terms, `sed` can be understood as a template parser: given a text as input, it places custom content at predefined positions or when it finds a match for a regular expression.

`Sed`, as the name implies, is well suited for text streamed through pipelines. Its basic syntax is `sed -f SCRIPT` when editing instructions are stored in the file `SCRIPT` or `sed -e COMMANDS` to execute `COMMANDS` directly from the command line. If neither `-f` or `-e` are present, `sed` uses the first non-option parameter as the script file. It is also possible to use a file as the input just by giving its path as an argument to `sed`.

`sed` instructions are composed of a single character, possibly preceded by an address or followed by one or more options, and are applied to each line at a time. Addresses can be a single line number, a regular expression, or a range of lines. For example, the first line of a text stream can be deleted with `1d`, where `1` specifies the line where the delete command `d` will be applied. To clarify sed 's usage, take the output of the command `factor ``seq 12`, which returns the prime factors for numbers 1 to 12:

```
$ factor `seq 12`
1:
2: 2
3: 3
4: 2 2
5: 5
6: 2 3
7: 7
8: 2 2 2
9: 3 3
10: 2 5
11: 11
12: 2 2 3
```

Deleting the first line with `sed` is accomplished by `1d`:

```
$ factor `seq 12` | sed 1d
2: 2
3: 3
4: 2 2
5: 5
6: 2 3
7: 7
8: 2 2 2
9: 3 3
10: 2 5
11: 11
12: 2 2 3
```

A range of lines can be specified with a separating comma:

```
$ factor `seq 12` | sed 1,7d
8: 2 2 2
9: 3 3
10: 2 5
11: 11
12: 2 2 3
```

More than one instruction can be used in the same execution, separated by semicolons. In this case, however, it is important to enclose them with parenthesis so the semicolon is not interpreted by the shell:

```
$ factor `seq 12` | sed "1,7d;11d"
8: 2 2 2
9: 3 3
10: 2 5
12: 2 2 3
```

In this example, two deletion instructions were executed, first on lines ranging from 1 to 7 and then on line 11. An address can also be a regular expression, so only lines with a match will be affected by the instruction:

```
$ factor `seq 12` | sed "1d;/:.*2.*/d"
3: 3
5: 5
7: 7
9: 3 3
11: 11
```

The regular expression `:.*2.*` matches with any occurrence of the number 2 anywhere after a colon, causing the deletion of lines corresponding to numbers with 2 as a factor. With `sed`, anything placed between slashes (`/`) is considered a regular expression and by default all basic RE is supported. For example, `sed -e "/^#/d" /etc/services` shows the contents of the file `/etc/services` without the lines beginning with `#` (comment lines).

The delete instruction `d` is only one of the many editing instructions provided by `sed`. Instead of deleting a line, `sed` can replace it with a given text:

```
$ factor `seq 12` | sed "1d;/:.*2.*/c REMOVED"
REMOVED
3: 3
REMOVED
5: 5
REMOVED
7: 7
REMOVED
9: 3 3
REMOVED
11: 11
REMOVED
```

The instruction `c REMOVED` simply replaces a line with the text `REMOVED`. In the example’s case, every line with a substring matching the regular expression `:.*2.*` is affected by instruction `c REMOVED`. Instruction a `TEXT` copies text indicated by `TEXT` to a new line after the line with a match. The instruction `r FILE` does the same, but copies the contents of the file indicated by `FILE`. Instruction `w` does the opposite of `r`, that is, the line will be appended to the indicated file.

By far the most used `sed` instruction is `s/FIND/REPLACE/`, which is used to replace a match to the regular expression `FIND` with text indicated by `REPLACE`. For example, the instruction `s/hda/sda/` replaces a substring matching the literal RE `hda` with `sda`. Only the first match found in the line will be replaced, unless the flag `g` is placed after the instruction, as in `s/hda/sda/g`.

A more realistic case study will help to illustrate `sed`'s features. Suppose a medical clinic wants to send text messages to its customers, reminding them of their scheduled appointments for the next day. A typical implementation scenario relies on a professional instant message service, which provides an API to access the system responsible for delivering the messages. These messages usually originate from the same system that runs the application controlling customer’s appointments, triggered by a specific time of the day or some other event. In this hypothetical situation, the application could generate a file called `appointments.csv` containing tabulated data with all the appointments for the next day, then used by `sed` to render the text messages from a template file called `template.txt`. CSV files are a standard way of export data from database queries, so sample appointments could be given as follows:

```
$ cat appointments.csv
"NAME","TIME","PHONE"
"Carol","11am","55557777"
"Dave","2pm","33334444"
```

The first line holds the labels for each column, which will be used to match the tags inside the sample template file:

```
$ cat template.txt
Hey <NAME>, don't forget your appointment tomorrow at <TIME>.
```

The less than `<` and greater than `>` signs were put around labels just to help identify them as tags. The following Bash script parses all enqueued appointments using `template.txt` as the message template:

```
#! /bin/bash

TEMPLATE=`cat template.txt`
TAGS=(`sed -ne '1s/^"//;1s/","/\n/g;1s/"$//p' appointments.csv`)
mapfile -t -s 1 ROWS < appointments.csv
for (( r = 0; r < ${#ROWS[*]}; r++ ))
do
  MSG=$TEMPLATE
  VALS=(`sed -e 's/^"//;s/","/\n/g;s/"$//' <<<${ROWS[$r]}`)
  for (( c = 0; c < ${#TAGS[*]}; c++ ))
  do
    MSG=`sed -e "s/<${TAGS[$c]}>/${VALS[$c]}/g" <<<"$MSG"`
  done
  echo curl --data message=\"$MSG\" --data phone=\"${VALS[2]}\" https://mysmsprovider/api
done
```

An actual production script would also handle authentication, error checking and logging, but the example has basic functionality to start with. The first instructions executed by `sed` are applied only to the first line — the address `1` in `1s/^"//;1s/","/\n/g;1s/"$//p` — to remove the leading and trailing quotes — `1s/^"//` and `1s/"$//` — and to replace field separators with a newline character: `1s/","/\n/g`. Only the first line is needed for loading column names, so non-matching lines will be suppressed by option `-n`, requiring flag p to be placed after the last `sed` command to print the matching line. The tags are then stored in the `TAGS` variable as a Bash array. Another Bash array variable is created by the command `mapfile` to store the lines containing the appointments in the array variable `ROWS`.

A `for` loop is employed to process each appointment line found in `ROWS`. Then, quotes and separators in the appointment — the appointment is in variable `${ROWS[$r]}` used as a here string — are replaced by `sed`, similarly to the commands used to load the tags. The separated values for the appointment are then stored in the array variable `VALS`, where array subscripts 0, 1 and 2 correspond to values for `NAME`, `TIME` and `PHONE`.

Finally, a nested `for` loop walks through the `TAGS` array and replaces each tag found in the template with its corresponding value in `VALS`. The `MSG` variable holds a copy of the rendered template, updated by the substitution command `s/<${TAGS[$c]}>/${VALS[$c]}/g` on every loop pass through `TAGS`.

This results in a rendered message like: `"Hey Carol, don’t forget your appointment tomorrow at 11am."` The rendered message can then be sent as a parameter through a HTTP request with `curl`, as a mail message or any other similar method.

### Combining grep and sed
Commands `grep` and `sed` can be used together when more complex text mining procedures are required. As a system administrator, you may want to inspect all the login attempts to a server, for example. The file `/var/log/wtmp` records all logins and logouts, whilst the file `/var/log/btmp` records the failed login attempts. They are written in a binary format, which can be read by the commands `last` and `lastb`, respectively.

The output of `lastb` shows not only the username used in the bad login attempt, but its IP address as well:

```
# lastb -d -a -n 10 --time-format notime
user     ssh:notty       (00:00)     81.161.63.251
nrostagn ssh:notty       (00:00)     vmd60532.contaboserver.net
pi       ssh:notty       (00:00)     132.red-88-20-39.staticip.rima-tde.net
pi       ssh:notty       (00:00)     132.red-88-20-39.staticip.rima-tde.net
pi       ssh:notty       (00:00)     46.6.11.56
pi       ssh:notty       (00:00)     46.6.11.56
nps      ssh:notty       (00:00)     vmd60532.contaboserver.net
narmadan ssh:notty       (00:00)     vmd60532.contaboserver.net
nominati ssh:notty       (00:00)     vmd60532.contaboserver.net
nominati ssh:notty       (00:00)     vmd60532.contaboserver.net
```

Option `-d` translates the IP number to the corresponding hostname. The hostname may provide clues about the ISP or hosting service used to perform these bad login attempts. Option `-a` puts the hostname in the last column, which facilitates the filtering yet to be applied. Option `--time-format notime` suppresses the time when the login attempt occurred. Command `lastb` can take some time to finish if there were too many bad login attempts, so the output was limited to ten entries with the option `-n 10`.

Not all remote IPs have a hostname associated to it, so reverse DNS does not apply to them and they can be dismissed. Although you could write a regular expression to match the expected format for a hostname at the end of the line, it is probably simpler to write a regular expression to match with either a letter from the alphabet or with a single digit at the end of the line. The following example shows how the command `grep` takes the listing at its standard input and removes the lines without hostnames:

```
# lastb -d -a --time-format notime | grep -v '[0-9]$' | head -n 10
nvidia   ssh:notty       (00:00)     vmd60532.contaboserver.net
n_tonson ssh:notty       (00:00)     vmd60532.contaboserver.net
nrostagn ssh:notty       (00:00)     vmd60532.contaboserver.net
pi       ssh:notty       (00:00)     132.red-88-20-39.staticip.rima-tde.net
pi       ssh:notty       (00:00)     132.red-88-20-39.staticip.rima-tde.net
nps      ssh:notty       (00:00)     vmd60532.contaboserver.net
narmadan ssh:notty       (00:00)     vmd60532.contaboserver.net
nominati ssh:notty       (00:00)     vmd60532.contaboserver.net
nominati ssh:notty       (00:00)     vmd60532.contaboserver.net
nominati ssh:notty       (00:00)     vmd60532.contaboserver.net
```

Command `grep` option `-v` shows only the lines that don’t match with the given regular expression. A regular expression matching any line ending with a number (i.e. `[0-9]$`) will capture only the entries without a hostname. Therefore, `grep -v '[0-9]$'` will show only the lines ending with a hostname.

The output can be filtered even further, by keeping only the domain name and removing the other parts from each line. Command `sed` can do it with a substitution command to replace the whole line with a back-reference to the domain name in it:

```
# lastb -d -a --time-format notime | grep -v '[0-9]$' | sed -e 's/.* \(.*\)$/\1/' | head -n 10
vmd60532.contaboserver.net
vmd60532.contaboserver.net
vmd60532.contaboserver.net
132.red-88-20-39.staticip.rima-tde.net
132.red-88-20-39.staticip.rima-tde.net
vmd60532.contaboserver.net
vmd60532.contaboserver.net
vmd60532.contaboserver.net
vmd60532.contaboserver.net
vmd60532.contaboserver.net
```

The escaped parenthesis in `.* \(.*\)$` tells `sed` to remember that part of the line, that is, the part between the last space character and the end of the line. In the example, this part is referenced with `\1` and used the replace the entire line.

It’s clear that most remote hosts try to login more than once, thus the same domain name repeats itself. To suppress the repeated entries, first they need to be sorted (with command `sort`) then passed to the command `uniq`:

```
# lastb -d -a --time-format notime | grep -v '[0-9]$' | sed -e 's/.* \(.*\)$/\1/' | sort | uniq | head -n 10
116-25-254-113-on-nets.com
132.red-88-20-39.staticip.rima-tde.net
145-40-33-205.power-speed.at
tor.laquadrature.net
tor.momx.site
ua-83-226-233-154.bbcust.telenor.se
vmd38161.contaboserver.net
vmd60532.contaboserver.net
vmi488063.contaboserver.net
vmi515749.contaboserver.net
```

This shows how different commands can be combined to produce the desired outcome. The hostname list can then be used to write blocking firewall rules or to take other measures to enforce the security of the server.

___ 

## Lesson 103.8: Basic File Editing

In most Linux distributions, `vi` — abbreviation for “visual” — is pre-installed and it is the standard editor in the shell environment. Vi is an interactive text editor, it shows the file content on the screen as it is being edited. As such, it allows the user to move through and to make modifications anywhere in the document. However, unlike visual editors from the graphical desktop, the `vi` editor is a shell application with keyboard shortcuts to every editing task.

An alternative to `vi`, called `vim` (vi improved), is sometimes used as a modern replacement for `vi`. Among other improvements, `vim` offers support for syntax highlighting, multilevel undo/redo and multi-document editing. Although more resourceful, `vim` is fully backwards compatible with `vi`, making both indistinguishable for most tasks.

The standard way to start `vi` is to give it a path to a file as a parameter. To jump directly to a specific line, its number should be informed with a plus sign, as in `vi +9 /etc/fstab` to open `/etc/fstab/` and place the cursor at the 9th line. Without a number, the plus sign by itself places the cursor at the last line.

`vi`'s interface is very simple: all space available in the terminal window is occupied to present a file, normally informed as a command argument, to the user. The only visual clues are a footer line showing the current position of the cursor and a tilde `~` indicating where the file ends. There are different execution modes for `vi` where program behavior changes. The most common are: insert mode and normal mode.

### Insert Mode
The insert mode is straightforward: text appears on the screen as it is typed on the keyboard. It is the type of interaction most users expect from a text editor, but it is not how `vi` first presents a document. To enter the insert mode, the user must execute an insertion command in the normal mode. The `Esc` key finishes the insert mode and returns to normal mode, the default `vi` mode.

```
Note
If you are interested to know more on the other execution modes, open vi and type:

:help vim-modes-intro
```

### Normal Mode
Normal mode — also known as command mode — is how `vi` starts by default. In this mode, keyboard keys are associated with commands for navigation and text manipulation tasks. Most commands in this mode are unique keys. Some of the keys and their functions on normal mode are:

`0`, `$`
- Go to the beginning and end of the line.

`1G`, `G`
- Go to the beginning and end of the document.

`(`, `)`
- Go to the beginning and end of the sentence.

`{`, `}`
- Go to the beginning and end of the paragraph.

`w`, `W`
- Jump word and jump word including punctuation.

`h`, `j`, `k`, `l`
- Left, down, up, right.

`e` or `E`
- Go to the end of current word.

`/`, `?`
- Search forward and backwards.

`i`, `I`
Enter the insert mode before the current cursor position and at the beginning of the current line.

`a`, `A`
- Enter the insert mode after the current cursor position and at the end of the current line.

`o`, `O`
- Add a new line and enter the insert mode in the next line or in the previous line.

`s`, `S`
- Erase the character under the cursor or the entire line and enter the insert mode.

`c`
- Change the character(s) under the cursor.

`r`
- Replace the character under the cursor.

`x`
- Delete the selected characters or the character under the cursor.

`v`, `V`
- Start a new selection with the current character or the entire line.

`y`, `yy`
- Copy (yanks) the character(s) or the entire line.

`p`, `P`
- Paste copied content, after or before the current position.

`u`
- Undo the last action.

`Ctrl`-`R`
- Redo the last action.

`ZZ`
- Close and save.

`ZQ`
- Close and do not save.

If preceded by a number, the command will be executed the same number of times. For example, press `3yy` to copy the current line plus the following two, press `d5w` to delete the current word and the following 4 words, and so on.

Most editing tasks are combinations of multiple commands. For example, the key sequence `vey` is used to copy a selection starting at the current position until the end of the current word. Command repetition can also be used in combinations, so `v3ey` would copy a selection starting at the current position until the end of the third word from there.

`vi` can organize copied text in registers, allowing to keep distinct contents at the same time. A register is specified by a character preceded by `"` and once created it’s kept until the end of the current session. The key sequence `"ly` creates a register containing the current selection, which will be accessible through the key `l`. Then, the register `l` may be pasted with `"lp`.

There is also a way to set custom marks at arbitrary positions along the text, making it easier to quickly jump between them. Marks are created by pressing the key `m` and then a key to address the current position. With that done, the cursor will come back to the marked position when `'` followed by the chosen key are pressed.

Any key sequence can be recorded as a macro for future execution. A macro can be recorded, for example, to surround a selected text in double-quotes. First, a string of text is selected and the key `q` is pressed, followed by a register key to associate the macro with, like `d`. The line `recording @d` will appear in the footer line, indicating that the recording is on. It is assumed that some text is already selected, so the first command is `x` to remove (and automatically copy) the selected text. The key `i` is pressed to insert two double quotes at the current position, then `Esc` returns to normal mode. The last command is `P`, to re-insert the deleted selection just before the last double-quote. Pressing `q` again will end the recording. Now, a macro consisting of key sequence `x`, `i`, `""`, `Esc` and `P` will execute every time keys `@d` are pressed in normal mode, where `d` is the register key associated with the macro.

However, the macro will be available only during the current session. To make macros persistent, they should be stored in the configuration file. As most modern distributions use vim as the vi compatible editor, the user’s configuration file is `~/.vimrc`. Inside `~/.vimrc`, the line `let @d = 'xi""P'` will set the register `d` to the key sequence inside single-quotes. The same register previously assigned to a macro can be used to paste its key sequence.

### Colon Commands
The normal mode also supports another set of `vi` commands: the colon commands. Colon commands, as the name implies, are executed after pressing the colon key `:` in normal mode. Colon commands allow the user to perform searches, to save, to quit, to run shell commands, to change `vi` settings, etc. To go back to the normal mode, the command `:visual` must be executed or the Enter key pressed without any command. Some of the most common colon commands are indicated here (the initial is not part of the command):

`:s/REGEX/TEXT/g`
- Replaces all the occurrences of regular expression `REGEX` with `TEXT` in the current line. It accepts the same syntax of command `sed`, including addresses.

`:!`
- Run a following shell command.

`:quit` or `:q`
- Exit the program.

`:quit!` or `:q!`
- Exit the program without saving.

`:wq`
- Save and exit.

`:exit` or `:x` or `:e`
- Save and exit, if needed.

`:visual`
- Go back to navigation mode.

The standard `vi` program is capable of doing most text editing tasks, but any other non-graphical editor can be used to edit text files in the shell environment.

```
Tip
Novice users may have difficulty memorizing vi's command keys all at once. Distributions adopting vim also have the command vimtutor, which uses vim itself to open a step-by-step guide to the main activities. The file is an editable copy that can be used to practice the commands and progressively get used to them.
```

### Alternative Editors
Users unfamiliar with `vi` may have difficulties adapting to it, since its operation is not intuitive. A simpler alternative is GNU `nano`, a small text editor that offers all basic text editing features like undo/redo, syntax coloring, interactive search-and-replace, auto-indentation, line numbers, word completion, file locking, backup files, and internationalization support. Unlike `vi`, all key presses are just inserted in the document being edited. Commands in nano are given by using the `Ctrl` key or the Meta key (depending on the system, Meta is `Alt` or `⌘`).

`Ctrl`-`6` or `Meta-A`
- Start a new selection. It’s also possible to create a selection by pressing Shift and moving the cursor.

`Meta-6`
- Copy the current selection.

`Ctrl-K`
- Cut the current selection.

`Ctrl-U`
- Paste copied content.

`Meta-U`
- Undo.

`Meta-E`
- Redo.

`Ctrl-\`
- Replace the text at the selection.

`Ctrl-T`
- Start a spell-checking session for the document or current selection.

Emacs is another very popular text editor for the shell environment. Whilst text is inserted just by typing it, like in `nano`, navigation through the document is assisted by keyboard commands, like in `vi`. Emacs includes many features that makes it more than just a text editor. It is also an IDE (integrated development environment) capable of compiling, running, and testing programs. Emacs can be configured as an email, news or RSS client, making it an authentic productivity suite.

The shell itself will run a default text editor, usually `vi`, every time it is necessary. This is the case, for example, when `crontab -e` is executed to edit cronjobs. Bash uses the session variables `VISUAL` or `EDITOR` to find out the default text editor for the shell environment. For example, the command `export EDITOR=nano` defines `nano` as the default text editor in the current shell session. To make this change persistent across sessions, the command should be included in `~/.bash_profile`.


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)