# LPIC-1.102: Linux Installation and Package Management

## Lesson 102-5 Use RPM and YUM package management

A long time ago, when Linux was still in its infancy, the most common way to distribute software was a compressed file (usually as a `.tar.gz` archive) with source code, which you would unpack and compile yourself.

However, as the amount and complexity of software grew, the need for a way to distribute pre-compiled software became clear. After all, not everyone had the resources, both in time and computing power, to compile large projects like the Linux kernel or an X Server.

Soon, efforts to standardize a way to distribute these software “packages” grew, and the first package managers were born. These tools made it much easier to install, configure or remove software from a system.

One of those was the RPM Package Manager and its corresponding tool (`rpm`), developed by Red Hat. Today, they are widely used not only on Red Hat Enterprise Linux (RHEL) itself, but also on its descendants, like Fedora, CentOS and Oracle Linux, other distributions like openSUSE and even other operating systems, like IBM’S AIX.

Other package management tools popular on Red Hat compatible distros are `yum` (YellowDog Updater Modified), `dnf` (Dandified YUM) and `zypper`, which can streamline many of the aspects of the installation, maintenance and removal of packages, making package management much easier.

In this lesson, we will learn how to use `rpm`, `yum`, `dnf` and `zypper` to obtain, install, manage and remove software on a Linux system.

```
Note
Despite using the same package format, there are internal differences between distributions so a package made specifically for openSUSE might not work on a RHEL system, and vice-versa. When searching for packages, always check for compatibility and try to find one tailored for your specific distribution, if possible.
```

### The RPM Package Manager (rpm)
The RPM Package Manager (`rpm`) is the essential tool for managing software packages on Red Hat-based (or derived) systems.

### Installing, Upgrading and Removing Packages
The most basic operation is to install a package, which can be done with:

```
# rpm -i PACKAGENAME
```

Where `PACKAGENAME` is the name of the `.rpm` package you wish to install.

If there is a previous version of a package on the system, you can upgrade to a newer version using the `-U` parameter:

```
# rpm -U PACKAGENAME
```

If there is no previous version of `PACKAGENAME` installed, then a fresh copy will be installed. To avoid this and only upgrade an installed package, use the `-F` option.

In both operations you can add the `-v` parameter to get a verbose output (more information is shown during the installation) and `-h` to get hash signs (`#`) printed as a visual aid to track installation progress. Multiple parameters can be combined into one, so `rpm -i -v -h` is the same as `rpm -ivh`.

To remove an installed package, pass the `-e` parameter (as in “erase”) to `rpm`, followed by the name of the package you wish to remove:

```
# rpm -e wget
```

If an installed package depends on the package being removed, you will get an error message:

```
# rpm -e unzip
error: Failed dependencies:
	/usr/bin/unzip is needed by (installed) file-roller-3.28.1-2.el7.x86_64
```

To complete the operation, first you will need to remove the packages that depend on the one you wish to remove (in the example above, `file-roller`). You can pass multiple package names to `rpm -e` to remove multiple packages at once.

### Dealing with Dependencies
More often than not, a package may depend on others to work as intended. For example, an image editor may need libraries to open JPG files, or a utility may need a widget toolkit like Qt or GTK for its user interface.

`rpm` will check if those dependencies are installed on your system, and will fail to install the package if they are not. In this case, `rpm` will list what is missing. However it cannot solve dependencies by itself.

In the example below, the user tried to install a package for the GIMP image editor, but some dependencies were missing:

```
# rpm -i gimp-2.8.22-1.el7.x86_64.rpm
error: Failed dependencies:
	babl(x86-64) >= 0.1.10 is needed by gimp-2:2.8.22-1.el7.x86_64
	gegl(x86-64) >= 0.2.0 is needed by gimp-2:2.8.22-1.el7.x86_64
	gimp-libs(x86-64) = 2:2.8.22-1.el7 is needed by gimp-2:2.8.22-1.el7.x86_64
	libbabl-0.1.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgegl-0.2.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimp-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpbase-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpcolor-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpconfig-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpmath-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpmodule-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpthumb-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpui-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libgimpwidgets-2.0.so.0()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libmng.so.1()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libwmf-0.2.so.7()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
	libwmflite-0.2.so.7()(64bit) is needed by gimp-2:2.8.22-1.el7.x86_64
```

