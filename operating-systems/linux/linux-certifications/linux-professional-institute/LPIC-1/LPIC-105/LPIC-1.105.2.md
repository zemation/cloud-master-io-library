# LPIC-1.105: Shells and shell Scripting

## Lesson 105.2: Customize or write simple scripts
The Linux shell environment allows the use of files — called scripts — containing commands from any available program in the system combined with shell builtin commands to automate a user’s and/or a system’s custom tasks. Indeed, many of the operating system maintenance tasks are performed by scripts consisting of sequences of commands, decision structures and conditional loops. Although scripts are most of the time intended for tasks related to the operating system itself, they are also useful for user oriented tasks, like mass renaming of files, data collecting and parsing, or any otherwise repetitive command line activities.

Scripts are nothing more than text files that behave like programs. An actual program — the interpreter — reads and executes the instructions listed in the script file. The interpreter can also start an interactive session where commands — including scripts — are read and executed as they are entered, as is the case with Linux shell sessions. Script files can group those instructions and commands when they grow too complex to be implemented as an alias or a custom shell function. Furthermore, script files can be maintained like conventional programs and, being just text files, they can be created and modified with any simple text editor.

### Script Structure and Execution
Basically, a script file is an ordered sequence of commands that must be executed by a corresponding command interpreter. How an interpreter reads a script file varies and there are distinct ways of doing so in a Bash shell session, but the default interpreter for a script file will be the one indicated in the first line of the script, just after the characters`#!` (known as shebang). In a script with instructions for the Bash shell, the first line should be `#!/bin/bash`. By indicating this line, the interpreter for all the instructions in the file will be `/bin/bash`. Except for the first line, all other lines starting with the hash character # will be ignored, so they can be used to place reminders and comments. Blank lines are also ignored. A very concise shell script file can therefore be written as follows:
```
#!/bin/bash

# A very simple script

echo "Cheers from the script file! Current time is: "

date +%H:%M
```

This script has only two instructions for the `/bin/bash` interpreter: the builtin command `echo` and the command date. The most basic way to run a script file is to execute the interpreter with the script path as the argument. So, assuming the previous example was saved in a script file named `script.sh` in the current directory, it will be read and interpreted by Bash with the following command:

```
$ bash script.sh
Cheers from the script file! Current time is:
10:57
```
Command `echo` will automatically add a new line after displaying the content, but the option `-n` will suppress this behaviour. Thus, using `echo -n` in the script will make the output of both commands to appear in the same line:
```
$ bash script.sh
Cheers from the script file! Current time is: 10:57
```
Although not required, the suffix `.sh` helps to identify shell scripts when listing and searching for files.

```
Tip
Bash will call whatever command is indicated after the #! as the interpreter for the script file. It may be useful, for example, to employ the shebang for other scripting languages, like Python (#!/usr/bin/python), Perl (#!/usr/bin/perl) or awk (#!/usr/bin/awk).
```

If the script file is intended to be executed by other users in the system, it is important to check if the proper reading permissions are set. The command `chmod o+r script.sh` will give reading permission to all users in the system, allowing them to execute `script.sh` by placing the path to the script file as the argument of command bash. Alternatively, the script file may have the execution bit permission set so the file can be executed as a conventional command. The execution bit is activated on the script file with the command `chmod`:
```
$ chmod +x script.sh
```
With the execution bit enabled, the script file named `script.sh` in the current directory can be executed directly with the command `./script.sh`. Scripts placed in a directory listed in the `PATH` environment variable will also be accessible without their complete path.
```
Warning
A script performing restricted actions may have its SUID permission activated, so ordinary users can also run the script with root privileges. In this case, it is very important to ensure that no user other than root has the permission to write in the file. Otherwise, an ordinary user could modify the file to perform arbitrary and potentially harmful operations.
```
The placement and indentation of commands in script files are not too rigid. Every line in a shell script will be executed as an ordinary shell command, in the same sequence as the line appears in the script file, and the same rules that apply to the shell prompt also apply to each script line individually. It is possible to place two or more commands in the same line, separated by semicolons:
```
echo "Cheers from the script file! Current time is:" ; date +%H:%M
```
Although this format might be convenient at times, its usage is optional, as sequential commands can be placed one command per line and they will be executed just as they were separated by semicolons. In other words, the semicolon can be replaced by a new line character in Bash script files.

