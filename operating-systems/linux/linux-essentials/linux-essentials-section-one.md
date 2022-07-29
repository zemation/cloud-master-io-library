# Linux Essentials Section 1

## Lesson 1-1 Linux Evolution and Popular Operating Systems
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


## Lesson 1-2 Major Open Source Applications

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
Tip
> One of the best sources for learning about web development is the MDN Web Docs, available at https://developer.mozilla.org/. Maintained by Mozilla, the site is full of tutorials for beginners and reference materials on most modern web technologies.
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

## Lesson 1-3 Open Source Software and Licensing

While the terms free software and open source software are widely used, there are still some misconceptions about their meaning. In particular, the concept of “freedom” needs closer examination. Let’s start with the definition of the two terms.

### Definition of Free and Open Source Software
### Criteria of Free Software
First of all, “free” in the context of free software has nothing to do with “free of charge”, or as the founder of the Free Software Foundation (FSF), Richard Stallman, succinctly puts it:

>To understand the concept, you should think of “free” as in “free speech,” not as in “free beer”.
>— Richard Stallman

What is free software?
Regardless of whether you have to pay for the software or not, there are four criteria which constitute free software. Richard Stallman describes these criteria as “the four essential freedoms”, the counting of which he starts from zero:

- “The freedom to run the program as you wish, for any purpose (freedom 0).”

- Where, how and for what purpose the software is used can neither be prescribed nor restricted.

- “The freedom to study how the program works, and change it so it does your computing as you wish (freedom 1). Access to the source code is a precondition for this.”

Everyone may change the software according to their ideas and needs. This in turn presupposes that the so-called source code, i.e. all files of which a software consists, must be available in a form readable by programmers. And, of course, this right applies to a single user who may want to add a single feature, as well as to software companies that build complex systems such as smartphone operating systems or router firmware.

- “The freedom to redistribute copies so you can help others (freedom 2).”

- This freedom explicitly encourages each user to share the software with others. It is therefore a matter of the widest possible distribution and thus the widest possible community of users and developers who, on the basis of these freedoms, further develop and improve the software for the benefit of all.

- “The freedom to distribute copies of your modified versions to others (freedom 3). By doing this you can give the whole community a chance to benefit from your changes. Access to the source code is a precondition for this.”

- This is not only about the distribution of free software, but about the distribution of modified free software. Anyone who makes changes to free software has the right to make the changes available to others. If they do so, they are obliged to do so freely as well, i.e. they must not restrict the original freedoms when distributing the software, even if they modified or extended it. For example, if a group of developers has different ideas about the direction of a specific software than the original authors, it can split off its own development branch (called a fork) and continue it as a new project. But, of course, all obligations associated with these freedoms remain.

The emphasis on the idea of freedom is also consistent insofar as every freedom movement is directed against something, namely an opponent who suppresses the postulated freedoms, who regards software as property and wants to keep it under lock and key. In contrast to free software, such software is called proprietary.

### Open Source Software vs. Free Software
For many, free software and open source software are synonyms. The frequently used abbreviation FOSS for Free and Open Source Software emphasizes this commonality. FLOSS for Free/Libre and Open Source Software is another popular term, which unmistakably emphasizes the idea of freedom also for other languages other than English. However, if one considers the origin and development of both terms, a differentiation is worthwhile.

The term free software with the definition of the described four freedoms goes back to Richard Stallman and the GNU project founded by him in 1985 — almost 10 years before the emergence of Linux. The name “GNU is not Unix” describes the intention with a wink of the eye: GNU started as an initiative to develop a technically convincing solution — namely the operating system Unix — from scratch, to make it available to the general public and to improve it continuously with the general public. The openness of the source code was merely a technical and organizational necessity for this, but in its self-image the free software movement is still a social and political — some also say ideological — movement.

With the success of Linux, the collaborative possibilities of the Internet, and the thousands of projects and companies that emerged in this new software cosmos, the social aspect increasingly receded into the background. The openness of the source code itself changed from a technical requirement to a defining feature: as soon as the source code was visible, the software was considered “open source”. The social motives gave way to a more pragmatic approach to software development.

Free software and open source software work on the same thing, with the same methods and in a worldwide community of individuals, projects and companies. But since they have come together from different directions — one social and one pragmatic-technical — there are sometimes conflicts. These conflicts arise when the results of the joint work do not correspond to the original goals of both movements. This happens especially when software reveals its sources but does not respect the four freedoms of free software at the same time, for example when there are restrictions on disclosure, change, or connections with other software components.

