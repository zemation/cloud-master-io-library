# CompTIA A+ 220-1103

## 1.0 Operating Systems
### 1.1 Identify basic features of Microsoft Windows editions
#### Windows 10 editions
- Home
- Pro
- Pro for Workstations
- Enterprise

#### Feature differences
- Domain access vs workgroup
- Desktop styles/user interface
- Availability for Remote Desktop Protocol (RDP)
- Random-access memory (RAM) support limitations
- BitLocker
- gpedit.msc

#### Upgrade paths
- in-place upgrade

### 1.2 Given a scenario, use the appropriate Microsoft command-line tool.
#### Navigation
- cd
- dir
- md
- rmdir
- Drive navigation inputs:
  - C: or D: or x:
#### Command-line tools
- ipconfig
- ping
- hostname
- netstat
- nslookup
- chkdsk
- net user
- net use
- tracert
- format
- xcopy
- copy
- robocopy
- gpupdate
- gpresult
- shutdown
- sfc 
- [command name] /?
- diskpart
- pathping
- winver

### 1.3 Given a scenario, use features and tools of the Microsoft Windows 10 operating system (OS)
#### Task Manager
- Services
- Startup
- Performance
- Processes
- Users

#### Microsoft Management Console (MMC) snap-in
- Event Viewer (eventvwr.msc)
- Disk Management (diskmgmt.msc)
- Task Scheduler (taskchd.msc)
- Device Manager (devmgmt.msc)
- Certificate Manager (certmgr.msc)
- Local Users and Groups (lusrmgr.msc)
- Performance Monitor (perfmon.msc)
- Group Policy Editor (gpedit.msc)

#### Additional Tools
- System Information (msinfo32.exe)
- Resource Monitor (resmon.exe)
- System configuration (msconfig.exe)
- Disk Cleanup (cleanmgr.exe)
- Disk Defragment (dfrgui.exe)
- Registry Editor (regedit.exe)

### 1.4 given a scenario, use the appropriate Microsoft Windows 10 Control Panel utility
#### Internet Options
#### Devices and Printers
#### Programs and Features
#### Network and Sharing Center
#### System
#### Windows Defender Firewall
#### Mail
#### Sound
#### User Accounts
#### Device Manager
#### Indexing Options
#### Administrative Tools
#### File Explorer Options
- Show hidden files
- Hide Extensions
- General options
- View options
#### Power Options
- Hibernate
- Power plans
- Sleep/suspend
- Standby
- choose what closing the lid does
- Turn on fast startup
- Universal Serial Bus (USB) selective suspend
#### Ease of Access

### 1.5 Given a scenario, us the appropriate Windows settings
#### Time and Language
#### Update and Security
#### Personalization
#### Apps
#### Privacy
#### System
#### Devices
#### Network and Internet
#### Gaming
#### Accounts

### 1.6 fiven a scenario, configure Microsoft Windows networking features on a client/desktop
#### Workgroups vs domain setup
- Shared resources
- Printers
- File Servers
- Mapped drives
#### Local OS firewall settings
- Application restrictions and exceptions
- Configuration
#### Client network configuration
- Internet Protocol (IP) addressing scheme
- Domain Name System (DNS) settings
- Subnet mask
- Gateway
- Static vs dynamic
#### Establish network connections
- Virtual private network (VPN)
- Wireless 
- Wired
- Wireless wide area network (WWAN)
#### Proxy settings
#### Public network vs. private network
#### File Explorer navigation - network paths
#### Metered connections and limitations

### 1.7 Given a scenario, apply application installation and configuration concepts
#### System requirements for applications
- 32-bit vs 64-bit dependent application requirements 
- Dedicated graphics card vs integrated
- Video random-access memory (VRAM) requirements
- RAM requirements
- Central processing unit (CPU) requirements
- External hardware tokens
- Storage requirements
#### OS requirements for applications
- Application to OS compatibility
- 32-bit vs. 64-bit OS
#### Distribution methods
- Physical media vs downloadable
- ISO mountable
#### Other considerations for new applications
- Impact to device
- Impact to network
- Impact to operation
- Impact to business

