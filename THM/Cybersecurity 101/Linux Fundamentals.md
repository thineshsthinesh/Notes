
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

### The man Page 

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




