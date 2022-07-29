
# Linux Essentials Topic 4: The Linux Operating System

## Lesson 4.1 Choosing an Operating System

No matter if you are using your computer system at home, at the university or within an enterprise, there still has to be a decision made on the operating system that you will use. This decision may be made by you, especially if it is your computer, but you may also be responsible for making the choice across systems in your business. As always, being well-informed about the choices available will aid you in making a responsible decision. In this lesson we aim to help keep you informed of the operating system choices that you may be considering.

### What is an Operating System
One of the first things that we must be sure of before we begin our journey in choosing an operating system is to understand what we mean by the term. The operating system lies at the heart of your computer and allows applications to run within and on top of it. Additionally, the operating system will contain drivers to access the computer’s hardware such as disks and partitions, screens, keyboards, network cards and so on. We often abbreviate the operating system to simply the OS. Today there are many operating systems available for both business computer usage as well as for those looking for something in the home. If we want to simplify the selection available to us, we can group selections as follows:

- Linux-based Operating Systems
  - Enterprise Linux
  - Consumer Linux

- UNIX
- macOS
- Windows-based Operation Systems
  - Windows Servers
  - 
  Windows Desktops

### Choosing a Linux Distribution
### The Linux Kernel and Linux Distributions
When talking about Linux distributions the operating system is Linux. Linux is the kernel and at the core of every Linux distribution. The software of the Linux kernel is maintained by a group of individuals, lead by Linus Torvalds. Torvalds is employed by an industry consortium called The Linux Foundation to work on the Linux kernel.
```
Note
The Linux kernel was first developed by Linus Torvalds, a student from Finland, back in 1991. In 1992, the first Kernel release under the GNU General Public License version 2 (GPLv2) was version 0.12.
```
Linux Kernel
- As we have mentioned, all Linux distributions run the same operating system, Linux.

Linux Distribution
- When people talk about Red Hat Linux, or Ubuntu Linux they are referring to the Linux distribution. The Linux distribution will ship with a Linux kernel and an environment that makes the kernel useful in a way that we can interact with it. At a minimum we would need a command line shell such as Bash and a set of basic commands allowing us to access and manage the system. Often, of course, the Linux distribution will have a full Desktop Environment such as Gnome or KDE.

Even though each Linux distribution runs the Linux operating system, distributions can and do vary on the version of the operating system that is used. By this, we mean, the version of the Linux Kernel that is in use when the distribution boots.
```
Tip
If you have access to a Linux command line at the moment, you can easily check the version of the Linux kernel that you are running by reading the kernel release:

$ uname -r
4.15.0-1019-aws
```

### Types of Linux Distributions
It may seem an obvious choice to always run the latest version of the Linux kernel but it is not quite as simple as that. We can vaguely categorize Linux distributions into three sets:

- Enterprise Grade Linux Distributions
  - Red Hat Enterprise Linux
  - CentOS
  - SUSE Linux Enterprise Server
  - Debian GNU/Linux
  - Ubuntu LTS

- Consumer Grade Linux Distributions
  - Fedora
  - Ubuntu non-LTS
  - openSUSE

- Experimental and Hacker Linux Distributions
  - Arch
  - Gentoo

This, of course, is a just a very small subset of possible distributions but the importance is the difference between enterprise, consumer and experimental distributions and why each exists.

Enterprise Grade Linux
- Distributions such as CentOS (Community Enterprise OS) are designed to be deployed within large organizations using enterprise hardware. The needs of the enterprise are very different from the needs of the small business, hobbyist or home user. In order to ensure the availability of their services, enterprise users have higher requirements regarding the stability of their hard- and software. Therefore, enterprise Linux distributions tend to include older releases of the kernel and other software, which are known to work reliably. Often the distributions port important updates like security fixes back to these stable versions. In return, enterprise Linux distributions might lack support for the most recent consumer hardware and provide older versions of software packages. However, like consumer Linux distributions, enterprises tend to also choose mature hardware components and build their services on stable software versions.

Consumer Grade Linux
- Distributions such as Ubuntu are more targeted for small business or home and hobbyist users. As such, they are also likely to be using the latest hardware found on consumer grade systems. These systems will need the latest drivers to make the most of the new hardware but the maturity of both the hardware and the drivers is unlikely to meet the needs of larger enterprises. For the consumer market, however, the latest kernel is exactly what is needed with the most modern drivers even if they are little under tested. The newer Linux kernels will have the latest drivers to support the very latest hardware that are likely to be in use. Especially with the development we see with Linux in the gaming market it is tremendously important that the latest drivers are available to these users.
```
Note
Some distributions like Ubuntu provide both consumer grade versions which contain recent software and receive updates for a rather small period of time, as well as so-called Long Term Support versions, LTS for short, which are more suited for enterprise environments.
```

Experimental and Hacker Linux Distributions
- Distributions such as Arch Linux or Gentoo Linux live on the cutting edge of technology. They contain the most recent versions of software, even if these versions still contain bugs and untested features. In return, these distributions tend to use a rolling release model which allows them to deliver updates at any time. These distributions are used by advanced users who want to always receive the most recent software and are aware that functionality can break at any time and are able to repair their systems in such cases.

In short, when considering Linux as your operating system, if you are using enterprise grade hardware on your servers or desktops then you can make use of either enterprise grade or consumer grade Linux distributions. If you are using consumer grade hardware and need to make the most of the latest hardware innovations then you are likely to need a similar Linux distribution to match the needs of the hardware.

Some Linux distributions are related to each other. Ubuntu, for example, is based on Debian Linux and uses the same packaging system, DPKG. Fedora, as another example, is a testbed for RedHat Enterprise Linux, where potential features of future RHEL versions can be explored ahead of their availability in the enterprise distribution.

As well as the distributions we have mentioned here there are many other Linux distributions. One advantage that comes with Linux being open source software is that many people can develop what they think Linux should look like. As such we have many hundreds of distributions. To view more Linux distributions you may choose to visit The Distro Watch Web Site, the maintainers of the website list the top 100 downloads of Linux distributions, allowing you to compare and see what is currently popular.

### Linux Support Lifecycle
As you might expect, enterprise Linux distributions have a longer support life than consumer or community editions of Linux. For example Red Hat Enterprise Linux has support for 10 years. Red Hat Enterprise Linux 8 was launched in May 2019, while software updates and support are available until May 2029.

Consumer editions often will only have community support via forums. Software updates are often available for 3 releases. If we take Ubuntu as an example, at the time of writing 19.04 is the latest available having updates through the release of 19.10 and stopping in January 2020. Ubuntu also supply editions with long term support, known as LTS editions, which have 5 years of support from the original release. The current LTS version is 18.04 which will have software updates until 2023. These LTS versions make Ubuntu a possible option for the enterprise with commercial support available from Canonical (the company behind the Ubuntu brand) or independent consulting firms.

```
Note
The Ubuntu distributions use date-based version numbers in the format YY.MM: For example, version 19.04 was released April 2019.
```

### Linux as Your Desktop
Using Linux as your desktop system may be more challenging in an enterprise where desktop support focusses on commercial operating system offerings. However it is not only the support that may prove challenging. An enterprise customer may also have made large investments into software solutions that tie them into specific desktop operating systems. Having said this, there are many examples of Linux desktops being integrated into large organizations with companies like Amazon even having their own Linux distribution Amazon Linux 2. This is used on their AWS cloud platform but also internally for both servers and desktops.

Using Linux in a smaller business or at home becomes a lot easier and can be a rewarding experience, removing the need for licensing and opening your eyes to the wealth of free and open source software that is available for Linux. You will also find that there are many different desktop environments available. The most common being Gnome and KDE, however many others exists. The decision comes down to personal preference.

### Using Linux on Servers
Using Linux as your server operating system is common practice in the enterprise sector. Servers are maintained by engineers who specialize in Linux. So even with thousands of users, the users can remain ignorant of the servers that they are connecting to. The server operating system is not important to them and, in general, client applications will not differ between Linux and other operating systems in the backend. It is also true that as more applications are virtualized or containerized within local and remote clouds, the operating system is masked even more and the embedded operating system is likely to be Linux.

### Linux in the Cloud
Another opportunity to become familiar with Linux is to deploy Linux within one of the many public clouds available. Creating an account with one of the many others cloud providers will allow you to quickly deploy many different Linux distributions quickly and easily.

### Non Linux Operating Systems
Yes, incredible as it seems, there are operating systems that are not based on the Linux kernel. Of course, over the years there have been many and some have fallen by the wayside but there are still other choices that are available to you. Either at home or in the office.

### Unix
Before we had Linux as an operating system there was Unix. Unix used to be sold along with hardware and still today several commercial Unixes such as AIX and HP-UX are available on the market. While Linux was highly inspired by Unix (and the lack of its availability for certain hardware), the family of BSD operating systems is directly based on Unix. Today, FreeBSD, NetBSD and OpenBSD, along with some other related BSD systems, are available as free software.

Unix was heavily used in the enterprise but we have seen a decline in the fortunes of Unix with the growth of Linux. As Linux has grown and the enterprise support offerings have also grown, we have seen Unix slowly start to vanish. Solaris, originally from Sun before moving to Oracle, has recently disappeared. This was one of the larger Unix Operating Systems used by telecommunication companies, heralded as Telco Grade Unix.

Unix Operating Systems include:
- AIX
- FreeBSD, NetBSD, OpenBSD
- HP-UX
- Irix
- Solaris

