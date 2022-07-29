# Linux Essentials Topic 5: Security and File Permissions

## Lesson 5-1 Basic Security and Identifying User Types

This lesson will focus on the basic terminology of the accounts, access controls and security of local Linux systems, the command line interface (CLI) tools in a Linux system for basic security access controls and the basic files to support user and group accounts, including those used for elementary privilege escalation.

Basic security in Linux systems is modeled after Unix access controls that, despite being nearly fifty years-old, are quite effective in comparison to some popular consumer operating systems of much newer lineage. Even some other, popular, Unix-based operating systems tend to “take liberties” that are focused on “ease-of-access,” while Linux does not.

Modern Linux desktop environments and interfaces simplify the creation and management of users and often automate the assignment of access controls when a user logs in — e.g., to the display, audio and other services — requiring virtually no manual system administrator intervention. However, it is important to understand the basic concepts of an underlying Linux operating system.

### Accounts
Security involves many concepts, one of the most common being the general concept of access controls. Before one can tackle file access controls such as ownership and permissions, one must understand the basic concepts of Linux user accounts, which are broken out into several types.

Every user on a Linux system has an associated account which besides login information (like username and password) also defines how, and where, the user can interact with the system. Privileges and access controls define the “boundaries” within which each user can operate.

### Identifiers (UIDs/GIDs)
The User and Group Identifiers (UIDs/GIDs) are the basic, enumerated references to accounts. Early implementations were limited 16-bit (values 0 to 65535) integers, but 21st century systems support 64-bit UIDs and GIDs. Users and groups are enumerated independently, so the same ID can stand for both a user and group.

Every user has not only an UID, but also a primary GID. The primary GID for a user can be unique to that user alone, and may end up not being used by any other users. However, this group could also be a group shared by numerous users. In addition to these primary groups, each user can be member of other groups, too.

By default on Linux systems, every user is assigned to a group with the same name as their username, and same GID as his UID. E.g., create a new user named `newuser` and, by default, its default group is `newuser` as well.

### The Superuser Account
On Linux the superuser account is `root`, which always has UID 0. The superuser is sometimes called the system administrator, and has unlimited access and control over the system, including other users.

The default group for the superuser has the GID `0` and is also named `root`. The home directory for the superuser is a dedicated, top level directory, `/root`, only accessible by the `root` user himself.

### Standard User Accounts
All accounts other than `root` are technically regular user accounts, but on a Linux system the colloquial term user account often means a “regular” (unprivileged) user account. They typically have the following properties, with select exceptions:

- UIDs starting at 1000 (4 digits), although some legacy systems may start at 500.
- A defined home directory, usually a subdirectory of /home, depending on the site-local configuration.
- A defined login shell. In Linux the default shell is usually the Bourne Again Shell (/bin/bash), though others may be available.

If a user account does not have a valid shell in their attributes, the user will not be able to open an interactive shell. Usually `/sbin/nologin` is used as an invalid shell. This may be purposeful, if the user will only be authenticated for services other than console or SSH access, e.g., Secure FTP (`sftp`) access only.

```
Note
To avoid confusion, the term user account will only apply to standard or regular user accounts going forward. E.g., system account will be used to explain a Linux user account that is of the system user account type.
```

### System Accounts
System accounts are typically pre-created at system installation time. These are for facilities, programs and services that will not run as the superuser. In an ideal world, these would all be operating system facilities.

The system accounts vary, but their attributes include:

- UIDs are usually under 100 (2-digit) or 500-1000 (3-digit).
- Either no dedicated home directory or a directory that is usually not under `/home`.
- No valid login shell (typically `/sbin/nologin`), with rare exceptions.

Most system accounts on Linux will never login, and do not need a defined shell in their attributes. Many processes owned and executed by system accounts are forked into their own environment by the system management, running with the specified, system account. These accounts usually have limited or, more often than not, no privileges.

```
Note
From the standpoint of the LPI Linux Essentials, system accounts are UIDs <1000, with 2 or 3 digit UIDs (and GIDs).
```
In general, the system accounts should not have a valid login shell. The opposite would be a security issue in most cases.

### Service Accounts
Service accounts are typically created when services are installed and configured. Similar to system accounts, these are for facilities, programs and services that will not run as the superuser.

In much documentation, system and service accounts are similar, and interchanged often. This includes the location of home directories typically being outside of `/home`, if defined at all (service accounts are often more likely to have one, compared to system accounts), and no valid login shell. Although there is no strict definition, the primary difference between system and service accounts breaks down to UID/GID.

System account
- UID/GID <100 (2-digit) or <500-1000 (3-digit)

Service account
- UID/GID >1000 (4+ digit), but not a “standard” or “regular” user account, an account for services, with an UID/GID >1000 (4+ digits)

Some Linux distributions still have pre-reserved service accounts under UID <100, and those could be considered a system account as well, even though they are not created at system installation. E.g., on Fedora-based (including Red Hat) Linux distributions, the user for the Apache Web server has UID (and GID) 48, clearly a system account, despite having a home directory (usually at `/usr/share/httpd` or `/var/www/html/`).
```
Note
From the standpoint of the LPI Linux Essentials, system accounts are UIDs <1000, and regular user accounts are UIDs >1000. Since the regular user accounts are >1000, these UIDs can also include service accounts.
```

### Login Shells and Home Directories
Some accounts have a login shell, while others do not for security purposes as they do not require interactive access. The default login shell on most Linux distributions is the Bourne Again Shell, or `bash`, but other shells may be available, like the C Shell (`csh`), Korn shell (`ksh`) or  shell (`zsh`), to name a few.

A user can change his login shell using the `chsh` command. By default the command runs in interactive mode, and displays a prompt asking which shell should be used. The answer should be the full path to the shell binary, like below:
```
$ chsh

Changing the login shell for emma
Enter the new value, or press ENTER for the default
	Login Shell [/bin/bash]: /usr/bin/zsh
```

You can also run the command in non-interactive mode, with the `-s` parameter followed by the path to the binary, like so:

```
$ chsh -s /usr/bin/zsh
```

Most accounts have a defined home directory. On Linux, this is usually the only location where that user account has guaranteed write access, with some exceptions (e.g., temporary file system areas). However, some accounts are purposely setup to not have any write access to even their own home directory, for security purposes.

