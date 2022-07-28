# Linux Essentials

## Lesson 1-1
### Introduction
Linux is one of the most popular operating systems. Its development was started in 1991 by Linus Torvalds. The operating system was inspired by Unix, another operating system developed in the 1970s by AT&T Laboratories. Unix was geared towards small computers. At the time, “small” computers were considered machines that don’t need an entire hall with air conditioning and cost less than one million dollars. Later, they were considered as the machines that can be lifted by two people. At that time, an affordable Unix system was not readily available on computers such as office computers, which were tended to be based on the x86 platform. Therefore Linus, who was a student by that time, started to implement a Unix-like operating system which was supposed to run on this platform.

Mostly, Linux uses the same principles and basic ideas of Unix, but Linux itself doesn’t contain Unix code, as it is an independent project. Linux is not backed by an individual company but by an international community of programmers. As it is freely available, it can be used by anyone without restrictions.

### Distributions
A Linux distribution is a bundle that consists of a Linux kernel and a selection of applications that are maintained by a company or user community. A distribution’s goal is to optimize the kernel and the applications that run on the operating system for a certain use case or user group. Distributions often include distribution-specific tools for software installation and system administration. This is why some distributions are mainly used for desktop environments where they need to be easy to use while others are mainly used to run on servers to use the available resources as efficient as possible.

Another way to classify distributions is by referring to the distribution family they belong to. Distributions of the Debian distribution family use the package manager dpkg to manage the software that is run on the operating system. Packages that can be installed with the package manager are maintained by voluntary members of the distribution’s community. Maintainers use the deb package format to specify how the software is installed on the operating system and how it is configured by default. Just like a distribution, a package is a bundle of software and a corresponding configuration and documentation that makes it easier for the user to install, update and use the software.

The Debian GNU/Linux distribution is the biggest distribution of the Debian distribution family. The Debian GNU/Linux Project was launched by Ian Murdock in 1993. Today thousands of volunteers are working on the project. Debian GNU/Linux aims to provide a very reliable operating system. It also promotes Richard Stallman’s vision of an operating system that respects the freedoms of the user to run, study, distribute and improve the software. This is why it does not provide any proprietary software by default.

Ubuntu is another Debian-based distribution worth mentioning. Ubuntu was created by Mark Shuttleworth and his team in 2004, with the mission to bring an easy to use Linux desktop environment. Ubuntu’s mission is to provide a free software to everyone across the world as well as to cut the cost of professional services. The distribution has a scheduled release every six months with a long-term support release every 2 years.

Red Hat is a Linux distribution developed and maintained by the identically named software company, which was acquired by IBM in 2019. The Red Hat Linux distribution was started in 1994 and re-branded in 2003 to Red Hat Enterprise Linux, often abbreviated as RHEL. It is provided to companies as a reliable enterprise solution that is supported by Red Hat and comes with software that aims to ease the use of Linux in professional server environments. Some of its components require fee-based subscriptions or licenses. The CentOS project uses the freely available source code of Red Hat Enterprise Linux and compiles it to a distribution which is available completely free of charge, but in return does not come with commercial support.

Both RHEL and CentOS are optimized for use in server environments. The Fedora project was founded in 2003 and creates a Linux distribution which is aimed at desktop computers. Red Hat initiated and maintains the Fedora distribution ever since. Fedora is very progressive and adopts new technologies very quickly and is sometimes considered a test-bed for new technologies which later might be included in RHEL. All Red Hat based distributions use the package format rpm.

The company SUSE was founded in 1992 in Germany as a Unix service provider. The first version of SUSE Linux was released in 1994. Over the years SUSE Linux became mostly known for its YaST configuration tool. This tool allows administrators to install and configure software and hardware, set up servers and networks. Similar to RHEL, SUSE releases SUSE Linux Enterprise Server, which is their commercial edition. This is less frequently released and is suitable for enterprise and production deployment. It is distributed as a server as well as a desktop environment, with fit-for-purpose packages. In 2004, SUSE released the openSUSE project, which opened opportunities for developers and users to test and further develop the system. The openSUSE distribution is freely available to download.

