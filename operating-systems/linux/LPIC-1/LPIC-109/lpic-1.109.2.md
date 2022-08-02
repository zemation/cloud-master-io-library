# LPIC-1.109: Networking Fundamentals

## Lesson 109.2 Fundamentals of internet protocols

In any TCP/IP network, every node must configure its network adapter to match the network requirements, otherwise they will not be able to communicate with each other. Therefore, the system administrator must provide the basic configuration so the operating system will be able to setup the appropriate network interface, as well as to identify itself and the basic features of the network every time it boots.

Network settings are agnostic in regard to operating systems, but the latter have their own methods to store and apply these settings. Linux systems rely on configurations stored in plain text files under the /etc directory to bring up network connectivity during boot time. It is worth knowing how these files are used to avoid connectivity loss due to local misconfiguration.

### The Network Interface
Network interface is the term by which the operating system refers to the communication channel configured to work with the network hardware attached to the system, such as an ethernet or wi-fi device. The exception to this is the loopback interface, which the operating system uses when it needs to establish a connection with itself, but the main purpose of a network interface is to provide a route through which local data can be sent and remote data can be received. Unless the network interface is properly configured, the operating system will not be able to communicate with other machines in the network.

For most cases, the correct interface settings are either defined by default or customized during the installation of the operating system. Nevertheless, these settings often need to be inspected or even modified when the communication isn’t working properly or when the interface’s behavior requires customization.

There are many Linux commands to list which network interfaces are present on the system, but not all of them are available in all distributions. Command `ip`, however, is part of the basic set of networking tools bundled with all Linux distributions and can be used to list the network interfaces. The complete command to show the interfaces is `ip link show`:
```
$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp3s5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
        link/ether 00:16:3e:8d:2b:5b brd ff:ff:ff:ff:ff:ff
```
If available, command `nmcli device` can also be used:
```
$ nmcli device
DEVICE      TYPE      STATE      CONNECTION
enp3s5      ethernet  connected  Gigabit Powerline Adapter
lo          loopback  unmanaged  --
```
The commands shown in the examples do not modify any settings in the system, so they can be executed by an unprivileged user. Both commands list two network interfaces: `lo` (the loopback interface) and `enp3s5` (an ethernet interface).

Desktops and laptops running Linux usually have two or three predefined network interfaces, one for the loopback virtual interface and the others assigned to the network hardware found by the system. Servers and network appliances running Linux, on the other hand, may have tens of network interfaces, but the same principles apply to all of them. The abstraction provided by the operating system allows for the setup of network interfaces using the same methods, regardless of the underlying hardware.

However, knowing the details about the underlying hardware of an interface can be useful to better understand what is going on when the communication is not working as expected. In a system where many network interfaces are available, it could not be obvious which one corresponds to the wi-fi and which one corresponds to the ethernet, for example. For this reason, Linux uses an interface naming convention that helps identify which network interface corresponds to which device and port.

### Interface Names
Older Linux distributions named ethernet network interfaces as `eth0`, `eth1`, etc., numbered according to the order in which the kernel identifies the devices. The wireless interfaces were named `wlan0`, `wlan1`, etc. This naming convention, however, does not clarify which specific ethernet port matches with the interface `eth0`, for example. Depending on how the hardware was detected, it was even possible for two network interfaces to swap names after a reboot.

To overcome this ambiguity, more recent Linux systems employ a predictable naming convention for network interfaces, making up a closer relationship between the interface name and the underlying hardware connection.

In Linux distributions that use the systemd naming scheme, all interface names start with a two-character prefix that signifies the interface type:

`en`
- Ethernet

`ib`
- InfiniBand

`sl`
- Serial line IP (slip)

`wl`
- Wireless local area network (WLAN)

`ww`
- Wireless wide area network (WWAN)

From higher to lower priority, the following rules are used by the operating system to name and number the network interfaces:

1. Name the interface after the index provided by the BIOS or by the firmware of embedded devices, e.g. `eno1`.
2. Name the interface after the PCI express slot index, as given by the BIOS or firmware, e.g. `ens1`.
3. Name the interface after its address at the corresponding bus, e.g. `enp3s5`.
4. Name the interface after the interface’s MAC address, e.g. `enx78e7d1ea46da`.
5. Name the interface using the legacy convention, e.g. `eth0`.