### Getting Information About Your Users
Listing basic user information is a common, everyday practice on a Linux system. In some cases, users will need to switch users and raise privilege to complete privileged tasks.

Even users have the ability to list attributes and access from the command line, using the commands below. Basic information under limited context is not a privileged operation.

Listing the current information of a user at the command line is as simple as a two letter command, `id`. The output will vary based on your login ID:
```
$ id
uid=1024(emma) gid=1024(emma) 1024(emma),20(games),groups=10240(netusers),20480(netadmin) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

In the preceding listing, the user (emma) has identifiers which breakdown as follows:

- 1024 = User ID (UID), followed by the username (common name aka login name) in parenthesis.
- 1024 = the primary Group ID (GID), followed by the groupname (common name) in parenthesis.
- A list of additional GIDs (groupnames) the user also belongs to.

Listing the last time users have logged into the system is done with the command `last`:

```
$ last
emma     pts/3        ::1              Fri Jun 14 04:28   still logged in
reboot   system boot  5.0.17-300.fc30. Fri Jun 14 04:03   still running
reboot   system boot  5.0.17-300.fc30. Wed Jun  5 14:32 - 15:19  (00:46)
reboot   system boot  5.0.17-300.fc30. Sat May 25 18:27 - 19:11  (00:43)
reboot   system boot  5.0.16-100.fc28. Sat May 25 16:44 - 17:06  (00:21)
reboot   system boot  5.0.9-100.fc28.x Sun May 12 14:32 - 14:46  (00:14)
root     tty2                          Fri May 10 21:55 - 21:55  (00:00)
	...
```
The information listed in columns may vary, but some notable entries in the preceding listing are:
- A user (`emma`) logged in via the network (pseudo TTY `pts/3`) and is still logged in.
- The time of current boot is listed, along with the kernel. In the example above, about 25 minutes before the user logged in.
- The superuser (`root`) logged in via a virtual console (TTY `tty2`), briefly, in mid-May.

A variant of the `last` command is the `lastb` command, which lists all the last bad login attempts.

The commands `who` and `w` list only the active logins on the system:
```
$ who
emma  pts/3        2019-06-14 04:28 (::1)

$ w
 05:43:41 up  1:40,  1 user,  load average: 0.25, 0.53, 0.51
USER     TTY        LOGIN@   IDLE   JCPU   PCPU WHAT
emma  pts/3     04:28    1:14m  0.04s  0.04s -bash
```

Both commands list some of the same information. For example, one user (`emma`) is logged in with a pseudo TTY device (`pts/3`) and the time of login is 04:28.

The `w` command lists more information, including the following:
- The current time and how long the system has been up
- How many users are connected
- The load averages for the past 1, 5 and 15 minutes

And the additional information for each active user session.
- Select, total CPU utilization times (`IDLE`, `JCPU` and `PCPU`)
- The current process (`-bash`). The total CPU utilization time of that process is the last item (`PCPU`).

Both commands have further options to list various, additional information.

### Switching Users and Escalating Privilege
In an ideal world, users would never need to escalate privilege to complete their tasks. The system would always “just work” and everything would be configured for various access.

Luckily for us, Linux — out-of-the-box — works like this for most users who are not system administrators, despite always following the least privilege security model.

However, there are commands that allow for privilege escalation when needed. Two of the most important ones are `su` and `sudo`.

On most Linux systems today, the `su` command is only used for escalating privileges to root, which is the default user if a username is not specified after the command name. While it can be used to switch to another user, it is not good practice: users should login from another system, over the network, or physical console or terminal on the system.
```
emma ~$ su -
Password:
root ~#
```
After entering the superuser (`root`) password, the user has a superuser shell (notice the `#` at the end of the command prompt) and is, for all intents and purposes, the superuser (`root`).

Sharing passwords is a very bad security practice, so there should be very few, if any, need for the `su` command in a modern Linux system.

The dollar symbol (`$`) should terminate the command line prompt for a non-privileged user shell, while the pound symbol (`#`) should terminate the command line prompt for the superuser (`root`) shell prompt. It is highly recommend that final character of any prompt never be changed from this “universally understood” standard, since this nomenclature is used in learning materials, including these.
```
Warning
Never switch to the superuser (root) without passing the login shell (-) parameter. Unless explicitly instructed otherwise by the OS or software vendor when su is required, always execute su - with extremely limited exceptions. User environments may cause undesirable configuration changes and issues when used in full privilege mode as superuser.
```
What is the biggest issue with using `su` to switch to the superuser (`root`)? If a regular user’s session has been compromised, the superuser (root) password could be captured. That’s where “Switch User Do” (or “Superuser Do”) comes in:

```
$ cat /sys/devices/virtual/dmi/id/board_serial
cat: /sys/devices/virtual/dmi/id/board_serial: Permission denied

$ sudo cat /sys/devices/virtual/dmi/id/board_serial
[sudo] password for emma:
/6789ABC/
```

In the preceding listing, the user is attempting to look up the serial number of their system board. However, the permission is denied, as that information is marked privileged.

However, using `sudo`, the user enters his own password to authenticate who he is. If he has been authorized in the `sudoers` configuration to run that command with privilege, with the options allowed, it will work.
```
Tip
By default, the first authorized sudo command will authenticate subsequent sudo commands for a (very short) period of time. This is configurable by the system administrator.
```

### Access Control Files
Nearly all operating systems have a set of places used to store access controls. In Linux these are typically text files located under the `/etc` directory, which is where system configuration files should be stored. By default, this directory is readable by every user on the system, but writable only by `root`.

The main files related to user accounts, attributes and access control are:

`/etc/passwd`
- This file stores basic information about the users on the system, including UID and GID, home directory, shell, etc. Despite the name, no passwords are stored here.

`/etc/group`
- This file stores basic information about all user groups on the system, like group name and GID and members.

`/etc/shadow`
- This is where user passwords are stored. They are hashed, for security.