Independent distributions have been released over the years. Some of them are based on either Red Hat or Ubuntu, some are designed to improve a specific propriety of a system or hardware. There are distributions built with specific functionalities like QubesOS, a very secure desktop environment, or Kali Linux, which provides an environment for exploiting software vulnerabilities, mainly used by penetration testers. Recently various super small Linux distributions are designed to specifically run in Linux containers, such as Docker. There are also distributions built specifically for components of embedded systems and even smart devices.

### Embedded Systems
Embedded systems are a combination of computer hardware and software designed to have a specific function within a larger system. Usually they are part of other devices and help to control these devices. Embedded systems are found in automotive, medical and even military applications. Due to its wide variety of applications, a variety of operating systems based on the Linux kernel was developed in order to be used in embedded systems. A significant part of smart devices have a Linux kernel based operating system running on it.

Therefore, with embedded systems comes embedded software. The purpose of this software is to access the hardware and make it usable. The major advantages of Linux over any proprietary embedded software include cross vendor platform compatibility, development, support and no license fees. Two of the most popular embedded software projects are Android, that is mainly used on mobile phones across a variety of vendors and Raspbian, which is used mainly on Raspberry Pi.

### Android
Android is mainly a mobile operating system developed by Google. Android Inc. was founded in 2003 in Palo Alto, California. The company initially created an operating system meant to run on digital cameras. In 2005, Google bought Android Inc. and developed it to be one of the biggest mobile operating systems.

The base of Android is a modified version of the Linux kernel with additional open source software. The operating system is mainly developed for touchscreen devices, but Google has developed versions for TV and wrist watches. Different versions of Android have been developed for game consoles, digital cameras, as well as PCs.

Android is freely available in open source as Android Open Source Project (AOSP). Google offers a series of proprietary components in addition to the open source core of Android. These components include applications such as Google Calendar, Google Maps, Google Mail, the Chrome browser as well as the Google Play Store which facilitates the easy installation of apps. Most users consider these tools an integral part of their Android experience. Therefore almost all mobile devices shipped with Android in Europe and America include proprietary Google software.

Android on embedded devices has many advantages. The operating system is intuitive and easy to use with a graphical user interface, it has a very wide developer community, therefore it is easy to find help for development. It is also supported by the majority of the hardware vendors with an Android driver, therefore it is easy and cost effective to prototype an entire system.

### Raspbian and the Raspberry Pi
Raspberry Pi is a low cost, credit-card sized computer that can function as a full-functionality desktop computer, but it can be used within an embedded Linux system. It is developed by the Raspberry Pi Foundation, which is an educational charity based in UK. It mainly has the purpose to teach young people to learn to program and understand the functionality of computers. The Raspberry Pi can be designed and programmed to perform desired tasks or operations that are part of a much more complex system.

The specialties of the Raspberry Pi include a set of General Purpose Input-Output (GPIO) pins which can be used to attach electronic devices and extension boards. This allows using the Raspberry Pi as a platform for hardware development. Although it was intended for educational purposes, Raspberry Pis are used today in various DIY projects as well as for industrial prototyping when developing embedded systems.

The Raspberry Pi uses ARM processors. Various operating systems, including Linux, run on the Raspberry Pi. Since the Raspberry Pi does not contain a hard disk, the operating system is started from an SD memory card. One of the most prominent Linux distributions for the Raspberry Pi is Raspbian. As the name suggests, it belongs to the Debian distribution family. It is customized to be installed on the Raspberry Pi hardware and provides more than 35000 packages optimized for this environment. Besides Raspbian, numerous other Linux distributions exist for the Raspberry Pi, like, for example, Kodi, which turns the Raspberry Pi into a media center.

### Linux and the Cloud
The term cloud computing refers to a standardized way of consuming computing resources, either by buying them from a public cloud provider or by running a private cloud. As of 2017 reports, Linux runs 90% of the public cloud workload. Every cloud provider, from Amazon Web Services (AWS) to Google Cloud Platform (GCP), offers different forms of Linux. Even Microsoft offers Linux-based virtual machines in their Azure cloud today.