When a script is executed, the commands contained therein are not executed directly in the current session, but instead they are executed by a new Bash process, called a sub-shell. It prevents the script from overwriting the current session’s environment variables and from leaving unattended modifications in the current session. If the goal is to run the script’s contents in the current shell session, then it should be executed with `source script.sh` or `. script.sh` (note that there is a space between the dot and the script name).

As it happens with the execution of any other command, the shell prompt will only be available again when the script ends its execution and its exit status code will be available in the `$?` variable. To change this behaviour, so the current shell also ends when the script ends, the script — or any other command — can be preceded by the `exec` command. This command will also replace the exit status code of the current shell session with its own.

### Variables
Variables in shell scripts behave in the same way as in interactive sessions, given that the interpreter is the same. For example, the format `SOLUTION=42` (without spaces around the equal sign) will assign the value `42` to the variable named `SOLUTION`. By convention, uppercase letters are used for variable names, but it’s not mandatory. Variable names can not, however, start with non alphabetical characters.

In addition to the ordinary variables created by the user, Bash scripts also have a set of special variables called parameters. Unlike ordinary variables, parameter names start with a non-alphabetical character that designates its function. Arguments passed to a script and other useful information are stored in parameters like `$0`, `$*`, `$?`, etc, where the character following the dollar sign indicates the information to be fetched:

`$*`
- All the arguments passed to the script.

`$@`
- All the arguments passed to the script. If used with double quotes, as in `"$@"`, every argument will be enclosed by double quotes.

`$#`
- The number of arguments.

`$0`
- The name of the script file.

`$!`
- PID of the last executed program.

`$$`
- PID of the current shell.

`$?`
- Numerical exit status code of the last finished command. For POSIX standard processes, a numerical value of `0` means that the last command was successfully executed, which also applies to shell scripts.

A positional parameter is a parameter denoted by one or more digits, other than the single digit `0`. For example, the variable `$1` corresponds to the first argument given to the script (positional parameter one), `$2` corresponds to the second argument, and so on. If the position of a parameter is greater than nine, it must be referenced with curly braces, as in `${10}`, `${11}`, etc.