`/etc/gshadow`
- This file stores more detailed information about groups, including a hashed password which lets users temporarily become a member of the group, a list of users who can become a member of the group at and time and a list of group administrators.
```
Warning
These files are not designed to and should never be edited directly. This lesson only covers the information stored in these files, and not editing these files.
```
By default, every user can enter `/etc` and read the files `/etc/passwd` and `/etc/group`. And also by default no user, except `root`, may read the files `/etc/shadow` or `/etc/gshadow`.

There are also files involved with basic privilege escalation on Linux systems, like on the commands `su` and `sudo`. By default, these are only accessible by the `root` user.

`/etc/sudoers`
- This file controls who can use the sudo command, and how.

`/etc/sudoers.d`
- This directory may contain files that supplement the settings on the sudoers file.

From the standpoint of the LPI Linux Essentials exam, just know the path and filename of the default sudo configuration file, `/etc/sudoers`. Its configuration is beyond the scope of these materials.
```
Warning
Even though /etc/sudoers is a text file, it should never be edited directly. If any changes to its contents are needed, they should be made using the visudo utility.
```

#### `/etc/passwd`
The file `/etc/passwd` is commonly referred to as the “password file”. Each line contains multiple fields always delimited by a colon (`:`). Despite the name, the actual one-way password hash is nowadays not stored in this file.

The typical syntax for a line on this file is:
```
USERNAME:PASSWORD:UID:GID:GECOS:HOMEDIR:SHELL
```
Where:

`USERNAME`
- The username aka login (name), like `root`, `nobody`, `emma`.

`PASSWORD`
- Legacy location of the password hash. Almost always `x`, indicating that the password is stored in the file `/etc/shadow`.

`UID`
- User ID (UID), like `0`, `99`, `1024`.

`GID`
- Default Group ID (GID), like `0`, `99`, `1024`.

`GECOS`
- A CSV list of user information including name, location, phone number. For example: `Emma Smith,42 Douglas St,555.555.5555`

`HOMEDIR`
- Path to the user’s home directory, like `/root`, `/home/emma`, etc.

`SHELL`
- The default shell for this user, like `/bin/bash`, `/sbin/nologin`, `/bin/ksh`, etc.

For example, the following line describes the user emma:
```
emma:x:1000:1000:Emma Smith,42 Douglas St,555.555.5555:/home/emma:/bin/bash
```
#### Understanding the `GECOS` Field

The GECOS field contains three (3) or more fields, delimited by a comma (`,`), aka a list of Comma Separated Values (CSV). Although there is no enforced standard, the fields are usually in the following order:
```
NAME,LOCATION,CONTACT
```
Where:

`NAME`
- is the user’s “Full Name”, or the “Software Name” in the case of a service account.

`LOCATION`
- is usually the user’s physical location within a building, room number or the contact department or person in the case of a service account.

`CONTACT`
- lists contact information such as home or work telephone number.

Additional fields may include additional contact information, such as a home number or e-mail address. To change the information on the GECOS field, use the `chfn` command and answer the questions, like below. If no username is provided after the command name, you will change information for the current user:

```
$ chfn

Changing the user information for emma
Enter the new value, or press ENTER for the default
	Full Name: Emma Smith
	Room Number []: 42
	Work Phone []: 555.555.5555
	Home Phone []: 555.555.6666
```

### `/etc/group`
The `/etc/group` file contains fields always delimited by a colon (:), storing basic information about the groups on the system. It is sometimes called the “group file”. The syntax for each line is:
```
NAME:PASSWORD:GID:MEMBERS
```
Where:

`NAME`
- is the group name, like `root`, `users`, `emma`, etc.

`PASSWORD`
- legacy location of an optional group password hash. Almost always `x`, indicating that the password (if defined) is stored in the file `/etc/gshadow`.

`GID`
- Group ID (GID), like `0`, `99`, `1024`.

`MEMBERS`
- a comma-separated list of usernames which are members of the group, like `jsmith`,`emma`.

The example below shows a line containing information about the `students` group:
```
students:x:1023:jsmith,emma
```
The user does not need to be listed in the members field when the group is the primary group for a user. If a user is listed, then it is redundant — i.e., there is no change in functionality, listed or not.
```
Note
Use of passwords for groups are beyond the scope of this section, however if defined the password hash is stored in the file /etc/gshadow. This is also beyond the scope of this section.
```

### `/etc/shadow`
The following table lists the attributes stored in the file `/etc/shadow`, commonly referred to as the “shadow file”. The file contains fields always delimited by a colon (`:`). Although the file has many fields, most are beyond the scope of this lesson, other than the first two.

The basic syntax for a line on this file is:
```
USERNAME:PASSWORD:LASTCHANGE:MINAGE:MAXAGE:WARN:INACTIVE:EXPDATE
```
Where:

`USERNAME`
- The username (same as /etc/passwd), like root, nobody, emma.

`PASSWORD`
- A one-way hash of the password, including preceding salt. For example: !!, !$1$01234567$ABC…​, $6$012345789ABCDEF$012…​.

`LASTCHANGE`
- Date of the last password change in days since the “epoch”, such as 17909.

`MINAGE`
- Minimum password age in days.

`MAXAGE`
- Maximum password age in days.

`WARN`
- Warning period before password expiration, in days.

`INACTIVE`
- Maximum password age past expiration, in days.

`EXPDATE`
- Date of password expiration, in days since the “epoch”.

In the example below you can see a sample entry from the `/etc/shadow` file. Note that some values, like `INACTIVE` and `EXPDATE` are undefined.

```
emma:$6$nP532JDDogQYZF8I$bjFNh9eT1xpb9/n6pmjlIwgu7hGjH/eytSdttbmVv0MlyTMFgBIXESFNUmTo9EGxxH1OT1HGQzR0so4n1npbE0:18064:0:99999:7:::
```

The “epoch” of a POSIX system is midnight (0000), Universal Coordinate Time (UTC), on Thursday, January 1st, 1970. Most POSIX dates and times are in either seconds since “epoch”, or in the case of the file `/etc/shadow`, days since “epoch”.

```
Note
The shadow file is designed to be only readable by the superuser, and select, core system authentication services that check the one-way password hash at login or other authentication-time.
```

