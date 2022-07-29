# Linux Essentials Section 2

## Lesson 2-1 Command Line Basics

Modern Linux distributions have a wide range of graphical user interfaces but an administrator will always need to know how to work with the command line, or shell as it is called. The shell is a program that enables text based communication between the operating system and the user. It is usually a text mode program that reads the user’s input and interprets it as commands to the system.

There are several different shells on Linux, these are just a few:

- Bourne-again shell (Bash)

- C shell (csh or tcsh, the enhanced csh)

- Korn shell (ksh)

- Z shell (zsh)

On Linux the most common one is the Bash shell. This is also the one that will be used in examples or exercises here.

When using an interactive shell, the user inputs commands at a so-called prompt. For each Linux distribution, the default prompt may look a little different, but it usually follows this structure:

`username@hostname current_directory shell_type`

On Ubuntu or Debian GNU/Linux, the prompt for a regular user will likely look like this:

`carol@mycomputer:~$`

The superuser’s prompt will look like this:

`root@mycomputer:~#`

On CentOS or Red Hat Linux, the prompt for a regular user will instead look like this:

`[dave@mycomputer ~]$`

And the superuser’s prompt will look like this:

`[root@mycomputer ~]#`

Let’s explain each component of the structure:

`username`

Name of the user that runs the shell

`hostname`

Name of the host on which the shell runs. There is also a command hostname, with which you can show or set the system’s host name.

`current_directory`

The directory that the shell is currently in. A ~ means that the shell is in the current user’s home directory.

`shell_type`
`$` indicates the shell is run by a regular user.

`#` indicates the shell is run by the superuser `root`.

As we do not need any special privileges, we will use an unprivileged prompt in the following examples. For brevity, we will just use the `$` as prompt.

### Command Line Structure
Most commands at the command line follow the same basic structure:

`command  [option(s)/parameter(s)...]  [argument(s)...]`

Take the following command as an example:

`$ ls -l /home`

Let’s explain the purpose of each component:

Command
Program that the user will run – `ls` in the above example.

Option(s)/Parameter(s)
A “switch” that modifies the behavior of the command in some way, such as `-l` in the above example. Options can be accessed in a short and in a long form. For example, `-l` is identical to `--format=long`.

Multiple options can be combined as well and for the short form, the letters can usually be typed together. For example, the following commands all do the same:
```
$ ls -al
$ ls -a -l
$ ls --all --format=long
```
Argument(s)
Additional data that is required by the program, like a filename or path, such as /home in the above example.

The only mandatory part of this structure is the command itself. In general, all other elements are optional, but a program may require certain options, parameters or arguments to be specified.
```
Note
> Most commands display a short overview of available options when they are run with the --help parameter. We will learn additional ways to learn more about Linux commands soon.
```
### Command Behavior Types
The shell supports two types of commands:

Internal
- These commands are part of the shell itself and are not separate programs. There are around 30 such commands. Their main purpose is executing tasks inside the shell (e.g. `cd`, `set`, `export`).

External
- These commands reside in individual files. These files are usually binary programs or scripts. When a command which is not a shell builtin is run, the shell uses the `PATH` variable to search for an executable file with same name as the command. In addition to programs which are installed with the distribution’s package manager, users can create their own external commands as well.

The command `type` shows what type a specific command is:
```
$ type echo
echo is a shell builtin
$ type man
man is /usr/bin/man
```
Quoting
As a Linux user, you will have to create or manipulate files or variables in various ways. This is easy when working with short filenames and single values, but it becomes more complicated when, for example, spaces, special characters and variables are involved. Shells provide a feature called quoting which encapsulates such data using various kinds of quotes (" ", ' '). In Bash, there are three types of quotes:

- Double quotes

- Single quotes

- Escape characters

For example, the following commands do not act in the same way due to quoting:
```
$ TWOWORDS="two words"
$ touch $TWOWORDS
$ ls -l
-rw-r--r-- 1 carol carol     0 Mar 10 14:56 two
-rw-r--r-- 1 carol carol     0 Mar 10 14:56 words
$ touch "$TWOWORDS"
$ ls -l
-rw-r--r-- 1 carol carol     0 Mar 10 14:56  two
-rw-r--r-- 1 carol carol     0 Mar 10 14:58 'two words'
-rw-r--r-- 1 carol carol     0 Mar 10 14:56  words
$ touch '$TWOWORDS'
$ ls -l
-rw-r--r-- 1 carol carol     0 Mar 10 15:00 '$TWOWORDS'
-rw-r--r-- 1 carol carol     0 Mar 10 14:56  two
-rw-r--r-- 1 carol carol     0 Mar 10 14:58 'two words'
-rw-r--r-- 1 carol carol     0 Mar 10 14:56  words
```
```
Note
> The line with `TWOWORDS=` is a Bash variable that we have created ourselves. We will introduce variables later. This is just meant to show you how quoting affects the output of variables.
```
### Double Quotes
Double quotes tell the shell to take the text in between the quote marks ("...") as regular characters. All special characters lose their meaning, except the `$` (dollar sign), `\` (backslash) and "`" (backquote). This means that variables, command substitution and arithmetic functions can still be used.

For example, the substitution of the `$USER` variable is not affected by the double quotes:
```
$ echo I am $USER
I am tom
$ echo "I am $USER"
I am tom
```
A space character, on the other hand, loses its meaning as an argument separator:

```
$ touch new file
$ ls -l
-rw-rw-r-- 1 tom students 0 Oct 8 15:18 file
-rw-rw-r-- 1 tom students 0 Oct 8 15:18 new
$ touch "new file"
$ ls -l
-rw-rw-r-- 1 tom students 0 Oct 8 15:19 new file
```

As you can see, in the first example, the touch command creates two individual files, the command interprets the two strings as individual arguments. In the second example, the command interprets both strings as one argument, therefore it only creates one file. It is, however, best practice to avoid the space character in filenames. Instead, an underscore (_) or a dot (.) could be used.

Single Quotes
Single quotes don’t have the exceptions of the double quotes. They revoke any special meaning from each character. Let’s take one of the first examples from above:
```
$ echo I am $USER
I am tom
```
When applying the single quotes you see a different result:
```
$ echo 'I am $USER'
I am $USER
```
The command now displays the exact string without substituting the variable.