### macOS
macOS (previously OS X) from Apple dates back to 2001. Based very much on BSD Unix, and making use of the Bash command line shell, it is a friendly system to use if you are used to using Unix or Linux operating systems. If you’re using macOS you can open the terminal application to access the command line. Running the same uname command again we can check the reported operating system:
```
$ uname -s
Darwin
```
```
Note
We make use of the option -s in this case to return the OS name. We previously used -r to return the kernel version number.
```

### Microsoft Windows
We can still say that the majority of desktops and laptops out there will be Windows based. The operating system has been truly successful and has dominated the desktop market for years. Although it is proprietary software and is not free, often the operating system license is included when you buy the hardware so it becomes the easy choice to make. There is, of course, wide support for Windows throughout hardware and software vendors as well of course many open source applications are also available for Windows. The future for Windows does not seem as bright as it has been. With fewer desktops and laptops being sold now the focus is on the tablet and phone market. This has been dominated by Apple and Android and it is hard for Microsoft to gain ground.

As a server platform Microsoft does now allow its customers to choose between a GUI (Graphical User Interface) and command line only version. The separation of the GUI and the command line is an important one. Most of the time the GUI of older Microsoft Servers will be loaded but no-one will use it. Consider an Active Directory Domain Controller…​ users use it all the time to authenticate to the domain, but it is managed remotely from administrators' desktops and not the server.


## Lesson 4.2 Understanding Computer Hardware

Without hardware, software is nothing more than another form of literature. Hardware processes the commands described by the software and provides mechanisms for storage, input, and output. Even the cloud is ultimately backed by hardware.

As an operating system, one of Linux' responsibilities is providing software with interfaces to access a system’s hardware. Most configuration specifics are beyond the scope of this lesson. However, users are often concerned with the performance, capacity, and other factors of system hardware since they impact the ability of a system to adequately support specific applications. This lesson discusses hardware as separate physical items using standard connectors and interfaces. The standards are relatively static. But the form factor, performance, and capacity characteristics of hardware are constantly evolving. Regardless of how changes may blur physical distinctions the conceptual aspects of hardware described in this lesson still apply.
```
Note
At various points within this lesson command line examples are used to demonstrate ways to access information about hardware. Most examples are from a Raspberry Pi B+ but should apply to most systems. Understanding these commands is not required to understand this material.
```
### Power Supplies
All of the active components in a computer system require electricity to operate. Unfortunately, most sources of electricity are not appropriate. Computer system hardware requires specific voltages with relatively tight tolerances. Which is not what is available from your local wall outlet.

Power supplies normalize available sources of power. Standard voltage requirements allow manufacturers to create hardware components that can be used in systems anywhere in the world. Desktop power supplies tend to use the electricity from wall outlets as a source. Server power supplies tend to be more critical so they can often connect to multiple sources to assure that they continue operating if one source fails.

Consuming power generates heat. Excessive heat can cause system components to operate slowly or even fail. Most systems have some form of fan to move air for more efficient cooling. Components such as processors often generate heat that air flow alone cannot dissipate. These hot components attach special fins known as heat sinks to help dissipate the heat they generate. Heat sinks often have their own small local fan to ensure adequate air flow.

### Motherboard
All of a system’s hardware needs to interconnect. A motherboard normalizes that interconnection using standardized connectors and form factors. It also provides support for the configuration and electrical needs of those connectors.

There are a large number of motherboard configurations. They support different processors and memory systems. They have different combinations of standardized connectors. And they adapt to the different sizes of the packaging that contains them. Except perhaps for the ability to connect specific external devices, motherboard configuration is effectively transparent to users. Administrators are mostly exposed to motherboard configuration when there is a need to identify specific devices.

When power is first applied there is motherboard specific hardware that must be configured and initialized before the system can operate. Motherboards use programming stored in nonvolatile memory known as firmware to deal with motherboard specific hardware. The original form of motherboard firmware was known as BIOS (Basic Input/Output System). Beyond basic configuration settings BIOS was mostly responsible for identifying, loading, and transferring operation to an operating system such as Linux. As hardware evolved, firmware expanded to support larger disks, diagnostics, graphical interfaces, networking, and other advanced capabilities independent of any loaded operating system. Early attempts to advance firmware beyond basic BIOS was often specific to a motherboard manufacturer. Intel defined a standard for advanced firmware known as EFI (Extensible Firmware Interface). Intel contributed EFI to a standards organization to create UEFI (Unified Extensible Firmware Interface). Today, most motherboards use UEFI. BIOS and EFI are almost never seen on recent systems. Regardless, most people still refer to motherboard firmware as BIOS.

There are very few firmware settings of interest to general users so only individuals responsible for system hardware configuration typically need to deal with firmware and its settings. One of the few commonly changed options is enabling virtualization extensions of modern CPUs.

### Memory
System memory holds the data and program code of currently running applications. When they talk about computer memory most people are referring to this system memory. Another common term used for system memory is the acronym RAM (Random Access Memory) or some variation of that acronym. Sometimes references to the physical packaging of the system memory such as DIMM, SIMM or DDR are also used.

Physically, system memory is usually packaged on individual circuit board modules that plug into the motherboard. Individual memory modules currently range in size from 2 GB to 64 GB. For most general purpose applications 4 GB is the minimum system memory people should consider. For individual workstations 16 GB is typically more than sufficient. However even 16 GB might be limiting for users running gaming, video, or high-end audio applications. Servers often require 128 GB or even 256 GB of memory to efficiently support user loads.

For the most part Linux allows users to treat system memory as a black box. An application is started and Linux takes care of allocating the system memory required. Linux releases the memory for use by other applications when an application completes. But what if an application requires more than the available system memory? In this case, Linux moves idle applications from system memory into a special disk area known as swap space. Linux moves idle applications from the disk swap space back to system memory when they need to run.

Systems without dedicated video hardware often use a portion of the system memory (often 1 GB) to act as video display storage. This reduces the effective system memory. Dedicated video hardware typically has its own separate memory that is not available as system memory.

There are several ways to obtain information about system memory. As a user, the total amount of memory available and in use are typically the values of interest. One source of information would be to run the command free along with the parameter -m to use megabytes in the output:
```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:            748          37          51          14         660         645
Swap:            99           0          99
```

The first line specifies the total memory available to the system (`total`), the memory in use (`used`) and the free memory (`free`). The second line displays this information for the swap space. Memory indicated as `shared` and `buff/cache` is currently used for other system functions, although the amount indicated in `available` could be used for application.

### Processors
The word “processor” implies that something is being processed. In computers most of that processing is dealing with electrical signals. Typically those signals are treated as having one of the binary values 1 or 0.

When people talk about computers they often use the word processor interchangeably with the acronym CPU (Central Processing Unit). Which is not technically correct. Every general purpose computer has a CPU that processes the binary commands specified by software. So it is understandable that people interchange processor and CPU. However, in addition to a CPU, modern computers often include other task specific processors. Perhaps the most recognizable additional processor is a GPU (Graphical Processing Unit). Thus, while a CPU is a processor, not all processors are CPUs.

For most people CPU architecture is a reference to the instructions that the processor supports. Although Intel and AMD make processors supporting the same instructions it is meaningful to differentiate by vendor because of vendor specific packaging, performance, and power consumption differences. Software distributions commonly use these designations to specify the minimum set of instructions they require to operate:

i386
- References the 32-bit instruction set associated with the Intel 80386.

x86
- Typically references the 32-bit instruction sets associated with successors to the 80386 such as 80486, 80586, and Pentium.

x64 / x86-64
- References processors that support both the 32-bit and 64-bit instructions of the x86 family.

AMD
- A reference to x86 support by AMD processors.

AMD64
- A reference to x64 support by AMD processors.

ARM
- References a Reduced Instruction Set Computer (RISC) CPU that is not based on the x86 instruction set. Commonly used by embedded, mobile, tablet, and battery operated devices. A version of Linux for ARM is used by the Raspberry Pi.

The file `/proc/cpuinfo` contains detailed information about a system’s processor. Unfortunately the details are not friendly to general users. A more general result can be obtained with the command `lscpu`. Output from a Raspberry Pi B+:
```
$ lscpu
Architecture:          armv7l
Byte Order:            Little Endian
CPU(s):                4
On-line CPU(s) list:   0-3
Thread(s) per core:    1
Core(s) per socket:    4
Socket(s):             1
Model:                 4
Model name:            ARMv7 Processor rev 4 (v7l)
CPU max MHz:           1400.0000
CPU min MHz:           600.0000
BogoMIPS:              38.40
Flags:                 half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt vfpd32 lpae evtstrm crc32
```

To most people the myriad of vendors, processor families, and specification factors represent a bewildering array of choices. Regardless, there are several factors associated with CPUs and processors that even general users and administrators often need to consider when they need to specify operational environments:

Bit size
- For CPUs this number relates to both the native size of data it manipulates and the amount of memory it can access. Most modern systems are either 32-bit or 64-bit. If an application needs access to more than 4 gigabytes of memory then it must run on a 64-bit system since 4 gigabytes is the maximum address that can be represented using 32 bits. And, while 32-bit applications can typically run on 64-bit systems, 64-bit applications cannot run on 32-bit systems.

Clock speed
- Often expressed in megahertz (MHz) or gigahertz (GHz). This relates to how fast a processor processes instructions. But processor speed is just one of the factors impacting system response times, wait times, and throughput. Even an active multi-tasking user rarely keeps a CPU of a common desktop PC active more than 2 or 3 percent of the time. Regardless, if you frequently use computationally intensive applications involving activities such as encryption or video rendering then CPU speed may have a significant impact on throughput and wait time.