Although different authentication solutions exist, the elementary method of password storage is the one-way hash function. This is done so the password is never stored in clear-text on a system, as the hashing function is not reversible. You can turn a password into a hash, but (ideally) it is not possible to turn a hash back into a password.

At most, a brute force method is required to hash all combinations of a password, until one matches. To mitigate the issue of a password hash being cracked on one system, Linux systems use a random “salt” on each password hash for a user. So the hash for a user password on one Linux system will usually not be the same as on another Linux system, even if the password is the same.

In the file `/etc/shadow`, the password may take several forms. These forms typically include the following:

`!!`
- This means a “disabled” account (no authentication possible), with no password hash stored.

`!$1$01234567$ABC…​`
- A “disabled” account (due to the initial exclamation mark), with a prior hash function, hash salt and hash string stored.

`$1$0123456789ABC$012…​`
- An “enabled” account, with a hash function, hash salt and hash string stored.

The hash function, hash salt and hash string are preceded and delimited by a dollar symbol (`$`). The hash salt length must be between eight and sixteen (8-16) characters. Examples of the three most common are as follows:

`$1$01234567$ABC…​`
- A hash function of MD5 (1), with an example hash length of eight.

`$5$01234567ABCD$012…​`
- A hash function of SHA256 (5), with an example hash length of twelve.

`$6$01234567ABCD$012…​`
- A hash function of SHA512 (6), with an example hash length of twelve.

```
Note
The MD5 hash function is considered cryptographically insecure with today’s (2010s and later) level of ASIC and even general computing SIMD performance. E.g., the US Federal Information Processing Standards (FIPS) does not allow MD5 to be used for any cryptographic functions, only very limited aspects of validation, but not the integrity of digital signatures or similar purposes.
```
From the standpoint of the LPI Linux Essentials objectives and exam, just understand the password hash for a local user is only stored in the file `/etc/shadow` which only select, authentication services can read, or the superuser can modify via other commands.


## Lesson 5.2 Creating Users and Groups

Managing users and groups on a Linux machine is one of the key aspects of system administration. In fact, Linux is a multi-user operating system in which multiple users can use the same machine at the same time.

Information about users and groups is stored in four files within the `/etc/` directory tree:

`/etc/passwd`
- a file of seven colon-delimited fields containing basic information about users

`/etc/group`
- a file of four colon-delimited fields containing basic information about groups

`/etc/shadow`
- a file of nine colon-delimited fields containing encrypted user passwords

`/etc/gshadow`
- a file of four colon-delimited fields file containing encrypted group passwords

All of these files are updated by a suite of command-line tools for user and group management, which we’ll discuss later in this lesson. They can also be managed by graphical applications, specific to each Linux distribution, which provide simpler and more immediate management interfaces.

```
Warning
Even though the files are plain text, do not edit them directly. Always use the tools provided with your distribution for this purpose.
```

### The File `/etc/passwd`
`/etc/passwd` is a world-readable file that contains a list of users, each on a separate line:
```
frank:x:1001:1001::/home/frank:/bin/bash
```

Each line consists of seven colon-delimited fields:

Username
- The name used when the user logs into the system.

Password
- The encrypted password (or an x if shadow passwords are used).

User ID (UID)
- The ID number assigned to the user in the system.

Group ID (GID)
- The primary group number of the user in the system.

GECOS
- An optional comment field, which is used to add extra information about the user (such as the full name). The field can contain multiple comma-separated entries.

Home directory
- The absolute path of the user’s home directory.

Shell
- The absolute path of the program that is automatically launched when the user logs into the system (usually an interactive shell such as `/bin/bash`).

### The File `/etc/group`
`/etc/group` is a world-readable file that contains a list of groups, each on a separate line:
```
developer:x:1002:
```

Each line consists of four colon-delimited fields:

Group Name
- The name of the group.

Group Password
- The encrypted password of the group (or an x if shadow passwords are used).

Group ID (GID)
- The ID number assigned to the group in the system.

Member list
- A comma-delimited list of users belonging to the group, except those for whom this is the primary group.

### The File `/etc/shadow`
`/etc/shadow` is a file readable only by root and users with root privileges and contains the encrypted passwords of the users, each on a separate line:
```
frank:$6$i9gjM4Md4MuelZCd$7jJa8Cd2bbADFH4dwtfvTvJLOYCCCBf/.jYbK1IMYx7Wh4fErXcc2xQVU2N1gb97yIYaiqH.jjJammzof2Jfr/:18029:0:99999:7:::
```

Each line consists of nine colon-delimited fields:

Username
- The name used when user logs into the system.

Encrypted password
- The encrypted password of the user (if the value is !, the account is locked).

Date of last password change
- The date of the last password change, as number of days since 01/01/1970. A value of 0 means that the user must change the password at the next access.

Minimum password age
- The minimum number of days, after a password change, which must pass before the user will be allowed to change the password again.

Maximum password age
- The maximum number of days that must pass before a password change is required.

Password warning period
- The number of days, before the password expires, during which the user is warned that the password must be changed.

Password inactivity period
- The number of days after a password expires during which the user should update the password. After this period, if the user does not change the password, the account will be disabled.

Account expiration date
- The date, as number of days since 01/01/1970, in which the user account will be disabled. An empty field means that the user account will never expire.

A reserved field
- A field that is reserved for future use.

### The File `/etc/gshadow`
`/etc/gshadow` is a file readable only by root and by users with root privileges that contains encrypted passwords for groups, each on a separate line:
```
developer:$6$7QUIhUX1WdO6$H7kOYgsboLkDseFHpk04lwAtweSUQHipoxIgo83QNDxYtYwgmZTCU0qSCuCkErmyR263rvHiLctZVDR7Ya9Ai1::
```

Each line consists of four colon-delimited fields:

Group name
- The name of the group.

Encrypted password
- The encrypted password for the group (it is used when a user, who is not a member of the group, wants to join the group using the `newgrp` command — if the password starts with `!`, no one is allowed to access the group with `newgrp`).

Group administrators
- A comma-delimited list of the administrators of the group (they can change the password of the group and can add or remove group members with the `gpasswd` command).

Group members
- A comma-delimited list of the members of the group.

Now that we’ve seen where user and group information is stored, let’s talk about the most important command-line tools to update these files.

