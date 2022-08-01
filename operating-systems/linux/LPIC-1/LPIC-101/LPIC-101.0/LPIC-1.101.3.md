# LPIC-1: System Architecture

## Lesson 101.3: Change runlevels / boot targets and shutdown or reboot the system

A common feature among operating systems following Unix design principles is the employment of separate processes to control distinct functions of the system. These processes, called daemons (or, more generally, services), are also responsible for extended features underlying the operating system, like network application services (HTTP server, file sharing, email, etc.), databases, on-demand configuration, etc. Although Linux utilizes a monolithic kernel, many low level aspects of the operating system are affected by daemons, like load balancing and firewall configuration.

Which daemons should be active depends on the purpose of the system. The set of active daemons should also be modifiable at runtime, so services can be started and stopped without having to reboot the whole system. To tackle this issue, every major Linux distribution offers some form of service management utility to control the system.

Services can be controlled by shell scripts or by a program and its supporting configuration files. The first method is implemented by the SysVinit standard, also known as System V or just SysV. The second method is implemented by systemd and Upstart. Historically, SysV based service managers were the most used by Linux distributions. Today, systemd based service managers are more often found in most Linux distributions. The service manager is the first program launched by the kernel during the boot process, so its PID (process identification number) is always 1.

### SysVinit
A service manager based on the SysVinit standard will provide predefined sets of system states, called runlevels, and their corresponding service script files to be executed. Runlevels are numbered 0 to 6, being generally assigned to the following purposes:

`Runlevel 0`
- System shutdown.

`Runlevel 1, s or single`
- Single user mode, without network and other non-essential capabilities (maintenance mode).

`Runlevel 2, 3 or 4`
- Multi-user mode. Users can log in by console or network. Runlevels 2 and 4 are not often used.

`Runlevel 5`
- Multi-user mode. It is equivalent to 3, plus the graphical mode login.

`Runlevel 6`
- System restart.

The program responsible for managing runlevels and associated daemons/resources is `/sbin/init`. During system initialization, the `init` program identifies the requested runlevel, defined by a kernel parameter or in the `/etc/inittab` file, and loads the associated scripts listed there for the given runlevel. Every runlevel may have many associated service files, usually scripts in the `/etc/init.d/` directory. As not all runlevels are equivalent through different Linux distributions, a short description of the runlevel’s purpose can also be found in SysV based distributions.

The syntax of the `/etc/inittab` file uses this format:

```
id:runlevels:action:process
```

The `id` is a generic name up to four characters in length used to identify the entry. The `runlevels` entry is a list of runlevel numbers for which a specified action should be executed. The action term defines how `init` will execute the process indicated by the term `process`. The available actions are:

`boot`
- The process will be executed during system initialization. The field `runlevels` is ignored.

`bootwait`
- The process will be executed during system initialization and `init` will wait until it finishes to continue. The field `runlevels` is ignored.

`sysinit`
- The process will be executed after system initialization, regardless of runlevel. The field `runlevels` is ignored.

`wait`
- The process will be executed for the given runlevels and `init` will wait until it finishes to continue.

`respawn`
- The process will be restarted if it is terminated.

`ctrlaltdel`
- The process will be executed when the init process receives the `SIGINT` signal, triggered when the key sequence of `Ctrl`+`Alt`+`Del` is pressed.

The default runlevel — the one that will be chosen if no other is given as a kernel parameter — is also defined in `/etc/inittab`, in the entry `id:x:initdefault`. The `x` is the number of the default runlevel. This number should never be `0` or `6`, given that it would cause the system to shutdown or restart as soon as it finishes the boot process. A typical `/etc/inittab` file is shown below:

```
# Default runlevel
id:3:initdefault:

# Configuration script executed during boot
si::sysinit:/etc/init.d/rcS

# Action taken on runlevel S (single user)
~:S:wait:/sbin/sulogin

# Configuration for each execution level
l0:0:wait:/etc/init.d/rc 0
l1:1:wait:/etc/init.d/rc 1
l2:2:wait:/etc/init.d/rc 2
l3:3:wait:/etc/init.d/rc 3
l4:4:wait:/etc/init.d/rc 4
l5:5:wait:/etc/init.d/rc 5
l6:6:wait:/etc/init.d/rc 6

# Action taken upon ctrl+alt+del keystroke
ca::ctrlaltdel:/sbin/shutdown -r now

# Enable consoles for runlevels 2 and 3
1:23:respawn:/sbin/getty tty1 VC linux
2:23:respawn:/sbin/getty tty2 VC linux
3:23:respawn:/sbin/getty tty3 VC linux
4:23:respawn:/sbin/getty tty4 VC linux

# For runlevel 3, also enable serial
# terminals ttyS0 and ttyS1 (modem) consoles
S0:3:respawn:/sbin/getty -L 9600 ttyS0 vt320
S1:3:respawn:/sbin/mgetty -x0 -D ttyS1
```

The `telinit q` command should be executed every time after the `/etc/inittab` file is modified. The argument `q` (or `Q`) tells init to reload its configuration. Such a step is important to avoid a system halt due to an incorrect configuration in `/etc/inittab`.

