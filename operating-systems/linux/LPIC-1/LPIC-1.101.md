# LPIC-1: System Architecture

## Lesson 101-1: Determine and configure hardware settings
Since the early years of electronic computing, business and personal computer manufacturers have integrated a variety of hardware parts in their machines, which in turn need to be supported by the operating system. That could be overwhelming from the operating system developer’s perspective, unless standards for instruction sets and device communication are established by the industry. Similar to the standardized abstraction layer provided by the operating system to an application, these standards make it easier to write and maintain an operating system that is not tied to a specific hardware model. However, the complexity of the integrated underlying hardware sometimes requires adjustments on how resources should be exposed to the operating system, so it can be installed and function correctly.

Some of these adjustments can be made even without an installed operating system. Most machines offer a configuration utility that can be executed as the machine is turned on. Until the mid 2000s, the configuration utility was implemented in the BIOS (Basic Input/Output System), the standard for firmware containing the basic configuration routines found in x86 motherboards. From the end of the first decade of the 2000s, machines based on the x86 architecture started to replace the BIOS with a new implementation called UEFI (Unified Extensible Firmware Interface), which has more sophisticated features for identification, testing, configuration and firmware upgrades. Despite the change, it is not uncommon to still call the configuration utility BIOS, as both implementations fulfill the same basic purpose.

```
Note
Further details on the similarities and differences between BIOS and UEFI will be covered in a later lesson.
```

### Device Activation
The system configuration utility is presented after pressing a specific key when the computer is turned on. Which key to press varies from manufacturer to manufacturer, but usually it is `Del` or one of the function keys, such as `F2` or `F12`. The key combination to use is often displayed in the power on screen.

In the BIOS setup utility it is possible to enable and disable integrated peripherals, activate basic error protection and change hardware settings like IRQ (interrupt request) and DMA (direct memory access). Changing these settings is rarely needed on modern machines, but it may be necessary to make adjustments to address specific issues. There are RAM technologies, for example, that are compatible with faster data transfer rates than the default values, so it is recommended to change it to the values specified by the manufacturer. Some CPUs offer features that may not be required for the particular installation and can be deactivated. Disabled features will reduce power consumption and can increase system protection, as CPU features containing known bugs can also be disabled.

If the machine is equipped with many storage devices, it is important to define which one has the correct bootloader and should be the first entry in the device boot order. The operating system may not load if the incorrect device comes first in the BIOS boot verification list.

### Device Inspection in Linux
Once devices are correctly identified, it is up to the operating system to associate the corresponding software components required by them. When a hardware feature is not working as expected, it is important to identify where exactly the problem is happening. When a piece of hardware is not detected by the operating system, it is most likely that the part — or the port to which it is connected — is defective. When the hardware part is correctly detected, but still does not properly work, there may be a problem on the operating system side. Therefore, one of the first steps when dealing with hardware-related issues is to check if the operating system is properly detecting the device. There are two basic ways to identify hardware resources on a Linux system: to use specialized commands or to read specific files inside special filesystems.

### Commands for Inspection
The two essential commands to identify connected devices in a Linux system are:

`lspci`
- Shows all devices currently connected to the PCI (Peripheral Component Interconnect) bus. PCI devices can be either a component attached to the motherboard, like a disk controller, or an expansion card fitted into a PCI slot, like an external graphics card.

`lsusb`
- Lists USB (Universal Serial Bus) devices currently connected to the machine. Although USB devices for almost any imaginable purpose exist, the USB interface is largely used to connect input devices — keyboards, pointing devices — and removable storage media.

The output of commands `lspci` and `lsusb` consists of a list of all PCI and USB devices identified by the operating system. However, the device may not be fully operational yet, because every hardware part requires a software component to control the corresponding device. This software component is called a kernel module and it can be part of the official Linux kernel or added separately from other sources.

Linux kernel modules related to hardware devices are also called drivers, as in other operating systems. Drivers for Linux, however, are not always supplied by the device’s manufacturer. Whilst some manufacturers provide their own binary drivers to be installed separately, many drivers are written by independent developers. Historically, parts that work on Windows, for example, may not have a counterpart kernel module for Linux. Nowadays, Linux-based operating systems have strong hardware support and most devices work effortlessly.

Commands directly related to hardware often require root privileges to execute or will only show limited information when executed by a normal user, so it may be necessary to login as root or to execute the command with `sudo`.

The following output of command `lspci`, for example, shows a few identified devices:

```
$ lspci
01:00.0 VGA compatible controller: NVIDIA Corporation GM107 [GeForce GTX 750 Ti] (rev a2)
04:02.0 Network controller: Ralink corp. RT2561/RT61 802.11g PCI
04:04.0 Multimedia audio controller: VIA Technologies Inc. ICE1712 [Envy24] PCI Multi-Channel I/O Controller (rev 02)
04:0b.0 FireWire (IEEE 1394): LSI Corporation FW322/323 [TrueFire] 1394a Controller (rev 70)
```

The output of such commands can be tens of lines long, so the previous and next examples contain only the sections of interest. The hexadecimal numbers at the beginning of each line are the unique addresses of the corresponding PCI device. The command `lspci` shows more details about a specific device if its address is given with option `-s`, accompanied by the option `-v`:

```
$ lspci -s 04:02.0 -v
04:02.0 Network controller: Ralink corp. RT2561/RT61 802.11g PCI
    Subsystem: Linksys WMP54G v4.1
    Flags: bus master, slow devsel, latency 32, IRQ 21
    Memory at e3100000 (32-bit, non-prefetchable) [size=32K]
    Capabilities: [40] Power Management version 2
    kernel driver in use: rt61pci
```

The output now shows many more details of the device on address `04:02.0`. It is a network controller, whose internal name is `Ralink corp. RT2561/RT61 802.11g PCI`. `Subsystem` is associated with the device’s brand and model — `Linksys WMP54G v4.1` — and can be helpful for diagnostic purposes.

The kernel module can be identified in the line `kernel driver` in use, which shows the module `rt61pci`. From all the gathered information, it is correct to assume that:

- The device was identified.
- A matching kernel module was loaded.
- The device should be ready for use.

Another way to verify which kernel module is in use for the specified device is provided by the option `-k`, available in more recent versions of `lspci`:

```
$ lspci -s 01:00.0 -k
01:00.0 VGA compatible controller: NVIDIA Corporation GM107 [GeForce GTX 750 Ti] (rev a2)
    kernel driver in use: nvidia
    kernel modules: nouveau, nvidia_drm, nvidia
```

