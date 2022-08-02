# LPIC-1.108: Essential System Services

## Lesson 108.3: Mail Transfer Agent (MTA) basics

In Unix-like operating systems, such as Linux, every user has their own inbox: a special location on the filesystem that is inaccessible by other non-root users and stores the user’s personal email messages. New incoming messages are added to the user’s inbox by the Mail Transfer Agent (MTA). The MTA is a program running as a system service which collects messages sent by other local accounts as well as messages received from the network, sent from remote user accounts.

The same MTA is also responsible for sending messages to the network, if the destination address refers to a remote account. It does so by using a filesystem location as an email outbox for all system users: as soon as a user places a new message in the outbox, the MTA will identify the target network node from the domain name given by the destination email address — the portion after the @ sign — and then it will try to transfer the message to the remote MTA using the Simple Mail Transfer Protocol (SMTP). SMTP was designed with unreliable networks in mind, so it will try to establish alternative delivery routes if the primary mail destination node is unreachable.

### Local and Remote MTA
Traditional user accounts in network connected machines make up the simplest email exchange scenario, where every network node runs its own MTA daemon. No software other than the MTA is required to send and to receive email messages. In practice, however, it is more common to use a remote email account and not have an active local MTA service (i.e. instead using an email client application to access the remote account).

Unlike local accounts, a remote email account — also called remote mailbox — requires user authentication to grant access to the user’s mailbox and to the remote MTA (in this case, simply called the SMTP server). While the user interacting with a local inbox and MTA is already identified by the system, a remote system must verify the user’s identity before handling its messages through IMAP or POP3.
```
Note
Nowadays the most common method to send and receive email is through a hosted account on a remote server, e.g. a company’s centralized email server hosting all employee accounts or a personal email service, such as Google’s Gmail. Instead of collecting locally-delivered messages, the email client application will connect to the remote mailbox and retrieve the messages from there. The POP3 and IMAP protocols are commonly used to retrieve the messages from the remote server, but other non-standard proprietary protocols may be used as well.
```
When an MTA daemon is running on the local system, local users can send an email to other local users or to users on a remote machine, provided their system also has an MTA service that is accepting network connections. TCP port 25 is the standard port for SMTP communication, but other ports may be used as well, depending on the authentication and/or encryption schema being used (if any).

Leaving aside topologies involving the access to remote mailboxes, an email exchange network between ordinary Linux user accounts can be implemented as long as all network nodes have an active MTA that is able to perform the following tasks:

- Maintain the outbox queue of messages to be sent. For each queued message, the local MTA will assess the destination MTA from the recipient’s address.
- Communicate with remote MTA daemons using SMTP. The local MTA should be able to use the Simple Mail Transfer Protocol (SMTP) over the TCP/IP stack to receive, send and redirect messages from/to other remote MTA daemons.
- Maintain an individual inbox for every local account. The MTA will usually store the messages in the mbox format: a single text file containing all email messages in sequence.

Normally, email addresses specify a domain name as the location, e.g. `lpi.org` in `info@lpi.org`. When this is the case the sender’s MTA will query the DNS service for the corresponding MX record. The DNS MX record contains the IP address of the MTA handling the email for that domain. If the same domain has more than one MX record specified in the DNS, the MTA should try to contact them according to their priority values. If the recipient’s address does not specify a domain name or the domain does not have an MX record, then the part after the `@` symbol will be treated as the host of the destination MTA.

Security aspects must be considered if the MTA hosts will be visible to hosts on the Internet. For instance, it is possible for an unknown user to use the local MTA to impersonate another user and send potentially harmful emails. An MTA that blindly relays an email is known as an open relay, when it can be used as an intermediary to potentially disguise the true sender of the message. To prevent these misuses, the recommendation is to accept connections from authorized domains only and to implement a secure authentication schema.

In addition to that, there are many different MTA implementations for Linux, each one focusing on specific aspects like compatibility, performance, security, etc. Nevertheless, all MTAs will follow the same basic principles and provide similar features.