Linux is usually offered as part of Infrastructure as a Service (IaaS) offering. IaaS instances are virtual machines which are provisioned within minutes in the cloud. When starting an IaaS instance, an image is chosen which contains the data that is deployed to the new instance. Cloud providers offer various images containing ready to run installations of both popular Linux distributions as well as own versions of Linux. The cloud user chooses an image containing their preferred distribution and can access a cloud instance running this distribution shortly after. Most cloud providers add tools to their images to adjust the installation to a specific cloud instance. These tools can, for example, extend the file systems of the image to fit the actual hard disk of the virtual machine.


## Lesson 1-2

An application is a computer program whose purpose is not directly tied to the inner workings of the computer, but with tasks performed by the user. Linux distributions offer many application options to perform a variety of tasks, such as office applications, web browsers, multimedia players and editors, etc. There is often more than one application or tool to perform a particular job. It is up to the user to choose the application which best fits their needs.

## Software Packages
Almost every Linux distribution offers a pre-installed set of default applications. Besides those pre-installed applications, a distribution has a package repository with a vast collection of applications available to install through its package manager. Although the various distributions offer roughly the same applications, several different package management systems exist for various distributions. For instance, Debian, Ubuntu and Linux Mint use the dpkg, apt-get and apt tools to install software packages, generally referred as DEB packages. Distributions such as Red Hat, Fedora and CentOS use the rpm, yum and dnf commands instead, which in turn install RPM packages. As the application packaging is different for each distribution family, it is very important to install packages from the correct repository designed to the particular distribution. The end user usually doesn’t have to worry about those details, as the distribution’s package manager will choose the right packages, the required dependencies and future updates. Dependencies are auxiliary packages needed by programs. For example, if a library provides functions for dealing with JPEG images which are used by multiple programs, this library is likely packaged in its own package on which all applications using the library depend.

The commands dpkg and rpm operate on individual package files. In practice, almost all package management tasks are performed by the commands apt-get or apt on systems that use DEB packages or by yum or dnf on systems that use RPM packages. These commands work with catalogues of packages, can download new packages and their dependencies, and check for newer versions of the installed packages.

## Package Installation
Suppose you have heard about a command called figlet which prints enlarged text on the terminal and you want to try it. However, you get the following message after executing the command figlet:

```
$ figlet
-bash: figlet: command not found
```

That probably means the package is not installed on your system. If your distribution works with DEB packages, you can search its repositories using apt-cache search package_name or apt search package_name. The apt-cache command is used to search for packages and to list information about available packages. The following command looks for any occurrences of the term “figlet” in the package’s names and descriptions:

```
$ apt-cache search figlet
figlet - Make large character ASCII banners out of ordinary text
```

The search identified a package called figlet that corresponds to the missing command. The installation and removal of a package require special permissions granted only to the system’s administrator: the user named root. On desktop systems, ordinary users can install or remove packages by prepending the command sudo to the installation/removal commands. That will require you to type your password to proceed. For DEB packages, the installation is performed with the command apt-get install package_name or apt install package_name:

```
$ sudo apt-get install figlet
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  figlet
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
```
At this point the package will be downloaded and installed on the system. Any dependencies that the package eventually needs will also be downloaded and installed:

```
Need to get 184 kB of archives.
After this operation, 741 kB of additional disk space will be used.
Get:1 http://archive.raspbian.org/raspbian stretch/main armhf figlet armhf 2.2.5-2 [184 kB]
Fetched 184 kB in 0s (213 kB/s)
Selecting previously unselected package figlet.
(Reading database ... 115701 files and directories currently installed.)
Preparing to unpack .../figlet_2.2.5-2_armhf.deb ...
Unpacking figlet (2.2.5-2) ...
Setting up figlet (2.2.5-2) ...
update-alternatives: using /usr/bin/figlet-figlet to provide /usr/bin/figlet (figlet) in auto mode
Processing triggers for man-db (2.7.6.1-2) ...
```
After the download is finished, all files are copied to the proper locations, any additional configuration will be performed and the command will become available:
```
$ figlet Awesome!
    _                                         _
   / \__      _____  ___  ___  _ __ ___   ___| |
  / _ \ \ /\ / / _ \/ __|/ _ \| '_ ` _ \ / _ \ |
 / ___ \ V  V /  __/\__ \ (_) | | | | | |  __/_|