It is correct to assume, for example, that the network interface `enp3s5` was so named because it did not fit the first two naming methods, so its address in the corresponding bus and slot was used instead. The device address `03:05.0`, found in the output of the `lspci` command, reveals the associate device:
```
$ lspci | fgrep Ethernet
03:05.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL-8110SC/8169SC Gigabit Ethernet (rev 10)
```
Network interfaces are created by the Linux kernel itself, but there are many commands that can be used to interact with them. Normally, the configuration happens automatically and there is no need to change the settings manually. Nonetheless, with the name of the interface, it is possible to tell the kernel how to proceed in configuring it if necessary.

### Interface Management
Over the years, several programs have been developed to interact with the networking features provided by the Linux kernel. Although the old `ifconfig` command can still be used to do simple interface configurations and queries, it is now deprecated due to its limited support of non-ethernet interfaces. The `ifconfig` command was superseded by the command `ip`, which is capable of managing many other aspects of TCP/IP interfaces, like routes and tunnels.

The many capabilities of the `ip` command can be overkill for most ordinary tasks, so there are auxiliary commands to facilitate the activation and configuration of the network interfaces. Commands `ifup` and `ifdown` may be used to configure network interfaces based on interface definitions found in the file `/etc/network/interfaces`. Although they can be invoked manually, these commands are normally executed automatically during system boot.

All network interfaces managed by `ifup` and `ifdown` should be listed in the `/etc/network/interfaces` file. The format used in the file is straightforward: lines beginning with the word `auto` are used to identify the physical interfaces to be brought up when `ifup` is executed with the `-a` option. The interface name should follow the word auto on the same line. All interfaces marked `auto` are brought up at boot time, in the order they are listed.
```
Warning
Network configuration methods used by ifup and ifdown are not standardized throughout all Linux distributions. CentOS, for example, keeps the interface settings in individual files in the /etc/sysconfig/network-scripts/ directory and the configuration format used in them is slightly different from the format used in /etc/network/interfaces.
```

The actual interface configuration is written in another line, starting with the word `iface`, followed by the interface name, the name of the address family that the interface uses and the name of the method used to configure the interface. The following example shows a basic configuration file for interfaces `lo` (loopback) and `enp3s5`:
```
auto lo
iface lo inet loopback

auto enp3s5
iface enp3s5 inet dhcp
```
The address family should be `inet` for TCP/IP networking, but there is also support for IPX networking (`ipx`), and IPv6 networking (`inet6`). Loopback interfaces use the `loopback` configuration method. With the `dhcp` method, the interface will use the IP settings provided by the network’s DHCP server. The settings from the example configuration allow the execution of command `ifup` using interface name `enp3s5` as its argument:
```
# ifup enp3s5
Internet Systems Consortium DHCP Client 4.4.1
Copyright 2004-2018 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/enp3s5/00:16:3e:8d:2b:5b
Sending on   LPF/enp3s5/00:16:3e:8d:2b:5b
Sending on   Socket/fallback
DHCPDISCOVER on enp3s5 to 255.255.255.255 port 67 interval 4
DHCPOFFER of 10.90.170.158 from 10.90.170.1
DHCPREQUEST for 10.90.170.158 on enp3s5 to 255.255.255.255 port 67
DHCPACK of 10.90.170.158 from 10.90.170.1
bound to 10.90.170.158 -- renewal in 1616 seconds.
```
In this example, the method chosen for the `enp3s5` interface was `dhcp`, so the command `ifup` called a DHCP client program to obtain the IP settings from the DHCP server. Likewise, command `ifdown` `enp3s5` can be used to turn the interface off.

In networks without a DHCP server, the `static` method could be used instead and the IP settings provided manually in `/etc/network/interfaces`. For example:
```
iface enp3s5 inet static
    address 192.168.1.2/24
    gateway 192.168.1.1
```
Interfaces using the `static` method do not need a corresponding `auto` directive, as they are brought up whenever the network hardware is detected.

If the same interface has more than one `iface` entry, then all of the configured addresses and options will be applied when bringing up that interface. This is useful to configure both IPv4 and IPv6 addresses on the same interface, as well as to configure multiple addresses of the same type on a single interface.

### Local and Remote Names
A working TCP/IP setup is just the first step towards full network usability. In addition to being able to identify nodes on the network by their IP numbers, the system must be able to identify them with names more easily understood by human beings.

