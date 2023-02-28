# LPIC-1.109: Networking Fundamentals

## Lesson 109.3 Basic network troubleshooting
Linux has very flexible and powerful network capabilities. In fact, Linux based operating systems are often used on common network devices, including expensive commercial equipment. Linux networking could be a certification in itself. With this mind, this lesson is only going to cover a few basic configuration and troubleshooting tools.

Be sure to review the lessons on internet protocols and persistent network configuration prior to this lesson. Within this lesson, we will be covering tools to configure and troubleshoot IPv4 and IPv6 networking.

While not an official objective, packet sniffers such as `tcpdump` are useful troubleshooting tools. Packet sniffers allow you to view and record packets coming into or out of a network interface. Tools such as hex viewers and protocol analyzers can be used to view these packets in more detail than a packet sniffer will typically allow. It wouldn’t hurt to at least be aware of such programs.

### About the `ip` Command
The ip command is a fairly recent utility used to view and configure just about anything relating to network configurations. This lesson covers some of the most used subcommands of ip, but it barely scratches the surface of what is available. Learning to read the documentation will help you be much more efficient with it.

Each subcommand of `ip` has its own man page. The `SEE ALSO` section of the `ip` man page has a list of them:
```
$ man ip
...
SEE ALSO
       ip-address(8), ip-addrlabel(8), ip-l2tp(8), ip-link(8), ip-maddress(8),
       ip-monitor(8), ip-mroute(8), ip-neighbour(8), ip-netns(8), ip-
       ntable(8), ip-route(8), ip-rule(8), ip-tcp_metrics(8), ip-token(8), ip-
       tunnel(8), ip-xfrm(8)
       IP Command reference ip-cref.ps
...
```
Instead of looking at this every time you need the man page, simply add `-` and the name of the subcommand to `ip`, e.g. `man ip-route`.

Another source of information is the help function. To view the built-in help, add `help` after the subcommand:

```
$ ip address help
Usage: ip address {add|change|replace} IFADDR dev IFNAME [ LIFETIME ]
                                                      [ CONFFLAG-LIST ]
       ip address del IFADDR dev IFNAME [mngtmpaddr]
       ip address {save|flush} [ dev IFNAME ] [ scope SCOPE-ID ]
                            [ to PREFIX ] [ FLAG-LIST ] [ label LABEL ] [up]
       ip address [ show [ dev IFNAME ] [ scope SCOPE-ID ] [ master DEVICE ]
                         [ type TYPE ] [ to PREFIX ] [ FLAG-LIST ]
                         [ label LABEL ] [up] [ vrf NAME ] ]
       ip address {showdump|restore}
IFADDR := PREFIX | ADDR peer PREFIX
...
```
### Netmask and Routing Review
IPv4 and IPv6 are what are known as routed or routable protocols. This means they are designed in a way that make it possible for network designers to control traffic flow. Ethernet is not a routable protocol. This means that if you were to connect a bunch of devices together using nothing but Ethernet, there is very little you can do to control the flow of network traffic. Any measures to control traffic would end up similar to current routable and routing protocols.

Routable protocols allow network designers to segment networks to reduce the processing requirements of connectivity devices, provide redundancy, and manage traffic.

IPv4 and IPv6 addresses have two sections. The first set of bits make up the network section while the second set make up the host portion. The number of bits that make up the network portion are determined by the netmask (also called subnet mask). Sometimes it will also be referred to as the prefix length. Regardless of what it is called, it is the number of bits that the machine treats as the network portion of the address. With IPv4, sometimes this is specified in dotted decimal notation.

Below is an example using IPv4. Notice how the binary digits maintain their place value in the octets even when it is divided by the netmask.

