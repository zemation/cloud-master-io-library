
# LPIC-1.110: Security

## Lesson 110: Securing Data with encryption

Securing data with encryption is of paramount importance in many aspects of today’s system administration — even more so when it comes to accessing systems remotely. As opposed to insecure solutions such as telnet, rlogin or FTP, the SSH (Secure Shell) protocol was designed with security in mind. Using public key cryptography, it authenticates both hosts and users and encrypts all subsequent information exchange. Furthermore, SSH can be used to establish port tunnels, which — amongst other things — allows for a non-encrypted protocol to transmit data over an encrypted SSH connection. The current, recommended version of the SSH protocol is 2.0. OpenSSH is a free and open source implementation of the SSH protocol.

This lesson will cover basic OpenSSH client configuration as well as the role of OpenSSH server host keys. The concept of SSH port tunnels will also be discussed. We will be using two machines with the following setup:

Machine Role | OS | IP Address | Hostname | User
--- | --- | --- | --- | ---
Client | Debian GNU/Linux 10 (buster) | 192.168.1.55 | debian | carol
Server | openSUSE Leap 15.1 | 192.168.1.77 | halof | ina

### Basic OpenSSH Client Configuration and Usage
Although the OpenSSH server and client come in separate packages, you can normally install a metapackage that will provide both at once. To establish a remote session with the SSH server you use the ssh command, specifying the user you want to connect as on the remote machine and the remote machine’s IP address or hostname. The first time you connect to a remote host you will receive a message like this:
```
carol@debian:~$ ssh ina@192.168.1.77
The authenticity of host '192.168.1.77 (192.168.1.77)' can't be established.
ECDSA key fingerprint is SHA256:5JF7anupYipByCQm2BPvDHRVFJJixeslmppi2NwATYI.
Are you sure you want to continue connecting (yes/no)?
```
After typing `yes` and hitting Enter, you will be asked for the remote user’s password. If successfully entered, you will be shown a warning message and then logged in to the remote host:
```
Warning: Permanently added '192.168.1.77' (ECDSA) to the list of known hosts.
Password:
Last login: Sat Jun 20 10:52:45 2020 from 192.168.1.4
Have a lot of fun...
ina@halof:~>
```
The messages are quite self-explanatory: as it was the first time that you established a connection to the `192.168.1.77` remote server, its authenticity could not be checked against any database. Thus, the remote server provided an `ECDSA key fingerprint` of its public key (using the `SHA256` hash function). Once you accepted the connection, the public key of the remote server was added to the known hosts database, thus enabling the authentication of the server for future connections. This list of known hosts' public keys is kept in the file `known_hosts` which lives in `~/.ssh`:
```
ina@halof:~> exit
logout
Connection to 192.168.1.77 closed.
carol@debian:~$ ls .ssh/
known_hosts
```
Both `.ssh` and `known_hosts` were created after the first remote connection was established. `~/.ssh` is the default directory for user-specific configuration and authentication information.
```
Note
You can also use ssh to just execute a single command on the remote host and then come back to your local terminal (e.g.: running ssh ina@halof ls).
```

If you are using the same user on both the local and remote hosts, there is no need to specify the username when establishing the SSH connection. For instance, if you are logged in as user `carol` on `debian` and wanted to connect to `halof` also as user `carol`, you would simply type `ssh 192.168.1.77` or `ssh halof` (if the name can be resolved):
```
carol@debian:~$ ssh halof
Password:
Last login: Wed Jul  1 23:45:02 2020 from 192.168.1.55
Have a lot of fun...
carol@halof:~>
```
Now suppose you establish a new remote connection with a host that happens to have the same IP address as `halof` (a common thing if you use DHCP in your LAN). You will be warned about the possibility of a man-in-the-middle attack:
```
carol@debian:~$ ssh john@192.168.1.77
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:KH4q3vP6C7e0SEjyG8Wlz9fVlf+jmWJ5139RBxBh3TY.
Please contact your system administrator.
Add correct host key in /home/carol/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/carol/.ssh/known_hosts:1
  remove with:
  ssh-keygen -f "/home/carol/.ssh/known_hosts" -R "192.168.1.77"
ECDSA host key for 192.168.1.77 has changed and you have requested strict checking.
Host key verification failed.
```
Since you are not dealing with a man-in-the-middle attack, you can safely add the public key fingerprint of the new host to `.ssh/known_hosts`. As the message indicates, you can first use the command `ssh-keygen -f "/home/carol/.ssh/known_hosts" -R "192.168.1.77`" to remove the offending key (alternatively, you can go for `ssh-keygen -R 192.168.1.77` to delete all keys belonging to `192.168.1.77` from` ~/.ssh/known_hosts`). Then, you will be able to establish a connection to the new host.