### Linux MTAs
The traditional MTA available for Linux systems is Sendmail, a very flexible general purpose MTA used by many Unix-like operating systems. Some of the other common MTAs are Postfix, qmail and Exim. The main reason to choose an alternative MTA is to implement advanced features more easily, as configuring custom email servers in Sendmail can be a complicated task. Also, each distribution may have its preferred MTA, with predefined settings appropriate for most common setups. All MTAs intend to be drop-in Sendmail replacements, so all Sendmail-compatible applications should work regardless of what MTA is being used.

If the MTA is running but not accepting network connections, it will only be able to deliver email messages on the local machine. For the `sendmail` MTA, the `/etc/mail/sendmail.mc` file should be modified to accept non-local connections. To do so, the entry
```
DAEMON_OPTIONS(`Port=smtp,Addr=127.0.0.1, Name=MTA')dnl
```
should be modified to the correct network address and the service should be restarted. Some Linux distributions, such as Debian, may offer configuration tools to help bring up the email server with a predefined set of commonly used features.
```
Tip
Due to security issues most Linux distributions do not install an MTA by default. To test the examples given in this lesson, make sure there’s a running MTA on every machine and that they are accepting connections on TCP port 25. For security purposes, these systems should not be exposed to incoming connections from the public internet during testing.
```
Once the MTA is running and accepting connections from the network, new email messages are passed to it with SMTP commands that are sent through a TCP connection. The command `nc` — a networking utility which reads and writes generic data across the network — can be used to send SMTP commands directly to the MTA. If the command nc is not available, it will be installed with the ncat or nmap-ncat package, depending on the package management system in use. Writing SMTP commands directly to the MTA will help you understand the protocol and other general email concepts better, but it can also help to diagnose problems in the mail delivery process.

If, for example, user `emma` at the `lab1.campus` host wants to send a message to user `dave` at the `lab2.campus` host, then she can use the `nc` command to directly connect to the lab2.campus MTA, assuming it is listening on the TCP port 25:
```
$ nc lab2.campus 25
220 lab2.campus ESMTP Sendmail 8.15.2/8.15.2; Sat, 16 Nov 2019 00:16:07 GMT
HELO lab1.campus
250 lab2.campus Hello lab1.campus [10.0.3.134], pleased to meet you
MAIL FROM: emma@lab1.campus
250 2.1.0 emma@lab1.campus... Sender ok
RCPT TO: dave@lab2.campus
250 2.1.5 dave@lab2.campus... Recipient ok
DATA
354 Enter mail, end with "." on a line by itself
Subject: Recipient MTA Test

Hi Dave, this is a test for your MTA.
.
250 2.0.0 xAG0G7Y0000595 Message accepted for delivery
QUIT
221 2.0.0 lab2.campus closing connection
```
Once the connection is established, the remote MTA identifies itself and then it’s ready to receive SMTP commands. The first SMTP command in the example, `HELO lab1.campus`, indicates `lab1.campus` as the exchange initiator. The next two commands, `MAIL FROM: emma@lab1.campus` and `RCPT TO: dave@lab2.campus`, indicate the sender and the recipient. The proper email message starts after the `DATA` command and ends with a period on a line by itself. To add a `subject` field to the email, it should be in the first line after the `DATA` command, as shown in the example. When the subject field is used, there must be an empty line separating it from the email content. The `QUIT` command finishes the connection with the MTA at the `lab2.campus` host.

On the `lab2.campus` host, user `dave` will receive a message similar to `You have new mail in /var/spool/mail/dave` as soon as he enters a shell session. This file will contain the raw email message sent by `emma` as well as the headers added by the MTA:
```
$ cat /var/spool/mail/dave
From emma@lab1.campus  Sat Nov 16 00:19:13 2019
Return-Path: <emma@lab1.campus>
Received: from lab1.campus (lab1.campus [10.0.3.134])
	by lab2.campus (8.15.2/8.15.2) with SMTP id xAG0G7Y0000595
	for dave@lab2.campus; Sat, 16 Nov 2019 00:17:06 GMT
