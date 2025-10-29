---
tags: #windows #linux #cli #cmd #powershell #bash #scripting #fundamentals
related:
- "[[Windows Fundamentals.md]]"
- "[[Linux Fundamentals.md]]"
---

# Windows Command Line 
See also: [[Windows Fundamentals.md]]

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
| * acts as wildcard |                                                                |

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
See also: [[Windows Fundamentals.md]]

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
See also: [[Linux Fundamentals.md]]

Q: What is the facilitator between the user and the OS ? 
A: Shell

## How to interact with shell ? 

| commands | What it does              |
| -------- | ------------------------- |
| **pwd**  | Print Working DIrectory   |
| **cd**   | Change Directory          |
| **ls**   | List out                  |
| **cat**  | concatenate to read files |
| **grep** | To find a string in file  |


Q: What is the default shell in most linux distributions ? 
A: Bash

Q: What command utility is used to list down the contents of a directory ? 
A: ls

Q: Which command utility can help you search for anything in a file ? 
A: grep 

## Types of Linux Shells 


There are multiple shells available, To see which shell currently used : 

```bash
echo $SHELL
```

You can list down the available shells by 

```bash
cat /etc/shells
```

To switch shell just type the name of shell 

```bash
zsh
```


### Bourne Again Shell (Bash)

- Shell with scripting capabilities 
- Tab completion 
- history of previous commands 

### Friendly Interactive Shell (Fish)

- simple syntax
- auto spell correction 
- customizable shell themes 
- Also provides scripting, tab completion, command history like bash

### Z Shell

- Advanced tab completion with scripting 
- Auto spell correction for commands 
- Offers extensive customization
- Command history and other functionalities 

| Feature             | Bash                                                                                                                           | Fish                                                                                     | Zsh                                                                                                                       |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Full Name           | The full form of Bash is Bourne Again Shell.                                                                                   | The full form of Fish is Friendly Interactive Shell.                                     | The full form of Zsh is Z Shell.                                                                                          |
| Scripting           | It offers widely compatible scripting with extensive documentation available.                                                  | It has limited scripting features as compared to the other two shells.                   | It offers an excellent level of scripting, combining the traditional capabilities of Bash shell with some extra features. |
| Tab completion      | It has a basic tab completion feature.                                                                                         | It offers advanced tab completion by giving suggestions based on your previous commands. | Its tab completion capability can be extended heavily by using plugins.                                                   |
| Customization       | Basic level of customization.                                                                                                  | It offers some good customization through interactive tools.                             | Advanced customization through oh-my-zsh framework.                                                                       |
| User friendliness   | It is less user-friendly, but being a traditional and widely used shell, its users are quite familiar and comfortable with it. | It is the most user-friendly shell.                                                      | It can be highly user-friendly with proper customization.                                                                 |
| Syntax highlighting | The syntax highlighting feature is not available in this shell.                                                                | The syntax highlighting is built-in to this shell.                                       | The syntax highlighting can be used with this shell by introducing some plugins.                                          |

Q: Which shell comes with syntax highlighting out-of-the-box feature ? 
A: Fish

Q: Which shell does not have auto spell correction ? 
A: Bash 

Q: Which command displays all the previously executed commands of the current session ? 
A: history 

## Shell Scripting 

A shell script is a set of commands. Used to automate tasks by putting the all the commands in script and running it once.

The extension of the script much be named with extension **.sh** , which is default for bash scripts 

Every script starts with **shebang**

```bash
#!/bin/bash
```

### Variables 

A variable stores a value inside it, You can read values using **read** 

```bash
#!/bin/bash
echo "Hey, what's your name ?"
read name 
echo "Welcome, $name"
```

Run the script by making it executable 

```bash
chmod +x ./first_script.sh
```

Run the script by : 

```bash
./first-script.sh
```

### Loops 

Loops are used to code repeating things. example loop script : 

```bash
#!/bin/bash
for i in {1..10};
do 
echo $i
done 
```

### Conditional Statements 

Conditional scripts are used to branch code based on If-else conditions 

Example Script  : 

```bash
#!/bin/bash
echo "Please enter your name first : "
read name 
if [ "$name" = "Stewart" ]; then 
	echo "Welcome Stewart! Here is the secret: THM_Script"
else
	echo "Sorry! You are not authorized to access the secret."
fi
```

### Comments 

**#** is used to comment in bash script 

Q: What is the shebang used in Bash script ? 
A: #/bin/bash

Q: Which command gives executable permissions to a script ? 
A: chmod +x

Q: Which scripting functionality helps us configure iterative tasks ? 
A: loops 

## Locker Script 

```bash
#!/bin/bash 

username=""
company=""
pin=""

for i in {1..3}; do 
	if [ $i -eq 1 ]; then 
		echo "Enter your username :"
		read username 
	elif [ $i -eq 2 ]; then 
		echo "Enter your company name :"
		read company
	else
		echo "Enter your PIN :"
		read pin
	fi
done 

if [ "$username" = "John"] && [ "$company" = "TryHackMe" ] && [ "$pin" = "7385" ]; then 
	echo "Authentication Sucessful"
else
	echo "Authentication Denied"
fi 
```

Q: What would be the correct PIN to authenticate in the locker script ?
A: 7385

### Practical Exercise 

Q: which file has the keyword 
A: authentication.log

Q: Where is cat sleeping ? 
A: Under the table