The license under which the software is available determines which conditions a software is subject to with regard to use, distribution and modification. And because requirements and motives can be very different, countless different licenses have been created in the FOSS area. Due to the much more fundamental approach of the free software movement, it is not surprising that it does not recognize many open source licenses as “free” and therefore rejects them. Conversely, this is hardly the case due to the much more pragmatic open source approach.

Let’s take a brief look at the actually very complex area of licenses below.

### Licenses
Unlike a refrigerator or a car, software is not a physical product, but a digital product. Thus, a company cannot actually transfer ownership of such a product by selling it and changing the physical possession — rather, it transfers the rights of use to that product, and the user contractually agrees to those rights of use. Which rights of use these are and above all are not is recorded in the software license, and thus it becomes understandable how important the regulations contained therein are.

While large vendors of proprietary software, such as Microsoft or SAP, have their own licenses that are precisely tailored to their products, the advocates of free and open source software have from the outset striven for clarity and general validity of their licenses, because after all, every user should understand them and, if necessary, use them himself for his own developments.

However, it should not be concealed that this ideal of simplicity can hardly be achieved because too many specific requirements and internationally not always compatible legal understandings stand in the way of this. To give just one example: German and American copyright law are fundamentally different. According to German law there is one person as author (more precisely: Urheber), whose work is his intellectual property. While the author can grant permission to use his work, he can not assign or give up his authorship. The latter is alien to American law. Here, too, there is an author (who, however, can also be a company or an institution), but he only has exploitation rights which he can transfer in part or in full and thus completely detach himself from his work. An internationally valid licence must be interpreted with respect of different legislation.

The consequences are numerous and sometimes very different FOSS licenses. Worse, still, are different versions of a license, or a mix of licenses (within a project, or even when connecting multiple projects) which can cause confusion or even legal disputes.

Both the representatives of free software and the advocates of the clearly economically oriented open source movement created their own organizations, which today are decisively responsible for the formulation of software licenses according to their principles and support their members in their enforcement.

### Copyleft
The already mentioned Free Software Foundation (FSF) has formulated the GNU General Public License (GPL) as one of the most important licenses for free software, which is used by many projects, e.g. the Linux kernel. In addition, it has released licenses with case-specific customizations, such as the GNU Lesser General Public License (LGPL), which governs the combination of free software with modifications made to code where the source code for the modifications do not have to be released to the public, the GNU Affero General Public License (AGPL), which covers selling access to hosted software, or the GNU Free Documentation License (FDL), which extends freedom principles to software documentation. In addition, the FSF makes recommendations for or against third-party licenses, and affiliated projects such as GPL-Violations.org investigate suspected violations of free licenses.

The FSF calls the principle according to which a free license also applies to modified variants of the software copyleft — in contrast to the principle of restrictive copyright which it rejects. The idea, therefore, is to transfer the liberal principles of a software license as unrestrictedly as possible to future variants of the software in order to prevent subsequent restrictions.

What sounds obvious and simple, however, leads to considerable complications in practice, which is why critics often call the copyleft principle “viral”, since it is transmitted to subsequent versions.

From what has been said it follows, for example, that two software components that are licensed under different copyleft licenses might not be combinable with each other, since both licenses cannot be transferred to the subsequent product at the same time. This can even apply to different versions of the same license!

For this reason, newer licenses or license versions often no longer grasp the copyleft so rigorously. Already the mentioned GNU Lesser General Public License (LGPL) is in this sense a concession to be able to connect free software with “non-free” components, as it is frequently done with so-called libraries. Libraries contain subroutines or routines, which in turn are used by various other programs. This leads to the common situation where proprietary software calls such a subroutine from a free library.

Another way to avoid license conflicts is dual licensing, where one software is licensed under different licenses, e.g. a free license and a proprietary license. A typical use case is a free version of a software which might only be used when respecting the copyleft restrictions and the alternative offering to obtain the software under a different license which frees the licensee from certain restriction in return for a fee which could be used to fund the development of the software.

It should therefore become clear that the choice of license for software projects should be made with much caution, since the cooperation with other projects, the combinability with other components and also the future design of the own product depend on it. The copyleft presents developers with special challenges in this respect.