Date: Sat, 16 Nov 2019 00:16:07 GMT
From: emma@lab1.campus
Message-Id: <201911160017.xAG0G7Y0000595@lab2.campus>
Subject: Recipient MTA Test

Hi Dave, this is a test for your MTA.
```
The header `Received`: shows that the message from `lab1.campus` was received directly by `lab2.campus`. By default, MTAs will only accept messages to local recipients. The following error will probably occur if user `emma` tries to send an email to user `henry` at the `lab3.campus` host, but using the `lab2.campus` MTA instead of the proper `lab3.campus` MTA:
```
$ nc lab2.campus 25
220 lab2.campus ESMTP Sendmail 8.15.2/8.15.2; Sat, 16 Nov 2019 00:31:44 GMT
HELO lab1.campus
250 lab2.campus Hello lab1.campus [10.0.3.134], pleased to meet you
MAIL FROM: emma@lab1.campus
250 2.1.0 emma@lab1.campus... Sender ok
RCPT TO: henry@lab3.campus
550 5.7.1 henry@lab3.campus... Relaying denied
```
SMTP response numbers beginning with 5, such as the `Relaying denied` message, indicate an error. There are legitimate situations when relaying is desirable, like when the hosts sending and receiving emails are not connected all the time: an intermediate MTA can be configured to accept emails intended to other hosts, acting as a relay SMTP server that can forward messages between MTAs.

The ability to route email traffic through intermediate SMTP servers discourages the attempt to connect directly to the host indicated by the recipient’s email address, as shown in the previous examples. Moreover, email addresses often have a domain name as the location (after the `@`), so the actual name of the corresponding MTA host must be retrieved through DNS. Therefore, it is recommended to delegate the task of identifying the appropriate destination host to the local MTA or to the remote SMTP server, when remote mailboxes are used.

Sendmail provides `sendmail` command to perform many email-related operations, including assisting in composing new messages. It also requires the user to type the email headers by hand, but in a friendlier way than using SMTP commands directly. So a more adequate method for user `emma@lab1.campus` to send an email message to `dave@lab2`.campus would be:
```
$ sendmail dave@lab2.campus
From: emma@lab1.campus
To: dave@lab2.campus
Subject: Sender MTA Test

Hi Dave, this is a test for my MTA.
.
```
Here again, the dot in a line by itself finishes the message. The message should be immediately sent to the recipient, unless the local MTA was unable to contact the remote MTA. The command `mailq`, if executed by root, will show all non-delivered messages. If, for example, the MTA at `lab2.campus` did not respond, then command `mailq` will list the undelivered message and the cause of the failure:
```
# mailq
		/var/spool/mqueue (1 request)
-----Q-ID----- --Size-- -----Q-Time----- ------------Sender/Recipient-----------
xAIK3D9S000453       36 Mon Nov 18 20:03 <emma@lab1.campus>
                 (Deferred: Connection refused by lab2.campus.)
					 <dave@lab2.campus>
		Total requests: 1
```
The default location for the outbox queue is `/var/spool/mqueue/`, but different MTAs may use different locations in the `/var/spool/` directory. Postfix, for instance, will create a directory tree under `/var/spool/postfix/` to manage the queue. The command `mailq` is equivalent to `sendmail -bp`, and they should be present regardless of the MTA installed in the system. To ensure backward compatibility, most MTAs provide these traditional mail administration commands.

If the primary email destination host — when it is provided from an MX DNS record for the domain — is unreachable, the MTA will try to contact the entries with lower priority (if there are any specified). If none of them are reachable, the message will stay in the local outbox queue to be sent later. If configured to do so, the MTA can periodically check the availability of the remote hosts and perform a new delivery attempt. If using a Sendmail-compatible MTA, a new attempt will immediately take place with the command `sendmail -q`.

Sendmail will store incoming messages in a file named after the corresponding inbox owner, for example `/var/spool/mail/dave`. Other MTAs, like Postfix, may store the incoming email messages in locations like `/var/mail/dave`, but the file content is the same. In the example, the command `sendmail` was used in the sender’s host to compose the message, so the raw message headers show that the email took extra steps before reaching the final destination:
```
$ cat /var/spool/mail/dave
From emma@lab1.campus  Mon Nov 18 20:07:39 2019
Return-Path: <emma@lab1.campus>
Received: from lab1.campus (lab1.campus [10.0.3.134])
	by lab2.campus (8.15.2/8.15.2) with ESMTPS id xAIK7clC000432
	(version=TLSv1.3 cipher=TLS_AES_256_GCM_SHA384 bits=256 verify=NOT)
	for <dave@lab2.campus>; Mon, 18 Nov 2019 20:07:38 GMT