/_/   \_\_/\_/ \___||___/\___/|_| |_| |_|\___(_)
```
In distributions based on RPM packages, searches are performed using yum search package_name or dnf search package_name. Let’s say you want to display some text in a more irreverent way, followed by a cartoonish cow, but you are not sure about the package that can perform that task. As with the DEB packages, the RPM search commands accept descriptive terms:

```
$ yum search speaking cow
Last metadata expiration check: 1:30:49 ago on Tue 23 Apr 2019 11:02:33 PM -03
==================== Name & Summary Matched: speaking, cow ====================
cowsay.noarch : Configurable speaking/thinking cow
After finding a suitable package at the repository, it can be installed with yum install package_name or dnf install package_name:

$ sudo yum install cowsay
Last metadata expiration check: 2:41:02 ago on Tue 23 Apr 2019 11:02:33 PM -03.
Dependencies resolved.
==============================================================================
 Package         Arch           Version               Repository         Size
==============================================================================
Installing:
 cowsay          noarch         3.04-10.fc28          fedora             46 k

Transaction Summary
==============================================================================
Install  1 Package

Total download size: 46 k
Installed size: 76 k
Is this ok [y/N]: y
```
Once again, the desired package and all its possible dependencies will be downloaded and installed:
```
Downloading Packages:
cowsay-3.04-10.fc28.noarch.rpm                    490 kB/s |  46 kB     00:00
==============================================================================
Total                                              53 kB/s |  46 kB     00:00
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
Preparing        :                                                          1/1
Installing       : cowsay-3.04-10.fc28.noarch                               1/1
Running scriptlet: cowsay-3.04-10.fc28.noarch                               1/1
Verifying        : cowsay-3.04-10.fc28.noarch                               1/1

Installed:
cowsay.noarch 3.04-10.fc28

Complete!
```
The command cowsay does exactly what its name implies:
```
$ cowsay "Brought to you by yum"
 _______________________
< Brought to you by yum >
 -----------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
Although they may seem useless, commands figlet and cowsay provide a way to draw the attention of other users to relevant information.

### Package Removal
The same commands used to install packages are used to remove them. All the commands accept the remove keyword to uninstall an installed package: apt-get remove package_name or apt remove package_name for DEB packages and yum remove package_name or dnf remove package_name for RPM packages. The sudo command is also needed to perform the removal. For example, to remove the previously installed package figlet from a DEB-based distribution:

```
$ sudo apt-get remove figlet
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages will be REMOVED:
  figlet
0 upgraded, 0 newly installed, 1 to remove and 0 not upgraded.
After this operation, 741 kB disk space will be freed.
Do you want to continue? [Y/n] Y
```
After confirming the operation, the package is erased from the system:
```
(Reading database ... 115775 files and directories currently installed.)
Removing figlet (2.2.5-2) ...
Processing triggers for man-db (2.7.6.1-2) ...
```
A similar procedure is performed on an RPM-based system. For example, to remove the previously installed package cowsay from an RPM-based distribution:

```
$ sudo yum remove cowsay
Dependencies resolved.
==================================================================================
 Package          Arch             Version                Repository         Size
==================================================================================
Removing:
 cowsay           noarch           3.04-10.fc28           @fedora            76 k

Transaction Summary
==================================================================================
Remove  1 Package

Freed space: 76 k
Is this ok [y/N]: y
```
Likewise, a confirmation is requested and the package is erased from the system:
```
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                          1/1
  Erasing          : cowsay-3.04-10.fc28.noarch                               1/1
  Running scriptlet: cowsay-3.04-10.fc28.noarch                               1/1
  Verifying        : cowsay-3.04-10.fc28.noarch                               1/1

Removed:
  cowsay.noarch 3.04-10.fc28

Complete!
```
The configuration files of the removed packages are kept on the system, so they can be used again if the package is reinstalled in the future.

### Office Applications
Office applications are used for editing files such as texts, presentations, spreadsheets and other formats commonly used in an office environment. These applications are usually organised in collections called office suites.

