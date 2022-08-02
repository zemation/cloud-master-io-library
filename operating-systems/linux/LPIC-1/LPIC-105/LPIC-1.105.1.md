# LPIC-1.105: Shells and shell Scripting

## Lesson 105.1: Customize and usee the shell environment

The shell is arguably the most powerful tool in a Linux system and can be defined as an interface between the user and the kernel of the operating system. It interprets commands entered by the user. Therefore, all system administrators must be skilled in using the shell. As we may certainly know by now, the Bourne Again Shell (Bash) is the de facto shell for the vast majority of Linux distributions.

Once started, the first thing Bash — or any other shell for that matter — does is executing a series of startup scripts. These scripts customize the session’s environment. There are both system wide and user specific scripts. We can put our personal preferences or settings that best fit our users' needs in these scripts in the form of variables, aliases and functions.

The exact series of startup files depends on a very important parameter: the type of shell. Let us have a look at the variety of shells that exist.

### Shell Types: Interactive vs. Non-Interactive and Login vs. Non-Login
To start with, let us clarify the concepts of interactive and login in the context of shells:

`Interactive / Non-interactive Shells`
- This kind of shell refers to the interaction that takes place between the user and the shell: The user provides input by typing commands into the terminal using the keyboard; the shell provides output by printing messages on the screen.

`Login / Non-login Shells`
- This kind of shell refers to the event of a user accessing a computer system providing its credentials, such as username and password.

Both interactive and non-interactive shells can be either login or non-login and any possible combination of these types has its specific uses.

Interactive login shells are executed when users log into the system and are used to customize users' configurations according to their needs. A good example of this type of shell would be that of a group of users belonging to the same department who need a particular variable set in their sessions.

By interactive non-login shells we refer to any other shells opened by the user after logging into the system. Users use these shells during sessions to carry out maintenance and administrative tasks such as setting variables, the time, copying files, writing scripts, etc.

On the other hand, non-interactive shells do not require any kind of human interaction. Thus, these shells do not ask the user for input and their output — if any — is in most cases written to a log.

Non-interactive login shells are quite rare and impractical. Their uses are virtually non-existent and we will only comment on them for the sake of insight into shell behaviour. Some odd examples include forcing a script to be run from a login shell with `/bin/bash --login <some_script>` or piping the standard output (stdout) of a command into the standard input (stdin) of an ssh connection:

```
<some_command> | ssh <some_user>@<some_server>
```

As for non-interactive non-login shell there is neither interaction nor login on behalf of the user, so we are referring here to the use of automated scripts. These scripts are mostly used to carry out repetitive administrative and maintenance tasks such as those included in cronjobs. In such cases, `bash` does not read any startup files.

### Opening a Terminal
When we are in a desktop environment, we can either open a terminal application or switch to one of the system consoles. Therefore, a new shell is either a `pts` shell when opened from a terminal emulator in the GUI or a `tty` shell when run from a system console. In the first case we are not dealing with a terminal but with a terminal emulator. As part of graphical sessions, terminal emulators like gnome-terminal or konsole are very feature-rich and user-friendly as compared to text-based user interface terminals. Less feature-rich terminal emulators include — amongst others — XTerm and sakura.

Using the `Ctrl`+`Alt`+`F1`-`F6` combos we can go to the console logins which open an interactive text-based login shell. `Ctrl`+`Alt`+`F7` will take the session back into the Desktop.

```
Note
tty stands for teletypewritter; pts stands for pseudo terminal slave. For more information: man tty and man pts.
```

### Launching Shells with `bash`
After logging in, type `bash` into a terminal to open a new shell. Technically, this shell is a child process of the current shell.

While starting the `bash` child process, we can specify various switches to define which kind of shell we want to start. Here some important `bash` invocation options:

`bash -l` or `bash --login`
- will invoke a login shell.

`bash -i`
- will invoke an interactive shell.

`bash --noprofile`
- with login shells will ignore both the system-wide startup file `/etc/profile` and the user-level startup files `~/.bash_profile`, `~/.bash_login` and `~/.profile`.

`bash --norc`
- with interactive shells will ignore both the system-wide startup file `/etc/bash.bashrc` and the user-level startup file `~/.bashrc`.

`bash --rcfile <file>`
- with interactive shells will take `<file>` as the startup file ignoring system wide `/etc/bash.bashrc` and user-level `~/.bashrc`.

We will discus the various startup files below.

### Launching Shells with `su` and `sudo`
Through the use of these two similar programs we can obtain specific types of shells:

`su`
- Change user ID or become superuser (`root`). With this command we can invoke both login and non-login shells:

  - `su - user2`, `su -l user2` or `su --login user2` will start an interactive login shell as `user2`.
  - `su user2` will start an interactive non-login shell as `user2`.
  - `su - root` or `su -` will start an interactive login shell as `root`.
  - `su root` or `su` will start an interactive non-login shell as `root`.

`sudo`
- Execute commands as another user (including the supersuser). Because this command is mainly used to gain root privileges temporarily, the user using it must be in the `sudoers` file. To add users to `sudoers` we need to become `root` and then run:

```
root@debian:~# usermod -aG sudo user2
```

Just as `su`, `sudo` allows us to invoke both login and non-login shells:

- `sudo su - user2`, `sudo su -l user2` or `sudo su --login user2` will start an interactive login shell as `user2`.
- `sudo su user2` will start an interactive non-login shell as `user2`.
- `sudo -u user2 -s` will start an interactive non-login shell as `user2`.
- `sudo su - root` or `sudo su -` will start an interactive login shell as `root`.
- `sudo -i` will start an interactive login shell as `root`.
- `sudo -i <some_command>` will start an interactive login shell as `root`, run the command and return to the original user.
- `sudo su root` or `sudo su` will start an interactive non-login shell as `root`.
- `sudo -s` or `sudo -u root -s` will start a non-login shell as `root`.

When using either `su` or `sudo`, it is important to consider our particular case scenario for starting a new shell: Do we need the target user’s environment or not? If so, we would use the options which invoke login shells; if not, those which invoke non-login shells.

### What Shell Type Do We Have?
In order to find out what type of shell we are working at, we can type `echo $0` into the terminal and get the following output:

Interactive login
- `-bash` or `-su`

Interactive non-login
- `bash` or `/bin/bash`

