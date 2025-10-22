
# Linux Fundamentals Part 1

## Linux Background

"Linux" is the term for multiple OS's that are based on UNIX, Linux is open source, so there are multiple variants of linux available 

Q: Research: What year was the first release of a Linux operating system ? 
A: 1991

## Linux Commands 

echo - output text 
whoami - current logged in user 
ls - list 
cd - change directory
cat - concatenate
pwd - print working directory
find - find files and directories by -name -type etc 
wc - word count 
grep - extract words or patterns 


Q: If we wanted to output the text "TryHackMe", what would our command be ?
A: echo TryHackMe

Q: What is the username of who you're logged in as on your deployed Linux machine ? 
A: tryhackme 

Q: On the Linux machine that you deploy, how many folders are there ? 
A: 4

Q: Which directory contains a file ?
A: folder4

Q: What is the contents of this file ?
A: Hello World 

Q: Use the cd command to navigate to this file and find out the new working directory. What is the path ? 
A: /home/tryhackme/folder4

Q: Use grep on "access.log" to find the flag that has a prefix of "THM". What is the flag ? 
A: THM{ACCESS}

## Shell Operators 

Operator - Description 
&           -        background command
&&        -        combine multiple commands 
\>           -        Redirect to file or input for other 
\>>         -        appends rather than overwriting 

Q: If we wanted to run a command in the background, what operator would we want to use ? 
A: &

Q: If I wanted to replace the contents of a file named "passwords" with the word "password123", what would my command be ? 
A: echo password123 > passwords

Q: Now if I wanted to add "tryhackme" to this file named "passwords " but also keep "passwords123", what would my command be  ?
A: echo tryhackme >> passwords 

# Linus Fundamentals Part 2

## SSH

Secure Shell or SSH is a protocol that allows execute commands on other devices remotes it's a encrypted protocol 

**ssh login syntax :** 

ssh \<username>@\<IP>   , example : ssh tryhackme@10.201.73.15


## Flags and Switches 

Lot of commands allow arguments to be supplied these arguments are called flags are switches they are a keyword followed by hyphen

**Ex:** ls -a (--all)

with **--help** option you can view the flags supported by a command 

## The man Page 

The man(ual) page is the documentation for the commands in linux, which are available on machine as well as online 

To access man page : **man \<command>** 

Q: What directional arrow key would we use to navigate down the manual page ? 
A: down 

Q: What flag would we use to display the output in a "human-readable" way ? 
A: -h 

## File System Interaction 



| Command | Full Name      | Purpose                 |
| ------- | -------------- | ----------------------- |
| touch   | touch          | create file             |
| mkdir   | make directory | create a folder         |
| cp      | copy           | copy a file or folder   |
| mv      | move           | move a file or folder   |
| rm      | remove         | remove a file or folder |
| file    | file           | type of file            |

Q: How would you create a file named "newnote" ? 
A: touch newnote

Q: On the deployable machine, what is the file type of "unknown1" in tryhackme's home directory ?
A: ASCII text 

Q: How would we move the file "myfile" to the directory "myfolder"
A: mv myfile myfolder

Q: What is the contents of this file ? 
A: THM{FILESYSTEM}

## Permissions 101 

Every file in linux has three action permissions : 

- Read
- Write 
- Execute 

These three permissions are then divided into 3 category of users 

- file owner 
- file owner group
- others 

### switching users 

We can switch to other users on the system using **su** command though we need the password of that user to do so 

Q; On the deployable machine, who is the owner of "important" ?
A: user2

Q: What would the command be to switch to the user "user2" ?
A: su user2

Q: Output the contents of "important", what is the flag ? 
A: THM{SU_USER2}

## Common Directories 

### /etc 

The /etc folder is a location to store system files that are used by the operating system 

### /var

The /var directory stores data that is frequently accessed or written by services like log files (/var/log), or data that is not associated with any specific user 

### /root 

The /root directory is the home for the **root** system user. 

### /tmp

The /tmp directory is volatile and temporary folder that is cleared out when restarted 


Q: What is the directory path that would we expect logs to be stored in ? 
A: /var/log

Q: What root directory is simiilar to how RAM on a computer works ? 
A: /tmp 

Q: Name the home directory of the root user 
A: /root

# Linux Fundamentals Part 3

## Terminal Text Editors 

### Nano 

To create or edit a file using nano, simply use **nano filename**
nano has a few features 
- Searching for text (Ctrl + W)
- Copying and Pasting (Ctrl + K for cut Ctrl + U for paste)
- Jumping to a line number (Ctrl + _ )
- See number of line you are on (Alt + 6)
- Exit (Ctrl + X)