### Key-Based Logins
You can set up your SSH client to not provide any passwords at login but use public keys instead. This is the preferred method of connecting to a remote server via SSH, as it is far more secure. The first thing you have to do is create a key pair on the client machine. To do this, you will use `ssh-keygen` with the `-t` option specifying the type of encryption you want (Elliptic Curve Digital Signature Algorithm in our case). Then, you will be asked for the path to save the key pair (`~/.ssh/` is convenient as well as the default location) and a passphrase. While a passphrase is optional, it is highly recommended to always use one.
```
carol@debian:~/.ssh$ ssh-keygen -t ecdsa
Generating public/private ecdsa key pair.
Enter file in which to save the key (/home/carol/.ssh/id_ecdsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/carol/.ssh/id_ecdsa.
Your public key has been saved in /home/carol/.ssh/id_ecdsa.pub.
The key fingerprint is:
SHA256:tlamD0SaTquPZYdNepwj8XN4xvqmHCbe8g5FKKUfMo8 carol@debian
The key's randomart image is:
+---[ECDSA 256]---+
|      .          |
|     o .         |
|    = o o        |
|     B *         |
|    E B S o      |
|     o & O       |
|      @ ^ =      |
|     *.@ @.      |
|    o.o+B+o      |
+----[SHA256]-----+
```
```
Note
When creating the key pair, you can pass ssh-keygen the -b option to specify the key size in bits (e.g.: ssh-keygen -t ecdsa -b 521).
```
The previous command produced two more files in your `~/.ssh` directory:
```
carol@debian:~/.ssh$ ls
id_ecdsa  id_ecdsa.pub  known_hosts
```
`id_ecdsa`
- This is your private key.

`id_ecdsa.pub`
- This is your public key.
```
Note
In asymmetric cryptography (aka public-key cryptography), the public and private keys are mathematically related to one another in such a way that whatever is encrypted by one can only be decrypted by the other.
```
The next thing you need to do is add your public key to the `~/.ssh/authorized_keys` file of the user you want to log in as on the remote host (if the `~/.ssh` directory does not already exist, you will have to create it first). You can copy your public key into the remote server in a number of ways: using a USB flash drive, through the `scp` command — which will transfer the file over using SSH — or by catting out the content of your public key and piping it into `ssh` like so:
```
carol@debian:~/.ssh$ cat id_ecdsa.pub |ssh ina@192.168.1.77 'cat >> .ssh/authorized_keys'
Password:
```
Once your public key has been added to the `authorized_keys` file on the remote host, you can face two scenarios when trying to establish a new connection:
- If you did not provide a passphrase when creating the key pair, you will be logged in automatically. Although convenient, this method can be insecure depending on the situation:
```
carol@debian:~$ ssh ina@192.168.1.77
Last login: Thu Jun 25 20:31:03 2020 from 192.168.1.55
Have a lot of fun...
ina@halof:~>
```
- If you provided a passphrase when creating the key pair, you will have to enter it on every connection much in the same way as if it was a password. Apart from the public key, this method adds an extra layer of security in the form of a passphrase and can — therefore — be considered more secure. As far as convenience goes — however — it is is exactly the same as having to enter a password every time you establish a connection. If you don’t use a passphrase and someone manages to obtain your private SSH key file, they would have access to every server on which your public key is installed.
```
carol@debian:~/.ssh$ ssh ina@192.168.1.77
Enter passphrase for key '/home/carol/.ssh/id_ecdsa':
Last login: Thu Jun 25 20:39:30 2020 from 192.168.1.55
Have a lot of fun...
ina@halof:~>
```
There is a way which combines security and convenience, though: using the SSH authentication agent (`ssh-agent`). The authentication agent needs to spawn its own shell and will hold your private keys — for public key authentication — in memory for the remainder of the session. Let us see how it works in a little bit more detail:

- Use `ssh-agent` to start a new Bash shell:
```
carol@debian:~/.ssh$ ssh-agent /bin/bash
carol@debian:~/.ssh$
```
- Use the `ssh-add` command to add your private key to a secure area of memory. If you supplied a passphrase when generating the key pair — which is recommended for extra security — you will be asked for it:
```
carol@debian:~/.ssh$ ssh-add
Enter passphrase for /home/carol/.ssh/id_ecdsa:
Identity added: /home/carol/.ssh/id_ecdsa (carol@debian)
```
- Once your identity has been added, you can login to any remote server on which your public key is present without having to type your passphrase again. It is common practice on modern desktops to perform this command upon booting your computer, as it will remain in memory until the computer is shutdown (or the key is unloaded manually).

Let us round this section off by listing the four types of public-key algorithms that can be specified with `ssh-keygen`:

`RSA`
- Named after its creators Ron Rivest, Adi Shamir and Leonard Adleman, it was published in 1977. It is considered secure and still widely used today. Its minimum key size is 1024 bits (default is 2048).

`DSA`
- The Digital Signature Algorithm has proven to be insecure and it was deprecated as of OpenSSH 7.0. DSA keys must be exaclty 1024 bits in length.

`ecdsa`
- The Elliptic Curve Digital Signature Algorithm is an improvement on DSA and — therefore — considered more secure. It uses elliptic curve cryptography. ECDSA key length is determined by one of the three possible elliptic curve sizes in bits: 256, 384 or 521.

`ed25519`
- It is an implementation of `EdDSA` — Edwards-curve Digital Signature Algorithm — that uses the stronger 25519 curve. It is considered the most secure of all. All Ed25519 keys have a fixed length of 256 bits.
```
Note
If invoked with no -t specification, ssh-keygen will generate an RSA key pair by default.
```
### The Role of OpenSSH Server Host Keys
The global configuration directory for OpenSSH lives in the `/etc` directory:
```
halof:~ # tree /etc/ssh
/etc/ssh
├── moduli
├── ssh_config
├── ssh_host_dsa_key
├── ssh_host_dsa_key.pub
├── ssh_host_ecdsa_key
├── ssh_host_ecdsa_key.pub
├── ssh_host_ed25519_key
├── ssh_host_ed25519_key.pub
├── ssh_host_rsa_key
├── ssh_host_rsa_key.pub
└── sshd_config

0 directories, 11 files
```
Apart from `moduli` and the configuration files for the client (`ssh_config`) and the server (`sshd_config`), you will find four key pairs — a key pair for each supported algorithm — that are created when the OpenSSH server is installed. As already noted, the server uses these host keys to identify itself to clients as required. Their name pattern is as follows:

Private keys
- `ssh_host_` prefix + algorithm + `key` suffix (e.g.: `ssh_host_rsa_key`)

Public keys (or public key fingerprints)
- `ssh_host_` prefix + algorithm + `key.pub` suffix (e.g.: `ssh_host_rsa_key.pub`)
```
Note
A fingerprint is created by applying a cryptographic hash function to a public key. As fingerprints are shorter than the keys they refer to, they come in handy to simplify certain key management tasks.
```
The permissions on the files containing the private keys are `0600` or `-rw-------`: only readable and writable by the owner (`root`). On the other hand, all public key files are also readable by members in the owner group and everybody else (`0644` or `-rw-r—​r--`):
```
halof:~ # ls -l /etc/ssh/ssh_host_*
-rw------- 1 root root 1381 Dec 21 20:35 /etc/ssh/ssh_host_dsa_key
-rw-r--r-- 1 root root  605 Dec 21 20:35 /etc/ssh/ssh_host_dsa_key.pub
-rw------- 1 root root  505 Dec 21 20:35 /etc/ssh/ssh_host_ecdsa_key
-rw-r--r-- 1 root root  177 Dec 21 20:35 /etc/ssh/ssh_host_ecdsa_key.pub
-rw------- 1 root root  411 Dec 21 20:35 /etc/ssh/ssh_host_ed25519_key
-rw-r--r-- 1 root root   97 Dec 21 20:35 /etc/ssh/ssh_host_ed25519_key.pub
-rw------- 1 root root 1823 Dec 21 20:35 /etc/ssh/ssh_host_rsa_key
-rw-r--r-- 1 root root  397 Dec 21 20:35 /etc/ssh/ssh_host_rsa_key.pub
```
You can view the fingerprints of the keys by passing `ssh-keygen` the `-l` switch. You must also provide the `-f` to specify the key file path:
```
halof:~ # ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key
256 SHA256:8cnPrinC49ZHc+/9Ai5pV+1JfZ4WBRZhd3rDOsc2zlA root@halof (ED25519)
halof:~ # ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key.pub
256 SHA256:8cnPrinC49ZHc+/9Ai5pV+1JfZ4WBRZhd3rDOsc2zlA root@halof (ED25519)
```
To view the key fingerprint as well as its random art, just add the `-v` switch like so:
```
halof:~ # ssh-keygen -lv -f /etc/ssh/ssh_host_ed25519_key.pub
256 SHA256:8cnPrinC49ZHc+/9Ai5pV+1JfZ4WBRZhd3rDOsc2zlA root@halof (ED25519)
+--[ED25519 256]--+
|              +oo|
|             .+o.|
|        .    ..E.|
|         + .  +.o|
|        S +  + *o|
|          ooo Oo=|
|     . . . =o+.==|
|      = o =oo o=o|
|     o.o +o+..o.+|
+----[SHA256]-----+
```
### SSH Port Tunnels
OpenSSH features a very powerful forwarding facility whereby traffic on a source port is tunnelled — and encrypted — through an SSH process which then redirects it to a port on a destination host. This mechanism is known as port tunnelling or port forwarding and has important advantages like the following:

- It allows you to bypass firewalls to access ports on remote hosts.
- It allows access from the outside to a host on your private network.
- It provides encryption for all data exchange.

Roughly speaking, we can differentiate between local and remote port tunnelling.

### Local Port Tunnel
You define a port locally to forward traffic to the destination host through the SSH process which sits in between. The SSH process can run on the local host or on a remote server. For instance, if for some reason you wanted to tunnel a connection to `www.gnu.org` through SSH using port `8585` on your local machine, you would do something like this:
```
carol@debian:~$ ssh -L 8585:www.gnu.org:80 debian
carol@debian's password:
Linux debian 4.19.0-9-amd64 #1 SMP Debian 4.19.118-2 (2020-04-29) x86_64

The programs included with the Debian GNU/Linux system are free software;
(...)
Last login: Sun Jun 28 13:47:27 2020 from 127.0.0.1
```
The explanation is as follows: with the `-L` switch, we specify the local port `8585` to connect to http port `80` on `www.gnu.org` using the `SSH` process running on `debian` — our localhost. We could have written `ssh -L 8585:www.gnu.org:80 localhost` with the same effect. If you now use a web browser to go to `http://localhost:8585`, you will be forwarded to `www.gnu.org`. For demonstration purposes, we will use lynx (the classic, text-mode web browser):
```
carol@debian:~$ lynx http://localhost:8585
(...)
  * Back to Savannah Homepage
     * Not Logged in
     * Login
     * New User
     * This Page
     * Language
     * Clean Reload
     * Printer Version
     * Search
     * _
(...)
```
If you wanted to do the exact same thing but connecting through an SSH process running on `halof`, you would have proceeded like so:
```
carol@debian:~$ ssh -L 8585:www.gnu.org:80 -Nf ina@192.168.1.77
Enter passphrase for key '/home/carol/.ssh/id_ecdsa':
carol@debian:~$
carol@debian:~$ lynx http://localhost:8585
(...)
  * Back to Savannah Homepage
     * Not Logged in
     * Login
     * New User
     * This Page
     * Language
     * Clean Reload
     * Printer Version
     * Search
     * _
(...)
```
It is important that you note three details in the command:
- Thanks to the `-N` option we did not login to halof but did the port forwarding instead.
- The `-f` option told SSH to run in the background.
- We specified user `ina` to do the forwarding: `ina@192.168.1.77`

### Remote Port Tunnel
In remote port tunnelling (or reverse port forwarding) the traffic coming on a port on the remote server is forwarded to the SSH process running on your local host, and from there to the specified port on the destination server (which may also be your local machine). For example, say you wanted to let someone from outside your network access the Apache web server running on your local host through port `8585` of the SSH server running on `halof` (`192.168.1.77`). You would proceed with the following command:
```
carol@debian:~$ ssh -R 8585:localhost:80 -Nf ina@192.168.1.77
Enter passphrase for key '/home/carol/.ssh/id_ecdsa':
carol@debian:~$
```
Now anyone who establishes a connection to `halof` on port `8585` will see `Debian`'s Apache2 default homepage:
```
carol@debian:~$ lynx 192.168.1.77:8585
(...)
                                                                 Apache2 Debian Default Page: It works (p1 of 3)
   Debian Logo Apache2 Debian Default Page
   It works!

   This is the default welcome page used to test the correct operation of the Apache2 server after
   installation on Debian systems. If you can read this page, it means that the Apache HTTP server
   installed at this site is working properly. You should replace this file (located at
   /var/www/html/index.html) before continuing to operate your HTTP server.
(...)
```
```
Note
There is a third, more complex type of port forwarding which is outside the scope of this lesson: dynamic port forwarding. Instead of interacting with a single port, this type of forwarding uses various TCP communications across a range of ports.
```
### X11 Tunnels
Now that you understand port tunnels, let us round this lesson off by discussing X11 tunnelling (also known as X11forwarding). Through an X11 tunnel, the X Window System on the remote host is forwarded to your local machine. For that, you just need to pass `ssh` the `-X` option:
```
carol@debian:~$ ssh -X ina@halof
...
```
You can now launch a graphical application such as the `firefox` web browser with the following result: the app will be run on the remote server, but its display will be forwarded to your local host.

