# LPIC-1.108: Essential System Services

## Lesson 108.4: Manage printers and printing

Declarations of a “paperless society” brought on by the advent of computers have, up to current times, proven to be false. Many organizations still rely on printed, or “hard copy”, pages of information. With this in mind we can see how important it is for a computer user to know how to print from a system as well as an administrator needing to know how to maintain a computer’s ability to work with printers.

On Linux, as well as many other operating systems, the Common Unix Printing System (CUPS) software stack allows for printing and printer management from a computer. Here is a very simplified outline of how a file is printed in Linux using CUPS:

1. A user submits a file to be printed.
2. The CUPS daemon, `cupsd`, then spools the print job. This print job is given a job number by CUPS, along with information about which print queue holds the job as well as the name of the document to print.
3. CUPS utilizes filters that are installed on the system to generate a formatted file that the printer can use.
4. CUPS then sends the re-formatted file to the printer for printing.

We will look at these steps in more detail, as well as how to install and manage a printer in Linux.

### The CUPS Service
Most Linux desktop installations will have the CUPS packages already installed. On minimal Linux installations the CUPS packages may not be installed, depending on the distribution. A basic CUPS installation can be performed on a Debian system with the following:
```
$ sudo apt install cups
```
On Fedora systems the installation process is just as easy. You will need to start the CUPS service manually after installation on Fedora and other Red Hat-based distributions:
```
$ sudo dnf install cups
...
$ sudo systemctl start cups.service
```
After the installation has completed, you can verify that the CUPS service is running with the use of the `systemctl` command:
```
$ systemctl status cups.service
● cups.service - CUPS Scheduler
   Loaded: loaded (/lib/systemd/system/cups.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-06-25 14:35:47 EDT; 41min ago
     Docs: man:cupsd(8)
 Main PID: 3136 (cupsd)
    Tasks: 2 (limit: 1119)
   Memory: 3.2M
   CGroup: /system.slice/cups.service
           ├─3136 /usr/sbin/cupsd -l
           └─3175 /usr/lib/cups/notifier/dbus dbus://
```
As with many other Linux daemons, CUPS relies on a set of configuration files for its operations. Listed below are the main ones that are of interest to the system administrator:

`/etc/cups/cupsd.conf`
- This file contains the configuration settings for the CUPS service itself. If you are at all familiar with the Apache web server configuration file then the CUPS configuration file will look quite similar to you as it uses a very similar syntax. The `cupsd.conf` file contains settings for things such as controlling access to the various print queues in use on the system, if whether or not the CUPS web interface is enabled, as well as the level of logging that the daemon will use.

`/etc/printcap`
- This is the legacy file that was used by the LPD (Line Printer Daemon) protocol before the advent of CUPS. CUPS will still create this file on systems for backwards compatibility and it is often-times a symbolic link to `/run/cups/printcap`. Each line in this file contains a printer that the system has access to.

`/etc/cups/printers.conf`
- This file contains each printer that is configured to be used by the CUPS system. Each printer and its associated print queue in this file is enclosed within a `<Printer></Printer>` stanza. This file provides the individual printer listings found within `/etc/printcap`.
```
Warning
No modifications to the /etc/cups/printers.conf file should be made at the command line while the CUPS service is running.
```
`/etc/cups/ppd/`
- This is not a configuration file but a directory that holds the PostScript Printer Description (PPD) files for the printers that use them. Each printer’s operating capabilities will be stored within a PPD file (ending in the `.ppd` extension). These are plain-text files and follow a specific format.

The CUPS service also utilizes logging in much the same manner as the Apache 2 service. The logs are stored within `/var/log/cups/` and contain an `access_log`, `page_log`, and an `error_log`. The `access_log` keeps a record of access to the CUPS web interface as well as actions taken within it, such as printer management. The `page_log` keeps track of print jobs that have been submitted to the print queues managed by the CUPS installation. The `error_log` will contain messages about print jobs that have failed and other errors recorded by the web interface.

We will next look at the tools and utilities that are used to manage the CUPS service.