Ordinary variables, on the other hand, are intended to store manually inserted values or the output generated by other commands. Command read, for example, can be used inside the script to ask the user for input during script execution:
```
echo "Do you want to continue (y/n)?"
read ANSWER
```
The returned value will be stored in the `ANSWER` variable. If the name of the variable is not supplied, the variable name REPLY will be used by default. It is also possible to use command `read` to read more than one variable simultaneously:
```
echo "Type your first name and last name:"
read NAME SURNAME
```
In this case, each space separated term will be assigned to the variables `NAME` and `SURNAME` respectively. If the number of given terms is greater than the number of variables, the exceeding terms will be stored in the last variable. `read` itself can display the message to the user with option `-p`, making the `echo` command redundant in this case:
```
read -p "Type your first name and last name:" NAME SURNAME
```
Scripts performing system tasks will often require information provided by other programs. The backtick notation can be used to store the output of a command in a variable:
```
$ OS=`uname -o`
```
In the example, the output of command `uname -o` will be stored in the `OS` variable. An identical result will be produced with `$()`:
```
$ OS=$(uname -o)
```
The length of a variable, that is, the quantity of characters it contains, is returned by prepending a hash `#` before the name of the variable. This feature, however, requires the use of the curly braces syntax to indicate the variable:
```
$ OS=$(uname -o)
$ echo $OS
GNU/Linux
$ echo ${#OS}
9
```
Bash also features one-dimensional array variables, so a set of related elements can be stored with a single variable name. Every element in an array has a numerical index, which must be used to write and read values in the corresponding element. Different from ordinary variables, arrays must be declared with the Bash builtin command `declare`. For example, to declare a variable named `SIZES` as an array:
```
$ declare -a SIZES
```
Arrays can also be implicitly declared when populated from a predefined list of items, using the parenthesis notation:
```
$ SIZES=( 1048576 1073741824 )
```
In the example, the two large integer values were stored in the `SIZES` array. Array elements must be referenced using curly braces and square brackets, otherwise Bash will not change or display the element correctly. As array indexes start at 0, the content of the first element is in `${SIZES[0]}`, the second element is in `${SIZES[1]}`, and so on:
```
$ echo ${SIZES[0]}
1048576
$ echo ${SIZES[1]}
1073741824
```
Unlike reading, changing an array element’s content is performed without the curly braces (e.g., `SIZES[0]=1048576`). As with ordinary variables, the length of an element in an array is returned with the hash character (e.g., `${#SIZES[0]}` for the length of the first element in `SIZES` array). The total number of elements in an array is returned if @ or * are used as the index:
```
$ echo ${#SIZES[@]}
2
$ echo ${#SIZES[*]}
2
```
Arrays can also be declared using the output of a command as the initial elements through command substitution. The following example shows how to create a Bash array whose elements are the current system’s supported filesystems:
```
$ FS=( $(cut -f 2 < /proc/filesystems) )
```
The command `cut -f 2 < /proc/filesystems` will display all the filesystems currently supported by the running kernel (as listed in the second column of the file `/proc/filesystems`), so the array `FS` now contains one element for each supported filesystem. Any text content can be used to initialize an array as, by default, any terms delimited by space, tab or newline characters will become an array element.
```
Tip
Bash treats each character of an environment variable’s $IFS (Input Field Separator) as a delimiter. To change the field delimiter to newline characters only, for example, the IFS variable should be reset with command IFS=$'\n'.
```
### Arithmetic Expressions
Bash provides a practical method to perform integer arithmetic operations with the builtin command `expr`. Two numerical variables, `$VAL1` and `$VAL2` for example, can be added together with the following command:
```
$ SUM=`expr $VAL1 + $VAL2`
```
The resulting value of the example will be available in the `$SUM` variable. Command `expr` can be replaced by `$(())`, so the previous example can be rewritten as `SUM=$(( $VAL1 + $VAL2 ))`. Power expressions are also allowed with the double asterisks operator, so the previous array declaration `SIZES=( 1048576 1073741824)` could be rewritten as `SIZES=( $((1024**2)) $((1024**3)) )`.

Command substitution can also be used in arithmetic expressions. For example, the file `/proc/meminfo` has detailed information about the system memory, including the number of free bytes in RAM:
```
$ FREE=$(( 1000 * `sed -nre '2s/[^[:digit:]]//gp' < /proc/meminfo` ))
```
The example shows how the command `sed` can be used to parse the contents of `/proc/meminfo` inside the arithmetic expression. The second line of the `/proc/meminfo` file contains the amount of free memory in thousands of bytes, so the arithmetic expression multiplies it by 1000 to obtain the number of free bytes in RAM.

### Conditional Execution
Some scripts usually are not intended to execute all the commands in the script file, but just those commands that match a predefined criteria. For example, a maintenance script may send a warning message to the administrator’s email only if the execution of a command fails. Bash provides specific methods of assessing the success of command execution and general conditional structures, more similar to those found in popular programming languages.

By separating commands with `&&`, the command to the right will be executed only if the command to the left did not encounter an error, that is, if its exit status was equal to `0`:
```
COMMAND A && COMMAND B && COMMAND C
```
The opposite behaviour occurs if commands are separated with `||`. In this case, the following command will be executed only if the previous command did encounter an error, that is, if its returning status code differs from 0.