If you start a new SSH session with the -x option instead, X11forwarding will be disabled. Try to start `firefox` now and you will get an error such as the following:
```
carol@debian:~$ ssh -x ina@halof
carol@192.168.0.106's password:
(...)
ina@halof:~$ firefox

(firefox-esr:1779): Gtk-WARNING **: 18:45:45.603: Locale not supported by C library.
	Using the fallback 'C' locale.
Error: no DISPLAY environment variable specified
```
```
Note
The three configuration directives related to local port forwarding, remote port forwarding and X11 forwarding are AllowTcpForwarding,GatewayPorts and X11Forwarding, respectively. For more information, type man ssh_config and/or man sshd_config.
```
___

In the previous lesson we learned how to use OpenSSH to encrypt remote login sessions as well as any other subsequent exchange of information. There may be other scenarios where you may want to encrypt files or email so that they reach their recipient safely and free from prying eyes. You may also need to digitally sign those files or messages to prevent them from being tampered with.

A great tool for these types of uses is the GNU Privacy Guard (aka GnuPG or simply GPG), which is a free and open source implementation of the proprietary Pretty Good Privacy (PGP). GPG uses the OpenPGP standard as defined by the OpenPGP Working Group of the Internet Engineering Task Force (IETF) in RFC 4880. In this lesson we will be reviewing the basics of the GNU Privacy Guard.

### Perform Basic GnuPG Configuration, Usage and Revocation
Just as with SSH, the underlying mechanism to GPG is that of asymmetric cryptography or public-key cryptography. A user generates a key pair which is made up of a private key and a public key. The keys are mathematically related in such a way that what is encrypted by one can only be decrypted by the other. For communication to take place successfully, the user has to send their public key to the intended recipient.

### GnuPG Configuration and Usage
The command to work with GPG is `gpg`. You can pass it a number of options to carry out different tasks. Let us start by generating a key pair as user `carol`. For that, you will use the `gpg --gen-key` command:
```
carol@debian:~$ gpg --gen-key
gpg (GnuPG) 2.2.12; Copyright (C) 2018 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: directory '/home/carol/.gnupg' created
gpg: keybox '/home/carol/.gnupg/pubring.kbx' created
Note: Use "gpg --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name:

(...)
```
After informing you — amongst other things — that the configuration directory `~/.gnupg` and your public keyring `~/.gnugpg/pubring.kbx` have been created, `gpg` goes on to ask you to provide your real name and email address:
```
(...)
Real name: carol
Email address: carol@debian
You selected this USER-ID:
    "carol <carol@debian>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit?
```
If you are OK with the resulting `USER-ID` and press `O`, you will be then asked for a passphrase (it is recommended that it has enough complexity):
```
┌──────────────────────────────────────────────────────┐
│ Please enter the passphrase to                       │
│ protect your new key                                 │
│                                                      │
│ Passphrase:  │

(...)
```
Some final messages will be displayed telling you about the creation of other files as well as the keys themselves and then you are done with the key generation process:
```
(...)
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: /home/carol/.gnupg/trustdb.gpg: trustdb created
gpg: key 19BBEFD16813034E marked as ultimately trusted
gpg: directory '/home/carol/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/carol/.gnupg/openpgp-revocs.d/D18FA0021F644CDAF57FD0F919BBEFD16813034E.rev'
public and secret key created and signed.

pub   rsa3072 2020-07-03 [SC] [expires: 2022-07-03]
      D18FA0021F644CDAF57FD0F919BBEFD16813034E
uid                      carol <carol@debian>
sub   rsa3072 2020-07-03 [E] [expires: 2022-07-03]
```
You can now see what is inside the `~/.gnupg` directory (GPG’s configuration directory):
```
carol@debian:~/.gnupg$ ls -l
total 16
drwx------ 2 carol carol 4096 Jul  3 23:34 openpgp-revocs.d
drwx------ 2 carol carol 4096 Jul  3 23:34 private-keys-v1.d
-rw-r--r-- 1 carol carol 1962 Jul  3 23:34 pubring.kbx
-rw------- 1 carol carol 1240 Jul  3 23:34 trustdb.gpg
```
Let us explain the use of each file:

`opengp-revocs.d`
- The revocation certificate that was created along with the key pair is kept here. The permissions on this directory are quite restrictive as anyone who has access to the certificate could revoke the key (more on key revocation in the next subsection).

`private-keys-v1.d`
- This is the directory that keeps your private keys, therefore permissions are restrictive.

`pubring.kbx`
- This is your public keyring. It stores your own as well as any other imported public keys.