```
192.168.130.5/20

          192      168      130      5
          11000000 10101000 10000010 00000101

20 bits = 11111111 11111111 11110000 00000000

Network = 192.168.128.0
Host    = 2.5
```
The network portion of an address is used by an IPv4 or IPv6 machines to lookup which interface a packet should be sent out on in its routing table. When an IPv4 or IPv6 host with routing enabled receives a packet that is not for the host itself, it attempts to match the network portion of the destination to a network in the routing table. If a matching entry is found, it sends the packet to the destination specified in the routing table. If no entries are found and a default route is configured, it is sent to the default route. If no entry is found and no default route are configured, the packet is discarded.

### Configuring an Interface
There are two tools we will be covering that you can use to configure a network interface: `ifconfig` and `ip`. The `ifconfig` program, while still widely used, is considered a legacy tool and may not be available on newer systems.
```
Tip
On newer Linux distributions, installation of the net-tools package will provide you with the legacy networking commands.
```
Before configuring an interface, you must first know what interfaces are available. There are a few ways to do this. One way is to use the `-a` option of `ifconfig`:
```
$ ifconfig -a
```
Another way is with `ip`. Sometimes you will see examples with `ip addr`, `ip a`, and some with `ip address`. They are synonymous. Officially, the subcommand is `ip address`. This means that if you wish to view the man page, you must use `man ip-address` and not `man ip-addr`.

The `link` subcommand for `ip` will list the interface links available for configuration:
```
$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:54:18:57 brd ff:ff:ff:ff:ff:ff
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:ab:11:3e brd ff:ff:ff:ff:ff:ff
```
Assuming the `sys` filesystem is mounted, you can also list the contents of `/sys/class/net`:
```
$ ls /sys/class/net
enp0s3  enp0s8  lo
```
To configure an interface with `ifconfig`, you must be logged in as root or use a utility such as `sudo` to run the command with root privilege. Follow the example below:
```
# ifconfig enp1s0 192.168.50.50/24
```
The Linux version of `ifconfig` is flexible with how you specify the subnet mask:
```
# ifconfig eth2 192.168.50.50 netmask 255.255.255.0
# ifconfig eth2 192.168.50.50 netmask 0xffffff00
# ifconfig enp0s8 add 2001:db8::10/64
```
Notice how with IPv6 the keyword `add` was used. If you don’t precede an IPv6 address with `add`, you will get an error message.

The following command configures an interface with `ip`:
```
# ip addr add 192.168.5.5/24 dev enp0s8
# ip addr add 2001:db8::10/64 dev enp0s8
```
With `ip`, the same command is used for both IPv4 and IPv6.

### Configuring Low Level Options
The `ip link` command is used to configure low level interface or protocol settings such as VLANs, ARP, or MTUs, or disabling an interface.

