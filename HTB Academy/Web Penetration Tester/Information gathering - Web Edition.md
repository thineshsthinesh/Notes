
# Introduction 

## Introduction 

### Purpose

- Foundation of web security assessments.
- Part of **Information Gathering** in the penetration testing process.
- Helps map the target, find weaknesses, and prepare for exploitation.

### Goals

- **Identify Assets:** Pages, subdomains, IPs, technologies.
- **Find Hidden Info:** Backup files, configs, internal docs.
- **Analyse Attack Surface:** Technologies, configurations, entry points.
- **Gather Intelligence:** Emails, personnel info, behavioural patterns.

### Types of Reconnaissance

#### 1. Active Reconnaissance

Direct interaction with the target → higher detection risk.

|Technique|Purpose|Tools|Detection|
|---|---|---|---|
|**Port Scanning**|Find open ports & services|Nmap, Masscan|High|
|**Vulnerability Scanning**|Check for known vulns|Nessus, Nikto|High|
|**Network Mapping**|Discover infrastructure|Traceroute, Nmap|Med–High|
|**Banner Grabbing**|Identify service versions|Netcat, curl|Low|
|**OS Fingerprinting**|Identify OS|Nmap -O|Low|
|**Service Enumeration**|Identify service versions|Nmap -sV|Low|
|**Web Spidering**|Crawl site for pages/files|Burp/ZAP Spider|Low–Med|

Active recon → More data, but easily noticed.

#### 2. Passive Reconnaissance

No direct interaction → stealthy.

|Technique|Purpose|Tools|Detection|
|---|---|---|---|
|**Search Engine Queries**|Find public information|Google, Shodan|Very Low|
|**WHOIS Lookups**|Domain ownership data|whois|Very Low|
|**DNS Analysis**|Subdomain & infra discovery|dig, dnsenum|Very Low|
|**Web Archive Analysis**|Old versions of site|Wayback Machine|Very Low|
|**Social Media Analysis**|Employee/target data|LinkedIn, Twitter|Very Low|
|**Code Repositories**|Exposed code/credentials|GitHub|Very Low|

Passive recon → Stealthy, but limited to public info.


# WHOIS

## WHOIS 

### What is WHOIS?

- Query/response protocol for retrieving info about registered internet resources.
- Works for **domain names**, **IP blocks**, and **autonomous systems**.
- Acts like an **internet phonebook**—shows ownership and administrative details.
    
### Example Output Fields

Typical WHOIS record includes:

- **Domain Name** – e.g., _example.com_
- **Registrar** – company where domain was registered
- **Registrant Contact** – owner of the domain
- **Admin Contact** – manages domain operations
- **Technical Contact** – handles technical issues
- **Creation/Expiration Dates** – domain lifecycle info
- **Name Servers** – DNS servers pointing to the domain
    
### History (Short)

- Created in the 1970s by **Elizabeth Feinler** and the NIC (Network Information Center).
- Built to track users, hostnames, and domain names on ARPANET.
- Evolved into the modern WHOIS directory used globally.

### Why WHOIS Matters in Recon