### Open Source Definition and Permissive Licenses
On the open source side, it is the Open Source Initiative (OSI), founded in 1998 by Eric S. Raymond and Bruce Perens, which is mainly concerned with licensing issues. It has also developed a standardized procedure for checking software licenses for compliance with its Open Source Definition. More than 80 recognized open source licenses can currently be found on the OSI website.

Here they also list licenses as “OSI-approved” that explicitly contradict the copyleft principle, especially the BSD licenses group. The Berkeley Software Distribution (BSD) is a variant of the Unix operating system originally developed at the University of Berkeley, which later gave rise to free projects such as NetBSD, FreeBSD and OpenBSD. The licenses underlying these projects are often referred to as permissive. In contrast to copyleft licenses, they do not have the aim of establishing the terms of use of modified variants. Rather, the maximum freedom should help the software to be as widely distributed as possible by leaving the editors of the software alone to decide how to proceed with the edits — whether, for example, they also release them or treat them as closed source and distribute them commercially.

The 2-Clause BSD License, also called Simplified BSD License or FreeBSD License, proves how reduced such a permissive license can be. In addition to the standardized liability clause, which protects developers from liability claims arising from damage caused by the software, the license consists of only the following two rules:

- Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

  - Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

  - Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

### Creative Commons
The successful development concept of FLOSS and the associated technological progress led to attempts to transfer the open source principle to other, non-technical areas. The preparation and provision of knowledge, as well as the creative cooperation in solving complex tasks, are now regarded as evidence of the extended, content-related open source principle.

This led to the need to create reliable foundations in these areas as well, according to which work results can be shared and processed. Since the available software licenses were hardly suitable for this, there were numerous attempts to convert the specific requirements from scientific work to digitized works of art “in the spirit of open source” into similarly handy licenses.

By far the most important initiative of this kind today is Creative Commons (CC), which summarizes its concerns as follows:

Creative Commons is a global nonprofit organization that enables sharing and reuse of creativity and knowledge through the provision of free legal tools.
— https://creativecommons.org/faq/#what-is-creative-commons-and-what-do-you-do
With Creative Commons, the focus of rights assignment goes back from the distributor to the author. An example: In traditional publishing, an author usually transfers all publishing rights (printing, translation, etc.) to a publisher, who in turn ensures the best possible distribution of the work. The significantly changed distribution channels of the Internet now put the author in a position to exercise many of these publishing rights herself and to decide for herself how her work may be used. Creative Commons gives the opportunity to determine this simply and legally reliably, but Creative Commons wants more: authors are encouraged to make their works available as a contribution to a general process of exchange and cooperation. Unlike traditional copyright, which gives the author all the rights that they can transfer to others as needed, the Creative Commons approach takes the opposite approach: the author makes her work available to the community, but can choose from a set of features those that need to be considered when using the work — the more features she chooses, the more restrictive the license.

And so the “Choose a License” principle of CC asks an author step by step for the individual properties and generates the recommended license, which the author can last assign to the work as text and icon.

For a better understanding, here is an overview of the six possible combinations and licenses offered by CC:

CC BY (“Attribution”)
- The free license that allows anyone to edit and distribute the work as long as they name the author.

CC BY-SA (“Attribution-ShareAlike”)
- As CC BY, except that the modified work may only be distributed under the same license. The principle reminds of the copyleft, because the license is “inherited” here as well.

CC BY-ND (“Attribution-NoDerivatives”)
- Like CC BY, except that the work may only be passed on unmodified.

CC BY-NC (“Attribution-NonCommercial”)
- The work may be edited and distributed by naming the author, but only under non-commercial conditions.

CC BY-NC-SA (“Attribution-NonCommercial-ShareAlike”)
- As BY-NC, except that the work may only be shared under the same conditions (i.e. a copyleft-like license).

CC BY-NC-ND (“Attribution-NonCommercial-NoDerivatives”)
- The most restrictive license: the distribution is allowed with attribution of the author, but only unchanged and under non-commercial conditions.

### Business Models in Open Source
In retrospect, the triumph of FLOSS acts like a grassroots movement of technophile idealists who, independent of economic constraints and free of monetary dependencies, put their work at the service of the general public. At the same time, companies worth billions have been created in the FLOSS environment; to name just one, the US company Red Hat founded in 1993 with annual sales of over 3 billion USD (2018), which was taken over by the IT giant IBM in 2018.