Received: from lab1.campus (localhost [127.0.0.1])
	by lab1.campus (8.15.2/8.15.2) with ESMTPS id xAIK3D9S000453
	(version=TLSv1.3 cipher=TLS_AES_256_GCM_SHA384 bits=256 verify=NOT)
	for <dave@lab2.campus>; Mon, 18 Nov 2019 20:03:13 GMT
Received: (from emma@localhost)
	by lab1.campus (8.15.2/8.15.2/Submit) id xAIK0doL000449
	for dave@lab2.campus; Mon, 18 Nov 2019 20:00:39 GMT
Date: Mon, 18 Nov 2019 20:00:39 GMT
Message-Id: <201911182000.xAIK0doL000449@lab1.campus>
From: emma@lab1.campus
To: dave@lab2.campus
Subject: Sender MTA Test

Hi Dave, this is a test for my MTA.
```
From bottom to top, the lines starting with `Received`: show the route taken by the message. The message was submitted by user `emma` with the command `sendmail dave@lab2.campus` issued on `lab1.campus`, as stated by the first `Received`: header. Then, still on `lab1.campus`, the MTA uses ESMTPS — a superset of the SMTP, which adds encryption extensions — to send the message to the MTA at `lab2.campus`, as stated by the last (top) `Received`: header.

The MTA finishes its job after the message is saved in the user’s inbox. It is common to perform some kind of email filtering, such as spam blockers or the enforcement of user-defined filtering rules. These tasks are executed by third-party applications, working together with the MTA. The MTA could, for example, call the SpamAssassin utility to mark suspicious messages using its text analysis features.

Although possible, it is not convenient to read the mailbox file directly. It is recommended to use a email client program instead (e.g. Thunderbird, Evolution, or KMail), which will parse the file and appropriately manage the messages. Such programs also offer extra features, like shortcuts to common actions, inbox sub-directories, etc.

### The `mail` Command and Mail User Agents (MUA)
It is possible to write an email message directly in its raw format, but it is much more practical to use a client application — also known as an MUA (Mail User Agent) — to speed up the process and to avoid mistakes. The MUA takes care of the work under the hood, that is, the email client presents and organizes the received messages and handles the proper communication with the MTA after the user composes an email.

There are many distinct types of Mail User Agents. Desktop applications like Mozilla Thunderbird and Gnome’s Evolution support both local and remote email accounts. Even Webmail interfaces can be seen as a type of MUA, as they intermediate the interaction between the user and the underlying MTA. Email clients are not restricted to graphical interfaces though: console email clients are widely used to access mailboxes not integrated with a graphical interface and to automate email related tasks within shell scripts.

Originally, the Unix `mail` command was only intended to share messages between local system users (the first `mail` command dates back to the first Unix edition, released in 1971). When network email exchanges became more prominent, other programs were created to deal with the new delivery system and gradually replaced the old `mail` program.

Nowadays, the most commonly used `mail` command is provided by the mailx package, which is compatible with all modern email features. In most Linux distributions, the command `mail` is just a symbolic link to the `mailx` command. Other implementations, like the GNU Mailutils package, basically provide the same features as `mailx`. There are, however, slight differences between them, especially with respect to command-line options.

Regardless of their implementation, all modern variations of the `mail` command operate in two modes: normal mode and send mode. If an email address is provided as an argument to the command `mail`, it will enter the send mode, otherwise it will enter the normal (read) mode. In normal mode, the received messages are listed with a numerical index for each one, so the user can refer to them individually when typing commands in the interactive prompt. The command `print 1` can be used to display the contents of message number 1, for example. Interactive commands can be abbreviated, so commands like `print`, `delete` or `reply` can be replaced by `p`, `d` or `r`, respectively. The `mail` command will always consider the last received or the last viewed message when the message index number is omitted. The command `quit` or `q` will exit the program.

The send mode is especially useful for sending automated email messages. It can be used, for example, to send an email to the system administrator if a scheduled maintenance script fails to perform its task. In send mode, `mail` will use the contents from the standard input as the message body:
```
$ mail -s "Maintenance fail" henry@lab3.campus <<<"The maintenance script failed at `date`"
```
In this example, the option `-s` was added to include a subject field to the message. The message body was provided by the Hereline redirection to the standard input, but the contents of a file or the output of a command could also be piped to the program’s stdin. If no content is provided by a redirection to the standard input, then the program will wait for the user to enter the message body. In this case, keystroke `Ctrl`+`D` will end the message. The `mail` command will immediately exit after the message is added to the outbox queue.

### Delivery Customization
By default, the email accounts on a Linux system are associated with the standard system accounts. For example, If user Carol has the login name `carol` on the host `lab2.campus` then her email address will be `carol@lab2.campus`. This one-to-one association between system accounts and mailboxes can be extended by standard methods provided by most Linux distributions, in particular the email routing mechanism provided by the `/etc/aliases` file.

An email alias is a “virtual” email recipient whose receiving messages are redirected to existing local mailboxes or to other types of message storage or processing destinations. Aliases are useful, for example, to place messages sent to `postmaster@lab2.campus` in Carol’s mailbox, which is an ordinary local mailbox in the `lab2.campus` system. To do so, the line `postmaster: carol` should be added to the `/etc/aliases` file in `lab2.campus`. After modifying the `/etc/aliases` file, the command newaliases should be executed to update the MTA’s aliases database and make the changes effective. Commands `sendmail -bi` or `sendmail -I` can also be used to update the aliases database.

Aliases are defined one per line, in the format `<alias>: <destination>`. In addition to the ordinary local mailboxes, indicated by the corresponding username, other destination types are available:

- A full path (starting with `/`) to a file. Messages sent to the corresponding alias will be appended to the file.
- A command to process the message. The `<destination>` must start with a pipe character and, if the command contains special characters (like blank spaces), it must be enclosed in double quotes. For example, the alias `subscribe: |subscribe.sh` in `lab2.campus` will forward all messages sent to `subscribe@lab2.campus` to the standard input of the command `subscribe.sh`. If sendmail is running in restricted shell mode, the allowed commands — or the links to them — should be in `/etc/smrsh/`.
- An include file. A single alias can have multiple destinations (separated by commas), so it may be more practical to keep them in an external file. The `:include:` keyword must indicate the file path, as in `:include:/var/local/destinations`
- An external address. Aliases can also forward messages to external email addresses.
- Another alias.

An unprivileged local user can define aliases for their own email by editing the file `.forward` in their home directory. As the aliases can only affect their own mailbox, only the `<destination>` part is necessary. To forward all incoming emails to an external address, for example, user dave in `lab2.campus` could create the following `~/.forward` file:
```
$ cat ~/.forward
emma@lab1.campus
```
It will forward all email messages sent to `dave@lab2.campus` to `emma@lab1.campus`. As with the `/etc/aliases` file, other redirection rules can be added to `.forward`, one per line. Nevertheless, the `.forward` file must be writable by its owner only and it is not necessary to execute the `newaliases` command after modifying it. Files starting with a dot do not appear in regular file listings, which could make the user unaware of active aliases. Therefore, it is important to verify if the file exists when diagnosing email delivery issues.




___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)