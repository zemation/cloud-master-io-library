# LPIC-1.107: Administrative Tasks

## Lesson 107.2 Automate system administration tasks by scheduling jobs

One of the most important tasks of a good system administrator is to schedule jobs that must be executed on a regular basis. For example, an administrator can create and automate jobs for backups, system upgrades and to perform many other repetitive activities. To do this you can use the `cron` facility, which is useful to automate periodic job scheduling.

### Schedule Jobs with Cron
In Linux, `cron` is a daemon that runs continuously and wakes up every minute to check a set of tables to find tasks to execute. These tables are known as crontabs and contain the so-called cron jobs. Cron is suitable for servers and systems that are constantly powered on, because each cron job is executed only if the system is running at the scheduled time. It can be used by ordinary users, each of whom has their own `crontab`, as well as the root user who manages the system crontabs.
```
Note
In Linux there is also the anacron facility, which is suitable for systems that can be powered off (such as desktops or laptops). It can only be used by root. If the machine is off when the anacron jobs must be executed, they will run the next time the machine is powered on. anacron is out of scope for the LPIC-1 certification.
```
### User Crontabs
User crontabs are text files that manage the scheduling of user-defined cron jobs. They are always named after the user account that created them, but the location of these files depends on the distribution used (generally a subdirectory of `/var/spool/cron`).

Each line in a user crontab contains six fields separated by a space:

- The minute of the hour (0-59).
- The hour of the day (0-23).
- The day of the month (1-31).
- The month of the year (1-12).
- The day of the week (0-7 with Sunday=0 or Sunday=7).
- The command to run.

For the month of the year and the day of the week you can use the first three letters of the name instead of the corresponding number.

The first five fields indicate when to execute the command that is specified in the sixth field, and they can contain one or more values. In particular, you can specify multiple values using:

`*` (asterisk)
- Refers to any value.

`,` (comma)
- Specifies a list of possible values.

`-` (dash)
- Specifies a range of possible values.

`/` (slash)
- Specifies stepped values.

Many distributions include the `/etc/crontab` file which can be used as a reference for the layout of a `cron` file. Here is an example `/etc/crontab` file from a Debian installation:
```
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
```
### System Crontabs
System crontabs are text files that manage the scheduling of system cron jobs and can only be edited by the root user. `/etc/crontab` and all files in the `/etc/cron.d` directory are system crontabs.

Most distributions also include the `/etc/cron.hourly`, `/etc/cron.daily`, `/etc/cron.weekly` and `/etc/cron.monthly` directories that contain scripts to be run with the appropriate frequency. For example, if you want to run a script daily you can place it in `/etc/cron.daily`.
```
Warning
Some distributions use /etc/cron.d/hourly, /etc/cron.d/daily, /etc/cron.d/weekly and /etc/cron.d/monthly. Always remember to check for the correct directories in which to place scripts you would like cron to run.
```
The syntax of system crontabs is similar to that of user crontabs, however it also requires an additional mandatory field that specifies which user will run the cron job. Therefore, each line in a system crontab contains seven fields separated by a space:

- The minute of the hour (0-59).
- The hour of the day (0-23).
- The day of the month (1-31).
- The month of the year (1-12).
- The day of the week (0-7 with Sunday=0 or Sunday=7).
- The name of the user account to be used when executing the command.
- The command to run.

As for user crontabs, you can specify multiple values for the time fields using the `*`, `,` , `-` and `/` operators. You may also indicate the month of the year and the day of the week with the first three letters of the name instead of the corresponding number.

### Particular Time Specifications
When editing crontab files, you can also use special shortcuts in the first five columns instead of the time specifications:

`@reboot`
- Run the specified task once after reboot.

`@hourly`
- Run the specified task once an hour at the beginning of the hour.

`@daily` (or `@midnight`)
- Run the specified task once a day at midnight.

`@weekly`
- Run the specified task once a week at midnight on Sunday.

`@monthly`
- Run the specified task once a month at midnight on the first day of the month.

`@yearly` (or `@annually`)
- Run the specified task once a year at midnight on the 1st of January.