A common task for `ip link` is to disable or enable an interface. This can be done with `ifconfig` as well:
```
# ip link set dev enp0s8 down
# ip link show dev enp0s8
3: enp0s8: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast state DOWN mode DEFAULT group default qlen 1000
    link/ether 08:00:27:ab:11:3e brd ff:ff:ff:ff:ff:ff
# ifconfig enp0s8 up
# ip link show dev enp0s8
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:ab:11:3e brd ff:ff:ff:ff:ff:ff
```
Sometimes you may need to adjust an interface’s MTU. As with enabling/disabling interfaces, this can be done with either `ifconfig` of `ip link`:
```
# ip link set enp0s8 mtu 2000
# ip link show dev enp0s3
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 2000 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:54:53:59 brd ff:ff:ff:ff:ff:ff
# ifconfig enp0s3 mtu 1500
# ip link show dev enp0s3
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:54:53:59 brd ff:ff:ff:ff:ff:ff
```
### The Routing Table
The commands `route`, `netstat -r`, and `ip route` can all be used to view your routing table. If you wish to modify your routes, you need to use `route` or `ip route`. Below are examples of viewing a routing table:
```
$ netstat -r
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         10.0.2.2        0.0.0.0         UG        0 0          0 enp0s3
10.0.2.0        0.0.0.0         255.255.255.0   U         0 0          0 enp0s3
192.168.150.0   0.0.0.0         255.255.255.0   U         0 0          0 enp0s8
$ ip route
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
192.168.150.0/24 dev enp0s8 proto kernel scope link src 192.168.150.200
$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         10.0.2.2        0.0.0.0         UG    100    0        0 enp0s3
10.0.2.0        0.0.0.0         255.255.255.0   U     100    0        0 enp0s3
192.168.150.0   0.0.0.0         255.255.255.0   U     0      0        0 enp0s8
```
Notice how there is no output regarding IPv6. If you wish to view your routing table for IPv6, you must use `route -6`, `netstat -6r`, and `ip -6 route`.
```
$ route -6
Kernel IPv6 routing table
Destination                    Next Hop                   Flag Met Ref Use If
2001:db8::/64                  [::]                       U    256 0      0 enp0s8
fe80::/64                      [::]                       U    100 0      0 enp0s3
2002:a00::/24                  [::]                       !n   1024 0      0 lo
[::]/0                         2001:db8::1                UG   1   0      0 enp0s8
localhost/128                  [::]                       Un   0   2     84 lo
2001:db8::10/128               [::]                       Un   0   1      0 lo
fe80::a00:27ff:fe54:5359/128   [::]                       Un   0   1      0 lo
ff00::/8                       [::]                       U    256 1      3 enp0s3
ff00::/8                       [::]                       U    256 1      6 enp0s8
```
An example of `netstat -r6` has been omitted because its output is identical to `route -6`. Some of the output of the above `route` command is self explanatory. The `Flag` column provides some information about the route. The `U` flag indicates that a route is up. A `!` means reject route i.e. a route with a `!` won’t be used. The n flag means the route hasn’t been cached. The kernel maintains a cache of routes for faster lookups separately from all known routes. The `G` flag indicates a gateway. The `Metric` or `Met` column isn’t used by the kernel. It refers to the administrative distance to the target. This administrative distance is used by routing protocols to determine dynamic routes. The `Ref` column is the reference count, or number of uses of a route. Like `Metric`, it is not used by the Linux kernel. The `Use` column shows the number of lookups for a route.

In the output of `netstat -r`, `MSS` indicates the maximum segment size for TCP connections over that route. The Window column shows you the defualt TCP window size. The `irtt` shows the round trip time for packets on this route.

The output of `ip route` and `ip -6` route reads as follows:

1. Destination.
2. Optional address followed by interface.
3. The routing protocol used to add the route.
4. The scope of the route. If this is omitted, it is global scope, or a gateway.
5. The route’s metric. This is used by dynamic routing protocols to determine the cost of the route. This isn’t used by most systems.
6. If it is an IPv6 route, the RFC4191 route preference.

Working through a few examples should clarify this:

IPv4 Example
```
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
```
1. The destination is the default route.
2. The gateway address is 10.0.2.2 reachable through interface enp0s3.
3. It was added to the routing table by DHCP.
4. The scope was omitted, so it is global.
5. The route has a cost value of 100.
6. No IPv6 route preference.

IPv6 Example
```
fc0::/64 dev enp0s8 proto kernel metric 256 pref medium
```
1. The destination is fc0::/64.
2. It is reachable through interface enp0s8.
3. It was added automatically by the kernel.
4. The scope was omitted, so it is global.
5. The route has a cost value of 256.
6. It has an IPv6 preference of medium.