### 1.8 Explain common OS types and their purposes
#### Workstation OSs
- Windows
- Linux
- macOS
- Chrome OS
#### Cell phone/tablet OSs
- iPadOS
- iOS
- Android
#### Various filesystem types
- New Technology File System
- File Allocation Table 32 (FAT32)
- Third extended filesystem (ext3)
- Fourth extended filesystem (ext4)
- Apple File System (APFS)
- Extensible File Allocation Table (exFAT)
#### Vendor life-cycle limitations
- End-of-life (EOL)
- Update limitations
#### Compatibility concerns between OSs

### 1.9 Given a scenario, perform OS installations and upgrades in a diverse OS environment
#### Boot methods
- USB
- Optical Media
- Network
- Solid-state/flash drives
- Internet-based
- External/hot-swappable drive
- Internal hard drive (partition)
#### Types of installations
- Upgrade
- Recovery partition
- Clean install
- Image deployment
- Repair installation
- Remote network installation
- Other considerations
  - Third-party drivers
#### Partitioning
- GUID [globally unique identifier] Partition Table (GPT)
- Master boot record (MBR)
#### Drive format
#### Upgrade considerations
- Backup files and user preferences
- Application and driver support / backward compatibility
- Hardware compatibility
#### Feature updates
- Product lifecycle

### 1.10 Identify common features and tools of the macOS/desktop OS
#### Installation and uninstallation of applications
- File types
  - .dmg
  - .pkg
  - .app
- App Store
- Uninstallation process
#### Apple ID and corporate restrictions
#### Best practices
- Backups
- Antivirus
- Updates/patches
#### System Preferences
- Displays
- Networks
- Printers
- Scanners
- Privacy
- Accessibility
- Time Machine
#### Features
- Multiple desktops
- Mission Control
- Keychain
- Spotlight
- iCloud
- Gestures
- Finder
- Remote Disc
- Dock
#### Disk Utility
#### FileVault
#### Terminal
#### Force Quit

### 1.11 Identify common features and tools of the Linux client/desktop OS
#### Common commands
- ls
- pwd
- mv
- cp
- rm
- chmod
- chown
- su/sudo
- apt-get
- yum
- ip
- df
- grep
- ps
- man
- top
- find
- dig
- cat
- nano

#### Best practices
- Backups
- Antivirus
- Updates/patches
#### Tools
- Shell/terminal
- Samba

## 2.0 Security
### 2.1 Summarize various security measures and their purposes
#### Physical security
- Access control vestibule
- Badge reader
- Video surveillance
- Alarm systems
- Motion sensors
- Door locks
- Equipment locks
- Guards
- Bollards
- Fences
#### Physical security for staff
- Key fobs
- Smart cards
- Keys
- Biometrics
  - Retina scanner
  - Fingerprint scanner
  - Palmprint scanner
- Lighting
- Magnetometers
#### Logical security
- Principle of least privilege
- Access control lists (ACLs)
- Multifactor authentication (MFA)
- Email
- Hard token
- Soft token
- Short message service (SMS)
- Voice call
- Authenticator aplication
#### Mobile device management (MDM)
#### Active Directory
- Login script
- Domain
- Group Policy/updates
- Organizational units
- Home folder
- Folder redirection
- Security Groups

### 2.2 Compare and contrast wireless security protocols and authentication methods
#### Protocols and encryption
- WiFi Protected Access 2 (WPA2)
- WPA3
- Temporal Key Integrity Protocol (TKIP)
- Advanced Encryption Standard (AES)
#### Authentication 
- Remote Authentication Dial-In User Service (RADIUS)
- Terminal Access Controller Access-Control System (TACACS+)
- Kerberos
- Multifactor

### 2.3 Given a scenario, detect, remove, and prevent malware using the appropriate tools and methods
#### Malware
- Trojan
- Rootkit
- Virus
- Spyware
- Ransomware
- Keylogger
- Boot sector virus
- Cryptominers
#### Tools and methods
- Recovery mode
- Antivirus
- Anti-malware
- Software firewalls
- Anti-phishing trainging
- User education regarding common threats
- OS reinstallation

### 2.4 Explain common social-engineering attacks, threats, and vulnerabilities
#### Social engineering
- Phishing 
- Vishing
- Shoulder surfing
- Whaling
- Tailgating
- Impersonation
- Dumpster Diving
- Evil twin

#### Threads
- Distributed denial of service (DDoS)
- Denial of service (DoS)
- Zero-day attack
- Spoofing
- On-path attack
- Brute-force attack
- Dictionary attack
- Insider threat
- Structured Query Language (SQL) injection
- Cross-site scripting (XSS)

