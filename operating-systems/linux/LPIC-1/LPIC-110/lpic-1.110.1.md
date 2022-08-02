
# LPIC-1.110: Security

## Lesson 110.1 Perform security administration tasks

Security is a must in system administration. As a good Linux sysadmin you have to keep an eye on a number of things such as special permissions on files, user password aging, open ports and sockets, limiting the use of system resources, dealing with logged-in users, and privilege escalation through `su` and `sudo`. In this lesson we will be reviewing each one of these topics.

### Checking for Files with the SUID and SGID Set
Aside from the traditional permission set of read, write and execute, files in a Linux system may also have some special permissions set such as the SUID or the SGID bits.

The SUID bit will allow the file to be executed with the privileges of the file’s owner. It is numerically represented by `4000` and symbolically represented by either `s` or `S` on the owner’s execute permission bit. A classic example of an executable file with the SUID permission set is `passwd`:
```
carol@debian:~$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 63736 jul 27  2018 /usr/bin/passwd
```
The lowercase `s` in `rws` indicates the presence of the SUID on the file — together with the execute permission. An uppercase `S` instead (`rwS`) would mean the underlying execute permission is not set.
```
Note
You will learn about passwd in the next section. The utility is mostly used by root to set/change users' passwords (e.g.: passwd carol). However, regular users can use it to change their own passwords, too. Therefore it comes with the SUID set.
```
On the other hand, the SGID bit can be set both on files and directories. With files, its behaviour is equivalent to that of SUID but the privileges are those of the group owner. When set on a directory, however, it will allow all files created therein to inherit the ownership of the directory’s group. Like SUID, SGID is symbollically represented by either `s` or `S` on the group’s execute permission bit. Numerically, it is represented by `2000`. You can set the SGID on a directory by using `chmod`. You have to add 2 (SGID) to the traditional permissions (`755` in our case):
```
carol@debian:~$ ls -ld shared_directory
drwxr-xr-x 2 carol carol 4096 may 30 23:55 shared_directory
carol@debian:~$ sudo chmod 2755 shared_directory/
carol@debian:~$ ls -ld shared_directory
drwxr-sr-x 2 carol carol 4096 may 30 23:55 shared_directory
```
To find files with either or both the SUID and SGID set you can use the `find` command and make use of the `-perm` option. You can use both numeric and symbolic values. The values — in turn — can be passed on their own or preceded by a dash (`-`) or a forward slash (`/`). The meaning is as follows:

`-perm numeric-value` or `-perm symbolic-value`
- find files having the special permission exclusively

`-perm -numeric-value` or `-perm -symbolic-value`
- find files having the special permission and other permissions

`-perm /numeric-value` or `-perm /symbolic-value`
- find files having either of the special permission (and other permissions)

For example, to find files with only SUID set in the present working directory, you will use the following command:
```
carol@debian:~$ find . -perm 4000
carol@debian:~$ touch file
carol@debian:~$ chmod 4000 file
carol@debian:~$ find . -perm 4000
./file
```
Note how — as there were not any files having exclusively the SUID — we created one to show some output. You can run the same command in symbolic notation:
```
carol@debian:~$ find . -perm u+s
./file
```
To find files matching SUID (irrespective of any other permssions) in the `/usr/bin/` directory, you can use either of the following commands:
```
carol@debian:~$ sudo find /usr/bin -perm -4000
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/mount
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/su
carol@debian:~$ sudo find /usr/bin -perm -u+s
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/mount
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/su
```
If you are looking for files in the same directory with the SGID bit set, you can execute `find /usr/bin/ -perm -2000` or `find /usr/bin/ -perm -g+s`.

Finally, to find files with either of the two special permissions set, add `4` and `2` and use `/`:
```
carol@debian:~$ sudo find /usr/bin -perm /6000
/usr/bin/dotlock.mailutils
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/wall
/usr/bin/ssh-agent
/usr/bin/chage
/usr/bin/dotlockfile
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/mount
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/expiry
/usr/bin/sudo
/usr/bin/bsd-write
/usr/bin/crontab
/usr/bin/su
```