### Managing Routes
Routes can by managed by using `route` or `ip route`. Below is an example of adding and removing a route using the `route` command. With `route`, you must use the `-6` option for IPv6:
```
# ping6 -c 2 2001:db8:1::20
connect: Network is unreachable
# route -6 add 2001:db8:1::/64 gw 2001:db8::3
# ping6 -c 2 2001:db8:1::20
PING 2001:db8:1::20(2001:db8:1::20) 56 data bytes
64 bytes from 2001:db8:1::20: icmp_seq=1 ttl=64 time=0.451 ms
64 bytes from 2001:db8:1::20: icmp_seq=2 ttl=64 time=0.438 ms
# route -6 del 2001:db8:1::/64 gw 2001:db8::3
# ping6 -c 2 2001:db8:1::20
connect: Network is unreachable
```
Below is the same example using the `ip route` command:
```
# ping6 -c 2 2001:db8:1:20
connect: Network is unreachable
# ip route add 2001:db8:1::/64 via 2001:db8::3
# ping6 -c 2 2001:db8:1:20
PING 2001:db8:1::20(2001:db8:1::20) 56 data bytes
64 bytes from 2001:db8:1::20: icmp_seq=2 ttl=64 time=0.529 ms
64 bytes from 2001:db8:1::20: icmp_seq=2 ttl=64 time=0.438 ms
# ip route del 2001:db8:1::/64 via 2001:db8::3
# ping6 -c 2 2001:db8:1::20
connect: Network is unreachable
```
___

Linux based operating systems have a variety of tools to troubleshoot network problems with. This lesson is going to cover some of the more common ones. At this point you should have a grasp of the OSI or other layered models of networking, IPv4 or IPv6 addressing, and the basics of routing and switching.

The best way to test a network connection is to try to use your application. When that doesn’t work, there are plenty of tools available to help diagnose the problem.

### Testing Connections With `ping`
The `ping` and `ping6` commands can be used to send an ICMP echo request to an IPv4 or IPv6 address, respectively. An ICMP echo request sends a small amount of data to the destination address. If the destination address is reachable, it will send an ICMP echo reply message back to the sender with the same data that was sent to it:
```
$ ping -c 3 192.168.50.2
PING 192.168.50.2 (192.168.50.2) 56(84) bytes of data.
64 bytes from 192.168.50.2: icmp_seq=1 ttl=64 time=0.525 ms
64 bytes from 192.168.50.2: icmp_seq=2 ttl=64 time=0.419 ms
64 bytes from 192.168.50.2: icmp_seq=3 ttl=64 time=0.449 ms
```
```
--- 192.168.50.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2006ms
rtt min/avg/max/mdev = 0.419/0.464/0.525/0.047 ms
$ ping6 -c 3 2001:db8::10
PING 2001:db8::10(2001:db8::10) 56 data bytes
64 bytes from 2001:db8::10: icmp_seq=1 ttl=64 time=0.425 ms
64 bytes from 2001:db8::10: icmp_seq=2 ttl=64 time=0.480 ms
64 bytes from 2001:db8::10: icmp_seq=3 ttl=64 time=0.725 ms

--- 2001:db8::10 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.425/0.543/0.725/0.131 ms
```
The `-c` option is used to specify the number of packets to send. If you omit this option, `ping` and `ping6` will continue to send packets until you stop it, typically with the `Ctrl`+`C` keyboard combination.

Just because you can’t ping a host, doesn’t mean you can’t connect to it. Many organizations have firewalls or router access control lists that block everything but the bare minimum needed for their systems to function. This includes ICMP echo request and replies. Since these packets can include arbitrary data, a clever attacker could use them to exfiltrate data.