### Escape Characters
We can use escape characters to remove special meanings of characters from Bash. Going back to the $USER environment variable:
```
$ echo $USER
carol
```
We see that by default, the contents of the variable are displayed in the terminal. However, if we were to precede the dollar sign with a backslash character (\) then the special meaning of the dollar sign will be negated. This in turn will not let Bash expand the variable’s value to the username of the person running the command, but will instead interpret the variable name literally:
```
$ echo \$USER
$USER
```
If you recall, we can get similar results to this using the single quote, which prints the literal contents of whatever is between the single quotes. However the escape character works differently by instructing Bash to ignore whatever special meaning the character it precedes may possess.
___

All shells manage a set of status information throughout the shell sessions. This runtime information may change during the session and influences how the shell behaves. This data is also used by programs to determinate aspects of the system’s configuration. Most of this data is stored in so-called variables, which we will cover in this lesson.

### Variables
Variables are pieces of storage for data, such as text or numbers. Once set, a variable’s value can be accessed at a later time. Variables have a name which allows accessing a specific variable, even when the variable’s content changes. They are a very common tool in most programming languages.

In most Linux shells, there are two types of variables:

Local variables
- These variables are available to the current shell process only. If you create a local variable and then start another program from this shell, the variable is not accessible to that program anymore. Because they are not inherited by sub processes, these variables are called local variables.

Environment variables
- These variables are available both in a specific shell session and in sub processes spawned from that shell session. Theses variables can be used to pass configuration data to commands which are run. Because these programs can access these variables, they are called environment variables. The majority of the environment variables are in capital letters (e.g. PATH, DATE, USER). A set of default environment variables provide, for example, information about the user’s home directory or terminal type. Sometimes the complete set of all environment variables is referred to as the environment.

These types of variables are also known as variable scope.
```
Note
> Variables are not persistent. When the shell in which they were set is closed, all variables and their contents are lost. Most shells provide configuration files that contain variables which are set whenever a new shell is started. Variables that should be set permanently must be added to one of these configuration files.
```
### Manipulating Variables
As a system administrator, you will need to create, modify or remove both local and environment variables.

### Working with Local Variables
You can set up a local variable by using the = (equal) operator. A simple assignment will create a local variable:

`$ greeting=hello`
```
Note
> Don’t put any space before or after the = operator.
```

You can display any variable using the echo command. The command usually displays the text in the argument section:
```
$ echo greeting
greeting
```
In order to access the value of the variable you will need to use $ (dollar sign) in front of the variable’s name.
```
$ echo $greeting
hello
```
As it can be seen, the variable has been created. Now open another shell and try to display the contents of the variable created.

`$ echo $greeting`
Nothing is displayed. This illustrates, that variables always exist in a specific shell only.

To verify that the variable is actually a local variable, try to spawn a new process and check if this process can access the variable. We can do so by starting another shell and let this shell run the echo command. As the new shell is run in a new process, it won’t inherit local variables from its parent process:

```
$ echo $greeting world
hello world
$ bash -c 'echo $greeting world'
world
```
```
Note
> Make sure to use single quotes in the example above.
```
In order to remove a variable, you will need to use the command unset:
```
$ echo $greeting
hey
$ unset greeting
$ echo $greeting
```
```
Note
> unset requires the name of the variable as an argument. Therefore you may not add $ to the name, as this would resolve the variable and pass the variable’s value to unset instead of the variable’s name.
```
### Working with Global Variables
To make a variable available to subprocesses, turn it from a local into an environment variable. This is done by the command export. When it is invoked with the variable name, this variable is added to the shell’s environment:
```
$ greeting=hello
$ export greeting
```
```
Note
> Again, make sure to not use $ when running export as you want to pass the name of the variable instead of its contents.
```
An easier way to create the environment variable is to combine both of the above methods, by assigning the variable value in the argument part of the command.

`$ export greeting=hey`
Let’s check again if the variable is accessible to subprocesses:
```
$ export greeting=hey
$ echo $greeting world
hey world
$ bash -c 'echo $greeting world'
hey world
```
Another way to use environment variables is to use them in front of commands. We can test this with the environment variable `TZ` which holds the timezone. This variable is used by the command `date` to determine which timezone’s time to display:
```
$ TZ=EST date
Thu 31 Jan 10:07:35 EST 2019
$ TZ=GMT date
Thu 31 Jan 15:07:35 GMT 2019
```
You can display all environment variables using the env command.

### The `PATH` Variable
The `PATH` variable is one of the most important environment variables in a Linux system. It stores a list of directories, separated by a colon, that contain executable programs eligible as commands from the Linux shell.
```
$ echo $PATH
/home/user/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
```

To append a new directory to the variable, you will need to use the colon sign (`:`).

`$ PATH=$PATH:new_directory`
Here an example:
```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
$ PATH=$PATH:/home/user/bin
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/home/user/bin
```
As you see, `$PATH` is used in the new value assigned to `PATH`. This variable is resolved during the command execution and makes sure that the original content of the variable is preserved. Of course, you can use other variables in the assignment as well:
```
$ mybin=/opt/bin
$ PATH=$PATH:$mybin
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/home/user/bin:/opt/bin
```
The `PATH` variable needs to be handled with caution, as it is crucial for working on the command line. Let’s consider the following `PATH` variable:
```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```
To find out how the shell invokes a specific command, which can be run with the command’s name as argument. We can, for example, try to find out where nano is stored:
```
$ which nano
/usr/bin/nano
```
As it can be seen, the nano executable is located within the /usr/bin directory. Let’s remove the directory from the variable and check to see if the command still works:
```
$ PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/sbin:/bin:/usr/games
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/sbin:/bin:/usr/games
```
Let’s look up the nano command again:
```
$ which nano
which: no nano in (/usr/local/sbin:/usr/local/bin:/usr/sbin:/sbin:/bin:/usr/games)
```
As it can be seen, the command is not found, therefore not executed. The error message also explains the reason why the command was not found and in what locations it was searched.