### Password Management and Aging
As noted above you can use the pa`sswd utility to change your own password as a regular user. Additionally, you can pass the `-S` or `--status` switch to get status information about your account:
```
carol@debian:~$ passwd -S
carol P 12/07/2019 0 99999 7 -1
```
Here is a breakdown of the seven fields you get in the output:

`carol`
- User’s login name.

`P`
- It indicates that the user has a valid password (`P`); other possible values are `L` for a locked password and `NP` for no password.

`12/07/2019`
- Date of the last password change.

`0`
- Minimum age in days (the minimum number of days between password changes). A value of `0` means the password may be changed at any time.

`99999`
- Maximum age in days (the maximum number of days the password is valid for). A value of `99999` will disable password expiration.

`7`
- Warning period in days (the number of days prior to password expiration that a user will be warned).

`-1`
- Password inactivity period in days (the number of inactive days after password expiration before the account is locked). A value of `-1` will remove an account’s inactivity.

Apart from reporting on account status, you will use the `passwd` command as root to carry out some basic account maintenance. You can lock and unlock accounts, force a user to change their password on the next login and delete a user’s password with the `-l`, `-u`, `-e` and `-d `options, respectively.

To test these options it is convenient to introduce the `su` command at this point. Through `su` you can change users during a login session. So, for example, let us use `passwd` as root to lock `carol`'s password. Then we will switch to `carol` and check our account status to verify that the password has — in fact — been locked (`L`) and cannot be changed. Finally, going back to the root user, we will unlock `carol`'s password:
```
root@debian:~# passwd -l carol
passwd: password expiry information changed.
root@debian:~# su - carol
carol@debian:~$ passwd -S
carol L 05/31/2020 0 99999 7 -1
carol@debian:~$ passwd
Changing password for carol.
Current password:
passwd: Authentication token manipulation error
passwd: password unchanged
carol@debian:~$ exit
logout
root@debian:~# passwd -u carol
passwd: password expiry information changed.
```
Alternatively, you can also lock and unlock a user’s password with the `usermod` command:

Lock user `carol`'s password
- `usermod -L carol` or `usermod --lock carol`.

Unlock user `carol`'s password
- `usermod -U carol` or `usermod --unlock carol`.
```
Note
With the -f or --inactive switches, usermod can also be used to set the number of days before an account with an expired password is disabled (e.g.: usermod -f 3 carol).
```
Aside from `passwd` and `usermod`, the most direct command to deal with password and account aging is `chage` (“change age”). As root, you can pass `chage` the `-l` (or `--list`) switch followed by a username to have that user’s current password and account expiry information printed on the screen; as a regular user, you can view your own information:
```
carol@debian:~$ chage -l carol
Last password change					: Aug 06, 2019
Password expires					: never
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 99999
Number of days of warning before password expires	: 7
```
Run without options and only followed by a username, `chage` will behave interactively:
```
root@debian:~# chage carol
Changing the aging information for carol
Enter the new value, or press ENTER for the default

	Minimum Password Age [0]:
	Maximum Password Age [99999]:
	Last Password Change (YYYY-MM-DD) [2020-06-01]:
	Password Expiration Warning [7]:
	Password Inactive [-1]:
	Account Expiration Date (YYYY-MM-DD) [-1]:
```

The options to modify the different `chage` settings are as follows:

`-m days username` or `--mindays days username`
- Specify minimum number of days between password changes (e.g.: `chage -m 5 carol`). A value of `0` will enable the user to change his/her password at any time.

`-M days username` or `--maxdays days username`
- Specify maximum number of days the password will be valid for (e.g.: `chage -M 30 carol`). To disable password expiration, it is customary to give this option a value of `99999`.

`-d days username` or `--lastday days username`
- Specify number of days since the password was last changed (e.g.: `chage -d 10 carol`). A value of 0 will force the user to change their password on the next login.

`-W days username` or `--warndays days username`
- Specify number of days the user will be reminded of their password being expired.

`-I days username` or `--inactive days username`
- Specify number of inactive days after password expiration (e.g.: `chage -I 10 carol`) — the same as `usermod -f` or `usermod --inactive`. Once that number of days has gone by, the account will be locked. With a value of `0`, the account will not be locked, though.

`-E date username` or `--expiredate date username`
- Specify date (or number of days since the epoch — January, 1st 1970) on which the account will be locked. It is normally expressed in the format `YYYY-MM-DD`(e.g.: `chage -E 2050-12-13 carol`).
```
Note
You can learn more about passwd, usermod and chage — and their options — by consulting their respective manual pages.
```
### Discovering Open Ports
When it comes to keeping an eye on open ports, four powerful utilities are present on most Linux systems: `lsof`, `fuser`, `netstat` and `nmap`. We will cover them in this section.

`lsof` stands for “list open files”, which is no small thing considering that — for Linux — everything is a file. In fact, if you type `lsof` into the terminal, you will get a large listing of regular files, device files, sockets, etc. However, for the sake of this lesson, we will mainly focus on ports. To print the listing of all “Internet” network files, run `lsof` with the `-i` option:
```
root@debian:~# lsof -i
COMMAND  PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
dhclient 357     root    7u  IPv4  13493      0t0  UDP *:bootpc
sshd     389     root    3u  IPv4  13689      0t0  TCP *:ssh (LISTEN)
sshd     389     root    4u  IPv6  13700      0t0  TCP *:ssh (LISTEN)
apache2  399     root    3u  IPv6  13826      0t0  TCP *:http (LISTEN)
apache2  401 www-data    3u  IPv6  13826      0t0  TCP *:http (LISTEN)
apache2  402 www-data    3u  IPv6  13826      0t0  TCP *:http (LISTEN)
sshd     557     root    3u  IPv4  14701      0t0  TCP 192.168.1.7:ssh->192.168.1.4:60510 (ESTABLISHED)
sshd     569    carol    3u  IPv4  14701      0t0  TCP 192.168.1.7:ssh->192.168.1.4:60510 (ESTABLISHED)
```
Apart from the `bootpc` service — which is used by DHCP — the ouput shows two services listening for connections — `ssh` and the Apache web server (`http`) —  as well as two established SSH connections. You can specify a particular host with the `@ip-address` notation to check for its connections:
```
root@debian:~# lsof -i@192.168.1.7
COMMAND PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    557  root    3u  IPv4  14701      0t0  TCP 192.168.1.7:ssh->192.168.1.4:60510 (ESTABLISHED)
sshd    569 carol    3u  IPv4  14701      0t0  TCP 192.168.1.7:ssh->192.168.1.4:60510 (ESTABLISHED)
```
```
Note
To print only IPv4 and IPv6 network files, use the -i4 and -i6 options respectively.
```
Likewise, you can filter by port by passing the `-i` (or `-i@ip-address`) option the `:port` argument:
```
root@debian:~# lsof -i :22
COMMAND PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    389  root    3u  IPv4  13689      0t0  TCP *:ssh (LISTEN)
sshd    389  root    4u  IPv6  13700      0t0  TCP *:ssh (LISTEN)
sshd    557  root    3u  IPv4  14701      0t0  TCP 192.168.1.7:ssh->192.168.1.4:60510 (ESTABLISHED)
sshd    569 carol    3u  IPv4  14701      0t0  TCP 192.168.1.7:ssh->192.168.1.4:60510 (ESTABLISHED)
```
Multiple ports are separated by commas (and ranges are specified using a dash):
```
root@debian:~# lsof -i@192.168.1.7:22,80
COMMAND PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    705  root    3u  IPv4  13960      0t0  TCP 192.168.1.7:ssh->192.168.1.4:44766 (ESTABLISHED)
sshd    718 carol    3u  IPv4  13960      0t0  TCP 192.168.1.7:ssh->192.168.1.4:44766 (ESTABLISHED)
```
```Note
The amount of options available to lsof is quite impressive. To find out more, consult its manual page.
```
Next on the list of network commands is `fuser`. Its main purpose is to find a “file’s user” — which involves knowing what processes are accessing what files; it also gives you some other information such as the type of access. For example, to check on the current working directory, it is sufficient to run `fuser .` However, to get a little more information, it is convenient to use the verbose (`-v` or `--verbose`) option:
```
root@debian:~# fuser .
/root:                 580c
root@debian:~# fuser -v .
                     USER        PID ACCESS COMMAND