### Tracing Routes
The `traceroute` and `traceroute6` programs can be used to show you the route a packet takes to get to its destination. They do this by sending multiple packets to the destination, incrementing the Time-To-Live (TTL) field of the IP header with each subsequent packet. Each router along the way will respond with a TTL exceeded ICMP message:
```
$ traceroute 192.168.1.20
traceroute to 192.168.1.20 (192.168.1.20), 30 hops max, 60 byte packets
 1  10.0.2.2 (10.0.2.2)  0.396 ms  0.171 ms  0.132 ms
 2  192.168.1.20 (192.168.1.20)  2.665 ms  2.573 ms  2.573 ms
$ traceroute 192.168.50.2
traceroute to 192.168.50.2 (192.168.50.2), 30 hops max, 60 byte packets
 1  192.168.50.2 (192.168.50.2)  0.433 ms  0.273 ms  0.171 ms
$ traceroute6 2001:db8::11
traceroute to 2001:db8::11 (2001:db8::11), 30 hops max, 80 byte packets
 1  2001:db8::11 (2001:db8::11)  0.716 ms  0.550 ms  0.641 ms
$ traceroute 2001:db8::11
traceroute to 2001:db8::11 (2001:db8::11), 30 hops max, 80 byte packets
 1  2001:db8::10 (2001:db8::11)  0.617 ms  0.461 ms  0.387 ms
$ traceroute net2.example.net
traceroute to net2.example.net (192.168.50.2), 30 hops max, 60 byte packets
 1  net2.example.net (192.168.50.2)  0.533 ms  0.529 ms  0.504 ms
$ traceroute6 net2.example.net
traceroute to net2.example.net (2001:db8::11), 30 hops max, 80 byte packets
 1  net2.example.net (2001:db8::11)  0.738 ms  0.607 ms  0.304 ms
```
By default, `traceroute` sends 3 UDP packets with junk data to port 33434, incrementing it each time it sends a packet. Each line in the command’s output is a router interface the packet traverses through. The times shown in each line of the output is the round trip time for each packet. The IP address is the address of the router interface in question. If `traceroute` is able to, it uses the DNS name of the router interface. Sometimes you will see * in place of a time. When this happens, it means that `traceroute` never received the TTL exceeded message for this packet. When you start seeing this, this often indicates that the last response is the last hop on the route.