Non-interactive non-login (scripts)
- `<name_of_script>`

### How Many Shells Do We Have?
To see how many `bash` shells we have up and running in the system, we can use the command `ps aux | grep bash`:

```
user2@debian:~$ ps aux | grep bash
user2       5270  0.1  0.1  25532  5664 pts/0    Ss   23:03   0:00 bash
user2       5411  0.3  0.1  25608  5268 tty1     S+   23:03   0:00 -bash
user2       5452  0.0  0.0  16760   940 pts/0    S+   23:04   0:00 grep --color=auto bash
```

`user2` at `debian` has logged into a GUI (or X Window System) session and opened gnome-terminal, then  she has pressed `Ctrl`+`Alt`+`F1` to go into a `tty` terminal session. Finally, she has gone back to the GUI session by pressing `Ctrl`+`Alt`+`F7` and typed in the command `ps aux | grep bash`. Thus, the output shows an interactive non-login shell via the terminal emulator (`pts/0`) and an interactive login shell via the proper text-based terminal (`tty1`). Note also how the last field of each line (the command) is `bash` for the former and `-bash` for the latter.

### Where Shells Get their Configuration From: Startup Files
Well, now that we know the shell types that we can find in a Linux system, it is high time that we saw what startup files get executed by what shell. Note that system wide or global scripts are placed in the `/etc/` directory, whereas local or user-level ones are found in the user’s home (`~`). Also, when there is more than one file to be searched, once one is found and run the others are ignored. Explore and study these files yourself with your favorite text editor or by typing `less <startup_file>.`

```
Note
Startup files can be divided into Bash specific (those limited only to bash configurations and commands) and general ones (relating to most shells).
```

#### Interactive Login Shell
##### Global Level
`/etc/profile`
- This is the system-wide `.profile` file for the Bourne shell and Bourne compatible shells (`bash` included). Through a series of `if` statements this file sets a number of variables such as `PATH` and `PS1` accordingly as well as sourcing — if they exist — both the file `/etc/bash.bashrc` and those in the directory `/etc/profile.d`.

`/etc/profile.d/*`
- This directory may contain scripts that get executed by `/etc/profile`.

##### Local Level
`~/.bash_profile`
- This Bash specific file is used for configuring the user environment. It can also be used to source both `~/.bash_login` and `~/.profile`.

`~/.bash_login`
- Also Bash specific, this file will only be executed if there is no `~/.bash_profile` file. Its name suggests that it should be used to run commands needed on login.

`~/.profile`
- This file is not Bash specific and gets sourced only if neither `~/.bash_profile` nor `~/.bash_login` exist — which is normally the case. Thus, the main purpose of `~/.profile` is that of checking if a Bash shell is being run and — if so — sourcing `~/.bashrc` if it exists. It usually sets the variable `PATH` so that it includes the user’s private `~/bin` directory if it exists.

`~/.bash_logout`
If it exists, this Bash specific file does some clean-up operations when exiting the shell. This can be convenient in such cases as those in remote sessions.

##### Exploring Interactive Login Shell Configuration Files
Let us show some of these files in action by modifying `/etc/profile` and `/home/user2/.profile`. We will append to each a line reminding us the file being executed:

```
root@debian:~# echo 'echo Hello from /etc/profile' >> /etc/profile
root@debian:~# echo 'echo Hello from ~/.profile' >> ~/.profile
```

```
Note
Two redirection operators >> append the output of a command into an existing file — not overwriting it. If the file does not exist, though, it will be created.
```

Thus, through the output of their respective `echo` commands we will know when each of these files is read and executed. To prove it, let us see what happens when `user2` logs in via `ssh` from another machine:

```
user2@debian:~$ ssh user2@192.168.1.6
user2@192.168.1.6's password:
Linux debian 4.9.0-8-amd64 #1 SMP Debian 4.9.130-2 (2018-10-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Nov 27 19:57:19 2018 from 192.168.1.10
Hello from /etc/profile
Hello from /home/user2/.profile
```

As the last two lines show, it worked. Also, note three things:

- The global file was run first.
- There were no `.bash_profile` or `.bash_login` files in he home directory of `user2`.
- The tilde (`~`) expanded to the absolute path of the file (`/home/user2/.profile`).

#### Interactive Non-Login Shell
##### Global Level
`/etc/bash.bashrc`
- This is the system-wide `.bashrc` file for interactive `bash` shells. Through its execution `bash` makes sure it is being run interactively, checks the window size after each command (updating the values of `LINES` and `COLUMNS` if necessary) and sets some variables.

##### Local Level
`~/.bashrc`
- In addition to carrying out similar tasks to those described for `/etc/bash.bashrc` at a user level (such as checking the window size or if being run interactively), this Bash specific file usually sets some history variables and sources `~/.bash_aliases` if it exists. Apart from that, this file is normally used to store users' specific aliases and functions.

Likewise, it is also worthwhile noting that `~/.bashrc` is read if `bash` detects its `<stdin>` is a network connection (as it was the case with the Secure Shell (SSH) connection in the example above).

##### Exploring Interactive Non-Login Shell Configuration Files
Let us now modify `/etc/bash.bashrc` and `/home/user2/.bashrc`:

```
root@debian:~# echo 'echo Hello from /etc/bash.bashrc' >> /etc/bash.bashrc
root@debian:~# echo 'echo Hello from ~/.bashrc' >> ~/.bashrc
```

And this is what happens when `user2` starts a new shell:

```
user2@debian:~$ bash
Hello from /etc/bash.bashrc
Hello from /home/user2/.bashrc
```

Again, the two files were read and executed.

```
Warning
Remember, because of the order in which files are run, local files take precedence over global ones.
```

#### Non-Interactive Login Shell
A non-interactive shell with the `-l` or `--login` options is forced to behave like a login shell and so the startup files to be run will be the same as those for interactive login shells.

To prove it, let us write a simple script and make it executable. We will not include any shebangs because we will be invoking the bash executable (`/bin/bash` with the login option) from the command line.

We create the script `test.sh` containing the line `echo 'Hello from a script'` so that we can prove the script runs successfully:

```
user2@debian:~$ echo "echo 'Hello from a script'" > test.sh
```

We make our script executable:

```
user2@debian:~$ chmod +x ./test.sh
```