The name by which the system identifies itself is customizable and it is good practice to define it, even if the machine is not intended to join a network. The local name often matches the network name of the machine, but this isn’t necessarily always true. If the file `/etc/hostname` exists, the operating system will use the contents of the first line as its local name, thereafter simply called the hostname. Lines starting with # inside `/etc/hostname` are ignored.

The `/etc/hostname` file can be edited directly, but the machine’s hostname can also be defined with the `hostnamectl` command. When supplied with sub-command `set-hostname`, command `hostnamectl` will take the name given as an argument and write it in `/etc/hostname`:
```
# hostnamectl set-hostname storage
# cat /etc/hostname
storage
```
The hostname defined in `/etc/hostname` is the static hostname, that is, the name which is used to initialize the system’s hostname at boot. The static hostname may be a free-form string up to 64 characters in length. However, it is recommended that it consists only of ASCII lower-case characters and no spaces or dots. It should also limit itself to the format allowed for DNS domain name labels, even though this is not a strict requirement.

Command `hostnamectl` can set two other types of hostnames in addition to the static hostname:

Pretty hostname
> Unlike the static hostname, the pretty hostname may include all kinds of special characters. It can be used to set a more descriptive name for the machine, e.g. “LAN Shared Storage”:
```
# hostnamectl --pretty set-hostname "LAN Shared Storage"
```
Transient hostname
- Used when the static hostname is not set or when it is the default `localhost` name. The transient hostname is normally the name set together with other automatic configurations, but it can also be modified by the command `hostnamectl`, e.g.
```
# hostnamectl --transient set-hostname generic-host
```
If neither the `--pretty` nor `--transient` option is used, then all three hostname types will be set to the given name. To set the static hostname, but not the pretty and transient names, the option `--static` should be used instead. In all cases, only the static hostname is stored in the `/etc/hostname` file. Command `hostnamectl` can also be used to display various descriptive and identity bits of information about the running system:
```
$ hostnamectl status
     Static hostname: storage
     Pretty hostname: LAN Shared Storage
  Transient hostname: generic-host
           Icon name: computer-server
             Chassis: server
          Machine ID: d91962a957f749bbaf16da3c9c86e093
             Boot ID: 8c11dcab9c3d4f5aa53f4f4e8fdc6318
    Operating System: Debian GNU/Linux 10 (buster)
              Kernel: Linux 4.19.0-8-amd64
        Architecture: x86-64
```
This is the default action of the `hostnamectl` command, so the `status` sub-command can be omitted.

Regarding the name of the remote network nodes, there are two basic ways the operating system can implement to match names and IP numbers: to use a local source or to use a remote server to translate names into IP numbers and vice versa. The methods can be complementary to each other and their priority order is defined in the Name Service Switch configuration file: `/etc/nsswitch.conf`. This file is used by the system and applications to determine not only the sources for name-IP matches, but also the sources from which to obtain name-service information in a range of categories, called databases.

The hosts database keeps track of the mapping between host names and host numbers. The line inside` /etc/nsswitch.conf` beginning with `hosts` defines the services accountable for providing the associations for it:
```
hosts: files dns
```
In this example entry, `files` and `dns` are the service names that specify how the lookup process for host names will work. First, the system will look for matches in local files, then it will ask the DNS service for matches.

The local file for the hosts database is `/etc/hosts`, a simple text file that associates IP addresses with hostnames, one line per IP address, e.g.:
```
127.0.0.1 localhost
```
The IP number 127.0.0.1 is the default address for the loopback interface, hence its association with the localhost name.

It is also possible to bind optional aliases to the same IP. Aliases can provide alternate spellings, shorter hostnames and should be added at the end of the line, for example:
```
192.168.1.10 foo.mydomain.org foo
```
The formatting rules for the `/etc/hosts` file are:
- Fields of the entry are separated by any number of blanks and/or tab characters.
- Text from a`#` character until the end of the line is a comment and is ignored.
- Host names may contain only alphanumeric characters, minus signs and periods.
- Host names must begin with an alphabetic character and end with an alphanumeric character.

