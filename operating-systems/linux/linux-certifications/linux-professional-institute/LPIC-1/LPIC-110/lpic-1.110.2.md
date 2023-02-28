
# LPIC-1.110: Security

## Lesson 110.2 Setup host security

This chapter explains four basic ways to improve host security:
1. Some basic commands and configuration settings to improve authentication security with shadow passwords.
2. How to use superdaemons to listen for incoming network connections.
3. Checking network services for unnecessary daemons.
4. TCP wrappers as sort of a simple firewall.

### Improve Authentication Security with Shadow Passwords
The basic components of a user’s account data are stored in the file `/etc/passwd`. This file contains seven fields: login name, userid, groupid, password, comment (aka GECOS), home directory location and finally the default shell. A simple way to remember the order of these fields is to think about the process of a user logging in: first you enter a login name, secondly the system will map this into a userid (uid) and thirdly into a groupid (gid). The fourth step asks for a password, the fifth looks up the comment, the sixth enters the users home directory and the seventh step sets the default shell.

Although in modern systems the password is no longer stored in the file `/etc/passwd`. Instead the password field contains a lower-case `x` only. The file `/etc/passwd` has to be readable by all users. Therefore it is not a good idea to store passwords there. The `x` indicates the encrypted (hashed) password is actually stored in the `/etc/shadow` file instead. This file must not be readable to all users.

Password settings are configured with the commands `passwd` and `chage`. Both commands will change the entry for user `emma` in the file `/etc/shadow`. As a superuser you can set the password for user emma with the following command:
```
$ sudo passwd emma
New password:
Retype new password:
passwd: password updated successfully
```
You will then be prompted twice to confirm the new password.

To list the password expiration time and other password expiration settings for user `emma` use:
```
$ sudo chage -l emma
Last password change					: Apr 27, 2020
Password expires					: never
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 99999
Number of days of warning before password expires	: 7
```
To prevent the user `emma` from logging into the system the superuser may set a password expiration date that precedes the current date. For example, if today’s date were 2020-03-27, you could expire the user’s password by using an older date:
```
$ sudo chage -E 2020-03-26 emma
```
Alternatively, the superuser may use:
```
$ sudo passwd -l emma
```
to lock the account temporarily via the `-l` option for `passwd`. To test the effects of these changes, attempt to login as the `emma` account:
```
$ sudo login emma
Password:
Your account has expired; please contact your system administrator

Authentication failure
```
To prevent all users except the root user from logging into the system temporarily, the superuser may create a file named `/etc/nologin`. This file may contain a message to the users notifying them as to why they can not login (for example, system maintenance notifications). For details see `man 5 nologin`. Note there is also a command `nologin` which can be used to prevent a login when set as the default shell for a user. For example:
```
$ sudo usermod -s /sbin/nologin emma
```
See `man 8 nologin` for more details.

### How to Use a Superdaemon to Listen for Incoming Network Connections
Network services such as web servers, email servers and print servers usually run as a standalone service listening on a dedicated network port. All of these standalone services are running side-by-side. On classic Sys-V-init based system each of these services can be controlled by the command `service`. On current systemd based systems you would use `systemctl` to manage the service.

In former times the availability of computer resources had been much smaller. To run many services in standalone mode in tandem was not a good option. Instead a so-called superdaemon had been used to listen for incoming network connections and start the appropriate service on demand. This method of building a network connection took a little more time. Well known superdaemons are `inetd` and `xinetd`. On current systems based on systemd the systemd.socket unit can be used in a similar way. In this section we will use `xinetd` to intercept connections to the `sshd` daemon and start this daemon on request to demonstrate how the superdaemon was used.