#### Vulnerabilities
- Non-compliant systems
- Unpatched systems 
- Unprotected systems (missing antivirus/missing firewall)
- EOL OSs
- Bring your own device (BYOD)

### 2.5 Given a scenario, manage and configure basic security settings in the Microsoft Windows OS
#### Defender Antivirus
- Activate/deactivate
- Updated definitions
#### Firewall
- Activate/deactivate
- Port security
- Application security
#### Users and groups
- Local vs Microsoft account
- Standard account
- Administrator
- Guest user
- Power user
#### Login OS options
- Username and password
- Personal identification number (PIN)
- Fingerprint
- Facial recognition
- Single sign-on (SSO)
#### NTFS vs share permissions
- File and folder attributes
- Inheritance
#### Run as administrator vs standard user 
- User Account Control (UAC)
#### BitLocker
#### BitLocker To Go
#### Encrypting File System (EFS)

### 2.6 Given a scenario, configure a workstation to meet best practices for security
#### Data-at-rest encryption
#### Password best practices
- Complexity requirements
  - Length
  - Character Types
- Expiration requirements
- Basic input/output system (BIOS)/Unified Extensible Firmware Interface (UEFI) passwords
#### End-user best practices
- Use screensaver locks
- Log off when not in use
- Secure/protect critical hardware (e.g. laptops)
- Secure personally identifiable information (PII) and passwords
#### Account Management
- Restrict user permissions
- Restrict login times
- Disable gues account
- Use failed attempts lockout
- Use timeout/screen lock
#### Change default administrator's user account/password
#### Disable AutoRun
#### Disable AutoPlay

### 2.7 Explain common methods for securing mobile and embedded devices
#### Screen locks
- Facial recognition
- PIN codes
- Fingerprint
- Pattern
- Swipe
#### Remote wipes
#### Locator applications
#### OS updates
#### Device encryption
#### Remote backup applications
#### Failed login attempts restrictions
#### Antivirus/anti-malware
#### Firewalls
#### Polcies and procedures
- BYOD vs corporate owned
- Profile security requirements
#### Internet of Things (IoT)


### 2.8 Given a scenario, use common data destruction and disposal methoids
#### Physical destruction
- Drilling
- Shredding
- Degaussing
- Incinerating
#### Recycling or repurposing best practices
- Erasing/wiping
- Low-level formatting
- Standard formatting
#### Outsourcing concepts
- Third-party vendor
- Certification of destruction/recycling

### 2.9 Given a scenario, configure appropriate security settings on small office/home office (SOHO) wireless and wired networks
#### Home router settings
- Change default passwords
- IP filtering
- Firmware updates
- Content filtering
- Physical placement/secure locations
- Dynamic Host Configuration Protocol (DHCP) reservations
- Static wide-area network (WAN) IP
- Universal Plug and Play (UPnP)
- Screened subnet
#### Wireless specific
- Changing the service set identifier (SSID)
- Disabling SSID broadcast
- Encryption software
- Disabling guess access
- Changing channels
#### Firewall settings
- Disabling unused ports
- Port forwarding/mapping

### 2.10 Given a scenario, install and configure browsers and relevant security settings
#### Browser download/installation
- Trusted sources
  - Hashing
- Untrusted sources
#### Extensions and plug-ins
- Trusted sources
- Untrusted sources
#### Password managers
#### Secure connections/sites - valid certificates
#### Settings
- Pop-up blocker
- Clearing browsing data
- Clearing cache
- Private- browsing mode
- Sign-in/browser data syncrhonization
- Ad blockers

## 3.0 Software Troubleshooting
### 3.1 Given a scenario, troubleshoot common Windows OS problems
#### Common symptoms
- Blue screen of death (BSOD)
- Sluggish performance
- Boot problems
- Frequent shutdowns
- Services not starting
- Applications crashing
- Low memory warnings
- USB controller resource warnings
- System instability
- No OS found
- Slow profile load
- Time drift
#### Common troubleshooting steps
- Reboot
- Restart services
- Uninstall/reinstall/update applications
- Add resources
- Verify requirements
- System file check
- Repair Windows
- Restore
- Reimage
- Roll back updates
- Rebuild Windows profiles