### Adding and Deleting User Accounts
In Linux, you add a new user account with the `useradd` command, and you delete a user account with the `userdel` command.

If you want to create a new user account named `frank` with a default setting, you can run the following:
```
# useradd frank
```

After creating the new user, you can set a password using passwd:

```
# passwd frank
Changing password for user frank.
New UNIX password:
Retype new UNIX password:
passwd: all authentication tokens updated successfully.
```
Both of these commands require root authority. When you run the `useradd` command, the user and group information stored in password and group databases are updated for the newly created user account and, if specified, the home directory of the new user is created as well as a group with the same name as the user account.

```
Tip
Remember that you can always use the grep utility to filter the password and group databases, displaying only the entry that refers to a specific user or group. For the above example you can use

cat /etc/passwd | grep frank

or

grep frank /etc/passwd

to see basic information on the newly created frank account.
```

The most important options which apply to the useradd command are:

-c
- Create a new user account with custom comments (for example full name).

`-d`
- Create a new user account with a custom home directory.

`-e`
- Create a new user account by setting a specific date on which it will be disabled.

`-f`
- Create a new user account by setting the number of days after the password expires during which the user should update the password.

`-g`
- Create a new user account with a specific GID

`-G`
- Create a new user account by adding it to multiple secondary groups.

`-m`
- Create a new user account with its home directory.

`-M`
- Create a new user account without its home directory.

`-s`
- Create a new user account with a specific login shell.

`-u`
- Create a new user account with a specific UID.

Once the new user account is created, you can use the id and groups commands to find out its UID, GID and the groups to which it belongs.
```
# id frank
uid=1000(frank) gid=1000(frank) groups=1000(frank)
# groups frank
frank : frank
```
```
Tip
Remember to check and eventually edit the /etc/login.defs file, which defines the configuration parameters that control the creation of users and groups. For example, you can set the range of UIDs and GIDs that can be assigned to new user and group accounts, specify that you don’t need to use the -m option to create the new user’s home directory and if the system should automatically create a new group for each new user.
```
If you want to delete a user account, you can use the userdel command. In particular, this command updates the information stored in the account databases, deleting all entries referring to the specified user. The -r option also removes the user’s home directory and all of its contents, along with the user’s mail spool. Other files, located elsewhere, must be searched for and deleted manually.
```
# userdel -r frank
```

As before, you need root authority to delete user accounts.

### The Skeleton Directory
When you add a new user account, even creating its home directory, the newly created home directory is populated with files and folders that are copied from the skeleton directory (by default `/etc/skel`). The idea behind this is simple: a system administrator wants to add new users having the same files and directories in their home. Therefore, if you want to customize the files and folders that are created automatically in the home directory of the new user accounts, you must add these new files and folders to the skeleton directory.
```
Tip
Note that the profile files that are usually found in the skeleton directory are hidden files. Therefore, if you want to list all the files and folders in the skeleton directory, which will be copied to the home dir of the newly created users, you must use the ls -Al command.
```

### Adding and Deleting Groups
As for group management, you can add or delete groups using the `groupadd` and `groupdel` commands.

If you want to create a new group named `developer`, you can run the following command as root:
```
# groupadd -g 1090 developer
```
The `-g` option of this command creates a group with a specific GID.

If you want to delete the `developer` group, you can run the following:
```
# groupdel developer
```
```
Warning
Remember that when you add a new user account, the primary group and the secondary groups to which it belongs must exist before launching the useradd command. Also, you cannot delete a group if it is the primary group of a user account.
```
### The `passwd` Command
This command is primarily used to change a user’s password. Any user can change their password, but only root can change any user’s password.

Depending on the `passwd` option used, you can control specific aspects of password aging:

`-d`
- Delete the password of a user account (thus setting an empty password, making it a passwordless account).

`-e`
- Force the user account to change the password.

`-l`
- Lock the user account (the encrypted password is prefixed with an exclamation mark).

`-u`
- Unlock the user account (it removes the exclamation mark).

`-S`
- -Output information about the password status for a specific account.

These options are available only for root. To see the full list of options, refer to the man pages.

___
## 5.3 Managing File Permissions and Ownership

Being a multi-user system, Linux needs some way to track who owns each file and whether or not a user is allowed to perform actions on that file. This is to ensure the privacy of users who might want to keep the content of their files confidential, as well as to ensure collaboration by making certain files accessible to multiple users.

This is done through a three-level permissions system: every file on disk is owned by a user and a user group and has three sets of permissions: one for its owner, one the group who owns the file and one for everyone else. In this lesson, you will learn how to query the permissions for a file and how to manipulate them.

### Querying Information about Files and Directories
The command `ls` is used to get a listing of the contents of any directory. In this basic form, all you get are the filenames:

```
$ ls
Another_Directory  picture.jpg  text.txt
```
But there is much more information available for each file, including type, size, ownership and more. To see this information you must ask `ls` for a “long form” listing, using the `-l` parameter:
```
$ ls -l
total 536
drwxrwxr-x 2 carol carol   4096 Dec 10 15:57 Another_Directory
-rw------- 1 carol carol 539663 Dec 10 10:43 picture.jpg
-rw-rw-r-- 1 carol carol   1881 Dec 10 15:57 text.txt
```
Each column on the output above has a meaning:

- The first column on the listing shows the file type and permissions.
- For example, on `drwxrwxr-x`:
  - The first character, `d`, indicates the file type.
  - The next three characters, `rwx`, indicate the permissions for the owner of the file, also referred to as user or `u`.
  - The next three characters, `rwx`, indicate the permissions of the group owning the file, also referred to as `g`.
  - The last three characters, r-x, indicate the permissions for anyone else, also known as others or `o`.
- The second column indicates the number of hard links pointing to that file. For a directory, this means the number of subdirectories, plus a link to itself (`.`) and the parent directory (`..`).
- The third and fourth columns show ownership information: respectively the user and group that own the file.
- The fifth column shows the filesize, in bytes.
- The sixth column shows the precise date and time, or timestamp, when the file was last modified.
- The seventh and last column shows the file name.