Let’s add back the directories and try running the command again.
```
$ PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
$ which nano
/usr/bin/nano
```
Now our command works again.

```
Tip
The order of elements in PATH also defines the lookup order. The first matching executable found while going through the paths is executed.
```

## Lessson 2.2 Using the Command Line to Get Help

The command line is a very complex tool. Each command has its own unique options, therefore documentation is key when working with a Linux system. Besides the /usr/share/doc/ directory, which stores most of the documentation, various other tools provide information on using Linux commands. This chapter focuses on methods to access that documentation, with the purpose of getting help.

There are a multitude of methods to get help within the Linux command line. man, help and info are just a few of them. For Linux Essentials, we will be focusing on man and info as they are the most commonly used tools for obtaining help.

Another topic of this chapter will be locating files. You will mainly work with the locate command.

### Getting Help on the Command Line
### Built-in Help
When started with the `--help` parameter, most commands display some brief instructions about their usage. Although not all commands provide this switch, it is still a good first try to learn more about the parameters of a command. Be aware that the instructions from `--help` often are rather brief compared to the other sources of documentation which we will discuss in the rest of this lesson.

### Man Pages
Most commands provide a manual page or “man” page. This documentation is usually installed with the software and can be accessed with the `man` command. The command whose man page should be displayed is added to `man` as an argument:

`$ man mkdir`
This command opens the man page for `mkdir`. You can use the up and down arrow keys or the space bar to navigate through the man page. To exit the man page, press `Q`.

Each man page is divided in maximum of 11 sections, though many of these sections are optional:

|Section	|Description|
|-----------|--------------|
|NAME       |Command name and brief description|
|SYNOPSIS   |Description of the command’s syntax|
|DESCRIPTION|Description of the effects of the command|
|OPTIONS    |Available options|
|ARGUMENTS  |Available arguments|
|FILES      |Auxiliary files|
|EXAMPLES   |A sample of the command line|
|SEE ALSO   |Cross-references to the related topics|
|DIAGNOSTICS|Warning and Error messages|
|COPYRIGHT  |Author(s) of the command|
|BUGS       |Any known limitations of the command|

In practice, most man pages don’t contain all of these parts.

Man pages are organized in eight categories, numbered from 1 to 8:

Category |	Description
---|---
1|User command
2|System calls
3|Functions of the C library
4|Drivers and device files
5|Configuration files and file formats
6|Games
7|Miscellaneous
8|System administrator commands
9|Kernel functions (not standard)

Each man page belongs to exactly one category. However, multiple categories can contain man pages with the same name. Let’s take the `passwd` command as an example. This command can be used to change a user’s password. Since `passwd` is a user command, its man page resides in category 1. In addition to the `passwd` command, the password database file `/etc/passwd` also has a man page which is called `passwd`, too. As this file is a configuration file, it belongs to category 5. When referring to a man page, the category is often added to the name of the man page, as in `passwd(1) or passwd(5)` to identify the respective man page.

By default, man passwd displays the first available man page, in this case `passwd(1)`. The category of the desired man page can be specified in a command such as `man 1 passwd` or `man 5 passwd`.

We have already discussed how to navigate through a man page and how to return to the command line. Internally, `man` uses the `less` command to display the man page’s content. `less` lets you search for text within a man page. To search for the word `linux` you can just use `/linux` for forward searching from the point that you are on the page, or `?linux` to start a backward search. This action highlights the all the matching results and moves the page to the first highlighted match. In both cases you can type `N` to jump to the next match. In order to find more information about these additional features, press `H` and a menu with all the information will be displayed.

### Info Pages
Another tool that will help you while working with the Linux system are the info pages. The info pages are usually more detailed than the man pages and are formatted in hypertext, similar to web pages on the Internet.

The info pages can be displayed like so:

`$ info mkdir`
For each info page, `info` reads an info file that is structured into individual nodes within a tree. Each node contains a simple topic and the `info` command contains hyperlinks that can help you move from one to the other. You can access the link by pressing enter while placing the cursor on one of the leading asterisks.

Similar to `man`, the `info` tool also has page navigation commands. You can find out more about these command by pressing `?` while being on the info page. These tools will help you navigate the page easier as well as understand how to access the nodes and move within the node tree.

The `/usr/share/doc/` directory
As mentioned before, the `/usr/share/doc/` directory stores most documentation of the commands that the system is using. This directory contains a directory for most packages installed on the system. The name of the directory is usually the name of the package and occasionally its version. These directories include a `README` or `readme.txt` file that contains the package’s basic documentation. Alongside the `README` file, the folder can also contain other documentation files, such as the changelog which includes the history of the program in detail, or examples of configuration files for the specific package.

The information within the `README` file varies from one package to another. All files are written in plain text, therefore they can be read with any preferred text editor. The exact number and kinds of files depend on the package. Check some of the directories to get an overview of their contents.

### Locating files
### The `locate` command
A Linux system is built from numerous directories and files. Linux has many tools to locate a particular file within a system. The quickest one is the command `locate`.

`locate` searches within a database and then outputs every name that matches the given string:
```
$ locate note
/lib/udev/keymaps/zepto-znote
/usr/bin/zipnote
/usr/share/doc/initramfs-tools/maintainer-notes.html
/usr/share/man/man1/zipnote.1.gz
```
The `locate` command supports the usage of wildcards and regular expressions as well, therefore the search string does not have to match the entire name of the desired file. You will learn more about regular expressions in a later chapter.

By default, `locate` behaves as if the pattern would be surrounded by asterisks, so `locate PATTERN` is the same as locate `*PATTERN*`. This allows you just provide substrings instead of the exact filename. You can modify this behavior with the different options that you can find explained in the `locate` man page.

Because `locate` is reading from a database, you may not find a file that you recently created. The database is managed by a program named `updatedb`. Usually it is run periodically, but if you have root privileges and you need the database to be updated immediately, you can run the `updatedb` command yourself at any time.

### The `find` command
`find` is another very popular tool that is used to search for files. This command has a different approach, compared to the `locate` command. `find` command searches a directory tree recursively, including its subdirectories. `find` does such a search at each invocation, it does not maintain a database like `locate`. Similar to `locate`, `find` also supports wildcards and regular expressions.