One of the most important features of all programming languages is the ability to execute commands depending on previously defined conditions. The most straightforward way to conditionally execute commands is to use the Bash builtin command `if`, which executes one or more commands only if the command given as argument returns a 0 (success) status code. Another command, `test`, can be used to assess many different special criteria, so it is mostly used in conjunction with `if`. In the following example, the message `Confirmed: /bin/bash is executable`. will be displayed if the file `/bin/bash` exists and it is executable:
```
if test -x /bin/bash ; then
  echo "Confirmed: /bin/bash is executable."
fi
```
Option `-x` makes command `test` return a status code 0 only if the given path is an executable file. The following example shows another way to achieve the exact same result, as the square brackets can be used as a replacement for `test`:
```
if [ -x /bin/bash ] ; then
  echo "Confirmed: /bin/bash is executable."
fi
```
The `else` instruction is optional to the `if` structure and can, if present, define a command or sequence of commands to execute if the conditional expression is not true:
```
if [ -x /bin/bash ] ; then
  echo "Confirmed: /bin/bash is executable."
else
  echo "No, /bin/bash is not executable."
fi
```
`if` structures must always end with `fi`, so the Bash interpreter knows were the conditional commands end.

### Script Output
Even when the purpose of a script only involves file-oriented operations, it is important to display progress related messages in the standard output, so the user is kept informed of any issues and can eventually use those messages to generate operation logs.

The Bash builtin command `echo` is commonly used to display simple strings of text, but it also provides some extended features. With option `-e`, command `echo` is able to display special characters using escaped sequences (a backslash sequence designating a special character). For example:
```
#!/bin/bash

# Get the operating system's generic name
OS=$(uname -o)

# Get the amount of free memory in bytes
FREE=$(( 1000 * `sed -nre '2s/[^[:digit:]]//gp' < /proc/meminfo` ))

echo -e "Operating system:\t$OS"
echo -e "Unallocated RAM:\t$(( $FREE / 1024**2 )) MB"
```
Whilst the use of quotes is optional when using `echo` with no options, it is necessary to add them when using option `-e`, otherwise the special characters may not render correctly. In the previous script, both `echo` commands use the tabulation character `\t` to align the text, resulting in the following output:

```
Operating system:       GNU/Linux
Unallocated RAM:        1491 MB
```
The newline character `\n` can be used to separate the output lines, so the exact same output is obtained by combining the two `echo` commands into only one:
```
echo -e "Operating system:\t$OS\nUnallocated RAM:\t$(( $FREE / 1024**2 )) MB"
```
Although fit to display most text messages, command `echo` may not be well suited to display more specific text patterns. Bash builtin command `printf` gives more control over how to display the variables. Command `printf` uses the first argument as the format of the output, where placeholders will be replaced by the following arguments in the order they appear in the command line. For example, the message of the previous example could be generated with the following `printf` command:
```
printf "Operating system:\t%s\nUnallocated RAM:\t%d MB\n" $OS $(( $FREE / 1024**2 ))
```
The placeholder %s is intended for text content (it will be replaced by the `$OS` variable) and the `%d` placeholder is intended to integer numbers (it will be replaced by the resulting number of free megabytes in RAM). `printf` does not append a newline character at the end of the text, so the newline character `\n` should be placed at the end of the pattern if needed. The entire pattern should be interpreted as a single argument, so it must be enclosed in quotes.
```
Tip
The format of placeholder substitution performed by printf can be customized using the same format used by the printf function from the C programming language. The full reference for the printf function can be found in its manual page, accessed with the command man 3 printf.
```
With `printf`, the variables are placed outside the text pattern, which makes it possible to store the text pattern in a separate variable:
```
MSG='Operating system:\t%s\nUnallocated RAM:\t%d MB\n'
printf "$MSG" $OS $(( $FREE / 1024**2 ))
```
This method is particularly useful to display distinct output formats, depending on the user’s requirements. It makes it easier, for example, to write a script that uses a distinct text pattern if the user requires a CSV (Comma Separated Values) list rather than a default output message.

___

