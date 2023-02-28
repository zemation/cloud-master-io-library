# LPIC-1.107: Administrative Tasks

## Lesson 107.3 Localisation and internationalisation

All major Linux distributions can be configured to use custom localisation settings These settings include region and language related definitions such as the time zone, interface language as well as character encoding can be modified during the installation of the operating system or anytime after that.

Applications rely on environment variables, system configuration files and commands to decide the proper time and language to use; hence most Linux distribution share a standardized way to adjust time and localisation settings. These adjustments are important not only to improve user experience, but also to ensure that the timing of system events — important, for example, for reporting security related issues — are correctly calculated.

To be able to represent any written text, regardless of the spoken language, modern operating systems need a reference character encoding standard, and Linux systems are no different. As computers are only able to deal with numbers, a text character is nothing more than a number associated with a graphic symbol. Distinct computer platforms may associate distinct number values to the same character, so a common character encoding standard is necessary to make them compatible. A text document created in one system will be readable in another system only if both agree on the encoding format and on what number is associated to what character, or at least if they know how to convert between the two standards.

The heterogeneous nature of the localisation settings in Linux-based systems result in subtle differences between the distributions. Despite these differences, all distributions share the same basic tools and concepts to setup the internationalisation aspects of a system.

### Time Zones
Time zones are roughly proportional discrete bands of Earth’s surface spanning the equivalent to one hour, that is, regions of the world experiencing the same hour of the day at any given time. Since there is not a single longitude that can be considered as the beginning of the day for the whole world, time zones are relative to the prime meridian, where the Earth’s longitude angle is defined to be 0. The time at the prime meridian is called the Coordinated Universal Time, by convention abbreviated to UTC. Due to practical reasons, time zones do not follow the exact longitudinal distance from the reference point (the prime meridian). Instead, time zones are artificially adapted to follow the borders of countries or other significant subdivisions.

The political subdivisions are so relevant that time zones are named after some major geographical agent in that particular area, usually based on the name of a large country or city inside the zone. Yet, time zones are divided according to their time offset relative to UTC and this offset can also be used to indicate the zone in question. The time zone GMT-5, for example, indicates a region for which UTC time is five hours ahead, i.e. that region is 5 hours behind UTC. Likewise, the time zone GMT+3 indicates a region for which UTC time is three hours behind. The term GMT — from Greenwich Mean Time — is used as a synonym for UTC in the offset based zone names.

A connected machine can be accessed from different parts of the world, so it is a good practice to set the hardware clock to UTC (the GMT+0 time zone) and leave the choice of the time zone to each particular case. Cloud services, for example, are commonly configured to use UTC, as it can help to mitigate occasional inconsistencies between local time and time at clients or at other servers. In contrast, users who open a remote session on the server may want to use their local time zone. Thus, it will be up to the operating system to set up the correct time zone according to each case.

In addition to the current date and time, command `date` will also print the currently configured time zone:
```
$ date
Mon Oct 21 10:45:21 -03 2019
```
The offset relative to UTC is given by the `-03` value, meaning that the displayed time is three hours less than UTC. Therefore, UTC time is three hours ahead, making GMT-3 the corresponding time zone for the given time. Command `timedatectl`, which is available in distributions using systemd, shows more details about the system time and date:
```
$ timedatectl
                      Local time: Sat 2019-10-19 17:53:18 -03
                  Universal time: Sat 2019-10-19 20:53:18 UTC
                        RTC time: Sat 2019-10-19 20:53:18
                       Time zone: America/Sao_Paulo (-03, -0300)
       System clock synchronized: yes
systemd-timesyncd.service active: yes
                 RTC in local TZ: no
```
As shown in the `Time zone` entry, time zone names based on localities — like `America/Sao_Paulo` — are also accepted. The default time zone for the system is kept in the file `/etc/timezone`, either by the zone’s full descriptive name or offset. Generic time zone names given by the UTC offset must include `Etc` as the first part of the name. So, to set the default time zone to GMT+3, the name of the time zone must be `Etc/GMT+3`:
```
$ cat /etc/timezone
Etc/GMT+3
```
Although time zone names based on localities do not require the time offset to work, they are not so straight forward to choose from. The same zone can have more than one name, which can make it difficult to remember. In order to ease this issue, command `tzselect` offers an interactive method which will guide the user towards the correct time zone definition. Command `tzselect` should be available by default in all Linux distributions, as it is provided by the package that contains necessary utility programs related to the GNU C Library.