`find` requires at least the path it should search. Furthermore, so-called expressions can be added to provide filter criteria for which files to display. An example is the `-name` expression, which looks for files with a specific name:
```
~$ cd Downloads
~/Downloads
$ find . -name thesis.pdf
./thesis.pdf
~/Downloads
$ find ~ -name thesis.pdf
/home/carol/Downloads/thesis.pdf
```
The first `find` command searches for the file within the current `Downloads` directory, whereas the second one searches for the file in the user’s home directory.

The `find` command is very complex, therefore it will not be covered in the Linux Essentials exam. However, it is a powerful tool which is particularly handy in practice.


## Lesson 2-3 Using Directories and Listing Files

### Files and Directories
The Linux filesystem is similar to other operating system’s filesystems in that it contains files and directories. Files contain data such as human-readable text, executable programs, or binary data that is used by the computer. Directories are used to create organization within the filesystem. Directories can contain files and other directories.
```
$ tree

Documents
├── Mission-Statement.txt
└── Reports
    └── report2018.txt

1 directory, 2 files
```
In this example, Documents is a directory that contains one file (Mission-Statement.txt) and one subdirectory (Reports). The Reports directory in turn contains one file called report2018.txt. The Documents directory is said to be the parent of the Reports directory.
```
Tip
> If the command tree is not available on your system, install it using your Linux distribution’s package manager. Refer to the lesson on package management to learn how to do so.
```
### File and Directory Names
File and directory names in Linux can contain lower case and upper case letters, numbers, spaces and special characters. However, since many special characters have a special meaning in the Linux shell, it is good practice to not use spaces or special characters when naming files or directories. Spaces, for example, need the escape character `\` to be entered correctly:
```
$ cd Mission\ Statements
```
Also, refer to the filename `report2018.txt`. Filenames can contain a suffix which comes after the period (`.`). Unlike Windows, this suffix has no special meaning in Linux; it is there for human understanding. In our example `.txt` indicates to us that this is a plaintext file, although it could technically contain any kind of data.

### Navigating the Filesystem
### Getting Current Location
Since Linux shells such as Bash are text-based, it is important to remember your current location when navigating the filesystem. The command prompt provides this information:

`user@hostname ~/Documents/Reports $`
Note that information such as `user` and `hostname` will be covered in future sections. From the prompt, we now know that our current location is in the `Reports` directory. Similarly, the command `pwd` will print working directory:
```
user@hostname ~/Documents/Reports $ pwd
/home/user/Documents/Reports
```
The relationship of directories is represented with a forward slash (`/`). We know that `Reports` is a subdirectory of `Documents`, which is a subdirectory of `user`, which is located in a directory called `home`. `home` doesn’t seem to have a parent directory, but that is not true at all. The parent of `home` is called `root`, and is represented by the first slash (`/`). We will discuss the root directory in a later section.

Notice that the output of the `pwd` command differs slightly from the path given on the command prompt. Instead of `/home/user`, the command prompt contains a tilde (`~`). The tilde is a special character that represents the user’s home directory. This will be covered in more detail in the next lesson.

### Listing Directory Contents
The contents of the current directory are listed with the ls command:
```
user@hostname ~/Documents/Reports $ ls
report2018.txt
```
Note that `ls` provides no information about the parent directory. Similarly, by default `ls` does not show any information about contents of subdirectories. `ls` can only “see” what is in the current directory.

### Changing Current Directory
Navigation in Linux is primarily done with the `cd` command. This changes directory. Using the `pwd` command from before, we know our current directory is `/home/user/Documents/Reports`. We can change our current directory by entering a new path:
```
user@hostname ~ $ cd /home/user/Documents
user@hostname ~/Documents $ pwd
/home/user/Documents
user@hostname ~/Documents $ ls
Mission-Statement.txt Reports
```
From our new location, we can “see” `Mission-Statement.txt` and our subdirectory `Reports`, but not the contents of our subdirectory. We can navigate back into Reports like this:
```
user@hostname ~/Documents $ cd Reports
user@hostname ~/Documents/Reports $ pwd
/home/user/Documents/Reports
user@hostname ~/Documents/Reports $ ls
report2018.txt
```
We are now back where we started.

### Absolute and Relative Paths
The `pwd` command always prints an absolute path. This means that the path contains every step of the path, from the top of the filesystem (`/`) to the bottom (`Reports`). Absolute paths always begin with a `/`.
```
/
└── home
    └── user
        └── Documents
            └── Reports
