# LPIC-1: GNU and Unix Commands

## Lesson 103.1: Work on the Command Line

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

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)