Shell scripts are generally intended to automate operations related to files and directories, the same operations that could be performed manually at the command line. Yet, the coverage of shell scripts are not only restricted to a user’s documents, as the configuration and interaction with many aspects of a Linux operating system are also accomplished through script files.

The Bash shell offers many helpful builtin commands to write shell scripts, but the full power of these scripts relies on the combination of Bash builtin commands with the many command line utilities available on a Linux system.

### Extended Tests
Bash as a script language is mostly oriented to work with files, so the Bash builtin command `test` has many options to evaluate the properties of filesystem objects (essentially files and directories). Tests that are focused on files and directories are useful, for example, to verify if the files and directories required to perform a particular task are present and can be read. Then, associated with an if conditional construct, the proper set of actions are executed if the test is successful.

The `test` command can evaluate expressions using two different syntaxes: test expressions can be given as an argument to command `test` or they can be placed inside square brackets, where command `test` is given implicitly. Thus, the test to evaluate if `/etc` is a valid directory can be written as `test -d /etc` or as `[ -d /etc]`:
```
$ test -d /etc
$ echo $?
0
$ [ -d /etc ]
$ echo $?
0
```
As confirmed by the exit status codes in the special variable `$?` — a value of 0 means the test was successful — both forms evaluated `/etc` as a valid directory. Assuming the path to a file or directory was stored in the variable `$VAR`, the following expressions can be used as arguments to `test` or inside square brackets:

`-a "$VAR"`
- Evaluate if the path in `VAR` exists in the filesystem and it is a file.

`-b "$VAR"`
- Evaluate if the path in `VAR` is a special block file.

`-c "$VAR"`
- Evaluate if the path in `VAR` is a special character file.

`-d "$VAR"`
- Evaluate if the path in VAR is a directory.

`-e "$VAR"`
- Evaluate if the path in `VAR` exists in the filesystem.

`-f "$VAR"`
- Evaluate if the path in `VAR` exists and it is a regular file.

`-g "$VAR"`
- Evaluate if the path in `VAR` has the SGID permission.

`-h "$VAR"`
- Evaluate if the path in `VAR` is a symbolic link.

`-L "$VAR"`
- Evaluate if the path in `VAR` is a symbolic link (like `-h`).

`-k "$VAR"`
- Evaluate if the path in `VAR` has the sticky bit permission.

`-p "$VAR"`
- Evaluate if the path in `VAR` is a pipe file.

`-r "$VAR"`
- Evaluate if the path in `VAR` is readable by the current user.

`-s "$VAR"`
- Evaluate if the path in `VAR` exists and it is not empty.

`-S "$VAR"`
- Evaluate if the path in `VAR` is a socket file.

`-t "$VAR"`
- Evaluate if the path in `VAR` is open in a terminal.

`-u "$VAR"`
- Evaluate if the path in `VAR` has the SUID permission.

`-w "$VAR"`
- Evaluate if the path in `VAR` is writable by the current user.

`-x "$VAR"`
- Evaluate if the path in `VAR` is executable by the current user.

`-O "$VAR"`
- Evaluate if the path in `VAR` is owned by the current user.

`-G "$VAR"`
- Evaluate if the path in `VAR` belongs to the effective group of the current user.

`-N "$VAR"`
- Evaluate if the path in `VAR` has been modified since the last time it was accessed.

`"$VAR1" -nt "$VAR2"`
- Evaluate if the path in `VAR1` is newer than the path in `VAR2`, according to their modification dates.

`"$VAR1" -ot "$VAR2"`
- Evaluate if the path in `VAR1` is older than `VAR2`.

`"$VAR1" -ef "$VAR2"`
- This expression evaluates to True if the path in `VAR1` is a hardlink to `VAR2`.

It is recommended to use the double quotes around a tested variable because, if the variable happens to be empty, it could cause a syntax error for the `test` command. The test options require an operand argument and an unquoted empty variable would cause an error due to a missing required argument. There are also tests for arbitrary text variables, described as follows:

`-z "$TXT"`
- Evaluate if variable `TXT` is empty (zero size).

