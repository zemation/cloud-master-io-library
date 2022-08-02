# LPIC-1.109: Networking Fundamentals

## Lesson 109.1 Fundamentals of internet protocols

The TCP/IP (Transmission Control Protocol/Internet Protocol) is a stack of protocols used to enable communication between computers. Despite the name, the stack consists of several protocols such as IP, TCP, UDP, ICMP, DNS, SMTP, ARP and others.

### IP (Internet Protocol)
The IP is the protocol responsible for the logical addressing of a host, enabling the packet to be sent from one host to another. For this each device on the network is assigned a unique IP address, and it is possible to assign more than one address to the same device.

In the version 4 of the IP protocol, usually called IPv4, the address is formed by a set of 32 bits separated into 4 groups of 8 bits, represented in decimal form, called “dotted quad”. For example:

Binary format (4 groups of 8 bits)
- `11000000.10101000.00001010.00010100`

Decimal format
- `192.168.10.20`

In IPv4, the values for each octet can range from 0 to 255, which is the equivalent of 11111111 in binary format.

### Address Classes
Theoretically, IP addresses are separated by classes, which are defined by the range of the first octet as shown in the table below:

Class |	First Octect | Range | Example
--- | --- | --- | ---
A | 1-126 | 1.0.0.0 – 126.255.255.255 | 10.25.13.10
B | 128-191 | 128.0.0.0 – 191.255.255.255 | 141.150.200.1
C | 192-223 | 192.0.0.0 – 223.255.255.255 | 200.178.12.242

### Public and Private IPs
As mentioned earlier, for communication to occur each device on the network must be associated with at least one unique IP address. However, if each device connected to the Internet in the world had a unique IP address, there would not be enough IPs (v4) for everyone. For this, private IP addresses were defined.

Private IPs are ranges of IP addresses that have been reserved for use in the internal (private) networks of companies, institutions, homes, etc. Within the same network, the use of an IP address remains unique. However, the same private IP address can be used within any private network.

Thus, on the Internet we have data traffic using public IP addresses, which are recognizable and routed over the Internet, while within private networks these reserved IP ranges are used. The router is responsible for converting traffic from the private network to the public network and vice versa.

The ranges of Private IPs, separated by classes, can be seen in the table below:

Class | First Octet	| Range | Private IPs
--- | --- | --- | ---
A | 1-126 | 1.0.0.0 – 126.255.255.255 | 10.0.0.0 – 10.255.255.255
B | 128-191 | 128.0.0.0 – 191.255.255.255 | 172.16.0.0 – 172.31.255.255
C | 192-223 | 192.0.0.0 – 223.255.255.255 | 192.168.0.0 – 192.168.255.255

### Converting from Decimal Format to Binary
For the subjects of this topic, it is important to know how to convert IP addresses between binary and decimal formats.

The conversion from decimal format to binary is done through consecutive divisions by 2. As an example, let’s convert the value 105 by the following steps:

1. Dividing the value 105 by 2 we have:
```
105/2
Quotient = 52
Rest = 1
```
2. Divide the quotient sequentially by 2, until the quotient is equal to 1:
```
52/2
Rest = 0
Quotient = 26
```
```
26/2
Rest = 0
Quotient = 13
```
```
13/2
Rest = 1
Quotient = 6
```
```
6/2
Rest = 0
Quotient = 3
```
```
3/2
Rest = 1
Quotient = 1
```
3. Group the last quotient followed by the remainder of all divisions:
```
1101001
```
Fill in 0s to the left until 8 bits are completed:
```
01101001
```
In the end, we have that the value 105 in decimal is equal to 01101001 in binary.

### Converting from Binary Format to Decimal
In this example, we will use the binary value `10110000`.

1. Each bit is associated with a value with a base power of two. The powers are started at 0, and are incremented from right to left. In this example we will have:

1 | 0 | 1 | 1 | 0 | 0 | 0 | 0
--- | --- | --- | --- | --- | --- | --- | --- 
2**7 | 2**6 | 2**5 | 2**4 | 2**3 | 2**2 | 2**1 | 2**0

2. When the bit is 1, we assign the value of the respective power, when the bit is 0 the result is 0.
1 | 0 | 1 | 1 | 0 | 0 | 0 | 0
--- | --- | --- | --- | --- | --- | --- | ---
2**7 | 2**6 | 2**5 | 2**4 | 2**3 | 2**2 | 2**1 | 2**0
128 | 0 | 32 | 16 | 0 | 0 | 0 | 0