Cache
- CPUs require a constant stream of both instructions and data in order to operate. The cost and power consumption of a multi-gigabyte system memory that could be accessed at CPU clock speeds would be prohibitive. CPU-speed cache memory is integrated onto the CPU chip to provide a high-speed buffer between CPUs and system memory. Cache is separated into multiple layers, commonly referenced as L1, L2, L3 and even L4. In the case of cache, more is often better.

Cores
- Core refers to an individual CPU. In addition to core representing a physical CPU, Hyper-Threading Technology (HTT) allows a single physical CPU to concurrently process multiple instructions thus virtually acting as multiple physical CPUs. Most typically, multiple physical cores are packaged as a single physical processor chip. However, there are motherboards that support multiple physical processor chips. In theory having more cores to process tasks would always seem to yield better system throughput. Unfortunately, desktop applications often only keep CPUs busy 2 or 3 percent of the time, so adding more mostly idle CPUs is likely to result in minimal improvement to throughput. More cores are best suited to running applications that are written to have multiple independent threads of operation such as video frame rendering, web page rendering, or multi-user virtual machine environments.

### Storage
Storage devices provide a method for retaining programs and data. Hard Disk Drives (HDDs) and Solid State Drives (SSDs) are the most common form of storage device for servers and desktops. USB memory sticks and optical devices such as DVD are also used but rarely as a primary device.

As the name implies, a hard disk drive stores information on one or more rigid physical disks. The physical disks are covered with magnetic media to make storage possible. The disks are contained within a sealed package since dust, small particles, and even finger prints would interfere with the ability of the HDD to read and write the magnetic media.

SSDs are effectively more sophisticated versions of USB memory sticks with significantly larger capacity. SSDs store information in microchips so there are no moving parts.

Although the underlying technologies for HDDs and SSDs are different, there are important factors that can be compared. HDD capacity is based on scaling physical components while SSD capacity depends on the number of microchips. Per gigabyte, SSDs cost between 3 and 10 times what an HDD costs. To read or write, an HDD must wait for a location on a disk to rotate to a known location while SSDs are random access. SSD access speeds are typically 3 to 5 times faster than HDD devices. Since they have no moving parts SSDs consume less power and are more reliable than HDDs.

Storage capacity is constantly increasing for HDDs and SSDs. Today, 5 terabyte HDDs and 1 terabyte SSDs are commonly available. Regardless, large storage capacity is not always better. When a storage device fails the information it contained is no longer available. And of course, backup takes longer when there is more information to back up. For applications which read and write a lot of data, latency and performance may be more important than capacity.

Modern systems use SCSI (Small Computer System Interface) or SATA (Serial AT Attachment) to connect with storage devices. These interfaces are typically supported by the appropriate connector on the motherboard. Initial load comes from a storage device attached to the motherboard. Firmware settings define the order in which devices are accessed for this initial loading.

Storage systems known as RAID (Redundant Array of Independent Disks) are a common implementation to avoid loss of information. A RAID array consists of multiple physical devices containing duplicate copies of information. If one device fails all of the information is still available. Different physical RAID configurations are referenced as 0, 1, 5, 6, and 10. Each designation has different storage size, performance characteristics and ways to store redundant data or checksums for data recovery. Beyond some administrative configuration overhead, the existence of RAID is effectively transparent to users.

Storage devices commonly read and write data as blocks of bytes. The lsblk command can be used to list the block devices available to a system. The following example was run on a Raspberry Pi using an SD card as a storage device. The details of the output are covered by information in the Partitions and Drivers lessons that follow:
```
$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
mmcblk0     179:0    0 29.7G  0 disk
+-mmcblk0p1 179:1    0 43.9M  0 part /boot
+-mmcblk0p2 179:2    0 29.7G  0 part /
```

### Partitions
A storage device is effectively a long sequence of storage locations. Partitioning is the mechanism that tells Linux if it is to see these storage locations as a single sequence or multiple independent sequences. Each partition is treated as if it is an individual device. Most of the time partitions are created when a system is first configured. If change is needed, administrative tools are available to manage device partitioning.

So why would multiple partitions be desirable? Some examples for using partitions would be managing available storage, isolating encryption overhead, or supporting multiple file systems. Partitions make it possible to have a single storage device that can boot under different operating systems.

While Linux can recognize the storage sequence of a raw device a raw device cannot be used as-is. To use a raw device it must be formatted. Formatting writes a file system to a device and prepares it for file operations. Without a file system a device cannot be used for file-related operations.

Users see partitions as if they are individual devices. This makes it easy to overlook the fact that they’re still dealing with a single physical device. In particular, device to device operations that are actually partition to partition operations will not have the expected performance. A single device is one physical mechanism with one set of read/write hardware. More importantly, you can’t use partitions of a single physical device as a fault tolerant design. If the device fails, all partitions fail so there would be no fault tolerance.
```
Note
Logical Volume Manager (LVM) is a software capability that allows administrators to combine individual disks and disk partitions and treat them as if they are a single drive.
```
### Peripherals
Servers and workstations need a combination of CPU, system memory, and storage to operate. But these fundamental components don’t directly interface with the external world. Peripherals are the devices that provide systems with input, output, and access to the rest of the real world.

Most motherboards have built-in external connectors and firmware support for common legacy peripheral interfaces supporting devices such as keyboard, mouse, sound, video, and network. Recent motherboards typically have an Ethernet connector to support networks, a HDMI connector supporting basic graphical needs, and one or more USB (Universal Serial Bus) connectors for mostly everything else. There are several versions of USB with different speed and physical characteristics. Several versions of USB ports are common on a single motherboard.

Motherboards may also have one or more expansion slots. Expansion slots allow users to add special circuit boards known as expansion cards that support custom, legacy, and non-standard peripherals. Graphics, sound, and network interfaces are common expansion cards. Expansion cards also support RAID, and special format legacy interfaces involving serial and parallel connections.

System on a Chip (SoC) configurations achieve power, performance, space, and reliability advantages over motherboard configurations by packaging processors, system memory, SSD, and hardware to control peripherals as a single integrated circuit package. The peripherals supported by SoC configurations are limited by the components packaged. Thus, SoC configurations tend to be developed for specific uses. Phones, tablets, and other portable devices are often based on SoC technology.

Some systems incorporate peripherals. Laptops are similar to workstations but incorporate default display, keyboard, and mouse peripherals. All-In-One systems are similar to laptops but require mouse and keyboard peripherals. Motherboard or SoC based controllers are often packaged with integral peripherals appropriate to a specific use.

### Drivers and Device Files
So far, this lesson has presented information about processors, memory, disks, partitioning, formatting and peripherals. But requiring general users to deal with the specific details for each of the devices in their system would make those systems unusable. Likewise, software developers would need to modify their code for every new or modified device they need to support.

The solution to this “dealing with the details” problem is provided by a device driver. Device drivers accept a standard set of requests then translate those requests into the device appropriate control activities. Device drivers are what allow you and the applications you run to read from the file `/home/carol/stuff` without worrying about whether that file is on a hard drive, solid state drive, memory stick, encrypted storage, or some other device.

Device files are found in the `/dev` directory and identify physical devices, device access, and supported drivers. By convention, in modern systems using SCSI or SATA based storage devices the specification filename starts with the prefix `sd`. The prefix is followed by a letter such as `a` or `b` indicating a physical device. After the prefix and device identifier comes a number indicating a partition within the physical device. So, `/dev/sda` would reference the entire first storage device while `/dev/sda3` would reference partition 3 in the first storage device. The device file for each type of device has a naming convention appropriate to the device. While covering all of the possible naming conventions is beyond the scope of this lesson it is important to remember that these conventions are critical to making system administration possible.

While it is beyond the scope of this lesson to cover the contents of the `/dev` directory it is informative to look at the entry for a storage device. Device files for SD cards typically use `mmcblk` as a prefix:
```
$ ls -l mmcblk*
brw-rw---- 1 root disk 179, 0 Jun 30 01:17 mmcblk0
brw-rw---- 1 root disk 179, 1 Jun 30 01:17 mmcblk0p1
brw-rw---- 1 root disk 179, 2 Jun 30 01:17 mmcblk0p2
```
The listing details for a device file are different from typical file details:

- Unlike a file or directory the first letter of the permissions field is `b`. This indicates that blocks are read from and written to the device in blocks rather than individual characters.

- The size field is two values separated by a comma rather than a single value. The first value generally indicates a particular driver within the kernel and the second value specifies a specific device handled by the driver.

- The filename uses a number for the physical device so the naming convention adapts by specifying the partition suffix as a `p` followed by a digit.

```
Note
Each system device should have an entry in /dev. Since the contents of the /dev directory are created at installation there are often entries for every possible driver and device even if no physical device exists.
```

### Lesson 4-3 Where Data is Stored

For an operating system, everything is considered data. For Linux, everything is considered a file: programs, regular files, directories, block devices (hard disks, etc.), character devices (consoles, etc.), kernel processes, sockets, partitions, links, etc. The Linux directory structure, starting from the root /, is a collection of files containing data. The fact that everything is a file is a powerful feature of Linux as it allows for tweaking virtually every single aspect of the system.

In this lesson we will be discussing the different locations in which important data is stored as established by the Linux Filesystem Hierarchy Standard (FHS). Some of these locations are real directories which store data persistently on disks, whereas others are pseudo filesystems loaded in memory which give us access to kernel subsystem data such as running processes, use of memory, hardware configuration and so on. The data stored in these virtual directories is used by a number of commands that allow us to monitor and handle it.

### Programs and their Configuration
Important data on a Linux system are — no doubt — its programs and their configuration files. The former are executable files containing sets of instructions to be run by the computer’s processor, whereas the latter are usually text documents that control the operation of a program. Executable files can be either binary files or text files. Executable text files are called scripts. Configuration data on Linux is traditionally stored in text files too, although there are various styles of representing configuration data.