`-n "$TXT"` or test `"$TXT"`
- Evaluate if variable `TXT` is not empty.

`"$TXT1" = "$TXT2" or "$TXT1" == "$TXT2"`
- Evaluate if TXT1 and TXT2 are equal.

`"$TXT1" != "$TXT2"`
- Evaluate if TXT1 and TXT2 are not equal.

`"$TXT1" < "$TXT2"`
- Evaluate if `TXT1` comes before `TXT2`, in alphabetical order.

`"$TXT1" > "$TXT2"`
- Evaluate if `TXT1` comes after `TXT2`, in alphabetical order.

Distinct languages may have different rules for alphabetical ordering. To obtain consistent results, regardless of the localization settings of the system where the script is being executed, it is recommended to set the environment variable `LANG` to `C`, as in `LANG=C`, before doing operations involving alphabetical ordering. This definition will also keep the system messages in the original language, so it should be used only within script scope.

Numerical comparisons have their own set of test options:

`$NUM1 -lt $NUM2`
- Evaluate if `NUM1` is less than `NUM2`.

`$NUM1 -gt $NUM2`
- Evaluate if `NUM1` is greater than `NUM2`.

`$NUM1 -le $NUM2`
- Evaluate if `NUM1` is less or equal to `NUM2`.

`$NUM1 -ge $NUM2`
- Evaluate if `NUM1` is greater or equal to `NUM2`.

`$NUM1 -eq $NUM2`
- Evaluate if `NUM1` is equal to `NUM2`.

`$NUM1 -ne $NUM2`
- Evaluate if `NUM1` is not equal to `NUM2`.

All tests can receive the following modifiers:

`! EXPR`
- Evaluate if the expression `EXPR` is false.

`EXPR1 -a EXPR2`
- Evaluate if both `EXPR1` and `EXPR2` are true.

`EXPR1 -o EXPR2`
- Evaluate if at least one of the two expressions are true.

Another conditional construct, `case`, can be seen as a variation of the if construct. The instruction `case` will execute a list of given commands if a specified item — the content of a variable, for example — can be found in a list of items separated by pipes (the vertical bar `|`) and terminated by `)`. The following sample script shows how the `case` construct can be used to indicate the corresponding software packaging format for a given Linux distribution:
```
#!/bin/bash

DISTRO=$1

echo -n "Distribution $DISTRO uses "
case "$DISTRO" in
	debian | ubuntu | mint)
    echo -n "the DEB"
  ;;
	centos | fedora | opensuse )
    echo -n "the RPM"
  ;;
	*)
    echo -n "an unknown"
  ;;
esac
echo " package format."
```

Each list of patterns and associated commands must be terminated with `;;`, `;&`, or `;;&`. The last pattern, an asterisk, will match if no other previous pattern corresponded beforehand. The `esac` instruction (`case` backwards) terminates the case construct. Assuming the previous sample script was named `script.sh` and it is executed with `opensuse` as the first argument, the following output will be generated:
```
$ ./script.sh opensuse
Distribution opensuse uses the RPM package format.
```
```
Tip
Bash has an option called nocasematch that enables case-insensitive pattern matching for the case construct and other conditional commands. The shopt builtin command toggles the values of settings controlling optional shell behaviour: shopt -s will enable (set) the given option and shopt -u will disable (unset) the given option. Therefore, placing shopt -s nocasematch before the case construct will enable case-insensitive pattern matching. Options modified by shopt will only affect the current session, so modified options inside scripts running in a sub-shell — which is the standard way to run a script — do not affect the options of the parent session.
```

The searched item and the patterns undergo tilde expansion, parameter expansion, command substitution and arithmetic expansion. If the searched item is specified with quotes, they will be removed before matching is attempted.

### Loop Constructs
Scripts are often used as a tool to automate repetitive tasks, performing the same set of commands until a stop criteria is verified. Bash has three loop instructions — `for`, `until` and `while` — designed for slightly distinct loop constructions.

