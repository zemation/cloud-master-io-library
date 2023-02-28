# LPIC-1.107: Administrative Tasks

## Lesson 107.1 Manage user and group accounts and related system files

User and group administration is a very important part of any system administrator’s job. Modern Linux distributions implement graphical interfaces that allow you to manage all the activities related to this key aspect quickly and easily. These interfaces are different from each other in terms of graphical layouts, but the features are the same. With these tools you can view, edit, add, and delete local users and groups. However for more advanced management you need to work through the command line.

### Adding User Accounts
In Linux, you can add a new user account with the `useradd` command. For example, acting with root privileges, you can create a new user account named `michael` with a default setting, using the following:

```
# useradd michael
```
When you run the `useradd` command, the user and group information stored in the password and group databases are updated for the newly created user account and, if specified, the home directory of the new user is created as well. A group with the same name of the new user account is also created.

Once you have created the new user, you can set its password using the `passwd` command. You can review its User ID (UID), Group ID (GID) and the groups it belongs to through the `id` and `groups` commands.
```
# passwd michael
Changing password for user michael.
New UNIX password:
Retype new UNIX password:
passwd: all authentication tokens updated successfully.
# id michael
uid=1000(michael) gid=100(michael) groups=100(michael)
# groups michael
michael : michael
```
```
Note
Remember that any user can review their UID, GID and the groups they belong to by simply using the id and groups commands without arguments, and that any user can change their password using the passwd command. However only users with root privileges can change any user’s password.
```
The most important options which apply to the `useradd` command are:

`-c`
- Create a new user account with custom comments (for example the user’s full name).

`-d`
- Create a new user account with a custom home directory.

`-e`
- Create a new user account by setting a specific date on which it will be disabled.

`-f`
- Create a new user account by setting the number of days after a password expires during which the user should update the password (otherwise the account will be disabled).

`-g`
- Create a new user account with a specific GID.

`-G`
- Create a new user account by adding it to multiple secondary groups.

`-k`
- Create a new user account by copying the skeleton files from a specific custom directory (this option is only valid if the `-m` or `--create-home` option is specified).

`-m`
- Create a new user account with its home directory (if it does not exist).

`-M`
- Create a new user account without its home directory.

`-s`
- Create a new user account with a specific login shell.

`-u`
- Create a new user account with a specific UID.

See the manual pages for the `useradd` command for the complete list of options.

### Modifying User Accounts
Sometimes you need to change an attribute of an existing user account, such as the login name, login shell, password expiry date and so on. In such cases, you need to use the `usermod` command.
```
# usermod -s /bin/tcsh michael
# usermod -c "Michael User Account" michael
```
Just as with the `useradd` command, the `usermod` command requires root privileges.

In the examples above, the login shell of `michael` is changed first and then a brief description is added to this user account. Remember that you can modify multiple attributes at once, specifying them in a single command.

The most important options which apply to the `usermod` command are:

`-c`
- Add a brief comment to the specified user account.

`-d`
- Change the home directory of the specified user account. When used with the `-m` option, the contents of the current home directory are moved to the new home directory, which is created if it does not already exist.

`-e`
- Set the expiration date of the specified user account.

`-f`
- Set the number of days after a password expires during which the user should update the password (otherwise the account will be disabled).

`-g`
- Change the primary group of the specified user account (the group must exist).

`-G`
- Add secondary groups to the specified user account. Each group must exist and must be separated from the next by a comma, with no intervening whitespace. If used alone, this option removes all existing groups to which the user belongs, while when used with the `-a` option, it simply appends new secondary groups to the existing ones.

`-l`
- Change the login name of the specified user account.

`-L`
- Lock the specified user account. This puts an exclamation mark in front of the encrypted password within the /etc/shadow file, thus disabling access with a password for that user.

`-s`
- Change the login shell of the specified user account.

`-u`
- Change the UID of the specified user account.

`-U`
- Unlock the specified user account. This removes the exclamation mark in front of the encrypted password with the `/etc/shadow` file.

See the manual pages for the `usermod` command for the complete list of options.
```
Tip
Remember that when you change the login name of a user account, you should probably rename the home directory of that user and other user-related items such as mail spool files. Also remember that when you change the UID of a user account, you should probably fix the ownership of files and directories outside the user’s home directory (the user ID is changed automatically for the user’s mailbox and for all files owned by the user and located in the user’s home directory).
```

