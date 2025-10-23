
# Windows Fundamentals Part 1
## Desktop GUI 

It's the GUI that welcomes you once you were in a windows machine (windows 10)

![[Pasted image 20251022181622.png]]


1. The Desktop 
2. Start Menu
3. Search Box
4. Task View
5. Taskbar
6. Toolbars
7. Notification Area 

### The Desktop 

- The desktop contains shortcuts to programs some files and folders for quick access. If they are placed in the desktop directory 
- Right clicking on the desktop will open a popup window which has copy, paste, arrange options also create new files and folders 
- The Display setting on the popup allows you change screen resolution and orientation 
- Personalize settings allows you to change background image, themes, color scheme etc.

### The Start Menu 

- The start menu provides access to all the apps, programs, files and utility tools
- The start menu has 3 sections
	- The left most section has quick shortcut actions you can perform with the login session, like making changes to user account locking screen, Changing settings, Opening user documents etc 
	- The middle section shows recently added apps and programs followed by all the apps and programs 
	- The right side has apps and programs that are pinned to the start menu for quick access 

### The taskbar 

Any apps / programs that you open/start will appear in the taskbar. Hovering over these icon will provide you with preview along with tooltip

### The Notification Area 

Notification area which is at the bottom right corner this where the time, date, volume and other icons as well as notifications show up, You can edit what icons should be visible by specifying them in the taskbar settings 

Q: Which selection will hide/disable the Search box ? 
A: Hidden 

Q: Which selection will hide/disable the Task View button ? 
A: Show Task View button 

Q: Besides Clock and Network, what other icons are visible in the notification area 
A: Action Center 


## The File System 

- The file system used in windows is New Technology File System (NTFS)
- NTFS is known as journaling file system. In case of failure file system can automatically repair itself using the information on the log file 
- NTFS Improvements : 
	- Supports files larger than 4GB
	- Set specific permissions on folders and files 
	- Folder and file compression
	- Encrytion 
 - On NTFS volumes, you can grant or deny permissions to files and folders. The permissions are :
	 - Full control
	 - Modify
	 - Read & Execute
	 - List folder contents
	 - Read
	 - Write 
	![[ntfs-permissions1.png]]
	- To view the permissions for a file :
		- Right click the file or folder you want to check for permission 
		- select Properties -> security 
		- All the permissions for user and groups are available there 
- Another feature of NTFS is Alternative Data Streams (ADS) - ADS allows files to contain more than one stream of data 

Q: What is the meaning of NTFS ? 
A: New Technology File System 

### The windows\system32 folders 

- The windows folder C:\Windows is knows as folder which contains windows operating system 
- The system environment variable for the windows directory is `%windir%`
- One of the main folder inside windows is `system32` that are critical for operating system 
- Accidently changing anything in the system32 folder can affect the windows OS 

Q: What is the system variable for the windows folder ? 
A: %windir%

## User accounts 

- There are two types of user accounts on local windows system : **Administrator** & **Standard User**
- An administrator can make changes to the system: add users, delete users, modify groups, modify settings on the system etc.
- A standard user can only make changes to folder/files attributed to the user & can't perform system-level changes, such as install programs 

- You can create new user if you are a administrator by going to **system settings > other users**  and using add someone else to the pc you can add new user 
- When a user account is created, a profile is created for the user user on **C:\Users**
- Each user profile will have the same folders : 
	- Desktop 
	- Documents
	- Downloads
	- Music
	- Pictures
- You can open Local User and Group Management to see detailed information about the users and groups by going to RUN -> lusrmgr.msc

Q: What is the name of the other user account ? 
A: tryhackmebilly

Q: What group is this user a member of ?
A: Remote Desktop Users, Users 

Q: What built-in account is for guest access to the computer ? 
A: Guest 

Q: What is the account description ? 
A: window$Fun1!

## User Account Control 

User account control is in place to protect user accounts, when a software  wants to run with elevated privileges it will prompt the user for permit 

If the user is a standard user it will prompt for administrator password to go on with the action 


Q: What does UAC mean ? 
A: user Account Control 


## Settings and Control Panel 

![[win-settings.png]]

![[win-control-panel.png]]


Q: In the control panel, change the view to small icons, what is the last setting in the control panel view 
A: Windows Defender Firewall

## Task Manger 

The task manager provides information about the application and processes currently running on the system. Also how much CPU and RAM being utilized by each process 

**Right Click on task bar** -> **Task Manger**

![[win-task-manager3.png]]


Q: What is the keyboard shortcut to open task manager ? 
A: Ctrl + Shift + Esc

# Windows Fundamentals 2

## System Configuration 

System Configuration utility (MSConfig) is used to diagnose startup issues. 

The utility has five tabs : 

- General 
- Boot
- Services
- Startup 
- Tools 

### General 

![[msconfig1.png]]

In the general tab we can select what devices and services should load on upon boot. The options are: Normal, Diagnostic, Selective 

### Boot 

![[msconfig2.png]]