So let’s take a look at the tension between the free and mostly free-of-charge distribution of high-quality software and the business models for its creators, because one thing should be clear: The countless highly qualified developers of free software must also earn money, and the originally purely non-commercial FLOSS environment must therefore develop sustainable business models in order to preserve its own cosmos.

A common approach, especially for larger projects in the initial phase, is the so-called crowdfunding, i.e. the collection of money donations via a platform like Kickstarter. In return, the donors receive a pre-defined bonus from the developers in the event of success, i.e. if previously defined goals are achieved, be it unlimited access to the product or special features.

Another approach is dual licensing: free software is offered in parallel under a more restrictive or even proprietary license, which in turn guarantees the customer more extensive services (response times in the event of errors, updates, versions for certain platforms, etc.). One example among many is ownCloud, which is being developed under the GPL and offers business customers a “Business Edition” under a proprietary license.

Let us also take ownCloud as an example of another widespread FLOSS business model: professional services. Many companies lack the necessary in-house technical knowledge to set up and operate complex and critical software reliably and, above all, securely. That’s why they buy professional services such as consulting, maintenance or helpdesk directly from the manufacturer. Liability issues also play a role in this decision, as the company transfers the risks of operation to the manufacturer.

If a software manages to become successful and popular in its field, it is peripheral monetization possibilities such as merchandising or certificates that customers acquire and thus point out its special status when using this software. The learning platform Moodle offers the certification of trainers, who document their knowledge to potential clients, for example, and this is just one example among countless others.

Software as a Service (SaaS) is another business model, especially for web-based technologies. Here, a cloud provider runs a software like a Customer Relationship Management (CRM) or a Content Management System (CMS) on their servers and grant their customers access to the installed application. This saves the customer installation and maintenance of the software. In return, the customer pays for the use of the software according to various parameters, for example the number of users. Availability and security play an important role as business-critical factors.

Last but not least, the model of developing customer-specific extensions into free software by order is particularly common in smaller projects. It is then usually up to the customer to decide how to proceed with these extensions, i.e. whether he also releases them or keeps them under lock and key as part of his own business model.

One thing should have become clear: Although free software is usually available free of charge, numerous business models have been created in their environment, which are constantly modified and extended by countless freelancers and companies worldwide in a very creative form, which ultimately also ensures the continued existence of the entire FLOSS movement.

## Lesson 1-4 ICT Skills and Working in Linux

There was a time when working with Linux on the desktop was considered hard since the system lacked many of the more polished desktop applications and configuration tools that other operating systems had. Some of the reasons for that were that Linux was a lot younger than many other operating systems. That being said, it was easier to start by developing more essential command line applications and just leave the more complex graphical tools for later. In the beginning, since Linux was first targeted to more advanced users, that should not have been a problem. But those days are long gone. Today, Linux desktop environments are very mature, leaving nothing to be desired as regards to features and ease of use. Nevertheless, the command line is still considered a powerful tool used each and every day by advanced users. In this lesson we’ll take a look at some of the basic desktop skills you will need in order to choose the best tool for the right job, including getting to the command line.

### Linux User Interfaces
When using a Linux system, you either interact with a command line or with a graphical user interfaces. Both ways grant you access to numerous applications that support performing almost any task with the computer. While objective 1.2 already introduced you to a series of commonly used applications, we will start this lesson with a closer look at desktop environments, ways to access the terminal and tools used for presentations and project management.

### Desktop Environments
Linux has a modular approach where different parts of the system are developed by different projects and developers, each one filling a specific need or objective. Because of that, there are several options of desktop environments to choose from and together with package managers, the default desktop environment is one of the main differences among the many distributions out there. Unlike proprietary operating systems like Windows and macOS, where the users are restricted to the desktop environment that comes with their OS, there is the possibility to install multiple environments and pick the one that adapts the most to you and your needs.

Basically, there are two major desktop environments in the Linux world: Gnome and KDE. They are both very complete, with a large community behind them and aim for the same purpose but with slightly divergent approaches. In a nutshell, Gnome tries to follow the KISS (“keep it simple stupid”) principle, with very streamlined and clean applications. On the other hand, KDE has another perspective with a larger selection of applications and giving the user the opportunity to change every configuration setting in the environment.