### 3.2 Given a scenario, troubleshoot common personal computer (PC) security issues
#### Common symptoms
- Unable to access the network
- Desktop alerts
- False alerts regarding antivirus protection
- Altered system or personal files
  - Missing/renamed files
- Unwanted notifications within the OS
- OS update failures
#### Browser-related symptoms
- Random/frequent pop-ups
- Certificate warnings
- Redirection

### 3.3 Given a scenario, use best practice procedures for malware removal
#### Investigate and verify malware symptoms
#### Quarantine infected systems
#### Disable System Restore in Windows
#### Remediate infected systems
- a. Update anti-malware software
- b. Scanning and removal techniques (e.g. safe mode, preinstallation environment)
#### Schedule scans and run updates
#### Enable System Restore and create a restore point in Windows
#### Education the end-user

### 3.4 Given a scenario, troubleshoot common mobile OS and application issues
#### Common symptoms
- Application fails to launch
- Application failes to close/crashes
- Application fails to update
- Slow to respond
- OS fails to update
- Battery life issues
- Randomly reboots
- Connectivity issues
  - Bluetooth
  - Wi-Fi
  - Near-field communication (NFC)
  - Airdrop
- Screen does not autorotate

### 3.5 Given a scenario, troubleshoot common mobile OS and application security issues
#### Security concerns
- Android package (APK) source
- Developer mode
- Root access/jailbreak
- Bootleg/malicious applicaion
  - Application spoofing
#### Common symptoms
- High network traffic
- Sluggish response time
- Data-usage limit notification
- Limited internet connectivity
- No internet connectivity
- High number of ads
- Fake security warnings
- Unexpected browser behavior
- Leaked personal files/data


## 4.0 Operational Procedures
### 4.1 Given a scenario, implement best practices associated with documentation and support systems information management
#### Ticketing system
- User information
- Device information
- Description of problems
- Categories
- Severity
- Escalation levels
- Clear, concise written communication
  - Problem description
  - Progress notes
  - Problem resolution
#### Asset management
- Inventory lists
- Database system
- Asset tags and IDs
- Procurement life cycle
- Warranty and licensing
- Assigned users
#### Types of documents
- Acceptable use policy (AUP)
- Network topology diagram
- Regulatory compliance requirements
  - Splash Screens
- Incident eports
- Standard operating procedures
  - Procedures for custom installation of software packages
- New-user setup checklist
- end-user termination checklist
#### Knowledge base/articles

### 4.2 Explain basic change-management best practices
#### Documented business procedures
- Rollback plan
- Sandbox testing
- Responsible staff member
#### Change management
- Request forms
- Purpose of the change
- Scope of the change
- Date and time of the change
- Affected systems/impact
- Risk analysis
  - Risk level
- Change board approvals
- End-user acceptance

### 4.3 Given a scenario, implement workstation backup and recovery methods.
#### Backup and recovery
- Full 
- Incremental
- Differential
- Synthetic
#### Backup testing
- Frequency
#### Backup rotation schemes
- On-site vs off site
- Grandfather-father-son (GFS)
- 3-2-1 backup rule

### 4.4 Given a scenario, use common safety procedures
#### Electrostatic discharge (ESD) straps
#### ESD mats
#### Equipment grounding
#### Proper power handling
#### Proper component handling and storage
#### Antistatic bags
#### Compiance with government regulations
#### Personal safety
- Disconnect power before repairing PC
- Lifting techniques
- Electrical fire safety
- Safety googles
- Air filtration mask

### 4.5 Summarize environmental impacts and local environmental controls
#### Material safety data sheet (MSDS)/documentation for handling and disposal
- Proper battery disposal
- Proper toner disposal
- Proper disposal of other devices and assets
#### Temperature, humidity-level awareness and proper ventilation
- Location/equipment placement
- Dust cleanup
- Compressed air/vacuums
#### Power surges, under-voltage events and power failures
- Battery backup
- Surge Suppressor

### 4.6 Explain the importance of prohibited content/activity and privacy, licensing and policy concepts
#### Incident responses
- Chain of custody
- Inform management/law enforcement as necessary
- Copy of drive (data integrity and preservation)
- Documentation of incident
#### Licensing/digital rights management (DRM)/end-user license agreement (EULA)
- Valid licenses
- Non-expired licenses
- Personal use license vs corporate use license
- Open-source licenses
#### Regulated data
- Credit-card transactions
- Personal government-issued information
- PII
- Healthcare data
- Data retention requirements

