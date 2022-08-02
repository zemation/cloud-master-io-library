# LPIC-1.108: Essential System Services

## Lesson 108.1 Maintain system time

Accurate timekeeping is absolutely crucial for modern computing. The implementation of keeping time, however, is surprisingly complex. The practice of keeping time seems trivial to an end-user, but the system needs to be able to handle many idiosyncrasies and edge cases intelligently. Consider that time zones are not static, but may be changed by an administrative or political decision. A country may choose to stop observing Daylight Savings Time. Any program must be able to handle those changes logically. Fortunately for system administrators, the solutions for timekeeping on the Linux operating system are mature, robust and usually work without much interference.

When a Linux computer boots up, it starts keeping time. We refer to this as a system clock, since it is updated by the operating system. In addition, modern computers will also have a hardware or real time clock. This hardware clock is often a feature of the motherboard and keeps time regardless if the computer is running or not. During boot, the system time is set from the hardware clock, but for the most part these two clocks run independently of each other. In this lesson we will be discussing methods of interacting with both the system and hardware clocks.

On most modern Linux systems, system time and hardware time are synchronised to network time, which is implemented by the Network Time Protocol (NTP). In the vast majority of cases, the only configuration a normal user will be required to do is to set their time zone and NTP will take care of the rest. However, we will cover some ways of working with time manually and the specifics of configuring network time will be covered in the next lesson.

### Local Versus Universal Time
The system clock is set to Coordinated Universal Time (UTC), which is the local time at Greenwich, United Kingdom. Usually a user wants to know their local time. Local time is calculated by taking UTC time and applying an offset based on time zone and Daylight Savings. In this way, a lot of complexity can be avoided.

The system clock can be set to either UTC time or local time, but it is recommended that it also be set to UTC time.

### Date
`date` is a core utility which simply prints local time:
```
$ date
Sun Nov 17 12:55:06 EST 2019
```
Modifying the options of the `date` command will change the format of the output.

For example, a user can use `date -u` to view the current UTC time.
```
$ date -u
Sun Nov 17 18:02:51 UTC 2019
```
Some other commonly-used options will return the local time in a format which adheres to an accepted RFC format:

`-I`
- Date/time in ISO 8601 format. Appending `date` (`-Idate`) will limit the output to date only. Other formats are `hours`, `minutes`, `seconds` and `ns` for nanoseconds.

`-R`
- Returns date and time in RFC 5322 format.

`--rfc-3339`
- Returns date and time in RFC 3339 format.

The format of `date` can be customized by the user with sequences specified in the man page. For example, the current time can be formatted as Unix time thusly:
```
$ date +%s
1574014515
```
From `date` 's man page we can see that `%s` refers to Unix time.

Unix time is used internally on most Unix-like systems. It stores UTC time as the number of seconds since Epoch, which has been defined as January 1st, 1970.
```
Note
The number of bits required to store Unix time at the present moment is 32 bits. There is a future issue when 32 bits will become insufficient to contain the current time in Unix format. This will cause serious issues for any 32-bit Linux systems. Fortunately, this will not occur until January 19, 2038.
```
Using these sequences, we are able to format date and time in almost any format required by any application. Of course, in most cases it is far preferable to stick with an accepted standard.

Additionally, `date --date` can be used to format a time that is not the current time. In this scenario, a user can specify the date to be applied to the system using Unix time for example:
```
$ date --date='@1564013011'
Wed Jul 24 20:03:31 EDT 2019
```
Using the `--debug` option can be very useful for ensuring that a date can be successfully parsed. Observe what happens when passing a valid date to the command:
```
$ date --debug --date="Fri, 03 Jan 2020 14:00:17 -0500"
date: parsed day part: Fri (day ordinal=0 number=5)
date: parsed date part: (Y-M-D) 2020-01-03
date: parsed time part: 14:00:17 UTC-05
date: input timezone: parsed date/time string (-05)
date: using specified time as starting value: '14:00:17'
date: warning: day (Fri) ignored when explicit dates are given
date: starting date/time: '(Y-M-D) 2020-01-03 14:00:17 TZ=-05'
date: '(Y-M-D) 2020-01-03 14:00:17 TZ=-05' = 1578078017 epoch-seconds
date: timezone: system default
date: final: 1578078017.000000000 (epoch-seconds)
date: final: (Y-M-D) 2020-01-03 19:00:17 (UTC)
date: final: (Y-M-D) 2020-01-03 14:00:17 (UTC-05)
```