Command `tzselect` will be useful, for example, for a user who wants to identify the time zone for “São Paulo City” in “Brazil”. `tzselect` starts by asking the macro region of the desired location:
```
$ tzselect
Please identify a location so that time zone rules can be set correctly.
Please select a continent, ocean, "coord", or "TZ".
 1) Africa
 2) Americas
 3) Antarctica
 4) Asia
 5) Atlantic Ocean
 6) Australia
 7) Europe
 8) Indian Ocean
 9) Pacific Ocean
10) coord - I want to use geographical coordinates.
11) TZ - I want to specify the time zone using the Posix TZ format.
#? 2
```
Option `2` is for (North and South) American locations, not necessarily in the same time zone. It is also possible to specify the time zone with geographical coordinates or with the offset notation, also known as the Posix TZ format. The next step is to choose the country:
```
Please select a country whose clocks agree with yours.
 1) Anguilla              19) Dominican Republic    37) Peru
 2) Antigua & Barbuda     20) Ecuador               38) Puerto Rico
 3) Argentina             21) El Salvador           39) St Barthelemy
 4) Aruba                 22) French Guiana         40) St Kitts & Nevis
 5) Bahamas               23) Greenland             41) St Lucia
 6) Barbados              24) Grenada               42) St Maarten (Dutch)
 7) Belize                25) Guadeloupe            43) St Martin (French)
 8) Bolivia               26) Guatemala             44) St Pierre & Miquelon
 9) Brazil                27) Guyana                45) St Vincent
10) Canada                28) Haiti                 46) Suriname
11) Caribbean NL          29) Honduras              47) Trinidad & Tobago
12) Cayman Islands        30) Jamaica               48) Turks & Caicos Is
13) Chile                 31) Martinique            49) United States
14) Colombia              32) Mexico                50) Uruguay
15) Costa Rica            33) Montserrat            51) Venezuela
16) Cuba                  34) Nicaragua             52) Virgin Islands (UK)
17) Curaçao               35) Panama                53) Virgin Islands (US)
18) Dominica              36) Paraguay
#? 9
```
Brazil’s territory spans four time zones, so the country information alone is not enough to set the time zone. In the next step `tzselect` will require the user to specify the local region:
```
Please select one of the following time zone regions.
 1) Atlantic islands
 2) Pará (east); Amapá
 3) Brazil (northeast: MA, PI, CE, RN, PB)
 4) Pernambuco
 5) Tocantins
 6) Alagoas, Sergipe
 7) Bahia
 8) Brazil (southeast: GO, DF, MG, ES, RJ, SP, PR, SC, RS)
 9) Mato Grosso do Sul
10) Mato Grosso
11) Pará (west)
12) Rondônia
13) Roraima
14) Amazonas (east)
15) Amazonas (west)
16) Acre
#? 8
```
Not all locality names are available, but choosing the closest region will be good enough. The given information will then be used by `tzselect` to display the corresponding time zone:
```
The following information has been given:

        Brazil
        Brazil (southeast: GO, DF, MG, ES, RJ, SP, PR, SC, RS)

Therefore TZ='America/Sao_Paulo' will be used.
Selected time is now:   sex out 18 18:47:07 -03 2019.
Universal Time is now:  sex out 18 21:47:07 UTC 2019.
Is the above information OK?
1) Yes
2) No
#? 1

You can make this change permanent for yourself by appending the line
        TZ='America/Sao_Paulo'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /usr/bin/tzselect command in shell scripts:
America/Sao_Paulo
```
The resulting time zone name, `America/Sao_Paulo`, can also be used as the content of the `/etc/timezone` to inform the default time zone for the system:
```
$ cat /etc/timezone
America/Sao_Paulo
```
As stated by the output of `tzselect`, the environment variable `TZ` defines the time zone for the shell session, whatever the system’s default time zone is. Adding the line `TZ='America/Sao_Paulo'`; export `TZ` to the file `~/.profile` will make `America/Sao_Paulo` the time zone for the user’s future sessions. The `TZ` variable can also be temporarily modified during the current session, in order to display the time in a different time zone:
```
$ env TZ='Africa/Cairo' date
Mon Oct 21 15:45:21 EET 2019
```
In the example, command `env` will run the given command in a new sub-shell session with the same environment variables of the current session, except for the variable `TZ`, modified by the argument `TZ='Africa/Cairo'`.