For a long time, the most used office suite in Linux was the OpenOffice.org suite. OpenOffice.org was an open source version of the StarOffice suite released by Sun Microsystems. A few years later Sun was acquired by Oracle Corporation, which in turn transferred the project to the Apache Foundation and OpenOffice.org was renamed to Apache OpenOffice. Meanwhile, another office suite based on the same source code was released by the Document Foundation, which named it LibreOffice.

The two projects have the same basic features and are compatible with the document formats from Microsoft Office. However, the preferred document format is the Open Document Format, a fully open and ISO standardized file format. The use of ODF files ensures that documents can be transferred between operating systems and applications from different vendors, such as Microsoft Office. The main applications offered by OpenOffice/LibreOffice are:

Writer
- Text editor

Calc
- Spreadsheets

Impress
- Presentations

Draw
- Vector drawing

Math
- Math formulas

Base
- Database

Both LibreOffice and Apache OpenOffice are open source software, but LibreOffice is licensed under LGPLv3 and Apache OpenOffice is licensed under Apache License 2.0. The licensing distinction implies that LibreOffice can incorporate improvements made by Apache OpenOffice, but Apache OpenOffice cannot incorporate improvements made by LibreOffice. That, and a more active community of developers, are the reason most distributions adopt LibreOffice as their default office suite.

### Web Browsers
For most users, the main purpose of a computer is to provide access to the Internet. Nowadays, web pages can act as a full featured app, with the advantage of being accessible from anywhere, without the need of installing extra software. That makes the web browser the most important application of the operating system, at least for the average user.

```
### Tip
One of the best sources for learning about web development is the MDN Web Docs, available at https://developer.mozilla.org/. Maintained by Mozilla, the site is full of tutorials for beginners and reference materials on most modern web technologies.
```

The main web browsers in the Linux environment are Google Chrome and Mozilla Firefox. Chrome is a web browser maintained by Google but is based on the open source browser named Chromium, which can be installed using the distribution’s package manager and is fully compatible with Chrome. Maintained by Mozilla, a non-profit organization, Firefox is a browser whose origins are linked to Netscape, the first popular web browser to adopt the open source model. The Mozilla Foundation is deeply involved with the development of the open standards underlying the modern web.

Mozilla also develops other applications, like the e-mail client Thunderbird. Many users opt to use webmail instead of a dedicated email application, but a client like Thunderbird offers extra features and integrates best with other applications on the desktop.

### Multimedia
Compared to the available web applications, desktop applications are still the best option for the creation of multimedia content. Multimedia related activities like video rendering often require high amounts of system resources, which are best managed by a local desktop application. Some of the most popular multimedia applications for the Linux environment and their uses are listed below.

Blender
- A 3D renderer to create animations. Blender can also be used to export 3D objects to be printed by a 3D printer.

GIMP
- A full-featured image editor, which can be compared with Adobe Photoshop, but has its own concepts and tools to work with images. GIMP can be used to create, edit and save most bitmap files, like JPEG, PNG, GIF, TIFF and many others.

Inkscape
- A vector graphics editor, similar to Corel Draw or Adobe Illustrator. Inkscape’s default format is SVG, which is an open standard for vector graphics. SVG files can be opened by any web browser and, due to its nature as a vector graphic, it can be used in flexible layouts of web pages.

Audacity
- An audio editor. Audacity can be used to filter, to apply effects and to convert between many different audio formats, like MP3, WAV, OGG, FLAC, etc.

- ImageMagick
ImageMagick is a command line tool to convert and edit most image file types. It can also be used to create PDF documents from image files and vice versa.

There are also many applications dedicated to multimedia playback. The most popular application for video playback is VLC, but some users prefer other alternatives, like smplayer. Local music playback also has many options, like Audacious, Banshee and Amarok, which can also manage a collection of local sound files.

### Server Programs
When a web browser loads a page from a website, it actually connects to a remote computer and asks for a specific piece of information. In that scenario, the computer running the web browser is called the client and the remote computer is called the server.

The server computer, which can be an ordinary desktop computer or specialized hardware, needs a specific program to manage each type of information it will provide. Regarding serving web pages, most servers around the world deploy open source server programs. This particular server program is called an HTTP server (HTTP stands for Hyper Text Transfer Protocol) and the most popular ones are Apache, Nginx and lighttpd.