If you wish to see the filesizes in “human readable” format, add the `-h` parameter to `ls`. Files less than one kilobyte in size will have the size shown in bytes. Files with more than one kilobyte and less than one megabyte will have a `K` added after the size, indicating the size is in kilobytes. The same follows for filesizes in the megabyte (`M`) and gigabyte (`G`) ranges:

```
$ ls -lh
total 1,2G
drwxrwxr-x 2 carol carol 4,0K Dec 10 17:59 Another_Directory
----r--r-- 1 carol carol    0 Dec 11 10:55 foo.bar
-rw-rw-r-- 1 carol carol 1,2G Dec 20 18:22 HugeFile.zip
-rw------- 1 carol carol 528K Dec 10 10:43 picture.jpg
---xr-xr-x 1 carol carol   33 Dec 11 10:36 test.sh
-rwxr--r-- 1 carol carol 1,9K Dec 20 18:13 text.txt
-rw-rw-r-- 1 carol carol 2,6M Dec 11 22:14 Zipped.zip
```

To only show information on a specific set of files, add the names of these files to `ls`:

```
$ ls -lh HugeFile.zip test.sh
total 1,2G
-rw-rw-r-- 1 carol carol 1,2G Dec 20 18:22 HugeFile.zip
---xr-xr-x 1 carol carol   33 Dec 11 10:36 test.sh
```

### What about Directories?
If you try to query information about a directory using `ls -l`, it will show you a listing of the directory contents instead:
```
$ ls -l Another_Directory/
total 0
-rw-r--r-- 1 carol carol 0 Dec 10 17:59 another_file.txt
```

To avoid this and query information about the directory itself, add the `-d` parameter to `ls`:

```
$ ls -l -d Another_Directory/
drwxrwxr-x 2 carol carol 4096 Dec 10 17:59 Another_Directory/
```

### Seeing Hidden Files
The directory listing we have retrieved using `ls -l` before is incomplete:

```
$ ls -l
total 544
drwxrwxr-x 2 carol carol   4096 Dec 10 17:59 Another_Directory
-rw------- 1 carol carol 539663 Dec 10 10:43 picture.jpg
-rw-rw-r-- 1 carol carol   1881 Dec 10 15:57 text.txt
```
There are three other files in that directory, but they are hidden. On Linux, files whose name starts with a period (`.`) are automatically hidden. To see them we need to add the `-a` parameter to `ls`:

```
$ ls -l -a
total 544
drwxrwxr-x 3 carol carol   4096 Dec 10 16:01 .
drwxrwxr-x 4 carol carol   4096 Dec 10 15:56 ..
drwxrwxr-x 2 carol carol   4096 Dec 10 17:59 Another_Directory
-rw------- 1 carol carol 539663 Dec 10 10:43 picture.jpg
-rw-rw-r-- 1 carol carol   1881 Dec 10 15:57 text.txt
-rw-r--r-- 1 carol carol      0 Dec 10 16:01 .thisIsHidden
```

The file `.thisIsHidden` is simply hidden because its name starts with `.`.

The directories `.` and `..` however are special. `.` is a pointer to the current directory, while `..` is a pointer to the parent directory (the directory which contains the current directory). In Linux, every directory contains at least these two special directories.

```
Tip
You can combine multiple parameters for ls (and many other Linux commands). ls -l -a can, for example, be written as ls -la.
```

### Understanding Filetypes
We have already mentioned that the first letter in each output of `ls -l` describes the type of the file. The three most common file types are:

`- `(normal file)
- A file can contain data of any kind. Files can be modified, moved, copied and deleted.

`d` (directory)
- A directory contains other files or directories and helps to organize the file system. Technically, directories are a special kind of file.

`l` (soft link)
- This “file” is a pointer to another file or directory elsewhere in the filesystem.

In addition to those, there are three other file types that you should at least know about, but are out of scope for this lesson:

`b` (block device)
- This file stands for a virtual or physical device, usually disks or other kinds of storage devices. For example, the first hard disk in the system might be represented by `/dev/sda`.

`c` (character device)
- This file stands for a virtual or physical device. Terminals (like the main terminal on `/dev/ttyS0`) and serial ports are common examples of character devices.

`s` (socket)
- Sockets serve as “conduits” passing information between two programs.

```
Warning
Do not alter any of the permissions on block devices, character devices or sockets, unless you know what you’re doing. This may prevent your system from working!
```

### Understanding Permissions
In the output of `ls -l` the file permissions are shown right after the filetype, as three groups of three characters each, in the order `r`, `w` and `x`. Here is what they mean. Keep in mind that a dash `-` represents the lack of a particular permission.

### Permissions on Files
`r`
- Stands for read and has an octal value of `4` (don’t worry, we will discuss octals shortly). This means permission to open a file and read its contents.

`w`
- Stands for write and has an octal value of `2`. This means permission to edit or delete a file.

`x`
- Stands for execute and has an octal value of `1`. This means that the file can be run as an executable or script.

So, for example, a file with permissions `rw-` can be read and written to, but cannot be executed.

### Permissions on Directories
`r`
- Stands for read and has an octal value of `4`. This means permission to read the directory’s contents, like filenames. But it does not imply permission to read the files themselves.

`w`
- Stands for write and has an octal value of `2`. This means permission to create or delete files in a directory, or change their names, permissions and owners. If a user has the write permission on a directory, the user can change permissions of any file in the directory, even if the user has no permissions on the file or if the file is owned by another user.

`x`
Stands for execute and has an octal value of `1`. This means permission to enter a directory, but not to list its files (for that, the `r` permission is needed).

The last bit about directories may sound a bit confusing. Let’s imagine, for example, that you have a directory named `Another_Directory`, with the following permissions:

```
$ ls -ld Another_Directory/
d--xr-xr-x 2 carol carol 4,0K Dec 20 18:46 Another_Directory
```

Also imagine that inside this directory you have a shell script called `hello.sh` with the following permissions:

```
-rwxr-xr-x 1 carol carol 33 Dec 20 18:46 hello.sh
```

If you are the user `carol` and try to list the contents of `Another_Directory`, you will get an error message, as your user lacks read permission for that directory:

```
$ ls -l Another_Directory/
ls: cannot open directory 'Another_Directory/': Permission denied
```