### Deleting User Accounts
If you want to delete a user account, you can use the `userdel` command. In particular, this command updates the information stored in the account databases, deleting all entries referring to the specified user. The `-r` option also removes the user’s home directory and all its contents, along with the user’s mail spool. Other files, located elsewhere, must be searched for and deleted manually.
```
# userdel -r michael
```
As for `useradd` and `usermod`, you need root authority to delete user accounts.

### Adding, Modifying and Deleting Groups
Just as with user management, you can add, modify and delete groups using the `groupadd`, `groupmod` and `groupdel` commands with root privileges. If you want to create a new group named `developer`, you can run the following command:
```
# groupadd -g 1090 developer
```
The `-g` option of this command creates a group with a specific GID.
```
Warning
Remember that when you add a new user account, the primary group and the secondary groups to which it belongs must exist before launching the useradd command.
```
Later, if you want to rename the group from `developer` to `web-developer` and change its GID, you can run the following:
```
# groupmod -n web-developer -g 1050 developer
```
```
Tip
Remember that if you change the GID using the -g option, you should change the GID of all files and directories that must continue to belong to the group.
```
Finally, if you want to delete the `web-developer` group, you can run the following:
```
# groupdel web-developer
```
You cannot delete a group if it is the primary group of a user account. Therefore, you must remove the user before removing the group. As for users, if you delete a group, the files belonging to that group remain in your filesystem and are not deleted or assigned to another group.

### The Skeleton Directory
When you add a new user account, even creating its home directory, the newly created home directory is populated with files and folders that are copied from the skeleton directory (by default `/etc/skel`). The idea behind this is simple: a system administrator wants to add new users having the same files and directories in their home folder. Therefore, if you want to customize the files and folders that are created automatically in the home directory of new user accounts, you must add these new files and folders to the skeleton directory.
```
Tip
Note that if you want to list all the files and directories in the skeleton directory, you must use the ls -al command.
```
### The `/etc/login.defs` File
In Linux, the `/etc/login.defs` file specifies the configuration parameters that control the creation of users and groups. In addition, the commands shown in the previous sections take default values from this file.

The most important directives are:

`UID_MIN` and `UID_MAX`
- The range of user IDs that can be assigned to new ordinary users.

`GID_MIN` and `GID_MAX`
- The range of group IDs that can be assigned to new ordinary groups.

`CREATE_HOME`
- Specify whether a home directory should be created by default for new users.

`USERGROUPS_ENAB`
- Specify whether the system should by default create a new group for each new user account with the same name as the user, and whether deleting the user account should also remove the user’s primary group if it no longer contains members.

`MAIL_DIR`
- The mail spool directory.

`PASS_MAX_DAYS`
- The maximum number of days a password may be used.

`PASS_MIN_DAYS`
- The minimum number of days allowed between password changes.

`PASS_MIN_LEN`
- The minimum acceptable password length.

`PASS_WARN_AGE`
- The number of warning days before a password expires.
```
Tip
When managing users and groups, always check this file to view and eventually change the default behavior of the system if needed.
```
### The `passwd` Command
This command is primarily used to change a user’s password. As described before, any user can change their own password, but only root can change any user’s password. This happens because the `passwd` command has the SUID bit set (an `s` in the place of the executable flag for the owner), which means that it executes with the privileges of the file’s owner (thus root).
```
# ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 42096 mag 17  2015 /usr/bin/passwd
```
Depending on `the` passwd options used, you can control specific aspects of password aging:

`-d`
- Delete the password of a user account (thus disabling the user).

`-e`
- Force the user account to change the password.

`-i`
- Set the number of days of inactivity after a password expires during which the user should update the password (otherwise the account will be disabled).

`-l`
- Lock the user account (the encrypted password is prefixed with an exclamation mark in the `/etc/shadow` file).

`-n`
- Set the minimum password lifetime.

`-S`
- Output information about the password status of a specific user account.

`-u`
- Unlock the user account (the exclamation mark is removed from the password field in the `/etc/shadow` file).

`-x`
- Set the maximum password lifetime.

`-w`
- Set the number of days of warning before the password expires during which the user is warned that the password must be changed.
```
Note
Groups can also have a password, which can be set using the gpasswd command. Users, who are not members of a group but know its password, can join it temporarily using the newgrp command. Remember that gpasswd is also used to add and remove users from a group and to set the list of administrators and ordinary members of the group.
```
### The `chage` Command
This command, which stands for “change age”, is used to change the password aging information of a user. The `chage` command is restricted to root, except for the `-l` option, which can be used by ordinary users to list password aging information of their own account.

The other options which apply to the `chage` command are:

`-d`
- Set the last password change for a user account.

`-E`
- Set the expiration date for a user account.

`-I`
- Set the number of days of inactivity after a password expires during which the user should update the password (otherwise the account will be disabled).

