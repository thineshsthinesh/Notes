
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

ssh <username>@<IP>   , example : ssh tryhackme@10.201.73.15