Finally, we invoke `bash` with the `-l` option to run the script:

```
user2@debian:~$ bash -l ./test.sh
Hello from /etc/profile
Hello from /home/user2/.profile
Hello from a script
```

It works! Before running the script, the login took place and both `/etc/profile` and `~/.profile` were executed.

```
Note
We will learn about shebangs and all other aspects of shell scripting in future lessons.
```

Let us now have the standard output (stdout) of the `echo` command into the standard input (stdin) of an `ssh` connection by means of a pipe (`|`):

```
user2@debian:~$ echo "Hello-from-a-noninteractive-login-shell" | ssh user2@192.168.1.6
Pseudo-terminal will not be allocated because stdin is not a terminal.
user2@192.168.1.6's password:
Linux debian 4.9.0-8-amd64 #1 SMP Debian 4.9.130-2 (2018-10-27) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Hello from /etc/profile
Hello from /home/user2/.profile
-bash: line 1: Hello-from-a-noninteractive-login-shell: command not found
```

Again, `/etc/profile` and `~/.profile` are executed. Other than that, the first and last lines of the output are quite telling as far as the behaviour of the shell is concerned.

#### Non-Interactive Non-Login Shell
Scripts do not read any of the files listed above but look for the environment variable `BASH_ENV`, expand its value if needed and use it as the name of a startup file to read and execute commands. We will learn more about environment variables in the next lesson.

As mentioned above, typically `/etc/profile` and `~/.profile` make sure that both `/etc/bash.bashrc` and `~/.bashrc` get executed after a successful login. The output of the following command shows this phenomenon:

```
root@debian:~# su - user2
Hello from /etc/bash.bashrc
Hello from /etc/profile
Hello from /home/user2/.bashrc
Hello from /home/user2/.profile
```

Bearing in mind the lines we previously appended to the startup scripts — and invoking an interactive-login shell at user-level with `su - user2` — the four lines of output can be explained as follows:
1. `Hello from /etc/bash.bashrc` means `/etc/profile` has sourced `/etc/bash.bashrc`.
2. `Hello from /etc/profile` means `/etc/profile` has been fully read and executed.
3. Hello from `/home/user2/.bashrc` means `~/.profile` has sourced `~/.bashrc`.
4. Hello from `/home/user2/.profile` means `~/.profile` has been fully read and executed.

Note how with `su - <username>` (also `su -l <username>` and `su --login <username>`) we guarantee the invocation of a login shell, whereas `su <username>` would have only invoked `/etc/bash.bashrc` and `~/.bashrc`.

### Sourcing Files
In the previous sections we have discussed that some startup scripts include or execute other scripts. This mechanism is called sourcing and is explained in this section.

### Sourcing Files with `.`
The dot (`.`) is normally found in startup files.

In the `.profile` file of our Debian server we can find — for instance — the following block:

```
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
	. "$HOME/.bashrc"
    fi
```

We already saw how the execution of a script may lead to that of another. Thus, the `if` statement guarantees that the file `$HOME/.bashrc` — if it exists (`-f`) — will be sourced (i.e. read and executed) at login:

```
. "$HOME/.bashrc"
```

```
Note
As we will learn in the next lesson, $HOME is an environment variable that expands to the absolute path of the user’s home directory.
```

Furthermore, we can use the `.` whenever we have modified a startup file and want to make the changes effective without a reboot. For example we can:

- add an alias to `~/.bashrc`:

```
user2@debian:~$ echo "alias hi='echo We salute you.'" >> ~/.bashrc
```

```
Warning
When sending the output of one command into a file, remember not to mistake append (>>) with overwrite (>).
```

- output the last line of `~/.bashrc` to check everything went fine:

```
user2@debian:~$ tail -n 1 !$
tail -n 1 ~/.bashrc
alias hi='echo We salute you.'
```

```
Note
!$ expands to the last argument from the previous command, in our case: ~/.bashrc.
```

- source the file by hand:

```
user2@debian:~$ . ~/.bashrc
```

and invoke the alias to prove it works:

```
user2@debian:~$ hi
We salute you.
```

```
Note
Refer to the next lesson to learn about aliases and variables.
```

#### Sourcing Files with source
The `source` command is a synonym for `.`. So to source `~/.bashrc` we can also do it like this:

```
user2@debian:~$ source ~/.bashrc
```

### The Origin of Shell Startup Files: `SKEL`
`SKEL` is a variable whose value is the absolute path to the `skel` directory. This directory serves as a template for the file system structure of users' home directories. It includes the files that will be inherited by any new user accounts created (including, of course, the configuration files for shells). `SKEL` and other related variables are stored in `/etc/adduser.conf`, which is the configuration file for `adduser`:

```
user2@debian:~$ grep SKEL /etc/adduser.conf
# The SKEL variable specifies the directory containing "skeletal" user
SKEL=/etc/skel
# If SKEL_IGNORE_REGEX is set, adduser will ignore files matching this
SKEL_IGNORE_REGEX="dpkg-(old|new|dist|save)"
```

`SKEL` is set to `/etc/skel`; thus, the startup scripts that configure our shells lie therein:

```
user2@debian:~$ ls -a /etc/skel/
.  ..  .bash_logout  .bashrc  .profile
```

```
Warning
Remember, files starting with a . are hidden, so we must use ls -a to see them when listing directory contents.
```

Let us now create a directory in `/etc/skel` for all new users to store their personal scripts in:

1. As `root` we move into `/etc/skel`:

```
root@debian:~# cd /etc/skel/
root@debian:/etc/skel#
```

2. We list its contents:
```
root@debian:/etc/skel# ls -a
.  ..  .bash_logout  .bashrc  .profile
```

3. We create our directory and check all went as expected:
```
root@debian:/etc/skel# mkdir my_personal_scripts
root@debian:/etc/skel# ls -a
.  ..  .bash_logout  .bashrc  my_personal_scripts  .profile
```

4. Now we delete `user2` together with its `home` directory:
```
root@debian:~# deluser --remove-home user2
Looking for files to backup/remove ...
Removing files ...
Removing user `user2' ...
Warning: group `user2' has no more members.
Done.
```

5. We add `user2` again so that it gets a new home directory:

```
root@debian:~# adduser user2
Adding user `user2' ...
Adding new group `user2' (1001) ...
Adding new user `user2' (1001) with group `user2' ...
Creating home directory `/home/user2' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for user2
Enter the new value, or press ENTER for the default
	Full Name []:
	Room Number []:
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n] y
```

6. Finally, we login as `user2` and list all the files in `/home/user2` to see if everything went as expected:

```
root@debian:~# su - user2
user2@debian:~$ pwd
/home/user2
user2@debian:~$ ls -a
.  ..  .bash_history  .bash_logout  .bashrc  my_personal_scripts  .profile
```

It did.

___

Think of a variable as an imaginary box in which to temporarily place a piece of information. The same as with its initialization scripts, Bash classifies variables as either shell/local (those which live only within the limits of the shell in which they were created) or environment/global (those that are inherited by children shells and/or processes). In fact, in the previous lesson we had a look at shells and their configuration or initialization scripts. It is now convenient to point out that the power of these startup files lies in the fact that they allow us to use variables — as well as aliases and functions — which help us create and customize the shell environment of our choice.

### Variables: Assignment and Reference
A variable can be defined as a name containing a value.

In Bash, giving a value to a name is called variable assignment and it is the way in which we create or set variables. On the other hand, the process of accessing the value contained in the name is called variable referencing.

The syntax for assigning variables is:

```
<variable_name>=<variable_value>
```

For example:

```
$ distro=zorinos
```

The variable `distro` equals `zorinos`, that is to say, there is a portion of memory holding the value `zorinos` — with distro being the pointer to it.

Note, however, that there can not be any space on either side of the equal sign when assigning a variable:

```
$ distro =zorinos
-bash: distro: command not found
$ distro= zorinos
-bash: zorinos: command not found
```

Because of our mistake, Bash read `distro` and `zorinos` as commands.

To reference a variable (that is, to check its value) we use the `echo` command preceding the variable name with a `$` sign:

```
$ echo $distro
zorinos
```

### Variable Names
When choosing the name of variables, there are certain rules that we must take into consideration.

The name of a variable may contain letters (`a-z`,`A-Z`), numbers (`0-9`) and underscores (`_`):

```
$ distro=zorinos
$ echo $distro
zorinos
$ DISTRO=zorinos
$ echo $DISTRO
zorinos
$ distro_1=zorinos
$ echo $distro_1
zorinos
$ _distro=zorinos
$ echo $_distro
zorinos
It may not start with a number or Bash will get confused:

$ 1distro=zorinos
-bash: 1distro=zorinos: command not found
```

It may not contain spaces (not even using quotes); by convention, underscores are used instead:

```
$ "my distro"=zorinos
-bash: my: command not found
$ my_distro=zorinos
$ echo $my_distro
zorinos
```

### Variable Values
Concerning the reference or value of variables it is also important to consider a number of rules.

Variables may contain any alphanumerical characters (`a-z`,`A-Z`,`0-9`) as well as most other characters (`?`,`!`,`*`,`.`,`/`, etc.):

```
$ distro=zorin12.4?
$ echo $distro
zorin12.4?
```

Variable values must be enclosed in quotes if they contain single spaces:

```
$ distro=zorin 12.4
-bash: 12.4: command not found
$ distro="zorin 12.4"
$ echo $distro
zorin 12.4
$ distro='zorin 12.4'
$ echo $distro
zorin 12.4
```

Variable values must also be enclosed in quotes if they contain such characters as those used for redirection (`<`,`>`) or the pipe symbol (`|`). The only thing the following command does is create an empty file named `zorin`:

```
$ distro=>zorin
$ echo $distro

$ ls zorin
zorin
```

This works, though, when we use the quotes:

```
$ distro=">zorin"
$ echo $distro
>zorin
```

However, single and double quotes are not always interchangeable. Depending on what we are doing with a variable (assigning or referencing), the use of one or the other has implications and will yield different results. In the context of variable assignment single quotes take all the characters of the variable value literally, whereas double quotes allow for variable substitution:

```
$ lizard=uromastyx
$ animal='My $lizard'
$ echo $animal
My $lizard
$ animal="My $lizard"
$ echo $animal
My uromastyx
```

On the other hand, when referencing a variable whose value includes some initial (or extra) spaces — sometimes combined with asterisks — it is mandatory that we use double quotes after the `echo` command to avoid field splitting and pathname expansion:

```
$ lizard="   genus   |   uromastyx"
$ echo $lizard
genus | uromastyx
$ echo "$lizard"
   genus   |   uromastyx
```

If the reference of the variable contains a closing exclamation mark, this must be the last character in the string (as otherwise Bash will think we are referring to a `history` event):

```
$ distro=zorin.?/!os
-bash: !os: event not found
$ distro=zorin.?/!
$ echo $distro
zorin.?/!
```

Any backslashes must be escaped with another backslash. Also, if a backslash is the last character in the string and we do not escape it Bash will interpret we want a line break and give us a new line:

```
$ distro=zorinos\
>
$ distro=zorinos\\
$ echo $distro
zorinos\
```

In the next two sections we will sum up the main differences between local and environment variables.

### Local or Shell Variables
Local or shell variables exist only in the shell in which they are created. By convention, local variables are written in lower-case letters.

For the sake of carrying out a few tests, let us create a local variable. As explained above, we choose an appropriate variable name and equate it to an appropriate value. For instance:

```
$ reptile=tortoise
```

Let us now use the echo command to reference our variable and check that everything went as expected:

```
$ echo $reptile
tortoise
```

In certain scenarios — such as when writing scripts — immutability can be an interesting feature of variables. If we want our variables to be immutable, we can either create them `readonly`:

```
$ readonly reptile=tortoise
```

Or turn them so after they have been created:

```
$ reptile=tortoise
$ readonly reptile
```

Now, if we try to change the value of `reptile`, Bash will refuse:

```
$ reptile=lizard
-bash: distro: readonly variable
```

```
Note
To list all readonly variables in our current session, type readonly or readonly -p into the terminal.
```

A useful command when dealing with local variables is `set`.

`set` outputs all of the currently assigned shell variables and functions. Since that can be a lot of lines (try it yourself!), it is recommended to use it in combination with a pager such as `less`:

```
$ set | less
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:complete_fullquote:expand_aliases:extglob:extquote:force_fignore:histappend:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_COMPLETION_COMPAT_DIR=/etc/bash_completion.d
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="4" [1]="4" [2]="12" [3]="1" [4]="release" [5]="x86_64-pc-linux-gnu")
BASH_VERSION='4.4.12(1)-release'
(...)
```

Is our `reptile` variable there?

```
$ set | grep reptile
reptile=tortoise
```

Yes, there it is!

However, `reptile` — being a local variable — will not be inherited by any child processes spawned from the current shell:

```
$ bash
$ set | grep reptile
$
```

And, of course, we cannot echo out its value either:

```
$ echo $reptile
$
```

```
Note
By typing the bash command into the terminal we open a new (child) shell.
```

To unset any variables (either local or global), we use the `unset` command:

```
$ echo $reptile
tortoise
$ unset reptile
$ echo $reptile
$
```

```
Note
unset must be followed by the name of the variable alone (not preceded by the $ symbol).
```

### Global or Environment Variables
Global or environment variables exist for the current shell as well as for all subsequent processes spawned from it. By convention, environment variables are written in uppercase letters:

``` 
$ echo $SHELL
/bin/bash
```

We can recursively pass the value of these variables to other variables and the value of the latter will ultimately expand to that of the former:

```
$ my_shell=$SHELL
$ echo $my_shell
/bin/bash
$ your_shell=$my_shell
$ echo $your_shell
/bin/bash
$ our_shell=$your_shell
$ echo $our_shell
/bin/bash
```

In order for a local shell variable to become an environment variable, the `export` command must be used:

```
$ export reptile
```

With `export reptile` we have turned our local variable into an environment variable so that child shells can recognize it and use it:

```
$ bash
$ echo $reptile
tortoise
```

Likewise, `export` can be used to set and export a variable, all at once:

```
$ export amphibian=frog
```

Now we can open a new instance of Bash and successfully reference the new variable:

```
$ bash
$ echo $amphibian
frog
```

```
Note
With export -n <VARIABLE-NAME> the variable will be turned back into a local shell variable.
```

The `export` command will also give us a list of all existing environment variables when typed on its own (or with the `-p` option):

```
$ export
declare -x HOME="/home/user2"
declare -x LANG="en_GB.UTF-8"
declare -x LOGNAME="user2"
(...)
declare -x PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games"
declare -x PWD="/home/user2"
declare -x SHELL="/bin/bash"
declare -x SHLVL="1"
declare -x SSH_CLIENT="192.168.1.10 49330 22"
declare -x SSH_CONNECTION="192.168.1.10 49330 192.168.1.7 22"
declare -x SSH_TTY="/dev/pts/0"
declare -x TERM="xterm-256color"
declare -x USER="user2"
declare -x XDG_RUNTIME_DIR="/run/user/1001"
declare -x XDG_SESSION_ID="8"
declare -x reptile="tortoise"
```

```
Note
The command declare -x is equivalent to export.
```

Two more commands that can be used to print a list of all environment variables are `env` and `printenv`:

```
$ env
SSH_CONNECTION=192.168.1.10 48678 192.168.1.7 22
LANG=en_GB.UTF-8
XDG_SESSION_ID=3
USER=user2
PWD=/home/user2
HOME=/home/user2
SSH_CLIENT=192.168.1.10 48678 22
SSH_TTY=/dev/pts/0
MAIL=/var/mail/user2
TERM=xterm-256color
SHELL=/bin/bash
SHLVL=1
LOGNAME=user2
XDG_RUNTIME_DIR=/run/user/1001
PATH=/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
_=/usr/bin/env
```

On top of being a synonym for `env`, we can sometimes use `printenv` in a similar way as we use the `echo` command to check for the value of a variable:

```
$ echo $PWD
/home/user2
$ printenv PWD
/home/user2
```

Note, however, that with `printenv` the variable name is not preceded by `$`.

```
Note
PWD stores the path of the present working directory. We will learn about this and other common environment variables later.
```

### Running a Program in a Modified Environment
`env` can be used to modify the shell environment at a program’s time of execution.

To start a new Bash session with as empty an environment as possible — clearing most variables (as well as functions and aliases) — we will use `env` with the `-i` option:

```
$ env -i bash
```

Now most of our environment variables are gone:

```
$ echo $USER
$
```

And only a few remain:

```
$ env
LS_COLORS=
PWD=/home/user2
SHLVL=1
_=/usr/bin/printenv
```

We can also use `env` to set a particular variable for a particular program.

In our previous lesson, when discussing non-interactive non-login shells, we saw how scripts do not read any standard startup files but instead they look for the value of the `BASH_ENV` variable and use it as their startup file if it exists.

Let us demonstrate this process:

1. We create our own startup file called `.startup_script` with the following content:
```
CROCODILIAN=caiman
```

2. We write a Bash script named `test_env.sh` with the following content:
```
#!/bin/bash

echo $CROCODILIAN
```

3. We set the executable bit for our `test_env.sh` script:
```
$ chmod +x test_env.sh
```

4. Finally, we use `env` to set `BASH_ENV` to `.startup_script` for `test_env.sh`:
```
$ env BASH_ENV=/home/user2/.startup_script ./test_env.sh
caiman
```
The `env` command is implicit even if we get rid of it:
```
$ BASH_ENV=/home/user2/.startup_script ./test_env.sh
caiman
```

```
Note
If you do not understand the line #!/bin/bash or the chmod +x command, do not panic! We will learn everything necessary about shell scripting in future lessons. For now, just remember that to execute a script from within its own directory we use ./some_script.
```

### Common Environment Variables
Time now for reviewing some of the most relevant environment variables that are set in Bash configuration files.

`DISPLAY`
- Related to the X server, this variable’s value is normally made up of three elements:

  - The hostname (the absence of it means `localhost`) where the X server is running.
  - A colon as a delimiter.
  - A number (it is normally `0` and refers to the computer’s display).
```
$ printenv DISPLAY
:0
```
- An empty value for this variable means a server without an X Window System. An extra number — as in `my.xserver:0:1` — would refer to the screen number if more than one exists.

`HISTCONTROL`
This variable controls what commands get saved into the `HISTFILE` (see below). Their are three possible values:

`ignorespace`
- Commands starting with a space will not be saved.

`ignoredups`
- A command which is the same as the previous one will not be saved.