3. Add up all values:
- 128 + 32 + 16 = 176

4. Thus, 10110000 in binary is equal to 176 in decimal.

### Netmask
The network mask (or netmask) is used in conjunction with the IP address to determine which part of the IP represents the network and which represents the hosts. It has the same format as the IP address, that is, there are 32 bits in 4 groups of 8. For example:

Decimal | Binary |	CIDR
--- | --- | ---
255.0.0.0 | 11111111.00000000.00000000.00000000 | 8
255.255.0.0 | 11111111.11111111.00000000.00000000 | 16
255.255.255.0 | 11111111.11111111.11111111.00000000 | 24

Using the `255.255.0.0` mask as an example, it indicates that in the IP associated with it, the first 16 bits (2 first decimals) identify the network/subnet and the last 16 bits are used to uniquely identify the hosts within the network.

The CIDR (Classless Inter-Domain Routing) mentioned above is related to a simplified mask notation, which indicates the number of bits (1) associated with the network/subnet. This notation is commonly used to replace the decimal format, for example `/24` instead of `255.255.255.0`.

It is interesting to note that each class of IP has a standard mask, as follows:

Class |	First Octet	| Range | Default Mask
--- | --- | --- | ---
A | 1-126 | 1.0.0.0 – 126.255.255.255 | 255.0.0.0 / 8
B | 128-191 | 128.0.0.0 – 191.255.255.255 | 255.255.0.0 / 16
C | 192-223 | 192.0.0.0 – 223.255.255.255 | 255.255.255.0 / 24

However, this pattern does not mean that this is the mask that will always be used. It is possible to use any mask with any IP address, as we will see below.

Here are some examples of using IPs and Masks:
```
192.168.8.12 / 255.255.255.0 / 24
```
Range
- `192.168.8.0` - `192.168.8.255`

Network Address
- `192.168.8.0`

Broadcast Address
- `192.168.8.255`

Hosts
- `192.168.8.1` - `192.168.8.254`

In this case we have the first 3 digits (first 24 bits) of the IP address define the network and the final digit identifies the addresses of the hosts, that is, the range of this network goes from `192.168.8.0` to `192.168.8.255`.

We now have two important concepts: Every network/subnet has 2 reserved addresses, the first address in the range is called the network address. In this case `192.168.8.0`, which is used to identify the network/subnet itself. The last address in the range is called the broadcast address, in this case `192.168.8.255`. This destination address is used to send the same message (packet) to all IP hosts on that network/subnet.

The network and broadcast addresses cannot be used by the machines on the network. Therefore, the list of IPs that can be effectively configured ranges from `192.168.8.1` to `192.168.8.254`.

Now the example of the same IP, but with a different mask:
```
192.168.8.12 / 255.255.0.0 / 16
```
Range
- `192.168.0.0` - `192.168.255.255`

Network Address
- `192.168.0.0`

Broadcast Address
- `192.168.255.255`

Hosts
- `192.168.0.1` – `192.168.255.254`

See how the different mask changes the range of IPs that are within the same network/subnet.

The division of networks by masks is not restricted to the default values (8, 16, 24). We can create subdivisions as desired, adding or removing bits in the network identification, creating the new subnets.

For example:
```
11111111.11111111.11111111.00000000 = 255.255.255.0 = 24
```
If we want to subdivide the network above into 2, just add another bit to the network identification in the mask, like this:
```
11111111.11111111.11111111.10000000 = 255.255.255.128 = 25
```
We have then the following subnets:
```
192.168.8.0   - 192.168.8.127
192.168.8.128 - 192.168.8.255
```
If we further increase the subdivision of the network:
```
11111111.11111111.11111111.11000000 = 255.255.255.192 = 26
```
We will have:
```
192.168.8.0   - 192.168.8.63
192.168.8.64  - 192.168.8.127
192.168.8.128 - 192.168.8.191
192.168.8.192 - 192.168.8.255
```
Note that in each subnet we will have the reserved network (the first in the range) and broadcast (the last in the range) addresses, so the more the network is subdivided, the fewer IPs can be effectively used by the hosts.

### Identifying the Network and Broadcast Addresses
Through an IP Address and a Mask, we can identify the network address and the broadcast address, and thus define the range of IPs for the network/subnet.

