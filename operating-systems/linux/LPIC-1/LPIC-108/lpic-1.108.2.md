# LPIC-1.108: Essential System Services

## Lesson 108.2: System logging

Logs can be a system administrator’s best friend. Logs are files (usually text files) where all system and network events are chronologically registered from the moment your system is booted up. Thus, the range of information that can be found in logs includes virtually every aspect of the system: failed authentication attempts, program and service errors, hosts blocked by the firewall, etc. As you can imagine, logs make system administrators' lives a lot easier when it comes to troubleshooting, resource-checking, detection of anomalous behaviour of programs, and so on.

In this lesson we will discuss one of the most common logging facilites currently found in GNU/Linux distributions: `rsyslog`. We will study the different types of logs that exist, where they are stored, what information they include and how that information can be obtained and filtered. We will also discuss how logs can be kept in centralized servers across IP networks, log rotation and the kernel ring buffer.

### System Logging
The moment the kernel and the different processes in your system start running and communicating with one another, a lot of information is generated in the form of messages that are — for the most part — sent to the logs.

Without logging, searching for an event that happened on a server would give system administrators a headache, hence the importance of having a standardized and centralized way of keeping track of any system events. Logs are determinant and telling when it comes to troubleshooting and security and are reliable data sources for understanding system statistics and making trend predictions.

Leaving aside `systemd-journald` (which we will discuss in the next lesson), logging has traditionally been handled by three main dedicated services: `syslog`, `syslog-ng` (syslog new generation) and `rsyslog` (“the rocket-fast system for log processing”). `rsyslog` brought along important improvements (such as RELP support) and has become the most popular choice nowadays. Each of these services collects messages from other services and programs and stores them in log files, typically under `/var/log`. However, some services take care of their own logs (take — for example — the Apache HTTPD web server or the CUPS printing system). Likewise, the Linux kernel uses an in-memory ring buffer for storing its log messages.
```
Note
RELP stands for Reliable Event Logging Protocol and extends the functionality of the syslog protocol to provide reliable delivery of messages.
```
Since `rsyslog` has become the de facto standard logging facility in all major distros, we will focus on it for the present lesson. `rsyslog` uses a client-server model. The client and the server can live on the same host or in different machines. Messages are sent and received in a particular format and can be kept in centralized rsyslog servers across IP networks. rsyslog’s daemon — `rsyslogd` — works together with `klogd` (which manages kernel messages). In the next sections `rsyslog` and its logging infrastructure will be discussed.
```
Note
A daemon is a service that runs in the background. Note the final d in daemon names: klogd or rsyslogd.
```
### Log Types
Because logs are variable data, they are normally found in `/var/log`. Roughly speaking, they can be classified into system logs and service or program logs.

Let us see some system logs and the information they keep:

`/var/log/auth.log`
- Activities related to authentication processes: logged users, `sudo` information, cron jobs, failed login attempts, etc.

`/var/log/syslog`
- A centralized file for practically all of the logs captured by `rsyslogd`. Because it includes so much information, logs are distributed across other files according to the configuration supplied in `/etc/rsyslog.conf`.

`/var/log/debug`
- Debug information from programs.

`/var/log/kern.log`
- Kernel messages.

`/var/log/messages`
- Informative messages which are not related to the kernel but to other services. It is also the default remote client log destination in a centralized log server implementation.

`/var/log/daemon.log`
- Information related to daemons or services running in the background.

`/var/log/mail.log`
- Information related to the email server, e.g. postfix.

`/var/log/Xorg.0.log`
- Information related to the graphics card.

`/var/run/utmp` and `/var/log/wtmp`
- Successful logins.

`/var/log/btmp`
- Failed login attempts, e.g. brute force attack via ssh.

`/var/log/faillog`
- Failed authentication attempts.

`/var/log/lastlog`
- Date and time of recent user logins.

Now let us see a few examples of service logs:

`/var/log/cups/`
Directory for logs of the Common Unix Printing System. It commonly includes the following default log files: `error_log`, `page_log` and `access_log`.

`/var/log/apache2/` or `/var/log/httpd`
Directory for logs of the Apache Web Server. It commonly includes the following default log files: `access.log`, `error_log`, and `other_vhosts_access.log`.

`/var/log/mysql`
Directory for logs of the MySQL Relational Database Management System. It commonly includes the following default log files: `error_log`, `mysql.log` and `mysql-slow.log`.

`/var/log/samba/`
Directory for logs of the Session Message Block (SMB) protocol. It commonly includes the following default log files: `log.`, `log.nmbd` and `log.smbd`.
```
Note
The exact name and contents of log files may vary across Linux distributions. There are also logs particular to specific distributions such as /var/log/dpkg.log (containing information related to dpkg packages) in Debian GNU/Linux and its derivatives.
```
### Reading Logs
To read log files, first ensure you are the root user or have reading permissions on the file. You can use a variety of utilities such as:

`less` or `more`
Pagers that allow viewing and scrolling one page at a time:
```
root@debian:~# less /var/log/auth.log
Sep 12 18:47:56 debian sshd[441]: Received SIGHUP; restarting.
Sep 12 18:47:56 debian sshd[441]: Server listening on 0.0.0.0 port 22.
Sep 12 18:47:56 debian sshd[441]: Server listening on :: port 22.
Sep 12 18:47:56 debian sshd[441]: Received SIGHUP; restarting.
Sep 12 18:47:56 debian sshd[441]: Server listening on 0.0.0.0 port 22.
Sep 12 18:47:56 debian sshd[441]: Server listening on :: port 22.
Sep 12 18:49:46 debian sshd[905]: Accepted password for carol from 192.168.1.65 port 44296 ssh2
Sep 12 18:49:46 debian sshd[905]: pam_unix(sshd:session): session opened for user carol by (uid=0)
Sep 12 18:49:46 debian systemd-logind[331]: New session 2 of user carol.
Sep 12 18:49:46 debian systemd: pam_unix(systemd-user:session): session opened for user carol by (uid=0)
(...)
```
`zless` or `zmore`
The same as `less` and `more`, but used for logs that are compressed with gzip (a common function of `logrotate`):

```
root@debian:~# zless /var/log/auth.log.3.gz
Aug 19 20:05:57 debian sudo:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/sbin/shutdown -h now
Aug 19 20:05:57 debian sudo: pam_unix(sudo:session): session opened for user root by carol(uid=0)
Aug 19 20:05:57 debian lightdm: pam_unix(lightdm-greeter:session): session closed for user lightdm
Aug 19 23:50:49 debian systemd-logind[333]: Watching system buttons on /dev/input/event2 (Power Button)
Aug 19 23:50:49 debian systemd-logind[333]: Watching system buttons on /dev/input/event3 (Sleep Button)
Aug 19 23:50:49 debian systemd-logind[333]: Watching system buttons on /dev/input/event4 (Video Bus)
Aug 19 23:50:49 debian systemd-logind[333]: New seat seat0.
Aug 19 23:50:49 debian sshd[409]: Server listening on 0.0.0.0 port 22.
(...)
```
`tail`
- View the last lines in a file (the default is 10 lines). The power of tail lies — to a great extent — in the `-f` switch, which will dynamically show new lines as they are appended:
```
root@suse-server:~# tail -f /var/log/messages
2019-09-14T13:57:28.962780+02:00 suse-server sudo: pam_unix(sudo:session): session closed for user root
2019-09-14T13:57:38.038298+02:00 suse-server sudo:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/usr/bin/tail -f /var/log/messages
2019-09-14T13:57:38.039927+02:00 suse-server sudo: pam_unix(sudo:session): session opened for user root by carol(uid=0)
2019-09-14T14:07:22+02:00 debian carol: appending new message from client to remote server...
```
`head`
- View the first lines in a file (the default is 10 lines):
```
root@suse-server:~# head -5 /var/log/mail
2019-06-29T11:47:59.219806+02:00 suse-server postfix/postfix-script[1732]: the Postfix mail system is not running
2019-06-29T11:48:01.355361+02:00 suse-server postfix/postfix-script[1925]: starting the Postfix mail system
2019-06-29T11:48:01.391128+02:00 suse-server postfix/master[1930]: daemon started -- version 3.3.1, configuration /etc/postfix
2019-06-29T11:55:39.247462+02:00 suse-server postfix/postfix-script[3364]: stopping the Postfix mail system
2019-06-29T11:55:39.249375+02:00 suse-server postfix/master[1930]: terminating on signal 15
```
`grep`
Filtering utility which allows you to search for specific strings:
```
root@debian:~# grep "dhclient" /var/log/syslog
Sep 13 11:58:48 debian dhclient[448]: DHCPREQUEST of 192.168.1.4 on enp0s3 to 192.168.1.1 port 67
Sep 13 11:58:49 debian dhclient[448]: DHCPACK of 192.168.1.4 from 192.168.1.1
Sep 13 11:58:49 debian dhclient[448]: bound to 192.168.1.4 -- renewal in 1368 seconds.
(...)
```
As you may have noticed, the output is printed in the following format:

- Timestamp
- Hostname from which the message originated
- Name of program/service that generated the message
- The PID of the program that generated the message
- Description of the action that took place

There are a few examples in which logs are not text, but binary files and — consequently — you must use special commands to parse them:

`/var/log/wtmp`
- Use `who` (or `w`):
```
root@debian:~# who
root    pts/0        2020-09-14 13:05 (192.168.1.75)
root    pts/1        2020-09-14 13:43 (192.168.1.75)
```
`/var/log/btmp`
- Use `utmpdump` or `last -f`:
```
root@debian:~# utmpdump /var/log/btmp
Utmp dump of /var/log/btmp
[6] [01287] [    ] [dave     ] [ssh:notty   ] [192.168.1.75        ] [192.168.1.75   ] [2019-09-07T19:33:32,000000+0000]
```
`/var/log/faillog`
- Use `faillog`:
```
root@debian:~# faillog -a | less
Login       Failures Maximum Latest                   On

root            0        0   01/01/70 01:00:00 +0100
daemon          0        0   01/01/70 01:00:00 +0100
bin             0        0   01/01/70 01:00:00 +0100
sys             0        0   01/01/70 01:00:00 +0100
sync            0        0   01/01/70 01:00:00 +0100
games           0        0   01/01/70 01:00:00 +0100
man             0        0   01/01/70 01:00:00 +0100
lp              0        0   01/01/70 01:00:00 +0100
mail            0        0   01/01/70 01:00:00 +0100
(...)
```
`/var/log/lastlog`
- Use `lastlog`:
```
root@debian:~# lastlog | less
Username         Port     From             Latest
root                                       Never logged in
daemon                                     Never logged in
bin                                        Never logged in
sys                                        Never logged in
(...)
sync                                       Never logged in
avahi                                      Never logged in
colord                                     Never logged in
saned                                      Never logged in
hplip                                      Never logged in
carol            pts/1    192.168.1.75     Sat Sep 14 13:43:06 +0200 2019
dave             pts/3    192.168.1.75     Mon Sep  2 14:22:08 +0200 2019
```
```
Note
There are also graphical tools for reading log files, for example: gnome-logs and KSystemLog.
```
### How Messages are Turned into Logs
The following process illustrates how a message is written to a log file:

1. Applications, services and the kernel write messages in special files (sockets and memory buffers), e.g. `/dev/log` or `/dev/kmsg`.

2. `rsyslogd` gets the information from the sockets or memory buffers.

3. Depending on the rules found in `/etc/rsyslog.conf` and/or the files in `/etc/ryslog.d/`, `rsyslogd` moves the information to the corresponding log file (typically found in `/var/log`).
```
Note
A socket is a special file used to transfer information between different processes. To list all sockets on your system, you can use the command systemctl list-sockets --all.
```
### Facilities, Priorities and Actions
`rsyslog` configuration file is `/etc/rsylog.conf` (in some distributions you can also find configuration files in `/etc/rsyslog.d/`). It is normally divided into three sections: `MODULES`, `GLOBAL DIRECTIVES` and `RULES`. Let us have a look at them by exploring the `rsyslog.conf` file in our Debian GNU/Linux 10 (buster) host — you can use `sudo less /etc/rsyslog.conf` to do so.

`MODULES` includes module support for logging, message capability, and UDP/TCP log reception:
```
#################
#### MODULES ####
#################

module(load="imuxsock") # provides support for local system logging
module(load="imklog")   # provides kernel logging support
#module(load="immark")  # provides --MARK-- message capability

# provides UDP syslog reception
#module(load="imudp")
#input(type="imudp" port="514")

# provides TCP syslog reception
#module(load="imtcp")
#input(type="imtcp" port="514")
```
`GLOBAL DIRECTIVES` allow us to configure a number of things such as logs and log directory permissions:
```
###########################
#### GLOBAL DIRECTIVES ####
###########################

#
# Use traditional timestamp format.
# To enable high precision timestamps, comment out the following line.
#
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat

#
# Set the default permissions for all log files.
#
$FileOwner root
$FileGroup adm
$FileCreateMode 0640
$DirCreateMode 0755
$Umask 0022

#
# Where to place spool and state files
#
$WorkDirectory /var/spool/rsyslog

#
# Include all config files in /etc/rsyslog.d/
#
$IncludeConfig /etc/rsyslog.d/*.conf
```
`RULES` is where facilities, priorities and actions come in. The settings in this section tell the logging daemon to filter messages according to certain rules and log them or send them where required. To understand these rules, we should first explain the concepts of `rsyslog` facilities and priorities. Each log message is given a facility number and keyword that are associated with the Linux internal subsystem that produces the message:

Number | Keyword | Description
--- | --- | ---
0 | kern | Linux kernel messages
1 | user | User-level messages
2 | mail | Mail system
3 | daemon | System daemons
4 | auth, authpriv | Security/Authorization messages
5 | syslog | syslogd messages
6 | lpr | Line printer subsystem
7 | news | Network news subsystem
8 | uucp | UUCP (Unix-to-Unix Copy Protocol) subsystem
9 | cron | Clock daemon
10 | auth, authpriv | Security/Authorization messages
11 | ftp | FTP (File Transfer Protocol) daemon
12 | ntp | NTP (Network Time Protocol) daemon
13 | security | Log audit
14 | console |Log alert
15 | cron | Clock daemon
16 - 23 | local0 through local7 | Local use 0 - 7

Furthermore, each message is assigned a priority level:

Code | Severity | Keyword | Description
--- | --- | --- | ---
0 | Emergency | emerg, panic | System is unusable
1 | Alert | alert | Action must be taken immediately
2 | Critical | crit | Critical conditions
3 | Error | err, error | Error conditions
4 | Warning | warn, warning | Warning conditions
5 | Notice | notice | Normal but significant condition
6 | Informational | info | Informational messages
7 | Debug | debug | Debug-level messages

Here is an excerpt of `rsyslog.conf` from our Debian GNU/Linux 10 (buster) system which including some sample rules:
```
###############
#### RULES ####
###############

# First some standard log files.  Log by facility.
#
auth,authpriv.*                 /var/log/auth.log
*.*;auth,authpriv.none          -/var/log/syslog
#cron.*                         /var/log/cron.log
daemon.*                        -/var/log/daemon.log
kern.*                          -/var/log/kern.log
lpr.*                           -/var/log/lpr.log
mail.*                          -/var/log/mail.log
user.*                          -/var/log/user.log

#
# Logging for the mail system.  Split it up so that
# it is easy to write scripts to parse these files.
#
mail.info                       -/var/log/mail.info
mail.warn                       -/var/log/mail.warn
mail.err                        /var/log/mail.err

#
# Some "catch-all" log files.
#
*.=debug;\
        auth,authpriv.none;\
	news.none;mail.none     -/var/log/debug
*.=info;*.=notice;*.=warn;\
	auth,authpriv.none;\
	cron,daemon.none;\
	mail,news.none          -/var/log/messages
```
The rule format is as follows: `<facility>`.`<priority>` `<action>`