IPv6 addresses may also be added to `/etc/hosts`. The following entry refers to the IPv6 loopback address:
```
::1 localhost ip6-localhost ip6-loopback
```
Following the `files` service specification, the `dns` specification tells the system to ask a DNS service for the desired name/IP association. The set of routines responsible for this method is called the resolver and its configuration file is `/etc/resolv.conf`. The following example shows a generic `/etc/resolv.conf` containing entries for Google’s public DNS servers:
```
nameserver 8.8.4.4
nameserver 8.8.8.8
```
As shown in the example, the `nameserver` keyword indicates the IP address of the DNS server. Only one nameserver is required, but up to three nameservers can be given. The supplementary ones will be used as a fallback. If no nameserver entries are present, the default behaviour is to use the name server on the local machine.

The resolver can be configured to automatically add the domain to names before consulting them on the name server. For example:
```
nameserver 8.8.4.4
nameserver 8.8.8.8
domain mydomain.org
search mydomain.net mydomain.com
```
The `domain` entry sets `mydomain.org` as the local domain name, so queries for names within this domain will be allowed to use short names relative to the local domain. The `search` entry has a similar purpose, but it accepts a list of domains to try when a short name is provided. By default, it contains only the local domain name.
___

Linux supports virtually every network technology used to connect servers, containers, virtual machines, desktops and mobile devices. The connections between all these network nodes can be dynamic and heterogeneous, thus requiring appropriate management by the operating system running in them.

In the past, distributions developed their own customized solutions for managing dynamic network infrastructure. Today, tools like NetworkManager and systemd provide more comprehensive and integrated features to meet all the specific demands.

### NetworkManager
Most Linux distributions adopt the NetworkManager service daemon to configure and control the system’s network connections. NetworkManager’s purpose is to make the network configuration as simple and automatic as possible. When using DHCP, for example, NetworkManager arranges route changes, IP address fetching and updates to the local list of DNS servers, if necessary. When both wired and wireless connections are available, NetworkManager prioritizes the wired connection by default. NetworkManager will try to keep at least one connection active all the time, whenever it is possible.
```
Note
A request using DHCP (Dynamic Host Configuration Protocol) is usually sent through the network adapter as soon as the link to the network is established. The DHCP server that is active on the network then responds with the settings (IP address, network mask, default route, etc.) which the requester must use to communicate via IP protocol.
```
By default, the NetworkManager daemon controls the network interfaces not mentioned in the `/etc/network/interfaces` file. It does so to not interfere with other configuration methods that may be present as well, thus modifying the unattended interfaces only.

The NetworkManager service runs in the background with root privileges and triggers the necessary actions to keep the system online. Ordinary users can create and modify network connections with client applications that, albeit not having root privileges themselves, are capable of communicating with the underlying service in order to perform the requested actions.

Client applications for NetworkManager are available for both the command line and the graphical environment. For the latter, the client application comes as an accessory of the desktop environment (under names like, nm-tray, network-manager-gnome, nm-applet or plasma-nm) and it is usually accessible through an indicator icon at the corner of the desktop bar or from the system configuration utility.

In the command line, NetworkManager itself provides two client programs: `nmcli` and `nmtui`. Both programs have the same basic features, but `nmtui` has a curses-based interface while `nmcli` is a more comprehensive command that can also be used in scripts. Command `nmcli` separates all network related properties controlled by NetworkManager in categories called objects:

`general`
- NetworkManager’s general status and operations.

`networking`
- Overall networking control.

`radio`
- NetworkManager radio switches.

`connection`
- NetworkManager’s connections.

`device`
- Devices managed by NetworkManager.

`agent`
- NetworkManager secret agent or polkit agent.

`monitor`
- Monitor NetworkManager changes.

The object name is the main argument to command `nmcli`. To show the overall connectivity status of the system, for example, the object `general` should be given as the argument:
```
$ nmcli general
STATE      CONNECTIVITY  WIFI-HW  WIFI     WWAN-HW  WWAN
connected  full          enabled  enabled  enabled  enabled
```
Column `STATE` tells whether the system is connected to a network or not. If the connection is limited due to external misconfiguration or access restrictions, then the `CONNECTIVITY` column will not report the `full` connectivity status. If `Portal` appears in the `CONNECTIVITY` column, it means that extra authentication steps (usually through the web browser) are required to complete the connection process. The remaining columns report the status of the wireless connections (if any), either `WIFI` or `WWAN` (Wide Wireless Area Network, i.e. cellular networks). The `HW` suffix indicates that the status corresponds to the network device rather than the system network connection, that is, it tells if the hardware is enabled or disabled to save power.