This can be a handy tool when troubleshooting an application that generates a date.

### Hardware Clock
A user may run the `hwclock` command to view the time as maintained on the real-time clock. This command will require elevated privileges, so we will use `sudo` to call the command in this case:
```
$ sudo hwclock
2019-11-20 11:31:29.217627-05:00
```
Using the option `--verbose` will return more output which might be useful for troubleshooting:
```
$ sudo hwclock --verbose
hwclock from util-linux 2.34
System Time: 1578079387.976029
Trying to open: /dev/rtc0
Using the rtc interface to the clock.
Assuming hardware clock is kept in UTC time.
Waiting for clock tick...
...got clock tick
Time read from Hardware Clock: 2020/01/03 19:23:08
Hw clock time : 2020/01/03 19:23:08 = 1578079388 seconds since 1969
Time since last adjustment is 1578079388 seconds
Calculated Hardware Clock drift is 0.000000 seconds
2020-01-03 14:23:07.948436-05:00
```
Note the `Calculated Hardware Clock drift`. This output can tell you if system time and hardware time are deviating from one another.

### timedatectl
`timedatectl` is a command that can be used to check the general status of time and date, including whether or not network time has been synchronised (Network Time Protocol will be covered in the next lesson).

By default `timedatectl` returns information similar to `date`, but with the addition of the RTC (hardware) time as well as the status of the NTP service:
```
$ timedatectl
               Local time: Thu 2019-12-05 11:08:05 EST
           Universal time: Thu 2019-12-05 16:08:05 UTC
                 RTC time: Thu 2019-12-05 16:08:05
                Time zone: America/Toronto (EST, -0500)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```
### Setting Time Using timedatectl
If NTP is unavailable, it is recommended to use `timedatectl` rather than `date` or `hwclock` to set time:
```
# timedatectl set-time '2011-11-25 14:00:00'
```
The process is similar to that of `date`. The user can also set time independent of date using the format HH:MM:SS.

### Setting Timezone Using timedatectl
`timedatectl` is the preferred way of setting the local time zone on `systemd` based Linux systems when no GUI exists. `timedatectl` will list possible time zones and then the time zone can be set using one of these as an argument.

First we will list possible timezones:
```
$ timedatectl list-timezones
Africa/Abidjan
Africa/Accra
Africa/Algiers
Africa/Bissau
Africa/Cairo
...
```
The list of possible time zones is long, so the use of the `grep` command is recommended in this case.

Next we can set the timezone using one of the elements of the list that was returned:
```
$ timedatectl set-timezone Africa/Cairo
$ timedatectl
               Local time: Thu 2019-12-05 18:18:10 EET
           Universal time: Thu 2019-12-05 16:18:10 UTC
                 RTC time: Thu 2019-12-05 16:18:10
                Time zone: Africa/Cairo (EET, +0200)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```
Keep in mind that the name of the time zone must be exact. `Africa/Cairo` for example will change the time zone, but `cairo` or `africa/cairo` will not.

### Disabling NTP Using timedatectl
In some cases it might be necessary to disable NTP. This could be done using `systemctl` but we will demonstrate this using `timedatectl`:
```
# timedatectl set-ntp no
$ timedatectl
             Local time: Thu 2019-12-05 18:19:04 EET Universal time: Thu 2019-12-05 16:19:04 UTC
               RTC time: Thu 2019-12-05 16:19:04
              Time zone: Africa/Cairo (EET, +0200)
            NTP enabled: no
       NTP synchronized: no
        RTC in local TZ: no
             DST active: n/a
``` 
### Setting Time Zone Without timedatectl
Setting time zone information is a standard step when installing Linux on a new machine. If there is a graphical installation process, this will most likely be handled without any further user input.

The `/usr/share/zoneinfo` directory contains information for the different time zones that are possible. In the `zoneinfo` directory, there are subdirectories that contain the names of continents as well as other symbolic links. It is recommended to find your region’s `zoneinfo` starting from your continent.

