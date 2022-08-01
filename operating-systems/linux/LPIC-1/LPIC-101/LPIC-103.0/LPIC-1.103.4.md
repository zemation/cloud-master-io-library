# LPIC-1: GNU and Unix Commands

## Lesson 103.4: Use streams, pipes, redirectes
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
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)