In addition to the object argument, `nmcli` also needs a command argument to execute. The `status` command is used by default if no command argument is present, so the command `nmcli general` is actually interpreted as `nmcli general status`.

It is hardly necessary to take any action when the network adapter is connected directly to the access point through cables, but wireless networks require further interaction to accept new members. `nmcli` facilitates the connection process and saves the settings to connect automatically in the future, hence it is very helpful for laptops or any other mobile appliances.

Before connecting to wi-fi, it is convenient to first list the available networks in the local area. If the system has a working wi-fi adapter, then the `device` object will use it to scan the available networks with command `nmcli device wifi list`:
```
$ nmcli device wifi list
IN-USE  BSSID              SSID        MODE   CHAN  RATE        SIGNAL  BARS  SECURITY
        90:F6:52:C5:FA:12  Hypnotoad   Infra  11    130 Mbit/s  67      ▂▄▆_  WPA2
        10:72:23:C7:27:AC  Jumbao      Infra  1     130 Mbit/s  55      ▂▄__  WPA2
        00:1F:33:33:E9:BE  NETGEAR     Infra  1     54 Mbit/s   35      ▂▄__  WPA1 WPA2
        A4:33:D7:85:6D:B0  AP53        Infra  11    130 Mbit/s  32      ▂▄__  WPA1 WPA2
        98:1E:19:1D:CC:3A  Bruma       Infra  1     195 Mbit/s  22      ▂___  WPA1 WPA2
```
Most users will probably use the name in the `SSID` column to identify the network of interest. For example, command `nmcli` can connect to the network named `Hypnotoad` using the `device` object again:
```
$ nmcli device wifi connect Hypnotoad
```
If the command is executed inside a terminal emulator in the graphical environment, then a dialog box will appear asking for the network’s passphrase. When executed in a text only console, the password may be provided together with the other arguments:
```
$ nmcli device wifi connect Hypnotoad password MyPassword
```
If the wi-fi network hides its `SSID` name, `nmcli` can still connect to it with the extra `hidden yes` arguments:
```
$ nmcli device wifi connect Hypnotoad password MyPassword hidden yes
```
If the system has more than one wi-fi adapter, the one to be used may be indicated with `ifname`. For example, to connect using the adapter named `wlo1`:
```
$ nmcli device wifi connect Hypnotoad password MyPassword ifname wlo1
```
After the connection succeeds, NetworkManager will name it after the corresponding SSID (if it is a wi-fi connection) and will keep it for future connections. The connections names and their UUIDs are listed by command `nmcli connection show`:
```
$ nmcli connection show
NAME               UUID                                  TYPE      DEVICE
Ethernet           53440255-567e-300d-9922-b28f0786f56e  ethernet  enp3s5
tun0               cae685e1-b0c4-405a-8ece-6d424e1fb5f8  tun       tun0
Hypnotoad          6fdec048-bcc5-490a-832b-da83d8cb7915  wifi      wlo1
4G                 a2cf4460-0cb7-42e3-8df3-ccb927f2fd88  gsm       --
```
The type of each connection is shown — which can be `ethernet`, `wifi`, `tun`, `gsm`, `bridge`, etc. — as well as the device to which they are associated with. To perform actions on a specific connection, its name or UUID must be supplied. To deactivate the `Hypnotoad` connection, for example:
```
$ nmcli connection down Hypnotoad
Connection 'Hypnotoad' successfully deactivated
```
Likewise, the command `nmcli connection up Hypnotoad `can be used to bring the connection up, as it is now saved by NetworkManager. The interface name can also be used to reconnect, but in this case the `device` object should be used instead:
```
$ nmcli device disconnect wlo2
Device 'wlo1' successfully disconnected.
```
The interface name can also be used to reestablish the connection:
```
$ nmcli device connect wlo2
Device 'wlo1' successfully activated with '833692de-377e-4f91-a3dc-d9a2b1fcf6cb'.
```
Note that the connection UUID changes every time the connection is brought up, so it is preferable to use its name for consistency.

If the wireless adapter is available but it is not being used, then it can be turned off to save power. This time, the object radio should be passed to `nmcli`:
```
$ nmcli radio wifi off
```
Of course, the wireless device can be turned on again with command `nmcli radio wifi` on.