### Using the Web Interface
As stated earlier, the `/etc/cups/cupsd.conf` configuration file determines if the web interface for the CUPS system is enabled. The configuration option looks like this:
```
# Web interface setting...
WebInterface Yes
```
If the web interface is enabled, then CUPS can be managed from a browser at the default URL of `http://localhost:631`. By default a user on the system can view printers and print queues but any form of configuration modification requires a user with root access to authenticate with the web service. The configuration stanza within the `/etc/cups/cupsd.conf` file for restricting access to administrative capabilities will resemble the following:
```
# All administration operations require an administrator to authenticate...
<Limit CUPS-Add-Modify-Printer CUPS-Delete-Printer CUPS-Add-Modify-Class CUPS-Delete-Class CUPS-Set-Default>
  AuthType Default
  Require user @SYSTEM
  Order deny,allow
</Limit>
```
Here is a breakdown of these options:

`AuthType Default`
- will use a basic authentication prompt when an action requires root access.

`Require user @SYSTEM`
- indicates that a user with administrative privileges will be required for the operation. This could be changed to `@groupname` where members of `groupname` can administer the CUPS service or individual users could be provided with a list as in `Require user carol, tim`.

`Order deny,allow`
- works much like the Apache 2 configuration option where the action is denied by default unless a user (or member of a group) is authenticated.

The web interface for CUPS can be disabled by first stopping the CUPS service, changing the WebInterface option from Yes to No, then restarting the CUPS service.

The CUPS web interface is built like a basic web site with navigation tabs for various sections of CUPS system. The tabs in the web interface include the following:

Home
- The home page will list the current version of CUPS that is installed. It also breaks down CUPS into sections such as:

   - CUPS for Users
     - Provides a description of CUPS, command-line options for working with printers and print queues, and a link to the CUPS user forum.

   - CUPS for Administrators
     - Provides links in the interface to install and manage printers and links to information about working with printers on a network.

   - CUPS for Developers
     - Provides links to developing for CUPS itself as well as creating PPD files for printers.

Administration
- The administration page is also broken down into sections:

   - Printers
     - Here an administrator can add new printers to the system, locate printers connected to the system and manage printers that are already installed.
   - Classes
     - Classes are a mechanism where printers can be added to groups with specific policies. For example, a class can contain a group of printers that belong to a specific floor of a building that only users within a particular department can print to. Another class can have limitations on how many pages a user can print. Classes are not created by default on a CUPS installation and have to be defined by an administrator. This is the section in the CUPS web interface where new classes can be created and managed.
   - Jobs
     - This is where an administrator can view all print jobs that are currently in queue for all printers that this CUPS installation manages.

   - Server
     - This is where an administrator can make changes to the `/etc/cups/cupsd.conf` file. Also, further configuration options are available via check boxes such as allowing printers connected to this CUPS installation to be shared on a network, advanced authentication, and allowing remote printer administration.

Classes
- If printer classes are configured on the system they will be listed on this page. Each printer class will have options to manage all of the printers in the class at once, as well as view all jobs that are in queue for the printers in this class.

Help
- This tab provides links for all of the available documentation for CUPS that is installed on the system.

Jobs
- The Jobs tab allows for the searching of individual print jobs as well as listing out all of the current print jobs managed by the server.

Printers
- The Printers tab lists all of the printers currently managed by the system as well as a quick overview of each printer’s status. Each printer listed can be clicked on and the administrator will be taken to the page where the individual printer can be further managed. The information for the printers on this tab comes from the /etc/cups/printers.conf file.

### Installing a Printer
Adding a printer queue to the system is a simple process within the CUPS web interface:

1. Click on the `Administration` tab then the `Add Printer` button.
2. The next page will provide various options depending on how your printer is connected to your system. If it is a local printer select the most relevant option such as which port the printer is connected to or which third-party printer software may be installed. CUPS will also try to detect printers that are connected to the network and display those here. You can also choose a direct connection option to a network printer depending on which network printing protocols the printer supports. Select your appropriate option and click on the `Continue` button.
3. The next page will allow you to provide a name, description, and location (such as “back office” or “front desk” etc.) for the printer. If you wish to share this printer over the network you can select the check box for that option on this page as well. Once your settings have been entered, click on the `Continue` button.
4. The next page is where the printer’s make and model can be selected. This allows CUPS to search its locally installed database for the most suitable drivers and PPD files to use with the printer. If you have a PPD file provided by the printer manufacturer, browse to its location and select it for use here. Once this is done, click on the `Add Printer` button.
5. The final page is where you would set your default options such as the page size that the printer will use and the resolution of the characters printed to the page. Click on the `Set Default Options` button and your printer is now installed on your system.
```
Note
Many desktop installations of Linux will have different tools that can be used to install a printer. The GNOME and KDE desktop environments have their own applications built-in that can be used to install and manage printers. Also, some distributions provide separate printer management applications. However when dealing with a server installation that many users will print to, the CUPS web interface may provide the best tools for the task.
```
A printer’s queue may also be installed using the legacy LPD/LPR commands. Here is an example using the `lpadmin` command:
```
$ sudo lpadmin -p ENVY-4510 -L "office" -v socket://192.168.150.25 -m everywhere
```
We will break down the command to illustrate the options used here:
- Since the addition of a printer to the system requires a user with administrative privileges, we prepend the `lpadmin` command with `sudo`.
- The `-p` option is the destination for your print jobs. It is essentially a friendly name for the user to know where the print jobs will land. Typically you can provide the name of the printer.
- The `-L` option is the location of the printer. This is optional but helpful should you need to manage a number of printers in various locations.
- The `-v` option is for the printer device’s URI. The device URI is what the CUPS print queue needs in order to send rendered print jobs to a specific printer. In our example, we are using a network location using the IP address provided.
- The final option, `-m`, is set to “everywhere”. This sets the model of the printer for CUPS to determine which PPD file to use. In modern versions of CUPS, it is best to use “everywhere” so that CUPS can check the device URI (set with the previous `-v` option) to automatically determine the correct PPD file to use for the printer. In modern situations, CUPS will just use IPP as explained below.

As stated previously, it is best to let CUPS automatically determine which PPD file to use for a particular print queue. However, the legacy `lpinfo` command can be used to query the locally installed PPD files to see what are available. Just provide the `--make-and-model` option for the printer you wish to install and the `-m` option:
```
$ lpinfo --make-and-model "HP Envy 4510" -m
hplip:0/ppd/hplip/HP/hp-envy_4510_series-hpijs.ppd HP Envy 4510 Series hpijs, 3.17.10
hplip:1/ppd/hplip/HP/hp-envy_4510_series-hpijs.ppd HP Envy 4510 Series hpijs, 3.17.10
hplip:2/ppd/hplip/HP/hp-envy_4510_series-hpijs.ppd HP Envy 4510 Series hpijs, 3.17.10
drv:///hpcups.crv/hp-envy_4510_series.ppd HP Envy 4510 Series, hpcups 3.17.10
everywhere IPP Everywhere
```
Note that the `lpinfo` command is deprecated. It is shown here as an example of listing out what print driver files a printer could use.
```
Warning
Future versions of CUPS have deprecated drivers and will instead focus on using IPP (Internet Printing Protocol) and standard file formats. The output of the previous command illustrates this with the everywhere IPP Everywhere printing capability. IPP can perform the same tasks that a print driver is used for. IPP, just like the CUPS web interface, utilizes network port 631 with the TCP protocol.
```
A default printer can be set using the `lpoptions` command. This way, should the majority (or all) print jobs be sent to a particular printer then the one specified with the `lpoptions` command will be the default. Just specify the printer along with the `-d` option:
```
$ lpoptions -d ENVY-4510
```
### Managing Printers
Once a printer has been installed an administrator could use the web interface to manage the options that are available to the printer. A more direct approach to managing a printer is through the use of the `lpadmin` command.