### VIM

It's a advanced text editor with lot of features 
- Customizable 
- Syntax highlighting
- VIM works on almost all terminals
- Lot of tutorials and cheatsheet available 

Q: Edit "task3" in "tryhackme"'s home directory using Nano. What's the flag ? 
A: THM{TEXT_EDITORS}

## General / Useful Utilities 

### Downloading files (wget)

wget command let's you download files from web via HTTP, to download a file from web 

```bash
wget http://path-to-file/file
```


### Transferring files from your host - SCP

Secure copy or SCP, is used to securely copy files from the host to remote system suing the SSH protocol 

To send a file : 
```bash 
scp file <usernmae>@<IP>:/path-to-send/
```

To receive a file : 
```bash
scp <username>@<IP>:/path-to-file/ file
```

### Serving files from your host - WEB 

You can serve files on your machine using a **HTTP SERVER** , This is a python module that let's you host HTTP Server on your local host 

Other systems in the same network can get those files using **curl** or **wget**

To start a server :
```bash
python3 -m http.server 
```

To get a file from server : 
```bash
wget http://<IP>:<PORT>/Path-to-file
```

Q: What are the contents ? 
A: THM{WGET_WEBSERVER}


## Processes 101

processes are programs running on the machine, They are managed by the kernel, each process has a PID in the order the process starts 

### Viewing Processes

**ps** command can be used to get a list of running processes on the user's session it includes, 

- PID
- Session that running the process
- CPU time used
- Command that is being executed 

**ps aux** command can be used to see system processes and processes that are run by other users 

**top** command give live statistics of running processes which refreshes every ten seconds 

### Managing Processes 

You can terminate a process using **kill** command followed by PID 
**Ex: kill 1337**

We can also send signals when killing processes using **-s** flag  :

- SIGTERM - Kill the process, but allow it do cleanup beforehand
- SIGKILL - Kill the process, doesn't do any cleanup
- SIGSTOP - Stop/suspend a process 

#### Starting Processes/Services

The Process ID 0 is the process that starts when the system boots. This is the system's **init**
Once the system boots **systemd** is on of the first processses that are started. Any process of software that we want to start will be a child process of systemd
**systemctl** - This command allows to interact with systemd process

**systemctl syntax:** 
```bash 
systemctl [option] [service]
```

systemctl has 4 options: 

- start - Start the service
- stop - Stop the service 
- enable - start on the boot-up of system 
- disable - remove from boot-up of system 

#### Backgrounding and Foregrounding in Linux 

Processes can run in two states : background and foreground 

**&** operator is used to background a process by just appending it (or)
You can also background a running process by pressing **Ctrl+Z** which will pause the process then we can use **bg** to background that process, **fg** foreground that process 


Q: If we were to launch a process where the previous ID was "300", what would be the ID of this new process be ? 
A: 301

Q: If we wanted to cleanly kill a process, what signal would we send it ? 
A: SIGTERM 

Q: Locate the process that is running on the deployed instance (MACHINE_IP). What is flag is given ? 
A: THM{PROCESSES}

Q: What command would we use to stop the service "myservice" ?
A: systemctl stop myservice

Q: What command would we use to start the same service on the boot-up of the system ?
A: systemctl enable myservice 

Q: what command would we use to bring a previously backgrounded process to back to the foreground?
A: fg 

## Automation 

**cronjobs** are used to schedule a certain action or task to perform after system boot 
**crontab** is the process that is responsible for managing cronjobs

**crontab** is a special file that is recognised by cron processes to execute 

**crontab** requires 6  values : 

- MIN - What minute to execute at 
- HOUR - What hour to execute at 
- DOM - What day of the month 
- MON  - What month of the year 
- DOW - what day of the week 
- CMD - The command to execute at 

**Format example :**

```
0 */12 * * * cp file /direcotry
```

\* is used to specify wildcard for the values

Q: When will the  crontab on the deployed instance (MACHINE_IP) run ?
A: @reboot

## Package 

developers submit their softwares to the "apt" repository. If approved they are available for everyone to use 

You can add custom repositories using **add-apt-repository** command 

Also you can use **dpkg** to install from package installers 

You can remove package or repository by using **add-apt-repository --remove :ppa:PPA_Name/ppa**  (or) apt remvoe \[software-name]

## System logs 

As previously mentioned logs are stored in the **/var/log** directory, where logs of multiple services are present, we can investigate these logs

Q: What is the IP address of the user who visited the site ?
A: 10.9.232.111

Q: What file did they access ? 
A: catsanddogs.jpg 