```
The absolute path contains all the information required to get to `Reports` from anywhere in the filesystem. The drawback is that it is tedious to type.

The second example (`cd Reports`) was much easier to type. This is an example of a relative path. Relative paths are shorter but only have meaning in relation to your current location. Consider this analogy: I am visiting you at your house. You tell me that your friend lives next door. I will understand that location because it is relative to my current location. But if you tell me this over the phone, I will not be able to find your friend’s house. You will need to give me the complete street address.

### Special Relative Paths
The Linux shell gives us ways to shorten our paths when navigating. To reveal the first special paths, we enter the `ls` command with the flag `-a`. This flag modifies the `ls` command so that all files and directories are listed, including hidden files and directories:
```
user@hostname ~/Documents/Reports $ ls -a
.
..
report2018.txt
```
```
Note
> You can refer to the man page for ls to understand what -a is doing here.
```
This command has revealed two additional results: These are special paths. They do not represent new files or directories, but rather they represent directories that you already know:

`.`
Indicates the current location (in this case, `Reports`).

`..`
Indicates the parent directory (in this case, `Documents`).

It is usually unnecessary to use the special relative path for the current location. It is easier and more understandable to type `report2018.txt` than it is to type `./report2018.txt`. But the `.` has uses that you will learn in future sections. For now, we will focus on the relative path for the parent directory:
```
user@hostname ~/Documents/Reports $ cd ..
user@hostname ~/Documents $ pwd
/home/user/Documents
```
The example of cd is much easier when using .. instead of the absolute path. Additionally, we can combine this pattern to navigate up the file tree very quickly.
```
user@hostname ~/Documents $ cd ../..
$ pwd
/home
```
___

The Unix operating system was originally designed for mainframe computers in the mid-1960s. These computers were shared among many users, who accessed the system’s resources through terminals. These fundamental ideas carry through to Linux systems today. We still talk about using “terminals” to enter commands in the shell, and every Linux system is organized in such a way that it is easy to create many users on a single system.

### Home Directories
This is an example of a normal file system in Linux:
```
$ tree -L 1 /
/
├── bin
├── boot
├── cdrom
├── dev
├── etc
├── home
├── lib
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```
Most of these directories are consistent across all Linux systems. From servers to supercomputers to tiny embedded systems, a seasoned Linux user can be confident that they can find the `ls` command inside `/bin`, can change the system configuration by modifying files in `/etc`, and read system logs in `/var`. The standard location of these files and directories is defined by the Filesystem Hierarchy Standard (FHS), which will be discussed in a later lesson. You will learn more about the contents of these directories as you continue learning about Linux, but for the time being, know that:

- changes that you make in the root filesystem will affect all users, and

- changing files in the root filesystem will require administrator permissions.

This means that normal users will be prohibited from modifying these files, and may also be prohibited from even reading these files. We will cover the topic of permissions in a later section.

Now, we will focus on the directory `/home`, which should be somewhat familiar at this point:
```
$ tree -L 1 /home
/home
├── user
├── michael
└── lara
```
Our example system has three normal users, and each of our users has their own dedicated location, where they can create and modify files and directories without affecting their neighbor. For example, in the previous lesson we were working with the following file structure:
```
$ tree /home/user
user
└── Documents
    ├── Mission-Statement
    └── Reports
        └── report2018.txt
```
In actuality, the real filesystem may look like this:
```
$ tree /home
/home
├── user
│   └── Documents
│       ├── Mission-Statement
│       └── Reports
│           └── report2018.txt
├── michael
│   ├── Documents
│   │   └── presentation-for-clients.odp
│   └── Music
```

…​and so on for `lara`.

In Linux, `/home` is similar to an apartment building. Many users may have their space here, separated into dedicated apartments. The utilities and maintenance of the building itself are the responsibility of the superintendent root user.

### The Special Relative Path for Home
When you start a new terminal session in Linux, you see a command prompt similar to this:
```
user@hostname ~ $
```
The tilde (`~`) here represents our home directory. If you run the `ls` command, you will see some familiar output:
```
$ cd ~
$ ls
Documents
```
Compare that with the file system above to check your understanding.

Consider now what we know about Linux: it is similar to an apartment building, with many users residing in `/home`. So `user` 's home will be different from user `michael` 's home. To demonstrate this, we will use the `su` command to switch user.
```
user@hostname ~ $ pwd
/home/user
user@hostname ~ $ su - michael
Password:
michael@hostname ~ $ pwd
/home/michael
```
The meaning of `~` changes depending of who the user is. For `michael`, the absolute path of `~` is `/home/michael`. For `lara`, the absolute path of `~` is `/home/lara`, and so on.

### Relative-to-Home File Paths
Using `~` for commands is very convenient, provided that you don’t switch users. We will consider the following example for `user`, who has begun a new session:
```
$ ls
Documents
$ cd Documents
$ ls
Mission-Statement
Reports
$ cd Reports
$ ls
report2018.txt
$ cd ~
$ ls
Documents
```
Note that users will always begin a new session in their home directory. In this example, `user` has traveled into their `Documents/Reports` subdirectory, and with the `cd ~` command they have returned to where they started. You can perform the same action by using the `cd` command with no arguments:
```
$ cd Documents/Reports
$ pwd
/home/user/Documents/Reports
$ cd
$ pwd
/home/user
```
One last thing to note: we can specify the home directories of other users by specifying the username after the tilde. For example:
```
$ ls ~michael
Documents
Music
```
Note that this will only work if `michael` has given us permission to view the contents of his home directory.

Let’s consider a situation where `michael` would like to view the file `report2018.txt` in `user` 's home directory. Assuming that `michael` has the permission to do so, he can use the `less` command.
```
$ less ~user/Documents/Reports/report2018.txt
```
Any file path that contains the ~ character is called a relative-to-home path.

### Hidden Files and Directories
In the previous lesson, we introduced the option `-a` for the `ls` command. We used `ls -a` to introduce the two special relative paths: `.` and `..`. The `-a` option will list all files and directories, including hidden files and directories.
```
$ ls -a ~
.
..
.bash_history
.bash_logout
.bash-profile
.bashrc
Documents
```
Hidden files and directories will always begin with a period (`.`). By default, a user’s home directory will include many hidden files. These are often used to set user-specific configuration settings, and should only be modified by an experienced user.

### The Long List Option
The `ls` command has many options to change its behavior. Let’s look at one of the most common options:
```
$ ls -l
-rw-r--r-- 1 user staff      3606 Jan 13  2017 report2018.txt
```
`-l` creates a long list. Files and directories will each occupy one line, but additional information about each file and directory will be displayed.

`-rw-r—​r--`
Type of file and permissions of the file. Note that a regular file will begin with dash, and a directory will start with `d`.

`1`
Number of links to the file.

`user staff`
Specifies ownership of the file. `user` is the owner of the file, and the file is also associated with the `staff` group.

`3606`
Size of the file in bytes.

`Jan 13 2017`
Time stamp of the last modification to the file.

`report2018.txt`
Name of the file.

Subjects such as ownership, permissions and links will be covered in future sections. As you can see, the long list version of the `ls` is oftentimes preferable to the default.

### Additional ls Options
Below are some of the ways that we most commonly use the ls command. As you can see, the user can combine many options together to get the desired output.

`ls -lh`
Combining long list with human readable file sizes will give us useful suffixes such as `M` for megabytes or `K` for kilobytes.

`ls -d */`
The `-d` option will list directories but not their contents. Combining this with `*/` will show only subdirectories and no files.

`ls -lt`
Combines long list with the option to sort by modification time. The files with the most recent changes will be at the top, and files with the oldest changes will be at the bottom. But this order can be reversed with:

`ls -lrt`
Combines long list with sort by (modification) time, combined with `-r` which reverses the sort. Now files with the most recent changes are at the bottom of the list. In addition to sorting by modification time, files can also be sorted by access time or by status time.

`ls -lX`
Combines long list with the option to sort by file eXtension. This will, for example, group all files ending with `.txt` together, all files ending with `.jpg` together, and so on.

`ls -S`
The `-S` sorts by file `size`, much in the same way as `-t` and `-X` sort by time and extension respectively. The largest files will come first, and smallest last. Note that the contents of subdirectories are not included in the sort.

`ls -R`
The `-R` option will modify the `ls` command to display a recursive list. What does this mean?

### Recursion in Bash
Recursion refers to a situation when “something is defined in terms of itself”. Recursion is a very important concept in computer science, but here its meaning is far simpler. Let’s consider our example from before:
```
$ ls ~
Documents
```
We know from before that user has a home directory, and in this directory there is one subdirectory. ls has up until now only shown us the files and subdirectories of a location, but cannot tell us the contents of these subdirectories. In these lessons, we have been using the tree command when we wanted to display the contents of many directories. Unfortunately, tree is not one of the core utilities of Linux and thus is not always available. Compare the output of tree with the output of ls -R in the following examples:
```
$ tree /home/user
user
└── Documents
    ├── Mission-Statement
    └── Reports
        └── report2018.txt