### 4.7 Given a scenario, use proper communication techniques and professionalism
#### Professional appearnce and attire
- Match the required attire of the given envirnment
  - Formal
  - Business casual
#### Use proper language and avoid jargon, ancronyms and slang when applicable
#### Maintain a positive attitude/project confidence
#### Actively listen, take notes, and avoid interrupting the customer
#### Be culturally sensitive
- Use appropriate professional titles, when applicable
#### Be on time (if late, contact the customer)
#### Avoid distractions
- Personal calls
- Texting/social media sites
- Personal interruptions 
#### Deailing with difficult customers or situations
- Do not argue with customers or be defensive
- Avoid dismissing customer problems
- Avoid being judgemental
- Clarify customer statements (ask open-ended questions to narrow the scope of the problem, restate the issue, or question to verify everything)
- Do not disclose experience via social media outlets
#### Set and meet expectations/time line and communicate status with the customer 
- Offer repair/replacement options, as needed
- Provide proper documentation on the services provided
- Follow up with customer/user at a later date to verify satisfaction
#### Deal appropriately with customers' confidential and private materials
- Located on a computer, desktop, printer, etc

### 4.8 Identify the basics of scripting
#### Script file types
- .bat
- .ps1
- .vbs
- .sh
- .js
- .py
#### Use cases for scripting
- Basic automation
- Restarting machines
- Remapping network drives
- Installation of applications
- Automated backups
- Gathering of information/data
- Initiating updates
#### Other considerations when using scripts
- Unintentionally introducing malware
- Inadvertently changing system settings
- Browser or system crashes due to mishandling of resources

### 4.9 Given a scenario, use remote access technologies
#### Methods/tools
- RDP
- VPN
- Virtual network computer (VNC)
- Secure Shell (SSH)
- Remote monitoring and management (RMM)
- Microsoft Remote Assistance (MSRA)
- Third-party tools
  - Screen-sharing software
  - Video-conferencing software
  - File transfer software
  - Desktop management software
#### Security considerations of each access method