The scripts used by `init` to setup each runlevel are stored in the directory `/etc/init.d/`. Every runlevel has an associated directory in `/etc/`, named `/etc/rc0.d/`, `/etc/rc1.d/`, `/etc/rc2.d/`, etc., with the scripts that should be executed when the corresponding runlevel starts. As the same script can be used by different runlevels, the files in those directories are just symbolic links to the actual scripts in `/etc/init.d/`. Furthermore, the first letter of the link filename in the runlevel’s directory indicates if the service should be started or terminated for the corresponding runlevel. A link’s filename starting with letter `K` determines that the service will be killed when entering the runlevel (kill). Starting with letter `S`, the service will be started when entering the runlevel (start). The directory `/etc/rc1.d/`, for example, will have many links to network scripts beginning with letter `K`, considering that the runlevel 1 is the single user runlevel, without network connectivity.

The command `runlevel` shows the current runlevel for the system. The `runlevel` command shows two values, the first is the previous runlevel and the second is the current runlevel:

```
$ runlevel
N 3
```

The letter `N` in the output shows that the runlevel has not changed since last boot. In the example, the `runlevel 3` is the current runlevel of the system.

The same `init` program can be used to alternate between runlevels in a running system, without the need to reboot. The command `telinit` can also be used to alternate between runlevels. For example, commands `telinit 1`, `telinit s` or `telinit S` will change the system to runlevel 1.

 ### systemd
Currently, systemd is the most widely used set of tools to manage system resources and services, which are referred to as units by systemd. A unit consists of a name, a type and a corresponding configuration file. For example, the unit for a httpd server process (like the Apache web server) will be `httpd.service` on Red Hat based distributions and its configuration file will also be called `httpd.service` (on Debian based distributions this unit is named `apache2.service`).

There are seven distinct types of systemd units:

`service`
- The most common unit type, for active system resources that can be initiated, interrupted and reloaded.

`socket`
- The socket unit type can be a filesystem socket or a network socket. All socket units have a corresponding service unit, loaded when the socket receives a request.

`device`
- A device unit is associated with a hardware device identified by the kernel. A device will only be taken as a systemd unit if a udev rule for this purpose exists. A device unit can be used to resolve configuration dependencies when certain hardware is detected, given that properties from the udev rule can be used as parameters for the device unit.

`mount`
- A mount unit is a mount point definition in the filesystem, similar to an entry in /etc/fstab.

`automount`
- An automount unit is also a mount point definition in the filesystem, but mounted automatically. Every automount unit has a corresponding mount unit, which is initiated when the automount mount point is accessed.

`target`
- A target unit is a grouping of other units, managed as a single unit.

`snapshot`
- A snapshot unit is a saved state of the systemd manager (not available on every Linux distribution).

The main command for controlling systemd units is `systemctl`. Command `systemctl` is used to execute all tasks regarding unit activation, deactivation, execution, interruption, monitoring, etc. For a fictitious unit called `unit.service`, for example, the most common `systemctl` actions will be:

`systemctl start unit.service`
- Starts `unit`.

`systemctl stop unit.service`
- Stops `unit`.

`systemctl restart unit.service`
- Restarts `unit`.

`systemctl status unit.service`
= Shows the state of `unit`, including if it is running or not.

`systemctl is-active unit.service`
- Shows active if unit is running or inactive otherwise.

`systemctl enable unit.service`
- Enables unit, that is, unit will load during system initialization.

`systemctl disable unit.service`
- unit will not start with the system.

`systemctl is-enabled unit.service`
- Verifies if unit starts with the system. The answer is stored in the variable $?. The value 0 indicates that unit starts with the system and the value 1 indicates that unit does not starts with the system.

```
Note
Newer installations of systemd will actually list a unit’s configuration for boot time. For example:

$ systemctl is-enabled apparmor.service
enabled
```

If no other units with the same name exist in the system, then the suffix after the dot can be dropped. If, for example, there is only one `httpd` unit of type service, then only `httpd` is enough as the unit parameter for `systemctl`.

The `systemctl` command can also control system targets. The `multi-user.target` unit, for example, combines all units required by the multi-user system environment. It is similar to the runlevel number 3 in a system utilizing SysV.

Command `systemctl isolate` alternates between different targets. So, to manually alternate to target `multi-user`:

```
# systemctl isolate multi-user.target
```

There are corresponding targets to SysV runlevels, starting with `runlevelO.target` up to `runlevel6.target`. However, systemd does not use the `/etc/inittab` file. To change the default system target, the option `systemd.unit` can be added to the kernel parameters list. For example, to use `multi-user.target` as the standard target, the kernel parameter should be `systemd.unit=multi-user.target`. All kernel parameters can be made persistent by changing the bootloader configuration.

Another way to change the default target is to modify the symbolic link `/etc/systemd/system/default.target` so it points to the desired target. The redefinition of the link can be done with the `systemctl` command by itself:

```
# systemctl set-default multi-user.target
```

Likewise, you can determine what your system’s default boot target is with the following command:

```
$ systemctl get-default
graphical.target
```

Similar to systems adopting SysV, the default target should never point to `shutdown.target`, as it corresponds to the runlevel 0 (shutdown).