### Where Binary Files are Stored
Like any other file, executable files live in directories hanging ultimately from `/`. More specifically, programs are distributed across a three-tier structure: the first tier (`/`) includes programs that can be necessary in single-user mode, the second tier (`/usr`) contains most multi-user programs and the third tier (`/usr/local`) is used to store software that is not provided by the distribution and has been compiled locally.

Typical locations for programs include:

`/sbin`
- It contains essential binaries for system administration such as `parted` or `ip`.

`/bin`
- It contains essential binaries for all users such as `ls`, `mv`, or `mkdir`.

`/usr/sbin`
- It stores binaries for system administration such as `deluser`, or `groupadd`.

`/usr/bin`
- It includes most executable files — such as `free`, `pstree`, `sudo` or `man` — that can be used by all users.

`/usr/local/sbin`
- It is used to store locally installed programs for system administration that are not managed by the system’s package manager.

`/usr/local/bin`
- It serves the same purpose as `/usr/local/sbin` but for regular user programs.

Recently some distributions started to replace `/bin` and `/sbin` with symbolic links to `/usr/bin` and `/usr/sbin`.
```
Note
The /opt directory is sometimes used to store optional third-party applications.
```

Apart from these directories, regular users can have their own programs in either:

- /home/$USER/bin

- /home/$USER/.local/bin
```
Tip
You can find out what directories are available for you to run binaries from by referencing the PATH variable with echo $PATH. For more information on PATH, review the lessons on variables and shell customization.
```
We can find the location of programs with the `which` command:
```
$ which git
/usr/bin/git
```
### Where Configuration Files are Stored
#### The /etc Directory

In the early days of Unix there was a folder for each type of data, such as `/bi`n for binaries and `/boot` for the kernel(s). However, `/etc` (meaning et cetera) was created as a catch-all directory to store any files that did not belong in the other categories. Most of these files were configuration files. With the passing of time more and more configuration files were added so `/etc` became the main folder for configuration files of programs. As said above, a configuration file usually is a local, plain text (as opposed to binary) file which controls the operation of a program.

In `/etc` we can find different patterns for config files names:

- Files with an ad hoc extension or no extension at all, for example

  - `group`
    - System group database.


  - `hostname`
    - Name of the host computer.

  - `hosts`
    - List of IP addresses and their hostname translations.

  - `passwd`
    - System user database — made up of seven fields separated by colons providing information about the user.

  - `profile`
    - System-wide configuration file for Bash.

  - `shadow`
    - Encrypted file for user passwords.

- Initialization files ending in `rc`:

  - `bash.bashrc`
    - System-wide `.bashrc` file for interactive bash shells.

  - `nanorc`
    - Sample initialization file for GNU nano (a simple text editor that normally ships with any distribution).

- Files ending in `.conf`:

  - `resolv.conf`
    - Config file for the resolver — which provide access to the Internet Domain Name System (DNS).

  - `sysctl.conf`
    - Config file to set system variables for the kernel.

Directories with the `.d` suffix:

Some programs with a unique config file (`*.conf` or otherwise) have evolved to have a dedicated `*.d` directory which help build modular, more robust configurations. For example, to configure logrotate, you will find `logrotate.conf`, but also the `logrotate.d` directories.

This approach comes in handy in those cases where different applications need configurations for the same specific service. If, for example, a web server package contains a logrotate configuration, this configuration can now be placed in a dedicated file in the `logrotate.d` directory. This file can be updated by the webserver package without interfering with the remaining logrotate configuration. Likewise, packages can add specific tasks by placing files in the `/etc/cron.d` directory instead of modifying `/etc/crontab`.

In Debian — and Debian derivatives — such an approach has been applied to the list of reliable sources read by the package management tool `apt`: apart from the classic `/etc/apt/sources.list`, now we find the `/etc/apt/sources.list.d` directory:

```
$ ls /etc/apt/sources*
/etc/apt/sources.list
/etc/apt/sources.list.d:
```

#### Configuration Files in HOME (Dotfiles)
At user level, programs store their configurations and settings in hidden files in the user’s home directory (also represented ~). Remember, hidden files start with a dot (`.`) — hence their name: dotfiles.

Some of these dotfiles are Bash scripts that customize the user’s shell session and are sourced as soon as the user logs into the system:

  - `.bash_history`
    - It stores the command line history.

  - `.bash_logout`
    - It includes commands to execute when leaving the login shell.

  - `.bashrc`
    - Bash’s initialization script for non-login shells.

  - `.profile`
    - Bash’s initialization script for login shells.
```
Note
Refer to the lesson on “Command Line Basics” to learn more about Bash and its init files.
```
Other user-specific programs' config files get sourced when their respective programs are started: `.gitconfig`, `.emacs.d`, `.ssh`, etc.

### The Linux Kernel
Before any process can run, the kernel must be loaded into a protected area of memory. After that, the process with PID `1` (more often than not systemd nowadays) sets off the chain of processes, that is to say, one process starts other(s) and so on. Once the processes are active, the Linux kernel is in charge of allocating resources to them (keyboard, mouse, disks, memory, network interfaces, etc).

```
Note
Prior to systemd, /sbin/init was always the first process in a Linux system as part of the System V Init system manager. In fact, you still find /sbin/init currently but linked to /lib/systemd/systemd.
```


### Where Kernels are Stored: `/boot`
The kernel resides in `/boot` — together with other boot-related files. Most of these files include the kernel version number components in their names (kernel version, major revision, minor revision and patch number).

The `/boot` directory includes the following types of files, with names corresponding with the respective kernel version:

  - `config-4.9.0-9-amd64`
    - Configuration settings for the kernel such as options and modules that were compiled along with the kernel.

  - `initrd.img-4.9.0-9-amd64`
    - Initial RAM disk image that helps in the startup process by loading a temporary root filesystem into memory.

  - `System-map-4.9.0-9-amd64`
    - The `System-map` (on some systems it will be named `System.map`) file contains memory address locations for kernel symbol names. Each time a kernel is rebuilt the file’s contents will change as the memory locations could be different. The kernel uses this file to lookup memory address locations for a particular kernel symbol, or vice-versa.

  - `vmlinuz-4.9.0-9-amd64`
    - The kernel proper in a self-extracting, space-saving, compressed format (hence the `z` in `vmlinuz`; `vm` stands for virtual memory and started to be used when the kernel first got support for virtual memory).

  - `grub`
    - Configuration directory for the `grub2` bootloader.

```
Tip
Because it is a critical feature of the operating system, more than one kernel and its associated files are kept in /boot in case the default one becomes faulty and we have to fall back on a previous version to — at least — be able to boot the system up and fix it.
```

### The `/proc` Directory
The `/proc` directory is one of the so-called virtual or pseudo filesystems since its contents are not written to disk, but loaded in memory. It is dynamically populated every time the computer boots up and constantly reflects the current state of the system. `/proc` includes information about:

- Running processes
- Kernel configuration
- System hardware

Besides all the data concerning processes that we will see in the next lesson, this directory also stores files with information about the system’s hardware and the kernel’s configuration settings. Some of these files include:

`/proc/cpuinfo`
 - It stores information about the system’s CPU:

```
$ cat /proc/cpuinfo
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 158
model name	: Intel(R) Core(TM) i7-8700K CPU @ 3.70GHz
stepping	: 10
cpu MHz		: 3696.000
cache size	: 12288 KB
(...)
```

`/proc/cmdline`
- It stores the strings passed to the kernel on boot:
```
$ cat /proc/cmdline
BOOT_IMAGE=/boot/vmlinuz-4.9.0-9-amd64 root=UUID=5216e1e4-ae0e-441f-b8f5-8061c0034c74 ro quiet
```

`/proc/modules`

It shows the list of modules loaded into the kernel:

```
$ cat /proc/modules
nls_utf8 16384 1 - Live 0xffffffffc0644000
isofs 40960 1 - Live 0xffffffffc0635000
udf 90112 0 - Live 0xffffffffc061e000
crc_itu_t 16384 1 udf, Live 0xffffffffc04be000
fuse 98304 3 - Live 0xffffffffc0605000
vboxsf 45056 0 - Live 0xffffffffc05f9000 (O)
joydev 20480 0 - Live 0xffffffffc056e000
vboxguest 327680 5 vboxsf, Live 0xffffffffc05a8000 (O)
hid_generic 16384 0 - Live 0xffffffffc0569000
(...)
```

### The /proc/sys Directory
This directory includes kernel configuration settings in files classified into categories per subdirectory:
```
$ ls /proc/sys
abi  debug  dev  fs  kernel  net  user  vm
```
Most of these files act like a switch and — therefore — only contain either of two possible values: `0` or `1` (“on” or “off”). For instance:

`/proc/sys/net/ipv4/ip_forward`

The value that enables or disables our machine to act as a router (be able to forward packets):
```
$ cat /proc/sys/net/ipv4/ip_forward
0
```
There are some exceptions, though:

`/proc/sys/kernel/pid_max`

The maximum PID allowed:
```
$ cat /proc/sys/kernel/pid_max
32768
```
```
Warning
Be extra careful when changing the kernel settings as the wrong value may result in an unstable system.
```
### Hardware Devices
Remember, in Linux “everything is a file”. This implies that hardware device information as well as the kernel’s own configuration settings are all stored in special files that reside in virtual directories.

### The `/dev` Directory
The device directory `/dev` contains device files (or nodes) for all connected hardware devices. These device files are used as an interface between the devices and the processes using them. Each device file falls into one of two categories:

Block devices
- Are those in which data is read and written in blocks which can be individually addressed. Examples include hard disks (and their partitions, like `/dev/sda1`), USB flash drives, CDs, DVDs, etc.

Character devices
- Are those in which data is read and written sequentially one character at a time. Examples include keyboards, the text console (`/dev/console`), serial ports (such as `/dev/ttyS0` and so on), etc.

When listing device files, make sure you use `ls` with the `-l` switch to differentiate between the two. We can — for instance — check for hard disks and partitions:
```
# ls -l /dev/sd*
brw-rw---- 1 root disk 8, 0 may 25 17:02 /dev/sda
brw-rw---- 1 root disk 8, 1 may 25 17:02 /dev/sda1
brw-rw---- 1 root disk 8, 2 may 25 17:02 /dev/sda2
(...)
```
Or for serial terminals (TeleTYpewriter):
```
# ls -l /dev/tty*
crw-rw-rw- 1 root tty     5,  0 may 25 17:26 /dev/tty
crw--w---- 1 root tty     4,  0 may 25 17:26 /dev/tty0
crw--w---- 1 root tty     4,  1 may 25 17:26 /dev/tty1
(...)
```
Notice how the first character is `b` for block devices and `c` for character devices.
```
Tip
The asterisk (*) is a globbing character than means 0 or more characters. Hence its importance in the ls -l /dev/sd* and ls -l /dev/tty* commands above. To learn more about these special characters, refer to the lesson on globbing.
```
Furthermore, `/dev` includes some special files which are quite useful for different programming purposes:

`/dev/zero`
- It provides as many null characters as requested.

`/dev/null`
 - Aka bit bucket. It discards all information sent to it.

`/dev/urandom`
 - It generates pseudo-random numbers.

### The /sys Directory
The sys filesystem (`sysfs`) is mounted on `/sys`. It was introduced with the arrival of kernel 2.6 and meant a great improvement on `/proc/sys`.

Processes need to interact with the devices in `/dev` and so the kernel needs a directory which contains information about these hardware devices. This directory is `/sys` and its data is orderly arranged into categories. For instance, to check on the MAC address of your network card (`enp0s3`), you would `cat` the following file:
```
$ cat /sys/class/net/enp0s3/address
08:00:27:02:b2:74
```

### Memory and Memory Types
Basically, for a program to start running, it has to be loaded into memory. By and large, when we speak of memory we refer to Random Access Memory (RAM) and — when compared to mechanical hard disks — it has the advantage of being a lot faster. On the down side, it is volatile (i.e., once the computer shuts down, the data is gone).

Notwithstanding the aforementioned — when it comes to memory — we can differentiate two main types in a Linux system:

Physical memory
- Also known as RAM, it comes in the form of chips made up of integrated circuits containing millions of transistors and capacitors. These, in turn, form memory cells (the basic building block of computer memory). Each of these cells has an associated hexadecimal code — a memory address — so that it can be referenced when needed.

Swap
- Also known as swap space, it is the portion of virtual memory that lives on the hard disk and is used when there is no more RAM available.

On the other hand, there is the concept of virtual memory which is an abstraction of the total amount of usable, addressing memory (RAM, but also disk space) as seen by applications.

`free` parses `/proc/meminfo` and displays the amount of free and used memory in the system in a very clear manner:

```
$ free
              total        used        free      shared  buff/cache   available
Mem:        4050960     1474960     1482260       96900     1093740     2246372
Swap:       4192252           0     4192252
```
Let us explain the different columns:

`total`
 - Total amount of physical and swap memory installed.

`used`
 - Amount of physical and swap memory currently in use.

`free`
 - Amount of physical and swap memory currently not in use.

`shared`
 - Amount of physical memory used — mostly — by tmpfs.

`buff/cache`
 - Amount of physical memory currently in use by kernel buffers and the page cache and slabs.0

`available`
 - Estimate of how much physical memory is available for new processes.

By default `free` shows values in kibibytes, but allows for a variety of switches to display its results in different units of measurement. Some of these options include:

`-b`
 - Bytes.

`-m`
 - Mebibytes.

`-g`
 - Gibibytes.

`-h`
 - Human-readable format.

`-h` is always comfortable to read:
```
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           3,9G        1,4G        1,5G         75M        1,0G        2,2G
Swap:          4,0G          0B        4,0G
```
```
Note

A kibibyte (KiB) equals 1,024 bytes while a kilobytes (KB) equals 1000 bytes. The same is respectively true for mebibytes, gibibytes, etc.
```

After exploring programs and their configuration files, in this lesson we will learn how commands are executed as processes. Likewise, we will be commenting on the system messaging, the use of the kernel ring buffer and how the arrival of `systemd` and its journal daemon — `journald` — changed the ways things had been done so far regarding system logging.

### Processes
Every time a user issues a command, a program is run and one or more processes are generated.

Processes exist in a hierarchy. After the kernel is loaded in memory on boot, the first process is initiated which — in turn — starts other processes, which, again, can start other processes. Every process has a unique identifier (`PID`) and parent process identifier (`PPID`). These are positive integers that are assigned in sequential order.

Exploring Processes Dynamically: `top`
You can get a dynamic listing of all running processes with the `top` command:

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

As we saw above, `top` can also give us information about memory and CPU consumption of the overall system as well as for each process.

`top` allows the user some interaction.

By default, the output is sorted by the percentage of CPU time used by each process in descending order. This behavior can be modified by pressing the following keys from within `top`:

`M`
- Sort by memory usage.

`N`
- Sort by process ID number.

`T`
 - Sort by running time.

`P`
 - Sort by percentage of CPU usage.

To switch between descending/ascending order just press `R`.
```
Tip
A fancier and more user-friendly version of top is htop. Another — perhaps more exhaustive — alternative is atop. If not already installed in your system, I encourage you to use your package manager to install them and give them a try.
```

### A Snapshot of Processes: `ps`
Another very useful command to get information about processes is `ps`. Whereas `top` provides dynamic information, that of `ps` is static.

If invoked without options, the output of `ps` is quite discrete and relates only to the processes attached to the current shell:
```
$ ps
  PID TTY          TIME CMD
 2318 pts/0    00:00:00 bash
 2443 pts/0    00:00:00 ps
```
The displayed information has to do with the process identifier (`PID`), the terminal in which the process is run (`TTY`), the CPU time taken by the process (`TIME`) and the command which started the process (CMD).

A useful switch for `ps` is `-f` which shows the full-format listing:
```
$ ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
carol     2318  1682  0 08:38 pts/1    00:00:00 bash
carol     2443  2318  0 08:46 pts/1    00:00:00 ps -f
```
In combination with other switches, `-f` shows the relationship between parent and child processes:
```
$ ps -uf
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
carol       2318  0.0  0.1  21336  5140 pts/1    Ss   08:38   0:00 bash
carol       2492  0.0  0.0  38304  3332 pts/1    R+   08:51   0:00  \_ ps -uf
carol       1780  0.0  0.1  21440  5412 pts/0    Ss   08:28   0:00 bash
carol       2291  0.0  0.7 305352 28736 pts/0    Sl+  08:35   0:00  \_ emacs index.en.adoc -nw
(...)
```
Likewise, `ps` can show the percentage of memory used when invoked with the `-v` switch:
```
$ ps -v
  PID TTY      STAT   TIME  MAJFL   TRS   DRS   RSS %MEM COMMAND
 1163 tty2     Ssl+   0:00      1    67 201224 5576  0.1 /usr/lib/gdm3/gdm-x-session (...)
 (...)
 ```
 ```
Note
Another visually attractive command that shows the hierarchy of processes is pstree. It ships with all major distributions.
```

### Process Information in the `/proc` Directory
We have already seen the `/proc` filesystem. `/proc` includes a numbered subdirectory for every running process in the system (the number is the `PID` of the process):
```
carol@debian:~# ls /proc
1    108  13   17   21   27   354  41   665  8    9
10   109  14   173  22   28   355  42   7    804  915
103  11   140  18   23   29   356  428  749  810  918
104  111  148  181  24   3    367  432  75   811
105  112  149  19   244  349  370  433  768  83
106  115  15   195  25   350  371  5    797  838
107  12   16   2    26   353  404  507  798  899
(...)
```

Thus, all the information about a particular process is included within its directory. Let us list the contents of the first process — that whose `PID` is `1` (the output has been truncated for readability):
```
# ls /proc/1/
attr        cmdline          environ  io        mem	      ns
autogroup   comm             exe      limits    mountinfo numa_maps
auxv        coredump_filter  fd       loginuid  mounts    oom_adj
...
```
You can check — for instance — the process executable:
```
# cat /proc/1/cmdline; echo
/sbin/init
```
As you can see, the binary that started the hierarchy of processes was `/sbin/init`.
```
Note
Commands can be concatenated with the semicolon (;). The point in using the echo command above is to provide a newline. Try and run simply cat /proc/1/cmdline to see the difference.
```

### The System Load
Each process on a system can potentially consume system resources. The so-called system load tries to aggregate the overall load of the system into a single numeric indicator. You can see the current load with the command `uptime`:
```
$ uptime
 22:12:54 up 13 days, 20:26,  1 user,  load average: 2.91, 1.59, 0.39
```
The three last digits indicate the system’s load average for the last minute (`2.91`), the last five minutes (`1.59`) and the last fifteen minutes (`0.39`), respectively.

Each of these numbers indicates how many processes were waiting either for CPU resources or for input/output operations to complete. This means that these processes were ready to run if they had received the respective resources.

