# LPIC-1.103: GNU and Unix Commands

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