The network address is obtained by using a “Logical AND” between the IP address and the mask in their binary formats. Let’s take the example using IP `192.168.8.12` and mask `255.255.255.192`.

Converting from decimal to binary format, as we saw earlier, we have:
```
11000000.10101000.00001000.00001100 (192.168.8.12)
11111111.11111111.11111111.11000000 (255.255.255.192)
```
With “Logical AND”, we have 1 and 1 = 1, 0 and 0 = 0, 1 and 0 = 0, so:
```
11000000.10101000.00001000.00001100 (192.168.8.12)
11111111.11111111.11111111.11000000 (255.255.255.192)
11000000.10101000.00001000.00000000
```

So the network address for that subnet is `192.168.8.0`.

Now to obtain the broadcast address, we must use the network address where all bits related to the host address to 1:
```
11000000.10101000.00001000.00000000 (192.168.8.0)
11111111.11111111.11111111.11000000 (255.255.255.192)
11000000.10101000.00001000.00111111
```
The broadcast address is then `192.168.8.63`.

In conclusion, we have:
```
192.168.8.12 / 255.255.255.192 / 26
```
Range
- `192.168.8.0` - `192.168.8.63`

Network Address
- `192.168.8.0`

Broadcast Address
- `192.168.8.63`

Hosts
- `192.168.8.1` – `192.168.8.62`

### Default Route
As we have seen so far, machines that are within the same logical network/subnet can communicate directly via the IP protocol.

But let’s consider the example below:

Network 1
- `192.168.10.0/24`

Network 2
- `192.168.200.0/24`

In this case, the `192.168.10.20` machine cannot directly send a packet to the `192.168.200.100`, as they are on different logical networks.

To enable this communication a router (or a set of routers) is used. A router in this configuration can also be called a gateway as it provides a gateway between two networks. This device has access to both networks as it is configured with IPs from both networks. For example `192.168.10.1` and `192.168.200.1`, and for this reason it manages to be the intermediary in this communication.

To enable this, each host on the network must have configured what is called the default route. The default route indicates the IP to which all packets whose destination is an IP that is not part of the host’s logical network must be sent.

In the example above, the default route for machines on the `192.168.10.0/24` network will be the IP `192.168.10.1`, which is the router/gateway IP, while the default route for machines on the `192.168.200.0/24` network will be `192.168.200.1`.

The default route is also used so that machines on the private network (LAN) have access to the Internet (WAN), through a router.
___

At the beginning of this subtopic we saw that the TCP/IP stack is composed of a series of different protocols. So far we have studied the IP protocol, which allows communication between machines through IP addresses, masks, routes, etc.

For a host to be able to access a service available on another host, in addition to the IP addressing protocol at the network layer, it will be necessary to use a protocol at the transport layer such as the TCP and UDP protocols.

These protocols carry out this communication through network ports. So in addition to defining a source and destination IP, source and destination ports will be used to access a service.

The port is identified by a 16-bit field thus providing a limit of 65,535 possible ports. The services (destination) use ports 1 to 1023, which are called privileged ports because they have root access to the system. The origin of the connection will use the range of ports from 1024 to 65,535, called non-privileged ports, or socket ports.

The ports used by each type of service are standardized and controlled by IANA (Internet Assigned Numbers Authority). This means that on any system, port 22 is used by the SSH service, port 80 by the HTTP service and so on.

The table below contains the main services and their respective ports.

Port | Service
--- | ---
20 | FTP (data)
21 | FTP (control)
22 | SSH (Secure Socket Shell)
23 | Telnet (Remote connection without encryption)
25 | SMTP (Simple Mail Transfer Protocol), Sending Mails
53 | DNS (Domain Name System)
80 | HTTP (Hypertext Transfer Protocol)
110 | POP3 (Post Office Protocol), Receiving Mails
123 | NTP (Network Time Protocol)
139 | Netbios
143 | IMAP (Internet Message Access Protocol), Accessing Mails
161 | SNMP (Simple Network Management Protocol)
162 | SNMPTRAP, SNMP Notifications
389 | LDAP (Lightweight Directory Access Protocol)
443 | HTTPS (Secure HTTP)
465 | SMTPS (Secure SMTP)
514 | RSH (Remote Shell)
636 | LDAPS (Secure LDAP)
993 | IMAPS (Secure IMAP)
995 | POP3S (Secure POP3)

On a Linux system, standard service ports are listed in the `/etc/services` file.