While Gnome applications are based on the GTK toolkit (written in the C language), KDE applications make use of the Qt library (written in C++). One of the most practical aspects of writing applications with the same graphical toolkit is that applications will tend to share a similar look and feel, which is responsible for giving the user a sense of unity during their experience. Another important characteristic is that having the same shared graphical library for many frequently used applications may save some memory space at the same time that it will speed up loading time once the library has been loaded for the first time.

### Getting to the Command Line
For us, one of the most important applications is the graphical terminal emulator. Those are called terminal emulators because they really emulate, in a graphical environment, the old style serial terminals (often Teletype machines) that were in fact clients that used to be connected to a remote machine where the computing actually happened. Those machines were really simple computers with no graphics at all that were used on the first versions of Unix.

In Gnome, such an application is called Gnome Terminal, while in KDE it can be found as Konsole. But there are many other choices available, such as Xterm. Those applications are a way for us to have access to a command line environment in order to be able to interact with a shell.

So, you should look at the application menu of your distribution of choice for a terminal application. Besides any difference between them, every application will offer you what is necessary to gain confidence in using the command line.

Another way to get into the terminal is to use the virtual TTY. You can get into them by pressing Ctrl+Alt+F#. Read F# as one of the function keys from 1 to 7, for example. Probably, some of the initial combinations might be running your session manager or your graphical environment. The others will show a prompt asking for your login name like the one bellow:
```
Ubuntu 18.10 arrelia tty3
arrelia login:
```
`arrelia` in this case, is the hostname of the machine and `tty3` is the terminal available after using the key combination above, plus the `F3` key, like in `Ctrl+Alt+F3`.


After providing your login and password, you will finally get into a terminal, but there is no graphical environment in here, so, you won’t be able to use the mouse or run graphical applications without first starting an X, or Wayland, session. But that’s beyond the scope of this lesson.

### Presentations and Projects
The most important tool for presentations on Linux is LibreOffice Impress. It’s part of the open source office suite called LibreOffice. Think about LibreOffice as an open source replacement for the equivalent Microsoft Office. It can even open and save the PPT and PPTX files that are native to Powerpoint. But in spite of that, I really recommend you to use the native ODP Impress format. The ODP is part of the larger Open Document Format, which is a international standard for this kind of file. This is especially important if you want to keep your documents accessible for many years and worry less about compatibility problems. Because they are an open standard, it’s possible for anyone to implement the format without paying any royalties or licenses. This also makes you free to try other presentations software that you may like better and take your files with you, as it’s very likely they will be compatible with those newer softwares.

But if you prefer code over graphical interfaces, there are a few tools for you to choose. Beamer is a LaTeX class that can create slide presentations from LaTeX code. LaTeX itself is a typesetting system largely used for writing scientific documents at the academy, specially for its capacity to handle complex math symbols, which other softwares have difficulty to deal with. If you are at the university and need to deal with equations and other math related problems, Beamer can save you a lot of time.

The other option is Reveal.js, an awesome NPM package (NPM is the default NodeJS package manager) which allows you to create beautiful presentations by using the web. So, if you can write HTML and CSS, Reveal.js will bring most of the JavaScript necessary to create pretty and interactive presentations that will adapt well on any resolution and screen size.

Lastly, if you want a replacement for Microsoft Project, you can try GanttProject or ProjectLibre. Both are very similar to their proprietary counterpart and compatible with Project files.

### Industry Uses of Linux
Linux is heavily used among the software and Internet industries. Sites like W3Techs report that about 68% of the website servers on the Internet are powered by Unix and the biggest portion of those are known to be Linux.

This large adoption is given not only for the free nature of Linux (as both in free beer and in freedom of speech) but also for its stability, flexibility and performance. These characteristics allow vendors to offer their services with a lower cost and a greater scalability. A significant portion of Linux systems nowadays run in the cloud, either on a IaaS (Infrastructure as a service), PaaS (Platform as a Service) or SaaS (Software as a Service) model.

IaaS is a way to share the resources of a large server by offering them access to virtual machines that are, in fact, multiple operating systems running as guests on a host machine over an important piece of software that is called a hypervisor. The hypervisor is responsible for making it possible for these guest OSs to run by segregating and managing the resources available on the host machine to those guests. That’s what we call virtualization. In the IaaS model, you pay only for the fraction of resources your infrastructure uses.