It is up to the user to find the `.rpm` packages with the corresponding dependencies and install them. Package managers such as `yum`, `zypper` and `dnf` have tools that can tell which package provides a specific file. Those will be discussed later in this lesson.

### Listing Installed Packages
To get a list of all installed packages on your system, use the `rpm -qa` (think of “query all”).

```
# rpm -qa
selinux-policy-3.13.1-229.el7.noarch
pciutils-libs-3.5.1-3.el7.x86_64
redhat-menus-12.0.2-8.el7.noarch
grubby-8.28-25.el7.x86_64
hunspell-en-0.20121024-6.el7.noarch
dejavu-fonts-common-2.33-6.el7.noarch
xorg-x11-drv-dummy-0.3.7-1.el7.1.x86_64
libevdev-1.5.6-1.el7.x86_64
[...]
```

### Getting Package Information
To get information about an installed package, such as its version number, architecture, install date, packager, summary, etc., use `rpm` with the `-qi` (think of “query info”) parameters, followed by the package name. For example:

```
# rpm -qi unzip
Name        : unzip
Version     : 6.0
Release     : 19.el7
Architecture: x86_64
Install Date: Sun 25 Aug 2019 05:14:39 PM EDT
Group       : Applications/Archiving
Size        : 373986
License     : BSD
Signature   : RSA/SHA256, Wed 25 Apr 2018 07:50:02 AM EDT, Key ID 24c6a8a7f4a80eb5
Source RPM  : unzip-6.0-19.el7.src.rpm
Build Date  : Wed 11 Apr 2018 01:24:53 AM EDT
Build Host  : x86-01.bsys.centos.org
Relocations : (not relocatable)
Packager    : CentOS BuildSystem <http://bugs.centos.org>
Vendor      : CentOS
URL         : http://www.info-zip.org/UnZip.html
Summary     : A utility for unpacking zip files
Description :
The unzip utility is used to list, test, or extract files from a zip
archive. Zip archives are commonly found on MS-DOS systems. The zip
utility, included in the zip package, creates zip archives. Zip and
unzip are both compatible with archives created by PKWARE(R)'s PKZIP
for MS-DOS, but the programs' options and default behaviors do differ
in some respects.

Install the unzip package if you need to list, test or extract files from
a zip archive.
```

To get a list of what files are inside an installed package use the `-ql` parameters (think of “query list”) followed by the package name:

```
# rpm -ql unzip
/usr/bin/funzip
/usr/bin/unzip
/usr/bin/unzipsfx
/usr/bin/zipgrep
/usr/bin/zipinfo
/usr/share/doc/unzip-6.0
/usr/share/doc/unzip-6.0/BUGS
/usr/share/doc/unzip-6.0/LICENSE
/usr/share/doc/unzip-6.0/README
/usr/share/man/man1/funzip.1.gz
/usr/share/man/man1/unzip.1.gz
/usr/share/man/man1/unzipsfx.1.gz
/usr/share/man/man1/zipgrep.1.gz
/usr/share/man/man1/zipinfo.1.gz
```

If you wish to get information or a file listing from a package that has not been installed yet, just add the `-p` parameter to the commands above, followed by the name of the RPM file (`FILENAME`). So `rpm -qi PACKAGENAME` becomes `rpm -qip FILENAME`, and `rpm -ql PACKAGENAME` becomes `rpm -qlp FILENAME`, as shown below.

```
# rpm -qip atom.x86_64.rpm
Name        : atom
Version     : 1.40.0
Release     : 0.1
Architecture: x86_64
Install Date: (not installed)
Group       : Unspecified
Size        : 570783704
License     : MIT
Signature   : (none)
Source RPM  : atom-1.40.0-0.1.src.rpm
Build Date  : sex 09 ago 2019 12:36:31 -03
Build Host  : b01bbeaf3a88
Relocations : /usr
URL         : https://atom.io/
Summary     : A hackable text editor for the 21st Century.
Description :
A hackable text editor for the 21st Century.
```

