
# Windows Command Line 

## Basic System Information 


| command    | what it does                              |
| ---------- | ----------------------------------------- |
| set        | print environment variables               |
| ver        | Operating System version                  |
| systeminfo | Print full System information             |
| more       | pipe through it if the output is too long |
| help       | Help information about command            |
| cls        | clear screen                              |

Q: What is the OS version of the Windows VM ?
A: 10.0.20348.2655

Q: What is the hostname of the Windows VM ? 
A: WINSRV2022-CORE

## Network Troubleshooting 


| command                          | what it does                                    |
| -------------------------------- | ----------------------------------------------- |
| ipconfig                         | To check network information                    |
| ipconfig /all                    | For detailed information                        |
| ping                             | pinging                                         |
| tracert                          | for  traceroute scan                            |
| nslookup                         | returns ipaddress of domains                    |
| nslookup \[domain] \[nameserver] | To get IP address from specific nameserver      |
| netstat                          | current network connections and listening ports |
| netstat -h                       | For netstat help                                |

Q: Which command can we use to look up the server's physical address (MAC address) ? 
A: ipconfig /all

Q: What is the name of the service listening on port 135 ? 
A: RpcSs

Q: What is the name of the service listening on port 3389 ? 
A: TermService 

## File and Disk Management 


| command             | what it does                                                   |
| ------------------- | -------------------------------------------------------------- |
| cd                  | current directory without arguments                            |
| dir                 | List out directory                                             |
| dir /a              | Displays hidden and system files                               |
| dir /s              | displays files and directories including inside subdirectories |
| tree                | visually represent directory path                              |
| cd target_direcory  | To change directory to target_directory                        |
| cd ..               | go back one directory                                          |
| mkdir               | To create a directory                                          |
| rmdir               | To remove a directory                                          |
| type                | view text files                                                |
| copy                | To copy files from one location to another                     |
| move                | To move files from one location to another                     |
| del or erase        | To delete a file                                               |
| \* acts as wildcard |                                                                |

Q: What are the file's contents in C:\Treasure\Hunt ? 
A: THM{CLI_POWER}

## Task and Process Management 


| commands                             | what it does                                            |
| ------------------------------------ | ------------------------------------------------------- |
| tasklist                             | list running process with multiple filters available /? |
| tasklist /FI "imagenaem eq sshd.exe" | To only find with Image name sshd.exe                   |
| taskkill /PID target_pid             | To kill a process with PID                              |

Q: What command would you use to find the running processes related to notepad.exe
A: tasklist /FI "imagename eq notepad.exe"

Q: What command can you use to kill the process with PID 1516 ? 
A: taskkill /PID 1516

Q: The command shutdown /s can shutdown a system. What is the command you can use to restart a system ? 
A: shutdown /r

Q: What command can you use to abort a scheduled shutdown ?
A: shutdown /a

# Windows PowerShell

## What is PowerShell ? 

PowerShell is a tool used for task automation and configuration management. It is a scripting language built on the .NET framework. PowerShell is object-oriented

PowerShell objects encapsulate data and functionality.

Q: What do we call the advanced approach used to develop PowerShell ? 
A: object-oriented 

## PowerShell Basics

PowerShell commands are known as **cmdlets**
Cmdlets follows **Verb-Noun** naming convention 


| PowerShell commands    | What it does                        |
| ---------------------- | ----------------------------------- |
| **Get-Command**        | To see what command can be executed |
| **Get-Help \[cmdlet]** | Provides information about cmdlets  |
| **Get-Alias**          | Lists all available aliases         |
| **Find-Module**        | To Search for online repositories   |
| **Install-Module**     | To install online repositories      |

Q: How would you retrieve a list of commands that start with the verb Remove ? 
A: Get-Command -Name Remove*

Q: What cmdlet has its traditional counterpart echo as an alias ? 
A: Write-Output

Q: What is the command to retrieve some example usage for the cmdlet New-LocalUser ? 
A: Get-Help New-LocalUser -examples 

## Navigating File System 

