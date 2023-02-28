# LPIC-1.109: Networking Fundamentals

## Lesson 109.4 Configure client side DNS

This lesson covers configuring client side name resolution and how to use a few CLI name resolution tools.

Remembering and maintaining IP addresses, UIDs, and GIDs, and other numbers for everything isn’t feasible. Name resolution services translate easy to remember names to numbers and vice versa. This lesson focuses on host name resolution, but a similar process happens for user names, group names, port numbers, and several others.

### Name Resolution Process
Programs that resolve names to numbers almost always use functions provided by the standard C library, which on Linux systems is the GNU project’s glibc. The first things these functions do is read the file `/etc/nsswitch.conf` for instructions on how to resolve that type of name. This lesson is focused on host name resolution, but the same process applies to other types of name resolution as well. Once the process reads `/etc/nsswitch.conf`, it looks up the name in the manner specified. Since `/etc/nsswitch.conf` supports plugins, what comes next could be anything. After the function is done looking up the name or number, it returns the result to the calling process.

### DNS Classes
DNS has three record classes, IN, HS, and CH. In this lesson, all DNS queries will be of type IN. The IN class is for internet addresses using the TCP/IP stack. CH is for ChaosNet, which is a network technology that was short lived and is no longer in use. The HS class is for Hesiod. Hesiod is a way to store things like passwd and group entries in DNS. Hesiod is beyond the scope of this lesson.

### Understanding `/etc/nsswitch.conf`
The best way to learn about this file is to read the man page that is part of the Linux man-pages project. It is available on most systems. It can be accessed with the command `man nsswitch.conf`. Alternatively, it can be found at https://man7.org/linux/man-pages/dir_section_5.html

Below is a simple example of `/etc/nsswitch.conf` from its man page:
```
           passwd:         compat
           group:          compat
           shadow:         compat

           hosts:          dns [!UNAVAIL=return] files
           networks:       nis [NOTFOUND=return] files
           ethers:         nis [NOTFOUND=return] files
           protocols:      nis [NOTFOUND=return] files
           rpc:            nis [NOTFOUND=return] files
           services:       nis [NOTFOUND=return] files
           # This is a comment. It is ignored by the resolution functions.
```
The file is organized into columns. The far left column is the type of name database. The rest of the columns are the methods the resolution functions should use to lookup a name. The methods are followed by the functions from left to right. Columns with `[]` are used to provide some limited conditional logic to the column immediately to the left of it.

Suppose a process is trying to resolve the host name `learning.lpi.org`. It would make an appropriate C library call (most likely `gethostbyname`). This function will then read `/etc/nsswitch.conf`. Since the process is looking up a host name, it will find the line starting with `hosts`. It would then attempt to use DNS to resolve the name. The next column, `[!UNAVAIL=return]` means that if the service is not unavailable, then do not try the next source, i.e., if DNS is available, stop trying to resolve the host name even if the name servers are unable to. If DNS is unavailable, then continue on to the next source. In this case, the next source is `files`.

When you see a column in the format `[result=action]`, it means that when a resolver lookup of the column to the left of it is `result`, then `action` is performed. If `result` is preceded with a `!`, it means if the result is not `result`, then perform `action`. For descriptions of the possible results and actions, see the man page.

Now suppose a process is trying to resolve a port number to a service name. It would read the `services` line. The first source listed is NIS. NIS stands for Network Information Service (it is sometimes referred to as yellow pages). It is an old service that allowed central management of things such as users. It is rarely used anymore due to its weak security. The next column `[NOTFOUND=return]` means that if the lookup succeeded but the service was not found to stop looking. If the aforementioned condition does not apply, use local files.

Anything to the right of `#` is a comment and ignored.

### The `/etc/resolv.conf` File
The file `/etc/resolv.conf` is used to configure host resolution via DNS. Some distributions have startup scripts, daemons, and other tools that write to this file. Keep this in mind when manually editing this file. Check your distribution and any network configuration tools documentation if this is the case. Some tools, such as Network Manager will leave a comment in the file letting you know that manual changes will be overwritten.