```
# rpm -qlp atom.x86_64.rpm
/usr/bin/apm
/usr/bin/atom
/usr/share/applications/atom.desktop
/usr/share/atom
/usr/share/atom/LICENSE
/usr/share/atom/LICENSES.chromium.html
/usr/share/atom/atom
/usr/share/atom/atom.png
/usr/share/atom/blink_image_resources_200_percent.pak
/usr/share/atom/content_resources_200_percent.pak
/usr/share/atom/content_shell.pak

(listing goes on)
```

### Finding Out Which Package Owns a Specific File
To find out which installed package owns a file, use the `-qf` (think “query file”) followed by the full path to the file:

```
# rpm -qf /usr/bin/unzip
unzip-6.0-19.el7.x86_64
```

In the example above, the file `/usr/bin/unzip` belongs to the `unzip-6.0-19.el7.x86_64` package.

### YellowDog Updater Modified (YUM)
`yum` was originally developed as the Yellow Dog Updater (YUP), a tool for package management on the Yellow Dog Linux distribution. Over time, it evolved to manage packages on other RPM based systems, such as Fedora, CentOS, Red Hat Enterprise Linux and Oracle Linux.

Functionally, it is similar to the `apt` utility on Debian-based systems, being able to search for, install, update and remove packages and automatically handle dependencies. `yum` can be used to install a single package, or to upgrade a whole system at once.

### Searching for Packages
In order to install a package, you need to know its name. For this you can perform a search with `yum search PATTERN`, where `PATTERN` is the name of the package you are searching for. The result is a list of packages whose name or summary contain the search pattern specified. For example, if you need a utility to handle 7Zip compressed files (with the `.7z` extension) you can use:

```
# yum search 7zip
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
=========================== N/S matchyutr54ed: 7zip ============================
p7zip-plugins.x86_64 : Additional plugins for p7zip
p7zip.x86_64 : Very high compression ratio file archiver
p7zip-doc.noarch : Manual documentation and contrib directory
p7zip-gui.x86_64 : 7zG - 7-Zip GUI version

  Name and summary matches only, use "search all" for everything.
```

### Installing, Upgrading and Removing Packages
To install a package using `yum`, use the command `yum install PACKAGENAME`, where `PACKAGENAME` is the name of the package. `yum` will fetch the package and corresponding dependencies from an online repository, and install everything in your system.

```
# yum install p7zip
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
Resolving Dependencies
--> Running transaction check
---> Package p7zip.x86_64 0:16.02-10.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================
 Package        Arch            Version               Repository     Size
==========================================================================
Installing:
 p7zip          x86_64          16.02-10.el7          epel          604 k

Transaction Summary
==========================================================================
Install  1 Package

Total download size: 604 k
Installed size: 1.7 M
Is this ok [y/d/N]:
```

To upgrade an installed package, use `yum update PACKAGENAME`, where `PACKAGENAME` is the name of the package you want to upgrade. For example:

```
# yum update wget
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
Resolving Dependencies
--> Running transaction check
---> Package wget.x86_64 0:1.14-18.el7 will be updated
---> Package wget.x86_64 0:1.14-18.el7_6.1 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================
 Package     Arch          Version                   Repository      Size
==========================================================================
Updating:
 wget        x86_64        1.14-18.el7_6.1           updates        547 k

Transaction Summary
==========================================================================
Upgrade  1 Package

Total download size: 547 k
Is this ok [y/d/N]:
```
If you omit the name of a package, you can update every package on the system for which an update is available.

To check if an update is available for a specific package, use `yum check-update PACKAGENAME`. As before, if you omit the package name, `yum` will check for updates for every installed package on the system.

To remove an installed package, use `yum remove PACKAGENAME`, where `PACKAGENAME` is the name of the package you wish to remove.