Linux has three well know open source hypervisors: Xen, KVM and VirtualBox. Xen is probably the oldest of them. KVM ran out Xen as the most prominent Linux Hypervisor. It has its development sponsored by RedHat and it is used by them and other players, both in public cloud services and in private cloud setups. VirtualBox belongs to Oracle since its acquisition of Sun Microsystems and is usually used by end users because of its easiness of use and administration.

PaaS and SaaS, on the other hand, build up on the IaaS model, both technically and conceptually. In PaaS instead of a virtual machine, the users have access to a platform where it will be possible to deploy and run their application. The goal here is to ease the burden of dealing with system administration tasks and operating systems updates. Heroku is a common PaaS example where program code can just be run without taking care of the underlying containers and virtual machines.

Lastly, SaaS is the model where you usually pay for a subscription in order to just use a software without worrying about anything else. Dropbox and Salesforce are two good examples of SaaS. Most of these services are accessed through a web browser.

A project like OpenStack is a collection of open source software that can make use of different hypervisors and other tools in order to offer a complete IaaS cloud environment on premise, by leveraging the power of computer cluster on your own datacenter. However, the setup of such infrastructure is not trivial.

### Privacy Issues when using the Internet
The web browser is a fundamental piece of software on any desktop these days, but some people still lack the knowledge to use it securely. While more and more services are accessed through a web browser, almost all actions done through a browser are tracked and analyzed by various parties. Securing access to internet services and preventing tracking is an important aspect of using the internet in a safe manner.

### Cookie Tracking
Let’s assume you have browsed an e-commerce website, selected a product you wanted and placed that in the shopping cart. But at the last second, you have decided to give it a second thought and think a little longer if you really needed that. After a while, you start seeing ads of that same product following you around the web. When clicking on the ads, you are immediately sent to the product page of that store again. It’s not uncommon that the products you placed in the shopping cart are still there, just waiting for you to decide to check them out. Have you ever wondered how they do that? How they show you the right ad at another web page? The answer for these questions is called cookie tracking.

Cookies are small files a website can save on your computer in order to store and retrieve some kind of information that can be useful for your navigation. They have been in use for many years and are one of the oldest ways to store data on the client side. One good example of their use are unique shopping card IDs. That way, if you ever come back to the same website in a few days, the store can remember you the products you’ve placed in your cart during your last visit and save you the time to find them again.

That’s usually okay, since the website is offering you a useful feature and not sharing any data with third parties. But what about the ads that are shown to you while you surf on other web pages? That’s where the ad networks come in. Ad networks are companies that offer ads for e-commerce sites like the one in our example on one side, and monetization for websites, on the other side. Content creators like bloggers, for example, can make some space available for those ad networks on their blog, in exchange for a commission related to the sales generated by that ad.

But how do they know what product to show you? They usually do that by saving also a cookie from the ad network at the moment you visited or searched for a certain product on the e-commerce website. By doing that, the network is able to retrieve the information on that cookie wherever the network has ads, making the correlation with the products you were interested. This is usually one of the most common ways to track someone over the Internet. The example we gave above makes use of an e-commerce to make things more tangible, but social media platforms do the same with their “Like” or “Share” buttons and their social login.

One way you can get rid of that is by not allowing third party websites to store cookies on your browser. This way, only the website you visit can store their cookies. But be aware that some “legitimate” features may not work well if you do that, because many sites today rely on third party services to work. So, you can look for a cookie manager at your browser’s add-on repository in order to have a fine-grained control of which cookies are being stored on your machine.

### Do Not Track (DNT)
Another common misconception is related to a certain browser configuration better known as DNT. That’s an acronym for “Do Not Track” and it can be turned on basically on any current browser. Similarly to the private mode, it’s not hard to find people that believe they will not be tracked if they have this configuration on. Unfortunately, that’s not always true. Currently, DNT is just a way for you to tell the websites you visit that you do not want them to track you. But, in fact, they are the ones who will decide if they will respect your choice or not. In other words, DNT is a way to opt-out from website tracking, but there is no guarantee on that choice.

Technically, this is done by simply sending an extra flag on the header of the HTTP request protocol (DNT: 1) upon requesting data from a web server. If you want to know more about this topic, the website https://allaboutdnt.com is good starting point.