### Crontab Variables
Within a crontab file, there are sometimes variable assignments defined before the scheduled tasks are declared. The environment variables commonly set are:

`HOME`
- The directory where `cron` invokes the commands (by default the user’s home directory).

`MAILTO`
- The name of the user or the address to which the standard output and error is mailed (by default the crontab owner). Multiple comma-separated values are also allowed and an empty value indicates that no mail should be sent.

`PATH`
- The path where commands can be found.

`SHELL`
- The shell to use (by default `/bin/sh`).

### Creating User Cron Jobs
The `crontab` command is used to maintain crontab files for individual users. In particular, you can run the `crontab -e` command to edit your own crontab file or to create one if it doesn’t already exist.
```
$ crontab -e
no crontab for frank - using an empty one

Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        < ‑‑‑‑ easiest
  3. /usr/bin/emacs24
  4. /usr/bin/vim.tiny

Choose 1-4 [2]:
```
By default, the `crontab` command opens the editor specified by the `VISUAL` or `EDITOR` environment variables so you can start editing your crontab file with the preferred editor. Some distributions, as shown in the example above, allow you to choose the editor from a list when `crontab` is run for the first time.

If you want to run the `foo.sh` script located in your home directory every day at 10:00 am, you can add the following line to your crontab file:
```
0 10 * * * /home/frank/foo.sh
```
Consider the following sample crontab entries:
```
0,15,30,45 08 * * 2 /home/frank/bar.sh
30 20 1-15 1,6 1-5 /home/frank/foobar.sh
```
In the first line the bar.sh script is executed every Tuesday at 08:00 am, at 08:15 am, at 08:30 am and at 08:45 am. On the second line the foobar.sh script is executed at 08:30 pm from Monday to Friday for the first fifteen days of January and June.
```
Warning
Although crontab files can be edited manually, it is always recommended to use the crontab command. The permissions on the crontab files usually only allow them to be edited via the crontab command.
```
In addition to the `-e` option mentioned above, the `crontab` command has other useful options:

`-l`
- Display the current crontab on standard output.

`-r`
- Remove the current crontab.

`-u`
- Specify the name of the user whose crontab needs to be modified. This option requires root privileges and allows the root user to edit user crontab files.

### Creating System Cron Jobs
Unlike user crontabs, system crontabs are updated using an editor: therefore, you do not need to run the `crontab` command to edit `/etc/crontab` and the files in `/etc/cron.d`. Remember that when editing system crontabs, you must specify the account that will be used to run the cron job (usually the root user).

For example, if you want to run the `barfoo.sh` script located in the `/root` directory every day at 01:30 am, you can open `/etc/crontab` with your preferred editor and add the following line:
```
30 01 * * * root /root/barfoo.sh >>/root/output.log 2>>/root/error.log
```
In the above example, output of the job is appended to `/root/output.log`, while errors are appended to `/root/error.log`.
```
Warning
Unless output is redirected to a file as in the example above (or the MAILTO variable is set to a blank value), all output from a cron job will be sent to the user via e-mail. A common practice is to redirect standard output to /dev/null (or to a file for later review if necessary) and to not redirect standard error. This way the user will be notified immediately by e-mail of any errors.
```
### Configure Access to Job Scheduling
In Linux the `/etc/cron.allow` and `/etc/cron.deny` files are used to set crontab restrictions. In particular, they are used to allow or disallow the scheduling of cron jobs for different users. If `/etc/cron.allow` exists, only non-root users listed within it can schedule cron jobs using the `crontab` command. If `/etc/cron.allow` does not exist but `/etc/cron.deny` exists, only non-root users listed within this file cannot schedule cron jobs using the `crontab` command (in this case an empty `/etc/cron.deny` means that each user is allowed to schedule cron jobs with `crontab`). If neither of these files exist, the user’s access to cron job scheduling depends on the distribution used.
```
Note
The /etc/cron.allow and /etc/cron.deny files contain of a list of usernames, each on a separate line.
```
### An Alternative to Cron
Using systemd as the system and service manager, you can set timers as an alternative to `cron` to schedule your tasks. Timers are systemd unit files identified by the `.timer` suffix, and for each of these there must be a corresponding unit file which describes the unit to be activated when the timer elapses. By default, a `timer` activates a service with the same name, except for the suffix.