### Finding Which Package Provides a Specific File
In a previous example we showed an attempt to install the `gimp` image editor, which failed because of unmet dependencies. However, `rpm` shows which files are missing, but does not list the name of the packages that provide them.

For example, one of the dependencies missing was `libgimpui-2.0.so.0`. To see what package provides it, you can use `yum whatprovides`, followed by the name of the file you are searching for:

```
# yum whatprovides libgimpui-2.0.so.0
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
2:gimp-libs-2.8.22-1.el7.i686 : GIMP libraries
Repo        : base
Matched from:
Provides    : libgimpui-2.0.so.0
```

The answer is `gimp-libs-2.8.22-1.el7.i686`. You can then install the package with the command `yum install gimp-libs`.

This also works for files already in your system. For example, if you wish to know where the file `/etc/hosts` came from, you can use:

```
# yum whatprovides /etc/hosts
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
setup-2.8.71-10.el7.noarch : A set of system configuration and setup files
Repo        : base
Matched from:
Filename    : /etc/hosts
```

The answer is `setup-2.8.71-10.el7.noarch`.

### Getting Information About a Package
To get information about a package, such as its version, architecture, description, size and more, use `yum info PACKAGENAME` where `PACKAGENAME` is the name of the package you want information for:

```
# yum info firefox
Last metadata expiration check: 0:24:16 ago on Sat 21 Sep 2019 02:39:43 PM -03.
Installed Packages
Name         : firefox
Version      : 69.0.1
Release      : 3.fc30
Architecture : x86_64
Size         : 268 M
Source       : firefox-69.0.1-3.fc30.src.rpm
Repository   : @System
From repo    : updates
Summary      : Mozilla Firefox Web browser
URL          : https://www.mozilla.org/firefox/
License      : MPLv1.1 or GPLv2+ or LGPLv2+
Description  : Mozilla Firefox is an open-source web browser, designed
             : for standards compliance, performance and portability.
```

### Managing Software Repositories
For `yum` the “repos” are listed in the directory `/etc/yum.repos.d/`. Each repository is represented by a `.repo` file, like `CentOS-Base.repo`.

Additional, extra repositories can be added by the user by adding a `.repo` file in the directory mentioned above, or at the end of `/etc/yum.conf`. However, the recommended way to add or manage repositories is with the `yum-config-manager tool`.

To add a repository, use the `--add-repo` parameter, followed by the URL to a `.repo` file.

```
# yum-config-manager --add-repo https://rpms.remirepo.net/enterprise/remi.repo
Loaded plugins: fastestmirror, langpacks
adding repo from: https://rpms.remirepo.net/enterprise/remi.repo
grabbing file https://rpms.remirepo.net/enterprise/remi.repo to /etc/yum.repos.d/remi.repo
repo saved to /etc/yum.repos.d/remi.repo
```

To get a list of all available repositories use `yum repolist all`. You will get an output similar to this:

```
# yum repolist all
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.ufscar.br
 * epel: mirror.globo.com
 * extras: mirror.ufscar.br
 * updates: mirror.ufscar.br
repo id                       repo name                    status
updates/7/x86_64              CentOS-7 - Updates           enabled:  2,500
updates-source/7              CentOS-7 - Updates Sources   disabled
```

`disabled` repositories will be ignored when installing or upgrading software. To enable or disable a repository, use the `yum-config-manager` utility, followed by the repository id.

In the output above, the repository id is shown on the first column (`repo id`) of each line. Use only the part before the first `/` , so the id for the `CentOS-7 - Updates` repo is `updates`, and not `updates/7/x86_64`.

```
# yum-config-manager --disable updates
```

The command above will disable the `updates` repo. To re-enable it use:

```
# yum-config-manager --enable updates
```

```
Note
Yum stores downloaded packages and associated metadata in a cache directory (usually /var/cache/yum). As the system gets upgraded and new packages are installed, this cache can get quite large. To clean the cache and reclaim disk space you can use the yum clean command, followed by what to clean. The most useful parameters are packages (yum clean packages) to delete downloaded packages and metadata (yum clean metadata) to delete associated metadata. See the manual page for yum (type man yum) for more information.
```