We can define various boot options for the operating system 

### Services 

![[msconfig3.png]]

Services tab lists all services configured for the system. A service is a special type of application that runs in the background 

### Startup 
![[msconfig4.png]]

It suggest to use task manger to enable/disable startup items 

### Tools 

![[msconfig5.png]]

Here is a list of various utilities, we can use to configure the operating system further 

Q: What is the name of the service that lists systems internals as the manufacturer ? 
A: PsShutdown

Q: Whom is the Windows license registered to ? 
A: Windows User 

Q: What is the command for Windows Troubleshooting ? 
A: C:\Windows\System32\control.exe /name Microsoft.Troubleshooting 

Q: What command will open the Control Panel ? 
A: contorl.exe 

## Change UAC Settings 

The UAC settings can be changed or even turned off entirely 

![[uac.png]]

You can adjust the slider based on need 

Q: What is the command to open User Account Control Settings?  
A: UserAccountControlSettings.exe 

## Computer Management 

The computer management (compmgmt) utility has three sections: System Tools, Storage, Services and Applications 

![[compmgmt1.png]]


### System Tools 

#### Task Scheduler : 

With Task scheduler we can create and manage common tasks that computer will automatically perform at the specified time 

To create a basic task, click on **Create Basic Task** under **Actions**

#### Event Viewer : 

Event viewer allows to view events that occurred on the computer. These records of events can be used to diagnose problems and investigate actions executed on the system 

![[five-event-types.png]]

![[standard-event-logs.png]]

#### Shared Folders 

Shared folder is where complete list of shares and folders others can connect to 

![[shared-folders.png]]

#### Local Users and Groups 

It's the same utility from before lusrmgr.msc, which shows information about users and groups on the system 

#### Performance 


Performance, There is a utility called Performance Monitor (perfmon). `perfmon` is used to see performance data 

![[perfmon.png]]

#### Device manager 

Device manager allows you to configure the hardware 

![[device-mgr.png]]


### Storage 

Under storage there is Disk Management 

#### Disk Management 

![[disk-mgmt.png]]

Disk management is a system utility in windows that enables you perform storage tasks : 

- Set up a new drive 
- Extend a partition 
- Shrink a partition 
- Assign or change a drive letter 


### Services and Applications

![[services-apps.png]]

Here you can enable and disable a service,  view the properties for the service 

WMI Control configures and controls the windows management instrumentation (WMI) (it is depreciated in latest versions )

Q: What is the command to open Computer Management ? 
A: compmgmt.msc

Q: At what time every day is the GoogleUpdateTaskMachineUA task configured to run ? 
A: 6:15 AM 

Q: What is the name of the hidden folder that is shared ? 
A: sh4r3dF0Ld3r

## System Information Tool 


System information **msinfo32** tool, this tool gathers information about your computer and displays a comprehensive view for hardware, system components, software environments 

The information in system summary is divided into three section : 

- Hardware Resources 
- Components 
- Software Environments 

Also the search option at the bottom is very useful 

Q: What is the command to open system information ? 
A: msinfo32.exe

Q: What is listed under System Name ?
A: THM-WINFUN2

Q: Under Environment Variables, what is the value for ComSpec ?
A: %SystemRoot%\System32\cmd.exe 

## Resource Monitor 

Resource Monitor **resmon** tool, displays per process CPU, Memory, disk and network usage information 

![[resmon1.png]]

Resomon has four section : 

- CPU 
- DIsk
- Network 
- Memory


Q: What is the command to open Resource Monitor ? 
A: resmon.exe 

## Command Prompt 

The command prompt  **cmd**, allows a CLI interface to interact with the system let's list out few commands : 

hostname - computer name 
whoami  - current logged in user 
ipconfig - Network address settings
cls - To clear screen 
/? - To retrieve help manual for a command 

commands can also run with parameters like -a -b -e or flags like \a \b \c 

Q: In System Configuration, what is the full command for Internet Protocol Configuration ? 
A: C:\Windows\System32\cmd.exe /k %windir%\system32\ipconfig.exe 

Q: For the ipconfig command, how do you show detailed information ? 
A: ipconfig /all

## Registry Editor 

The Windows Registry is a central hierarchical database that store information necessary to configure the system for one or more users, applications, and hardware devices 

The registry contains information that windows references during operation such as : 

- Profiles for each user 
- Applications installed on the computer and the type of documents each can create 
- Property sheet settings for folders and application icons 
- What hardware exists on the system 
- The ports that are being used 

You can view or edit registry using Registry Editor **regedit**

Q: What is the command to open the Registry Editor ? 
A: regedit32.exe


# Windows Fundamentals Part 3

## Windows update 

Windows Update is a service to provide security updates and patches for the windows operating system and other Microsoft products 

Updates are typically released on 2nd Tuesday of the month. This day is called Patch Tuesday. 

Q: There were two definition updates installed in the attached VM. On what date were these updates installed ? 
A: 5/3/2021

## Windows Security 