As with `/etc/nsswitch.conf`, there is a man page associated with the file. It can be accessed with the command man resolv.conf or at https://man7.org/linux/man-pages/man5/resolv.conf.5.html.

The file format is rather straight forward. In the far left column, you have the option `name`. The rest of the columns on the same line are the option’s value.

The most common option is the `nameserver` option. It is used to specify the IPv4 or IPv6 address of a DNS server. As of the date of this writing, you can specify up to three name servers. If your `/etc/resolv.conf` does not have a `nameserver` option, your system will by default use the name server on the local machine.

Below is a simple example file that is representative of common configurations:
```
search lpi.org
nameserver 10.0.0.53
nameserver fd00:ffff::2:53
```
The `search` option is used to allow short form searches. In the example, a single search domain of `lpi.org` is configured. This means that any attempt to resolve a host name without a domain portion will have `.lpi.org` appended before the search. For example, if you were to try to search for a host named `learning`, the resolver would search for `learning.lpi.org`. You can have up to six search domains configured.

Another common option is the `domain` option. This is used to set your local domain name. If this option is missing, this defaults to everything after the first `.` in the machine’s host name. If the host name does not contain a `.`, it is assumed that the machine is part of the root domain. Like `search`, `domain` can be used for short name searches.

Keep in mind that `domain` and `search` are mutually exclusive. If both are present, the last instance in the file is used.

There are several options that can be set to affect the behavior of the resolver. To set these, use the `options` keyword, followed by the name of the option to set, and if applicable, a `:` followed by the value. Below is an example of setting the timeout option, which is the length of time in seconds the resolver will wait for a name server before giving up:
```
option timeout:3
```
There are other options to `resolv.conf`, but these are the most common.

### The `/etc/hosts` File
The file `/etc/hosts` is used to resolve names to IP addresses and vice versa. Both IPv4 and IPv6 are supported. The left column is the IP address, the rest are names associated with that address. The most common use for `/etc/hosts` is for hosts and addresses where DNS is not possible, such as loop back addresses. In the example below, IP addresses of critical infrastructure components are defined.

Here is a realistic example of an `/etc/hosts` file:
```
127.0.0.1       localhost
127.0.1.1       proxy
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters

10.0.0.1        gateway.lpi.org gateway gw
fd00:ffff::1    gateway.lpi.org gateway gw

10.0.1.53       dns1.lpi.org
fd00:ffff::1:53 dns1.lpi.org
10.0.2.53       dns2.lpi.org
fd00:ffff::2:53 dns2.lpi.org
```
### systemd-resolved
Systemd provides a service called `systemd-resolved`. It provides mDNS, DNS, and LLMNR. When it is running, it listens for DNS requests on `127.0.0.53`. It does not provide a full fledged DNS server. Any DNS requests it receives are looked up by querying servers configured in `/etc/systemd/resolv.conf` or `/etc/resolv.conf`. If you wish to use this, use `resolve` for `hosts` in `/etc/nsswitch.conf`. Keep in mind that the OS package that has the `systemd-resolved` library may not be installed by default.

### Name Resolution Tools
There are many tools available to Linux users for name resolution. This lesson covers three. One, `getent`, is useful for seeing how real world requests will resolve. Another command is `host`, which is useful for simple DNS queries. A program called `dig` is useful for complex DNS operations that can aid with troubleshooting DNS server problems.

### The `getent` Command
The `getent` utility is used to display entries from name service databases. It can retrieve records from any source configurable by `/etc/nsswitch.conf`.

To use `getent`, follow the command with the type of name you wish to resolve and optionally a specific entry to lookup. If you only specify the type of name, `getent` will attempt to display all entries of that data type:

```
$ getent hosts
127.0.0.1       localhost
127.0.1.1       proxy
10.0.1.53       dns1.lpi.org
10.0.2.53       dns2.lpi.org
127.0.0.1       localhost ip6-localhost ip6-loopback
$ getent hosts dns1.lpi,org
fd00:ffff::1:53 dns1.lpi.org
```
Starting with glibc version 2.2.5, you can force `getent` to use a specific data source with the `-s` option. The example below demonstrates this:
```
$ getent -s files hosts learning.lpi.org
::1             learning.lpi.org
$ getent -s dns hosts learning.lpi.org
208.94.166.198  learning.lpi.org
```
### The `host` Command
`host` is a simple program for looking up DNS entries. With no options, if `host` is given a name, it returns the A, AAAA, and MX record sets. If given an IPv4 or IPv6 address, it outputs the PTR record if one is available:
```
$ host wikipedia.org
wikipedia.org has address 208.80.154.224
wikipedia.org has IPv6 address 2620:0:861:ed1a::1
wikipedia.org mail is handled by 10 mx1001.wikimedia.org.
wikipedia.org mail is handled by 50 mx2001.wikimedia.org.
$ host 208.80.154.224
224.154.80.208.in-addr.arpa domain name pointer text-lb.eqiad.wikimedia.org.
```
If you are looking for a specific record type, you can use `host -t`:
```
$ host -t NS lpi.org
lpi.org name server dns1.easydns.com.
lpi.org name server dns3.easydns.ca.
lpi.org name server dns2.easydns.net.
$ host -t SOA lpi.org
lpi.org has SOA record dns1.easydns.com. zone.easydns.com. 1593109612 3600 600 1209600 300
```
`host` can also be used to query a specific name server if you do not wish to use the ones in `/etc/resolv.conf`. Simply add the IP address or host name of the server you wish to use as the last argument:
```
$ host -t MX lpi.org dns1.easydns.com
Using domain server:
Name: dns1.easydns.com
Address: 64.68.192.10#53
Aliases:

lpi.org mail is handled by 10 aspmx4.googlemail.com.
lpi.org mail is handled by 10 aspmx2.googlemail.com.
lpi.org mail is handled by 5 alt1.aspmx.l.google.com.
lpi.org mail is handled by 0 aspmx.l.google.com.
lpi.org mail is handled by 10 aspmx5.googlemail.com.
lpi.org mail is handled by 10 aspmx3.googlemail.com.
lpi.org mail is handled by 5 alt2.aspmx.l.google.com.
```
### The `dig` Command
Another tool for querying DNS servers is dig. This command is much more verbose than `host`. By default, `dig` queries for A records. It is probably too verbose for simply looking up an IP address or host name. `dig` will work for simple lookups, but it is more suited for troubleshooting DNS server configuration:
```
$ dig learning.lpi.org

; <<>> DiG 9.11.5-P4-5.1+deb10u1-Debian <<>> learning.lpi.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 63004
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 3, ADDITIONAL: 5

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: ca7a415be1cec45592b082665ef87f3483b81ddd61063c30 (good)
;; QUESTION SECTION:
;learning.lpi.org.		IN	A

;; ANSWER SECTION:
learning.lpi.org.	600	IN	A	208.94.166.198

;; AUTHORITY SECTION:
lpi.org.		86400	IN	NS	dns2.easydns.net.
lpi.org.		86400	IN	NS	dns1.easydns.com.
lpi.org.		86400	IN	NS	dns3.easydns.ca.

;; ADDITIONAL SECTION:
dns1.easydns.com.	172682	IN	A	64.68.192.10
dns2.easydns.net.	170226	IN	A	198.41.222.254
dns1.easydns.com.	172682	IN	AAAA	2400:cb00:2049:1::a29f:1835
dns2.easydns.net.	170226	IN	AAAA	2400:cb00:2049:1::c629:defe

;; Query time: 135 msec
;; SERVER: 192.168.1.20#53(192.168.1.20)
;; WHEN: Sun Jun 28 07:29:56 EDT 2020
;; MSG SIZE  rcvd: 266
```
As you can see, `dig` provides a lot of information. The output is divided into sections. The first section displays information about the version of `dig` installed and the query sent, along with any options used for the command. Next it shows information about the query and the response.