## CompTIA A+ Core 2 (220-1102) Acronym List
The following is a list of acronyms that appear on the CompTIA A+
Core 2 (220-1102) exam. Candidates are encouraged to review the complete
list and attain a working knowledge of all listed acronyms as part of a
comprehensive exam preparation program.
| Acronym | Definition |
| --- | --- |
| AAA | Authentication, Authorization, and Accounting
| AC | Alternating Current
| ACL | Access Control List
| ADF | Automatic Document Feeder
| AES | Advanced Encryption Standard
| AP | Access Point
| APFS | Apple File System
| APIPA | Automatic Private Internet Protocol Addressing
| APK | Android Package
| ARM | Advanced RISC [Reduced Instruction Set Computer] Machine |
| ARP | Address Resolution Protocol |
| ATA | Advanced Technology Attachment |
| ATM | Asynchronous Transfer Mode
| ATX | Advanced Technology Extended
| AUP | Acceptable Use Policy
| BIOS | Basic Input/Output System
| BSOD | Blue Screen of Death
| BYOD | Bring Your Own Device
| CAPTCHA | Completely Automated Public Turing Test to Tell Computers and Humans Apart
| CD | Compact Disc
| CDFS | Compact Disc File System
| CDMA | Code-Division Multiple Access
| CERT | Computer Emergency Response Team
| CIFS | Common Internet File System
| CMD | Command Prompt
| CMOS | Complementary Metal-Oxide Semiconductor
| CPU | Central Processing Unit
| CRL | Certificate Revocation List
| DC | Direct Current
| DDoS | Distributed Denial of Service
| DDR | Double Data Rate
| DHCP | Dynamic Host Configuration Protocol
| DIMM | Dual Inline Memory Module
| DKIM | DomainKeys Identified Mail
| DMA | Direct Memory Access
| DMARC | Domain-based Message Authentication,Reporting, and Conformance
| DNS | Domain Name System
| DoS | Denial of Service
| DRAM | Dynamic Random-Access Memory
| DRM | Digital Rights Management
| DSL | Digital Subscriber Line
| DVI | Digital Visual Interface
| DVI-D | Digital Visual Interface-Digital
| ECC | Error Correcting Code
| EFS | Encrypting File System
| EMI | Electromagnetic Interference
| EOL | End-of-Life
| eSATA | External Serial Advanced Technology Attachment
| ESD | Electrostatic Discharge
| EULA | End-User License Agreement
| exFAT | Extensible File Allocation Table
| ext | Extended File System
| FAT | File Allocation Table
| FAT12 | 12-bit File Allocation Table
| FAT16 | 16-bit File Allocation Table
| FAT32 | 32-bit File Allocation Table
| FSB | Front-Side Bus
| FTP | File Transfer Protocol
| GFS | Grandfather-Father-Son
| GPS | Global Positioning System
| GPT | GUID [Globally Unique Identifier] Partition Table
| GPU | Graphics Processing Unit
| GSM | Global System for Mobile Communications
| GUI | Graphical User Interface
| GUID | Globally Unique Identifier
| HAL | Hardware Abstraction Layer
| HAV | Hardware-assisted Virtualization
| HCL | Hardware Compatibility List
| HDCP | High-bandwidth Digital Content Protection
| HDD | Hard Disk Drive
| HDMI | High-Definition Multimedia Interface
| HSM | Hardware Security Module
| HTML | Hypertext Markup Language
| HTTP | Hypertext Transfer Protocol
| HTTPS | Hypertext Transfer Protocol Secure
| I/O | Input/Output
| IaaS | Infrastructure as a Service
| ICR | Intelligent Character Recognition
| IDE | Integrated Drive Electronics
| IDS | Intrusion Detection System
| IEEE | Institute of Electrical and Electronics Engineers
| IMAP | Internet Mail Access Protocol
| IOPS | Input/Output Operations Per Second
| IoT | Internet of Things
| IP | Internet Protocol
| IPS | Intrusion Prevention System
| IPS | In-plane Switching
| IPSec | Internet Protocol Security
| IR | Infrared
| IrDA | Infrared Data Association
| IRP | Incident Response Plan
| ISO | International Organization for Standardization
| ISP | Internet Service Provider
| ITX | Information Technology eXtended
| KB | Knowledge Base
| KVM | Keyboard-Video-Mouse
| LAN | Local Area Network
| LC | Lucent Connector
| LCD | Liquid Crystal Display
| LDAP | Lightweight Directory Access Protocol
| LED | Light-emitting Diode
| MAC | Media Access Control/Mandatory Access Control
| MAM | Mobile Application Management
| MAN | Metropolitan Area Network
| MBR | Master Boot Record
| MDM | Mobile Device Management
| MFA | Multifactor Authentication
| MFD | Multifunction Device
| MFP | Multifunction Printer
| MMC | Microsoft Management Console
| MOU | Memorandum of Understanding
| MSDS | Material Safety Data Sheet
| MSRA | Microsoft Remote Assistance
| MX | Mail Exchange
| NAC | Network Access Control
| NAT | Network Address Translation
| NDA | Non-disclosure Agreement
| NetBIOS | Networked Basic Input/Output System
| NetBT | NetBIOS over TCP/IP [Transmission Control Protocol/Internet Protocol]
| NFC | Near-field Communication
| NFS | Network File System
| NIC | Network Interface Card
| NTFS | New Technology File System
| NVMe | Non-volatile Memory Express
| OCR | Optical Character Recognition
| OLED | Organic Light-emitting Diode
| ONT | Optical Network Terminal
| OS | Operating System
| PaaS | Platform as a Service
| PAN | Personal Area Network
| PC | Personal Computer
| PCIe | Peripheral Component Interconnect Express
| PCL | Printer Command Language
| PE | Preinstallation Environment
| PII | Personally Identifiable Information
| PIN | Personal Identification Number
| PKI | Public Key Infrastructure
| PoE | Power over Ethernet
| POP3 | Post Office Protocol 3
| POST | Power-on Self-Test
| PPP | Point-to-Point Protocol
| PRL | Preferred Roaming List
| PSU | Power Supply Unit
| PXE | Preboot Execution Environment
| RADIUS | Remote Authentication Dial-in User Service
| RAID | Redundant Array of Independent (orInexpensive) Disks
| RAM | Random-access Memory
| RDP | Remote Desktop Protocol
| RF | Radio Frequency
| RFI | Radio Frequency Interference
| RFID | Radio Frequency Identification
| RJ11 | Registered Jack Function 11
| RJ45 | Registered Jack Function 45
| RMM | Remote Monitoring and Management
| RTO | Recovery Time Objective
| SaaS | Software as a Service
| SAN | Storage Area Network
| SAS | Serial Attached SCSI [Small Computer System Interface]
| SATA | Serial Advanced Technology Attachment
| SC | Subscriber Connector
| SCADA | Supervisory Control and Data Acquisition
| SCP | Secure Copy Protection
| SCSI | Small Computer System Interface
| SDN | Software-defined Networking
| SFTP | Secure File Transfer Protocol
| SIM | Subscriber Identity Module
| SIMM | Single Inline Memory Module
| S.M.A.R.T. | Self-monitoring Analysis and Reporting Technology
| SMB | Server Message Block
| SMS | Short Message Service
| SMTP | Simple Mail Transfer Protocol
| SNMP | Simple Network Management Protocol
| SNTP | Simple Network Time Protocol
| SODIMM | Small Outline Dual Inline Memory Module
| SOHO | Small Office/Home Office
| SPF | Sender Policy Framework
| SQL | Structured Query Language
| SRAM | Static Random-access Memory
| SSD | Solid-State Drive
| SSH | Secure Shell
| SSID | Service Set Identifier
| SSL | Secure Sockets Layer
| SSO | Single Sign-on
| ST | Straight Tip
| STP | Shielded Twisted Pair
| TACACS | Terminal Access Controller Access-Control System
| TCP | Transmission Control Protocol
| TCP/IP | Transmission Control Protocol/Internet Protocol
| TFTP | Trivial File Transfer Protocol
| TKIP | Temporal Key Integrity Protocol
| TLS | Transport Layer Security
| TN | Twisted Nematic
| TPM | Trusted Platform Module
| UAC | User Account Control
| UDP | User Datagram Protocol
| UEFI | Unified Extensible Firmware Interface
| UNC | Universal Naming Convention
| UPnP | Universal Plug and Play
| UPS | Uninterruptible Power Supply
| USB | Universal Serial Bus
| UTM | Unified Threat Management
| UTP | Unshielded Twisted Pair
| VA | Vertical Alignment
| VDI | Virtual Desktop Infrastructure
| VGA | Video Graphics Array
| VLAN | Virtual LAN [Local Area Network]
| VM | Virtual Machine
| VNC | Virtual Network Computer
| VoIP | Voice over Internet Protocol
| VPN | Virtual Private Network
| VRAM | Video Random-access Memory
| WAN | Wide Area Network
| WEP | Wired Equivalent Privacy
| WISP | Wireless Internet Service Provider
| WLAN | Wireless LAN [Local Area Network]
| WMN | Wireless Mesh Network
| WPA | WiFi Protected Access
| WWAN | Wireless Wide Area Network
| XSS | Cross-site Scripting