Even simple web pages may require many requests, which can be ordinary files — called static content — or dynamic content rendered from various sources. The role of an HTTP server is to collect and send all the requested data back to the browser, which then arranges the content as defined by the received HTML document (HTML stands for Hyper Text Markup Language) and other supporting files. Therefore, the rendering of a web page involves operations performed on the server side and operations performed on the client side. Both sides can use custom scripts to accomplish specific tasks. On the HTTP server side, it is quite common to use the PHP scripting language. JavaScript is the scripting language used on the client side (the web browser).

Server programs can provide all kinds of information. It’s not uncommon to have a server program requesting information provided by other server programs. That is the case when an HTTP server requires information provided by a database server.

For instance, when a dynamic page is requested, the HTTP server usually queries a database to collect all the required pieces of information and sends the dynamic content back to the client. In a similar way, when a user registers on a website, the HTTP server gathers the data sent by the client and stores it in a database.

A database is an organized set of information. A database server stores contents in a formatted fashion, making it possible to read, write and link large amounts of data reliably and at great speed. Open source database servers are used in many applications, not only on the Internet. Even local applications can store data by connecting to a local database server. The most common type of database is the relational database, where the data is organized in predefined tables. The most popular open source relational databases are MariaDB (originated from MySQL) and PostgreSQL.

### Data Sharing
In local networks, like the ones found in offices and homes, it is desirable that computers not only should be able to access the Internet, but also should be able to communicate with each other. Sometimes a computer acts as a server, sometimes the same computer acts as a client. That is necessary when one wants to access files on another computer in the network — for instance, access a file stored on a desktop computer from a portable device — without the hassle of copying it to a USB drive or the like.

Between Linux machines, NFS (Network File System) is often used. The NFS protocol is the standard way to share file systems in networks equipped only with Unix/Linux machines. With NFS, a computer can share one or more of its directories with specific computers on the network, so they can read and write files in these directories. NFS can even be used to share an entire operating system’s directory tree with clients that will use it to boot from. These computers, called thin clients, are mostly often used in large networks to avoid the maintenance of each individual operating system on each machine.

If there are other types of operating systems attached to the network, it is recommended to use a data sharing protocol that can be understood by all of them. This requirement is fulfilled by Samba. Samba implements a protocol for sharing files over the network originally made for the Windows operating system, but today is compatible with all major operating systems. With Samba, computers in the local network not only can share files, but also printers.

On some local networks, the authorization given upon login on a workstation is granted by a central server, called the domain controller, which manages the access to various local and remote resources. The domain controller is a service provided by Microsoft’s Active Directory. Linux workstations can associate with a domain controller by using Samba or an authentication subsystem called SSSD. As of version 4, Samba can also work as a domain controller on heterogeneous networks.

If the goal is to implement a cloud computing solution able to provide various methods of web based data sharing, two alternatives should be considered: ownCloud and Nextcloud. The two projects are very similar because Nextcloud is a spin-off of ownCloud, which is not unusual among open source projects. Such spin-offs are usually called a fork. Both provide the same basic features: file sharing and sync, collaborative workspaces, calendar, contacts and mail, all through desktop, mobile and web interfaces. Nextcloud also provides private audio/video conferencing, whilst ownCloud is more focused on file sharing and integration with third-party software. Many more features are provided as plugins which can be activated later as needed.

Both ownCloud and Nextcloud offer a paid version with extra features and extended support. What makes them different from other commercial solutions is the ability to install Nextcloud or ownCloud on a private server, free of charge, avoiding keeping sensitive data on an unknown server. As all the services depend on HTTP communication and are written in PHP, the installation must be performed on a previous configured web server, like Apache. If you consider installing ownCloud or Nextcloud on your own server, make sure to also enable HTTPS to encrypt all connections to your cloud.

### Network Administration
Communication between computers is only possible if the network is working correctly. Normally, the network configuration is done by a set of programs running on the router, responsible for setting up and checking the network availability. In order to achieve this, two basic network services are used: DHCP (Dynamic Host Configuration Protocol) and DNS (Domain Name System).