`zoneinfo` files contain rules required to calculate the local time offset in relation to UTC, and they also are important if your region observes Daylight Savings Time. The contents of `/etc/localtime` will be read when Linux needs to determine the local time zone. In order to set the time zone without the use of a GUI, the user should create a symbolic link for their location from `/usr/share/zoneinfo` to `/etc/localtime`. For example:
```
$ ln -s /usr/share/zoneinfo/Canada/Eastern /etc/localtime
```
After setting the correct time zone, it is often recommended to run:
```
# hwclock --systohc
```
This will set the hardware clock from the system clock (that is, the real-time clock will be set to the same time as `date`). Please note that this command is run with root privileges, in this case by being logged in as root.

`/etc/timezone` is similar to `/etc/localtime`. It is a data representation of the local time zone, and as such it can be read using `cat`:
```
$ cat /etc/timezone
America/Toronto
```
Note that this file is not used by all Linux distributions.

### Setting Date and Time Without timedatectl
```
Note
Most modern Linux systems use systemd for its configuration and services and as such it is not recommended that you use date or hwclock for setting time. systemd uses timedatectl for this. Nonetheless it is important to know these legacy commands in the event that you must administer an older system.
```
### Using date
`date` has an option to set the system time. Use `--set` or `-s` to set the date and time. You may also choose to use `--debug` to verify the correct parsing of the command:
```
# date --set="11 Nov 2011 11:11:11"
```
Note that root privileges are required to set the date here. We may also choose to change to time or date independently:
```
# date +%Y%m%d -s "20111125"
```
Here we must specify the sequences so that our string is parsed properly. For example `%Y` refers to the year, and so the first four digits `2011` will be interpreted as the year 2011. Similarly, `%T` is the sequence for time, and it is demonstrated here by setting time:
```
# date +%T -s "13:11:00"
```
After changing system time, it is recommended to also set the hardware clock so that both system and hardware clocks are synchronised:
```
# hwclock --systohc
```
`systohc` means “system clock to hardware clock”.

### Using hwclock
Rather than setting the system clock and updating the hardware clock, you may choose to reverse the process. We will start by setting the hardware clock:
```
# hwclock --set --date "4/12/2019 11:15:19"
# hwclock
Fri 12 Apr 2019 6:15:19 AM EST -0.562862 seconds
```
Notice that by default `hwclock` is expecting UTC time, but returns the local time by default.

After setting the hardware clock, we will need to update the system clock from it. `hctosys` can be understood to mean “hardware clock to system clock”.
```
# hwclock --hctosys
```

___

While personal computers are able to keep reasonably accurate time on their own, production computing and network environments requires that very precise time be kept. The most accurate time is measured by reference clocks, which are typically atomic clocks. The modern world has devised a system where all internet-connected computer systems can be synchronised to these reference clocks using what is known as the Network Time Protocol (NTP). A computer system with NTP will be able to synchronise their system clocks to the time provided by reference clocks. If system time and the time as measured on these servers are different, then the computer will speed up or slow down its internal system time incrementally until system time matches network time.

NTP uses a hierarchical structure to disseminate time. Reference clocks are connected to servers at the top of the hierarchy. These servers are Stratum 1 machines and typically are not accessible to the public. Stratum 1 machines are however accessible to Stratum 2 machines, which are accessible to Stratum 3 machines and so on. Stratum 2 servers are accessible to the public, as are any machines lower in the hierarchy. When setting up NTP for a large network, it is good practice to have a small number of computers connect to Stratum 2+ servers, and then have those machines provide NTP to all other machines. In this way, demands on Stratum 2 machines can be minimized.

There are some important terms that come up when discussing NTP. Some of these terms are used in the commands we will use to track the status of NTP on our machines:

Offset
- This refers to the absolute difference between system time and NTP time. For example, if the system clock reads 12:00:02 and NTP time reads 11:59:58, then the offset between the two clocks is four seconds.

Step
- If the time offset between the NTP provider and a consumer is greater than 128ms, then NTP will perform a single significant change to system time, as opposed to slowing or speeding the system time. This is called stepping.

Slew
- Slewing refers to the changes made to system time when the offset between system time and NTP is less than 128ms. If this is the case, then changes will be made gradually. This is referred to as slewing.