`ignoreboth`
- Commands which fall into any of the two previous categories will not be saved.

```
$ echo $HISTCONTROL
ignoreboth
```

`HISTSIZE`
- This sets the number of commands to be stored in memory while the shell session lasts.
```
$ echo $HISTSIZE
1000
```

`HISTFILESIZE`
- This sets the number of commands to be saved in `HISTFILE` both at the start and at the end of the session:
```
$ echo $HISTFILESIZE
2000
```

`HISTFILE`
- The name of the file which stores all commands as they are typed. By default this file is located at `~/.bash_history`:
```
$ echo $HISTFILE
/home/user2/.bash_history
```

```
Note
To view the contents of HISTFILE, we simply type history. Alternatively, we can specify the number of commands we want to see by passing an argument (the number of the most recent commands) to history as in history 3.
```

`HOME`
- This variable stores the absolute path of the current user’s home directory and it is set when the user logs in.
- This piece of code — from `~/.profile` — is self-explanatory (it sources `"$HOME/.bashrc"` if it exists):

```
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
	. "$HOME/.bashrc"
    fi
```

```
Note
If you do not quite understand the if statement, do not worry: just refer to the lessons about shell scripting.
```

Remember that `~` is equivalent to `$HOME`:

```
$ echo ~; echo $HOME
/home/carol
/home/carol
```

```
Note
Commands can be concatenated with a semicolon (;).
```

We can also prove this with an `if` statement:
```
$ if [ ~ == "$HOME" ]; then echo "true"; else echo "false"; fi
true
```

```
Note
Remember: The equals sign = is used for variable assignment. == is used to test for equality.
```

`HOSTNAME`
- This variable stores the TCP/IP name of the host computer:
```
$ echo $HOSTNAME
debian
```

`HOSTTYPE`
- This stores the architecture of the host computer’s processor:
```
$ echo $HOSTTYPE
x86_64
```

`LANG`
- This variable saves the locale of the system:
```
$ echo $LANG
en_UK.UTF-8
```

`LD_LIBRARY_PATH`
- This variable consists of a colon-separated set of directories where shared libraries are shared by programs:
```
$ echo $LD_LIBRARY_PATH
/usr/local/lib
```

`MAIL`
This variable stores the file in which Bash searches for email:
```
$ echo $MAIL
/var/mail/carol
```
Another common value for this variable is `/var/spool/mail/$USER`.

`MAILCHECK`
- This variable stores a numeric value which indicates in seconds the frequency with which Bash checks for new mail:
```
$ echo $MAILCHECK
60
```

`PATH`
This environment variable stores the list of directories where Bash looks for executable files when told to run any program. In our example machine this variable is set through the system-wide `/etc/profile` file:

```
if [ "`id -u`" -eq 0 ]; then
  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
else
  PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games"
fi
export PATH
```

Through the `if` statement the identity of the user is tested and — depending on the result of the test (`root` or otherwise) — we will obtain one `PATH` or the other. Finally the chosen `PATH` is propagated with `export`.

Observe two things regarding the value of PATH:

- Directory names are written using absolute paths.
- The colon is used as a delimiter.
- If we wanted to include the folder `/usr/local/sbin` in the `PATH` for regular users, we will modify the line so that it looks like this:

```
(...)
else
  PATH="/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/local/sbin"
fi
export PATH
```

Now we can see how the value of the variable changes when we login as a regular user:

```
# su - carol
$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/local/sbin
```

```
Note
We could have also added /usr/local/sbin to the user’s PATH at the command line by typing either PATH=/usr/local/sbin:$PATH or PATH=$PATH:/usr/local/sbin — the former making /usr/local/sbin the first directory to be searched for executable files; the latter making it the last.
```

`PS1`
- This variable stores the value of the Bash prompt. In the following chunk of code (also from `/etc/profile`), the `if` statement tests for the identity of the user and gives them a very discrete prompt accordingly ( `#` for `root` or `$` for regular users):

```
if [ "`id -u`" -eq 0 ]; then
  PS1='# '
else
  PS1='$ '
fi
```

```
Note
The id of root is 0. Become root and test it yourself with id -u.
```

Other Prompt Variables include:

- `PS2`
  - Normally set to `>` and used as a continuation prompt for long multiline commands.

- `PS3`
  - Used as the prompt for the `select` command.

- `PS4`
  - Normally set to `+` and used for debugging.

`SHELL`
- This variable stores the absolute path of the current shell:
```
$ echo $SHELL
/bin/bash
```

`USER`
- This stores the name of the current user:
```
$ echo $USER
carol
```
___

After going through shells, startup scripts and variables in the previous lessons, we will round the whole topic of customizing the shell off by having a look at two very interesting shell elements: aliases and functions. In fact the entire group of variables, aliases and functions — and their influence on one another — is what makes up the shell environment.

The main strength of these two flexible and time-saving shell facilities has to do with the concept of encapsulation: they offer the possibility of putting together — under a single command — a series of repetitive or recurrent commands.

### Creating Aliases
An alias is a substitute name for another command(s). It can run like a regular command, but instead executes another command according to the alias definition.

The syntax for declaring aliases is quite straightforward. Aliases are declared by writing the keyword `alias` followed by the alias assignment. In turn, the alias assignment consists of the alias name, an equal sign and one or more commands:

```
alias alias_name=command(s)
```
For example:
```
$ alias oldshell=sh
```

This awkward alias will start an instance of the original `sh` shell when the user types `oldshell` into the terminal:
```
$ oldshell
$
```
The power of aliases lies in that they allow us to write short versions of long commands:
```
$ alias ls='ls --color=auto'
```
```
Note
For information about ls and its colors, type man dir_colors into the terminal.
```

Likewise, we can create aliases for a series of concatenated commands — the semicolon (`;`) is used as a delimiter. We can, for instance, have an alias that gives us information about the location of the `git` executable and its version:
```
$ alias git_info='which git;git --version'
```
To invoke an alias, we type its name into the terminal:
```
$ git_info
/usr/bin/git
git version 2.7.4
```
The `alias` command will produce a listing of all available aliases in the system:
```
$ alias
alias git-info='which git;git --version'
alias ls='ls --color=auto'
alias oldshell='sh'
```