The identification of the desired destination port in a connection is done using the character `:` (colon) after the IPv4 address. Thus, when seeking access to the HTTPS service that is served by the IP host `200.216.10.15`, the client must send the request to the destination `200.216.10.15:443`.

The services listed above, and all others, use a transport protocol according to the characteristics required by the service, where TCP and UDP are the main ones.

### Transmission Control Protocol (TCP)
TCP is a connection-oriented transport protocol. This means that a connection is established between the client through the socket port, and the service through the service standard port. The protocol is in charge of ensuring that all packets are delivered properly, verifying the integrity and order of the packets, including the re-transmission of packets lost due to network errors.

Thus the application does not need to implement this data flow control as it is already guaranteed by the TCP protocol.

### User Datagram Protocol (UDP)
UDP establishes a connection between the client and the service, but does not control the data transmission of that connection. In other words, it does not check if packages have been lost, or if they are out of order, etc. The application is responsible for implementing the controls that are necessary.

As there is less control, UDP enables better performance in the data flow which is important for some types of services.

### Internet Control Message Protocol (ICMP)
ICMP is a network layer protocol in the TCP/IP stack and its main function is to analyze and control network elements, making it possible, for example:

- Traffic volume control
- Detection of unreachable destinations
- Route redirection
- Checking the status of remote hosts

It is the protocol used by the `ping` command, which will be studied in another subtopic.

### IPv6
So far we have studied version 4 of the IP protocol, i.e. IPv4. This has been the standard version used in all network and Internet environments. However it has limitations especially in regards to the number of available addresses, and with an already current reality that all devices will be somehow connected to the Internet (see IoT), it is becoming increasingly common to use version 6 of the IP protocol, commonly written as IPv6.

IPv6 brings a series of changes, new implementations and features, as well as a new representation of the address itself.

Each IPv6 address has 128 bits, divided into 8 groups of 16 bits, represented by hexadecimal values.

For example:
```
2001:0db8:85a3:08d3:1319:8a2e:0370:7344
```

### Abbreviations
IPv6 defines ways to shorten addresses in some situations. Let’s review the following address:
```
2001:0db8:85a3:0000:0000:0000:0000:7344
```
The first possibility is to reduce strings from `0000` to just `0`, resulting in:
```
2001:0db8:85a3:0:0:0:0:7344
```
In addition, in case of group strings with a value of `0`, they can be omitted, as follows:
```
2001:0db8:85a3::7344
```
However, this last abbreviation can only be done once in the address. See the example:
```
2001:0db8:85a3:0000:0000:1319:0000:7344

2001:0db8:85a3:0:0:1319:0:7344

2001:0db8:85a3::1319:0:7344
```
### IPv6 Address Types
IPv6 classifies addresses into 3 types:

Unicast
- Identifies a single network interface. By default, the 64 bits on the left identify the network, and the 64 bits on the right identify the interface.

Multicast
- Identifies a set of network interfaces. A packet sent to a multicast address will be sent to all interfaces that belong to that group. Although similar, it should not be confused with broadcast, which does not exist in the IPv6 protocol.

Anycast
- This also identifies a set of interfaces on the network, but the packet forwarded to an anycast address will be delivered to only one address in that set, not everyone.

### Differences between IPv4 and IPv6
In addition to the address several other differences can be pointed out between versions 4 and 6 of the IP. Here are some of them:

- Service ports follow the same standards and protocols (TCP, UDP), the difference is only in the representation of the IP and port set. In IPv6 the IP address must be protected with [] (brackets):

- IPv4
   - 200.216.10.15:443

- IPv6
   - [2001:0db8:85a3:08d3:1319:8a2e:0370:7344]:443

- IPv6 does not implement the broadcast feature exactly as it exists in IPv4. However the same result can be achieved by sending the packet to the address `ff02::1`, reaching all hosts on the local network. Something similar to using `224.0.0.1` on IPv4 for multicasting as a destination.

- Through the SLAAC (Stateless Address Autoconfiguration) feature, IPv6 hosts are able to self-configure.

- The TTL (Time to Live) field of IPv4 has been replaced by the “Hop Limit” in the IPv6 header.

- All IPv6 interfaces have a local address, called link-local address, prefixed with `fe80::/10`.

- IPv6 implements the Neighbor Discovery Protocol (NDP), which is similar to the ARP used by IPv4, but with much more functionality.


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)