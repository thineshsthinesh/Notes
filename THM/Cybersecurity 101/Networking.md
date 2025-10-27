
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


## UDP and TCP 

### UDP

UDP (User Datagram Protocol) is a connectionless transport layer protocol, **IP address** is used to identify hosts, , but to target specific process on the host **port number** is used that ranges from **1 to 65535**. UDP doesn't provide **confirmation**

### TCP 

TCP (Transmission Control Protocol), is a connection-oriented transport protocol. 

TCP establishes a **connection** before data can be sent. Each data octet has **sequence numbers**, used to identify lost or duplicated packets. The receiver acknowledges with an **acknowledgement number** 

TCP connection established using **three-way-handshake**: 

1. **SYN packet** : Client initiates by sending initial sequence number SYN packet to client 
2. **SYN-ACK Packet** : The server responds with initial sequence number of it's own with a SYN-ACK packet 
3. **ACK packet**: Client sends back the ACK packet 

![[5f04259cf9bf5b57aed2c476-1719849036216.svg]]


Q: Which protocol required three-way handshake ? 
A: TCP 

Q: What is the approximate number of port numbers (in thousands) ? 
A: 65

## Encapsulation 

**Encapsulation** refers to every layer adding a **header** or **trailer** to the data and sending the **encapsulated unit** to the layer below

- **Application data** : application format it's data and sends it 
- **Transport protocol segment or datagram** : The transport layer adds **TCP header** (**segment**) or **UDP header** (**datagram**) and sends it 
- **Network Packet** : Adds **IP header**, IP **packet** is sent 
- **Data link frame** : The Ethernet or Wi-Fi adds **header** and **trailer**, creating **frame**


![[5f04259cf9bf5b57aed2c476-1719849061418.svg]]


Q: On a Wi-Fi, within what will an IP packet be encapsulated ? 
A: Frame 

Q: What do you call the UDP data unit that encapsulates the application data ? 
A: Datagram 

Q: What do you call the data unit that encapsulates the application data sent over TCP 
A: Segment 


## Telnet 

The TELNET (Teletype Network) is a protocol used for remote administration 

Q: Use telnet to connect the web server on MACHINE_IP. What is the name and version of the HTTP server ? 
A: lighttpd/1.4.63

Q: What flag did you get when you viewed the page ? 
A: THM{TELNET_MASTER}

# Network Essentials 

## DHCP 

DHCP is an application-level protocol that relies on **UDP** used to automatically configure network. **DHCP Server** listens on **port 67** and client sends from **port 68**

DHCP follows 4 steps: 

![[5f04259cf9bf5b57aed2c476-1719849114115.svg]]

1. **DHCP Discover** : Client broadcasts DHCPDISCOVER seeking DHCP server 
2. **DHCP Offer**: Server responds with DHCPOFFER with available IP address 
3. **DHCP Request**: Client sends DHCPREQUEST to indicate it accepts IP address 
4. **DHCP Acknowledge**: Server responds DHCPACK to confirm IP is assigned 

![[5f04259cf9bf5b57aed2c476-1719849148646.svg]]


**DHCP Server** provides the following : 

- The IP address
- The gateway to route 
- DNS server 


Q: How many steps does DHCP use to provide network configuration ? 
A: 4

Q: What is the destination IP address that a client uses when it sends a DHCP Discovery packet ? 
A: 255.255.255.255

Q: What is the source IP address a client uses when trying to get IP network configuration over DHCP ? 
A: 0.0.0.0

## ARP Bridging 

For two devices on the same Ethernet to communicate it requires each other's MAC addresses 

The Ethernet frame header contains : 

- **Destination MAC address**
- **Source MAC address**
- **Type (IPv4)**

**Address Resolution Protocol (ARP)** is used to map IP address to MAC address by sending a **ARP Request** and receiving **ARP Replay**

**ARP** is **layer 2** that allows translation from **layer 3** to **layer 2** 

Q: What is the destination MAC address used in an ARP Request ? 
A: ff:ff:ff:ff:ff:ff

Q: In the example above, what is the MAC address of 192.168.66.1 ?
A: 44:df:65:d8:fe:6c

## ICMP 

**Internet Control Message Protocol (ICMP)** is mainly used for **network diagnostics** and error **reporting**.

- **Ping** uses ICMP to test **connectivity** and measures **Round-Trip-Time (RTT)**
- **traceroute** uses ICMP to find **route** from **host to target** 

Q: Using the example images above, how many bytes were sent in the echo (ping) request ? 
A: 