The `unalias` command removes aliases. We can, for instance, `unalias git-info` and see how it disappears from the listing:
```
$ unalias git-info
$ alias
alias ls='ls --color=auto'
alias oldshell='sh'
```
As we saw with the `alias hi='echo We salute you`.' in a previous lesson, we must enclose commands in quotes (either single or double) when — as a result of having arguments or parameters — they contain spaces:
```
$ alias greet='echo Hello world!'
$ greet
Hello world!
```
Commands with spaces include also those with options:
```
$ alias ll='ls -al'
```
Now `ll` will list all files — including the hidden ones (`a`) — in the long format (`l`).

We can reference variables in aliases:
```
$ reptile=uromastyx
$ alias greet='echo Hello $reptile!'
$ greet
Hello uromastyx!
```
The variable can also be assigned within the alias:
```
$ alias greet='reptile=tortoise; echo Hello $reptile!'
$ greet
Hello tortoise!
```
We can escape an alias with `\`:

```
$ alias where?='echo $PWD'
$ where?
/home/user2
$ \where?
-bash: where?: command not found
```

Escaping an alias is useful when an alias has the same name as a regular command. In this case, the alias takes precedence over the original command, which, however, is still be accessible by escaping the alias.

Likewise, we can put an alias inside another alias:

```
$ where?
/home/user2
$ alias my_home=where?
$ my_home
/home/user2
```

On top of that, we can also put a function inside an alias as you will be shown below.

### Expansion and Evaluation of Quotes in Aliases
When using quotes with environment variables, single quotes make the expansion dynamic:
```
$ alias where?='echo $PWD'
$ where?
/home/user2
$ cd Music
$ where?
/home/user2/Music
```
However, with double quotes the expansion is done statically:
```
$ alias where?="echo $PWD"
$ where?
/home/user2
$ cd Music
$ where?
/home/user2
```

### Persistence of Aliases: Startup Scripts
Just as with variables, for our aliases to gain persistence, we must put them into initialization scripts that are sourced at startup. As we already know, a good file for users to put their personal aliases in is `~/.bashrc`. You will probably find some aliases there already (most of them commented out and ready to be used by removing the leading `#`):
```
$ grep alias .bashrc
# enable color support of ls and also add handy aliases
    alias ls='ls --color=auto'
    #alias dir='dir --color=
    #alias vdir='vdir --color=
    #alias grep='grep --color=
    #alias fgrep='fgrep --color'
    #alias egrep='egrep --color=
# some more ls aliases
#ll='ls -al'
#alias la='ls -A'
#alias l='ls -CF'
# ~/.bash_aliases, instead of adding them here directly.
if [ -f ~/.bash_aliases ]; then
   . ~/.bash_aliases
```
As you can read in the last three lines, we are offered the possibility of having our own alias-dedicated file — `~/.bash_aliases` — and have it sourced by `.bashrc` with every system start. So we can go for that option and create and populate such file:
```
###########
# .bash_aliases:
# a file to be populated by the user's personal aliases (and sourced by ~/.bashrc).
###########
alias git_info='which git;git --version'
alias greet='echo Hello world!'
alias ll='ls -al'
alias where?='echo $PWD'
```

### Creating Functions
Compared to aliases, functions are more programmatic and flexible, specially when it comes to exploiting the full potential of Bash special built-in variables and positional parameters. They are also great to work with flow control structures such as loops or conditionals. We can think of a function as a command which includes logic through blocks or collections of other commands.

### Two Syntaxes for Creating Functions
There are two valid syntaxes to define functions.

Using the keyword `function`
- On the one hand, we can use the keyword `function`, followed by the name of the function and the commands between curly brackets:
```
function function_name {
command #1
command #2
command #3
.
.
.
command #n
}
```
Using `()`
On the other, we can leave out the keyword `function` and use two brackets right after the name of the function instead:
```
function_name() {
command #1
command #2
command #3
.
.
.
command #n
}
```
It is commonplace to put functions in files or scripts. However, they can also be written directly into the shell prompt with each command on a different line — note `PS2`(`>`) indicating a new line after a line break:
```
$ greet() {
> greeting="Hello world!"
> echo $greeting
> }
```
Whatever the case — and irrespective of the syntax we choose — , if we decide to skip line breaks and write a function in just one line, commands must be separated by semicolons (note the semicolon after the last command too):
```
$ greet() { greeting="Hello world!"; echo $greeting; }
```
`bash` did not complain when we pressed Enter, so our function is ready to be invoked. To invoke a function, we must type its name into the terminal:
```
$ greet
Hello world!
```
Just as with variables and aliases, if we want functions to be persistent across system reboots we have to put them into shell initialization scripts such as `/etc/bash.bashrc` (global) or `~/.bashrc` (local).
```
Warning
After adding aliases or functions to any startup script file, you should source such files with either . or source for the changes to take effect if you do not want to logout and back in again or reboot the system.
```
### Bash Special Built-in Variables
The Bourne Again Shell comes with a set of special variables which are particularly useful for functions and scripts. These are special because they can only be referenced — not assigned. Here is a list of the most relevant ones:

`$?`
- This variable’s reference expands to the result of the last command run. A value of `0` means success:
```
$ ps aux | grep bash
user2      420  0.0  0.4  21156  5012 pts/0    Ss   17:10   0:00 -bash
user2      640  0.0  0.0  12784   936 pts/0    S+   18:04   0:00 grep bash
$ echo $?
0
```
A value other than `0` means error:
```
user1@debian:~$ ps aux |rep bash
-bash: rep: command not found
user1@debian:~$ echo $?
127
```
`$$`
- It expands to the shell PID (process ID):
```
$ ps aux | grep bash
user2      420  0.0  0.4  21156  5012 pts/0    Ss   17:10   0:00 -bash
user2      640  0.0  0.0  12784   936 pts/0    S+   18:04   0:00 grep bash
$ echo $$
420
```
`$!`
- It expands to the PID of the last background job:
```
$ ps aux | grep bash &
[1] 663
$ user2      420  0.0  0.4  21156  5012 pts/0    Ss+  17:10   0:00 -bash
user2      663  0.0  0.0  12784   972 pts/0    S    18:08   0:00 grep bash
^C
[1]+  Done                   ps aux | grep bash
$ echo $!
663
```
```
Note
Remember, the ampersand (&) is used to start processes in the background.
```
Positional parameters `$0` through `$9`
- They expand to the parameters or arguments being passed to the function (alias or script) — $0 expanding to the name of the script or shell.