### DNF
`dnf` is the package management tool used on Fedora, and is a fork of `yum`. As such, many of the commands and parameters are similar. This section will give you just a quick overview of `dnf`.

Searching for packages
- `dnf search PATTERN`, where `PATTERN` is what you are searching for. For example, `dnf search unzip` will show all packages that contain the word `unzip` in the name or description.

Getting information about a package
- `dnf info PACKAGENAME`

Installing packages
- `dnf install PACKAGENAME`, where `PACKAGENAME` is the name of the package you wish to install. You can find the name by performing a search.

Removing packages
- `dnf remove PACKAGENAME`

Upgrading packages
- `dnf upgrade PACKAGENAME` to update only one package. Omit the package name to upgrade all the packages in the system.

Finding out which package provides a specific file
- `dnf provides FILENAME`

Getting a list of all the packages installed in the system
- `dnf list --installed`

Listing the contents of a package
- `dnf repoquery -l PACKAGENAME`

```
Note
dnf has a built-in help system, which shows more information (such as extra parameters) for each command. To use it, type dnf help followed by the command, like dnf help install.
```

### Managing Software Repositories
Just as with `yum` and `zypper`, `dnf` works with software repositories (repos). Each distribution has a list of default repositories, and administrators can add or remove repos as needed.

To get a list of all available repositories, use `dnf repolist`. To list only enabled repositories, add the `--enabled` option, and to list only disabled repositories, add the `--disabled` option.

```
# dnf repolist
Last metadata expiration check: 0:20:09 ago on Sat 21 Sep 2019 02:39:43 PM -03.
repo id                    repo name                                      status
*fedora                    Fedora 30 - x86_64                             56,582
*fedora-modular            Fedora Modular 30 - x86_64                        135
*updates                   Fedora 30 - x86_64 - Updates                   12,774
*updates-modular           Fedora Modular 30 - x86_64 - Updates              145
```

To add a repository, use `dnf config-manager --add_repo URL`, where `URL` is the full URL to the repository. To enable a repository, use `dnf config-manager --set-enabled REPO_ID`.

Likewise, to disable a repository use `dnf config-manager --set-disabled REPO_ID`. In both cases `REPO_ID` is the unique ID for the repository, which you can get using `dnf repolist`. Added repositories are enabled by default.

Repositories are stored in `.repo` files in the directory `/etc/yum.repos.d/`, with exactly the same syntax used for `yum`.

### Zypper
`zypper` is the package management tool used on SUSE Linux and OpenSUSE. Feature-wise it is similar to `apt` and `yum`, being able to install, update and remove packages from a system, with automated dependency resolution.

### Updating the Package Index
Like other package management tools, `zypper` works with repositories containing packages and metadata. This metadata needs to be refreshed from time to time, so that the utility will know about the latest packages available. To do a refresh, simply type:

```
# zypper refresh
Repository 'Non-OSS Repository' is up to date.
Repository 'Main Repository' is up to date.
Repository 'Main Update Repository' is up to date.
Repository 'Update Repository (Non-Oss)' is up to date.
All repositories have been refreshed.
```

`zypper` has an auto-refresh feature that can be enabled on a per-repository basis, meaning that some repos may be refreshed automatically before a query or package installation, and others may need to be refreshed manually. You will learn how to control this feature shortly.

### Searching for Packages
To search for a package, use the `search` (or `se`) operator, followed by the package name:

```
# zypper se gnumeric
Loading repository data...
Reading installed packages...

S | Name           | Summary                           | Type
--+----------------+-----------------------------------+--------
  | gnumeric       | Spreadsheet Application           | package
  | gnumeric-devel | Spreadsheet Application           | package
  | gnumeric-doc   | Documentation files for Gnumeric  | package
  | gnumeric-lang  | Translations for package gnumeric | package
```