### Daylight Savings Time
Many regions adopt daylight savings time for part of the year — when clocks are typically adjusted by an hour — that could lead a misconfigured system to report the wrong time during that season of the year.

The `/etc/localtime` file contains the data used by the operating system to adjust its clock accordingly. Standard Linux systems have files for all time zones in the directory `/usr/share/zoneinfo/`, so `/etc/localtime` is just a symbolic link to the actual data file inside that directory. The files in `/usr/share/zoneinfo/` are organized by the name of the corresponding time zone, so the data file for the `America/Sao_Paulo` time zone will be `/usr/share/zoneinfo/America/Sao_Paulo`

As the definitions for the daylight savings time may change, it is important to keep the files in `/usr/share/zoneinfo/` up to date. The upgrade command of the package management tool provided by the distribution should update them every time a new version is available.

### Language and Character Encoding
Linux systems can work with a wide variety of languages and non-western character encodings, definitions known as locales. The most basic locale configuration is the definition of the environment variable `LANG`, from which most shell programs identify the language to use.

The content of the `LANG` variable follows the format `ab_CD`, where `ab` is the language code and `CD` is the region code. The language code should follow the ISO-639 standard and the region code should follow the ISO-3166 standard. A system configured to use Brazilian Portuguese, for example, should have the `LANG` variable defined to `pt_BR.UTF-8`:
```
$ echo $LANG
pt_BR.UTF-8
```
As seen in the sample output, the `LANG` variable also holds the character encoding intended for the system. ASCII, short for American Standard Code for Information Interchange, was the first widely used character encoding standard for electronic communication. However, since ASCII has a very limited range of available numerical values and it was based on the English alphabet, it does not contain characters used by other languages or an extended set of non-alphabetical symbols. The UTF-8 encoding is a Unicode Standard for the ordinary western characters, plus many other non-conventional symbols. As stated by the Unicode Consortium, the maintainer of the Unicode Standard, it should be adopted by default to ensure compatibility between computer platforms:

> The Unicode Standard provides a unique number for every character, no matter what platform, device, application or language. It has been adopted by all modern software providers and now allows data to be transported through many different platforms, devices and applications without corruption. Support of Unicode forms the foundation for the representation of languages and symbols in all major operating systems, search engines, browsers, laptops, and smart phones — plus the Internet and World Wide Web (URLs, HTML, XML, CSS, JSON, etc.). (…​) the Unicode Standard and the availability of tools supporting it are among the most significant recent global software technology trends.

>> — The Unicode Consortium
>> What is Unicode?
Some systems may still use ISO defined standards — like the ISO-8859-1 standard — for the encoding of non-ASCII characters. However, such character encoding standards should be deprecated in favor of Unicode encodings standards. Nevertheless, all major operating systems tend to adopt the Unicode standard by default.