$ ls -R ~
/home/user/:
Documents

/home/user/Documents:
Mission-Statement
Reports

/home/user/Documents/Reports:
report2018.txt
```

As you can see, with the recursive option, we get a far longer list of files. In fact, it is as if we ran the `ls` command in `user` 's home directory, and encountered one subdirectory. Then, we entered into that subdirectory and ran the `ls` command again. We encountered the file `Mission-Statement` and another subdirectory called `Reports`. And again, we entered into the subdirectory, and ran the `ls` command again. Essentially, running `ls -R` is like telling Bash: “Run `ls` here, and repeat the command in every subdirectory that you find.”

Recursion is particularly important in file modification commands such as copying or removing directories. For example, if you wanted to copy the `Documents` subdirectory, you would need to specify a recursive copy in order to extend this command to all subdirectories.

## Lesson 2.4 Creating, Moving and Deleting Files

This lesson covers managing files and directories on Linux using command line tools.

A file is a collection of data with a name and set of attributes. If, for example, you were to transfer some photos from your phone to a computer and give them descriptive names, you would now have a bunch of image files on your computer. These files have attributes such as the time the file was last accessed or modified.

A directory is a special kind of file used to organize files. A good way to think of directories is like the file folders used to organize papers in a file cabinet. Unlike paper file folders, you can easily put directories inside of other directories.

The command line is the most effective way to manage files on a Linux system. The shell and command line tools have features that make using the command line faster and easier than a graphical file manager.

In this section you will use the commands `ls`, `mv`, `cp`, `pwd`, `find`, `touch`, `rm`, `rmdir`, `echo`, `cat`, and `mkdir` to manage and organize files and directories.

### Case Sensitivity
Unlike Microsoft Windows, file and directory names on Linux systems are case sensitive. This means that the names `/etc/` and `/ETC/` are different directories. Try the following commands:
```
$ cd /
$ ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
$ cd ETC
bash: cd: ETC: No such file or directory
$ pwd
/
$ cd etc
$ pwd
/etc
```
The `pwd` shows you the directory you are currently in. As you can see, changing to `/ETC` did not work as there is no such directory. Changing into the directory `/etc` which exists, did succeed.

### Creating Directories
The `mkdir` command is used to create directories.

Let’s create a new directory within our home directory:
```
$ cd ~
$ pwd
/home/user
$ ls
Desktop  Documents  Downloads
$ mkdir linux_essentials-2.4
$ ls
Desktop  Documents  Downloads  linux_essentials-2.4
$ cd linux_essentials-2.4
$ pwd
/home/emma/linux_essentials-2.4
```
For the duration of this lesson, all commands will take place within this directory or in one of its subdirectories.

To easily return to the lesson directory from any other position in your file system, you can use the command:
```
$ cd ~/linux_essentials-2.4
```

The shell interprets the `~` character as your home directory.

When you’re in the lesson directory, create some more directories which we will use for the exercises. You can add all the directory names, separated by spaces, to `mkdir`:
```
$ mkdir creating moving copying/files copying/directories deleting/directories deleting/files globs
mkdir: cannot create directory ‘copying/files’: No such file or directory
mkdir: cannot create directory ‘copying/directories’: No such file or directory
mkdir: cannot create directory ‘deleting/directories’: No such file or directory
mkdir: cannot create directory ‘deleting/files’: No such file or directory
$ ls
creating  globs  moving
```
Notice the error message and that only `moving`, `globs`, and `creating` were created. The `copying` and `deleting` directories don’t exist yet. `mkdir`, by default, won’t create a directory inside of a directory that does not exist. The `-p` or `--parents` option instructs `mkdir` to create parent directories if they do not exist. Try the same `mkdir` command with the `-p` option:
```
$ mkdir -p creating moving copying/files copying/directories deleting/directories deleting/files globs
```
Now you don’t get any error messages. Let’s see which directories exist now:
```
$ find
.
./creating
./moving
./globs
./copying
./copying/files
./copying/directories
./deleting
./deleting/directories
./deleting/files
```
The `find` program is usually used to search for files and directories, but without any options, it will show you a listing of all the files, directories, and sub-directories of your current directory.
```
Tip
> When listing the contents of a directory with `ls`, the `-t` and `-r` options are particularly handy. They sort the output by time (`-t`) and reverse the sorting order (`-r`). In this case, the newest files will be at the bottom of the output.
```
### Creating Files
Typically, files will be created by the programs that work with the data stored in the files. An empty file can be created using the `touch` command. If you run `touch` on an existing file, the file’s contents won’t be changed, but the files modification timestamp will be updated.

Run the following command to create some files for the globbing lesson:
```
$ touch globs/question1 globs/question2012 globs/question23 globs/question13 globs/question14
$ touch globs/star10 globs/star1100 globs/star2002 globs/star2013
```
Now let’s verify all files exist in the globs directory:
```
$ cd globs
$ ls
question1   question14    question23  star1100  star2013
question13  question2012  star10      star2002
```
Notice how `touch` created the files? You can view the contents of a text file with the `cat` command. Try it on one of the files you just created:
```
$ cat question14
```
Since `touch` creates empty files, you should get no output. You can use `echo` with `>` to create simple text files. Try it:
```
$ echo hello > question15
$ cat question15
hello
```
`echo` displays text on the command line. The `>` character instructs the shell to write output of a command to the specified file instead of your terminal. This leads to the output of `echo`, `hello` in this case, being written to the file `question15`. This isn’t specific to `echo`, it can be used with any command.
```
Warning
> Be careful when using >! If the named file already exists, it will be overwritten!
```

### Renaming Files
Files are moved and renamed with the `mv` command.

Set your working directory to the `moving` directory:
```
$ cd ~/linux_essentials-2.4/moving
```
Create some files to practice with. By now, you should already be familiar with these commands:
```
$ touch file1 file22
$ echo file3 > file3
$ echo file4 > file4
$ ls
file1  file22  file3  file4
```
Suppose `file22` is a typo and should be `file2`. Fix it with the `mv` command. When renaming a file, the first argument is the current name, the second is the new name:
```
$ mv file22 file2
$ ls
file1  file2  file3  file4
```
Be careful with the `mv` command. If you rename a file to the name of an existing file, it will overwrite it. Let’s test this with `file3` and `file4`:
```
$ cat file3
file3
$ cat file4
file4
$ mv file4 file3
$ cat file3
file4
$ ls
file1  file2  file3
```
Notice how the contents of `file3` is now `file4`. Use the `-i` option to make `mv` prompt you if you are about to overwrite an existing file. Try it:
```
$ touch file4 file5
$ mv -i file4 file3
mv: overwrite ‘file3’? y
```

### Moving Files
Files are moved from one directory to another with the `mv` command.

Create a few directories to move files into:
```
$ cd ~/linux_essentials-2.4/moving
$ mkdir dir1 dir2
$ ls
dir1  dir2  file1  file2  file3  file5
```
Move `file1` into `dir1`:
```
$ mv file1 dir1
$ ls
dir1  dir2  file2  file3  file5
$ ls dir1
file1
```
Notice how the last argument to `mv` is the destination directory. Whenever the last argument to `mv` is a directory, files are moved into it. Multiple files can be specified in a single `mv` command:
```
$ mv file2 file3 dir2
$ ls
dir1  dir2  file5
$ ls dir2
file2  file3
```
It is also possible to use mv to move and rename directories. Rename `dir1` to `dir3`:
```
$ ls
dir1  dir2  file5
$ ls dir1
file1
$ mv dir1 dir3
$ ls
dir2  dir3  file5
$ ls dir3
file1
```
### Deleting Files and Directories
The `rm` command can delete files and directories, while the `rmdir` command can only delete directories. Let’s clean up the `moving` directory by deleting `file5`:
```
$ cd ~/linux_essentials-2.4/moving
$ ls
dir2  dir3  file5
$ rmdir file5
rmdir: failed to remove ‘file5’: Not a directory
$ rm file5
$ ls
dir2  dir3
```
By default `rmdir` can only delete empty directories, therefore we had to use `rm` to delete a regular file. Try to delete the `deleting` directory:

```
$ cd ~/linux_essentials-2.4/
$ ls
copying  creating  deleting  globs  moving
$ rmdir deleting
rmdir: failed to remove ‘deleting’: Directory not empty
$ ls -l deleting
total 0
drwxrwxr-x. 2 emma emma 6 Mar 26 14:58 directories
drwxrwxr-x. 2 emma emma 6 Mar 26 14:58 files
```

By default, `rmdir` refuses to delete a directory that is not empty. Use `rmdir` to remove one of the empty subdirectories of the deleting directory:
```
$ ls -a deleting/files
.  ..
$ rmdir deleting/files
$ ls -l deleting
directories
```
Deleting large numbers of files or deep directory structures with many subdirectories may seem tedious, but it is actually easy. By default, `rm` only works on regular files. The `-r` option is used to override this behavior. Be careful, `rm -r` is an excellent foot gun! When you use the `-r` option, rm will not only delete any directories, but everything within that directory, including subdirectories and their contents. See for yourself how `rm -r` works:
```
$ ls
copying  creating  deleting  globs  moving
$ rm deleting
rm: cannot remove ‘deleting’: Is a directory
$ ls -l deleting
total 0
drwxrwxr-x. 2 emma emma 6 Mar 26 14:58 directories
$ rm -r deleting
$ ls
copying  creating  globs  moving
```
Notice how `deleting` is gone, even tough it was not empty? Like `mv`, `rm` has a `-i` option to prompt you before doing anything. Use `rm -ri` to remove directories from `moving` section that are no longer needed:
```
$ find
.
./creating
./moving
./moving/dir2
./moving/dir2/file2
./moving/dir2/file3
./moving/dir3
./moving/dir3/file1
./globs
./globs/question1
./globs/question2012
./globs/question23
./globs/question13
./globs/question14
./globs/star10
./globs/star1100
./globs/star2002
./globs/star2013
./globs/question15
./copying
./copying/files
./copying/directories
$ rm -ri moving
rm: descend into directory ‘moving’? y
rm: descend into directory ‘moving/dir2’? y
rm: remove regular empty file ‘moving/dir2/file2’? y
rm: remove regular empty file ‘moving/dir2/file3’? y
rm: remove directory ‘moving/dir2’? y
rm: descend into directory ‘moving/dir3’? y
rm: remove regular empty file ‘moving/dir3/file1’? y
rm: remove directory ‘moving/dir3’? y
rm: remove directory ‘moving’? y
```
### Copying Files and Directories
The `cp` command is used to copy files and directories. Copy a few files into the `copying` directory:

```
$ cd ~/linux_essentials-2.4/copying
$ ls
directories  files
$ cp /etc/nsswitch.conf files/nsswitch.conf
$ cp /etc/issue /etc/hostname files
```

If the last argument is a directory, `cp` will create a copy of the previous arguments inside the directory. Like `mv`, multiple files can be specified at once, as long as the target is a directory.

When both operands of `cp` are files and both files exist, `cp` overwrites the second file with a copy of the first file. Let’s practice this by overwrite the `issue` file with the `hostname` file:

```
$ cd ~/linux_essentials-2.4/copying/files
$ ls
hostname  issue  nsswitch.conf
$ cat hostname
mycomputer
$ cat issue
Debian GNU/Linux 9 \n \l