## CompTIA A+ Core 2 (220-1102)
### Proposed Hardware and Software List
**CompTIA has included this sample list of hardware and software to
assist candidates as they prepare for the A+ Core 2 (220-1102) exam. This
list may also be helpful for training companies that wish to create a lab
component to their training offering. The bulleted lists below each topic
are sample lists and are not exhaustive.
#### Equipment
- Apple tablet/smartphone
- Android tablet/smartphone
- Windows tablet
- Chromebook
- Windows laptop/Mac laptop/Linux laptop
- Windows desktop/Mac desktop/ Linux desktop
-  Windows server with Active Directory and Print Management
- Monitors
- Projectors
- SOHO router/switch
- Access point
- Voice over Internet Protocol (VoIP)phone
- Printer
  - Laser/inkjet
  - Wireless
  - 3-D printer
  - Thermal
- Surge suppressor
- Uninterruptible power supply (UPS)
- Smart devices (Internet of Things [IoT] devices)
- Server with a hypervisor
- Punchdown block
- Patch panel
- Webcams
- Speakers
- Microphones
#### Spare parts/hardware
- Motherboards
- RAM
- Hard drives
- Power supplies
- Video cards
- Sound cards
- Network cards
- Wireless network interface cards (NICs)
- Fans/cooling devices/heat sink
- CPUs
- Assorted connectors/cables
  - USB
  - High-Definition Multimedia Interface (HDMI)
  - DisplayPort
  - Digital visual interface (DVI)
  - Video graphics array (VGA)
