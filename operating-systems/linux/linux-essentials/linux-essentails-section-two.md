# Linux Essentials

## Lesson 2-1

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

current_directory`
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
Most commands display a short overview of available options when they are run with the --help parameter. We will learn additional ways to learn more about Linux commands soon.
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
The line with `TWOWORDS=` is a Bash variable that we have created ourselves. We will introduce variables later. This is just meant to show you how quoting affects the output of variables.
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
Variables are not persistent. When the shell in which they were set is closed, all variables and their contents are lost. Most shells provide configuration files that contain variables which are set whenever a new shell is started. Variables that should be set permanently must be added to one of these configuration files.
```
### Manipulating Variables
As a system administrator, you will need to create, modify or remove both local and environment variables.

### Working with Local Variables
You can set up a local variable by using the = (equal) operator. A simple assignment will create a local variable:

`$ greeting=hello`
```
Note
Don’t put any space before or after the = operator.
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
Make sure to use single quotes in the example above.
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
unset requires the name of the variable as an argument. Therefore you may not add $ to the name, as this would resolve the variable and pass the variable’s value to unset instead of the variable’s name.
```
### Working with Global Variables
To make a variable available to subprocesses, turn it from a local into an environment variable. This is done by the command export. When it is invoked with the variable name, this variable is added to the shell’s environment:
```
$ greeting=hello
$ export greeting
```
```
Note
Again, make sure to not use $ when running export as you want to pass the name of the variable instead of its contents.
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
___

This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/010-160/)