For the chosen device, an NVIDIA GPU board, `lspci` tells that the module in use is named `nvidia`, at line `kernel driver in use: nvidia` and all corresponding kernel modules are listed in the line `kernel modules: nouveau, nvidia_drm, nvidia`.

Command `lsusb` is similar to `lspci`, but lists USB information exclusively:

```
$ lsusb
Bus 001 Device 029: ID 1781:0c9f Multiple Vendors USBtiny
Bus 001 Device 028: ID 093a:2521 Pixart Imaging, Inc. Optical Mouse
Bus 001 Device 020: ID 1131:1001 Integrated System Solution Corp. KY-BT100 Bluetooth Adapter
Bus 001 Device 011: ID 04f2:0402 Chicony Electronics Co., Ltd Genius LuxeMate i200 Keyboard
Bus 001 Device 007: ID 0424:7800 Standard Microsystems Corp.
Bus 001 Device 003: ID 0424:2514 Standard Microsystems Corp. USB 2.0 Hub
Bus 001 Device 002: ID 0424:2514 Standard Microsystems Corp. USB 2.0 Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

Command `lsusb` shows the available USB channels and the devices connected to them. As with `lspci`, option `-v` displays more detailed output. A specific device can be selected for inspection by providing its ID to the option `-d`:

```
$ lsusb -v -d 1781:0c9f
Bus 001 Device 029: ID 1781:0c9f Multiple Vendors USBtiny
Device Descriptor:
  bLength                18
  bDescriptorType         1
  bcdUSB               1.01
  bDeviceClass          255 Vendor Specific Class
  bDeviceSubClass         0
  bDeviceProtocol         0
  bMaxPacketSize0         8
  idVendor           0x1781 Multiple Vendors
  idProduct          0x0c9f USBtiny
  bcdDevice            1.04
  iManufacturer           0
  iProduct                2 USBtiny
  iSerial                 0
  bNumConfigurations      1
```

With option `-t`, command `lsusb` shows the current USB device mappings as a hierarchical tree:

```
$ lsusb -t
/:  Bus 01.Port 1: Dev 1, Class=root_hub, Driver=dwc_otg/1p, 480M
    |__ Port 1: Dev 2, If 0, Class=Hub, Driver=hub/4p, 480M
        |__ Port 1: Dev 3, If 0, Class=Hub, Driver=hub/3p, 480M
            |__ Port 2: Dev 11, If 1, Class=Human Interface Device, Driver=usbhid, 1.5M
            |__ Port 2: Dev 11, If 0, Class=Human Interface Device, Driver=usbhid, 1.5M
            |__ Port 3: Dev 20, If 0, Class=Wireless, Driver=btusb, 12M
            |__ Port 3: Dev 20, If 1, Class=Wireless, Driver=btusb, 12M
            |__ Port 3: Dev 20, If 2, Class=Application Specific Interface, Driver=, 12M
            |__ Port 1: Dev 7, If 0, Class=Vendor Specific Class, Driver=lan78xx, 480M
        |__ Port 2: Dev 28, If 0, Class=Human Interface Device, Driver=usbhid, 1.5M
        |__ Port 3: Dev 29, If 0, Class=Vendor Specific Class, Driver=, 1.5M
```

It is possible that not all devices have corresponding modules associated with them. The communication with certain devices can be handled by the application directly, without the intermediation provided by a module. Nevertheless, there is significant information in the output provided by `lsusb -t`. When a matching module exists, its name appears at the end of the line for the device, as in `Driver=btusb`. The device `Class` identifies the general category, like `Human Interface Device`, `Wireless`, `Vendor Specific Class`, `Mass Storage`, among others. To verify which device is using the module `btusb`, present in the previous listing, both `Bus` and `Dev` numbers should be given to the `-s` option of the `lsusb` command:

```
$ lsusb -s 01:20
Bus 001 Device 020: ID 1131:1001 Integrated System Solution Corp. KY-BT100 Bluetooth Adapter
```

It is common to have a large set of loaded kernel modules in a standard Linux system at any time. The preferable way to interact with them is to use the commands provided by the `kmod` package, which is a set of tools to handle common tasks with Linux kernel modules like insert, remove, list, check properties, resolve dependencies and aliases. The `lsmod` command, for example, shows all currently loaded modules:

```
$ lsmod
Module                  Size  Used by
kvm_intel             138528  0
kvm                   421021  1 kvm_intel
iTCO_wdt               13480  0
iTCO_vendor_support    13419  1 iTCO_wdt
snd_usb_audio         149112  2
snd_hda_codec_realtek  51465  1
snd_ice1712            75006  3
snd_hda_intel          44075  7
arc4                   12608  2
snd_cs8427             13978  1 snd_ice1712
snd_i2c                13828  2 snd_ice1712,snd_cs8427
snd_ice17xx_ak4xxx     13128  1 snd_ice1712
snd_ak4xxx_adda        18487  2 snd_ice1712,snd_ice17xx_ak4xxx
microcode              23527  0
snd_usbmidi_lib        24845  1 snd_usb_audio
gspca_pac7302          17481  0
gspca_main             36226  1 gspca_pac7302
videodev              132348  2 gspca_main,gspca_pac7302
rt61pci                32326  0
rt2x00pci              13083  1 rt61pci
media                  20840  1 videodev
rt2x00mmio             13322  1 rt61pci
hid_dr                 12776  0
snd_mpu401_uart        13992  1 snd_ice1712
rt2x00lib              67108  3 rt61pci,rt2x00pci,rt2x00mmio
snd_rawmidi            29394  2 snd_usbmidi_lib,snd_mpu401_uart
```

The output of command lsmod is divided into three columns:

`Module`
- Module name.

`Size`
- Amount of RAM occupied by the module, in bytes.

`Used by`
- Depending modules.

Some modules require other modules to work properly, as is the case with modules for audio devices:

```
$ lsmod | fgrep -i snd_hda_intel
snd_hda_intel          42658  5
snd_hda_codec         155748  3 snd_hda_codec_hdmi,snd_hda_codec_via,snd_hda_intel
snd_pcm                81999  3 snd_hda_codec_hdmi,snd_hda_codec,snd_hda_intel
snd_page_alloc         13852  2 snd_pcm,snd_hda_intel
snd                    59132  19 snd_hwdep,snd_timer,snd_hda_codec_hdmi,snd_hda_codec_via,snd_pcm,snd_seq,snd_hda_codec,snd_hda_intel,snd_seq_device
```

The third column, `Used by`, shows the modules that require the module in the first column to properly work. Many modules of the Linux sound architecture, prefixed by snd, are interdependent. When looking for problems during system diagnostics, it may be useful to unload specific modules currently loaded. Command `modprobe` can be used to both load and to unload kernel modules: to unload a module and its related modules, as long as they are not being used by a running process, command `modprobe -r` should be used. For example, to unload module `snd-hda-intel` (the module for a HDA Intel audio device) and other modules related to the sound system:
```
# modprobe -r snd-hda-intel
```

In addition to loading and unloading kernel modules while the system is running, it is possible to change module parameters when the kernel is being loaded, not so different from passing options to commands. Each module accepts specific parameters, but most times the default values are recommended and extra parameters are not needed. However, in some cases it is necessary to use parameters to change the behaviour of a module to work as expected.

Using the module name as the only argument, command `modinfo` shows a description, the file, the author, the license, the identification, the dependencies and the available parameters for the given module. Customized parameters for a module can be made persistent by including them in the file `/etc/modprobe.conf` or in individual files with the extension `.conf` in the directory `/etc/modprobe.d/`. Option `-p` will make command modinfo display all available parameters and ignore the other information:

```
# modinfo -p nouveau
vram_pushbuf:Create DMA push buffers in VRAM (int)
tv_norm:Default TV norm.
                Supported: PAL, PAL-M, PAL-N, PAL-Nc, NTSC-M, NTSC-J,
                        hd480i, hd480p, hd576i, hd576p, hd720p, hd1080i.
                Default: PAL
                NOTE Ignored for cards with external TV encoders. (charp)