The search operator can also be used to obtain a list of all the installed packages in the system. To do so, use the `-i` parameter without a package name, as in `zypper se -i`.

To see if a specific package is installed, add the package name to the command above. For example, the following command will search among the installed packages for any containing “firefox” in the name:

```
# zypper se -i firefox
Loading repository data...
Reading installed packages...

S | Name                               | Summary                 | Type
--+------------------------------------+-------------------------+--------
i | MozillaFirefox                     | Mozilla Firefox Web B-> | package
i | MozillaFirefox-branding-openSUSE   | openSUSE branding of -> | package
i | MozillaFirefox-translations-common | Common translations f-> | package
```

To search only among non-installed packages, add the `-u` parameter to the `se` operator.

### Installing, Upgrading and Removing Packages
To install a software package, use the `install` (or `in`) operator, followed by the package name. Like so:

```
# zypper in unrar
zypper in unrar
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following NEW package is going to be installed:
  unrar

1 new package to install.
Overall download size: 141.2 KiB. Already cached: 0 B. After the operation, additional 301.6 KiB will be used.
Continue? [y/n/v/...? shows all options] (y): y
Retrieving package unrar-5.7.5-lp151.1.1.x86_64
                                     (1/1), 141.2 KiB (301.6 KiB unpacked)
Retrieving: unrar-5.7.5-lp151.1.1.x86_64.rpm .......................[done]
Checking for file conflicts: .......................................[done]
(1/1) Installing: unrar-5.7.5-lp151.1.1.x86_64 .....................[done]
```

`zypper` can also be used to install an RPM package on disk, while trying to satisfy its dependencies using packages from the repositories. To do so, just provide the full path to the package instead of a package name, like `zypper in /home/john/newpackage.rpm`.

To update packages installed on the system, use `zypper update`. As in the installation process, this will show a list of packages to be installed/upgraded before asking if you want to proceed.

If you wish to only list the available updates, without installing anything, you can use `zypper list-updates`.

To remove a package, use the `remove` (or `rm`) operator, followed by the package name:

```
# zypper rm unrar
Loading repository data...
Reading installed packages...
Resolving package dependencies...

The following package is going to be REMOVED:
  unrar

1 package to remove.
After the operation, 301.6 KiB will be freed.
Continue? [y/n/v/...? shows all options] (y): y
(1/1) Removing unrar-5.7.5-lp151.1.1.x86_64 ........................[done]
Keep in mind that removing a package also removes any other packages that depend on it. For example:

# zypper rm libgimp-2_0-0
Loading repository data...
Warning: No repositories defined. Operating only with the installed resolvables. Nothing can be installed.
Reading installed packages...
Resolving package dependencies...

The following 6 packages are going to be REMOVED:
  gimp gimp-help gimp-lang gimp-plugins-python libgimp-2_0-0
  libgimpui-2_0-0

6 packages to remove.
After the operation, 98.0 MiB will be freed.
Continue? [y/n/v/...? shows all options] (y):
```

### Finding Which Packages Contain a Specific File
To see which packages contain a specific file, use the search operator followed by the `--provides` parameter and the name of the file (or full path to it). For example, if you want to know which packages contain the file `libgimpmodule-2.0.so.0` in `/usr/lib64/` you would use:

```
# zypper se --provides /usr/lib64/libgimpmodule-2.0.so.0
Loading repository data...
Reading installed packages...

S | Name          | Summary                                      | Type
--+---------------+----------------------------------------------+--------
i | libgimp-2_0-0 | The GNU Image Manipulation Program - Libra-> | package
```

### Getting Package Information
To see the metadata associated with a package, use the `info` operator followed by the package name. This will provide you with the origin repository, package name, version, architecture, vendor, installed size, if it is installed or not, the status (if it is up-to-date), the source package and a description.