System wide locale settings are configured in the file `/etc/locale.conf`. Variable `LANG` and other locale related variables are assigned in this file like an ordinary shell variable, for example:
```
$ cat /etc/locale.conf
LANG=pt_BR.UTF-8
```
Users can use a custom locale configuration by redefining the `LANG` environment variable. It can be done for the current session only or for future sessions, by adding the new definition to the user’s Bash profile in `~/.bash_profile` or `~/.profile`. Until the user logs in, however, the default system locale will still be used by user independent programs, like the display manager’s login screen.
```
Tip
The command localectl, available on systems employing systemd as the system manager, can also be used to query and change the system locale. For example: localectl set-locale LANG=en_US.UTF-8.
```
In addition to the `LANG` variable, other environment variables have an affect on specific locale aspects, like which currency symbol to use or the correct thousand separator for numbers:

`LC_COLLATE`
- Sets the alphabetical ordering. One of its purposes is to define the order files and directories are listed.

`LC_CTYPE`
- Sets how the system will treat certain sets of characters. It defines, for example, which characters to consider as uppercase or lowercase.

`LC_MESSAGES`
- Sets the language to display program messages (mostly GNU programs).

`LC_MONETARY`
- Sets the money unit and currency format.

`LC_NUMERIC`
- Sets the numerical format for non-monetary values. Its main purpose is to define the thousand and decimal separators.

`LC_TIME`
- Sets the time and date format.

`LC_PAPER`
- Sets the standard paper size.

`LC_ALL`
- Overrides all other variables, including LANG.

The `locale` command will show all defined variables in the current locale configuration:
```
$ locale
LANG=pt_BR.UTF-8
LC_CTYPE="pt_BR.UTF-8"
LC_NUMERIC=pt_BR.UTF-8
LC_TIME=pt_BR.UTF-8
LC_COLLATE="pt_BR.UTF-8"
LC_MONETARY=pt_BR.UTF-8
LC_MESSAGES="pt_BR.UTF-8"
LC_PAPER=pt_BR.UTF-8
LC_NAME=pt_BR.UTF-8
LC_ADDRESS=pt_BR.UTF-8
LC_TELEPHONE=pt_BR.UTF-8
LC_MEASUREMENT=pt_BR.UTF-8
LC_IDENTIFICATION=pt_BR.UTF-8
LC_ALL=
```
The only undefined variable is `LC_ALL`, which can be used to temporarily override all the other locale settings. The following example shows how the date command — running in a system configured to `pt_BR.UTF-8` locale —  will modify its output to comply with the new `LC_ALL` variable:
```
$ date
seg out 21 10:45:21 -03 2019
$ env LC_ALL=en_US.UTF-8 date
Mon Oct 21 10:45:21 -03 2019
```
The modification of the `LC_ALL` variable made both abbreviations for the day of the week and month name to be shown in American English (`en_US`). It is not mandatory, however, to set the same locale for all variables. It is possible, for example, to have the language defined to `pt_BR` and the numerical format (`LC_NUMERIC`) set to the American standard.

Some localisation settings change how programs deal with alphabetical ordering and number formats. Whilst conventional programs are usually prepared to correctly choose a common locale for such situations, scripts can behave unexpectedly when trying to correctly alphabetically order a list of items, for example. For this reason, it is recommended to set the environment variable `LANG` to the common `C` locale, as in `LANG=C`, so the script produces unambiguous results, regardless the localisation definitions used in the system where it is executed. The C locale only conducts a simple bytewise comparison, so it will also perform better than the others.

### Encoding Conversion
Text may appear with unintelligible characters when displayed on a system with a character encoding configuration other than the system where the text was created. The command `iconv` can be used to solve this issue, by converting the file from its original character encoding to the desired one. For example, to convert a file named `original.txt` from the ISO-8859-1 encoding to the file named `converted.txt` using UTF-8 encoding, the following command can be used:
```
$ iconv -f ISO-8859-1 -t UTF-8 original.txt > converted.txt
```
The option `-f ISO-8859-1` (or `--from-code=ISO-8859-1`) sets the encoding of the original file and option `-t UTF-8` (or `--to-code=UTF-8`) sets the encoding for the converted file. All encoding supported by command iconv are listed with command `iconv -l` or `iconv --list`. Instead of using the output redirection, like in the example, option `-o converted.txt` or `--output converted.txt` could also be used.


___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/102-500/)