/root:               root        580 ..c.. bash
```
Let us break down the output:

File
- The file we are getting information about (`/root`).

`USER` Column
- The owner of the file (`root`).

`PID` Column
- The process identifier (580).

`ACCESS` Column
Type of access (`..c..`). One of:

`c`
- Current directory.

`e`
- Executable being run.

`f`
- Open file (omitted in default display mode).

`F`
- Open file for writing (omitted in default display mode).

`r`
- root directory.

`m`
- mmap’ed file or shared library.

`.`
- Placeholder (omitted in default display mode).

`COMMAND` Column
The command affiliated with the file (`bash`).

With the `-n` (or `--namespace`) option, you can find information about network ports/sockets. You must also supply the network protocol and port number. Thus, to get information about the Apache web server you will run the following command:
```
root@debian:~# fuser -vn tcp 80
                     USER        PID ACCESS COMMAND
80/tcp:              root        402 F.... apache2
                     www-data    404 F.... apache2
                     www-data    405 F.... apache2
```
```
Note
fuser can be also be used to kill the processes accessing the file with the -k or --kill switches (e.g.: fuser -k 80/tcp). Refer to the manual page for more detailed information.
```
Let us turn to `netstat` now. `netstat` is a very versatile network tool that is mostly used to print “network statistics”.

Run without options, `netstat` will display both active Internet connections and Unix sockets. Because of the size of the listing, you may want to pipe its output through `less`:
```
carol@debian:~$ netstat |less
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 192.168.1.7:ssh         192.168.1.4:55444       ESTABLISHED
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ]         DGRAM                    10509    /run/systemd/journal/syslog
unix  3      [ ]         DGRAM                    10123    /run/systemd/notify
(...)
```
To list only “listening” ports and sockets, you will use the `-l` or `--listening` options. The `-t`/`--tcp` and `-u`/`--udp` options can be added to filter by TCP and UDP protocol, respectively (they can also be combined in the same command). Likewise, `-e`/`--extend` will display additional information:
```
carol@debian:~$ netstat -lu
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
udp        0      0 0.0.0.0:bootpc          0.0.0.0:*
carol@debian:~$ netstat -lt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
tcp        0      0 localhost:smtp          0.0.0.0:*               LISTEN
tcp6       0      0 [::]:http               [::]:*                  LISTEN
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN
tcp6       0      0 localhost:smtp          [::]:*                  LISTEN
carol@debian:~$ netstat -lute
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN      root       13729
tcp        0      0 localhost:smtp          0.0.0.0:*               LISTEN      root       14372
tcp6       0      0 [::]:http               [::]:*                  LISTEN      root       14159
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN      root       13740
tcp6       0      0 localhost:smtp          [::]:*                  LISTEN      root       14374
udp        0      0 0.0.0.0:bootpc          0.0.0.0:*                           root       13604
```
If you omit `-l` option, only established connections will be shown:
```
carol@debian:~$ netstat -ute
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode
tcp        0      0 192.168.1.7:ssh         192.168.1.4:39144       ESTABLISHED root       15103
```
If you are only interested in numerical information concerning ports and hosts, you can use the `-n` or `--numeric` option to print only port numbers and IP addresses. Note how `ssh` turns into to `22` when adding `-n` to the command above:
```
carol@debian:~$ netstat -uten
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode
tcp        0      0 192.168.1.7:22          192.168.1.4:39144       ESTABLISHED 0          15103
```
As you can see, you can make very useful and productive `netstat` commands by combining some of its options. Browse through the man pages to learn more and find the combinations that best suit your needs.

Finally, we will introduce `nmap — or` the “network mapper”. Another very powerful utility, this port scanner is executed by specifying an IP address or hostname:
```
root@debian:~# nmap localhost
Starting Nmap 7.70 ( https://nmap.org ) at 2020-06-04 19:29 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0000040s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 1.58 seconds
```
Aside from a single host, `nmap` allows you to scan:

multiple hosts
- by separating them by spaces (e.g.: `nmap localhost 192.168.1.7`).

host ranges
- by using a dash (e.g.: `nmap 192.168.1.3-20`).

subnets
- by using a wildcard or CIDR notation (e.g.: `nmap 192.168.1.*` or `nmap 192.168.1.0/24`). You can exclude particular hosts (e.g.: `nmap 192.168.1.0/24 --exclude 192.168.1.7`).

To scan a particular port, use the `-p` switch followed by the port number or service name (`nmap -p 22` and `nmap -p ssh` will give you the same output):
```
root@debian:~# nmap -p 22 localhost
Starting Nmap 7.70 ( https://nmap.org ) at 2020-06-04 19:54 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000024s latency).
Other addresses for localhost (not scanned): ::1

PORT   STATE SERVICE
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 0.22 seconds
```
You can also scan multiple ports or port ranges by using commas and dashes, respectively:
```
root@debian:~# nmap -p ssh,80 localhost
Starting Nmap 7.70 ( https://nmap.org ) at 2020-06-04 19:58 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000051s latency).
Other addresses for localhost (not scanned): ::1

PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.22 seconds
```
```
root@debian:~# nmap -p 22-80 localhost
Starting Nmap 7.70 ( https://nmap.org ) at 2020-06-04 19:58 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000011s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 57 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 1.47 seconds
```
Two other important and handy `nmap` options are:

`-F`
- Run a fast scan on the 100 most common ports.

`-v`
- Get verbose output (`-vv` will print even more verbose output).
```
Note
nmap can run quite complex commands by making use of scan types. However, that topic is outside of the scope of this lesson.
```

### Limits on Users Logins, Processes and Memory Usage
Resources on a Linux system are not unlimited so — as a sysadmin — you should ensure a good balance between user limits on resources and the proper functioning of the operating system. ulimit can help you out in that regard.

`ulimit` deals with soft and hard limits — specified by the `-S` and `-H` options, respectively. Run without options or arguments, `ulimit` will display the soft file blocks of the current user:
```
carol@debian:~$ ulimit
unlimited
```
With the `-a` option, `ulimit` will show all current soft limits (the same as `-Sa`); to display all current hard limits, use `-Ha`:
```
carol@debian:~$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
(...)
carol@debian:~$ ulimit -Ha
core file size          (blocks, -c) unlimited
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
(...)
```
The available shell resources are specified by options such as:

`-b`
- maximum socket buffer size

`-f`
- maximum size of files written by the shell and its children

`-l`
- maximum size that may be locked into memory

`-m`
- maximum resident set size (RSS) — the current portion of memory held by a process in main memory (RAM)

`-v`
- maximum amout of virtual memory

`-u`
- maximum number of processes available to a single user

Thus, to display limits you will use `ulimit` followed by either `-S` (soft) or `-H`(hard) and the resource option; if neither `-S` or `-H` is supplied, soft limits will be shown:
```
carol@debian:~$ ulimit -u
10000
carol@debian:~$ ulimit -Su
10000
carol@debian:~$ ulimit -Hu
15672
```
Likewise, to set new limits on a particular resource you will specify either `-S` or `-H`, followed by the corresponding resource option and the new value. This value can be a number or the special words `soft` (current soft limit), `hard` (current hard limit) or `unlimited` (no limit). If neither `-S` or `-H` is specified, both limits will be set. For example, let us first read the current maximum size value for files written by the shell and its children:
```
root@debian:~# ulimit -Sf
unlimited
root@debian:~# ulimit -Hf
unlimited
```
Now, let us change the value from `unlimited` to `500` blocks without specifying either `-S` or `-H`. Note how both the soft and hard limits are changed:
```
root@debian:~# ulimit -f 500
root@debian:~# ulimit -Sf
500
root@debian:~# ulimit -Hf
500
```
Finally, we will decrease only the soft limit to `200` blocks:
```
root@debian:~# ulimit -Sf 200
root@debian:~# ulimit -Sf
200
root@debian:~# ulimit -Hf
500
```
Hard limits can only be increased by the root user. On the other hand, regular users can decrease hard limits and increase soft limits up to the value of hard limits. To make new limit values persistent across reboots, you must write them to the `/etc/security/limits.conf` file. This is also the file used by the administrator to apply restrictions on particular users.
```
Note
Be warned that there is no ulimit man page as such. It is a bash builtin so you have to refer to bash man page to learn about it.
```
### Dealing with Logged in Users
Another of your jobs as a sysadmin entails keeping track of logged-in users. There are three utilities that can help you out with those tasks: `last`, `who` and `w`.

`last` prints a listing of the last logged in users with the most recent information on top:
```
root@debian:~# last
carol    pts/0        192.168.1.4      Sat Jun  6 14:25   still logged in
reboot   system boot  4.19.0-9-amd64   Sat Jun  6 14:24   still running
mimi     pts/0        192.168.1.4      Sat Jun  6 12:07 - 14:24  (02:16)
reboot   system boot  4.19.0-9-amd64   Sat Jun  6 12:07 - 14:24  (02:17)
(...)
wtmp begins Sun May 31 14:14:58 2020
```
Considering the truncated listing, we get information about the last two users on the system. The first two lines tell us about user `carol`; the next two lines, about user `mimi`. The information is as follows:

1. User carol at terminal pts/0 from host 192.168.1.4 started her session on Sat Jun 6 at 14:25 and is still logged in. The system — using kernel 4.19.0-9-amd64 — was started (reboot system boot) on Sat Jun 6 at 14:24 and is still running.

2. User `mimi` at terminal `pts/0` from host `192.168.1.4` started her session on `Sat Jun 6` at `12:07` and logged out at `14:24` (the session lasted a total of (0`2:16`) hours). The system — using `kernel 4.19.0-9-amd64` — was started (`reboot system boot`) on `Sat Jun 6` at `12:07` and was powered off at `14:24` (it was up and running for (`02:17`) hours).
```
Note
The line wtmp begins Sun May 31 14:14:58 2020 refers to /var/log/wtmp, which is the special log file from which last gets the information.
```
You can pass `last` a username to have only entries for that user displayed:
```
root@debian:~# last carol
carol    pts/0        192.168.1.4      Sat Jun  6 14:25   still logged in
carol    pts/0        192.168.1.4      Sat Jun  6 12:07 - 14:24  (02:16)
carol    pts/0        192.168.1.4      Fri Jun  5 00:48 - 01:28  (00:39)
(...)
```
Regarding the second column (terminal), `pts` stands for Pseudo Terminal Slave — as opposed to a proper TeleTYpewriter or `tty` terminal; `0` refers to the first one (the count starts at zero).
```
Note
To check for bad login attempts, run lastb instead of last.
```
The `who` and `w` utilities focus on currently logged in users and are quite similar. The former displays who is logged on, while the latter also shows information on what they are doing.

When executed with no options, `who` will display four columns corresponding to logged-in user, terminal, date and time, and hostname:
```
root@debian:~# who
carol    pts/0        2020-06-06 17:16 (192.168.1.4)
mimi     pts/1        2020-06-06 17:28 (192.168.1.4)
```
`who` accepts a series of options, among which we can highlight the following:

`-b`,`--boot`
- Display time of last system boot.

`-r`,`--runlevel`
- Display current runlevel.

`-H`,`--heading`
- Print column headings.

Compared to `who`, `w` gives a little more verbose output:
```
root@debian:~# w
 17:56:12 up 40 min,  2 users,  load average: 0.04, 0.12, 0.09
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
carol    pts/0    192.168.1.4    17:16    1.00s  0.15s  0.05s sshd: carol [priv]
mimi     pts/1    192.168.1.4    17:28   15:08   0.05s  0.05s -bash
```
The top line gives you information about the current time (`17:56:12`), how long the system has been up and running (`up 40 min`), the number of currently logged in users (`2 users`) and the load average numbers (`load average: 0.04, 0.12, 0.09`). These values refer to the number of jobs in the run queue averaged over the last 1, 5 and 15 minutes, respectively.

Then you find eight columns; let us break them down:

`USER`
- Login name of user.

`TTY`
- Name of terminal the user is on.

`FROM`
- Remote host from which the user has logged on.

`LOGIN@`
- Login time.

`IDLE`
- Idle time.

`JCPU`
- Time used by all processes attached to the tty (including currently running background jobs).

`PCPU`
- Time used by current process (the one showing under `WHAT`).

`WHAT`
- Command line of current process.

Just like with `who`, you can pass `w` usernames:
```
root@debian:~# w mimi
 18:23:15 up  1:07,  2 users,  load average: 0.00, 0.02, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
mimi     pts/1    192.168.1.4      17:28    9:23   0.06s  0.06s -bash
```
### Basic `sudo` Configuration and Usage
As already noted in this lesson, `su` lets you switch to any other user in the system as long as you provide the target user’s password. In the case of the root user, having its password distributed or known to (many) users puts the system at risk and is a very bad security practice. The basic usage of `su` is `su - target-username`. When changing to root — though — the target username is optional:
```
carol@debian:~$ su - root
Password:
root@debian:~# exit
logout
carol@debian:~$ su -
Password:
root@debian:~#
```
The use of the dash (`-`) ensures that the target user’s environment is loaded. Without it, the old user’s environment will be kept:
```
carol@debian:~$ su
Password:
root@debian:/home/carol#
```
On the other hand, there is the `sudo` command. With it you can execute a command as the root user — or any other user for that matter. From a security perspective, `sudo` is a far better option than `su` as it presents two main advantages:

1. to run a command as root, you do not need the root user’s password, but only that of the invoking user in compliance with a security policy. The default security policy is `sudoers` as specified in `/etc/sudoers` and `/etc/sudoers.d/*`.

2. `sudo` lets you run single commands with elevated privileges instead of launching a whole new subshell for root as `su` does.

The basic usage of `sudo` is `sudo -u target-username` command. However, to run a command as user root, the `-u target-username` option is not necessary:
```
carol@debian:~$ sudo -u mimi whoami
mimi
carol@debian:~$ sudo whoami
root
```
```
Note
sudoers will use a per-user (and per-terminal) timestamp for credential caching, so that you can use sudo without a password for a default period of fifteen minutes. This default value can be modified by adding the timestamp_timeout option as a Defaults setting in /etc/sudoers (e.g.: Defaults timestamp_timeout=1 will set credential caching timeout to one minute).
```

### The `/etc/sudoers` File
`sudo`'s main configuration file is `/etc/sudoers`(there is also the `/etc/sudoers.d` directory). That is the place where users' `sudo` privileges are determined. In other words, here you will specify who can run what commands as what users on what machines — as well as other settings. The syntax used is as follows:
```
carol@debian:~$ sudo less /etc/sudoers
(...)
# User privilege specification
root    ALL=(ALL:ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
(...)
```
The privilege specification for the root user is` ALL=(ALL:ALL) ALL`. This translates as: user root (`root`) can log in from all hosts (`ALL`), as all users and all groups (`(ALL:ALL)`), and run all commands (`ALL`). The same is true for members of the `sudo` group — note how group names are identified by a preceding percent sign `(%).`

Thus, to have user `carol` be able to check `apache2` status from any host as any user or group, you will add the following line in the `sudoers` file:
```
carol   ALL=(ALL:ALL) /usr/bin/systemctl status apache2
```
You may want to save `carol` the inconvenience of having to provide her password to run the `systemctl status apache2` command. For that, you will modify the line to look like this:
```
carol   ALL=(ALL:ALL) NOPASSWD: /usr/bin/systemctl status apache2
```
Say that now you want to restrict your hosts to 192.168.1.7 and enable `carol` to run `systemctl status apache2` as user `mimi`. You would modify the line as follows:
```
carol   192.168.1.7=(mimi) /usr/bin/systemctl status apache2
```
Now you can check the status of the Apache web server as user `mimi`:
```
carol@debian:~$ sudo -u mimi systemctl status apache2
● apache2.service - The Apache HTTP Server
   Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2020-06-09 13:12:19 CEST; 29min ago
(...)
```
If `carol` was to be promoted to sysadmin and you wanted to give her all privileges, the easiest approach would be that of including her in the special `sudo` group with `usermod` and the `-G` option (you may also want to use the -a option, which ensures that the user is not removed from any other groups they might belong to):
```
root@debian:~# sudo useradd -aG sudo carol
```
```
Note
In the Red Hat family of distributions the wheel group is the counterpart to the special administrative sudo group of Debian systems.
```
Instead of editing `/etc/sudoers` directly, you should simply use the `visudo` command as root (e.g.: `visudo`), which will open `/etc/sudoers` using your predefined text editor. To change the default text editor, you can add the `editor` option as a `Defaults` setting in `/etc/sudoers`. For instance, to change the editor to `nano`, you will add the following line:
```
Defaults	editor=/usr/bin/nano
```
```
Note
Alternatively, you can specify a text editor via the EDITOR environment variable when using visudo (e.g.: EDITOR=/usr/bin/nano visudo)
```
Aside from users and groups, you can also make use of aliases in `/etc/sudoers`. There are three main categories of aliases that you can define: host aliases (`Host_Alias`), user aliases (`User_Alias`) and command aliases (`Cmnd_Alias`). Here is an example:
```
# Host alias specification

Host_Alias SERVERS = 192.168.1.7, server1, server2

# User alias specification

User_Alias REGULAR_USERS = john, mary, alex

User_Alias PRIVILEGED_USERS = mimi, alex

User_Alias ADMINS = carol, %sudo, PRIVILEGED_USERS, !REGULAR_USERS

# Cmnd alias specification

Cmnd_Alias SERVICES = /usr/bin/systemctl *

# User privilege specification
root    ALL=(ALL:ALL) ALL
ADMINS  SERVERS=SERVICES

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
```
Considering this sample `sudoers` file, let us explain the three types of aliases in a bit more detail:

Host aliases
- They include a comma-separated list of hostnames, IP addresses, as well as networks and netgroups (preceded by `+`). Netmasks can be also specified. The `SERVERS` host alias includes an IP address and two hostnames:
```
Host_Alias SERVERS = 192.168.1.7, server1, server2
```
User aliases
- They include a comma-separated list of users specified as usernames, groups (preceded by `%`) and netgroups (preceded by `+`). You can exclude particular users with `!`. The `ADMINS` user alias — for example — includes user `carol`, the members of the `sudo` group and those members of the `PRIVILEGE_USERS` user alias that do not belong in the `REGULAR_USERS` user alias:
```
User_Alias ADMINS = carol, %sudo, PRIVILEGED_USERS, !REGULAR_USERS
```
Command aliases
- They include a comma-separated list of commands and directories. If a directory is specified, any file in that directory will be included — subdirectories will be ignored, though. The `SERVICES` command alias includes a single command with all its subcommands — as specified by the asterisk (`*`):
```
Cmnd_Alias SERVICES = /usr/bin/systemctl *
```
As a result of the alias specifications, the line `ADMINS SERVERS=SERVICES` under the `User privilege specification` section translates as: all users belonging in `ADMINS` can use sudo to run any command in `SERVICES` on any server in `SERVERS`.
```
Note
There is a fourth type of alias that you can include in /etc/sudoers: runas aliases (Runas_Alias). They are very similar to user aliases, but allow you to specify users by their user ID (UID). This feature might be convenient in some scenarios.
```



___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)