Q: Which IP header filed does the traceroute command require to become zero ? 
A: TTL

## Routing 

Routing Protocols : 

- **OSPF(Open Shortest Path First)** : Routing protocol that allows to routers to share info about network topology to find best routes 
- **EIGRP (Enhanced Interior Gateway Routing Protocol)** : Is  a CISCO proprietary that uses multiple routing algorithm 
- **BGP (Border Gateway Protocol)**: BGP is primarily used in internet to exchange routing info **between networks**, It helps in routing **across multiple networks** 
- **RIP (Routing Information Protocol)**: Simple routing protocol used in small networks. Routers share **number of hops** and **networks** they reach which is build into **routing table** 

Q: Which routing protocol discussed in the task is a Cisco proprietary protocol ?
A: EIGRP

## NAT

NAT is used to provide **one public IP address** to many **Private IP addresses** 

Router that uses NAT creates a **table** that maps **internal IP addresses and port number** with it's **external IP address and port number**

Q: In the network diagram above, what is the public IP that the phone will appear to use when accessing the internet 
A: 212.3.4.5

Q: Assuming that the router has infinite processing power, approximately speaking, how many thousand simultaneous TCP connection can it maintain ? 
A: 65

Q: Click on the View Site button to access the related site. Please follow the instruction on the site to obtain the flag. 
A: THM{computer_is_happy}


# Networking Core protocols 

## DNS

**Domain Name System**, responsible for mapping domain name to an IP address. It's **Application Layer** protocol, Uses UDP port 53. Has DNS records :
- **A record** : Maps hostname to IPv4 address 
- **AAAA Record** : Maps hostname to IPv6 address 
- **CNAME Record** : Canonical Name maps domain name to another domain name
- **MX Record** : **Mail Exchange** record specifies mail server responsible 

Q: Which DNS record type refers to IPv6 ? 
A: AAAA

Q: Which DNS record type refers to email server ? 
A: MX

## WHOIS 

**WHOIS** records contains domains registrant records publicly,**whois** is the command used 

![[Pasted image 20251027174229.png]]


![[Pasted image 20251027174209.png]]


Q: When was the x.com record created ? Provide the answer in YYYY-MM-DD format 
A: 1993-04-02

Q: When was the twitter.com record created ? Provide the answer in YYYY-MM-DD format 
A: 2000-01-21

## HTTP(S)

**HTTP** stands for **Hyper Text Transfer Protocol** and S for secure. TCP protocol used by web browser to communicate with web servers 

Common methods web browser uses : 

- **GET** get data from the server 
- **POST** submit data to the server 
- **PUT** create new or update existing info
- **DELETE** delete file or resources 

**HTTP** uses **80** and **HTTPS** uses **443** 

Q: Use telnet to access the file flag.html on MACHINE_IP. What is the hidden flag ? 
A: THM{TELNET-HTTP}

## FTP 

**File Transfer Protocol (FTP)** is designed for **file transfer** 

common **commands** in FTP  : 
- **USER** to input username 
- **PASS** to enter password 
- **RETR** used to download a file
- **STOR** used to upload a file 

FTP server listens on TCP **port 21** 

Q: Using the FTP client ftp on the AttackBox, access the FTP server at 10.201.111.228 and retrieve flag.txt. What is the flag found ? 
A: THM{FAST-FTP}


## SMTP 

**Simple Mail Transfer Protocol(SMTP)** is used for sending mails 

Common **commands** in **SMTP** : 

- **HELO** or **EHLO** initiates SMTP session
- **MAIL FROM** specifies sender email address
- **RCPT TO** specifies recipient's email address 
- **DATA** content of the email message 
- **.** indicate the end of email 

**SMTP** listens on TCP port **25**

Q: Which SMTP command indicates that the client will start the contents of the email message ? 
A: DATA

Q: What does the email client send to indicate that the email message has been fully entered 
A: .

## POP3 

The **Post Office Protocol** (POP3)  is designed to communicate with mail server to **retrieve email messages** 

common POP3 commands : 

- **USER \<username>** : the username 
- **PASS \<password>** : the user password
- **STAT** : requests number of messages and total size 
- **RETR \<message_number>** retrieves specified message
- **DELE \<message_number>** deletes message 
- **QUIT** : ends POP3 session 

**POP3** uses TCP **port 110**

Q: Looking at the traffic exchange, what is the name of the POP3 server running on the remote server ?
A: Dovecot

Use telnet to connect to 10.201.111.228's POP# server, What is the flag contained in the fourth message 
A: THM{TELENT_RETR_EMAIL}