- **Identify key personnel** (names, emails, phone numbers) → useful for social engineering.
- **Reveal infrastructure details** (DNS, IPs, hosting info).
- **View historical ownership** using services like [WhoisFreaks](https://whoisfreaks.com/) → helps track changes, migrations, or potential weaknesses.

## Utilizing WHOIS 

### Why WHOIS Matters

- Helps validate domains in investigations.
- Reveals ownership, hosting, registration dates, and infrastructure clues.
- Useful in phishing detection, malware analysis, and threat intelligence.
    
### Scenario 1 — Phishing Investigation

- WHOIS lookup on suspicious domain shows:
    
    - **Recently registered**
    - **Privacy-protected registrant**
    - **Name servers from bulletproof hosting**
        
- Indicators strongly suggest phishing.
- Analysts block the domain and warn users.
    
### Scenario 2 — Malware Analysis

- WHOIS lookup on C2 domain reveals:
    
    - Registrant using **anonymous free email**
    - Registered in **high-risk cybercrime region**
    - Registrar known for **weak abuse control**
        
- Suggests malicious infrastructure or bulletproof hosting.
- Analyst reports to hosting provider for takedown.

### Scenario 3 — Threat Intelligence

WHOIS patterns across attacker domains:

- Domains registered in **clusters** before attacks.
- Fake identities / aliases used.
- Shared **name servers** → common infrastructure.
- Domains repeatedly taken down.
- Helps build TTPs and IoCs for future detection.
    
### Using WHOIS Command

Install:

`sudo apt update sudo apt install whois -y`

Run lookup:

`whois facebook.com`
### Key Findings in facebook.com WHOIS

- **Registrar:** RegistrarSafe, LLC
- **Creation:** 1997
- **Expiry:** 2033
- **Owner:** Meta Platforms, Inc.
- **Statuses:** Delete/Transfer/Update prohibited → strong protection
- **Name servers:** All under facebook.com → internal DNS management

Summary: Legit, long-standing, secured domain for a major company.


# DNS & Subdomains 

## DNS 

### What DNS Is

- DNS = Internet’s “GPS”; translates domain names → IP addresses.
- Allows humans to use names instead of numbers.
- Essential for navigation across the internet.

### How DNS Works

1. **Computer checks cache** for the IP.
2. **DNS Resolver** (usually ISP) queried.
3. **Root Server** directs to correct TLD (.com, .org).
4. **TLD Server** points to authoritative server.
5. **Authoritative Server** returns actual IP.
6. **Resolver caches & sends IP** to user.
7. **Browser connects** to target server.

### Hosts File

- Local manual DNS override: `/etc/hosts` or `C:\...\hosts`.
- Format: `IP hostname`.
- Uses:
    
    - Local development
    - Testing connectivity
    - Blocking domains (`0.0.0.0 bad.com`)

### Key DNS Concepts

- **Zone** → administrative portion of domain space.
- **Zone File** → contains DNS records for the zone.
- **Resolvers** → translate names to IPs.
- **Root / TLD / Authoritative Servers** → DNS hierarchy layers.

### Common DNS Record Types

|Record|Purpose|
|---|---|
|A|Hostname → IPv4|
|AAAA|Hostname → IPv6|
|CNAME|Alias to another hostname|
|MX|Mail server for domain|
|NS|Authoritative name servers|
|TXT|Arbitrary text (SPF, verification)|
|SOA|Zone metadata (primary NS, admin, timers)|
|SRV|Service + port mapping|
|PTR|Reverse DNS (IP → name)|

_"IN"_ = Internet class (most common).

### Why DNS Matters for Recon

- **Find Hidden Assets** → subdomains, dev servers, old infrastructure.
- **Map Infrastructure** → hosting, load balancers, email services.
- **Track Changes** → new subdomains, exposed services, new tech.
- **Identify Weak Points** → misconfigurations, outdated systems.
- **Support Social Engineering** → clues in TXT records, domain usage.

## Digging DNS  
### DNS Recon Tools

Common tools used to query and enumerate DNS information:

|Tool|Key Points|Use Cases|
|---|---|---|
|**dig**|Most powerful DNS lookup tool; supports all record types; detailed output|Manual queries, zone transfers, troubleshooting|
|**nslookup**|Simple DNS query tool|Quick A/AAAA/MX lookups|
|**host**|Very concise output|Fast basic DNS checks|
|**dnsenum**|Automated enumeration + brute force|Subdomain discovery, zone transfers|
|**fierce**|Recursive DNS recon, wildcard detection|Subdomain mapping|
|**dnsrecon**|Multiple DNS recon techniques|Comprehensive DNS enumeration|
|**theHarvester**|OSINT harvesting + DNS extraction|Emails, employee names, DNS info|
|**Online lookup tools**|Browser-based|Quick lookups without CLI|

---

### Common `dig` Commands

|Command|Purpose|
|---|---|
|`dig domain.com`|Default A record lookup|
|`dig domain.com A`|IPv4 address|
|`dig domain.com AAAA`|IPv6 address|
|`dig domain.com MX`|Mail servers|
|`dig domain.com NS`|Name servers|
|`dig domain.com TXT`|TXT records|
|`dig domain.com SOA`|SOA record|
|`dig @1.1.1.1 domain.com`|Query specific DNS server|
|`dig +trace domain.com`|Full DNS resolution path|
|`dig -x IP`|Reverse DNS lookup|
|`dig +short domain.com`|Clean answer only|
|`dig ANY domain.com`|Request all record types (often restricted)|

**Warning:** Excessive DNS queries can be rate-limited or blocked.

### Understanding `dig` Output (Example Summary)

A typical `dig google.com` output includes:

#### 1. Header

- **opcode: QUERY** — type of action
- **status: NOERROR** — query successful
- **flags: qr rd ad** — response, recursion desired, authenticated data
- Shows counts: questions, answers, authority, additional

#### 2. Question Section

The record you asked for (e.g., `google.com A`).

#### 3. Answer Section

Returned record (e.g., `google.com A 142.251.47.142`).

#### 4. Footer

- Query time
- DNS server used
- Timestamp
- Message size

#### Optional EDNS Section

Appears when DNSSEC or large packet support is used.

#### Example: `+short` Output

```powershell
dig +short hackthebox.com 
104.18.20.126 
104.18.21.126
```


## Subdomains 

### What Are Subdomains?

- Extensions of a main domain (e.g., `blog.example.com`, `mail.example.com`).
- Used to organize different website functions.

### Why Subdomains Matter in Recon

Subdomains may expose:

- **Development/Staging areas** → often weak security.
- **Hidden admin/login portals**.
- **Legacy apps** → outdated & vulnerable.
- **Sensitive data** → misconfigurations, exposed files.

These increase the attack surface.
### Subdomain Enumeration

Process of discovering all subdomains associated with a domain.

#### DNS Records Involved

- **A / AAAA** → maps subdomain to IP.
- **CNAME** → alias pointing to another domain/subdomain.

### 1. Active Enumeration

Directly interacts with target DNS servers.

**Methods:**

- **Zone transfer attempts** (rarely works).
- **Brute-force guessing** using wordlists

**Tools:**

- `dnsenum`
- `ffuf`
- `gobuster`
    
Pros: more thorough  
Cons: noisy, detectable

### 2. Passive Enumeration

No direct interaction with target.

Sources:

- **Certificate Transparency logs** → SAN fields list subdomains.
- **Search engines (site: operator)** → indexed subdomains.
- **DNS data aggregators**.

Pros: stealthy  
Cons: may miss some subdomains

### Best Approach

Combine **active + passive** for widest coverage.

## Subdomain Bruteforcing 

### What It Is

An **active subdomain discovery technique** that uses a **wordlist** to guess possible subdomains and checks which ones resolve.

### 1. Wordlist Selection

Types of wordlists:

- **General-purpose** — common names (dev, test, mail, admin, blog).
- **Targeted** — industry or organisation-specific naming conventions.
- **Custom** — built from OSINT, keywords, patterns.

### 2. Iteration & Querying

For each word in the wordlist:

- Append to domain → `word.example.com`
- Prepare for DNS resolution.
    
### 3. DNS Lookup

Query DNS for:

- A record (IPv4)
- AAAA record (IPv6)

IF it resolves → potential valid subdomain.

### 4. Filtering & Validation

- Save resolving subdomains.
- Optional manual validation (browser visit, curl, HTTP checks).
    
### Tools for Subdomain Bruteforcing

|Tool|Description|
|---|---|
|**dnsenum**|DNS recon + brute-force + zone transfer attempts + Google scraping.|
|**fierce**|Recursive DNS discovery, wildcard detection, simple interface.|
|**dnsrecon**|Combined DNS enumeration techniques, customizable output.|
|**amass**|Actively maintained, integrates multiple data sources.|
|**assetfinder**|Lightweight, fast passive subdomain finder.|
|**puredns**|High-performance resolver & brute-force tool.|

### dnsenum Overview

A full-featured DNS recon tool with:

- DNS record enumeration (A/AAAA/MX/NS/TXT)
- Zone transfer attempts
- Subdomain brute-forcing with wordlists
- Reverse DNS lookups
- Google scraping for subdomains
- WHOIS lookups
    
#### Example Command

`dnsenum --enum inlanefreight.com \ -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r`

**Options used:**

- `--enum` → enables multiple discovery features.
- `-f` → specify brute-force wordlist.
- `-r` → recursive enumeration (enumerate sub-subdomains).
    
#### Sample Output (Summary)

```
 dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 

dnsenum VERSION:1.2.6

-----   inlanefreight.com   -----


Host's addresses:
__________________

inlanefreight.com.                       300      IN    A        134.209.24.248

[...]

Brute forcing with /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:
_______________________________________________________________________________________

www.inlanefreight.com.                   300      IN    A        134.209.24.248
support.inlanefreight.com.               300      IN    A        134.209.24.248
[...]


done.
```


## DNS Zone Transfers 

### What Is a DNS Zone Transfer?

- A mechanism used to **synchronize DNS records** between primary and secondary DNS servers.
- Uses **AXFR** (full zone transfer).
- Transfers **entire zone file** → includes all DNS records (A, AAAA, MX, CNAME, NS, TXT, etc.).

### How Zone Transfers Work

![[ig_dns_zone_transfers_1.png]]

1. **Secondary server sends AXFR request** to primary.
2. **Primary responds with SOA record** (serial number, authority info).
3. **Primary sends all DNS records** in the zone.
4. **Primary signals end-of-transfer**.
5. **Secondary acknowledges completion**.
    
### Why Zone Transfers Are a Security Risk

If misconfigured, **anyone can request a full zone transfer**, revealing:

- All **subdomains**
- **IP addresses** of internal/external systems
- **Mail servers**, infrastructure layout
- Hidden or sensitive hosts (dev, staging, admin portals)

This gives attackers a **complete map of the domain**.

### Why Misconfigs Happen

- Legacy default: _zone transfers allowed to anyone_
- Admin mistakes
- Outdated server configurations

Modern DNS hardening restricts transfers to **trusted secondary servers only**.

### How to Test for Zone Transfer

Using `dig`:
`dig axfr @nameserver domain.com`
Example:
`dig axfr @nsztm1.digi.ninja zonetransfer.me`
If vulnerable → full zone file is dumped (subdomains, records, IPs, etc.).
### What a Successful Transfer Reveals

- SOA record
- TXT, MX, NS, A/AAAA, CNAME entries
- Internal hosts
- Service records (SRV)
- PTR (reverse DNS) mappings
- Rare values (HINFO, AFSDB, ACME challenge tokens)
    
### Remediation

- **Limit AXFR access** to specific IPs (authorized secondary servers).
- Disable zone transfer where not needed.
- Ensure DNS server software uses **secure default configurations**.
   
### Example Output Summary (zonetransfer.me)

A successful AXFR reveals:

- Ownership info (SOA)
- Verification tokens (TXT)
- Mail routing (MX)
- Service endpoints (SRV)
- Numerous subdomains
- Reverse lookup records

Used as a **training/demo domain** to illustrate the risks.


## Virtual Hosts (VHosts)

### 1. What Are Virtual Hosts?

- Let one **web server host multiple websites** on the **same server/IP**.
- Differentiation is done using the **HTTP `Host` header**.
- Used by servers like **Apache, Nginx, IIS**.

### 2. VHosts vs Subdomains

- **Subdomain**
    
    - DNS concept.
    - Example: `blog.example.com`, `dev.example.com`.
    - Has its own **DNS record** (A/CNAME, etc.) pointing to an IP.
        
- **Virtual Host**
    
    - **Web server configuration** concept.
    - Maps a **hostname** (domain or subdomain) to a **document root** and config.
    - Can be:
        
        - Different domains: `example1.com`, `example2.org`, etc.
        - Subdomains: `dev.example.com`, `test.example.com`.
            
- **Key point:**
    
    - DNS says: “this name → this IP”.
    - VHost says: “for this name → serve these files/config on that IP”.

### 3. Accessing Hidden / Non-DNS VHosts

- Even if **no public DNS record** exists, you can still access a vhost by:
    - Editing your local **hosts file**:
        - Windows: `C:\Windows\System32\drivers\etc\hosts`
        - Linux/macOS: `/etc/hosts`
    - Add:
		 `<IP>   hidden.inlanefreight.htb`
        
- This is key for **internal or hidden vhosts**.
    
### 4. How the Web Server Picks the Site (High-Level Flow)

![[ig_virtualhosts_1.png]]


1. **Browser** sends HTTP request to server’s IP, with `Host: www.inlanefreight.com`.
2. **Web server** reads the `Host` header.
3. Looks into its **VirtualHost configuration** to find a match.
4. Serves files from that vhost’s **DocumentRoot** / config.
5. If no match → often serves **default vhost**.
    
### 5. Types of Virtual Hosting

1. **Name-Based Virtual Hosting**
    
    - Multiple sites on **same IP & port (e.g., 80)**.
    - Differentiation via `Host` header.
    - Most common (cheap, scalable).
    - Some limitations with old SSL/TLS usage (SNI largely solved this in practice, but not in text).
        
2. **IP-Based Virtual Hosting**
    
    - Each site has its **own IP**.
    - Server chooses site by **destination IP**, not Host header.
    - Pros: better isolation, protocol-agnostic.
    - Cons: needs **multiple IPs** (less scalable / more costly).
        
3. **Port-Based Virtual Hosting**
    
    - Same IP, **different ports** per site.
        
    - Example:
        
        - `http://example.com:80` → site A
        - `http://example.com:8080` → site B
            
    - Not user-friendly (requires port in URL).

### 6. Why VHosts Matter in Recon

- Some vhosts:
    
    - **Not in DNS** (no public subdomain).
    - Only reachable if:
        
        - You **guess/fuzz** the hostname (and edit hosts file), or
        - You know internal naming convention.
            
- VHosts may host:
    
    - Admin panels
    - Dev/staging sites
    - Old/legacy apps
    - Internal tools

Hence: **VHost fuzzing** = discovering hidden subdomains/websites on same IP.

### 7. VHost / Subdomain Discovery Tools

- **Gobuster**
- **Feroxbuster**
- **ffuf**

All can be used to fuzz vhosts by manipulating the **`Host` header**.

### 8. Gobuster for Virtual Host Discovery

**Requirements:**

- **Target IP / base domain** (e.g. `inlanefreight.htb:81`).
- **Wordlist** of potential names  
    (e.g. from SecLists: `subdomains-top1million-110000.txt`).

**Basic command:**

`gobuster vhost -u http://<target_IP_or_domain> \   -w <wordlist_file> \   --append-domain`

- `-u` → target URL (can include port).
- `-w` → path to wordlist.
- `--append-domain` → appends base domain to each word:
    
    - `forum` → `forum.inlanefreight.htb`.

**Useful extra options:**

- `-t` → number of threads (speed).
- `-k` → ignore SSL/TLS errors.
- `-o` → save results to file.
    

**Example from text:**

`gobuster vhost \   -u http://inlanefreight.htb:81 \   -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt \   --append-domain`

Output example:

- `Found: forum.inlanefreight.htb:81 Status: 200 [Size: 100]`
    

This indicates `forum.inlanefreight.htb` is a **valid vhost** on that IP/port.


## Certificate Transparency Logs 

- Certificate Transparency (CT) logs are **public, append-only databases** that record all SSL/TLS certificates issued by Certificate Authorities (CAs).
    
- Purpose:
    
    - Detect **rogue/misissued certificates**
    - Increase **CA accountability**
    - Strengthen **Web PKI security**
        
- Why useful for recon:
    
    - Reveal **real, historical, and hidden subdomains**
    - No brute-forcing required
    - Completely **passive** (no contact with target)
    - Can expose **staging**, **dev**, **backup**, and **old services**
        
- Common tools:
    
    - **crt.sh** — simple CT search engine
    - **Censys** — advanced search with filters/API
        
- Quick crt.sh API usage:
    

- `curl -s "https://crt.sh/?q=example.com&output=json"`
    
- Example: Find "dev" subdomains:
    

`curl -s "https://crt.sh/?q=facebook.com&output=json" \ | jq -r '.[].name_value' | grep dev | sort -u`


# Fingerprinting 

## Fingerprinting 

### Purpose

- Identify technologies, versions, and configurations behind a web app.
    
- Enables:
    
    - Targeted attacks (version-specific exploits)
    - Finding misconfigurations/outdated software
    - Prioritising vulnerable systems
    - Building a full infrastructure profile
### Fingerprinting Techniques

- **Banner Grabbing** – Read service banners (e.g., via `curl -I`).
- **HTTP Header Analysis** – `Server`, `X-Powered-By`, redirects, CMS clues.
- **Probing Behaviour** – Trigger errors or unique responses.
- **Page Content Analysis** – Script paths, comments, CMS fingerprints.
    
### Fingerprinting Tools

- **Wappalyzer / BuiltWith** – Technology profiling (browser/online).
- **WhatWeb** – CLI tech fingerprinting.
- **Nmap** – Service + OS detection (`-sV`, `-O`, NSE scripts).
- **Netcraft** – Hosting + tech + security posture.
- **wafw00f** – Detect Web Application Firewalls.
    
### Example: inlanefreight.com

#### 1. Banner Grabbing

`curl -I inlanefreight.com`

**Findings:**

- Apache/2.4.41 (Ubuntu)
- Redirects revealing **WordPress**
- WP-related paths (`wp-json`, redirect headers)

#### 2. WAF Detection

`wafw00f inlanefreight.com`

- WAF identified: **Wordfence**

#### 3. Nikto (Fingerprint Mode)

`nikto -h inlanefreight.com -Tuning b`

Findings:

- Apache outdated
- WordPress detected + login page
- Missing security headers (HSTS, X-Content-Type-Options)
- Potential BREACH risk
- Information disclosure (`license.txt`)

# Crawling

## Crawling

### What It Is

- Automated process (spidering) that follows links across a website
- Used to discover pages, structure, hidden content.
### How It Works

1. Start with a **seed URL**.
2. Fetch page → extract links.
3. Add links to queue → repeat.
4. Builds a map of the website.
### Crawling Strategies

- **Breadth-First**: Explore all links on each level first → good for overall site structure.
- **Depth-First**: Follow one path deep before backtracking → good for hidden/deep content.
### Data Extracted

- **Links (internal/external)** → find hidden pages/directories.
- **Comments** → possible leaks or hints.
- **Metadata** → author, software, keywords.
- **Sensitive files** → backups, configs, logs.

### Why It Matters

- Maps attack surface.
- Reveals hidden or exposed content.
- Helps detect misconfigurations (e.g., directory listing).
- Connects clues from multiple pages (context-building).


## robots.txt 

### What It Is

- A simple text file at **/robots.txt**.
- Gives **crawling rules** for bots (Robots Exclusion Standard).
### Purpose

- Tells crawlers **which paths to avoid**.
- Used to control indexing, reduce server load, hide areas from search engines.
    
### Structure

- **User-agent:** which bot the rules apply to.
- **Directives:**
    
    - **Disallow:** paths bots should not crawl
    - **Allow:** exceptions
    - **Crawl-delay:** time between requests
    - **Sitemap:** link to sitemap

**Example:**

`User-agent: * Disallow: /admin/ Allow: /public/`

### Why It Matters (Recon)

- Disallowed paths often reveal **hidden directories**, admin panels, staging areas, backups.
- Helps map site structure.
- Sometimes contains **honeypot paths** to catch malicious bots.
### Limitations

- Not enforced; bots may ignore it.
- Not a security control—only a guideline for _well-behaved_ crawlers.

## Well-Known URIs 

### What They Are

- A standardized directory: **/.well-known/** (RFC 8615).
- Stores important **site metadata**, configs, and security info.
- Provides a consistent place for clients/tools to discover config files.
    
# **Common Examples**

|URI|Purpose|
|---|---|
|**security.txt**|Security contact info (RFC 9116).|
|**change-password**|Standard URL for password-change page.|
|**openid-configuration**|OpenID Connect metadata (OAuth-related).|
|**assetlinks.json**|App/domain ownership verification.|
|**mta-sts.txt**|Email security policy (MTA-STS).|

### Why It Matters (Recon)

- Exposes useful **configuration endpoints**.
- Can reveal:
    
    - OAuth/OpenID endpoints
    - Token and authorization URLs
    - Supported auth methods
    - JWKS key URLs
    - Security contact details
        
- Helps map authentication flows and security mechanisms.

### High-Value Endpoint: openid-configuration

Returns JSON metadata such as:

- **authorization_endpoint**
- **token_endpoint**
- **userinfo_endpoint**
- **jwks_uri**
- Supported **scopes**, **response types**, **algorithms**

Useful for:

- Enumerating OAuth/OpenID flows
- Finding sensitive auth endpoints
- Understanding signing algorithms & server capabilities

## Creepy Crawlies

### Purpose

- Automate discovery of web pages, files, links, metadata.
- Helps find hidden content, emails, JS files, PDFs, comments, etc.

### Popular Crawlers

- **Burp Suite Spider** – maps apps, finds hidden content.
- **OWASP ZAP Spider** – free crawler + scanner.
- **Scrapy** – Python framework for custom spiders.
- **Apache Nutch** – large-scale Java crawler.
    
### Ethics

- Get permission.
- Avoid overloading the server.
### Scrapy Recon (ReconSpider)

### **Setup**

`pip3 install scrapy wget -O ReconSpider.zip <URL> unzip ReconSpider.zip python3 ReconSpider.py http://inlanefreight.com`

### Output → results.json

Contains:

- **emails** – harvested email addresses
- **links** – internal/external URL
- **external_files** – PDFs, docs
- **js_files** – JavaScript sources
- **form_fields** – input fields
- **images / videos / audio** – media assets
- **comments** – HTML comments
    
### Usefulness

- Reveals hidden directories/pages.
- Extracts sensitive files (PDFs, backups).
- Shows JS endpoints/frameworks.
- Finds emails for OSINT.
- Helps map the entire web app structure.

# Search Engine Discovery 

## Search Engine Discovery

### Purpose

- Use search engines for OSINT to find public info about websites, orgs, people.
- Helps uncover hidden pages, exposed files, credentials, employee data, etc.

### Why It Matters

- **Open-source** & legal
- **Huge reach** — search engines index large parts of the web
- **Easy & free**
- Supports security assessments, competitive intel, investigations, threat intel
### Key Technique → Search Operators

|Operator|Use|
|---|---|
|`site:`|Limit results to a domain|
|`inurl:`|Find terms inside URLs (e.g., login pages)|
|`filetype:`|Search file formats (PDF, XLS, etc.)|
|`intitle:`|Search text in page titles|
|`intext:`|Search text in page body|
|`cache:`|View cached version of site|
|`link:`|Pages linking to a site|
|`related:`|Similar websites|
|`info:`|Basic meta info on a page|
|`AND/OR/NOT`|Combine filters|
|`" "`|Exact phrase search|
|`*`|Wildcard for unknown words|
|`..`|Number ranges|

### Google Dorking (Examples)

#### Login Pages

- `site:example.com inurl:login`
- `site:example.com (inurl:admin OR inurl:login)`

#### Exposed Files

- `site:example.com filetype:pdf`
- `site:example.com (filetype:xls OR filetype:docx)

#### Configuration Files

- `site:example.com inurl:config.php`
- `site:example.com (ext:conf OR ext:cnf)`

#### Database Backups

- `site:example.com inurl:backup`
- `site:example.com filetype:sql`

# Web Archives 

## Web Archives 

### What It Is

- A digital archive of past versions of websites (since 1996).
- Lets you view old snapshots ("captures") of pages, design, content, and files.

### How It Works

1. **Crawling** – Bots visit sites and download pages + resources.
2. **Archiving** – Stores HTML, CSS, JS, images, etc., tied to timestamps.
3. **Accessing** – Users browse old versions by date/time.
    
### Why It’s Useful for Recon

- **Find hidden/removed content:** old pages, directories, subdomains, files.
- **Spot old vulnerabilities:** outdated apps, old endpoints, exposed data.
- **Track changes:** website structure, technologies, employee info, functionality.
- **Passive recon:** no interaction with live target → safer, stealthier.
    
### Limitations

- Not all pages are archived.
- Frequency of snapshots varies.
- Sites can request exclusion (not always enforced).

# Automating Recon 

## Automating Recon 

### Why Automate

- Faster + more efficient than manual recon
- Scales across many domains
- Consistent, repeatable results
- Covers more attack surface (DNS, subdomains, crawling, ports, etc.)
- Integrates with other tools/workflows

### Recon Frameworks (Summary)

- **FinalRecon** — All-in-one recon (headers, whois, SSL, crawl, DNS, subdomains, dirs, wayback, ports).
- **Recon-ng** — Modular OSINT/recon framework.
- **theHarvester** — Gathers emails, subdomains, hosts, and banners.
- **SpiderFoot** — Automated OSINT for domains, IPs, emails, social profiles.
- **OSINT Framework** — Curated list of OSINT tools/resources.

### FinalRecon – Key Features

- Headers
- WHOIS
- SSL info
- Crawler (HTML/JS/images/robots/sitemap)
- DNS enumeration
- Subdomain discovery (API integrations: crt.sh, VirusTotal, Shodan, etc.)
- Directory brute-force
- Wayback Machine URLs
- Fast port scan
- Full recon mode

#### Basic Usage

`./finalrecon.py --url <target> --headers --whois`

**Full recon:**

`./finalrecon.py --full --url <target>`