Once the connections are established no manual interaction will be required in the future, as NetworkManager identifies available known networks and automatically connects to them. If necessary, NetworkManager has plugins that can extend its functionalities, like the plugin to support VPN connections.

### systemd-networkd
Systems running systemd can optionally use its built-in daemons to manage network connectivity: `systemd-networkd` to control network interfaces and `systemd-resolved` to manage the local name resolution. These services are backwards compatible with legacy Linux configuration methods, but the configuration of network interfaces in particular has features that are worth knowing.

The configuration files used by systemd-networkd to setup network interfaces can be found in any of the following three directories:

`/lib/systemd/network`
- The system network directory.

`/run/systemd/network`
- The volatile runtime network directory.

`/etc/systemd/network`
- The local administration network directory.

The files are processed in lexicographic order, so it is recommended to start their names with numbers to make the ordering easier to read and set.

Files in `/etc` have the highest priority, whilst files in `/run` take precedence over files with the same name in `/lib`. This means that if configuration files in different directories have the same name, then systemd-networkd will ignore the files with lesser priority. Separating files like that is a way to change the interface settings without having to modify the original files: modifications can be placed in `/etc/systemd/network` to override those in `/lib/systemd/network`.

The purpose of each configuration file depends on its suffix. File names ending in `.netdev` are used by systemd-networkd to create virtual network devices, such as bridge or tun devices. Files ending in `.link` set low-level configurations for the corresponding network interface. systemd-networkd detects and configures network devices automatically as they appear — as well as ignore devices already configured by other means — so there is little need to add these files in most situations.

The most important suffix is .network. Files using this suffix can be used to setup network addresses and routes. As with the other configuration file types, the name of the file defines the order in which the file will be processed. The network interface to which the configuration file refers to is defined in the `[Match]`` section inside the file.

For example, the ethernet network interface `enp3s5` can be selected within the file `/etc/systemd/network/30-lan`.network by using the `Name=enp3s5` entry in the `[Match]` section:
```
[Match]
Name=enp3s5
```
A list of whitespace-separated names is also accepted to match many network interfaces with this same file at once. The names can contain shell-style globs, like `en*`. Other entries provide various matching rules, like selecting a network device by its MAC address:
```
[Match]
MACAddress=00:16:3e:8d:2b:5b
```
The settings for the device are in the `[Network]` section of the file. A simple static network configuration only requires the `Address` and `Gateway` entries:
```
[Match]
MACAddress=00:16:3e:8d:2b:5b

[Network]
Address=192.168.0.100/24
Gateway=192.168.0.1
```
To use the DHCP protocol instead of static IP addresses, the `DHCP` entry should be used instead:
```
[Match]
MACAddress=00:16:3e:8d:2b:5b

[Network]
DHCP=yes
```
The systemd-networkd service will try to fetch both IPv4 and IPv6 addresses for the network interface. To use IPv4 only, `DHCP=ipv4` should be used. Likewise, `DHCP=ipv6` will ignore IPv4 settings and use the provided IPv6 address only.

Password-protected wireless networks can also be configured by systemd-networkd, but the network adapter must be already authenticated in the network before systemd-networkd can configure it. Authentication is performed by WPA supplicant, a program dedicated to configure network adapters for password protected networks.

The first step is to create the credentials file with command `wpa_passphrase`:
```
# wpa_passphrase MyWifi > /etc/wpa_supplicant/wpa_supplicant-wlo1.conf
```
This command will take the passphrase for the `MyWifi` wireless network from the standard input and store its hash in the `/etc/wpa_supplicant/wpa_supplicant-wlo1.conf`. Note that the filename should contain the appropriate name of the wireless interface, hence the `wlo1` in the file name.

The systemd manager reads the WPA passphrase files in `/etc/wpa_supplicant/` and creates the corresponding service to run WPA supplicant and bring the interface up. The passphrase file created in the example will then have a corresponding service unit called `wpa_supplicant@wlo1.service`. Command `systemctl start wpa_supplicant@wlo1.service will associate the wireless adapter with the remote access point. Command systemctl enable wpa_supplicant@wlo1.service` makes the association automatic during boot time.

Finally, a `.network` file matching the `wlo1` interface must be present in `/etc/systemd/network/`, as systemd-networkd will use it to configure the interface as soon as WPA supplicant finishes the association with the access point.





___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)