The `for` construct walks through a given list of items — usually a list of words or any other space-separated text segments — executing the same set of commands on each one of those items. Before each iteration, the `for` instruction assigns the current item to a variable, which can then be used by the enclosed commands. The process is repeated until there are no more items left. The syntax of the `for` construct is:
```
for VARNAME in LIST
do
	COMMANDS
done
```
`VARNAME` is an arbitrary shell variable name and `LIST` is any sequence of separated terms. The valid delimiting characters splitting items in the list are defined by the `IFS` environment variable, which are the characters space, tab and newline by default. The list of commands to be executed is delimited by the `do` and `done` instructions, so commands can occupy as much lines as needed.

In the following example, the `for` command will take each item from the provided list — a sequence of numbers — and assign it to the `NUM` variable, one item at a time:
```
#!/bin/bash

for NUM in 1 1 2 3 5 8 13
do
	echo -n "$NUM is "
	if [ $(( $NUM % 2 )) -ne 0 ]
	then
		echo "odd."
	else
		echo "even."
  fi
done
```
In the example, a nested `if` construct is used in conjunction with an arithmetic expression to evaluate if the number in the current `NUM` variable is even or odd. Assuming the previous sample script was named `script.sh` and it is in the current directory, the following output will be generated:
```
$ ./script.sh
1 is odd.
1 is odd.
2 is even.
3 is odd.
5 is odd.
8 is even.
13 is odd.
```
Bash also supports an alternative format to `for` constructs, with the double parenthesis notation. This notation resembles the `for` instruction syntax from the C programming language and it is particularly useful for working with arrays:
```
#!/bin/bash

SEQ=( 1 1 2 3 5 8 13 )

for (( IDX = 0; IDX < ${#SEQ[*]}; IDX++ ))
do
	echo -n "${SEQ[$IDX]} is "
	if [ $(( ${SEQ[$IDX]} % 2 )) -ne 0 ]
	then
		echo "odd."
	else
		echo "even."
  fi
done
```
This sample script will generate the exact same output as the previous example. However, instead of using the `NUM` variable to store one item at a time, the `IDX` variable is employed to track the current array index in ascending order, starting from 0 and continuously adding to it while it is under the number of items in the `SEQ` array. The actual item is retrieved from its array position with `${SEQ[$IDX]}`.

In the same fashion, the `until` construct executes a command sequence until a `test` command — like the test command itself — terminates with status 0 (success). For example, the same loop structure from the previous example can be implemented with `until` as follows:
```
#!/bin/bash

SEQ=( 1 1 2 3 5 8 13 )

IDX=0

until [ $IDX -eq ${#SEQ[*]} ]
do
	echo -n "${SEQ[$IDX]} is "
	if [ $(( ${SEQ[$IDX]} % 2 )) -ne 0 ]
	then
		echo "odd."
	else
		echo "even."
  fi
  IDX=$(( $IDX + 1 ))
done
```
`until` constructs may require more instructions than `for` constructs, but it can be more suitable to non-numerical stop criteria provided by `test` expressions or any other command. It is important to include actions that ensure a valid stop criteria, like the increment of a counter variable, otherwise the loop may run indefinitely.

The instruction `while` is similar to the instruction `until`, but `while` keeps repeating the set of commands if the test command terminates with status 0 (success). Therefore, the instruction `until [ $IDX -eq ${#SEQ[*]} ]` from the previous example is equivalent to `while [ $IDX -lt ${#SEQ[*]} ]`, as the loop should repeat while the array index is less than the total of items in the array.

### A More Elaborate Example
Imagine a user wants to periodically sync a collection of their files and directories with another storage device, mounted at an arbitrary mount point in the filesystem, and a full featured backup system is considered overkill. Since this is an activity intended to be performed periodically, it is a good application candidate for automating with a shell script.

