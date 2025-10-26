
# Networking Concepts 

## OSI Model 

**OSI (Open Systems Interconnection)** is developed by ISO, defines framework for network communication. It is a theoretical model 

OSI model has seven layers : 

1. Physical Layer
2. Data Link Layer
3. Network Layer 
4. Transport Layer 
5. Session Layer
6. Presentation Layer
7. Application Layer 


### Layer 1 : Physical Layer 

Deals with **physical connection** between devices. such as wire, electrical, optical, wireless signal, Wi-Fi radio bands like 2.4GHz band, 5GHz band, 6GHz band 


### Layer 2: Data Link Layer 

**Layer 2** represent the protocol that enables data transfer between nodes on the same network segment.

A **network segment** refers to a group of devices using a shared medium 

**Examples:** Ethernet (802.3), Wi-Fi (802.11) 

Ethernet and WiFi addresses 6 bytes called **MAC address**, with hexadecimal format with a colon separating. The three leftmost bytes identify the vendor.

**Ex:** ***a4:c3:f0***:85:ac:2d

The packet up to layer 2 is called **Frame**

### Layer 3: Network Layer 

**Network Layer** is for sending data between different networks. It handles **logical addressing** and **routing**. (for finding a path)

**Example** : Internet Protocol (IP), Internet Control Message Protocol (ICMP), Virtual Private Network (VPN), IPSec, SSL/TLS

### Layer 4: Transport Layer 

**Transport Layer** handles end-to-end communication between applications on different hosts. Which does **flow control**, **Segmentation**, and error **correction**.

**Example:**  Transmission Control Protocol (TCP), User Datagram Protocol

### Layer 5: Session Layer 

The session layer is **establishing**, **maintaining**, and **synchronizing** communication between applications no different hosts. 

**Examples:** Network File System (NFS) and Remote Procedure Call (RPC).

### Layer 6: Presentation Layer 

The Presentation layer handles data **encoding**, **compression**, and **encryption**

**Example** : MIME(Multipurpose Internet Mail Extension)

### Layer 7: Application Layer

The application layer provides services directly to end-user applications

**Example** : HTTP, FTP, DNS, POP3, SMTP, IMAP

### Summary 

|Layer Number|Layer Name|Main Function|Example Protocols and Standards|
|---|---|---|---|
|Layer 7|Application layer|Providing services and interfaces to applications|HTTP, FTP, DNS, POP3, SMTP, IMAP|
|Layer 6|Presentation layer|Data encoding, encryption, and compression|Unicode, MIME, JPEG, PNG, MPEG|
|Layer 5|Session layer|Establishing, maintaining, and synchronising sessions|NFS, RPC|
|Layer 4|Transport layer|End-to-end communication and data segmentation|UDP, TCP|
|Layer 3|Network layer|Logical addressing and routing between networks|IP, ICMP, IPSec|
|Layer 2|Data link layer|Reliable data transfer between adjacent nodes|Ethernet (802.3), WiFi (802.11)|
|Layer 1|Physical layer|Physical data transmission media|Electrical, optical, and wireless signals|

Q: Which layer is responsible for end-to-end communication between running applications ? 
A: 4

Q: Which layer is responsible for routing packets to the proper network 
A: 3

Q: In the OSI model, which layer is responsible for encoding the application data ? 
A: 6

Q: Which layer is responsible for transferring data between hosts on the same network segment ? 
A: 2

## TCP / IP Model 

TCP/IP Model is an **implemented model**, developed by **Department of Defense (DoD)** in **1970s**. 

Has 5 Layers : 

- **Application Layer**  - {session, presentation, Application}
- **Transport Layer** 
- **Internet Layer**
- **Link Layer** 

|Layer Number|ISO OSI Model|TCP/IP Model (RFC 1122)|Protocols|
|---|---|---|---|
|7|Application Layer|Application Layer|HTTP, HTTPS, FTP, POP3, SMTP, IMAP, Telnet, SSH,|
|6|Presentation Layer|
|5|Session Layer|
|4|Transport Layer|Transport Layer|TCP, UDP|
|3|Network Layer|Internet Layer|IP, ICMP, IPSec|
|2|Data Link Layer|Link Layer|Ethernet 802.3, WiFi 802.11|
|1|Physical Layer|||

Q: To which layer does HTTP belong in the TCP/IP model ? 
A: Application Layer

Q: How many layers of the OSI model does the application layer in the TCP/IP model cover ?
A: 3

## IP Addresses and Subnets 

IP is the **unique identifier** for each device is connected to the network. There is **IPv4** and **IPv6**

IP address comprises of **4 octets**, 32bits. **8 bits** an octet ranging from **0-255**

![[5f04259cf9bf5b57aed2c476-1719849005781.png]]

0 and 255 are reserved for the **network** and **broadcast** addresses, ***192.168.1.0*** is network address ***192.168.1.255*** is a the broadcast address. Sending to broadcast address will target all hosts on the network. 2^32 IP addresses available 

Subnet mask of **255.255.255.0** can be written as **/24**. This means the 24bits are 1 on the ip address and do not change across the network. 

### Private Addresses 

Two types of IP addresses : 

- **Public IP addresses**
- **Private IP addresses**

Private IP address ranges: 

- **10.0.0.0** - **10.255.255.255** (**10/8**)
- **172.16.0.0** - **172.31.255.255** (**172.16/12**)
- **192.168.0.0** - **192.168.255.255** (**192.168/16**)

**Private IP addresses** are used for internal network where **Public IP address** used in external network  or public network 

### Routing

A router **forwards data packets** to the proper network. The router functions at **layer 3**. 

Q: Which of the following IP addresses is not a private IP address ? 

- 192.168.250.125
- 10.20.141.132
- 49.69.147.197
- 172.23.182.251

A: 49.69.147.197

Q: Which of the following IP addresses is not a valid IP address ?
- 192.168.250.15
- 192.168.254.17
- 192.168.305.19
- 192.168.199.13

A: 192.168.305.19