Le- t us create a function to demonstrate positional parameters — note `PS2` (`>`) indicating new lines after line breaks:
```
$ special_vars() {
> echo $0
> echo $1
> echo $2
> echo $3
}
```
Now, we will invoke the function (`special_vars`) passing three parameters to it (`debian`, `ubuntu`, `zorin`):
```
$ special_vars debian ubuntu zorin
-bash
debian
ubuntu
zorin
```
It worked as expected.
```
Warning
Although passing positional parameters to aliases is technically possible, it is not at all functional since — with aliases — positional parameters are always passed at the end:
```
```
$ alias great_editor='echo $1 is a great text editor'
$ great_editor emacs
is a great text editor emacs
```

Other Bash special built-in variables include:

`$#`
- It expands to the number of arguments passed to the command.

`$@`, `$*`
- They expand to the arguments passed to the command.

`$`_
- It expands to the last parameter or the name of the script (amongst other things; see man bash to find out more!):

### Variables in Functions
Of course, variables can be used within functions.

To prove it, this time we will create a new empty file called `funed` and put the following function into it:
```
editors() {

editor=emacs

echo "My editor is: $editor. $editor is a fun text editor."
}
```
As you may have guessed by now, we must source the file first to be able to invoke the function:
```
$ . funed
```
And now we can test it:
```
$ editors
My editor is emacs. emacs is a fun text editor.
```
As you can appreciate, for the `editors` function to work properly, the `editor` variable must first be set. The scope of that variable is local to the current shell and we can reference it as long as the session lasts:
```
$ echo $editor
emacs
```
Together with local variables we can also include environment variables in our function:
```
editors() {

editor=emacs

echo "The text editor of $USER is: $editor."
}

editors
```
Note how this time we decided to call the function from within the file itself (editors in the last line). That way, when we source the file, the function will also be invoked — all at once:
```
$ . funed
The text editor of user2 is: emacs.
```
### Positional Parameters in Functions
Something similar occurs with positional parameters.

We can pass them to functions from within the file or script (note the last line: `editors tortoise`):
```
editors() {

editor=emacs

echo "The text editor of $USER is: $editor."
echo "Bash is not a $1 shell."
}

editors tortoise
```
We source the file and prove it works:
```
$ . funed
The text editor of user2 is: emacs.
Bash is not a tortoise shell.
```
And we can also pass positional parameters to functions at the command line. To prove it, we get rid of the last line of the file:
```
editors() {

editor=emacs

echo "The text editor of $USER is: $editor."
echo "Bash is not a $1 shell."
}
```
Then, we have to source the file:
```
$ . funed
```
Finally, we invoke the function with tortoise as the positional parameter $1 at the command line:
```
$ editors tortoise
The text editor of user2 is: emacs.
Bash is not a tortoise shell.
```
### Functions in Scripts
Functions are mostly found in Bash scripts.

Turning our `funed` file into a script (we will name it `funed.sh`) is really a piece of cake:
```
#!/bin/bash

editors() {

editor=emacs

echo "The text editor of $USER is: $editor."
echo "Bash is not a $1 shell."
}

editors tortoise
```
That is it! We only added two lines:
- The first line is the shebang and defines what program is going to interpret the script: `#!/bin/bash`. Curiously enough, that program is `bash` itself.
- The last line is simply the invocation of the function.

Now there is only one thing left — we have to make the script executable:
```
$ chmod +x funed.sh
```
And now it is ready to be executed:
```
$ ./funed.sh
The text editor of user2 is: emacs.
Bash is not a tortoise shell.
```
```
Note
You will learn all about shell scripting in the next few lessons.
```
### A Function within an Alias
As said above, we can put a function inside an alias:
```
$ alias great_editor='gr8_ed() { echo $1 is a great text editor; unset -f gr8_ed; }; gr8_ed'
```
This long alias value deserves an explanation. Let us break it down:
- First there is the function itself: `gr8_ed() { echo $1 is a great text editor; unset -f gr8_ed; }`
- The last command in the function — `unset -f gr8_ed` — unsets the function so that it does not remain in the present `bash` session after the alias is called.
- Last but not least, to have a successful alias invocation, we must first invoke the function too: `gr8_ed`.

Let us invoke the alias and prove it works:
```
$ great_editor emacs
emacs is a great text editor
```
As shown in `unset -f gr8_ed` above, the `unset` command is not only used to unset variables, but also functions. In fact, there are specific switches or options:

`unset -v`
- for variables

`unset -f`
- for functions

If used without switches, `unset` will try to unset a variable first and — if it fails — then it will try to unset a function.

### A Function within a Function
Now say we want to communicate two things to `user2` every time she logs into the system:
- Say hello and recommend/praise a text editor.
- Since she is starting to put a lot of `Matroska` video files in its `$HOME/Video` folder, we also want to give her a warning.

To accomplish that purpose, we have put the following two functions into `/home/user2/.bashrc`:

The first function (`check_vids`) does the checking on `.mkv` files and the warning:
```
check_vids() {
    ls -1 ~/Video/*.mkv > /dev/null 2>&1
    if [ "$?" = "0" ];then
        echo -e "Remember, you must not keep more than 5 video files in your Video folder.\nThanks."
    else
	echo -e "You do not have any videos in the Video folder. You can keep up to 5.\nThanks."
    fi
}
```
`check_vids` does three things:

- It lists the `mkv` files in `~/Video` sending the output — and any errors — to the so-called bit-bucket (`/dev/null`).
- It tests the output of the previous command for success.
- Depending on the result of the test, it echoes one of two messages.

The second function is a modified version of our `editors` function:
```
editors() {

editor=emacs

echo "Hi, $USER!"
echo "$editor is more than a text editor!"

check_vids
}

editors
```
It is important to observe two things:
- The last command of `editors` invokes `check_vids` so both functions get chained: The greeting, praise and the checking and warning are executed in sequence.
- `editors` itself is the entry point to the sequence of functions so it is invoked in the last line (`editors`).

Now, let us log in as `user2` and prove it works:
```
# su - user2
Hi, user2!
emacs is more than a text editor!
Remember, you must not keep more than 5 video files in your Video folder.
Thanks.
```
___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)