The task is straight forward: sync every file and directory contained in a list, from an origin directory informed as the first script argument to a destination directory informed as the second script argument. To make it easier to add or remove items from the list, it will be kept in a separated file, `~/.sync.list`, one item per line:
```
$ cat ~/.sync.list
Documents
To do
Work
Family Album
.config
.ssh
.bash_profile
.vimrc
```
The file contains a mixture of files and directories, some with blank spaces in their names. This is a suitable scenario for the builtin Bash command `mapfile`, which will parse any given text content and create an array variable from it, placing each line as an individual array item. The script file will be named `sync.sh`, containing the following script:
```
#!/bin/bash

set -ef

# List of items to sync
FILE=~/.sync.list

# Origin directory
FROM=$1

# Destination directory
TO=$2

# Check if both directories are valid
if [ ! -d "$FROM" -o ! -d "$TO" ]
then
  echo Usage:
  echo "$0 <SOURCEDIR> <DESTDIR>"
  exit 1
fi

# Create array from file
mapfile -t LIST < $FILE

# Sync items
for (( IDX = 0; IDX < ${#LIST[*]}; IDX++ ))
do
	echo -e "$FROM/${LIST[$IDX]} \u2192 $TO/${LIST[$IDX]}";
	rsync -qa --delete "$FROM/${LIST[$IDX]}" "$TO";
done
```
The first action the script does is to redefine two shell parameters with command `set`: option `-e` will exit execution immediately if a command exits with a non-zero status and option `-f` will disable file name globbing. Both options can be shortened as `-ef`. This is not a mandatory step, but helps diminish the probability of unexpected behaviour.

The actual application oriented instructions of the script file can be divided in three parts:

1. Collect and check script parameters
   -  The `FILE` variable is the path to the file containing the list of items to be copied: `~/.sync.list`. Variables `FROM` and `TO` are the origin and destination paths, respectively. Since these last two parameters are user provided, they go through a simple validation test performed by the `if` construct: if any of the two is not a valid directory — assessed by the test `[ ! -d "$FROM" -o ! -d "$TO" ]` — the script will show a brief help message and then terminate with an exit status of 1.

2. Load list of files and directories
   - After all the parameters are defined, an array containing the list of items to be copied is created with the command `mapfile -t LIST < $FILE`. Option `-t` of `mapfile` will remove the trailing newline character from each line before including it in the array variable named `LIST`. The contents of the file indicated by the variable `FILE` — `~/.sync.list` — is read via input redirection.

3. Perform the copy and inform the user
   - A `for` loop using double parenthesis notation traverses the array of items, with the `IDX` variable keeping track of the index increment. The `echo` command will inform the user of each item being copied. The escaped unicode character — `\u2192` — for the right arrow character is present in the output message, so the `-e` option of command `echo` must be used. Command `rsync` will selectively copy only the modified file pieces from the origin, thus its use is recommended for such tasks. `rsync` options `-q` and `-a`, condensed in -`qa`, will inhibit `rsync` messages and activate the archive mode, where all file properties are preserved. Option `--delete` will make `rsync` delete an item in the destination that does not exist in the origin anymore, so it should be used with care.

Assuming all the items in the list exist in the home directory of user `carol`, `/home/carol`, and the destination directory `/media/carol/backup` points to a mounted external storage device, the command `sync.sh` `/home/carol` `/media/carol/backup` will generate the following output:
```
$ sync.sh /home/carol /media/carol/backup
/home/carol/Documents → /media/carol/backup/Documents
/home/carol/"To do" → /media/carol/backup/"To do"
/home/carol/Work → /media/carol/backup/Work
/home/carol/"Family Album" → /media/carol/backup/"Family Album"
/home/carol/.config → /media/carol/backup/.config
/home/carol/.ssh → /media/carol/backup/.ssh
/home/carol/.bash_profile → /media/carol/backup/.bash_profile
/home/carol/.vimrc → /media/carol/backup/.vimrc
```
The example also assumes the script is executed by root or by user `carol`, as most of the files would be unreadable by other users. If `script.sh` is not inside a directory listed in the `PATH` environment variable, then it should be specified with its full path.

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)