However, the user `carol` does have execute permissions, which means that she can enter the directory. Therefore, the user `carol` can access files inside the directory, as long as she has the correct permissions for the respective file. In this example, the user has full permissions for the script `hello.sh`, so she can run the script, even if she can’t read the contents of the directory containing it. All that is needed is the complete filename.

```
$ sh Another_Directory/hello.sh
Hello LPI World!
```

As we said before, permissions are specified in sequence: first for the owner of the file, then for the owning group, and then for other users. Whenever someone tries to perform an action on the file, the permissions are checked in the same fashion. First the system checks if the current user owns the file, and if this is true it applies the first set of permissions only. Otherwise, it checks if the current user belongs to the group owning the file. In that case, it applies the second set of permissions only. In any other case, the system will apply the third set of permissions. This means that if the current user is the owner of the file, only the owner permissions are effective, even if the group or other permissions are more permissive than the owner’s permissions.

### Modifying File Permissions

The command `chmod` is used to modify the permissions for a file, and takes at least two parameters: the first one describes which permissions to change, and the second one points to the file or directory where the change will be made. However, the permissions to change can be described in two different ways, or “modes”.

The first one, called symbolic mode offers fine grained control, allowing you to add or revoke a single permission without modifying others on the set. The other mode, called numeric mode, is easier to remember and quicker to use if you wish to set all permission values at once.

Both modes will lead to the same end result. So, for example, the commands:

```
$ chmod ug+rw-x,o-rwx text.txt
```
and

```
$ chmod 660 text.txt
```

will produce exactly the same output, a file with the permissions set:

```
-rw-rw---- 1 carol carol  765 Dec 20 21:25 text.txt
```

Now, let’s understand how each mode works.

### Symbolic Mode
When describing which permissions to change in symbolic mode the first character(s) indicate(s) whose permissions you will alter: the ones for the user (`u`), for the group (`g`), for others (`o`) and/or for all the three together (`a`).

Then you need to tell the command what to do: you can grant a permission (`+`), revoke a permission (`-`), or set it to a specific value (`=`).

Lastly, you specify which permission you wish to affect: read (`r`), write (`w`), or execute (`x`).

For example, imagine we have a file called `text.txt` with the following permission set:

```
$ ls -l text.txt
-rw-r--r-- 1 carol carol 765 Dec 20 21:25 text.txt
```

If you wish to grant write permissions to members of the group owning the file, you would use the `g+w` parameter. It is easier if you think about it this way: “For the group (`g`), grant (`+`) write permissions (`w`)”. So, the command would be:

```
$ chmod g+w text.txt
```

Let’s check the result with `ls`:

```
$ ls -l text.txt
-rw-rw-r-- 1 carol carol 765 Dec 20 21:25 text.txt
```

If you want to remove read permissions for the owner of the same file, think about it as: “For the user (`u`) revoke (`-`), read permissions (`r`)”. So the parameter is `u-r`, like so:

```
$ chmod u-r text.txt
$ ls -l text.txt
--w-rw-r-- 1 carol carol 765 Dec 20 21:25 text.txt
```

What if we want to set the permissions exactly as `rw-` for everyone? Then think of it as: “For all (`a`), set exactly (`=`), read (`r`), write (`w`), and no execute (`-`)”. So:

```
$ chmod a=rw- text.txt
$ ls -l text.txt
-rw-rw-rw- 1 carol carol 765 Dec 20 21:25 text.txt
```

Of course, it is possible to modify multiple permissions at the same time. In this case, separate them with a comma (`,`):

```
$ chmod u+rwx,g-x text.txt
$ ls -lh text.txt
-rwxrw-rw- 1 carol carol 765 Dec 20 21:25 text.txt
```

The example above can be read as: “For the user (`u`), grant (`+`) read, write and execute (`rwx`) permissions, for the group (`g`) revoke (`-`), execute permissions (`x`)”.

When run on a directory, `chmod` modifies only the directory’s permissions. `chmod` has a recursive mode, useful when you want to change the permissions for “all files inside a directory and its subdirectories”. To use this, add the parameter `-R` after the command name and before the permissions to change, like so:

```
$ chmod -R u+rwx Another_Directory/
```

This command can be read as: “Recursively (`-R`), for the user (`u`), grant (`+`) read, write and execute (`rwx`) permissions”.

```
Warning
Be careful and think twice before using the -R switch, as it is easy to change permissions on files and directories which you do not want to change, especially on directories with a large number of files and sub-directories.
```

### Numeric Mode

In numeric mode, the permissions are specified in a different way: as a three-digit numeric value on octal notation, a base-8 numeric system.

Each permission has a corresponding value, and they are specified in the following order: first comes read (`r`), which is `4`, then write (`w`), which is `2` and last is execute (`x`), represented by `1`. If there is no permission, use the value zero (`0`). So, a permission of rwx would be `7` (`4+2+1`) and `r-x` would be `5` `(4+0+1)`.

The first of the three digits on the permission set represents the permissions for the user (`u`), the second for the group (`g`) and the third for the other (`o`). If we wanted to set the permissions for a file to `rw-rw----`, the octal value would be `660`:

```
$ chmod 660 text.txt
$ ls -l text.txt
-rw-rw---- 1 carol carol 765 Dec 20 21:25 text.txt
```

Besides this, the syntax in numeric mode is the same as in symbolic mode, the first parameter represents the permissions you wish to change, and the second one points to the file or directory where the change will be made.

```
Tip
If a permission value is odd, the file surely is executable!
```

Which syntax to use? The numeric mode is recommended if you want to change the permissions to a specific value, for example `640` (`rw- r-- ---`).

The symbolic mode is more useful if you want to flip just a specific value, regardless of the current permissions for the file. For example, I can add execute permissions for the user using just `chmod u+x script.sh` without regard to, or even touching, the current permissions for the group and others.

### Modifying File Ownership
The command `chown` is used to modify the ownership of a file or directory. The syntax is quite simple:

```
chown username:groupname filename
```

For example, let’s check a file called `text.txt`:

```
$ ls -l text.txt
-rw-rw---- 1 carol carol 1881 Dec 10 15:57 text.txt
```