Before configuring the `xinetd` service some preparation is necessary. It does not matter whether you use a Debian or Red Hat based system. Though these explanations have been tested with Debian/GNU Linux 9.9 they should work on any current Linux system featuring systemd, without any significant changes. First make sure the packages `openssh-server` and `xinetd` are installed. Now verify that the SSH service works with:
```
$ systemctl status sshd
● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2020-04-27 09:33:48 EDT; 3h 11min ago
     Docs: man:sshd(8)
           man:sshd_config(5)
  Process: 430 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
 Main PID: 460 (sshd)
    Tasks: 1 (limit: 1119)
   Memory: 5.3M
   CGroup: /system.slice/ssh.service
           └─460 /usr/sbin/sshd -D
```
Also check that the SSH service is listening on its standard network port of 22:
```
# lsof -i :22
COMMAND  PID USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
sshd    1194 root    3u  IPv4 16053268      0t0  TCP *:ssh (LISTEN)
sshd    1194 root    4u  IPv6 16053270      0t0  TCP *:ssh (LISTEN)
Finally stop the SSH service with:

$ sudo systemctl stop sshd.service
```
In the case that you would want to make this change permanent and to survive reboot use `systemctl disable sshd.service`.

Now you can create the xinetd configuration file `/etc/xinetd.d/ssh` with some basic settings:
```
service ssh
{
	disable		= no
	socket_type	= stream
	protocol	= tcp
	wait		= no
	user		= root
	server		= /usr/sbin/sshd
	server_args 	= -i
	flags		= IPv4
	interface	= 192.168.178.1
}
```
Restart the xinetd service with:
```
$ sudo systemctl restart xinetd.service
```
Check which service is listening now for incoming SSH connections.
```
$ sudo lsof -i :22
COMMAND   PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
xinetd  24098 root    5u  IPv4 7345141      0t0  TCP 192.168.178.1:ssh (LISTEN)
```
We can see that the xinetd service has taken over control for the access of port 22.

Here are some more details about the `xinetd` configuration. The main configuration file is `/etc/xinetd.conf`:
```
# Simple configuration file for xinetd
#
# Some defaults, and include /etc/xinetd.d/

defaults
{

# Please note that you need a log_type line to be able to use log_on_success
# and log_on_failure. The default is the following :
# log_type = SYSLOG daemon info

}

includedir /etc/xinetd.d
```
Besides the default settings, there is only one directive to set an include directory. In this directory you can set up a single configuration file for each service you want to have handled by `xinetd`. We have done this above for the SSH service and named the file `/etc/xinetd.d/ssh`. The names of the configuration files can be chosen arbitrarily, except filenames containing a dot (`.`) or ending with a tilde (`~`). But it is widespread practice to name the file after the service you want to configure.

Some configuration files in the `/etc/xinet.d/` directory are provided by the distribution already:
```
$ ls -l /etc/xinetd.d
total 52
-rw-r--r-- 1 root root 640 Feb  5  2018 chargen
-rw-r--r-- 1 root root 313 Feb  5  2018 chargen-udp
-rw-r--r-- 1 root root 502 Apr 11 10:18 daytime
-rw-r--r-- 1 root root 313 Feb  5  2018 daytime-udp
-rw-r--r-- 1 root root 391 Feb  5  2018 discard
-rw-r--r-- 1 root root 312 Feb  5  2018 discard-udp
-rw-r--r-- 1 root root 422 Feb  5  2018 echo
-rw-r--r-- 1 root root 304 Feb  5  2018 echo-udp
-rw-r--r-- 1 root root 312 Feb  5  2018 servers
-rw-r--r-- 1 root root 314 Feb  5  2018 services
-rw-r--r-- 1 root root 569 Feb  5  2018 time
-rw-r--r-- 1 root root 313 Feb  5  2018 time-udp
```
These files can be used as templates in the rare case you have to use some legacy services such as `daytime`, a very early implementation of a time server. All of these template file contain the directive `disable = yes`.

Here are some more details about the directives used in the `/etc/xinetd.d/ssh` example file for ssh above.
```
service ssh
{
	disable		= no
	socket_type	= stream
	protocol	= tcp
	wait		= no
	user		= root
	server		= /usr/sbin/sshd
	server_args 	= -i
	flags		= IPv4
	interface	= 192.168.178.1
}
```
`service`
- Lists the service xinetd has to control. You may use either a port number, such as 22, or the name mapped to the port number in `/etc/services` e.g. `ssh`.

`{`
- Detailed settings begin with an opening curly bracket.

`disable`
- To activate these settings, set this to `no`. If you want to disable the setting temporarily you may set it to `yes`.

`socket_type`
- You can choose `stream` for TCP sockets or `dgram` for UDP sockets and more.

`protocol`
- Choose either TCP or UDP.

`wait`
- For TCP connections this is set to `no` usually.