A timer includes a `[Timer]` section that specifies when scheduled jobs should run. Specifically, you can use the OnCalendar= option to define real-time timers which work in the same way as cron jobs (they are based on calendar event expressions). The `OnCalendar=` option requires the following syntax:
```
DayOfWeek Year-Month-Day Hour:Minute:Second
```
with `DayOfWeek` being optional. The `*`, `/` and `,` operators have the same meaning as those used for cron jobs, while you can use `..` between two values to indicate a contiguous range. For `DayOfWeek` specification, you can use the first three letters of the name or the full name.
```
Note
You can also define monotonic timers that activate after some time has elapsed from a specific start point (for example, when the machine was booted up or when the timer itself is activated).
```
For example, if you want to run the service named `/etc/systemd/system/foobar.service` at 05:30 on the first Monday of each month, you can add the following lines in the corresponding `/etc/systemd/system/foobar.timer` unit file.
```
[Unit]
Description=Run the foobar service

[Timer]
OnCalendar=Mon *-*-1..7 05:30:00
Persistent=true

[Install]
WantedBy=timers.target
```
Once you have created the new timer, you can enable it and start it by running the following commands as root:
```
# systemctl enable foobar.timer
# systemctl start foobar.timer
```
You can change the frequency of your scheduled job, modifying the `OnCalendar` value and then typing the `systemctl daemon-reload` command.

Finally, if you want to view the list of active timers sorted by the time they elapse next, you can use the `systemctl list-timers` command. You can add the --all option to see the inactive timer units as well.
```
Note
Remember that timers are logged to the systemd journal and you can review the logs of the different units using the journalctl command. Also remember that if you are acting as an ordinary user, you need to use the --user option of the systemctl and journalctl commands.
```
Instead of the longer normalized form mentioned above, you can use some special expressions which describe particular frequencies for job execution:

`hourly`
- Run the specified task once an hour at the beginning of the hour.

`daily`
- Run the specified task once a day at midnight.

`weekly`
- Run the specified task once a week at midnight on Monday.

`monthly`
- Run the specified task once a month at midnight on the first day of the month.

`yearly`
- Run the specified task once a year at midnight on the first day of January.

You can see the manual pages for the full list of time and date specifications at `systemd.timer(5)`.

___

As you learned in the previous lesson, you can schedule regular jobs using cron or systemd timers, but sometimes you may need to run a job at a specific time in the future only once. To do this, you can use another powerful utility: the `at` command.

### Schedule Jobs with `at`
The `at` command is used for one-time task scheduling and only requires that you specify when the job should be run in the future. After entering `at` on the command line followed by the time specification, you will enter the at prompt where you can define the commands to be executed. You can exit the prompt with the `Ctrl`+`D` key-sequence.
```
$ at now +5 minutes
warning: commands will be executed using /bin/sh
at> date
at> Ctrl+D
job 12 at Sat Sep 14 09:15:00 2019
```
The `at` job in the above example simply executes the `date` command after five minutes. Similar to `cron`, the standard output and error is sent to you via e-mail. Note that the atd daemon will need to be running on the system in order for you to use `at` job scheduling.
```
Note
In Linux, the batch command is similar to at, however batch jobs are executed only when the system load is low enough to allow it.
```
The most important options which apply to the `at` command are:

`-c`
- Print the commands of a specific job ID to the standard output.

`-d`
- Delete jobs based on their job ID. It is an alias for atrm.

`-f`
- Read the job from a file instead of the standard input.

`-l`
- List the pending jobs of the user. If the user is root, all jobs of all users are listed. It is an alias for `atq`.

`-m`
- Send mail to the user at the end of the job even if there was no output.