If you have access to `root`, the `-I` option will set `traceroute` to use ICMP echo requests instead of UDP packets. This is often more effective than UDP because the destination host is more likely to respond to an ICMP echo request than the UDP packet:
```
# traceroute -I learning.lpi.org
traceroute to learning.lpi.org (208.94.166.201), 30 hops max, 60 byte packets
 1  047-132-144-001.res.spectrum.com (47.132.144.1)  9.764 ms  9.702 ms  9.693 ms
 2  096-034-094-106.biz.spectrum.com (96.34.94.106)  8.389 ms  8.481 ms  8.480 ms
 3  dtr01hlrgnc-gbe-4-15.hlrg.nc.charter.com (96.34.64.172)  8.763 ms  8.775 ms  8.770 ms
 4  acr01mgtnnc-vln-492.mgtn.nc.charter.com (96.34.67.202)  27.080 ms  27.154 ms  27.151 ms
 5  bbr01gnvlsc-bue-3.gnvl.sc.charter.com (96.34.2.112)  31.339 ms  31.398 ms  31.395 ms
 6  bbr01aldlmi-tge-0-0-0-13.aldl.mi.charter.com (96.34.0.161)  39.092 ms  38.794 ms  38.821 ms
 7  prr01ashbva-bue-3.ashb.va.charter.com (96.34.3.51)  34.208 ms  36.474 ms  36.544 ms
 8  bx2-ashburn.bell.ca (206.126.236.203)  53.973 ms  35.975 ms  38.250 ms
 9  tcore4-ashburnbk_0-12-0-0.net.bell.ca (64.230.125.190)  66.315 ms  65.319 ms  65.345 ms
10  tcore4-toronto47_2-8-0-3.net.bell.ca (64.230.51.22)  67.427 ms  67.502 ms  67.498 ms
11  agg1-toronto47_xe-7-0-0_core.net.bell.ca (64.230.161.114)  61.270 ms  61.299 ms  61.291 ms
12  dis4-clarkson16_5-0.net.bell.ca (64.230.131.98)  61.101 ms  61.177 ms  61.168 ms
13  207.35.12.142 (207.35.12.142)  70.009 ms  70.069 ms  59.893 ms
14  unassigned-117.001.centrilogic.com (66.135.117.1)  61.778 ms  61.950 ms  63.041 ms
15  unassigned-116.122.akn.ca (66.135.116.122)  62.702 ms  62.759 ms  62.755 ms
16  208.94.166.201 (208.94.166.201)  62.936 ms  62.932 ms  62.921 ms
```
Some organizations block ICMP echo requests and replies. To get around this, you can use TCP. By using a known open TCP port, you can guarantee the destination host will respond. To use TCP, use the `-T` option along with `-p` to specify the port. As with ICMP echo requests, you must have access to `root` to do this:
```
# traceroute -m 60 -T -p 80  learning.lpi.org
traceroute to learning.lpi.org (208.94.166.201), 60 hops max, 60 byte packets
 1  * * *
 2  096-034-094-106.biz.spectrum.com (96.34.94.106)  12.178 ms  12.229 ms  12.175 ms
 3  dtr01hlrgnc-gbe-4-15.hlrg.nc.charter.com (96.34.64.172)  12.134 ms  12.093 ms  12.062 ms
 4  acr01mgtnnc-vln-492.mgtn.nc.charter.com (96.34.67.202)  31.146 ms  31.192 ms  31.828 ms
 5  bbr01gnvlsc-bue-3.gnvl.sc.charter.com (96.34.2.112)  39.057 ms  46.706 ms  39.745 ms
 6  bbr01aldlmi-tge-0-0-0-13.aldl.mi.charter.com (96.34.0.161)  50.590 ms  58.852 ms  58.841 ms
 7  prr01ashbva-bue-3.ashb.va.charter.com (96.34.3.51)  34.556 ms  37.892 ms  38.274 ms
 8  bx2-ashburn.bell.ca (206.126.236.203)  38.249 ms  36.991 ms  36.270 ms
 9  tcore4-ashburnbk_0-12-0-0.net.bell.ca (64.230.125.190)  66.779 ms  63.218 ms tcore3-ashburnbk_100ge0-12-0-0.net.bell.ca (64.230.125.188)  60.441 ms
10  tcore4-toronto47_2-8-0-3.net.bell.ca (64.230.51.22)  63.932 ms  63.733 ms  68.847 ms
11  agg2-toronto47_xe-7-0-0_core.net.bell.ca (64.230.161.118)  60.144 ms  60.443 ms agg1-toronto47_xe-7-0-0_core.net.bell.ca (64.230.161.114)  60.851 ms
12  dis4-clarkson16_5-0.net.bell.ca (64.230.131.98)  67.246 ms dis4-clarkson16_7-0.net.bell.ca (64.230.131.102)  68.404 ms dis4-clarkson16_5-0.net.bell.ca (64.230.131.98)  67.403 ms
13  207.35.12.142 (207.35.12.142)  66.138 ms  60.608 ms  64.656 ms
14  unassigned-117.001.centrilogic.com (66.135.117.1)  70.690 ms  62.190 ms  61.787 ms
15  unassigned-116.122.akn.ca (66.135.116.122)  62.692 ms  69.470 ms  68.815 ms
16  208.94.166.201 (208.94.166.201)  61.433 ms  65.421 ms  65.247 ms
17  208.94.166.201 (208.94.166.201)  64.023 ms  62.181 ms  61.899 ms
```
Like `ping`, `traceroute` has its limitations. It is possible for firewalls and routers to block the packets sent from or returned to `traceroute`. If you have `root` access, there are options that can help you get accurate results.

### Finding MTUs With `tracepath`
The `tracepath` command is similar to `traceroute`. The difference is it tracks Maximum Transmission Unit (MTU) sizes along the path. The MTU is either a configured setting on a network interface or hardware limitation of the largest protocol data unit that it can transmit or receive. The `tracepath` program works the same way as `traceroute` in that it increments the TTL with each packet. It differs by sending a very large UDP datagram. It is almost inevitable for the datagram to be larger than the device with the smallest MTU along the route. When the packet reaches this device, the device will typically respond with a destination unreachable packet. The ICMP destination unreachable packet has a field for the MTU of the link it would send the packet on if it were able. `tracepath` then sends all subsequent packets with this size:
```
$ tracepath 192.168.1.20
 1?: [LOCALHOST]                                         pmtu 1500
 1:  10.0.2.2                                              0.321ms
 1:  10.0.2.2                                              0.110ms
 2:  192.168.1.20                                          2.714ms reached
     Resume: pmtu 1500 hops 2 back 64
```
Unlike `traceroute`, you must explicitly use `tracepath6` for IPv6:
```
$ tracepath 2001:db8::11
tracepath: 2001:db8::11: Address family for hostname not supported
$ tracepath6 2001:db8::11
 1?: [LOCALHOST]                        0.027ms pmtu 1500
 1:  net2.example.net                                      0.917ms reached
 1:  net2.example.net                                      0.527ms reached
     Resume: pmtu 1500 hops 1 back 1
```
The output is similar to `traceroute`. The advantage of `tracepath` is on the last line it outputs the smallest MTU on the entire link. This can be useful for troubleshooting connections that can’t handle fragments.