### “Private” Windows
You might have noticed the quotes in the heading above. This is because those windows are not as private as most people think. The names may vary but they can be called “private mode”, “incognito” or “anonymous” tab, depending on which browser you are using.

In Firefox, you can easily use it by pressing Ctrl+Shift+P keys. In Chrome, just press Ctrl+Shift+N. What it actually does is open a brand new session, which usually doesn’t share any configuration or data from your standard profile. When you close the private window, the browser will automatically delete all the data generated by that session, leaving no trace on the computer used. This means that no personal data, like history, passwords or cookies are stored on that computer.

Thus, many people misunderstand this concept by believing that they can browse anonymous on the Internet, which is not completely true. One thing that the privacy or incognito mode does is avoid what we call cookie tracking. When you visit a website, it can store a small file on your computer which may contain an ID that can be used to track you. Unless you configure your browser to not accept third-party cookies, ad networks or other companies can store and retrieve that ID and actually track your browsing across websites. But since the cookies stored on a private mode session are deleted right after you close that session, that information is forever lost.

Besides that, websites and other peers on the Internet can still use plenty other techniques in order to track you. So, private mode brings you some level of anonymity but it’s completely private only on the computer you are using. If you are accessing your email account or banking website from a public computer, like in an airport or a hotel, you should definitely access those using your browser’s private mode. In other situations, there can be benefits but you should know exactly what risks you are avoiding and which ones have no effect. Whenever you use a public accessible computer, be aware that other security threats such as malware or key loggers might exist. Be careful whenever you enter personal information, including usernames and passwords, on such computers or when you download or copy confidential data.

### Choosing the Right Password
One of the most difficult situations any user faces is choosing a secure password for the services they make use of. You have certainly heard before that you should not use common combinations like qwerty, 123456 or 654321, nor easily guessable numbers like your (or a relative’s) birthday or zip code. The reason for that is because those are all very obvious combinations and the first attempts an invader will try in order to gain access to your account.

There are known techniques for creating a safe password. One of the most famous is making up a sentence which reminds you of that service and picking the first letters of each word. Let’s assume I want to create a good password for Facebook, for example. In this case, I could come up with a sentence like “I would be happy if I had a 1000 friends like Mike”. Pick the first letter of each word and the final password would be IwbhiIha1000flM. This would result in a 15 characters password which is long enough to be hard to guess and easy to remember at the same time (as long as I can remember the sentence and the “algorithm” for retrieving the password).

Sentences are usually easier to remember than the passwords but even this method has its limitations. We have to create passwords for so many services nowadays and as we use them with different frequencies, it will eventually be very difficult to remember all the sentences at the time we need them. So what can we do? You may answer that the wisest thing to do in this case is creating a couple good passwords and reuse them on similar services, right?

Unfortunately, that’s also not a good idea. You probably also heard you should not reuse the same password among different services. The problem of doing such a thing is that a specific service may leak your password (yes, it happens all the time) and any person who have access to it will try to use the same email and password combination on other popular services on the Internet in hope you have done exactly that: recycled passwords. And guess what? In case they are right you will end up having a problem not only on just one service but on several of them. And believe me, we tend to think it’s not going to happen to us until it’s too late.

So, what can we do in order to protect ourselves? One of the most secure approaches available today is using what is called a password manager. Password managers are a piece of software that will essentially store all your passwords and usernames in an encrypted format which can be decrypted by a master password. This way you only need to remember one good password since the manager will keep all the others safe for you.

KeePass is one of the most famous and feature rich open source password managers available. It will store your passwords in an encrypted file within your file system. The fact it’s open source is an important issue for this kind of software since it guarantees they will not make any use of your data because any developer can audit the code and know exactly how it works. This brings a level of transparency that’s impossible to reach with proprietary code. KeePass has ports for most operating systems, including Windows, Linux and macOS; as well as mobile ones like iOS and Android. It also includes a plugin system that is able to extend it’s functionality far beyond the defaults.

Bitwarden is another open source solution that has a similar approach but instead of storing your data in a file, it will make use of a cloud server. This way, it’s easier to keep all your devices synchronized and your passwords easily accessible through the web. Bitwarden is one of the few projects that will make not only the clients, but also the cloud server available as an open source software. This means you can host your own version of Bitwarden and make it available to anyone, like your family or your company employees. This will give you flexibility but also total control over how their passwords are stored and used.