`-q`
Specify a queue in the form of a single letter from `a` to `z` and from `A` to `Z` (by default `a` for `at` and `b` for `batch`). Jobs in the queues with the highest letters are executed with increased niceness. Jobs submitted to a queue with a capital letter are treated as `batch` jobs.

`-v`
Show the time at which the job will run before reading the job.

### List Scheduled Jobs with `atq`
Now let us schedule two more `at` jobs: the first executes the `foo.sh` script at 09:30 am, while the second executes the `bar.sh` script after one hour.

```
$ at 09:30 AM
warning: commands will be executed using /bin/sh
at> ./foo.sh
at> Ctrl+D
job 13 at Sat Sep 14 09:30:00 2019
$ at now +2 hours
warning: commands will be executed using /bin/sh
at> ./bar.sh
at> Ctrl+D
job 14 at Sat Sep 14 11:10:00 2019
```

To list your pending jobs, you can use the `atq` command which shows the following information for each job: job ID, job execution date, job execution time, queue, and username.
```
$ atq
14      Sat Sep  14 11:10:00 2019 a frank
13      Sat Sep  14 09:30:00 2019 a frank
12      Sat Sep  14 09:15:00 2019 a frank
```
Remember that the `at -l` command is an alias for `atq`.
```
Note
If you run atq as root, it will display the queued jobs for all users.
```
### Delete Jobs with `atrm`
If you want to delete an `at` job, you can use the `atrm` command followed by the job ID. For example, to delete the job with ID 14, you can run the following:
```
$ atrm 14
```
You can delete multiple jobs with `atrm` by specifying multiple IDs separated by spaces. Remember that the `at -d` command is an alias for `atrm`.
```
Note
If you run atrm as root you can delete the jobs of all users.

```
### Configure Access to Job Scheduling
Authorization for ordinary users to schedule `at` jobs is determined by the `/etc/at.allow` and `/etc/at.deny` files. If `/etc/at.allow` exists, only non-root users listed within it can schedule `at` jobs. If `/etc/at.allow` does not exist but `/etc/at.deny` exists, only non-root users listed within it cannot schedule at jobs (in this case an empty `/etc/at.deny` file means that each user is allowed to schedule `at` jobs). If neither of these files exist, the user’s access to `at` job scheduling depends on the distribution used.

### Time Specifications
You can specify when to execute a particular `at` job using the form `HH:MM`, optionally followed by AM or PM in case of 12-hour format. If the specified time has already passed, the next day is assumed. If you want to schedule a particular date on which the job will run, you must add the date information after the time using one of the following forms: `month-name day-of-month`, `month-name day-of-month year`, `MMDDYY`, `MM/DD/YY`, `DD.MM.YY` and `YYYY-MM-DD`).

The following keywords are also accepted: `midnight`, `noon`, `teatime` (4 pm) and `now` followed by a plus sign (`+`) and a time period (minutes, hours, days and weeks). Finally, you can tell at to run the job today or tomorrow by suffixing the time with the words `today` or `tomorrow`. For example, you can use at `07:15 AM Jan 01` to execute a job at 07:15 am on 01 January and `at now +5 minutes` to execute a job five minutes from now. You can read the `timespec` file under the `/usr/share` tree for more information about the exact definition of time specifications.

### An Alternative to `at`
Using systemd as the system and service manager, you can also schedule one-time tasks with the `systemd-run` command. It is typically used to create a transient timer unit so that a command will be executed at a specific time without the need to create a service file. For example, acting as root, you can run the `date` command at 11:30 AM on 2019/10/06 using the following:
```
# systemd-run --on-calendar='2019-10-06 11:30' date
```
If you want to run the `foo.sh` script, located in your current working directory, after two minutes you can use:
```
# systemd-run --on-active="2m" ./foo.sh
```
Consult the manual pages to learn all possible uses of `systemd-run` with `systemd-run(1)`.
```
Note
Remember that timers are logged to the systemd journal and you can review the logs of the different units using the journalctl command. Also remember that if you are acting as an ordinary user, you need to use the --user option of the systemd-run and journalctl commands.
```

___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)