As with the previous troubleshooting tools, there is the potential for equipment to block your packets.

### Creating Arbitrary Connections
The `nc` program, known as netcat, can send or receive arbitrary data over a TCP or UDP network connection. The following examples should make its functionality clear.

Here is an example of setting up a listener on port `1234`:
```
$ nc -l 1234
LPI Example
```
The output of `LPI Example` appears after the example below, which is setting up a netcat sender to send packets to `net2.example.net` on port `1234`. The `-l` option is used to specify that you wish for nc to receive data instead of send it:
```
$ nc net2.example.net 1234
LPI Example
```
Press `Ctrl`+`C` on either system to stop the connection.

Netcat works with both IPv4 and IPv6 addresses. It works with both TCP and UDP. It can even be used to setup a crude remote shell.
```
Warning
Note that not every installation of nc supports the -e switch. Be sure to review the man pages for your installation for security information about this option as well as alternative methods to execute commands on a remote system.
```
```
$ hostname
net2
$ nc -u -e /bin/bash -l 1234
```
The `-u` option is for UDP. `-e` instructs netcat to send everything it receives to standard input of the executable following it. In this example, `/bin/bash`.
```
$ hostname
net1
$ nc -u net2.example.net 1234
hostname
net2
pwd
/home/emma
```
Notice how the `hostname` command output matched that of the listening host and the `pwd` command output a directory?

### Viewing Current Connections and Listeners
The `netstat` and `ss` programs can be used to view the status of your current listeners and connections. As with `ifconfig`, `netstat` is a legacy tool. Both `netstat` and `ss` have similar output and options. Here some options available to both programs:

`-a`
- Shows all sockets.

`-l`
- Shows listening sockets.

`-p`
- Shows the process associated with the connection.

`-n`
- Prevents name lookups for both ports and addresses.

`-t`
- Shows TCP connections.

`-u`
- Shows UDP connections.

The examples below show the output of a commonly used set of options for both programs:
```
# netstat -tulnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      892/sshd
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1141/master
tcp6       0      0 :::22                   :::*                    LISTEN      892/sshd
tcp6       0      0 ::1:25                  :::*                    LISTEN      1141/master
udp        0      0 0.0.0.0:68              0.0.0.0:*                           692/dhclient
# ss -tulnp
# ss -tulnp
Netid  State      Recv-Q Send-Q      Local Address:Port                     Peer Address:Port
udp    UNCONN     0      0                       :68                                  *:                   users:(("dhclient",pid=693,fd=6))
tcp    LISTEN     0      128                     :22                                  *:                   users:(("sshd",pid=892,fd=3))
tcp    LISTEN     0      100             127.0.0.1:25                                  :                   users:(("master",pid=1099,fd=13))
tcp    LISTEN     0      128                  [::]:22                               [::]:*                   users:(("sshd",pid=892,fd=4))
tcp    LISTEN     0      100                 [::1]:25                               [::]:*                   users:(("master",pid=1099,fd=14))
```
The `Recv-Q` column is the number of packets a socket has received but not passed off to its program. The `Send-Q` column is the number of packets a socket has sent that have not been acknowledged by the receiver. The rest of the columns are self explanatory.






___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)