`user`
- The service started in this line will be owned by this user.

`server`
- Full path to the service which should be started by `xinetd`.

`server_args`
- You can add options for the service here. If started by a super-server many services require a special option. For SSH this would be the `-i` option.

`flags`
- You may choose IPv4, IPv6 and others.

`interface`
- The network interface which `xinetd` has to control. Note: you may also choose the `bind` directive, which is just a synonym for `interface`.

`}`
- Finish with a closing curly bracket.

The successors of services started by the super-server `xinetd` are systemd socket units. Setting up a systemd socket unit is very straight forward and easy, because there is a predefined systemd socket unit for SSH available already. Make sure that the `xinetd` and SSH services are not running.

Now you just have to start the SSH socket unit:
```
$ sudo systemctl start ssh.socket
```
To check which service is now listening on port 22 we use `lsof` again. Note here the option `-P` has been used to show the port number instead of the service name in the output:
```
$ sudo lsof -i :22 -P
COMMAND PID USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
systemd   1 root   57u  IPv6 14730112      0t0  TCP *:22 (LISTEN)
```
To make this session complete, you should try to login into your server with an SSH client of your choice.
```
Tip
In case systemctl start ssh.socket does not work with your distribution, try systemctl start sshd.socket.
```
### Checking Services for Unnecessary Daemons
For security reasons, as well as to control system resources, it is important to have an overview of what services are running. Unnecessary and unused services should be disabled. For example in case you do not need to distribute web pages there is no need to run a web server such as Apache or nginx.

On SyS-V-init based systems you may check the status of all services with the following:
```
$ sudo service --status-all
```
Verify whether each of the services listed from the output of the command are necessary and disable all unnecessary services with (for Debian-based systems):
```
$ sudo update-rc.d SERVICE-NAME remove
```
Or on Red Hat-based systems you would use:
```
$ sudo chkconfig SERVICE-NAME off
```
On modern systemd based systems we can use the following to list all running services:
```
$ systemctl list-units --state active --type service
```
You would then disable each unnecessary service unit with:
```
$ sudo systemctl disable UNIT --now
```
This command will stop the service and remove it from the list of services, so as to prevent it from starting up at next system boot.

Additionally to get a survey of listening network services you may use `netstat` on older systems (provided that you have the `net-tools` package installed):
```
$ netstat -ltu
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
tcp        0      0 localhost:mysql         0.0.0.0:*               LISTEN
tcp6       0      0 [::]:http               [::]:*                  LISTEN
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN
udp        0      0 0.0.0.0:bootpc          0.0.0.0:*
```
Or on modern systems you could use the equivalent command ss (for “socket services”):
```
$ ss -ltu
Netid      State         Recv-Q        Send-Q      Local Address:Port           Peer Address:Port
udp        UNCONN        0             0                 0.0.0.0:bootpc              0.0.0.0:*
tcp        LISTEN        0             128               0.0.0.0:ssh                 0.0.0.0:*
tcp        LISTEN        0             80              127.0.0.1:mysql               0.0.0.0:*
tcp        LISTEN        0             128                     *:http                      *:*
tcp        LISTEN        0             128                  [::]:ssh                    [::]:*
```
### TCP Wrappers as Sort of a Simple Firewall

In times when no firewalls were available for Linux, TCP wrappers had been used to secure network connections into a host. Nowadays many programs do not obey TCP wrappers anymore. In recent Red Hat (e.g. Fedora 29) based distributions TCP wrappers support has been removed completely. In order to support legacy Linux systems that still use TCP wrappers, it is helpful to have some basic knowledge about this particular technology.

We will once again use the SSH service as a basic example. The service on our example host should be reachable from the local network only. First we check whether the SSH daemon uses the `libwrap` library which offers TCP wrappers support:
```
$ ldd /usr/sbin/sshd | grep "libwrap"
        libwrap.so.0 => /lib/x86_64-linux-gnu/libwrap.so.0 (0x00007f91dbec0000)
```
Now we add the following line in the file `/etc/hosts.deny`:
```
sshd: ALL
```
Finally we configure an exception in the file `/etc/hosts.allow` for SSH connections from the local network:
```
sshd: LOCAL
```
The changes take effect immediately, there is no need to restart any service. You may check this with the `ssh` client.


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)