The configuration files associated with every unit can be found in the `/lib/systemd/system/` directory. The command `systemctl list-unit-files` lists all available units and shows if they are enabled to start when the system boots. The option `--type` will select only the units for a given type, as in `systemctl list-unit-files --type=service` and `systemctl list-unit-files --type=target`.

Active units or units that have been active during the current system session can be listed with command `systemctl list-units`. Like the `list-unit-files` option, the `systemctl list-units --type=service` command will select only units of type service and command `systemctl list-units --type=target` will select only units of type `target`.

systemd is also responsible for triggering and responding to power related events. The `systemctl suspend` command will put the system in low power mode, keeping current data in memory. Command `systemctl hibernate` will copy all memory data to disk, so the current state of the system can be recovered after powering it off. The actions associated with such events are defined in the file `/etc/systemd/logind.conf` or in separate files inside the directory `/etc/systemd/logind.conf.d/`. However, this systemd feature can only be used when there is no other power manager running in the system, like the `acpid` daemon. The `acpid` daemon is the main power manager for Linux and allows finer adjustments to the actions following power related events, like closing the laptop lid, low battery or battery charging levels.

### Upstart
The initialization scripts used by Upstart are located in the directory `/etc/init/`. System services can be listed with command `initctl list`, which also shows the current state of the services and, if available, their PID number.

```
# initctl list
avahi-cups-reload stop/waiting
avahi-daemon start/running, process 1123
mountall-net stop/waiting
mountnfs-bootclean.sh start/running
nmbd start/running, process 3085
passwd stop/waiting
rc stop/waiting
rsyslog start/running, process 1095
tty4 start/running, process 1761
udev start/running, process 1073
upstart-udev-bridge start/running, process 1066
console-setup stop/waiting
irqbalance start/running, process 1842
plymouth-log stop/waiting
smbd start/running, process 1457
tty5 start/running, process 1764
failsafe stop/waiting
```

Every Upstart action has its own independent command. For example, command `start` can be used to initiate a sixth virtual terminal:

```
# start tty6
The current state of a resource can be verified with command status:
```

```
# status tty6
tty6 start/running, process 3282
```

And the interruption of a service is done with the command `stop`:

```
# stop tty6
```


Upstart does not use the `/etc/inittab` file to define runlevels, but the legacy commands `runlevel` and `telinit` can still be used to verify and alternate between runlevels.

```
Note
Upstart was developed for the Ubuntu Linux distribution to help facilitate parallel startup of processes. Ubuntu has stopped using Upstart since 2015 when it switched from Upstart to systemd.
```

### Shutdown and Restart
A very traditional command used to shutdown or restart the system is unsurprisingly called `shutdown`. The shutdown command adds extra functions to the power off process: it automatically notifies all logged-in users with a warning message in their shell sessions and new logins are prevented. Command `shutdown` acts as an intermediary to SysV or systemd procedures, that is, it executes the requested action by calling the corresponding action in the services manager adopted by the system.

After `shutdown` is executed, all processes receive the `SIGTERM` signal, followed by the `SIGKILL` signal, then the system shuts down or changes its runlevel. By default, when neither options `-h` or `-r` are used, the system alternates to runlevel 1, that is, the single user mode. To change the default options for `shutdown`, the command should be executed with the following syntax:

```
$ shutdown [option] time [message]
```

Only the parameter time is required. The time parameter defines when the requested action will be executed, accepting the following formats:

`hh:mm`
- This format specifies the execution time as hour and minutes.

`+m`
- This format specifies how many minutes to wait before execution.

`now or +0`
- This format determines immediate execution.

The `message` parameter is the warning text sent to all terminal sessions of logged-in users.

The SysV implementation allows for the limiting of users that will be able to restart the machine by pressing `Ctrl`+`Alt`+`Del`. This is possible by placing option `-a` for the `shutdown` command present at the line regarding `ctrlaltdel` in the `/etc/inittab` file. By doing this, only users whose usernames are in the `/etc/shutdown.allow` file will be able to restart the system with the `Ctrl`+`Alt`+`Del` keystroke combination.

The `systemctl` command can also be used to turn off or to restart the machine in systems employing systemd. To restart the system, the command `systemctl reboot` should be used. To turn off the system, the command `systemctl poweroff` should be used. Both commands require root privileges to run, as ordinary users can not perform such procedures.

```
Note
Some Linux distributions will link poweroff and reboot to systemctl as individual commands. For example:

$ sudo which poweroff
/usr/sbin/poweroff
$ sudo ls -l /usr/sbin/poweroff
lrwxrwxrwx 1 root root 14 Aug 20 07:50 /usr/sbin/poweroff -> /bin/systemctl
```

Not all maintenance activities require the system to be turned off or restarted. However, when it is necessary to change the system’s state to single-user mode, it is important to warn logged-in users so that they are not harmed by an abrupt termination of their activities.

Similar to what the `shutdown` command does when powering off or restarting the system, the `wall` command is able to send a message to terminal sessions of all logged-in users. To do so, the system administrator only needs to provide a file or directly write the message as a parameter to command `wall`.


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)