Our main focus is on Protection Areas: 

- Virus & threat protection
- Firewall & network protection
- App & browser control 
- Device security 

![[windows-security2.png]]

Has three status icons : 

- **Green** : means your device is sufficiently protected
- **Yellow** : means there is a safety recommendation 
- **Red** : Means a warning that needs immediate attention

Q: Checking the Security section on your VM, which area needs immediate attention ?
A: Virus & threat protection 

### Virus and Threat protection 

Virus and threat protection is divided into two parts: 

- Current threats
- Virus & threat protection settings 

#### Current threats 

![[windows-security3.png]]

##### Scan Options 

- **Quick Scan** - Checks the common folders where threats are found 
- **Full Scan** - Checks all files and running programs on your hard disk 
- **Custom scan** - Choose which files and locations you want to check 

##### Threat History 

- **Last scan** - Windows Defender automatically scans 
- **Quarantined threats**  - Threats that has been isolated and prevented from running and they will be periodically removed 
- **Allowed threats** - Identified threats that are allowed to run on your device 

#### Virus & Threat Protection settings 

##### Manage settings 

- **Real-time protection** - Locates and stops malware from installing or running on your device 
- **Cloud-delivered protection** - Provides increased protection with access to latest protection data in the cloud 
- **Automatic sample submission** - Send sample files to Microsoft
- **Controlled Folder Access** - Protect files, folders and memory areas from unauthorized changes
- **Exclusions** - Windows Defender Antivirus won't scan excluded items 
- **Notification** - Windows Defender will send notification with information about security of your device 

You can perform on-demand scans on any file / folder by right-clicking and selecting `Scan with Microsoft Defender`

Q: Specifically, what is turned off that Windows is notifying you to turn on ? 
A: Real-time protection 

### Firewall & network protection 

	A firewall is what controls what is - and more importantly isn't allowed to pass through the ports 

![[windows-firewall.png]]


- **Domain** - The domain profile applies to networks where the host can authenticate to a domain controller 
- **Private** - User assigned profile and used to designate private or home networks 
- **Public** - Default is public profile, used to designate public networks 

Every firewall profile has two options : **turn the firewall on / off** and **block all incoming connections**

Command to open windows firewall is `WF.msc`

![[windows-firewall4.png]]

We can use this to configure what firewall rule applies to what applications 


Q: If you were connected to airport Wi-Fi, what most likely be the active firewall profile ? 
A: Public network 

### App & browser control 

![[windows-app-control.png]]


#### Check apps and files 

- Windows Defender SmartScreen helps protect your device for unrecognized apps and files from the web 

#### Exploit protection 

- Helps protect against attacks 
	![[windows-exploit-protection.png]]

### Device Security 

Security that comes built into your device 

![[windows-device-sec.png]]

![[windows-mem-integrity2.png]]

![[windows-sec-processor.png]]

![[windows-tpm.png]]

What is Trusted Platform Module (TPM) 

	It is designed to provide hardware-based, security related functions.A TPM chip is a secure crypto processor 

Q: What is TPM ? 
A: Trusted Platform Module 

### BitLocker 

BitLocker Drive Encryption is a data protection feature that integrates with the operating system and encrypts disks on the OS level 

Q: We should use a removable drive on systems without a TPM version 1.2 or later. What does this removable drive contain ? 
A: startup key 


### Volume Shadow Copy Service 

Volume Shadow Copy Service performs actions to create a consistent snapshot of the data that is to be backed up 

Volume Shadow copies are stored on the System Volume Information folder on each drive 

If VSS is enabled, you can perform following within advanced system settings 

- Create a  restore point
- Perform system restore 
- Configure restore settings 
- Delete restore points 

Right-click on volume drive and select `Configure shadow copies` to edit shadow copies settings for that volume 

Q: What is VSS ? 
A: Volume Shadow Copy Service 

Further reading material:

- [Antimalware Scan Interface](https://docs.microsoft.com/en-us/windows/win32/amsi/antimalware-scan-interface-portal)[](https://docs.microsoft.com/en-us/windows/win32/amsi/antimalware-scan-interface-portal)
- [Credential Guard](https://docs.microsoft.com/en-us/windows/security/identity-protection/credential-guard/credential-guard-manage)[](https://docs.microsoft.com/en-us/windows/security/identity-protection/credential-guard/credential-guard-manage)
- [Windows 10 Hello](https://support.microsoft.com/en-us/windows/learn-about-windows-hello-and-set-it-up-dae28983-8242-bb2a-d3d1-87c9d265a5f0#:~:text=Windows%2010,in%20with%20just%20your%20PIN.)
- [CSO Online - The best new Windows 10 security features](https://www.csoonline.com/article/3253899/the-best-new-windows-10-security-features.html)

**Note**: Attackers use built-in Windows tools and utilities in an attempt to go undetected within the victim environment.  This tactic is known as Living Off The Land. Refer to the following resource [here](https://lolbas-project.github.io/) to learn more about this.