`trustdb.gpg`
- The trust database. This has to do with the concept of Web of Trust (which is outside the scope of this lesson).
```
Note
The arrival of GnuPG 2.1 brought along some significant changes, such as the disappearance of the secring.gpg and pubring.gpg files in favour of private-keys-v1.d and pubring.kbx, respectively.
```
Once your key pair has been created, you can view your public keys with `gpg --list-keys` — which will display the contents of your public keyring:
```
carol@debian:~/.gnupg$ gpg --list-keys
/home/carol/.gnupg/pubring.kbx
------------------------------
pub   rsa3072 2020-07-03 [SC] [expires: 2022-07-03]
      D18FA0021F644CDAF57FD0F919BBEFD16813034E
uid           [ultimate] carol <carol@debian>
sub   rsa3072 2020-07-03 [E] [expires: 2022-07-03]
```
The hexadecimal string `D18FA0021F644CDAF57FD0F919BBEFD16813034E` is your public key fingerprint.
```
Note
Apart from the USER-ID (carol in the example), there is also the KEY-ID. The KEY-ID consists of the last 8 hexadecimal digits in your public key fingerprint (6813 034E). You can check your key fingerprint with the command gpg --fingerprint USER-ID.
```
### Key Distribution and Revocation
Now that you have your public key, you should save it (i.e. export it) to a file so that you can make it available to your future recipients. They will then be able to use it to encrypt files or messages intended for you (since you are the only one in posssession of the private key, you will also be the only one able to decrypt and read them). Likewise, your recipients will also use it to decrypt and verify your encrypted or signed messages/files. The command to use is `gpg --export` followed by the `USER-ID` and a redirection to the output file name of your choice:
```
carol@debian:~/.gnupg$ gpg --export carol > carol.pub.key
carol@debian:~/.gnupg$ ls
carol.pub.key  openpgp-revocs.d  private-keys-v1.d  pubring.kbx  trustdb.gpg
```
```
Note
Passing the -a or --armor option to gpg --export(e.g.: gpg --export --armor carol > carol.pub.key) will create ASCII armored output (instead of the default binary OpenPGP format) which can be safely emailed.
```
As already noted, you must now send your public key file (`carol.pub.key`) to the recipient with whom you want to exchange information. For instance, let us send the public key file to `ina` on remote server `halof` using `scp`(secure copy):
```
carol@debian:~/.gnupg$ scp carol.pub.key ina@halof:/home/ina/
Enter passphrase for key '/home/carol/.ssh/id_ecdsa':
carol.pub.key                                                                                         100% 1740   775.8KB/s   00:00
carol@debian:~/.gnupg$
```
`ina` is now in the possession of `carol.pub.key`. She will use it to encrypt a file and send it to `carol` in the next section.
```
Note
Another means of public key distribution is through the use of key servers: you upload your public key to the server with the command gpg --keyserver keyserver-name --send-keys KEY-ID and other users will get (i.e. import) them with gpg --keyserver keyserver-name --recv-keys KEY-ID.
```
Let us close this section by discussing key revocation. Key revocation should be used when your private keys have been compromised or retired. The first step is to create a revocation certificate by passing `gpg` the option `--gen-revoke` followed by the `USER-ID`. You can precede `--gen-revoke` with the `--output` option followed by a destination file name specification to save the resulting certificate into a file (instead of having it printed on the terminal screen). The output messages throughout the revocation process are quite self-explanatory:
```
sonya@debian:~/.gnupg$ gpg --output revocation_file.asc --gen-revoke sonya

sec  rsa3072/0989EB7E7F9F2066 2020-07-03 sonya <sonya@debian>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 1
Enter an optional description; end it with an empty line:
> My laptop was stolen.
>
Reason for revocation: Key has been compromised
My laptop was stolen.
Is this okay? (y/N) y
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
```
The revocation certificate has been saved to the file `revocation_file.asc` (`asc` for ASCII format):
```
sonya@debian:~/.gnupg$ ls
openpgp-revocs.d  private-keys-v1.d  pubring.kbx  revocation_file.asc  trustdb.gpg
sonya@debian:~/.gnupg$ cat revocation_file.asc
-----BEGIN PGP PUBLIC KEY BLOCK-----
Comment: This is a revocation certificate

iQHDBCABCgAtFiEEiIVjfDnnpieFi0wvnlcN6yLCeHEFAl8ASx4PHQJzdG9sZW4g
bGFwdG9wAAoJEJ5XDesiwnhxT9YMAKkjQiMpo9Uyiy9hyvukPPSrLcmtAGLk4pKS
pLZfzA5kxa+HPQwBglAEvfNRR6VMxqXUgUGYC/IAyQQM62oNAcY2PCPrxyJNgVF7
8l4mMZKvW++5ikjZwyg6WWV0+w6oroeo9qruJFjcu752p4T+9gsHVa2r+KRqcPQe
aZ65sAvsBJlcsUDZqfWUXg2kQp9mNPCdQuqvDaKRgNCHA1zbzNFzXWVd2X5RgFo5
nY+tUP8ZQA9DTQPBLPcggICmfLopMPZYB2bft5geb2mMi2oNpf9CNPdQkdccimNV
aRjqdUP9C89PwTafBQkQiONlsR/dWTFcqprG5KOWQPA7xjeMV8wretdEgsyTxqHp
v1iRzwjshiJCKBXXvz7wSmQrJ4OfiMDHeS4ipR0AYdO8QCzmOzmcFQKikGSHGMy1
z/YRlttd6NZIKjf1TD0nTrFnRvPdsZOlKYSArbfqNrHRBQkgirOD4JPI1tYKTffq
iOeZFx25K+fj2+0AJjvrbe4HDo5m+Q==
=umI8
-----END PGP PUBLIC KEY BLOCK-----
```
To effectively revoke your private key, you now need to merge the certificate with the key, which is done by importing the revocation certificate file to your keyring:
```
sonya@debian:~/.gnupg$ gpg --import revocation_file.asc
gpg: key 9E570DEB22C27871: "sonya <sonya@debian>" revocation certificate imported
gpg: Total number processed: 1
gpg:    new key revocations: 1
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: next trustdb check due at 2022-07-04
```
List your keys now and you will be informed about your revoked key:
```
sonya@debian:~/.gnupg$ gpg --list-keys
/home/sonya/.gnupg/pubring.kbx
pub   rsa3072 2020-07-04 [SC] [revoked: 2020-07-04]
      8885637C39E7A627858B4C2F9E570DEB22C27871
uid           [ revoked] sonya <sonya@debian>
```
Last — but not least — make sure you make the revoked key available to any party that has public keys associated with it (including keyservers).