The next section shows information about EDNS extensions used and the query. In the example, the cookie extension is used. `dig` is looking for an A record for `learning.lpi.org`.

The next section shows the result of the query. The number in the second column is the TTL of the resource in seconds.

The rest of the output provides information about the domain’s name servers, including the NS records for the server along with the A and AAAA records of the servers in the domain’s NS record.

Like `host`, you can specify a record type with the `-t` option:
```
$ dig -t SOA lpi.org

; <<>> DiG 9.11.5-P4-5.1+deb10u1-Debian <<>> -t SOA lpi.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16695
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 3, ADDITIONAL: 6

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 185c67140a63baf46c4493215ef8906f7bfbe15bdca3b01a (good)
;; QUESTION SECTION:
;lpi.org.			IN	SOA

;; ANSWER SECTION:
lpi.org.		600	IN	SOA	dns1.easydns.com. zone.easydns.com. 1593109612 3600 600 1209600 300

;; AUTHORITY SECTION:
lpi.org.		81989	IN	NS	dns1.easydns.com.
lpi.org.		81989	IN	NS	dns2.easydns.net.
lpi.org.		81989	IN	NS	dns3.easydns.ca.

;; ADDITIONAL SECTION:
dns1.easydns.com.	168271	IN	A	64.68.192.10
dns2.easydns.net.	165815	IN	A	198.41.222.254
dns3.easydns.ca.	107	IN	A	64.68.196.10
dns1.easydns.com.	168271	IN	AAAA	2400:cb00:2049:1::a29f:1835
dns2.easydns.net.	165815	IN	AAAA	2400:cb00:2049:1::c629:defe

;; Query time: 94 msec
;; SERVER: 192.168.1.20#53(192.168.1.20)
;; WHEN: Sun Jun 28 08:43:27 EDT 2020
;; MSG SIZE  rcvd: 298
```
Dig has many options to fine tune both the output and query sent to the server. These options begin with `+`. One option is the `short` option, which suppresses all output except the result:
```
$ dig +short lpi.org
65.39.134.165
$ dig +short -t SOA lpi.org
dns1.easydns.com. zone.easydns.com. 1593109612 3600 600 1209600 300
```
Here is an example of turning off the cookie EDNS extension:
```
$ dig +nocookie -t MX lpi.org

; <<>> DiG 9.11.5-P4-5.1+deb10u1-Debian <<>> +nocookie -t MX lpi.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47774
;; flags: qr rd ra; QUERY: 1, ANSWER: 7, AUTHORITY: 3, ADDITIONAL: 5

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;lpi.org.			IN	MX

;; ANSWER SECTION:
lpi.org.		468	IN	MX	0 aspmx.l.google.com.
lpi.org.		468	IN	MX	10 aspmx4.googlemail.com.
lpi.org.		468	IN	MX	10 aspmx5.googlemail.com.
lpi.org.		468	IN	MX	10 aspmx2.googlemail.com.
lpi.org.		468	IN	MX	10 aspmx3.googlemail.com.
lpi.org.		468	IN	MX	5 alt2.aspmx.l.google.com.
lpi.org.		468	IN	MX	5 alt1.aspmx.l.google.com.

;; AUTHORITY SECTION:
lpi.org.		77130	IN	NS	dns2.easydns.net.
lpi.org.		77130	IN	NS	dns3.easydns.ca.
lpi.org.		77130	IN	NS	dns1.easydns.com.

;; ADDITIONAL SECTION:
dns1.easydns.com.	76140	IN	A	64.68.192.10
dns2.easydns.net.	73684	IN	A	198.41.222.254
dns1.easydns.com.	76140	IN	AAAA	2400:cb00:2049:1::a29f:1835
dns2.easydns.net.	73684	IN	AAAA	2400:cb00:2049:1::c629:defe

;; Query time: 2 msec
;; SERVER: 192.168.1.20#53(192.168.1.20)
;; WHEN: Mon Jun 29 10:18:58 EDT 2020
;; MSG SIZE  rcvd: 389
```
___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)