| PowerShell Command                                                  | What it does                                                      |
| ------------------------------------------------------------------- | ----------------------------------------------------------------- |
| **Get-ChildItem -Path**                                             | Lists files and directories in Path, by default current directory |
| **Set-Location -Path**                                              | Change Directory                                                  |
| **New-Item -Path -ItemType "Directory"**                            | To create a new directory in path                                 |
| **New-Item -Path -ItemType**                                        | To create a new file in path                                      |
| **Remove-Item -Path**                                               | Removes both file and directory                                   |
| **Copy-Item -Path \path\to\file -Destination \path\to\destination** | Copies a file from path to destination                            |
| **Move-Item -Path \path\to\file -Destination \path\to\destination** | Moves a file from path to destination                             |
| **Get-Content -Path**                                               | Display contents of file                                          |

Q: What cmdlet can you use instead of the traditional Windows command type ? 
A: Get-Content 

Q: What PowerShell command would you use to display the content of the "C:\Users" directory ? 
A: Get-ChildItem -Path C:\Users

Q: How many items displayed by the command described in the previous question ? 
A: 4

## Piping, Filtering and Sorting Data

Piping used in CLI environments to use output of one command to be used as input for another. Using `|` symbol.

For a simple sorting example 

```PowerShell
Get-ChildItem | Sort-Object Length
```

This sorts the output length property

```PowerShell
Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"
```

To sort only files with extension .txt, **-eq** is the **equal to** operator 

other operators include : 

| operator  | meaning                  |
| --------- | ------------------------ |
| **-eq**   | equal to                 |
| **-ne**   | not equal                |
| **-gt**   | greater than             |
| **-ge**   | greater than or equal to |
| **-lt**   | less than                |
| **-le**   | less than or equal to    |
| **-like** | for pattern matching     |

```PowerShell
Get-ChildItem | Where-Object -Property "Name" -like "ship*"
```

To show only the details needed, Use **Select-Object**

```PowerShell 
Get-ChildItem | Select-Object Name,Length
```

**Select-String** is a cmdlet similar to grep in Linux and **findstr** in Windows :

```PowerShell
Select-String -Path ".\captain-hat.txt" -Pattern "hat"
```

**Select-String** supports regex as well 

Q: How would you retrieve the items in the current directory with the size greater than 100 ? 
A: Get-ChildItem | Where-Object -Property Length -gt 100

## System and Network Information 

| PowerShell commands        | What it does                                       |
| -------------------------- | -------------------------------------------------- |
| **Get-ComputerInfo**       | Prints the snapshot of Entire system configuration |
| **Get-LocalUser**          | Lists local user accounts on the system            |
| **Get-NetIPConfiguration** | Information about network interfaces on the system |
| **Get-NetIPAddress**       | show details of all IP addresses configured        |

Q: Other than your current user and the default "Administrator" account, what other user in enabled on the target machine ? 
A: p1r4t3

Q: This lad has hidden his account among the other with no regard for our beloved captain! What is the motto he has so bluntly put as his account's description ?
A: A merry life and a short one 

Q: Can you navigate the filesystem and find the hidden treasure inside the pirate's home ? 
A: THM{p34rlInAsh3ll}

## Real-Time System Analysis

| PowerShell Commands      | What it does                                 |
| ------------------------ | -------------------------------------------- |
| **Get-Process**          | Detailed view of currently running processes |
| **Get-Service**          | Info about status of services on the machine |
| **Get-NetTCPConnection** | Current TCP connections                      |
| **Get-FileHash**         | To generate file hash                        |

Q: What is the hash of the file that contains it ? 
A: 71FC5EC11C2497A32F8F08E61399687D90ABE6E204D2964DF589543A613F3E08

Q: What property retrieved by default by Get-NetTCPConnection contain information about the process that has started the connection 
A: OwningProcess

Q: With this information and the PowerShell knowledge you have built so far, can you find service name ? 
A: p1r4t3-s-compass

## Scripting 

Scripting is used to automate tasks, 

**Invoke-Command** Let's you perform commands on other devices making remote management easy to do 

**Example :** 

```PowerShell
Invoke-Command -FilePath C:\scripts\test.ps1 -ComputerName Server01
```

this will execute the script in the Server01 computer, Also **-ScriptBlock** can be used to inject script directly instead of a script file 

Q: What is the syntax to execute the command **Get-Service** on a remote computer named "RoyalFortune"

A: Invoke-Command -ComputerName RoyalFortune -ScriptBlock { Get-Service }

# Linux Shells 