The `<facility>`.`<priority>` selector filters messages to match. Priority levels are hierarchically inclusive, which means rsyslog will match messages of the specified priority and higher. The `<action>` shows what action to take (where to send the log message). Here are a few examples for clarity:
```
auth,authpriv.*                 /var/log/auth.log
```
Regardless of their priority (`*`), all messages from the `auth` or `authpriv` facilities will be sent to `/var/log/auth.log`.
```
*.*;auth,authpriv.none          -/var/log/syslog
```
All messages — irrespective of their priority (`*`) — from all facilities (`*`) — discarding those from `auth` or `authpriv` (hence the `.none` suffix) — will be written to `/var/log/syslog` (the minus sign (`-`) before the path prevents excessive disk writes). Note the semicolon (`;`) to split the selector and the comma (`,`) to concatenate two facilities in the same rule (`auth,authpriv`).
```
mail.err                        /var/log/mail.err
```
Messages from the `mail` facility with a priority level of `error` or higher (`critical`, `alert` or `emergency`) will be sent to `/var/log/mail.err`.
```
*.=debug;\
        auth,authpriv.none;\
	news.none;mail.none     -/var/log/debug
```
Messages from all facilities with the `debug` priority and no other (`=`) will be written to `/var/log/debug` — excluding any messages coming from the `auth`, `authpriv`,` news` and `mail` facilities (note the syntax: `;\`).

#### Manual Entries into the System Log: logger
The `logger` command comes in handy for shell scripting or for testing purposes. `logger` will append any message it receives to `/var/log/syslog` (or to `/var/log/messages` when logging to a remote central log server as you will see later in this lesson):
```
carol@debian:~$ logger this comment goes into "/var/log/syslog"
```
To print the last line in `/var/log/syslog`, use the `tail` command with the `-1` option:
```
root@debian:~# tail -1 /var/log/syslog
Sep 17 17:55:33 debian carol: this comment goes into /var/log/syslog
```
#### `rsyslog` as a Central Log Server
To explain this topic we are going to add a new host to our setup. The layout is as follows:

Role | Hostname | OS | IP Address
--- | --- | --- | ---
Central Log Server | suse-server | openSUSE Leap 15.1 | 192.168.1.6
Client | debian | Debian GNU/Linux 10 (buster) | 192.168.1.4

Let us start by configuring the server. First of all, we make sure that `rsyslog` is up and running:
```
root@suse-server:~# systemctl status rsyslog
 rsyslog.service - System Logging Service
   Loaded: loaded (/usr/lib/systemd/system/rsyslog.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2019-09-17 18:45:58 CEST; 7min ago
     Docs: man:rsyslogd(8)
           http://www.rsyslog.com/doc/
 Main PID: 832 (rsyslogd)
    Tasks: 5 (limit: 4915)
   CGroup: /system.slice/rsyslog.service
           └─832 /usr/sbin/rsyslogd -n -iNONE
```
openSUSE ships with a dedicated configuration file for remote logging: `/etc/rsyslog.d/remote.conf`. Let us enable receiving messages from clients (remote hosts) via TCP. We must uncomment the lines which load the module and start the TCP server on port 514:
```
# ######### Receiving Messages from Remote Hosts ##########
# TCP Syslog Server:
# provides TCP syslog reception and GSS-API (if compiled to support it)
$ModLoad imtcp.so  # load module
##$UDPServerAddress 10.10.0.1  # force to listen on this IP only
$InputTCPServerRun 514  # Starts a TCP server on selected port

# UDP Syslog Server:
#$ModLoad imudp.so  # provides UDP syslog reception
##$UDPServerAddress 10.10.0.1  # force to listen on this IP only
#$UDPServerRun 514  # start a UDP syslog server at standard port 514
```
Once this is done, we must restart the rsyslog service and check that the server is listening on port 514:
```
root@suse-server:~# systemctl restart rsyslog
root@suse-server:~# netstat -nltp | grep 514
[sudo] password for root:
tcp        0      0 0.0.0.0:514             0.0.0.0:*               LISTEN      2263/rsyslogd
tcp6       0      0 :::514                  :::*                    LISTEN      2263/rsyslogd
```
Next, we should open the ports in the firewall and reload the configuration:
```
root@suse-server:~# firewall-cmd --permanent --add-port 514/tcp
success
root@suse-server:~# firewall-cmd --reload
success
```
```
Note
With the arrival of openSUSE Leap 15.0, firewalld replaced the classic SuSEFirewall2 completely.
```
### Templates and Filter Conditions
By default, the client’s logs will be written to the server’s `/var/log/messages` file — together with those of the server themselves. However, we will create a template and a filter condition to have our client’s logs stored in clear-cut directories of their own. To do so, we will add the following to `/etc/rsyslog.conf` (or `/etc/rsyslog.d/remote.conf`):
```
$template RemoteLogs,"/var/log/remotehosts/%HOSTNAME%/%$NOW%.%syslogseverity-text%.log"
if $FROMHOST-IP=='192.168.1.4' then ?RemoteLogs
& stop
```
Template
- The template corresponds to the first line and allows you to specify a format for log names using dynamic file name generation. A template consists of:
   - Template directive ($template)
   - Template name (RemoteLogs)
   - Template text ("/var/log/remotehosts/%HOSTNAME%/%$NOW%.%syslogseverity-text%.log")
   - Options (optional)

Our template is called `RemoteLogs` and its text consists of a path in `/var/log`. All of our remote host’s logs will go into the `remotehosts` directory, where a subdirectory will be created based on the machine’s hostname (`%HOSTNAME%`). Each filename in this directory will consist of the date (`%$NOW%`), the severity (aka priority) of the message in text format (`%syslogseverity-text%`) and the `.log` suffix. The words between percent signs are properties and allow you access to the contents of the log message (date, priority, etc.). A `syslog` message has a number of well-defined properties which can be used in templates. These properties are accessed — and can be modified — by the so-called property replacer which implies writing them between percent signs.

Filter Condition
- The remaining two lines correspond to the filter condition and its associated action:
   - Expression-Based Filter (if $FROMHOST-IP=='192.168.1.4')
   - Action (then ?RemoteLogs, & stop)

The first line checks the IP address of the remote host sending the log and — if it equals that of our Debian client — it applies the `RemoteLogs` template. The last line (`& stop`) guarantees that messages are not being sent simultaneously to `/var/log/messages` (but only to files in the `/var/log/remotehosts` directory).
```
Note
To learn more about templates, properties and rules, you can consult the manual page for rsyslog.conf.
```
With the configuration updated, we will restart `rsyslog` again and confirm there is no remotehosts directory in `/var/log` yet:
```
root@suse-server:~# systemctl restart rsyslog
root@suse-server:~# ls /var/log/
acpid             chrony     localmessages   pbl.log          Xorg.0.log
alternatives.log  cups       mail            pk_backend_zypp  Xorg.0.log.old
apparmor          firebird   mail.err        samba            YaST2
audit             firewall   mail.info       snapper.log      zypp
boot.log          firewalld  mail.warn       tallylog         zypper.log
boot.msg          krb5       messages        tuned
boot.omsg         lastlog    mysql           warn
btmp              lightdm    NetworkManager  wtmp
```
The server has now been configured. Next, we will configure the client.

Again, we must ensure that `rsyslog` is installed and running:
```
root@debian:~# sudo systemctl status rsyslog
 rsyslog.service - System Logging Service
   Loaded: loaded (/lib/systemd/system/rsyslog.service; enabled; vendor preset:
   Active: active (running) since Thu 2019-09-17 18:47:54 CEST; 7min ago
     Docs: man:rsyslogd(8)
           http://www.rsyslog.com/doc/
 Main PID: 351 (rsyslogd)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/rsyslog.service
           └─351 /usr/sbin/rsyslogd -n
```
In our sample environment we have implemented name resolution on the client by adding the line `192.168.1.6`` suse-server` to `/etc/hosts`. Thus, we can refer to the server by either name (`suse-server`) or IP address (`192.168.1.6`).

Our Debian client does not come with a `remote.conf` file in `/etc/rsyslog.d/`, so we will apply our configurations in `/etc/rsyslog.conf`. We will write the following line at the end of the file:
```
*.* @@suse-server:514
```
Finally, we restart `rsyslog`.
```
root@debian:~# systemctl restart rsyslog
```
Now, let us go back to our `suse-server` machine and check for the existence of `remotehosts` in `/var/log`:
```
root@suse-server:~# ls /var/log/remotehosts/debian/
2019-09-17.info.log  2019-09-17.notice.log
```
We already have two logs inside `/var/log/remotehosts` as described in our template. To complete this section, we run `tail -f` `2019-09-17.notice.log` on `suse-server` while we send a log manually from our Debian client and confirm that messages are appended to the log file as expected (the `-t` option supplies a tag for our message):
```
root@suse-server:~# tail -f /var/log/remotehosts/debian/2019-09-17.notice.log
2019-09-17T20:57:42+02:00 debian dbus[323]: [system] Successfully activated service 'org.freedesktop.nm_dispatcher'
2019-09-17T21:01:41+02:00 debian anacron[1766]: Anacron 2.3 started on 2019-09-17
2019-09-17T21:01:41+02:00 debian anacron[1766]: Normal exit (0 jobs run)
```
```
carol@debian:~$ logger -t DEBIAN-CLIENT Hi from 192.168.1.4
```
```
root@suse-server:~# tail -f /var/log/remotehosts/debian/2019-09-17.notice.log
2019-09-17T20:57:42+02:00 debian dbus[323]: [system] Successfully activated service 'org.freedesktop.nm_dispatcher'
2019-09-17T21:01:41+02:00 debian anacron[1766]: Anacron 2.3 started on 2019-09-17
2019-09-17T21:01:41+02:00 debian anacron[1766]: Normal exit (0 jobs run)
2019-09-17T21:04:21+02:00 debian DEBIAN-CLIENT: Hi from 192.168.1.4
```
### The Log Rotation Mechanism
Logs are rotated on a regular basis, which serves two main purposes:
- Prevent older log files from using more disk space than necessary.
- Keep logs to a manageable length for ease of consultation.

The utility in charge of log rotation (or cycling) is `logrotate` and its job includes actions such as moving log files to a new name, archiving and/or compressing them, sometimes emailing them to the system administrator and eventually deleting them as they grow old. There are various conventions for naming these rotated log files (adding a suffix with the date to the filename, for example); however, simply adding a suffix with an integer is the common practice:
```
root@debian:~# ls /var/log/messages*
/var/log/messages  /var/log/messages.1  /var/log/messages.2.gz  /var/log/messages.3.gz  /var/log/messages.4.gz
```
Let us now explain what will happen in the next log rotation:
1. `messages.4.gz` will be deleted and lost.
2. The content of `messages.3.gz` will be moved to `messages.4.gz`.
3. The content of `messages.2.gz` will be moved to `messages.3.gz`.
4. The content of `messages.1` will be moved to `messages.2.gz`.
5. The content of `messages` will be moved to `messages.1` and `messages` will be empty and ready to register new log entries.

Note how — according to `logrotate` directives that you will see shortly — the three older log files are compressed, whereas the two most recent ones are not. Also, we will be retaining the logs from the last 4-5 weeks. To read messages that are 1 week old, we will consult `messages.1` (and so on).

`logrotate` is run as an automated process or cron job daily through the script `/etc/cron.daily/logrotate` and reads the configuration file `/etc/logrotate.conf`. This file includes some global options and is well commented with each option introduced by a brief explanation of its purpose:
```
carol@debian:~$ sudo less /etc/logrotate.conf
# see "man logrotate" for details
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# uncomment this if you want your log files compressed
#compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

(...)
```
As you can see, the configuration files in `/etc/logrotate.d` for specific packages are also included. These files contain — for the most part — local definitions and specify the logfiles to rotate (remember, local definitions take precedence over global ones, and later definitions override earlier ones). What follows is an excerpt of a definition in `/etc/logrotate.d/rsyslog`:
```
/var/log/messages
{
        rotate 4
        weekly
        missingok
        notifempty
        compress
        delaycompress
        sharedscripts
        postrotate
                invoke-rc.d rsyslog rotate > /dev/null
        endscript
}
```
As you can see, every directive is separated from its value by whitespace and/or an optional equals sign (`=`). The lines between `postrotate` and `endscript` must appear on lines by themselves, though. The explanation is as follows:

`rotate 4`
- Retain 4 weeks worth of logs.

`weekly`
- Rotate log files weekly.

`missingok`
- Do not issue an error message if the log file is missing; just go on to the next one.

`notifempty`
- Do not rotate the log if it is empty.

`compress`
- Compress log files with `gzip` (default).

`delaycompress`
- Postpone compression of the previous log file to the next rotation cycle (only effective when used in combination with compress). It is useful when a program cannot be told to close its log file and thus might continue writing to the previous log file for some time.

`sharedscripts`
- Related to prerotate and postrotate scripts. To prevent a script from being executed multiple times, run the scripts only once regardless of how many log files match a given pattern (e.g. /var/log/mail/*).The scripts will not be run if none of the logs in the pattern require rotating, though. On top of that, if the scripts exit with errors, any remaining actions will not be executed for any logs.

`postrotate`
- Indicate the beginning of a postrotate script.

`invoke-rc.d rsyslog rotate > /dev/null`
- Use `/bin/sh` to run `invoke-rc.d rsyslog rotate > /dev/null` after rotating the logs.

`endscript`
Indicate the end of the postrotate script.
```
Note
For a complete list of directives and explanations, consult the manual page for logrotate.conf.
```
### The Kernel Ring Buffer
Since the kernel generates several messages before `rsyslogd` becomes available on boot, a mechanism to register those messages becomes necessary. This is where the kernel ring buffer comes into play. It is a fixed-size data structure and — therefore — as it grows with new messages, the oldest will disappear.

The `dmesg` command prints the kernel ring buffer. Because of the buffer’s size, this command is normally used in combination with the text filtering utility `grep`. For instance, to search for messages related to Universal Serial Bus devices:
```
root@debian:~# dmesg | grep "usb"
[    1.241182] usbcore: registered new interface driver usbfs
[    1.241188] usbcore: registered new interface driver hub
[    1.250968] usbcore: registered new device driver usb
[    1.339754] usb usb1: New USB device found, idVendor=1d6b, idProduct=0001, bcdDevice= 4.19
[    1.339756] usb usb1: New USB device strings: Mfr=3, Product=2, SerialNumber=1
(...)
```
___

With the general adoption of `systemd` by all major distributions, the journal daemon (`systemd-journald`) has become the standard logging service. In this lesson we will discuss how it operates and how you can use it to do a number of things: query it, filter its information by different criteria, configure its storage and size, delete old data, retrieve its data from a rescue system or filesystem copy and — last but not least — understand its interaction with `rsyslogd`.

### Basics of `systemd`
First introduced in Fedora, `systemd` has progressively replaced SysV Init as the de facto system and service manager in most major Linux distributions. Amongst its strenghts are the following:
- Ease of configuration: unit files as opposed to SysV Init scripts.
- Versatile management: apart from daemons and processes, it also manages devices, sockets and mount points.
- Backward compatibility with both SysV Init and Upstart.
- Parallel loading during boot-up: services are loaded in parallel as opposed to Sysv Init loading them sequentially.
- It features a logging service called the journal that presents the following advantages:
  - It centralizes all logs in one place.
  - It does not require log rotation.
  - Logs can be disabled, loaded in RAM or made persistent.

### Units and Targets
`systemd` operates on units. A unit is any resource that `systemd` can manage (e.g. network, bluetooth, etc.). Units — in turn — are governed by unit files. These are plain text files which live in `/lib/systemd/system` and include the configuration settings — in the form of sections and directives — for a particular resource to be managed. There are a number of unit types: `service`, `mount`, `automount`, `swap`, `timer`, `device`, `socket`,`path`, `timer`, `snapshot`, `slice`, `scope` and `target`. Thus, every unit file name follows the pattern `<resource_name>.<unit_type>` (e.g. `reboot.service`).

A target is a special type of unit which resembles the classic runlevels of SysV Init. This is because a target unit brings together various resources to represent a particular system state (e.g. `graphical.target` is similar to `runlevel 5`, etc.). To check the current target in your system, use the `systemctl get-default` command:
```
carol@debian:~$ systemctl get-default
graphical.target
```
On the other hand, targets and runlevels differ in that the former are mutually inclusive, whereas the latter are not. Thus a target can bring up other targets — which is not possible with runlevels.
```
Note
An explanation of how systemd units work is beyond the scope of this lesson.
```

### The System Journal: `systemd-journald`
`systemd-journald` is the system service which takes care of receiving logging information from a variety of sources: kernel messages, simple and structured system messages, standard output and standard error of services as well as audit records from the kernel audit subsystem (for further details see the manual page for `systemd-journald`). Its mission is that of creating and maintaining a structured and indexed journal.

Its configuration file is `/etc/systemd/journald.conf` and — as with any other service — you can use the `systemctl` command to start it, restart it, stop it or — simply — check its status:
```
root@debian:~# systemctl status systemd-journald
 systemd-journald.service - Journal Service
   Loaded: loaded (/lib/systemd/system/systemd-journald.service; static; vendor preset: enabled)
   Active: active (running) since Sat 2019-10-12 13:43:06 CEST; 5min ago
     Docs: man:systemd-journald.service(8)
           man:journald.conf(5)
 Main PID: 178 (systemd-journal)
   Status: "Processing requests..."
    Tasks: 1 (limit: 4915)
   CGroup: /system.slice/systemd-journald.service
           └─178 /lib/systemd/systemd-journald
(...)
```
Configuration files of the type `journal.conf.d/*.conf` — which can include package-specific configurations — are also possible (consult the manual page of `journald.conf` to learn more).

If enabled, the journal can be stored either persistently on disk or in a volatile manner on a RAM-based filesystem. The journal is not a plain text file, it is binary. Thus, you cannot use text parsing tools such as `less` or `more` to read its contents; the `journalctl` command is used instead.

### Querying the Journal Content
`journalctl` is the utility that you use to query `systemd` journal. You have to either be root or use `sudo` to invoke it. If queried without options, it will print the entire journal chronologically (with the oldest entries listed first):
```
root@debian:~# journalctl
-- Logs begin at Sat 2019-10-12 13:43:06 CEST, end at Sat 2019-10-12 14:19:46 CEST. --
Oct 12 13:43:06 debian kernel: Linux version 4.9.0-9-amd64 (debian-kernel@lists.debian.org) (...)
Oct 12 13:43:06 debian kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-9-amd64 root=UUID=b6be6117-5226-4a8a-bade-2db35ccf4cf4 ro qu
(...)
```
You can make more specific queries by using a number of switches:

`-r`
- The messages of the journal will be printed in reverse order:
```
root@debian:~# journalctl -r
-- Logs begin at Sat 2019-10-12 13:43:06 CEST, end at Sat 2019-10-12 14:30:30 CEST. --
Oct 12 14:30:30 debian sudo[1356]: pam_unix(sudo:session): session opened for user root by carol(uid=0)
Oct 12 14:30:30 debian sudo[1356]:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/bin/journalctl -r
Oct 12 14:19:53 debian sudo[1348]: pam_unix(sudo:session): session closed for user root
(...)
```
`-f`
- It will print the most recent journal messages and keep printing new entries as they are appended to the journal — much like `tail -f`:
```
root@debian:~# journalctl -f
-- Logs begin at Sat 2019-10-12 13:43:06 CEST. --
(...)
Oct 12 14:44:42 debian sudo[1356]: pam_unix(sudo:session): session closed for user root
Oct 12 14:44:44 debian sudo[1375]:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/bin/journalctl -f
Oct 12 14:44:44 debian sudo[1375]: pam_unix(sudo:session): session opened for user root by carol(uid=0)

(...)
```
`-e`
- It will jump to the end of the journal so that the latest entries are visible within the pager:
```
root@debian:~# journalctl -e
(...)
Oct 12 14:44:44 debian sudo[1375]:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/bin/journalctl -f
Oct 12 14:44:44 debian sudo[1375]: pam_unix(sudo:session): session opened for user root by carol(uid=0)
Oct 12 14:45:57 debian sudo[1375]: pam_unix(sudo:session): session closed for user root
Oct 12 14:48:39 debian sudo[1378]:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/bin/journalctl -e
Oct 12 14:48:39 debian sudo[1378]: pam_unix(sudo:session): session opened for user root by carol(uid=0)
```
`-n <value>, --lines=<value>`
- It will print the value most recent lines (if no `<value>` is specified, it defaults to 10):
```
root@debian:~# journalctl -n 5
(...)
Oct 12 14:44:44 debian sudo[1375]:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/bin/journalctl -f
Oct 12 14:44:44 debian sudo[1375]: pam_unix(sudo:session): session opened for user root by carol(uid=0)
Oct 12 14:45:57 debian sudo[1375]: pam_unix(sudo:session): session closed for user root
Oct 12 14:48:39 debian sudo[1378]:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/bin/journalctl -e
Oct 12 14:48:39 debian sudo[1378]: pam_unix(sudo:session): session opened for user root by carol(uid=0)
```
`-k`, `--dmesg`
- Equivalent to using the `dmesg` command:
```
root@debian:~# journalctl -k
-- Logs begin at Sat 2019-10-12 13:43:06 CEST, end at Sat 2019-10-12 14:53:20 CEST. --
Oct 12 13:43:06 debian kernel: Linux version 4.9.0-9-amd64 (debian-kernel@lists.debian.org) (gcc version 6.3.0 20170516 (Debian 6.3.0-18
Oct 12 13:43:06 debian kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-9-amd64 root=UUID=b6be6117-5226-4a8a-bade-2db35ccf4cf4 ro qu
Oct 12 13:43:06 debian kernel: x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
Oct 12 13:43:06 debian kernel: x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
(...)
```
#### Navigating and Searching Through the Journal
You can navigate the journal’s outuput with:
- PageUp, PageDown and arrow keys to move up, down, left and right.
- `>` to go to the end of the output.
- `<` to go to the beginning of the output.

You can search for strings both forward and backward from your current view position:

- Forward search: Press `/` and enter the string to be searched, then press Enter.

- Backward search: Press `?` and enter the string to be searched, then press Enter.

To navigate through matches in the searches, use `N` to go to the next occurrence of the match and `Shift`+`N` to go to the previous one.

### Filtering the Journal Data
The journal allows you to filter log data by different criteria:

Boot number
-  `--list-boots`
   - It lists all available boots. The output consists of three columns; the first one specifies the boot number (`0` refers to the current boot, `-1` is the previous one, `-2` the one prior to the previous one and so on); the second column is the boot ID; the third shows the time stamps:
```
root@debian:~# journalctl --list-boots
 0 83df3e8653474ea5aed19b41cdb45b78 Sat 2019-10-12 18:55:41 CEST—Sat 2019-10-12 19:02:24 CEST
 ```
`-b`, `--boot`
- It shows all messages from the current boot. To see log messages from previous boots, just add an offset parameter as explained above. For example, to have messages from the previous boot printed, you will type `journalctl -b -1`. Remember, though, that to recover information from previous logs, persistence of the journal must be enabled (you will learn how to do so in the next section):
```
root@debian:~# journalctl -b -1
Specifying boot ID has no effect, no persistent journal was found
```
Priority
- -p`
   - Interestingly enough, you can also filter by severity/priority with the `-p` option:
```
root@debian:~# journalctl -b -0 -p err
-- No entries --
```
The journal informs us that — so far — there have not been any messages with a priority of `error` (or above) from the current boot. Note: `-b -0` can be omitted when referring to the current boot.
```
Note
Refer to the previous lesson for a complete list of all syslog severities (aka priorities).
```
Time Interval
- You can have `journalctl` print only the messages logged within a specific time frame by using the `--since` and `--until` switches. The date specification should follow the format `YYYY-MM-DD HH:MM:SS`. Midnight will be assumed if we omit the time component. By the same token, if the date is omitted, the current day is assumed. For instance, to see messages logged from 7:00pm to 7:01pm, you will type:
```
root@debian:~# journalctl --since "19:00:00" --until "19:01:00"
-- Logs begin at Sat 2019-10-12 18:55:41 CEST, end at Sat 2019-10-12 20:10:50 CEST. --
Oct 12 19:00:14 debian systemd[1]: Started Run anacron jobs.
Oct 12 19:00:14 debian anacron[1057]: Anacron 2.3 started on 2019-10-12
Oct 12 19:00:14 debian anacron[1057]: Normal exit (0 jobs run)
Oct 12 19:00:14 debian systemd[1]: anacron.timer: Adding 2min 47.988096s random time.
```
Likewise you can use a slightly different time specification: `"integer time-unit ago"`. Thus, to see messages logged two minutes ago you will type `sudo journalctl --since "2 minutes ago"`. It is also possible to use `+` and `-` to specify times relative to the current time so `--since "-2 minutes"` and `--since "2 minutes ago"` are equivalent.

Apart from numeric expressions, you can specify a number of keywords:

`yesterday`
- As of midnight of the day before the current day.

`today`
- As of midnight of the current day.

`tomorrow`
- As of midnight of the day after the current day.

`now`
- The current time.
- Let us see all messages since last midnight until today at 21:00pm:
```
root@debian:~# journalctl --since "today" --until "21:00:00"
-- Logs begin at Sat 2019-10-12 20:45:29 CEST, end at Sat 2019-10-12 21:06:15 CEST. --
Oct 12 20:45:29 debian sudo[1416]:    carol : TTY=pts/0 ; PWD=/home/carol ; USER=root ; COMMAND=/bin/systemctl r
Oct 12 20:45:29 debian sudo[1416]: pam_unix(sudo:session): session opened for user root by carol(uid=0)
Oct 12 20:45:29 debian systemd[1]: Stopped Flush Journal to Persistent Storage.
(...)
```
```
Note
To learn more about the different syntaxes for time specifications, consult the manual page systemd.time.
```
Program
- To see journal messages related to a specific executable the following syntax is used: journalctl /path/to/executable:
```
root@debian:~# journalctl /usr/sbin/sshd
-- Logs begin at Sat 2019-10-12 20:45:29 CEST, end at Sat 2019-10-12 21:54:49 CEST. --
Oct 12 21:16:28 debian sshd[1569]: Accepted password for carol from 192.168.1.65 port 34050 ssh2
Oct 12 21:16:28 debian sshd[1569]: pam_unix(sshd:session): session opened for user carol by (uid=0)
Oct 12 21:16:54 debian sshd[1590]: Accepted password for carol from 192.168.1.65 port 34052 ssh2
Oct 12 21:16:54 debian sshd[1590]: pam_unix(sshd:session): session opened for user carol by (uid=0)
```
Unit
- Remember, a unit is any resource handled by `systemd` and you can filter by them, too.
   - `-u`
     - It shows messages about a specified unit:
```
root@debian:~# journalctl -u ssh.service
-- Logs begin at Sun 2019-10-13 10:50:59 CEST, end at Sun 2019-10-13 12:22:59 CEST. --
Oct 13 10:51:00 debian systemd[1]: Starting OpenBSD Secure Shell server...
Oct 13 10:51:00 debian sshd[409]: Server listening on 0.0.0.0 port 22.
Oct 13 10:51:00 debian sshd[409]: Server listening on :: port 22.
(...)
```
```
Note
To print all loaded and active units, use systemctl list-units; to see all installed unit files use systemctl list-unit-files.
```
Fields
- The journal can also be filtered by specific fields through any of the following syntaxes:
  - `<field-name>=<value>`
  - `_<field-name>=<value>_`
  - `__<field-name>=<value>`


  - `PRIORITY=`
    - One of eight possible `syslog` priority values formatted as a decimal string:
```
root@debian:~# journalctl PRIORITY=3
-- Logs begin at Sun 2019-10-13 10:50:59 CEST, end at Sun 2019-10-13 14:30:50 CEST. --
Oct 13 10:51:00 debian avahi-daemon[314]: chroot.c: open() failed: No such file or directory
```
   - Note how you could have achieved the same output by using the command `sudo journalctl -p err` that we saw above.

  - `SYSLOG_FACILITY=`
    - Any of the possible facility code numbers formatted as a decimal string. For example, to see all user-level messages:
```
root@debian:~# journalctl SYSLOG_FACILITY=1
-- Logs begin at Sun 2019-10-13 10:50:59 CEST, end at Sun 2019-10-13 14:42:52 CEST. --
Oct 13 10:50:59 debian mtp-probe[227]: checking bus 1, device 2: "/sys/devices/pci0000:00/0000:00:06.0/usb1/1-1"
Oct 13 10:50:59 debian mtp-probe[227]: bus: 1, device: 2 was not an MTP device
Oct 13 10:50:59 debian mtp-probe[238]: checking bus 1, device 2: "/sys/devices/pci0000:00/0000:00:06.0/usb1/1-1"
Oct 13 10:50:59 debian mtp-probe[238]: bus: 1, device: 2 was not an MTP device
```
  - `_PID=`
    - Show messages produced by a specific process ID. To see all messages produced by systemd, you would type:
```
root@debian:~# journalctl _PID=1
-- Logs begin at Sun 2019-10-13 10:50:59 CEST, end at Sun 2019-10-13 14:50:15 CEST. --
Oct 13 10:50:59 debian systemd[1]: Mounted Debug File System.
Oct 13 10:50:59 debian systemd[1]: Mounted POSIX Message Queue File System.
Oct 13 10:50:59 debian systemd[1]: Mounted Huge Pages File System.
Oct 13 10:50:59 debian systemd[1]: Started Remount Root and Kernel File Systems.
Oct 13 10:50:59 debian systemd[1]: Starting Flush Journal to Persistent Storage...
(...)
```
  - `_BOOT_ID`
    - Based on its boot ID you can single out the messages from a specific boot, for example: `sudo journalctl _BOOT_ID=83df3e8653474ea5aed19b41cdb45b78`.

  - `_TRANSPORT`
    - Show messages received from a specific transport. Possible values are: `audit` (kernel audit subsystem), `driver` (generated internally), `syslog` (syslog socket), `journal` (native journal protocol), `stdout` (services' standard output or standard error), `kernel` (kernel ring buffer — the same as `dmesg`, `journalctl -k` or `journalctl --dmesg`):
```
root@debian:~# journalctl _TRANSPORT=journal
-- Logs begin at Sun 2019-10-13 20:19:58 CEST, end at Sun 2019-10-13 20:46:36 CEST. --
Oct 13 20:19:58 debian systemd[1]: Started Create list of required static device nodes for the current kernel.
Oct 13 20:19:58 debian systemd[1]: Starting Create Static Device Nodes in /dev...
Oct 13 20:19:58 debian systemd[1]: Started Create Static Device Nodes in /dev.
Oct 13 20:19:58 debian systemd[1]: Starting udev Kernel Device Manager...
(...)
```
#### Combining Fields
Fields are not mutually exclusive so you can use more than one in the same query. However, only messages that match the value of both fields simultaneously will be shown:
```
root@debian:~# journalctl PRIORITY=3 SYSLOG_FACILITY=0
-- No entries --
root@debian:~# journalctl PRIORITY=4 SYSLOG_FACILITY=0
-- Logs begin at Sun 2019-10-13 20:19:58 CEST, end at Sun 2019-10-13 20:21:55 CEST. --
Oct 13 20:19:58 debian kernel: acpi PNP0A03:00: fail to add MMCONFIG information, can't access extended PCI configuration (...)
```
Unless you use the `+` separator to combine two expressions in the manner of a logical OR:
```
root@debian:~# journalctl PRIORITY=3 + SYSLOG_FACILITY=0
-- Logs begin at Sun 2019-10-13 20:19:58 CEST, end at Sun 2019-10-13 20:24:02 CEST. --
Oct 13 20:19:58 debian kernel: Linux version 4.9.0-9-amd64 (debian-kernel@lists.debian.org) (...9
Oct 13 20:19:58 debian kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-9-amd64 root=UUID= (...)
(...)
```
On the other hand, you can supply two values for the same field and all entries matching either value will be shown:
```
root@debian:~# journalctl PRIORITY=1
-- Logs begin at Sun 2019-10-13 17:16:24 CEST, end at Sun 2019-10-13 17:30:14 CEST. --
-- No entries --
root@debian:~# journalctl PRIORITY=1 PRIORITY=3
-- Logs begin at Sun 2019-10-13 17:16:24 CEST, end at Sun 2019-10-13 17:32:12 CEST. --
Oct 13 17:16:27 debian connmand[459]: __connman_inet_get_pnp_nameservers: Cannot read /pro
Oct 13 17:16:27 debian connmand[459]: The name net.connman.vpn was not provided by any .se
```
```
Note
Journal fields fall in any of the following categories: “User Journal Fields”, “Trusted Journal Fields”, “Kernel Journal Fields”, “Fields on behalf of a different program” and “Address Fields”. For more information on this topic — including a complete list of fields — see the man page for systemd.journal-fields(7).
```
### Manual Entries in the System Journal: systemd-cat
Just like how the `logger` command is used to send messages from the command line to the system log (as we saw in the previous lesson), the `systemd-cat` command serves a similar — but more well-rounded — purpose with the system journal. It allows us to send standard input (stdin), output (stdout) and error (stderr) to the journal.

If invoked with no parameters, it will send everything it reads from stdin to the journal. Once you are done, press `Ctrl`+`C`:.
```
carol@debian:~$ systemd-cat
This line goes into the journal.
^C
```
If it is passed the output of a piped command, this will be sent to the journal too:
```
carol@debian:~$ echo "And so does this line." | systemd-cat
```
If followed by a command, that command’s output will also be sent to the journal — together with stderr (if any):
```
carol@debian:~$ systemd-cat echo "And so does this line too."
```
There is also the possibility of specifiying a priority level with the `-p` option:
```
carol@debian:~$ systemd-cat -p emerg echo "This is not a real emergency."
```
Refer to `systemd-cat` man page to learn about its other options.

To see the last four lines in the journal:
```
carol@debian:~$ journalctl -n 4
(...)
-- Logs begin at Sun 2019-10-20 13:43:54 CEST. --
Nov 13 23:14:39 debian cat[1997]: This line goes into the journal.
Nov 13 23:19:16 debian cat[2027]: And so does this line.
Nov 13 23:23:21 debian echo[2030]: And so does this line too.
Nov 13 23:26:48 debian echo[2034]: This is not a real emergency.
```
```
Note
Journal entries with a priority level of emergency will be printed in bold red on most systems.
```
### Persistent Journal Storage
As mentioned previously, you have three options when it comes to the location of the journal:
- Journaling can be turned off altogether (redirection to other facilities such as the console are still possible, though).
- Keep it in memory — which makes it volatile — and get rid of the logs with every system reboot. In this scenario, the directory `/run/log/journal` will be created and used.
- Make it persistent so that it writes logs to disk. In this case, log messages will go into the `/var/log/journal` directory.

The default behaviour is as follows: if `/var/log/journal/` does not exist, logs will be saved in a volatile way to a directory in `/run/log/journal/` and — therefore — lost at reboot. The name of the directory — the `/etc/machine-id` — is a newline-terminated, hexadecimal, 32-character, lowercase string:
```
carol@debian:~$ ls /run/log/journal/8821e1fdf176445697223244d1dfbd73/
system.journal
```
If you try to read it with less you will get a warning, so instead use the command `journalctl`:
```
root@debian:~# less /run/log/journal/9a32ba45ce44423a97d6397918de1fa5/system.journal
"/run/log/journal/9a32ba45ce44423a97d6397918de1fa5/system.journal" may be a binary file.  See it anyway?
root@debian:~# journalctl
-- Logs begin at Sat 2019-10-05 21:26:38 CEST, end at Sat 2019-10-05 21:31:27 CEST. --
(...)
Oct 05 21:26:44 debian systemd-journald[1712]: Runtime journal (/run/log/journal/9a32ba45ce44423a97d6397918de1fa5) is 4.9M, max 39.5M, 34.6M free.
Oct 05 21:26:44 debian systemd[1]: Started Journal Service.
(...)
```
If `/var/log/journal/` exists, logs will be stored persistently there. Should this directory be deleted, `systemd-journald` would not recreate it but write to `/run/log/journal` instead. As soon as we create `/var/log/journal/` again and restart the daemon, persistent logging will be restablished:
```
root@debian:~# mkdir /var/log/journal/
root@debian:~# systemctl restart systemd-journald
root@debian:~# journalctl
(...)
Oct 05 21:33:49 debian systemd-journald[1712]: Received SIGTERM from PID 1 (systemd).
Oct 05 21:33:49 debian systemd[1]: Stopped Journal Service.
Oct 05 21:33:49 debian systemd[1]: Starting Journal Service...
Oct 05 21:33:49 debian systemd-journald[1768]: Journal started
Oct 05 21:33:49 debian systemd-journald[1768]: System journal (/var/log/journal/9a32ba45ce44423a97d6397918de1fa5) is 8.0M, max 1.1G, 1.1G free.
Oct 05 21:33:49 debian systemd[1]: Started Journal Service.
Oct 05 21:33:49 debian systemd[1]: Starting Flush Journal to Persistent Storage...
(...)
```
```
Note
By default, there will be specific journal files for every logged in user, located in /var/log/journal/, so — together with system.journal files — you will also find files of the type user-1000.journal.
```
On top of what we have just mentioned, the way the journal daemon deals with log storage can be changed after installation by tweaking its configuration file: `/etc/systemd/journald.conf`. The key option is `Storage=` and can have the following values:

`Storage=volatile`
- Log data will be stored exclusively in memory — under `/run/log/journal/`. If not present, the directory will be created.

`Storage=persistent`
- By default log data will be stored on disk  — under `/var/log/journal/` —  with a fallback to memory (`/run/log/journal/`) during early boot stages and if the disk is not writable. Both directories will be created if needed.

`Storage=auto`
- `auto` is similar to `persistent`, but the directory `/var/log/journal` is not created if needed. This is the default.

`Storage=none`
- All log data will be discarded. Forwarding to other targets such as the console, the kernel log buffer, or a syslog socket are still possible, though.

For instance, to have `systemd-journald` create `/var/log/journal/` and switch to persistent storage, you would edit `/etc/systemd/journald.conf` and set `Storage=persistent`, save the file and restart the daemon with `sudo systemctl restart systemd-journald`. To ensure the restart went flawlessly, you can always check the daemon’s status:
```
root@debian:~# systemctl status systemd-journald
 systemd-journald.service - Journal Service
   Loaded: loaded (/lib/systemd/system/systemd-journald.service; static; vendor preset: enabled)
   Active: active (running) since Wed 2019-10-09 10:03:40 CEST; 2s ago
     Docs: man:systemd-journald.service(8)
           man:journald.conf(5)
 Main PID: 1872 (systemd-journal)
   Status: "Processing requests..."
    Tasks: 1 (limit: 3558)
   Memory: 1.1M
   CGroup: /system.slice/systemd-journald.service
           └─1872 /lib/systemd/systemd-journald

Oct 09 10:03:40 debian10 systemd-journald[1872]: Journal started
Oct 09 10:03:40 debian10 systemd-journald[1872]: System journal (/var/log/journal/9a32ba45ce44423a97d6397918de1fa5) is 8.0M, max 1.2G, 1.2G free.
```
```
Note
The journal files in /var/log/journal/<machine-id>/ or /run/log/journal/<machine-id>/ have the .journal suffix (e.g. system.journal). However, if they are found to be corrupted or the daemon is stopped in an unclean fashion, they will be renamed appending ~ (e.g. system.journal~) and the daemon will start writing to a new, clean file.
```
### Deleting Old Journal Data: Journal Size
Logs are saved in journal files whose file names end with `.journal` or `.journal~` and are located in the appropriate directory (`/run/log/journal` or `/var/log/journal` as configured). To check how much disk space is currently being occupied by journal files (both archived and active), use the `--disk-usage` switch:
```
root@debian:~# journalctl --disk-usage
Archived and active journals take up 24.0M in the filesystem.
```
`systemd` logs default to a maximum of 10% of the size of the filesystem where they are stored. For instance on a 1GB filesystem they will not take up more than 100MB. Once this limit is reached, old logs will start to disappear to stay near this value.

However, size limit enforcement on stored journal files can be managed by tweaking a series of configuration options in `/etc/systemd/journald.conf`. These options fall into two categories depending on the filesystem type used: persistent (`/var/log/journal`) or in-memory (`/run/log/journal`). The former uses options that are prefixed with the word `System` and will only apply if persistent logging is properly enabled and once the system is fully booted up. The option names in the latter start with the word `Runtime` and will apply in the following scenarios:

`SystemMaxUse=`, `RuntimeMaxUse=`
- They control the amount of disk space that can be taken up by the journal. It defaults to 10% of the filesystem size but can be modified (e.g. `SystemMaxUse=500M`) as long as it does not surpass a maximum of 4GiB.

`SystemKeepFree=`, `RuntimeKeepFree=`
- They control the amount of disk space that should be left free for other users. It defaults to 15% of the filesystem size but can be modified (e.g. `SystemKeepFree=500M`) as long as it does not surpass a maximum of 4GiB.
- Regarding precedence of `*MaxUse` and `*KeepFree`, `systemd-journald` will satisfy both by using the smaller of the two values. Likewise, bear in mind that only archived (as opposed to active) journal files are deleted.

`SystemMaxFileSize=`, `RuntimeMaxFileSize=`
- They control the maximum size to which individual journal files can grow. The default is 1/8 of `*MaxUse`. Size reduction is carried out in a synchronous way and values can be specified in bytes or using `K`, `M`, `G`, `T`, `P`, `E` for Kibibytes, Mebibytes, Gibibyte, Tebibytes, Pebibytes and Exbibytes, respectively.

`SystemMaxFiles=`, `RuntimeMaxFiles=`
- They establish the maximum number of individual and archived journal files to store (active journal files are not affected). It defaults to 100.

Apart from size-based deletion and rotation of log messages, `systemd-journald` also allows for time-based criteria by using the following two options: `MaxRetentionSec=` and `MaxFileSec=`. Refer to the manual page of `journald.conf` for more information about these and other options.
```
Note
Whenever you modify the default behaviour of systemd-journald by uncommenting and editing options in /etc/systemd/journald.conf, you must restart the daemon for the changes to take effect.
```
### Vacuuming the Journal
You can manually clean archived journal files at any time with any of the following three options:

`--vacuum-time=`
- This time-based option will eliminate all messages in journal files with a timestamp older than the specified timeframe. Values must be written with any of the following suffixes: `s`, `m`, `h`, `days` (or `d`), `months`, `weeks` (or `w`) and `years` (or `y`). For instance, to get rid of all messages in archived journal files that are older than 1 month:
```
root@debian:~# journalctl --vacuum-time=1months
Deleted archived journal /var/log/journal/7203088f20394d9c8b252b64a0171e08/system@27dd08376f71405a91794e632ede97ed-0000000000000001-00059475764d46d6.journal (16.0M).
Deleted archived journal /var/log/journal/7203088f20394d9c8b252b64a0171e08/user-1000@e7020d80d3af42f0bc31592b39647e9c-000000000000008e-00059479df9677c8.journal (8.0M).
```
`--vacuum-size=`
- This size-based option will delete archived journal files until they occupy a value below the specified size. Values must be written with any of the following suffixes: `K`, `M`, `G` or `T`. For instance, to eliminate archived journal files until they are below 100 Mebibits:
```
root@debian:~# journalctl --vacuum-size=100M
Vacuuming done, freed 0B of archived journals from /run/log/journal/9a32ba45ce44423a97d6397918de1fa5.
```
`--vacuum-files=`
- This option will take care that no more archived journal files than the specified number remain. The value is an integer. For instance, to limit the number of archived journal files to 10:
```
root@debian:~# journalctl --vacuum-files=10
Vacuuming done, freed 0B of archived journals from /run/log/journal/9a32ba45ce44423a97d6397918de1fa5.
```
Vacuuming only removes archived journal files. If you want to get rid of everything (including active journal files), you need to use a signal (`SIGUSR2`) that requests immediate rotation of the journal files with the `--rotate` option. Other important signals can be invoked with the following options:

`--flush` (`SIGUSR1`)
- It requests flushing of journal files from `/run/` to `/var/` to make the journal persistent. It requires that persistent logging is enabled and `/var/` is mounted.

`--sync` (`SIGRTMIN+1)`
- It is used to request that all unwritten log data be written to disk.
```
Note
To check the internal consistency of the journal file, use journalctl with the --verify option. You will see a progress bar as the check is done and any possible issues will be shown.
```
### Retrieving Journal Data from a Rescue System
As a system administrator, you may find yourself in a situation where you need to access journal files on the hard drive of a faulty machine through a rescue system (a bootable CD or USB key containing a live Linux distribution).

`journalctl` looks for the journal files in `/var/log/journal/<machine-id>/`. Because the machine IDs on the rescue and faulty systems will be different, you must use the following option:

`-D </path/to/dir>, --directory=</path/to/dir>`
- With this option, we specify a directory path where `journalctl` will search for journal files instead of the default runtime and system locations.

Thus, it is necessary that you mount the faulty system’s `rootfs` (`/dev/sda1`) on the rescue system’s filesystem and proceed to read the journal files like so:
```
root@debian:~# journalctl -D /media/carol/faulty.system/var/log/journal/
-- Logs begin at Sun 2019-10-20 12:30:45 CEST, end at Sun 2019-10-20 12:32:57 CEST. --
oct 20 12:30:45 suse-server kernel: Linux version 4.12.14-lp151.28.16-default (geeko@buildhost) (...)
oct 20 12:30:45 suse-server kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.12.14-lp151.28.16-default root=UUID=7570f67f-4a08-448e-aa09-168769cb9289 splash=>
oct 20 12:30:45 suse-server kernel: x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
oct 20 12:30:45 suse-server kernel: x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
(...)
```
Other options that can be useful in this scenario are:

`-m`, `--merge`
- It merges entries from all available journals under `/var/log/journal`, including remote ones.

`--file`
- It will show the entries in a specific file, for example: `journalctl --file /var/log/journal/64319965bda04dfa81d3bc4e7919814a/user-1000.journal`.

`--root`
- A directory path meaning the root directory is passed as an argument. `journalctl` will search there for `journal files (e.g. journalctl --root /faulty.system/`).

See `journalctl` man page for more information.

### Forwarding Log Data to a Traditional `syslog` Daemon
Log data from the journal can be made available to a traditional `syslog` daemon by:
- Forwarding messages to the socket file `/run/systemd/journal/syslog` for `syslogd` to read. This facility is enabled with the `ForwardToSyslog=yes` option.
- Having a `syslog` daemon behaving like `journalctl`, therefore reading log messages directly from the journal files. In this case, the relevant option is that of `Storage`; it must have a value other than `none`.
```
Note
Likewise, you can forward log messages to other destinations with the following options: ForwardToKMsg (kernel log buffer — kmsg), ForwardToConsole (the system console) or ForwardToWall (all logged-in users via wall). For more information, consult the man page for journald.conf.
```

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)