Insane Time
- If the offset between system time and NTP time is greater than 17 minutes, then the system time is considered insane and the NTP daemon will not introduce any changes to system time. Special steps will have to be taken to bring system time within 17 minutes of proper time.

Drift
- Drift refers to the phenomenon where two clocks become out of sync over time. Essentially if two clocks are initially synchronised but then become out of sync over time, then clock drift is occurring.

Jitter
- Jitter refers to the amount of drift since the last time a clock was queried. So if the last NTP sync occurred 17 minutes ago, and the offset between the NTP provider and consumer is 3 milliseconds, then 3 milliseconds is the jitter.

Now we will discuss some of the specific ways that Linux implements NTP.

### timedatectl
If your Linux distribution uses `timedatectl`, then by default it implements an SNTP client rather than a full NTP implementation. This is a less complex implementation of network time and means that your machine will not serve NTP to other connected computers.

In this case, SNTP will not work unless the `timesyncd` service is running. As with all systemd services, we can verify that it is running with:
```
$ systemctl status systemd-timesyncd
● systemd-timesyncd.service - Network Time Synchronization
   Loaded: loaded (/lib/systemd/system/systemd-timesyncd.service; enabled; vendor preset: enabled)
  Drop-In: /lib/systemd/system/systemd-timesyncd.service.d
           └─disable-with-time-daemon.conf
   Active: active (running) since Thu 2020-01-09 21:01:50 EST; 2 weeks 1 days ago
     Docs: man:systemd-timesyncd.service(8)
 Main PID: 1032 (systemd-timesyn)
   Status: "Synchronized to time server for the first time 91.189.89.198:123 (ntp.ubuntu.com)."
    Tasks: 2 (limit: 4915)
   Memory: 3.0M
   CGroup: /system.slice/systemd-timesyncd.service
           └─1032 /lib/systemd/systemd-timesyncd

Jan 11 13:06:18 NeoMex systemd-timesyncd[1032]: Synchronized to time server for the first time 91.189.91.157:123 (ntp.ubuntu.com).
...
```
The status of `timedatectl` SNTP synchronisation can be verified using `show-timesync`:
```
$ timedatectl show-timesync --all
LinkNTPServers=
SystemNTPServers=
FallbackNTPServers=ntp.ubuntu.com
ServerName=ntp.ubuntu.com
ServerAddress=91.189.89.198
RootDistanceMaxUSec=5s
PollIntervalMinUSec=32s
PollIntervalMaxUSec=34min 8s
PollIntervalUSec=34min 8s
NTPMessage={ Leap=0, Version=4, Mode=4, Stratum=2, Precision=-23, RootDelay=8.270ms, RootDispersion=18.432ms, Reference=91EECB0E, OriginateTimestamp=Sat 2020-01-25 18:35:49 EST, ReceiveTimestamp=Sat 2020-01-25 18:35:49 EST, TransmitTimestamp=Sat 2020-01-25 18:35:49 EST, DestinationTimestamp=Sat 2020-01-25 18:35:49 EST, Ignored=no PacketCount=263, Jitter=2.751ms }
Frequency=-211336
```
This configuration might be adequate for most situations, but as noted before it will be insufficient if one is hoping to synchronise several clients in a network. In this case it is recommended to install a full NTP client.

### NTP Daemon
The system time is compared to network time on a regular schedule. For this to work we must have a daemon running in the background. For many Linux systems, the name of this daemon is `ntpd`. `ntpd` will allow a machine to not only be a time consumer (that is, able to sync its own clock from an outside source), but also to provide time to other machines.

Let us assume that our computer is systemd-based and it uses `systemctl` to control daemons. We will install `ntp` packages using the appropriate package manager and then ensure that our ntpd daemon is running by checking its status:
```
$ systemctl status ntpd

●  ntpd.service - Network Time Service
   Loaded: loaded (/usr/lib/systemd/system/ntpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2019-12-06 03:27:21 EST; 7h ago
  Process: 856 ExecStart=/usr/sbin/ntpd -u ntp:ntp $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 867 (ntpd)
   CGroup: /system.slice/ntpd.service
           `-867 /usr/sbin/ntpd -u ntp:ntp -g