### System Logging and System Messaging
As soon as the kernel and the processes start executing and communicating with each other, a lot of information is produced. Most of it is sent to files — the so-called log files or, simply, logs.

Without logging, searching for an event that happened on a server would give sysadmins many a headache, hence the importance of having a standardized and centralized way of keeping track of any system events. Besides, logs are determinant and telling when it comes to troubleshooting and security as well as reliable data sources for understanding system statistics and making trend predictions.

### Logging with the syslog Daemon
Traditionally, system messages have been managed by the standard logging facility — syslog — or any of its derivatives — syslog-ng or rsyslog. The logging daemon collects messages from other services and programs and stores them in log files, typically under /var/log. However, some services take care of their own logs (take — for example — the Apache HTTPD web server). Likewise, the Linux kernel uses an in-memory ring buffer for storing its log messages.

 #### Log Files in /var/log
Because logs are data that varies over time, they are normally found in `/var/log`.

If you explore `/var/log`, you will realize that the names of logs are — to a certain degree — quite self-explanatory. Some examples include:

`/var/log/auth.log`
 - It stores information about authentication.

`/var/log/kern.log`
- It stores kernel information.

`/var/log/syslog`
- It stores system information.

`/var/log/messages`
- It stores system and application data.

```
Note
The exact name and contents of log files may vary across Linux distributions.
```
### Accessing Log Files
When exploring log files, remember to be root (if you do not have reading permissions) and use a pager such as less;

```
# less /var/log/messages
Jun  4 18:22:48 debian liblogging-stdlog:  [origin software="rsyslogd" swVersion="8.24.0" x-pid="285" x-info="http://www.rsyslog.com"] rsyslogd was HUPed
Jun 29 16:57:10 debian kernel: [    0.000000] Linux version 4.9.0-8-amd64 (debian-kernel@lists.debian.org) (gcc version 6.3.0 20170516 (Debian 6.3.0-18+deb9u1) ) #1 SMP Debian 4.9.130-2 (2018-10-27)
Jun 29 16:57:10 debian kernel: [    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-8-amd64 root=/dev/sda1 ro quiet
```
Alternatively, you can use `tail` with the `-f` switch to read the most recent messages of the file and dynamically show new lines as they are appended:

```
# tail -f /var/log/messages
Jul  9 18:39:37 debian kernel: [    2.350572] RAPL PMU: hw unit of domain psys 2^-0 Joules
Jul  9 18:39:37 debian kernel: [    2.512802] input: VirtualBox USB Tablet as /devices/pci0000:00/0000:00:06.0/usb1/1-1/1-1:1.0/0003:80EE:0021.0001/input/input7
Jul  9 18:39:37 debian kernel: [    2.513861] Adding 1046524k swap on /dev/sda5.  Priority:-1 extents:1 across:1046524k FS
Jul  9 18:39:37 debian kernel: [    2.519301] hid-generic 0003:80EE:0021.0001: input,hidraw0: USB HID v1.10 Mouse [VirtualBox USB Tablet] on usb-0000:00:06.0-1/input0
Jul  9 18:39:37 debian kernel: [    2.623947] snd_intel8x0 0000:00:05.0: white list rate for 1028:0177 is 48000
Jul  9 18:39:37 debian kernel: [    2.914805] IPv6: ADDRCONF(NETDEV_UP): enp0s3: link is not ready
Jul  9 18:39:39 debian kernel: [    4.937283] e1000: enp0s3 NIC Link is Up 1000 Mbps Full Duplex, Flow Control: RX
Jul  9 18:39:39 debian kernel: [    4.938493] IPv6: ADDRCONF(NETDEV_CHANGE): enp0s3: link becomes ready
Jul  9 18:39:40 debian kernel: [    5.315603] random: crng init done
Jul  9 18:39:40 debian kernel: [    5.315608] random: 7 urandom warning(s) missed due to ratelimiting
```

You will find the output into the following format:
- Timestamp
- Hostname from which the message came from
- Name of program/service that generated the message
- The PID of the program that generated the message
- Description of the action that took place

Most log files are written in plain text; however, a few can contain binary data as is the case with `/var/log/wtmp` — which stores data relevant to successful logins. You can use the `file` command to determine which is the case:
```
$ file /var/log/wtmp
/var/log/wtmp: dBase III DBT, version number 0, next free block index 8
```
These files are normally read using special commands. last is used to interpret the data in `/var/log/wtmp`:
```
$ last
carol    tty2         :0               Thu May 30 10:53   still logged in
reboot   system boot  4.9.0-9-amd64    Thu May 30 10:52   still running
carol    tty2         :0               Thu May 30 10:47 - crash  (00:05)
reboot   system boot  4.9.0-9-amd64    Thu May 30 09:11   still running
carol    tty2         :0               Tue May 28 08:28 - 14:11  (05:42)
reboot   system boot  4.9.0-9-amd64    Tue May 28 08:27 - 14:11  (05:43)
carol    tty2         :0               Mon May 27 19:40 - 19:52  (00:11)
reboot   system boot  4.9.0-9-amd64    Mon May 27 19:38 - 19:52  (00:13)
carol    tty2         :0               Mon May 27 19:35 - down   (00:03)
reboot   system boot  4.9.0-9-amd64    Mon May 27 19:34 - 19:38  (00:04)
```
```
Note
Similar to /var/log/wtmp, /var/log/btmp stores information about failed login attempts and the special command to read its contents is lastb.
```

#### Log Rotation
Log files can grow a lot over a few weeks or months and take up all free disk space. To tackle this, the utility `logrotate` is used. It implements log rotation or cycling which implies actions such as moving log files to a new name, archiving and/or compressing them, sometimes emailing them to the sysadmin and eventually deleting them as they grow old. The conventions used for naming these rotated log files are diverse (adding a suffix with the date, for example); however, simply adding a suffix with an integer is commonplace:
```
# ls /var/log/apache2/
access.log  error.log  error.log.1  error.log.2.gz  other_vhosts_access.log
```

Note how error.log.2.gz has already been compressed with gzip (hence the .gz suffix).

### The Kernel Ring Buffer
The kernel ring buffer is a fixed-size data structure that records kernel boot messages as well as any live kernel messages. The function of this buffer — a very important one — is that of logging all the kernel messages produced on boot — when `syslog` is not yet available. The `dmesg` command prints the kernel ring buffer (which used to be also stored in `/var/log/dmesg`). Because of the extension of the ring buffer, this command is normally used in combination with the text filtering utility `grep` or a pager such as `less`. For instance, to search for boot messages:

```
$ dmesg | grep boot
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-9-amd64 root=UUID=5216e1e4-ae0e-441f-b8f5-8061c0034c74 ro quiet
[    0.000000] smpboot: Allowing 1 CPUs, 0 hotplug CPUs
[    0.000000] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-9-amd64 root=UUID=5216e1e4-ae0e-441f-b8f5-8061c0034c74 ro quiet
[    0.144986] AppArmor: AppArmor disabled by boot time parameter
(...)
```
```
Note
As the kernel ring buffer grows with new messages over time, the oldest ones will fade away.
```

### The System Journal: systemd-journald
As of 2015, systemd replaced SysV Init as a de facto system and service manager in most major Linux distributions. As a consequence, the journal daemon — journald — has become the standard logging component, superseding syslog in most aspects. The data is no longer stored in plain text but in binary form. Thus, the `journalctl` utility is necessary to read the logs. On top of that, journald is syslog compatible and can be integrated with syslog.

`journalctl` is the utility that is used to read and query systemd’s journal database. If invoked without options, it prints the entire journal:
```
# journalctl
-- Logs begin at Tue 2019-06-04 17:49:40 CEST, end at Tue 2019-06-04 18:13:10 CEST. --
jun 04 17:49:40 debian systemd-journald[339]: Runtime journal (/run/log/journal/) is 8.0M, max 159.6M, 151.6M free.
jun 04 17:49:40 debian kernel: microcode: microcode updated early to revision 0xcc, date = 2019-04-01
Jun 04 17:49:40 debian kernel: Linux version 4.9.0-8-amd64 (debian-kernel@lists.debian.org) (gcc version 6.3.0 20170516 (Debian 6.3.0-18+deb9u1) )
Jun 04 17:49:40 debian kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-8-amd64 root=/dev/sda1 ro quiet
(...)
```
However, if invoked with the `-k` or `--dmesg` switches, it will be equivalent to using the `dmesg` command:
```
# journalctl -k
[    0.000000] Linux version 4.9.0-9-amd64 (debian-kernel@lists.debian.org) (gcc version 6.3.0 20170516 (Debian 6.3.0-18+deb9u1) ) #1 SMP Debian 4.9.168-1+deb9u2 (2019-05-13)
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-4.9.0-9-amd64 root=UUID=5216e1e4-ae0e-441f-b8f5-8061c0034c74 ro quiet
(...)
```
Other interesting options for `journalctl` include:

`-b`, `--boot`
- It shows boot information.

`-u`
- It shows messages about a specified unit. Roughly, a unit can be defined as any resource handled by systemd. For example `journalctl -u apache2.service` is used to read messages about the `apache2` web server.

`-f`
 - It shows most recent journal messages and keeps printing new entries as they are appended to the journal — much like `tail -f`.

## Lesson 4.4 Your Computer on the Network

In today’s world, computing devices of any kind exchange information through networks. At the very heart of the concept of computer networks are the physical connections between a device and its peer(s). These connections are called links, and they are the most basic connection between two different devices. Links can be established through various media, such as copper cables, optical fibres, radio waves or lasers.