One of the most important things to keep in mind when using a password manager is creating a random password for each different service since you will not need to remind them anyway. It would be worthless if you use a password manager to store recycled or easily guessable passwords. Thus, most of them will offer you a random password generator you can use to create those for you.

### Encryption
Whenever data is transferred or stored, precautions need to be taken to ensure that third parties may not access the data. Data transferred over the internet passes by a series of routers and networks where third parties might be able to access the network traffic. Likewise, data stored on physical media might be read by anyone who comes into possession of that media. To avoid this kind of access, confidential information should be encrypted before it leaves a computing device.

### TLS
Transport Layer Security (TLS) is a protocol to offer security over network connections by making use of cryptography. TLS is the successor of the Secure Sockets Layer (SSL) which has been deprecated because of serious flaws. TLS has also evolved a couple of times in order to adapt itself and become more secure, thus it’s current version is 1.3. It can provide both privacy, and authenticity by making use of what is called symmetric and public-key cryptography. By saying that, we mean that once in use, you can be sure that nobody will be able to eavesdrop or alter your communication with that server during that session.

The most important lesson here is recognizing that a website is trustworthy. You should look for the “lock” symbol on the browser’s address bar. If you desire, you can click on it to inspect the certificate that plays an important role in the HTTPS protocol.

TLS is what is used on the HTTPS protocol (HTTP over TLS) in order to make it possible to send sensitive data (like your credit card number) through the web. Explaining how TLS works goes way beyond the purpose of this article, but you can find more information on the Wikipedia and at the Mozilla wiki.

### File and E-mail Encryption With GnuPG
There are plenty of tools for securing emails but one of the most important of them is certainly GnuPG. GnuPG stands for GNU Privacy Guard and it is an open source implementation of OpenPGP which is an international standard codified within RFC 4880.

GnuPG can be used to sign, encrypt, and decrypt texts, e-mails, files, directories, and even whole disk partitions. It works with public-key cryptography and is widely available. In a nutshell GnuPG creates a pair of files which contain your public and private keys. As the name implies, the public key can be available to anyone and the private key needs to be kept in secret. People will use your public key to encrypt data which only your private key will be able to decrypt.

You can also use your private key to sign any file or e-mail which can be validated against the corresponding public key. This digital signage works analogous to the real world signature. As long as you are the only one who posses your private key, the receiver can be sure that it was you who have authored it. By making use of the cryptographic hash functionality GnuPG will also guarantee no changes have been made after the signature because any changes to the content would invalidate the signature.

GnuPG is a very powerful tool and, in a certain extent, also a complex one. You can find more information on its website and on Archlinux wiki (Archlinux wiki is a very good source of information, even though you don’t use Archlinux).

### Disk Encryption
A good way to secure your data is to encrypt your whole disk or partition. There are many open source softwares you can use to achieve such a purpose. How they work and what level of encryption they offer also varies significantly. There are basic two methods available: stacked and block device encryption.

Stacked filesystem solutions are implemented on top of existing filesystem. When using this method, the files and directories are encrypted before being stored on the filesystem and decrypted after reading them. This means the files are stored on the host filesystem in an encrypted form (meaning that their contents, and usually also their file/folder names, are replaced by random-looking data), but other than that, they still exist in that filesystem as they would without encryption, as normal files, symlinks, hardlinks, etc.

On the other hand, block device encryption happens below the filesystem layer, making sure everything that is written to a block device is encrypted. If you look to the block while it’s offline, it will look like a large section of random data and you won’t even be able to tell what type of filesystem is there without decrypting it first. This means you can’t tell what is a file or directory; how big they are and what kind of data it is, because metadata, directory structure and permissions are also encrypted.

Both methods have their own pros and cons. Among all the options available, you should take a look at dm-crypt, which is the de-facto standard for block encryption for Linux systems, since it’s native in the kernel. It can be used with LUKS (Linux Unified Key Setup) extension, which is a specification that implements a platform-independent standard for use with various tools.

If you want to try a stackable method, you should take a look at EncFS, which is probably the easiest way to secure data on Linux because it does not require root privileges to implement and it can work on an existing filesystem without modifications.

Finally, if you need to access data on various platforms, check out Veracrypt. It is the successor of a Truecrypt and allows the creation of encrypted media and files, which can be used on Linux as well as on macOS and Windows.




___

This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/010-160/)