```
In some cases it might be required to both start and enable `ntpd`. On most Linux machines this is accomplished with:
```
# systemctl enable ntpd && systemctl start ntpd
```
NTP queries happen on TCP Port 123. If NTP fails, ensure that this port is open and listening.

### NTP Configuration
NTP is able to poll several sources and to select the best candidates to use for setting system time. If a network connection is lost, NTP uses previous adjustments from its history to estimate future adjustments.

Depending on your distribution of Linux, the list of network time servers will be stored in different places. Let us assume that `ntp` is installed on your machine.

The file `/etc/ntp.conf` contains configuration information about how your system synchronises with network time. This file can be read and modified using `vi` or `nano`.

By default, the NTP servers used will be specified in a section like this:
```
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst
```
The syntax for adding NTP servers looks like this:
```
server (IP Address)
server server.url.localhost
```
Server addresses can be IP addresses or URLs if DNS has been properly configured. In this case, the server will always be queried.

A network administrator might also consider using (or setting up) a pool. In this case, we assume that there are several NTP providers, all running NTP daemons and with the same time. When a client queries a pool, a provider is selected at random. This helps to distribute network load among many machines so that no one machine in the pool is handling all NTP queries.

Commonly, `/etc/ntp.conf` will be populated with a server pool called `pool.ntp.org`. So for example, `server 0.centos.pool.ntp.org` is a default NTP pool provided to CentOS machines.

`pool.ntp.org`
The NTP servers used by default are an open source project. More information can be found at ntppool.org.

> Consider if the NTP Pool is appropriate for your use. If business, organization or human life depends on having correct time or can be harmed by it being wrong, you shouldn’t “just get it off the internet”. The NTP Pool is generally very high quality, but it is a service run by volunteers in their spare time. Please talk to your equipment and service vendors about getting local and reliable service setup for you. See also our terms of service. We recommend time servers from Meinberg, but you can also find time servers from End Run, Spectracom and many others.
> — ntppool.org

### ntpdate
During initial setup, system time and NTP might be seriously de-synchronised. If the offset between system and NTP time is greater than 17 minutes, then the NTP daemon will not make changes to system time. In this scenario manual intervention will be required.

Firstly, if `ntpd` is running it will be necessary to stop the service. Use `systemctl stop ntpd` to do so.

Next, use `ntpdate pool.ntp.org` to perform an initial one-time synchronisation, where pool.ntp.org refers to the IP address or URL of an NTP server. More than one sync may be required.

`ntpq`
`ntpq` is a utility for monitoring the status of NTP. Once the NTP daemon has been started and configured, `ntpq` can be used to check on its status:
```
$ ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
+37.44.185.42    91.189.94.4      3 u   86  128  377  126.509  -20.398   6.838
+ntp2.0x00.lv    193.204.114.233  2 u   82  128  377  143.885   -8.105   8.478
*inspektor-vlan1 121.131.112.137  2 u   17  128  377  112.878  -23.619   7.959
 b1-66er.matrix. 18.26.4.105      2 u  484  128   10   34.907   -0.811  16.123