nofbaccel:Disable fbcon acceleration (int)
fbcon_bpp:fbcon bits-per-pixel (default: auto) (int)
mst:Enable DisplayPort multi-stream (default: enabled) (int)
tv_disable:Disable TV-out detection (int)
ignorelid:Ignore ACPI lid status (int)
duallink:Allow dual-link TMDS (default: enabled) (int)
hdmimhz:Force a maximum HDMI pixel clock (in MHz) (int)
config:option string to pass to driver core (charp)
debug:debug string to pass to driver core (charp)
noaccel:disable kernel/abi16 acceleration (int)
modeset:enable driver (default: auto, 0 = disabled, 1 = enabled, 2 = headless) (int)
atomic:Expose atomic ioctl (default: disabled) (int)
runpm:disable (0), force enable (1), optimus only default (-1) (int)
```

The sample output shows all the parameters available for module `nouveau`, a kernel module provided by the Nouveau Project as an alternative to the proprietary drivers for NVIDIA GPU cards. Option `modeset`, for example, allows to control whether display resolution and depth will be set in the kernel space rather than user space. Adding options `nouveau modeset=0` to the file `/etc/modprobe.d/nouveau.conf` will disable the modeset kernel feature.

If a module is causing problems, the file `/etc/modprobe.d/blacklist.conf` can be used to block the loading of the module. For example, to prevent the automatic loading of the module `nouveau`, the line `blacklist nouveau` must be added to the file `/etc/modprobe.d/blacklist.conf`. This action is required when the proprietary module `nvidia` is installed and the default module `nouveau` should be set aside.

```
Note
You can modify the /etc/modprobe.d/blacklist.conf file that already exists on the system by default. However, the preferred method is to create a separate configuration file, /etc/modprobe.d/<module_name>.conf, that will contain settings specific only to the given kernel module.
```

### Information Files and Device Files
The commands `lspci`, `lsusb` and `lsmod` act as front-ends to read hardware information stored by the operating system. This kind of information is kept in special files in the directories `/proc` and `/sys`. These directories are mount points to filesystems not present in a device partition, but only in RAM space used by the kernel to store runtime configuration and information on running processes. Such filesystems are not intended for conventional file storage, so they are called pseudo-filesystems and only exist while the system is running. The `/proc` directory contains files with information regarding running processes and hardware resources. Some of the important files in `/proc` for inspecting hardware are:

`/proc/cpuinfo`
- Lists detailed information about the CPU(s) found by the operating system.

`/proc/interrupts`
- A list of numbers of the interrupts per IO device for each CPU.

`/proc/ioports`
- Lists currently registered Input/Output port regions in use.

`/proc/dma`
- Lists the registered DMA (direct memory access) channels in use.

Files inside the `/sys` directory have similar roles to those in `/proc`. However, the `/sys` directory has the specific purpose of storing device information and kernel data related to hardware, whilst `/proc` also contains information about various kernel data structures, including running processes and configuration.

Another directory directly related to devices in a standard Linux system is `/dev`. Every file inside `/dev` is associated with a system device, particularly storage devices. A legacy IDE hard drive, for example, when connected to the motherboard’s first IDE channel, is represented by the file `/dev/hda`. Every partition in this disk will be identified by `/dev/hda1`, `/dev/hda2` up to the last partition found.

Removable devices are handled by the udev subsystem, which creates the corresponding devices in `/dev`. The Linux kernel captures the hardware detection event and passes it to the udev process, which then identifies the device and dynamically creates corresponding files in `/dev`, using pre-defined rules.

In current Linux distributions, udev is responsible for the identification and configuration of the devices already present during machine power-up (coldplug detection) and the devices identified while the system is running (hotplug detection). Udev relies on SysFS, the pseudo filesystem for hardware related information mounted in `/sys`.

```
Note
Hotplug is the term used to refer to the detection and configuration of a device while the system is running, such as when a USB device is inserted. The Linux kernel has supported hotplug features since version 2.6, allowing most system buses (PCI, USB, etc.) to trigger hotplug events when a device is connected or disconnected.
```

As new devices are detected, udev searches for a matching rule in the pre-defined rules stored in the directory `/etc/udev/rules.d/`. The most important rules are provided by the distribution, but new ones can be added for specific cases.

### Storage Devices
In Linux, storage devices are generically referred as block devices, because data is read to and from these devices in blocks of buffered data with different sizes and positions. Every block device is identified by a file in the `/dev` directory, with the name of the file depending on the device type (IDE, SATA, SCSI, etc.) and its partitions. CD/DVD and floppy devices, for example, will have their names given accordingly in `/dev`: a CD/DVD drive connected to the second IDE channel will be identified as `/dev/hdc` (`/dev/hda` and `/dev/hdb` are reserved for the master and slave devices on the first IDE channel) and an old floppy drive will be identified as `/dev/fdO`, `/dev/fd1`, etc.

From Linux kernel version 2.4 onwards, most storage devices are now identified as if they were SCSI devices, regardless of their hardware type. IDE, SSD and USB block devices will be prefixed by `sd`. For IDE disks, the `sd` prefix will be used, but the third letter will be chosen depending on whether the drive is a master or slave (in the first IDE channel, master will be `sda` and slave will be `sdb`). Partitions are listed numerically. Paths `/dev/sda1`, `/dev/sda2`, etc. are used for the first and second partitions of the block device identified first and `/dev/sdb1`, `/dev/sdb2`, etc. used to identify the first and second partitions of the block device identified second. The exception to this pattern occurs with memory cards (SD cards) and NVMe devices (SSD connected to the PCI Express bus). For SD cards, the paths `/dev/mmcblk0p1`, `/dev/mmcblk0p2`, etc. are used for the first and second partitions of the device identified first and `/dev/mmcblk1p1`, `/dev/mmcblk1p2`, etc. used to identify the first and second partitions of the device identified second. NVMe devices receive the prefix `nvme`, as in `/dev/nvme0n1p1` and `/dev/nvme0n1p2`.

___

## Lesson 101-2: Boot the System

In order to control the machine, the operating system’s main component — the kernel — must be loaded by a program called a bootloader, which itself is loaded by a pre-installed firmware such as BIOS or UEFI. The bootloader can be customized to pass parameters to the kernel, such as which partition contains the root filesystem or in which mode the operating system should execute. Once loaded the kernel continues the boot process identifying and configuring the hardware. Lastly, the kernel calls the utility responsible for starting and managing the system’s services.

```
Note
On some Linux distributions, commands executed in this lesson may require root privileges.
```

### BIOS or UEFI
The procedures executed by x86 machines to run the bootloader are different whether they use BIOS or UEFI. The BIOS, short for Basic Input/Output System, is a program stored in a non-volatile memory chip attached to the motherboard, executed every time the computer is powered on. This type of program is called firmware and its storage location is separate from the other storage devices the system may have. The BIOS assumes that the first 440 bytes in the first storage device — following the order defined in the BIOS configuration utility — are the first stage of the bootloader (also called bootstrap). The first 512 bytes of a storage device are named the MBR (Master Boot Record) of storage devices using the standard DOS partition schema and, in addition to the first stage of the bootloader, contain the partition table. If the MBR does not contain the correct data, the system will not be able to boot, unless an alternative method is employed.

Generally speaking, the pre-operating steps to boot a system equipped with BIOS are:
- The POST (power-on self-test) process is executed to identify simple hardware failures as soon as the machine is powered on.
- The BIOS activates the basic components to load the system, like video output, keyboard and storage media.
- The BIOS loads the first stage of the bootloader from the MBR (the first 440 bytes of the first device, as defined in the BIOS configuration utility).
- The first stage of the bootloader calls the second stage of the bootloader, responsible for presenting boot options and loading the kernel.

The UEFI, short for Unified Extensible Firmware Interface, differs from BIOS in some key points. As the BIOS, the UEFI is also a firmware, but it can identify partitions and read many filesystems found in them. The UEFI does not rely on the MBR, taking into account only the settings stored in its non-volatile memory (NVRAM) attached to the motherboard. These definitions indicate the location of the UEFI compatible programs, called EFI applications, that will be executed automatically or called from a boot menu. EFI applications can be bootloaders, operating system selectors, tools for system diagnostics and repair, etc. They must be in a conventional storage device partition and in a compatible filesystem. The standard compatible filesystems are FAT12, FAT16 and FAT32 for block devices and ISO-9660 for optical media. This approach allows for the implementation of much more sophisticated tools than those possible with BIOS.

The partition containing the EFI applications is called the EFI System Partition or just ESP. This partition must not be shared with other system filesystems, like the root filesystem or user data filesystems. The EFI directory in the ESP partition contains the applications pointed to by the entries saved in the NVRAM.

Generally speaking, the pre-operating system boot steps on a system with UEFI are:
- The POST (power-on self-test) process is executed to identify simple hardware failures as soon as the machine is powered on.
- The UEFI activates the basic components to load the system, like video output, keyboard and storage media.
- UEFI’s firmware reads the definitions stored in NVRAM to execute the pre-defined EFI application stored in the ESP partition’s filesystem. Usually, the pre-defined EFI application is a bootloader.
- If the pre-defined EFI application is a bootloader, it will load the kernel to start the operating system.

The UEFI standard also supports a feature called Secure Boot, which only allows the execution of signed EFI applications, that is, EFI applications authorized by the hardware manufacturer. This feature increases the protection against malicious software, but can make it difficult to install operating systems not covered by the manufacturer’s warranty.

### The Bootloader
The most popular bootloader for Linux in the x86 architecture is GRUB (Grand Unified Bootloader). As soon as it is called by the BIOS or by the UEFI, GRUB displays a list of operating systems available to boot. Sometimes the list does not appear automatically, but it can be invoked by pressing `Shift` while GRUB is being called by BIOS. In UEFI systems, the `Esc` key should be used instead.

From the GRUB menu it is possible to choose which one of the installed kernels should be loaded and to pass new parameters to it. Most kernel parameters follow the pattern `option=value`. Some of the most useful kernel parameters are:

`acpi`
- Enables/disables ACPI support. `acpi=off` will disable support for ACPI.

`init`
- Sets an alternative system initiator. For example, `init=/bin/bash` will set the Bash shell as the initiator. This means that a shell session will start just after the kernel boot process.

`systemd.unit`
- Sets the systemd target to be activated. For example, `systemd.unit=graphical.target`. Systemd also accepts the numerical runlevels as defined for SysV. To activate the runlevel 1, for example, it is only necessary to include the number `1` or the letter `S` (short for “single”) as a kernel parameter.

`mem`
- Sets the amount of available RAM for the system. This parameter is useful for virtual machines so as to limit how much RAM will be available to each guest. Using `mem=512M` will limit to 512 megabytes the amount of available RAM to a particular guest system.

`maxcpus`
- Limits the number of processors (or processor cores) visible to the system in symmetric multi-processor machines. It is also useful for virtual machines. A value of `0` turns off the support for multi-processor machines and has the same effect as the kernel parameter `nosmp`. The parameter `maxcpus=2` will limit the number of processors available to the operating system to two.

`-quiet`
- Hides most boot messages.

`vga`
- Selects a video mode. The parameter `vga=ask` will show a list of the available modes to choose from.

`root`
- Sets the root partition, distinct from the one pre-configured in the bootloader. For example, `root=/dev/sda3`.

`rootflags`
- Mount options for the root filesystem.

`ro`
- Makes the initial mount of the root filesystem read-only.

`rw`
- Allows writing in the root filesystem during initial mount.

Changing the kernel parameters is not usually required, but it can be useful to detect and solve operating system related problems. Kernel parameters must be added to the file `/etc/default/grub` in the line `GRUB_CMDLINE_LINUX` to make them persistent across reboots. A new configuration file for the bootloader must be generated every time `/etc/default/grub` changes, which is accomplished by the command `grub-mkconfig -o /boot/grub/grub.cfg`. Once the operating system is running, the kernel parameters used for loading the current session are available for reading in the file `/proc/cmdline`.

```
Note
Configuring GRUB will be discussed further in a later lesson.
```

### System Initialization
Apart from the kernel, the operating system depends on other components that provide the expected features. Many of these components are loaded during the system initialization process, varying from simple shell scripts to more complex service programs. Scripts are often used to perform short lived tasks that will run and terminate during the system initialization process. Services, also known as daemons, may be active all the time as they can be responsible for intrinsic aspects of the operating system.

The diversity of ways that startup scripts and daemons with the most different characteristics can be built into a Linux distribution is huge, a fact that historically hindered the development of a single solution that meets the expectations of maintainers and users of all Linux distributions. However, any tool that the distribution maintainers have chosen to perform this function will at least be able to start, stop and restart system services. These actions are often performed by the system itself after a software update, for example, but the system administrator will almost always need to manually restart the service after making modifications to its configuration file.

It is also convenient for a system administrator to be able to activate a particular set of daemons, depending on the circumstances. It should be possible, for example, to run just a minimum set of services in order to perform system maintenance tasks.

```
Note
Strictly speaking, the operating system is just the kernel and its components which control the hardware and manages all processes. It is common, however, to use the term “operating system” more loosely, to designate an entire group of distinct programs that make up the software environment where the user can perform the basic computational tasks.
```

The initialization of the operating system starts when the bootloader loads the kernel into RAM. Then, the kernel will take charge of the CPU and will start to detect and setup the fundamental aspects of the operating system, like basic hardware configuration and memory addressing.

The kernel will then open the initramfs (initial RAM filesystem). The initramfs is an archive containing a filesystem used as a temporary root filesystem during the boot process. The main purpose of an initramfs file is to provide the required modules so the kernel can access the “real” root filesystem of the operating system.

As soon as the root filesystem is available, the kernel will mount all filesystems configured in `/etc/fstab` and then will execute the first program, a utility named `init`. The `init` program is responsible for running all initialization scripts and system daemons. There are distinct implementations of such system initiators apart from the traditional init, like systemd and Upstart. Once the init program is loaded, the initramfs is removed from RAM.

`SysV standard`
- A service manager based on the SysVinit standard controls which daemons and resources will be available by employing the concept of runlevels. Runlevels are numbered 0 to 6 and are designed by the distribution maintainers to fulfill specific purposes. The only runlevel definitions shared between all distributions are the runlevels 0, 1 and 6.

`systemd`
- systemd is a modern system and services manager with a compatibility layer for the SysV commands and runlevels. systemd has a concurrent structure, employs sockets and D-Bus for service activation, on-demand daemon execution, process monitoring with cgroups, snapshot support, system session recovery, mount point control and a dependency-based service control. In recent years most major Linux distributions have gradually adopted systemd as their default system manager.

`Upstart`
- Like systemd, Upstart is a substitute to init. The focus of Upstart is to speed up the boot process by parallelizing the loading process of system services. Upstart was used by Ubuntu based distributions in past releases, but today gave way to systemd.

### Initialization Inspection
Errors may occur during the boot process, but they may not be so critical to completely halt the operating system. Notwithstanding, these errors may compromise the expected behaviour of the system. All errors result in messages that can be used for future investigations, as they contain valuable information about when and how the error occurred. Even when no error messages are generated, the information collected during the boot process can be useful for tuning and configuration purposes.

The memory space where the kernel stores its messages, including the boot messages, is called the kernel ring buffer. The messages are kept in the kernel ring buffer even when they are not displayed during the initialization process, like when an animation is displayed instead. However the kernel ring buffer loses all messages when the system is turned off or by executing the command `dmesg --clear`. Without options, command `dmesg` displays the current messages in the kernel ring buffer:

```
$ dmesg
[    5.262389] EXT4-fs (sda1): mounted filesystem with ordered data mode. Opts: (null)
[    5.449712] ip_tables: (C) 2000-2006 Netfilter Core Team
[    5.460286] systemd[1]: systemd 237 running in system mode.
[    5.480138] systemd[1]: Detected architecture x86-64.
[    5.481767] systemd[1]: Set hostname to <torre>.
[    5.636607] systemd[1]: Reached target User and Group Name Lookups.
[    5.636866] systemd[1]: Created slice System Slice.
[    5.637000] systemd[1]: Listening on Journal Audit Socket.
[    5.637085] systemd[1]: Listening on Journal Socket.
[    5.637827] systemd[1]: Mounting POSIX Message Queue File System...
[    5.638639] systemd[1]: Started Read required files in advance.
[    5.641661] systemd[1]: Starting Load Kernel Modules...
[    5.661672] EXT4-fs (sda1): re-mounted. Opts: errors=remount-ro
[    5.694322] lp: driver loaded but no devices found
[    5.702609] ppdev: user-space parallel port driver
[    5.705384] parport_pc 00:02: reported by Plug and Play ACPI
[    5.705468] parport0: PC-style at 0x378 (0x778), irq 7, dma 3 [PCSPP,TRISTATE,COMPAT,EPP,ECP,DMA]
[    5.800146] lp0: using parport0 (interrupt-driven).
[    5.897421] systemd-journald[352]: Received request to flush runtime journal from PID 1
```

The output of `dmesg` can be hundreds of lines long, so the previous listing contains only the excerpt showing the kernel calling the systemd service manager. The values in the beginning of the lines are the amount of seconds relative to when kernel load begins.

In systems based on systemd, command `journalctl` will show the initialization messages with options `-b`, `--boot`, `-k` or `--dmesg`. Command `journalctl --list-boots` shows a list of boot numbers relative to the current boot, their identification hash and the timestamps of the first and last corresponding messages:

```
$ journalctl --list-boots
 -4 9e5b3eb4952845208b841ad4dbefa1a6 Thu 2019-10-03 13:39:23 -03—Thu 2019-10-03 13:40:30 -03
 -3 9e3d79955535430aa43baa17758f40fa Thu 2019-10-03 13:41:15 -03—Thu 2019-10-03 14:56:19 -03
 -2 17672d8851694e6c9bb102df7355452c Thu 2019-10-03 14:56:57 -03—Thu 2019-10-03 19:27:16 -03
 -1 55c0d9439bfb4e85a20a62776d0dbb4d Thu 2019-10-03 19:27:53 -03—Fri 2019-10-04 00:28:47 -03
  0 08fbbebd9f964a74b8a02bb27b200622 Fri 2019-10-04 00:31:01 -03—Fri 2019-10-04 10:17:01 -03