Each link is connected with an interface of a device. Each device can have multiple interfaces and thus be connected to multiple links. Through these links computers can form a network; a small community of devices that can directly connect to each other. There are numerous examples of such networks in the world. To be able to communicate beyond the scope of a link layer network, devices use routers. Think of link layer networks as islands connected by routers which connect the islands — just like bridges which information has to travel to reach a device which is part of a remote island.

This model leads to several different layers of networking:

Link Layer
- Handles the communication between directly connected devices.

Network Layer
- Handles routing outside of individual networks and the unique addressing of devices beyond a single link layer network.

Application Layer
- Enables individual programs to connect to each other.

When first invented, computer networks used the same methods of communication as telephones in that they were circuit switched. This means that a dedicated and direct link had to be formed between two nodes for them to be able to communicate. This method worked well, however, it required all the space on a given link for only two hosts to communicate.

Eventually computer networks moved over to something called packet switching. In this method the data is grouped up with a header, which contains information about where the information is coming from and where it’s going to. The actual content information is contained in this frame and sent over the link to the recipient indicated in the frame’s header. This allows for multiple devices to share a single link and communicate almost simultaneously.

### Link Layer Networking
The job of any packet is to carry information from the source to its destination through a link connecting both devices. These devices need a way to identify themselves to each other. This is the purpose of a link layer address. In an ethernet network, Media Access Control Addresses (MAC) are used to identify individual devices. A MAC address consists of 48 bits. They are not necessarily globally unique and cannot be used to address peers outside of the current link. Thus these addresses can not be used to route packets to another links. The recipient of a packet checks whether the destination address matches its own link layer and, if it does, processes the packet. Otherwise the packet is dropped. The exception to this rule is broadcast packets (a packet sent to everyone in a given local network) which are always accepted.

The command `ip link show` displays a list of all the available network interfaces and their link layer addresses as well as some other information about the maximum packet size:
```
$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:33:3b:25 brd ff:ff:ff:ff:ff:ff
```

The above output shows that the device has two interfaces, `lo` and `ens33`. `lo` is the loopback device and has the MAC address `00:00:00:00:00:00` while `ens33` is an ethernet interface and has the MAC address `00:0c:29:33:3b:25`.

### IPv4 Networking
To visit websites such as Google or Twitter, to check emails or to allow businesses to connect to each other, packets need to be able to roam from one link layer network to another. Often, these networks are connected just indirectly, with several intermediate link layer networks which packets have to cross to reach their actual destination.

The link layer addresses of a network interface cannot be used outside that specific link layer network. Since this address has no significance to devices in other link layer networks, a form of globally unique addresses are needed in order to implement routing. This addressing scheme, along with the overall concept of routing, is implemented by the Internet Protocol (IP).
```
Note
A protocol is a set of procedures of doing something so that all parties following the protocol are compatible to each other. A protocol can be seen as the definition of a standard. In computing, the Internet Protocol is a standard agreed upon by everyone so that different devices produced by different manufacturers can all communicate with each other.
```
### IPv4 Addresses
IP addresses, like MAC addresses, are a way to indicate where a data packet comes from and where it is going to. IPv4 was the original protocol. IPv4 addresses are 32 bits wide giving a theoretical maximum number of 4,294,967,296 addresses. However, the number of those addresses useable by devices is much smaller as some ranges are reserved for special use cases such as broadcast addresses (which are used to reach all participants of a specific network), multicast addresses (similar to broadcast addresses, but a device must tune in like a radio) or those reserved for private use.

In their human readable format IPv4 addresses are denoted as four digits separated by a dot. Each digit can range from `0` to `255`. For example, take the following IP address:
```
192.168.0.1
```
Technically, each of these digits represents eight individual bits. Thus this address could also be written like this:
```
11000000.10101000.00000000.00000001
```
In practice the decimal notation as seen above is used. However, the bitwise representation is still important to understand subnetting.

### IPv4 Subnets
In order to support routing, IP addresses can be split into two parts: the network and host portions. The network portion identifies the network that the device is on and is used to route packets to that network. The host portion is used to specifically identify a given device on a network and to hand the packet over to its specific recipient once it has reached its link layer network.

IP addresses can be broken into subnet and host parts at any point. The so-called subnet mask defines where this split happens. Let’s reconsider the binary representation of the IP address from the former example:
```
11000000.10101000.00000000.00000001
```
Now for this IP address, the subnet mask sets each bit which belongs to the network part to `1` and each bit that belongs to the host part to `0`:
```
11111111.11111111.11111111.00000000
```
In practice the netmask is written in the decimal notation:
```
255.255.255.0
```
This means that this network ranges from `192.168.0.0` to `192.168.0.255`. Note that the first three numbers, which have all bits set in the net mask, stay unchanged in the IP addresses.

Finally, there is an alternative notation for the subnet mask, which is called Classless Inter-Domain Routing (CIDR). This notation just indicates how many bits are set in the subnet mask and adds this number to the IP address. In the example, 24 out of 32 bits are set to `1` in the subnet mask. This can be expressed in CIDR notation as `192.168.0.1/24`

### Private IPv4 Addreses
As mentioned before, certain sections of the IPv4 address space are reserved for special use cases. One of these use cases are private address assignments. The following subnets are reserved for private addressing:

- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

Addresses out of these subnets can be used by anyone. However, these subnets can not be routed on the public internet as they are potentially used by numerous networks at the same time.

Today, most networks use these internal addresses. They allow internal communication without the need of any external address assignment. Most internet connections today just come with a single external IPv4 address. Routers map all the internal addresses to that single external IP address when forwarding packets to the internet. This is called Network Address Translation (NAT). The special case of NAT where a router maps internal addresses to a single external IP address is sometimes call masquerading. This allows any device on the inside network to establish new connections with any global IP address on the internet.
```
Note
With masquerading, the internal devices can not be referenced from the internet since they do not have a globally valid address. However, this is not a security feature. Even when using masquerading, a firewall is still needed.
```
### IPv4 Address Configuration
There are two main ways to configure IPv4 addresses on a computer. Either by assigning addresses manually or by using the Dynamic Host Configuration Protocol (DHCP) for automatic configuration.

When using DHCP, a central server controls which addresses are handed out to which devices. The server can also supply devices with other information about the network such as the IP addresses of DNS servers, the IP address of the default router or, in the case of more complicated setups, to start an operating system from the network. DHCP is enabled by default on many systems, therefore you will likely already have an IP address when you are connected to a network.

IP addresses can also be manually added to an interface using the command `ip addr add`. Here, we add the address `192.168.0.5` to the interface `ens33`. The network uses the netmask `255.255.255.0` which equals `/24` in CIDR notation:
```
$ sudo ip addr add 192.168.0.5/255.255.255.0 dev ens33
```
Now we can verify that the address was added using the `ip addr show` command:
```
$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
25: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:33:3b:25 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.5/24 192.168.0.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::010c:29ff:fe33:3b25/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

The above output shows both the `lo` interface and the `ens33` interface with its address assigned with the command above.

To verify the reachability of a device, the `ping` command can be used. It sends a special type of message called an echo request in which the sender asks the recipient for a response. If the connection between the two devices can be successfully established, the recipient will send back an echo reply, thus verifying the connection:
```
$ ping -c 3 192.168.0.1
PING 192.168.0.1 (192.168.0.1) 56(84) bytes of data.
64 bytes from 192.168.0.1: icmp_seq=1 ttl=64 time=2.16 ms
64 bytes from 192.168.0.1: icmp_seq=2 ttl=64 time=1.85 ms
64 bytes from 192.168.0.1: icmp_seq=3 ttl=64 time=3.41 ms