```
In this case `-p` is for print and it will print a summary of peers. Host addresses can also be returned as IP addresses using `-n`.

`remote`
- hostname of the NTP provider.

`refid`
- Reference ID of the NTP provider.

`st`
- Stratum of the provider.

`when`
- Number of seconds since the last query.

`poll`
Number of seconds between queries.

`reach`
- Status ID to indicate whether a server was reached. Successful connections will increase this number by 1.

`delay`
- Time in ms between query and response by the server.

`offset`
- Time in ms between system time and NTP time.

`jitter`
- Offset in ms between system time and NTP in the last query.

`ntpq` also has an interactive mode, which is accessed when it is run without options or argument. The `?` option will return a list of commands that `ntpq` will recognize.

### chrony
`chrony` is yet another way to implement NTP. It is installed by default on some Linux systems, but is available to download on all major distributions. `chronyd` is the chrony daemon, and `chronyc` is the command line interface. It may be required to start and enable `chronyd` before interacting with `chronyc`.

If the chrony installation has a default configuration then using the command `chronyc tracking` will provide information about NTP and system time:
```
$ chronyc tracking
Reference ID    : 3265FB3D (bras-vprn-toroon2638w-lp130-11-50-101-251-61.dsl.)
Stratum         : 3
Ref time (UTC)  : Thu Jan 09 19:18:35 2020
System time     : 0.000134029 seconds fast of NTP time
Last offset     : +0.000166506 seconds
RMS offset      : 0.000470712 seconds
Frequency       : 919.818 ppm slow
Residual freq   : +0.078 ppm
Skew            : 0.555 ppm
Root delay      : 0.006151616 seconds
Root dispersion : 0.010947504 seconds
Update interval : 129.8 seconds
Leap status     : Normal
```
This output contains a lot of information, more than what is available from other implementations.

`Reference ID`
- The reference ID and name to which the computer is currently synced.

`Stratum`
- Number of hops to a computer with an attached reference clock.

`Ref time`
- This is the UTC time at which the last measurement from the reference source was made.

`System time`
- Delay of system clock from synchronized server.

`Last offset`
- Estimated offset of the last clock update.

`RMS offset`
- Long term average of the offset value.

`Frequency`
- This is the rate by which the system’s clock would be wrong if chronyd is not correcting it. It is provided in ppm (parts per million).

`Residual freq`
- Residual frequency indicating the difference between the measurements from reference source and the frequency currently being used.

`Skew`
- Estimated error bound of the frequency.

`Root delay`
- Total of the network path delays to the stratum computer, from which the computer is being synced.

`Leap status`
- This is the leap status which can have one of the following values – normal, insert second, delete second or not synchronized.

We can also look at detailed information about the last valid NTP update:
```
# chrony ntpdata
Remote address  : 172.105.97.111 (AC69616F)
Remote port     : 123
Local address   : 192.168.122.81 (C0A87A51)
Leap status     : Normal
Version         : 4
Mode            : Server
Stratum         : 2
Poll interval   : 6 (64 seconds)
Precision       : -25 (0.000000030 seconds)
Root delay      : 0.000381 seconds
Root dispersion : 0.000092 seconds
Reference ID    : 61B7CE58 ()
Reference time  : Mon Jan 13 21:50:03 2020
Offset          : +0.000491960 seconds
Peer delay      : 0.004312567 seconds
Peer dispersion : 0.000000068 seconds
Response time   : 0.000037078 seconds
Jitter asymmetry: +0.00
NTP tests       : 111 111 1111
Interleaved     : No
Authenticated   : No
TX timestamping : Daemon
RX timestamping : Kernel
Total TX        : 15
Total RX        : 15
Total valid RX  : 15
```
Finally, `chronyc sources` will return information about the NTP servers that are used to synchronise time:
```
$ chronyc sources
210 Number of sources = 0
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
```
At the moment, this machine has no sources configured. We can add sources from `pool.ntp.org` by opening the chrony configuration file. This will usually be located at `/etc/chrony.conf`. When we open this file, we should see that some servers are listed by default:
```
210 Number of sources = 0
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
# Most computers using chrony will send measurement requests to one or
# more 'NTP servers'.  You will probably find that your Internet Service
# Provider or company have one or more NTP servers that you can specify.
# Failing that, there are a lot of public NTP servers.  There is a list
# you can access at http://support.ntp.org/bin/view/Servers/WebHome or
# you can use servers from the 3.arch.pool.ntp.org project.

! server 0.arch.pool.ntp.org iburst iburst
! server 1.arch.pool.ntp.org iburst iburst
! server 2.arch.pool.ntp.org iburst iburst

! pool 3.arch.pool.ntp.org iburst
```
These servers will also serve as a syntax guide when entering our own servers. However, in this case we will simply remove the `!` s at the beginning of each line, thus uncommenting out these lines and using the default servers from the `pool.ntp.org` project.

In addition, in this file we can choose to change the default configuration regarding skew and drift as well as the location of the driftfile and keyfile.

On this machine, we need to make a large initial clock correction. We will choose to uncomment the following line:
```
! makestep 1.0 3
```
After making changes to the configuration file, restart the `chronyd` service and then use `chronyc makestep` to manually step the system clock:
```
# chronyc makestep
200 OK
```
And then use `chronyc tracking` as before to verify that the changes have taken place.


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)