```

Previous initialization logs are also kept in systems based on systemd, so messages from prior operating system sessions can still be inspected. If options `-b 0` or `--boot=0` are provided, then messages for the current boot will be shown. Options `-b -1` or `--boot=-1` will show messages from the previous initialization. Options `-b -2` or `--boot=-2` will show the messages from the initialization before that and so on. The following excerpt shows the kernel calling the systemd service manager for the last initialization process:

```
$ journalctl -b 0
oct 04 00:31:01 ubuntu-host kernel: EXT4-fs (sda1): mounted filesystem with ordered data mode. Opts: (null)
oct 04 00:31:01 ubuntu-host kernel: ip_tables: (C) 2000-2006 Netfilter Core Team
oct 04 00:31:01 ubuntu-host systemd[1]: systemd 237 running in system mode.
oct 04 00:31:01 ubuntu-host systemd[1]: Detected architecture x86-64.
oct 04 00:31:01 ubuntu-host systemd[1]: Set hostname to <torre>.
oct 04 00:31:01 ubuntu-host systemd[1]: Reached target User and Group Name Lookups.
oct 04 00:31:01 ubuntu-host systemd[1]: Created slice System Slice.
oct 04 00:31:01 ubuntu-host systemd[1]: Listening on Journal Audit Socket.
oct 04 00:31:01 ubuntu-host systemd[1]: Listening on Journal Socket.
oct 04 00:31:01 ubuntu-host systemd[1]: Mounting POSIX Message Queue File System...
oct 04 00:31:01 ubuntu-host systemd[1]: Started Read required files in advance.
oct 04 00:31:01 ubuntu-host systemd[1]: Starting Load Kernel Modules...
oct 04 00:31:01 ubuntu-host kernel: EXT4-fs (sda1): re-mounted. Opts: commit=300,barrier=0,errors=remount-ro
oct 04 00:31:01 ubuntu-host kernel: lp: driver loaded but no devices found
oct 04 00:31:01 ubuntu-host kernel: ppdev: user-space parallel port driver
oct 04 00:31:01 ubuntu-host kernel: parport_pc 00:02: reported by Plug and Play ACPI
oct 04 00:31:01 ubuntu-host kernel: parport0: PC-style at 0x378 (0x778), irq 7, dma 3 [PCSPP,TRISTATE,COMPAT,EPP,ECP,DMA]
oct 04 00:31:01 ubuntu-host kernel: lp0: using parport0 (interrupt-driven).
oct 04 00:31:01 ubuntu-host systemd-journald[352]: Journal started
oct 04 00:31:01 ubuntu-host systemd-journald[352]: Runtime journal (/run/log/journal/abb765408f3741ae9519ab3b96063a15) is 4.9M, max 39.4M, 34.5M free.
oct 04 00:31:01 ubuntu-host systemd-modules-load[335]: Inserted module 'lp'
oct 04 00:31:01 ubuntu-host systemd-modules-load[335]: Inserted module 'ppdev'
oct 04 00:31:01 ubuntu-host systemd-modules-load[335]: Inserted module 'parport_pc'
oct 04 00:31:01 ubuntu-host systemd[1]: Starting Flush Journal to Persistent Storage...
```

Initialization and other messages issued by the operating system are stored in files inside the directory `/var/log/`. If a critical error happens and the operating system is unable to continue the initialization process after loading the kernel and the initramfs, an alternative boot medium could be used to start the system and to access the corresponding filesystem. Then, the files under `/var/log/` can be searched for possible reasons causing the interruption of the boot process. Options `-D` or `--directory` of command `journalctl` can be used to read log messages in directories other than `/var/log/journal/`, which is the default location for systemd log messages. As systemd’s log messages are not stored in raw text, command `journalctl` is required to read them.

___

## Lesson 101-3: Change runlevels / boot targets and shutdown or reboot the system

A common feature among operating systems following Unix design principles is the employment of separate processes to control distinct functions of the system. These processes, called daemons (or, more generally, services), are also responsible for extended features underlying the operating system, like network application services (HTTP server, file sharing, email, etc.), databases, on-demand configuration, etc. Although Linux utilizes a monolithic kernel, many low level aspects of the operating system are affected by daemons, like load balancing and firewall configuration.

Which daemons should be active depends on the purpose of the system. The set of active daemons should also be modifiable at runtime, so services can be started and stopped without having to reboot the whole system. To tackle this issue, every major Linux distribution offers some form of service management utility to control the system.

Services can be controlled by shell scripts or by a program and its supporting configuration files. The first method is implemented by the SysVinit standard, also known as System V or just SysV. The second method is implemented by systemd and Upstart. Historically, SysV based service managers were the most used by Linux distributions. Today, systemd based service managers are more often found in most Linux distributions. The service manager is the first program launched by the kernel during the boot process, so its PID (process identification number) is always 1.

### SysVinit
A service manager based on the SysVinit standard will provide predefined sets of system states, called runlevels, and their corresponding service script files to be executed. Runlevels are numbered 0 to 6, being generally assigned to the following purposes:

`Runlevel 0`
- System shutdown.

`Runlevel 1, s or single`
- Single user mode, without network and other non-essential capabilities (maintenance mode).

`Runlevel 2, 3 or 4`
- Multi-user mode. Users can log in by console or network. Runlevels 2 and 4 are not often used.

`Runlevel 5`
- Multi-user mode. It is equivalent to 3, plus the graphical mode login.

`Runlevel 6`
- System restart.

The program responsible for managing runlevels and associated daemons/resources is `/sbin/init`. During system initialization, the `init` program identifies the requested runlevel, defined by a kernel parameter or in the `/etc/inittab` file, and loads the associated scripts listed there for the given runlevel. Every runlevel may have many associated service files, usually scripts in the `/etc/init.d/` directory. As not all runlevels are equivalent through different Linux distributions, a short description of the runlevel’s purpose can also be found in SysV based distributions.

The syntax of the `/etc/inittab` file uses this format:

```
id:runlevels:action:process
```

The `id` is a generic name up to four characters in length used to identify the entry. The `runlevels` entry is a list of runlevel numbers for which a specified action should be executed. The action term defines how `init` will execute the process indicated by the term `process`. The available actions are:

`boot`
- The process will be executed during system initialization. The field `runlevels` is ignored.

`bootwait`
- The process will be executed during system initialization and `init` will wait until it finishes to continue. The field `runlevels` is ignored.

`sysinit`
- The process will be executed after system initialization, regardless of runlevel. The field `runlevels` is ignored.

`wait`
- The process will be executed for the given runlevels and `init` will wait until it finishes to continue.

`respawn`
- The process will be restarted if it is terminated.

`ctrlaltdel`
- The process will be executed when the init process receives the `SIGINT` signal, triggered when the key sequence of `Ctrl`+`Alt`+`Del` is pressed.

The default runlevel — the one that will be chosen if no other is given as a kernel parameter — is also defined in `/etc/inittab`, in the entry `id:x:initdefault`. The `x` is the number of the default runlevel. This number should never be `0` or `6`, given that it would cause the system to shutdown or restart as soon as it finishes the boot process. A typical `/etc/inittab` file is shown below:

```
# Default runlevel
id:3:initdefault:

# Configuration script executed during boot
si::sysinit:/etc/init.d/rcS

# Action taken on runlevel S (single user)
~:S:wait:/sbin/sulogin

# Configuration for each execution level
l0:0:wait:/etc/init.d/rc 0
l1:1:wait:/etc/init.d/rc 1
l2:2:wait:/etc/init.d/rc 2
l3:3:wait:/etc/init.d/rc 3
l4:4:wait:/etc/init.d/rc 4
l5:5:wait:/etc/init.d/rc 5
l6:6:wait:/etc/init.d/rc 6

# Action taken upon ctrl+alt+del keystroke
ca::ctrlaltdel:/sbin/shutdown -r now

# Enable consoles for runlevels 2 and 3
1:23:respawn:/sbin/getty tty1 VC linux
2:23:respawn:/sbin/getty tty2 VC linux
3:23:respawn:/sbin/getty tty3 VC linux
4:23:respawn:/sbin/getty tty4 VC linux

# For runlevel 3, also enable serial
# terminals ttyS0 and ttyS1 (modem) consoles
S0:3:respawn:/sbin/getty -L 9600 ttyS0 vt320
S1:3:respawn:/sbin/mgetty -x0 -D ttyS1
```

The `telinit q` command should be executed every time after the `/etc/inittab` file is modified. The argument `q` (or `Q`) tells init to reload its configuration. Such a step is important to avoid a system halt due to an incorrect configuration in `/etc/inittab`.

The scripts used by `init` to setup each runlevel are stored in the directory `/etc/init.d/`. Every runlevel has an associated directory in `/etc/`, named `/etc/rc0.d/`, `/etc/rc1.d/`, `/etc/rc2.d/`, etc., with the scripts that should be executed when the corresponding runlevel starts. As the same script can be used by different runlevels, the files in those directories are just symbolic links to the actual scripts in `/etc/init.d/`. Furthermore, the first letter of the link filename in the runlevel’s directory indicates if the service should be started or terminated for the corresponding runlevel. A link’s filename starting with letter `K` determines that the service will be killed when entering the runlevel (kill). Starting with letter `S`, the service will be started when entering the runlevel (start). The directory `/etc/rc1.d/`, for example, will have many links to network scripts beginning with letter `K`, considering that the runlevel 1 is the single user runlevel, without network connectivity.

The command `runlevel` shows the current runlevel for the system. The `runlevel` command shows two values, the first is the previous runlevel and the second is the current runlevel:

```
$ runlevel
N 3
```

The letter `N` in the output shows that the runlevel has not changed since last boot. In the example, the `runlevel 3` is the current runlevel of the system.

The same `init` program can be used to alternate between runlevels in a running system, without the need to reboot. The command `telinit` can also be used to alternate between runlevels. For example, commands `telinit 1`, `telinit s` or `telinit S` will change the system to runlevel 1.

 ### systemd
Currently, systemd is the most widely used set of tools to manage system resources and services, which are referred to as units by systemd. A unit consists of a name, a type and a corresponding configuration file. For example, the unit for a httpd server process (like the Apache web server) will be `httpd.service` on Red Hat based distributions and its configuration file will also be called `httpd.service` (on Debian based distributions this unit is named `apache2.service`).

There are seven distinct types of systemd units:

`service`
- The most common unit type, for active system resources that can be initiated, interrupted and reloaded.

`socket`
- The socket unit type can be a filesystem socket or a network socket. All socket units have a corresponding service unit, loaded when the socket receives a request.

`device`
- A device unit is associated with a hardware device identified by the kernel. A device will only be taken as a systemd unit if a udev rule for this purpose exists. A device unit can be used to resolve configuration dependencies when certain hardware is detected, given that properties from the udev rule can be used as parameters for the device unit.

`mount`
- A mount unit is a mount point definition in the filesystem, similar to an entry in /etc/fstab.

`automount`
- An automount unit is also a mount point definition in the filesystem, but mounted automatically. Every automount unit has a corresponding mount unit, which is initiated when the automount mount point is accessed.

`target`
- A target unit is a grouping of other units, managed as a single unit.

`snapshot`
- A snapshot unit is a saved state of the systemd manager (not available on every Linux distribution).

The main command for controlling systemd units is `systemctl`. Command `systemctl` is used to execute all tasks regarding unit activation, deactivation, execution, interruption, monitoring, etc. For a fictitious unit called `unit.service`, for example, the most common `systemctl` actions will be:

`systemctl start unit.service`
- Starts `unit`.

`systemctl stop unit.service`
- Stops `unit`.

`systemctl restart unit.service`
- Restarts `unit`.

`systemctl status unit.service`
= Shows the state of `unit`, including if it is running or not.

`systemctl is-active unit.service`
- Shows active if unit is running or inactive otherwise.

`systemctl enable unit.service`
- Enables unit, that is, unit will load during system initialization.

`systemctl disable unit.service`
- unit will not start with the system.

`systemctl is-enabled unit.service`
- Verifies if unit starts with the system. The answer is stored in the variable $?. The value 0 indicates that unit starts with the system and the value 1 indicates that unit does not starts with the system.

```
Note
Newer installations of systemd will actually list a unit’s configuration for boot time. For example:

$ systemctl is-enabled apparmor.service
enabled
```

If no other units with the same name exist in the system, then the suffix after the dot can be dropped. If, for example, there is only one `httpd` unit of type service, then only `httpd` is enough as the unit parameter for `systemctl`.

The `systemctl` command can also control system targets. The `multi-user.target` unit, for example, combines all units required by the multi-user system environment. It is similar to the runlevel number 3 in a system utilizing SysV.

Command `systemctl isolate` alternates between different targets. So, to manually alternate to target `multi-user`:

```
# systemctl isolate multi-user.target
```

There are corresponding targets to SysV runlevels, starting with `runlevelO.target` up to `runlevel6.target`. However, systemd does not use the `/etc/inittab` file. To change the default system target, the option `systemd.unit` can be added to the kernel parameters list. For example, to use `multi-user.target` as the standard target, the kernel parameter should be `systemd.unit=multi-user.target`. All kernel parameters can be made persistent by changing the bootloader configuration.

Another way to change the default target is to modify the symbolic link `/etc/systemd/system/default.target` so it points to the desired target. The redefinition of the link can be done with the `systemctl` command by itself:

```
# systemctl set-default multi-user.target
```

Likewise, you can determine what your system’s default boot target is with the following command:

```
$ systemctl get-default
graphical.target
```

Similar to systems adopting SysV, the default target should never point to `shutdown.target`, as it corresponds to the runlevel 0 (shutdown).

The configuration files associated with every unit can be found in the `/lib/systemd/system/` directory. The command `systemctl list-unit-files` lists all available units and shows if they are enabled to start when the system boots. The option `--type` will select only the units for a given type, as in `systemctl list-unit-files --type=service` and `systemctl list-unit-files --type=target`.

Active units or units that have been active during the current system session can be listed with command `systemctl list-units`. Like the `list-unit-files` option, the `systemctl list-units --type=service` command will select only units of type service and command `systemctl list-units --type=target` will select only units of type `target`.

systemd is also responsible for triggering and responding to power related events. The `systemctl suspend` command will put the system in low power mode, keeping current data in memory. Command `systemctl hibernate` will copy all memory data to disk, so the current state of the system can be recovered after powering it off. The actions associated with such events are defined in the file `/etc/systemd/logind.conf` or in separate files inside the directory `/etc/systemd/logind.conf.d/`. However, this systemd feature can only be used when there is no other power manager running in the system, like the `acpid` daemon. The `acpid` daemon is the main power manager for Linux and allows finer adjustments to the actions following power related events, like closing the laptop lid, low battery or battery charging levels.

### Upstart
The initialization scripts used by Upstart are located in the directory `/etc/init/`. System services can be listed with command `initctl list`, which also shows the current state of the services and, if available, their PID number.

```
# initctl list
avahi-cups-reload stop/waiting
avahi-daemon start/running, process 1123
mountall-net stop/waiting
mountnfs-bootclean.sh start/running
nmbd start/running, process 3085
passwd stop/waiting
rc stop/waiting
rsyslog start/running, process 1095
tty4 start/running, process 1761
udev start/running, process 1073
upstart-udev-bridge start/running, process 1066
console-setup stop/waiting
irqbalance start/running, process 1842
plymouth-log stop/waiting
smbd start/running, process 1457
tty5 start/running, process 1764
failsafe stop/waiting
```

Every Upstart action has its own independent command. For example, command `start` can be used to initiate a sixth virtual terminal:

```
# start tty6
The current state of a resource can be verified with command status:
```

```
# status tty6
tty6 start/running, process 3282
```

And the interruption of a service is done with the command `stop`:

```
# stop tty6
```


Upstart does not use the `/etc/inittab` file to define runlevels, but the legacy commands `runlevel` and `telinit` can still be used to verify and alternate between runlevels.

```
Note
Upstart was developed for the Ubuntu Linux distribution to help facilitate parallel startup of processes. Ubuntu has stopped using Upstart since 2015 when it switched from Upstart to systemd.
```

### Shutdown and Restart
A very traditional command used to shutdown or restart the system is unsurprisingly called `shutdown`. The shutdown command adds extra functions to the power off process: it automatically notifies all logged-in users with a warning message in their shell sessions and new logins are prevented. Command `shutdown` acts as an intermediary to SysV or systemd procedures, that is, it executes the requested action by calling the corresponding action in the services manager adopted by the system.

After `shutdown` is executed, all processes receive the `SIGTERM` signal, followed by the `SIGKILL` signal, then the system shuts down or changes its runlevel. By default, when neither options `-h` or `-r` are used, the system alternates to runlevel 1, that is, the single user mode. To change the default options for `shutdown`, the command should be executed with the following syntax:

```
$ shutdown [option] time [message]
```

Only the parameter time is required. The time parameter defines when the requested action will be executed, accepting the following formats:

`hh:mm`
- This format specifies the execution time as hour and minutes.

`+m`
- This format specifies how many minutes to wait before execution.

`now or +0`
- This format determines immediate execution.

The `message` parameter is the warning text sent to all terminal sessions of logged-in users.

The SysV implementation allows for the limiting of users that will be able to restart the machine by pressing `Ctrl`+`Alt`+`Del`. This is possible by placing option `-a` for the `shutdown` command present at the line regarding `ctrlaltdel` in the `/etc/inittab` file. By doing this, only users whose usernames are in the `/etc/shutdown.allow` file will be able to restart the system with the `Ctrl`+`Alt`+`Del` keystroke combination.

The `systemctl` command can also be used to turn off or to restart the machine in systems employing systemd. To restart the system, the command `systemctl reboot` should be used. To turn off the system, the command `systemctl poweroff` should be used. Both commands require root privileges to run, as ordinary users can not perform such procedures.

```
Note
Some Linux distributions will link poweroff and reboot to systemctl as individual commands. For example:

$ sudo which poweroff
/usr/sbin/poweroff
$ sudo ls -l /usr/sbin/poweroff
lrwxrwxrwx 1 root root 14 Aug 20 07:50 /usr/sbin/poweroff -> /bin/systemctl
```

Not all maintenance activities require the system to be turned off or restarted. However, when it is necessary to change the system’s state to single-user mode, it is important to warn logged-in users so that they are not harmed by an abrupt termination of their activities.

Similar to what the `shutdown` command does when powering off or restarting the system, the `wall` command is able to send a message to terminal sessions of all logged-in users. To do so, the system administrator only needs to provide a file or directly write the message as a parameter to command `wall`.





___
This documentation is provided by the Linux Professional Institute

[Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

[Get the Full PDF](https://learning.lpi.org/en/learning-materials/101-500/)