- Adapters
  - Bluetooth adapter
- Network cables
- Unterminated network cable/connectors
- Alternating current (AC) adapters
- Optical drives
- Screws/standoffs
- Cases
- Maintenance kit
- Mice/keyboards
- Keyboard-video-mouse (KVM)
- Console cable
- Solid-state drive (SSD)
#### Tools
- Screwdriver
- Multimeter
- Wire cutters
- Punchdown tool
- Crimper
- Power supply tester
- Cable stripper
- Standard technician toolkit
- Electrostatic discharge (ESD) strap
- Thermal paste
- Cable tester
- Cable toner
- WiFi analyzer
- Serial advanced technology attachment (SATA) to USB connectors
#### Software
- OSs
  - Linux
  - Chrome OS
  - Microsoft Windows
  - macOS
  - Android
  - iOS
- Preinstallation environment (PE) disk/live compact disc (CD)
-  Antivirus software
- Virtualization software
-  Anti-malware
- Driver software


## CompTIA A+ 1102 Practice Questions
### Question 1
Which of the following symptoms is MOST likely a sign of ransomware?

- A. Internet connectivity is lost.
- B. Battery life is reduced.
- C. Files on devices are inaccessible.
- D. A large number of ads appear.

### Question 2
A technician has been directed to dispose of hard drives from company laptops properly. Company standards require the use of a high-powered magnet to destroy data on decommissioned hard drives. Which of the following data destruction methods should the technician choose?

- A. Degaussing
- B. Drilling
- C. Incinerating
- D. Shredding

### Question 3
A user reports being unable to access the Internet or use wireless headphones on a mobile device. The technician confirms the headphones properly connect to another device. Which of the following should the technician do to solve the issue?

- A. Turn off airplane mode.
- B. Connect to a different service set identifier.
- C. Test the battery on the device.
- D. Disable near-field communication.

### Question 4
A sales staff member recently left a laptop at a hotel and needs a new one immediately. After remotely wiping the old laptop, a support technician prepares to take a new laptop out of inventory to begin the deployment process. Which of the following should the technician do FIRST?

- A. Recycle all the cardboard and other shipping materials appropriately.
- B. Call the hotel and demand the old laptop be sent back to the repair depot.
- C. Confirm the shipping address for the new laptop with the sales staff member.
- D. Document the serial numbers and usernames for asset management.

### Question 5
A technician is installing M.2 devices in several workstations. Which of the following would be required when installing the devices?

- A. Air filtration
- B. Heat-resistant gloves
- C. Ergonomic floor mats
- D. Electrostatic discharge straps

### Question 6
A user's Windows desktop continuously crashes during boot. A technician runs the following command in safe mode and then reboots the desktop:

c:\Windows\system32> sfc /scannow
Which of the following BEST describes why the technician ran this command?

- A. The user's profile is damaged.
- B. The system files are corrupted.
- C. The hard drive needs to be defragmented.
- D. The system needs to have a restore performed.

### Question 7
A user calls the IT help desk and explains that all the data on the user's computer is encrypted. The user also indicates that a pop-up message on the screen is asking for payment in Bitcoins to unlock the encrypted data. The user's computer is MOST likely infected with which of the following?

- A. Botnet
- B. Spyware
- C. Ransomware
- D. Rootkit

### Question 8
A network engineer needs to update a network firewall, which will cause a temporary outage. The network engineer submits a change request form to perform the required maintenance. If the firewall update fails, which of the following is the NEXT step?

- A. Perform a risk analysis.
- B. Execute a backout plan.
- C. Request a change approval.
- D. Acquire end user acceptance.

### Question 9
Which of the following workstation operating systems uses NTFS for the standard filesystem type?

- A. macOS
- B. Windows
- C. Chrome OS
- D. Linux

### Question 10
Which of the following Linux commands will display a directory of files?

- A. chown
- B. ls
- C. chmod
- D. cls

## A+ 1102 Answer Key
Question 1) C. Files on devices are inaccessible.

Question 2) A. Degaussing

Question 3) A. Turn off airplane mode.

Question 4) D. Document the serial numbers and usernames for asset management.

Question 5) D. Electrostatic discharge straps

Question 6) B. The system files are corrupted.

Question 7) C. Ransomware

Question 8) B. Execute a backout plan.

Question 9) B. Windows

Question 10) B. ls