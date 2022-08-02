# LPIC-1.103: GNU and Unix Commands

## Lesson 103.7: Search text files using regular expressions

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
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)