`-m`
- Set the minimum password lifetime for a user account.

`-M`
- Set the maximum password lifetime for a user account.

`-W`
- Set the number of days of warning before the password expires during which the user is warned that the password must be changed.
___

The command line tools discussed in the previous lesson and the graphical applications provided by each distribution that perform the same tasks update a series of files that store information about users and groups.

These files are located under the `/etc/` directory and are:

`/etc/passwd`
- A file of seven colon-delimited fields containing basic information about users.

`/etc/group`
- A file of four colon-delimited fields containing basic information about groups.

`/etc/shadow`
- A file of nine colon-delimited fields containing encrypted user passwords.

`/etc/gshadow`
- A file of four colon-delimited fields file containing encrypted group passwords.

Although these four files are in plain text, they should not be edited directly, but always through the tools provided by the distribution you are using.

### /etc/passwd
This is a world-readable file that contains a list of users, each on a separate line. Each line consists of seven colon-delimited fields:

Username
- The name used when the user logs into the system.

Password
- The encrypted password (or an `x` if shadow passwords are used).

User ID (UID)
- The ID number assigned to the user in the system.

Group ID (GID)
- The primary group number of the user in the system.

GECOS
- An optional comment field, which is used to add extra information about the user (such as the full name). The field can contain multiple comma-separated entries.

Home Directory
- The absolute path of the user’s home directory.

Shell
- The absolute path of the program that is automatically launched when the user logs into the system (usually an interactive shell such as `/bin/bash`).

### /etc/group
This is a world-readable file that contains a list of groups, each on a separate line. Each line consists of four colon-delimited fields:

Group Name
- The name of the group.

Group Password
- The encrypted password of the group (or an x if shadow passwords are used).

Group ID (GID)
- The ID number assigned to the group in the system.

Member List
- A comma-delimited list of users belonging to the group, except those for whom this is the primary group.

### /etc/shadow
This is a file readable only by root and by users with root privileges that contains encrypted user passwords, each on a separate line. Each line consists of nine colon-delimited fields:

Username
- The name used when the user logs into the system.

Encrypted Password
- The encrypted password of the user (if the value starts with `!`, the account is locked).

Date of Last Password Change
- The date of the last password change, as number of days since 01/01/1970 (a value of 0 means that the user must change the password when they next login).

Minimum Password Age
- The minimum number of days, after a password change, which must pass before the user will be allowed to change the password again.

Maximum Password Age
- The maximum number of days that must pass before a password change is required.

Password Warning Period
- The number of days, before the password expires, during which the user is warned that the password must be changed.

Password Inactivity Period
- The number of days after a password expires during which the user should update the password. After this period, if the user does not change the password, the account will be disabled.

Account Expiration Date
- The date, expressed as the number of days since 01/01/1970, in which the user account will be disabled (an empty field means that the user account will never expire).

A reserved field
- A field that is reserved for future use.

### /etc/gshadow
This is a file readable only by root and by users with root privileges that contains encrypted group passwords, each on a separate line. Each line consists of four colon-delimited fields:

Group Name
- The name of the group.

Encrypted Password
- The encrypted password for the group (it is used when a user, who is not a member of the group, wants to join the group using the `newgrp` command — if the password starts with !, no one is allowed to access the group with `newgrp`).

Group Administrators
- A comma-delimited list of the administrators of the group (they can change the password of the group and can add or remove group members with the `gpasswd` command).

Group Members
- A comma-delimited list of the members of the group.

### Filter the Password and Group Databases
Very often it may be necessary to review information on users and groups stored in these four files and search for specific records. To perform this task, you can use the `grep` command or alternatively concatenate `cat` and `grep`.
```
# grep emma /etc/passwd
emma:x:1020:1020:User Emma:/home/emma:/bin/bash
# cat /etc/group | grep db-admin
db-admin:x:1050:grace,frank
```
Another way to access these databases is to use the `getent` command. In general, this command displays entries from databases supported by the Name Service Switch (NSS) libraries and requires the name of the database and a lookup key. If no key argument is provided, all entries in the specified database are displayed (unless the database does not support enumeration). Otherwise, if one or more key arguments are provided, the database is filtered accordingly.
```
# getent passwd emma
emma:x:1020:1020:User Emma:/home/emma:/bin/bash
# getent group db-admin
db-admin:x:1050:grace,frank
```
The `getent` command does not require root authority; you just need to be able to read the database from which you want to retrieve records.
```
Note
Remember that getent can only access databases configured in the /etc/nsswitch.conf file.
```
___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)