One option is allowing the ability for a printer to be shared on the network. This can be achieved with the `printer-is-shared` option, and by specifying the printer with the `-p` option:
```
$ sudo lpadmin -p FRONT-DESK -o printer-is-shared=true
```
An administrator can also configure a print queue to only accept print jobs from specific users with each user separated by a comma:
```
$ sudo lpadmin -p FRONT-DESK -u allow:carol,frank,grace
```
Inversely, only specific users could be denied access to a specific print queue:
```
$ sudo lpadmin -p FRONT-DESK -u deny:dave
```
User groups could also be used to allow or deny access to a printer’s queue provided that the group’s name is preceded by an “at” (`@`) character:
```
$ sudo lpadmin -p FRONT-DESK -u deny:@sales,@marketing
```
A print queue can also have an error policy should it encounter issues printing a job. With the use of policies, a print job could be aborted (`abort-job`) or another attempt to print it could occur at a later time (`retry-job`). Other policies include the ability to stop the printer immediately should an error occur (`stop-printer`) as well as the ability to retry the job immediately after a failure is detected (`retry-current-job`). Here is an example where the printer policy is set to abort the print job should an error occur on the `FRONT-DESK` printer:
```
$ sudo lpadmin -p FRONT-DESK -o printer-error-policy=abort-job
```
Be sure to review the manual pages for the `lpadmin` command located at `lpadmin(8)` for further details on the usage of this command.

### Submitting Print Jobs
Many desktop applications will allow you to submit print jobs from a menu item or by using the `Ctrl`+`p` keyboard shortcut. Should you find yourself on a Linux system that does not utilize a desktop environment, you can still send files to a printer by way of the legacy LPD/LPR commands.

The `lpr` (“line printer remote”) command is used to send a print job to a printer’s queue. In the command’s most basic form, a file name along with the `lpr` command is all that is required:
```
$ lpr report.txt
```
The above command will send the file `report.txt` to the default print queue for the system (as identified by the `/etc/cups/printers.conf` file).

If a CUPS installation has multiple printers installed then the `lpstat` command can be used to print out a list of available printers using the `-p` option and the `-d` option will indicate which is the default printer:
```
$ lpstat -p -d
printer FRONT-DESK is idle.  enabled since Mon 03 Aug 2020 10:33:07 AM EDT
printer PostScript_oc0303387803 disabled since Sat 07 Mar 2020 08:33:11 PM EST -
	reason unknown
printer ENVY-4510 is idle.  enabled since Fri 31 Jul 2020 10:08:31 AM EDT
system default destination: ENVY-4510
```
So in our example here, the `report.txt` file will be sent to the `ENVY-4510` printer as it is set as the default. Should the file need to be printed at a different printer, specify the printer along with the `-P` option:
```
$ lpr -P FRONT-DESK report.txt
```
When a print job is submitted to CUPS the daemon will figure out which backend is best suited to handle the task. CUPS can make use of various printer drivers, filters, hardware port monitors and other software to properly render the document. There will be times when a user printing a document will need to make modifications to how the document should be printed. Many graphical applications make this task rather easy. There are also command line options that can be utilized to change how a document should be printed. When a print job is submitted via the command line, the -o switch (for “options”) can be used in conjunction with specific terms to adjust the document’s layout for printing. Here is a short list of commonly used options:

`landscape`
- The document is printed where the page is rotated 90 degrees clockwise. The option `orientation-requested=4` will achieve the same result.

`two-sided-long-edge`
- The printer will print the document in portrait mode on both sides of the paper, provided that the printer supports this capability.

`two-sided-short-edge`
- The printer will print the document in landscape mode on both sides of the paper, provided that the printer supports this capability.

`media`
- The printer will print the job to the specified media size. The media sizes available for a print job to use depend on the printer, but here is a list of common sizes:

Size Option | Purpose
--- | ---
A4 | ISO A4
Letter | US Letter
Legal | US Legal
DL | ISO DL Envelope
COM10 | US #10 Envelope