### Use GPG to Encrypt, Decrypt, Sign and Verify Files
In the previous section, `carol` sent her public key to `ina`. We will use it now to discuss how GPG can encrypt, decrypt, sign and verify files.

### Encrypting and Decrypting Files
First, `ina` must import `carol`'s public key (`carol.pub.key`) into her keyring so that she can start working with it:
```
ina@halof:~> gpg --import carol.pub.key
gpg: /home/ina/.gnupg/trustdb.gpg: trustdb created
gpg: key 19BBEFD16813034E: public key "carol <carol@debian>" imported
gpg: Total number processed: 1
gpg:               imported: 1
ina@halof:~> gpg --list-keys
/home/ina/.gnupg/pubring.kbx
----------------------------
pub   rsa3072 2020-07-03 [SC] [expires: 2022-07-03]
      D18FA0021F644CDAF57FD0F919BBEFD16813034E
uid           [ unknown] carol <carol@debian>
sub   rsa3072 2020-07-03 [E] [expires: 2022-07-03]
```

Next you will create a file by writing some text into it and then encrypt it using `gpg` (because you did not sign `carol`'s key, you will be explicitly asked if you want to use that key):
```
ina@halof:~> echo "This is the message ..." > unencrypted-message
ina@halof:~> gpg --output encrypted-message --recipient carol --armor --encrypt unencrypted-message
gpg: 0227347CC92A5CB1: There is no assurance this key belongs to the named user
sub  rsa3072/0227347CC92A5CB1 2020-07-03 carol <carol@debian>
 Primary key fingerprint: D18F A002 1F64 4CDA F57F  D0F9 19BB EFD1 6813 034E
      Subkey fingerprint: 9D89 1BF9 39A4 C130 E44B  1135 0227 347C C92A 5CB1

It is NOT certain that the key belongs to the person named
in the user ID.  If you really know what you are doing,
you may answer the next question with yes.

Use this key anyway? (y/N) y
```
Let us break down the `gpg` command:

`--output encrypted-message`
- Filename specification for the encrypted version of the original file (`encrypted-message` in the example).

`--recipient carol`
- Recipient’s `USER-ID` specification (`carol` in our example). If not provided, GnuPG will ask for it (unless `--default-recipient` is specified).

`--armor`
- This option produces ASCII armored output, which can be copied into an email.

`--encrypt unencrypted-message`
- Filename specification of the original file to encrypt.

You can now send the `encrypted-message` to `carol` on `debian` using `scp`:
```
ina@halof:~> scp encrypted-message carol@debian:/home/carol/
carol@debian's password:
encrypted-message                                                             100%  736     1.8MB/s   00:00
```
If you log in as `carol` now and try to read the `encrypted-message`, you will confirm that it is actually encrypted and — therefore — unreadable:
```
carol@debian:~$ cat encrypted-message
-----BEGIN PGP MESSAGE-----

hQGMAwInNHzJKlyxAQv/brJ8Ubs/xya35sbv6kdRKm1C7ONLxL3OueWA4mCs0Y/P
GBna6ZEUCrMEgl/rCyByj3Yq74kuiTmzxAIRUDdvHfj0TtrOWjVAqIn/fPSfMkjk
dTxKo1i55tLJ+sj17dGMZDcNBinBTP4U1atuN71A5w7vH+XpcesRcFQLKiSOmYTt
F7SN3/5x5J6io4ISn+b0KbJgiJNNx+Ne/ub4Uzk4NlK7tmBklyC1VRualtxcG7R9
1klBPYSld6fTdDwT1Y4MofpyILAiGMZvUR1RXauEKf7OIzwC5gWU+UQPSgeCdKQu
X7QL0ZIBS0Ug2XKrO1k93lmDjf8PWsRIml6n/hNelaOBA3HMP0b6Ozv1gFeEsFvC
IxhUYPb+rfuNFTMEB7xIO94AAmWB9N4qknMxdDqNE8WhA728Plw6y8L2ngsplY15
MR4lIFDpljA/CcVh4BXVe9j0TdFWDUkrFMfaIfcPQwKLXEYJp19XYIaaEazkOs5D
W4pENN0YOcX0KWyAYX6r0l8BF0rq/HMenQwqAVXMG3s8ATuUOeqjBbR1x1qCvRQP
CR/3V73aQwc2j5ioQmhWYpqxiro0yKX2Ar/E6rZyJtJYrq+CUk8O3JoBaudknNFj
pwuRwF1amwnSZ/MZ/9kMKQ==
=g1jw
-----END PGP MESSAGE-----
```
However, as you are in possession of the private key, you can easily decrypt the message by passing `gpg` the `--decrypt` option followed by the path to the encrypted file (the private key’s passphrase will be required):
```
carol@debian:~$ gpg --decrypt encrypted-message
gpg: encrypted with 3072-bit RSA key, ID 0227347CC92A5CB1, created 2020-07-03
      "carol <carol@debian>"
This is the message ...
```
You can also specify the `--output` option to save the message into a new unencrypted file:
```
carol@debian:~$ gpg --output unencrypted-message --decrypt encrypted-message
gpg: encrypted with 3072-bit RSA key, ID 0227347CC92A5CB1, created 2020-07-03
      "carol <carol@debian>"
carol@debian:~$ cat unencrypted-message
This is the message ...
```
### Signing and Verifying Files
Apart from encrypting, GPG can also be used to sign files. The `--sign` option is relevant here. Let us start by creating a new message (`message`) and signing it with the `--sign` option (your private key’s passphrase will be required):
```
carol@debian:~$ echo "This is the message to sign ..." > message
carol@debian:~$ gpg --output message.sig --sign message
(...)
```
Breakdown of the `gpg` command:

`--output message`
- Filename specification of the signed version of the original file (`message.sig` in our example).

`--sign message`
- Path to original file.
```
Note
Using --sign the document is compressed and then signed. The output is in binary format.
```

Next we will transfer the file to `ina` on `halof` using `scp message.sig ina@halof:/home/ina` . Back as `ina` on `halof`, you can now verify it by using the `--verify` option:
```
ina@halof:~> gpg --verify message.sig
gpg: Signature made Sat 04 jul 2020 14:34:41 CEST
gpg:                using RSA key D18FA0021F644CDAF57FD0F919BBEFD16813034E
gpg: Good signature from "carol <carol@debian>" [unknown]
(...)
```
If you also want to read the file, you have to decrypt it to a new file (`message` in our case) using the `--output` option:
```
ina@halof:~> gpg --output message --decrypt message.sig
gpg: Signature made Sat 04 jul 2020 14:34:41 CEST
gpg:                using RSA key D18FA0021F644CDAF57FD0F919BBEFD16813034E
gpg: Good signature from "carol <carol@debian>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: D18F A002 1F64 4CDA F57F  D0F9 19BB EFD1 6813 034E
ina@halof:~> cat message
This is the message to sign ...
```
#### GPG-Agent
We will round this lesson off by briefly touching upon `gpg-agent`. `gpg-agent` is the daemon that manages private keys for GPG (it is started on demand by gpg). To view a summary of the most useful options, run `gpg-agent --help `or `gpg-agent -h`:
```
carol@debian:~$ gpg-agent --help
gpg-agent (GnuPG) 2.2.4
libgcrypt 1.8.1
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Syntax: gpg-agent [options] [command [args]]
Secret key management for GnuPG

Options:

     --daemon                        run in daemon mode (background)
     --server                        run in server mode (foreground)
     --supervised                    run in supervised mode
 -v, --verbose                       verbose
 -q, --quiet                         be somewhat more quiet
 -s, --sh                            sh-style command output
 -c, --csh                           csh-style command output
(...)
```
```
Note
For more information, consult the gpg-agent man page.
```

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)