--- 192.168.0.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 5ms
rtt min/avg/max/mdev = 1.849/2.473/3.410/0.674 ms
```

The `-c 3` parameter makes `ping` stop after sending three echo requests. Otherwise, `ping` continues to run forever and has to be stopped by pressing `Ctrl+C`.

### IPv4 Routing
Routing is the process in which a packet gets from the source network to the destination network. Each device maintains a routing table which contains information about which IP network can be directly reached through the device’s attachment to link layer networks and which IP network can be reached by passing packets on to a router. Finally, a default route defines a router which receives all packets which did not match any other route.

When establishing a connection, the device looks up the target’s IP address in its routing table. If an entry matches the address, the packet is either sent to the respective link layer network or passed on to the router indicated in the routing table.

Routers themselves maintain routing tables, too. When receiving a packet, a router also looks up the destination address in its own routing table and sends the packet on to the next router. This is repeated until the packet arrives at the router on the destination network. Each router involved in this journey is called a hop. This last router finds a direct connected link for the target address in its routing table and sends the packets to its target interface.

Most home networks only have one way out, the singular router that came from the internet service provider (ISP). In this case a device just forwards all packets that aren’t for the internal network directly to the home router which will then send it to the provider’s router for further forwarding. This is an example of the default route.

The command `ip route show` lists the current IPv4 routing table:
```
$ ip route show
127.0.0.0/8 via 127.0.0.1 dev lo0
192.168.0.0/24 dev ens33 scope link
```
To add a default route, all that’s needed is the internal address of the router that’s going to be the default gateway. If, for example, the router has the address `192.168.0.1`, then the following command sets it up as a default route:
```
$ sudo ip route add default via 192.168.0.1
```
To verify, `run ip route` show again:
```
$ ip route show
default via 192.168.0.1 dev ens33
127.0.0.0/8 via 127.0.0.1 dev lo0
192.168.0.0/24 dev ens33 scope link
```

### IPv6 Networking
IPv6 was designed to deal with the shortcomings of IPv4, mainly the lack of addresses as more and more devices were being brought online. However, IPv6 also includes other features such as new protocols for automatic network configuration. Instead of 32 bits per address IPv6 uses 128. This allows for approximately 2128 addresses. However, like IPv4, the number of globally unique usable addresses is a lot smaller due to sections of the allocation being reserved for other uses. This large number of addresses means there are more than enough public addresses for every device currently connected to the internet and for many more to come, thus reducing the need for masquerading and its issues such as the delay during translation and the impossibility to directly connect to masqueraded devices.

IPv6 Addresses
Written down, the addresses use 8 groups of 4 hexadecimal digits each separated by a colon:
```
2001:0db8:0000:abcd:0000:0000:0000:7334
```

```
Note
Hexadecimal digits range from 0 to f, so each digit can contain one of 16 different values.
```

To make it easier leading zeros from each group can be removed when written down however each group must contain at least one digit:
```
2001:db8:0:abcd:0:0:0:7334
```

Where multiple groups containing only zeros follow directly after each other they may be entirely replaced by '::':
```
2001:db8:0:abcd::7334
```

However, this can only happen once in each address.

### IPv6 Prefix
The first 64 bits of an IPv6 address are known as the routing prefix. The prefix is used by routers to determine which network a device belongs to and therefore which path the data needs to be sent on. Subnetting always happens within the prefix. ISPs usually hand out /48 or /58 prefixes to their customers, leaving them 16 or 8 bits for their internal subnetting.

There are three major prefix types in IPv6:

Global Unique Address
-Wherein the prefix is assigned from the blocks reserved for global addresses. These addresses ware valid in the entire internet.

Unique Local Address
-May not be routed in the internet. They may, however, be routed internally within an organization. These addresses are used within a network to ensure the devices still have an address even when there is no internet connection. They are the equivalent of the private address ranges from IPv4. The first 8 bits are always `fc` or `fd`, followed by 40 randomly generated bits.

Link Local Address
-Are only valid on a particular link. Every IPv6 capable network interface has one such address, starting with `fe80`. These addresses are used internally by IPv6 to request additional addresses using automatic configuration and to find other computers on the network using the Neighbor Discovery protocol.

### IPv6 Interface Identifier
While the prefix determines in which network a device resides, the interface identifier is used to enumerate the devices within a network. The last 64 bits in an IPv6 address form the interface identifier, just like the last bits of an IPv4 address.

When an IPv6 address is assigned manually, the interface identifier is set as part of the address. When using automatic address configuration, the interface identifier is either chosen randomly or derived from the device’s link layer address. This makes a variation of the link layer address appear within the IPv6 address.

### IPv6 Address Configuration
Like IPv4, IPv6 address can be either assigned manually or automatically. However, IPv6 has two different types of automated configuration, DHCPv6 and Stateless Address Autoconfiguration (SLAAC).

SLAAC is the easier of the two automated methods and built right into the IPv6 standard. The messages use the new Neighbor Discovery Protocol which allows devices to find each other and request information regarding a network. This information is sent by routers and can contain IPv6 prefixes which the devices may use by combining them with an interface identifier of their choice, as long as the resulting address it not yet in use. The devices do not provide feedback to the router about the actual addresses they have created.

DHCPv6, on the other hand, is the updated DHCP made to work with the changes of IPv6. It allows for finer control over the information handed out to clients, like allowing for the same address to be handed out to the same client every time, and sending out more options to the client than SLAAC. With DHCPv6, clients need to get the explicit consent of a DHCP server in order to use an address.

The method to manually assign an IPv6 address to an interface is the same as with IPv4:
```
$ sudo ip addr add 2001:db8:0:abcd:0:0:0:7334/64 dev ens33

```
To verify the assignment has worked the same `ip addr show` command is used:

```
$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
25: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:33:3b:25 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.5/24 192.168.0.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::010c:29ff:fe33:3b25/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
    inet6 2001:db8:0:abcd::7334/64 scope global
       valid_lft forever preferred_lft forever
```
Here we also see the link-local address `fe80::010c:29ff:fe33:3b25/64`.

Like IPv4, the `ping` command can also be used to confirm the reachability of devices through IPv6:

```
$ ping 2001:db8:0:abcd::1
PING 2001:db8:0:abcd::1(2001:db8:0:abcd::1) 56 data bytes
64 bytes from 2001:db8:0:abcd::1: icmp_seq=1 ttl=64 time=0.030 ms
64 bytes from 2001:db8:0:abcd::1: icmp_seq=2 ttl=64 time=0.040 ms
64 bytes from 2001:db8:0:abcd::1: icmp_seq=3 ttl=64 time=0.072 ms

--- 2001:db8:0:abcd::1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 43ms
rtt min/avg/max/mdev = 0.030/0.047/0.072/0.018 ms
```
```
Note
On some Linux systems, ping does not support IPv6. These systems provide a dedicated ping6 command instead.
```

To verify the link-local address again use `ping` again. But since all interfaces use the `fe80::/64` prefix, the correct interface has to be specified along with the address:

```
$ ping6 -c 1 fe80::010c:29ff:fe33:3b25%ens33
PING fe80::010c:29ff:fe33:3b25(fe80::010c:29ff:fe33:3b25) 56 data bytes
64 bytes from fe80::010c:29ff:fe33:3b25%ens33: icmp_seq=1 ttl=64 time=0.049 ms

--- fe80::010c:29ff:fe33:3b25 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.049/0.049/0.049/0.000 ms
```

### DNS
IP addresses are difficult to remember and don’t exactly have a high coolness factor if you’re trying to market a service or product. This is where the Domain Name System comes into play. In its simplest form DNS is a distributed phone book that maps friendly rememberable domain names such as `example.com` to IP addresses. When, for example, a user navigates to a website, they enter the DNS hostname as part of the URL. The web browser then sends the DNS name to whichever DNS resolver has been configured. That DNS resolver will in turn find out the address that correlates to the domain. The resolver then replies with that address and the web browser tries to reach the web server at that IP address.

The resolvers that Linux uses to look up DNS data are configured in the `/etc/resolv.conf` configuration file:

```
$ cat /etc/resolv.conf
search lpi
nameserver 192.168.0.1
```

When the resolver performs a name lookup, it first checks the `/etc/hosts` file to see if it contains an address for the requested name. If it does, it returns that address and does not contact the DNS. This allows network administrators to provide name resolution without having to go through the effort of configuration a complete DNS server. Each line in that file contains one IP address followed by one or more names:

```
127.0.0.1          localhost.localdomain   localhost
::1                localhost.localdomain   localhost
192.168.0.10       server
2001:db8:0:abcd::f server
```

To perform a lookup in the DNS, use the command `host`:

```
$ host learning.lpi.org
learning.lpi.org has address 208.94.166.198
```
More detailed information can be retrieved using the command `dig`:
```
$ dig learning.lpi.org
; <<>> DiG 9.14.3 <<>> learning.lpi.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 21525
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 2ac55879b1adef30a93013705d3306d2128571347df8eadf (bad)
;; QUESTION SECTION:
;learning.lpi.org.		IN	A

;; ANSWER SECTION:
learning.lpi.org.	550	IN	A	208.94.166.198

;; Query time: 3 msec
;; SERVER: 192.168.0.1#53(192.168.0.1)
;; WHEN: Sat Jul 20 14:20:21 EST 2019
;; MSG SIZE  rcvd: 89
```

Here we also see the name of the DNS record types, in this case `A` for IPv4.

### Sockets
A socket is a communication endpoint for two programs talking to each other. If the socket is connected over a network, the programs can run on different devices, such as a web browser running on a user’s laptop and a web server running in a company’s data center.

There are three main types of sockets:

Unix Sockets
- Which connect processes running on the same device.

UDP (User Datagram Protocol) Sockets
- Which connect applications using a protocol which is fast but not resilient.

TCP (Transmission Control Protocol) Sockets
- Which are more reliable than UDP sockets and, for example, confirm the receipt of data.

Unix sockets can only connect applications running on the same device. TCP and UDP sockets however can connect over a network. TCP allows for a stream of data that always arrives in the exact order it was sent. UDP is more fire and forget; the packet is sent but its delivery at the other end is not guaranteed. UDP does however lack the overhead of TCP, making it perfect for low latency applications such as online video games.

TCP and UDP both use ports to address multiple sockets on the same IP address. While the source port for a connection is usually random, target ports are standardized for a specific service. HTTP is, for example, usually hosted at port 80, HTTPS is run on port 443. SSH, a protocol to securely log into a remote Linux system, listens on port 22.

The `ss` command allows an administrator to investigate all of the sockets on a Linux computer. It shows everything from the source address, destination address and type. One of its best features is the use of filters so a user can monitor the sockets in whatever connection state they would like. `ss` can be run with a set of options as well as a filter expression to limit the information shown.

When executed without any options, the command shows a list of all established sockets. Using the `-p` option includes information on the process using each socket. The `–s` option shows a summary of sockets. There are many more options available for this tool but the last set of major ones are `-4` and `-6` for narrowing down the IP protocol to either IPv4 or IPv6 respectively, `-t` and `-u` allow the administrator to select TCP or UDP sockets and `-l` to show only socket which listen for new connections.

The following command, for example, lists all TCP sockets currently in use:

```
$ ss -t
State       Recv-Q  Send-Q    Local Address:Port      Peer Address:Port
ESTAB       0       0           192.168.0.5:49412      192.168.0.1:https
ESTAB       0       0           192.168.0.5:37616      192.168.0.1:https
ESTAB       0       0           192.168.0.5:40114      192.168.0.1:https
ESTAB       0       0           192.168.0.5:54948      192.168.0.1:imap
...
```


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/010-160/)