`collate`
- Collate the printed document. This is helpful if you have a multi-page document that will be printed more than once as all of the pages for each document will then be printed in order. Set this option to either true to enable it or false` to disable it.

`page-ranges`
- This option can be used to select a single page to print, or a specific set of pages to print from a document. An example would look like: `-o page-ranges=5-7,9,15`. This would print pages 5, 6 and 7 then pages 9 and 15.

`fit-to-page`
- Print the document so that the file is scaled to fit the paper. If no information about the page size is provided by the file to be printed, it is possible that the printed job will be scaled incorrectly and portions of the document could be scaled off the page or the document could be scaled too small.

`outputorder`
- Print the document in either `reverse` order or `normal` to start the printing on page one. If a printer prints its pages face-down, the default is for the order to be `-o outputorder=normal` whereas printers that print with their pages facing up will print with `-o outputorder=reverse`.

Taking a sampling of the options above, the following example command can be constructed:
```
$ lpr -P ACCOUNTING-LASERJET -o landscape -o media=A4 -o two-sided-short-edge finance-report.pdf
```
More than one copy of a document can be printed by using the number option in the following format: `-#N` where N is equal to the number of copies to print. Here is an example with the collate option where seven copies of a report are to be printed on the default printer:
```
$ lpr -#7 -o collate=true status-report.pdf
```
Aside from the `lpr` command, the `lp` command can also be used. Many of the options that are used with the lpr command can also be used with the `lp` command, but there are some differences. Be sure to consult the man page at `lp(1)` for reference. Here is how we can run the previous example `lpr` command using the syntax of the `lp` command while also specifying the destination printer with the `-d` option:
```
$ lp -d ACCOUNTING-LASERJET -n 7 -o collate=true status-report.pdf
```
### Managing Print Jobs
As stated earlier, each print job submitted to the print queue receives a job ID from CUPS. A user can view the print jobs that they have submitted with the `lpq` command. Passing in the `-a` option will show the queues of all printers that are managed by the CUPS installation:
```
$ lpq -a
Rank        Owner         Job       File(s)                  Total Size
1st         carol         20        finance-report.pdf       5072 bytes
```
The same `lpstat` command used previously also has an option to view printer queues. The `-o` option by itself will show all print queues, or a print queue can be specified by name:
```
$ lp -o
ACCOUNTING-LASERJET-4                   carol        19456   Wed 05 Aug 2020 04:29:44 PM EDT
```
The print job ID will be prepended with the name of the queue where the job was sent, then the name of the user that submitted the job, the file’s size, and the time it was submitted.

Should a print job get stuck on a printer or a user wishes to cancel their print job, use the `lprm` command along with the job ID found from the `lpq` command:
```
$ lprm 20
```
All jobs in a print queue could be deleted at once by providing just a dash `-`:
```
$ lprm -
```
Alternatively, the CUPS `cancel` command could also be used by a user to stop their current print job:
```
$ cancel
```
A specific print job can be cancelled by its job ID prepended by the printer name:
```
$ cancel ACCOUNTING-LASERJET-20
```
A print job can also be moved from one print queue to another. This is often helpful should a printer stop responding or the document to be printed requires features available on a different printer. Take note that this procedure typically requires a user with elevated privileges. Using the same print job from the previous example, we could move it to the queue of the `FRONT-DESK` printer:
```
$ sudo lpmove ACCOUNTING-LASERJET-20 FRONT-DESK
```

### Removing Printers
To remove a printer, it is often helpful to first list out all of the printers that are currently managed by the CUPS service. This can be done with the `lpstat` command:
```
$ lpstat -v
device for FRONT-DESK: socket://192.168.150.24
device for ENVY-4510: socket://192.168.150.25
device for PostScript_oc0303387803: ///dev/null
```
The `-v` option not only lists out the printers but also where (and how) they are attached. It is good practice to first reject any new jobs going to the printer and provide a reason as to why the printer will not be accepting new jobs. This can be done with the following:
```
$ sudo cupsreject -r "Printer to be removed" FRONT-DESK
```
Note the use of `sudo` as this task requires a user with elevated privileges.

To remove a printer, we utilize the `lpadmin` command with the `-x` option to delete the printer:
```
$ sudo lpadmin -x FRONT-DESK
```

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)