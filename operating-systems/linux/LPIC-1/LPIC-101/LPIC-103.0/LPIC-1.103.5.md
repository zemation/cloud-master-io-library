# LPIC-1: GNU and Unix Commands

## Lesson 103.5 Create, monitor and kill processes
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
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)