The user who owns the file is `carol`, and the group is also `carol`. Now, let’s modify the group owning the file to some other group, like `students`:

```
$ chown carol:students text.txt
$ ls -l text.txt
-rw-rw---- 1 carol students 1881 Dec 10 15:57 text.txt
```

Keep in mind that the user who owns a file does not need to belong to the group who owns a file. In the example above, the user `carol` does not need to be a member of the `students` group. However, she does have to member of the group in order to transfer the file’s group ownership to that group.

User or group can be omitted if you do not wish to change them. So, to change just the group owning a file you would use `chown :students text.txt`. To change just the user, the command would be `chown carol text.txt`. Alternatively, you could use the command `chgrp students text.txt` to change only the group.

Unless you are the system administrator (root), you cannot change ownership of a file owned by another user or a group that you don’t belong to. If you try to do this, you will get the error message `Operation not permitted`.

### Querying Groups
Before changing the ownership of a file, it might be useful to know which groups exist on the system, which users are members of a group and to which `groups` a user belongs. Those tasks can be accomplished with two commands, `groups` and `groupmems`.

To see which groups exist on your system, simply type `groups`:

```
$ groups
carol students cdrom sudo dip plugdev lpadmin sambashare
```

And if you want to know to which groups a user belongs, add the username as a parameter:

```
$ groups carol
carol : carol students cdrom sudo dip plugdev lpadmin sambashare
```

To do the reverse, displaying which users belong to a group, use `groupmems`. The parameter `-g` specifies the group, and `-l` will list all of its members:

```
$ sudo groupmems -g cdrom -l
carol
```

```
Tip
groupmems can only be run as root, the system administrator. If you are not currently logged in as root, add sudo before the command.
```

### Special Permissions
Besides the read, write and execute permissions for user, group and others, each file can have three other special permissions which can alter the way a directory works or how a program runs. They can be specified either in symbolic or numeric mode, and are as follows:

### Sticky Bit
The sticky bit, also called the restricted deletion flag, has the octal value `1` and in symbolic mode is represented by a `t` within the other’s permissions. This applies only to directories, and on Linux it prevents users from removing or renaming a file in a directory unless they own that file or directory.

Directories with the sticky bit set show a `t` replacing the `x` on the permissions for others on the output of `ls -l`:

```
$ ls -ld Sample_Directory/
drwxr-xr-t 2 carol carol 4096 Dec 20 18:46 Sample_Directory/
```

In numeric mode, the special permissions are specified using a “4-digit notation”, with the first digit representing the special permission to act upon. For example, to set the sticky bit (value `1`) for the directory `Another_Directory` in numeric mode, with permissions `755`, the command would be:

```
$ chmod 1755 Another_Directory
$ ls -ld Another_Directory
drwxr-xr-t 2 carol carol 4,0K Dec 20 18:46 Another_Directory
```

### Set GID
Set GID, also known as SGID or Set Group ID bit, has the octal value `2` and in symbolic mode is represented by an `s` on the group permissions. This can be applied to executable files or directories. On executable files, it will grant the process resulting from executing the file access to the privileges of the group who owns the file. When applied to directories, it will make every file or directory created under it inherit the group from the parent directory.

Files and directories with SGID bit show an `s` replacing the `x` on the permissions for the group on the output of `ls -l`:

```
$ ls -l test.sh
-rwxr-sr-x 1 carol carol 33 Dec 11 10:36 test.sh
```

To add SGID permissions to a file in symbolic mode, the command would be:

```
$ chmod g+s test.sh
$ ls -l test.sh
-rwxr-sr-x 1 carol root     33 Dec 11 10:36 test.sh
```

The following example will make you better understand the effects of SGID on a directory. Suppose we have a directory called `Sample_Directory`, owned by the user `carol` and the group `users`, with the following permission structure:

```
$ ls -ldh Sample_Directory/
drwxr-xr-x 2 carol users 4,0K Jan 18 17:06 Sample_Directory/
```

Now, let’s change to this directory and, using the command `touch`, create an empty file inside it. The result would be:

```
$ cd Sample_Directory/
$ touch newfile
$ ls -lh newfile
-rw-r--r-- 1 carol carol 0 Jan 18 17:11 newfile
```

As we can see, the file is owned by the user `carol` and group `carol`. But, if the directory had the SGID permission set, the result would be different. First, let’s add the SGID bit to the `Sample_Directory` and check the results:

```
$ sudo chmod g+s Sample_Directory/
$ ls -ldh Sample_Directory/
drwxr-sr-x 2 carol users 4,0K Jan 18 17:17 Sample_Directory/
```

The `s` on the group permissions indicates that the SGID bit is set. Now, let’s change to this directory and, again, create an empty file with the `touch` command:

```
$ cd Sample_Directory/
$ touch emptyfile
$ ls -lh emptyfile
 -rw-r--r-- 1 carol users 0 Jan 18 17:20 emptyfile
```

As we can see, the group who owns the file is `users`. This is because the SGID bit made the file inherit the group owner of its parent directory, which is `users`.

### Set UID
SUID, also known as Set User ID, has the octal value `4` and is represented by an `s` on the user permissions in symbolic mode. It only applies to files and its behavior is similar to the SGID bit, but the process will run with the privileges of the user who owns the file. Files with the SUID bit show an `s` replacing the `x` on the permissions for the user on the output of `ls -l`:
```
$ ls -ld test.sh
-rwsr-xr-x 1 carol carol 33 Dec 11 10:36 test.sh
```
You can combine multiple special permissions in one parameter by adding them together. So, to set SGID (value `2`) and SUID (value `4`) in numeric mode for the script `test.sh` with permissions `755`, you would type:
```
$ chmod 6755 test.sh
```
And the result would be:

```
$ ls -lh test.sh
-rwsr-sr-x 1 carol carol 66 Jan 18 17:29 test.sh
```

```
Tip
If your terminal supports color, and these days most of them do, you can quickly see if these special permissions are set by glancing at the output of ls -l. For the sticky bit, the directory name might be shown in a black font with blue background. The same applies for files with the SGID (yellow background) and SUID (red background) bits. Colors may be different depending on which Linux distribution and terminal settings you use.
```

## Lesson Special Directories and Files



___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/010-160/)