DHCP is responsible for assigning an IP address to the host when a network cable is connected or when the device enters a wireless network. When connecting to the Internet, the ISP’s DHCP server will provide an IP address to the requesting device. A DHCP server is very useful in local area networks also, to automatically provide IP addresses to all connected devices. If DHCP is not configured or if it’s not working properly, it would be necessary to manually configure the IP address of each device connected to the network. It is not practical to manually set the IP addresses on large networks or even in small networks, that’s why most network routers come with a DHCP server pre-configured by default.

The IP address is required to communicate with another device on an IP network, but domain names like www.lpi.org are much more likely to be remembered than an IP number like 203.0.113.165. The domain name by itself, however, is not enough to establish the communication through the network. That is why the domain name needs to be translated to an IP address by a DNS server. The IP address of the DNS server is provided by the ISP’s DHCP server and it’s used by all connected systems to translate domain names to IP addresses.

Both DHCP and DNS settings can be modified by entering the web interface provided by the router. For instance, it is possible to restrict the IP assignment only to known devices or associate a fixed IP address to specific machines. It’s also possible to change the default DNS server provided by the ISP. Some third-party DNS servers, like the ones provided by Google or OpenDNS, can sometimes provide faster responses and extra features.

### Programming Languages
All computer programs (client and server programs, desktop applications and the operating system itself) are made using one or more programming languages. Programs can be a single file or a complex system of hundreds of files, which the operating system treats as an instruction sequence to be interpreted and performed by the processor and other devices.

There are numerous programming languages for very different purposes and Linux systems provide a lot of them. Since open source software also includes the sources of the programs, Linux systems offer developers perfect conditions to understand, modify or create software according to their own needs.

Every program begins as a text file, called source code. This source code is written in a more or less human-friendly language that describes what the program is doing. A computer processor can not directly execute this code. In compiled languages, the source code is therefore be converted to a binary file which can then be executed by the computer. A program called compiler is responsible for doing the conversion from source code to executable form. Since the compiled binary is specific to one kind of processor, the program might have to be re-compiled to run on another type of computer.

In interpreted languages, the program does not need to be previously compiled. Instead, an interpreter reads the source code and executes its instruction every time the program is run. This makes the development easier and faster, but at the same time interpreted programs tend to be slower than compiled programs.

Here some of the most popular programming languages:

JavaScript
- JavaScript is a programming language mostly used in web pages. In its origins, JavaScript applications were very simple, like form validation routines. As for today, JavaScript is considered a first class language and it is used to create very complex applications not only on the web, but on servers and mobile devices.

C
- The C programming language is closely related with operating systems, particularly Unix, but it is used to write any kind of program to almost any kind of device. The great advantages of C are flexibility and speed. The same source code written in C can be compiled to run in different platforms and operating systems, with little or no modification. After being compiled, however, the program will run only in the targeted system.

Java
- The main aspect of Java is that programs written in this language are portable, which means that the same program can be executed in different operating systems. Despite the name, Java is not related to JavaScript.

Perl
- Perl is a programming language most used to process text content. It has a strong regular expressions emphasis, which makes Perl a language suited for text filtering and parsing.

Shell
- The shell, particularly the Bash shell, is not just a programming language, but an interactive interface to run other programs. Shell programs, known as shell scripts, can automate complex or repetitive tasks on the command line environment.

Python
- Python is a very popular programming language among students and professionals not directly involved with computer science. Whilst having advanced features, Python is a good way to start learning programming for its easy to use approach.

PHP
- PHP is most used as a server side scripting language for generating content for the web. Most online HTML pages are not static files, but dynamic content generated by the server from various sources, like databases. PHP programs — sometimes just called PHP pages or PHP scripts — are often used to generate this kind of content. The term LAMP comes from the combination of a Linux operating system, an Apache HTTP server, a MySQL (or MariaDB) database and PHP programming. LAMP servers are a very popular solution for running web servers. Besides PHP, all of the programming languages described before can be used to implement such applications too.

C and Java are compiled languages. In order to be executed by the system, source code written in C is converted to binary machine code, whereas Java source code is converted to bytecode executed in a special software environment called Java Virtual Machine. JavaScript, Perl, Shell script, Python and PHP are all interpreted languages, which are also called scripting languages.

___

This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/010-160/)