$ cp hostname issue
$ cat issue
mycomputer
```

Now let’s try to create a copy of the `files` directory within the `directories` directory:

```
$ cd ~/linux_essentials-2.4/copying
$ cp files directories
cp: omitting directory ‘files’
```
As you can see, `cp` by default only works on individual files. To copy a directory, you use the `-r` option. Keep in mind that the `-r` option will cause `cp` to also copy the contents of the directory you are copying:

```
$ cp -r files directories
$ find
.
./files
./files/nsswitch.conf
./files/fstab
./files/hostname
./directories
./directories/files
./directories/files/nsswitch.conf
./directories/files/fstab
./directories/files/hostname
```

Notice how when an existing directory was used as the destination, `cp` creates a copy of the source directory inside of it? If the destination doesn’t exist, it will create it and fill it with the contents of the source directory:

```
$ cp -r files files2
$ find
.
./files
./files/nsswitch.conf
./files/fstab
./files/hostname
./directories
./directories/files
./directories/files/nsswitch.conf
./directories/files/fstab
./directories/files/hostname
./files2
./files2/nsswitch.conf
./files2/fstab
./files2/hostname
```

### Globbing
What is commonly referred to as globbing is a simple pattern matching language. Command line shells on Linux systems use this language to refer to groups of files whose names match a specific pattern. POSIX.1-2017 specifies the following pattern matching characters:

`*`

Matches any number of any character, including no characters

`?`

Matches any one character

`[]`

Matches a class of characters

In English, this means you can tell your shell to match a pattern instead of a literal string of text. Usually Linux users specify multiple files with a glob instead of typing out each file name. Run the following commands:

```
$ cd ~/linux_essentials-2.4/globs
$ ls
question1   question14  question2012  star10    star2002
question13  question15  question23    star1100  star2013
$ ls star1*
star10  star1100
$ ls star*
star10  star1100  star2002  star2013
$ ls star2*
star2002  star2013
$ ls star2*2
star2002
$ ls star2013*
star2013
```

The shell expands `*` to any number of anything, so your shell interprets `star*` to mean anything in the relevant context that starts with `star`. When you run the command `ls star*`, your shell doesn’t run the `ls` program with an argument of `star*`, it looks for files in the current directory that match the pattern `star*` (including just `star`), and turns each file matching the pattern into an argument to `ls`:

```
$ ls star*
```

as far as `ls` is concerned is the equivalent of

```
$ ls star10  star1100  star2002  star2013
```

The `*` character doesn’t mean anything to `ls`. To prove this, run the following command:

```
$ ls star\*
ls: cannot access star*: No such file or directory
```

When you precede a character with a `\`, you are instructing your shell not to interpret it. In this case, you want `ls` to have an argument of `star*` instead of what the glob `star*` expands to.

The `?` expands to any single character. Try the following commands to see for yourself:

```
$ ls
question1   question14  question2012  star10    star2002
question13  question15  question23    star1100  star2013
$ ls question?
question1
$ ls question1?
question13  question14  question15
$ ls question?3
question13  question23
$ ls question13?
ls: cannot access question13?: No such file or directory
```

The `[]` brackets are used to match ranges or classes of characters. The `[]` brackets work like they do in POSIX regular expressions except with globs the `^` is used instead of `!`.

Create some files to experiment with:

```
$ mkdir brackets
$ cd brackets
$ touch file1 file2 file3 file4 filea fileb filec file5 file6 file7
```

Ranges within `[]` brackets are expressed using a `-`:

```
$ ls
file1  file2  file3  file4  file5  file6  file7  filea  fileb  filec
$ ls file[1-2]
file1  file2
$ ls file[1-3]
file1  file2  file3
```

Multiple ranges can be specified:
```
$ ls file[1-25-7]
file1  file2  file5  file6  file7
$ ls file[1-35-6a-c]
file1  file2  file3  file5  file6  filea  fileb  filec
```

Square brackets can also be used to match a specific set of characters.

```
$ ls file[1a5]
file1  file5  filea
```

You can also use the `^` character as the first character to match everything except certain characters.

```
$ ls file[^a]
file1  file2  file3  file4  file5  file6  file7  fileb  filec
```

The last thing we will cover in this lesson is character classes. To match a character class, you use `[:classname:]`. For example, to use the digit class, which matches numerals, you would do something like this:

```
$ ls file[[:digit:]]
file1  file2  file3  file4  file5  file6  file7
$ touch file1a file11
$ ls file[[:digit:]a]
file1  file2  file3  file4  file5  file6  file7  filea
$ ls file[[:digit:]]a
file1a
```
The glob `file[[:digit:]a]`, matches `file` followed by a digit or `a`.


The character classes supported depends on your current locale. POSIX requires the following character classes for all locales:

`[:alnum:]`
- Letters and numbers.

`[:alpha:]`
- Upper or lowercase letters.

`[:blank:]`
- Spaces and tabs.

`[:cntrl:]`
- Control characters, e.g. backspace, bell, NAK, escape.

`[:digit:]`
- Numerals (`0123456789`).

`[:graph:]`
- Graphic characters (all characters except `ctrl` and the space character)

`[:lower:]`
- Lowercase letters (`a-z`).

`[:print:]`
- Printable characters (`alnum`, `punct`, and the space character).

`[:punct:]`
- Punctuation characters, i.e. `!`, `&`, `"`.

`[:space:]`
- Whitespace characters, e.g. tabs, spaces, newlines.

`[:upper:]`
- Uppercase letters (`A-Z`).

`[:xdigit:]`
- Hexadecimal numerals (usually `0123456789abcdefABCDEF`).


___

This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/010-160/)