```
# zypper info gimp
Loading repository data...
Reading installed packages...

Information for package gimp:
 -----------------------------
Repository     : Main Repository
Name           : gimp
Version        : 2.8.22-lp151.4.6
Arch           : x86_64
Vendor         : openSUSE
Installed Size : 29.1 MiB
Installed      : Yes (automatically)
Status         : up-to-date
Source package : gimp-2.8.22-lp151.4.6.src
Summary        : The GNU Image Manipulation Program
Description    :
    The GIMP is an image composition and editing program, which can be
    used for creating logos and other graphics for Web pages. The GIMP
    offers many tools and filters, and provides a large image
    manipulation toolbox, including channel operations and layers,
    effects, subpixel imaging and antialiasing, and conversions, together
    with multilevel undo. The GIMP offers a scripting facility, but many
    of the included scripts rely on fonts that we cannot distribute.
```

### Managing Software Repositories
`zypper` can also be used to manage software repositories. To see a list of all the repositories currently registered in your system, use `zypper repos`:

```
# zypper repos
Repository priorities are without effect. All enabled repositories share the same priority.

#  | Alias                     | Name                               | Enabled | GPG Check | Refresh
---+---------------------------+------------------------------------+---------+-----------+--------
 1 | openSUSE-Leap-15.1-1      | openSUSE-Leap-15.1-1               | No      | ----      | ----
 2 | repo-debug                | Debug Repository                   | No      | ----      | ----
 3 | repo-debug-non-oss        | Debug Repository (Non-OSS)         | No      | ----      | ----
 4 | repo-debug-update         | Update Repository (Debug)          | No      | ----      | ----
 5 | repo-debug-update-non-oss | Update Repository (Debug, Non-OSS) | No      | ----      | ----
 6 | repo-non-oss              | Non-OSS Repository                 | Yes     | (r ) Yes  | Yes
 7 | repo-oss                  | Main Repository                    | Yes     | (r ) Yes  | Yes
 8 | repo-source               | Source Repository                  | No      | ----      | ----
 9 | repo-source-non-oss       | Source Repository (Non-OSS)        | No      | ----      | ----
10 | repo-update               | Main Update Repository             | Yes     | (r ) Yes  | Yes
11 | repo-update-non-oss       | Update Repository (Non-Oss)        | Yes     | (r ) Yes  | Yes
```

See in the `Enabled` column that some repositories are enabled, while others are not. You can change this with the `modifyrepo` operator, followed by the `-e` (enable) or `-d` (disable) parameter and the repository alias (the second column in the output above).

```
# zypper modifyrepo -d repo-non-oss
Repository 'repo-non-oss' has been successfully disabled.

# zypper modifyrepo -e repo-non-oss
Repository 'repo-non-oss' has been successfully enabled.
```

Previously we mentioned that `zypper` has an auto refresh capability that can be enabled on a per-repository basis. When enabled, this flag will make `zypper` run a refresh operation (the same as running `zypper refresh`) before working with the specified repo. This can be controlled with the `-f` and `-F` parameters of the `modifyrepo` operator:

```
# zypper modifyrepo -F repo-non-oss
Autorefresh has been disabled for repository 'repo-non-oss'.

# zypper modifyrepo -f repo-non-oss
Autorefresh has been enabled for repository 'repo-non-oss'.
```

#### Adding and Removing Repositories
To add a new software repository for `zypper`, use the `addrepo` operator followed by the repository URL and repository name, like below:

```
# zypper addrepo http://packman.inode.at/suse/openSUSE_Leap_15.1/ packman
Adding repository 'packman' ........................................[done]
Repository 'packman' successfully added

URI         : http://packman.inode.at/suse/openSUSE_Leap_15.1/
Enabled     : Yes
GPG Check   : Yes
Autorefresh : No
Priority    : 99 (default priority)

Repository priorities are without effect. All enabled repositories share the same priority.
```

While adding a repository, you can enable auto-updates with the `-f` parameter. Added repositories are enabled by default, but you can add and disable a repository at the same time by using the `-d` parameter.

To remove a repository, use the `removerepo` operator, followed by the repository name (Alias). To remove the repository added in the example above, the command would be:

```
# zypper removerepo packman
Removing repository 'packman' ......................................[done]
Repository 'packman' has been removed.
```


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)