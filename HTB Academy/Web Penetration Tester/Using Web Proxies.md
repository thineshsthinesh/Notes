
# Getting Started 

## Intro to Web Proxies 

### Why Web Proxies?

- Modern web/mobile apps constantly communicate with back-end servers.
- Pentesters must capture, inspect, and modify this traffic → **Web Proxies**.
### What Are Web Proxies?

- Tools that sit **between client (browser/app) and server** → act as **MITM**.
- Capture, view, modify **HTTP/HTTPS** traffic.
- Unlike Wireshark, proxies focus mainly on **web ports (80/443)**.

### Uses of Web Proxies

- Capturing & replaying HTTP requests
- Vulnerability scanning
- Fuzzing
- Crawling & mapping
- Request/Response analysis
- Config testing
- (Sometimes) code review

These capabilities are essential for web app pentesting.
### Burp Suite

- Most widely used web proxy tool.
- Includes built-in Chromium browser.
- **Free Community Edition** is powerful enough for most testing.
- **Paid features (Pro/Enterprise):**
    
    - Active Scanner
    - Faster Intruder
    - Some Extensions support

Tip: Educational/business emails may get a **free trial of Burp Pro**.

### OWASP ZAP

- Free, open-source alternative to Burp.
- No paid features → no throttling or limitations.
- Rapidly growing community + gaining many features Burp keeps paid.
- Strong option for full open-source pentesting.
    
### Burp vs ZAP

- Both provide similar core functionality.
- **ZAP** → best for free, open-source workflows.
- **Burp Pro** → more mature for advanced or corporate pentests.


## Setting Up 

### Setting Up Burp & ZAP 

#### Installation

- Both tools run on **Windows, macOS, Linux**.
- Pre-installed on **PwnBox, Kali, Parrot**.
- Require **Java Runtime Environment (JRE)** (usually bundled).
    
### Burp Suite Setup

#### Installation

- Download from Burp's official download page.
- Install normally (OS-specific installers).
- Can run via:
    
    - `burpsuite` (terminal)
    - App menu
    - **JAR version** → `java -jar burpsuite.jar`

#### Starting Burp

- On startup: choose **Temporary Project** (Community only).
- Pro version supports:
    
    - New project on disk
    - Open existing project
        
- Config options:
    
    - **Use Burp Defaults** (recommended for beginners)
    - Load custom config file

After setup → Burp is ready to use.
### ZAP Setup

#### Installation

- Download from ZAP’s download page.
- Install per OS or run via **JAR**: `java -jar zap.jar`.

#### Starting ZAP

- Launch via:
    
    - `zaproxy` (terminal)
    - App menu

- On startup: option to **persist session** or use **temporary project**.
    
    - Choose **temporary** unless doing long assessments.

Once open → ready for proxy configuration.


# Web Proxy 

## Proxy Setup 

### Purpose

- Web proxies sit **between browser/app ↔ server**.
- Capture, view, modify, and replay HTTP(S) requests.
- Essential for **web pentesting**, understanding app behavior.

### Using Pre-Configured Browsers

#### Burp

- Go to **Proxy → Intercept → Open Browser**.
- Opens Burp’s built-in Chromium with proxy + certificate preconfigured.

#### ZAP

- Click the **Firefox icon** in top bar.
- Opens ZAP’s preconfigured browser.
    

> For this module → using pre-configured browsers is enough.

### Manual Browser Proxy Setup (e.g., Firefox)

- Burp & ZAP default proxy: **127.0.0.1:8080**.
- Can change ports:
    
    - **Burp:** Proxy → Proxy Settings → Proxy Listeners
    - **ZAP:** Tools → Options → Network → Local Servers/Proxies

- Ensure Firefox proxy matches chosen port.

### Using FoxyProxy

- Install extension (preinstalled in PwnBox).
- Add new proxy:
    
    - IP: **127.0.0.1**
    - Port: **8080**
    - Name: Burp/ZAP
- Activate via FoxyProxy menu.

### Installing Proxy CA Certificates (Required for HTTPS)

Without certificate → HTTPS errors, blocked traffic, repeated warnings.

#### Burp CA Certificate

1. Enable Burp in FoxyProxy.
2. Visit **[http://burp](http://burp)**
3. → click **CA Certificate** to download.

#### ZAP CA Certificate

1. Tools → Options → Network → **Server Certificates**.
2. Click **Save** (or Generate for a new one).

### Import into Firefox

1. Visit **about:preferences#privacy**.
2. Scroll → **View Certificates**.
3. Go to **Authorities** tab → **Import** CA file.
4. Check:
    
    - **Trust this CA to identify websites**
    - **Trust this CA to identify email users**

After installation → all Firefox traffic routes cleanly through the proxy, including HTTPS.


## Intercepting Web Requests 

### Purpose

- Capture, view, edit, and resend HTTP requests.
- Understand backend behavior.
- Test for vulnerabilities by modifying requests.

### Intercepting Requests

#### Burp Suite

- Go to **Proxy → Intercept**.
- **Intercept is on** → requests will pause before reaching server.
- Use **Forward** to send request, **Drop** to discard.
- Pre-configured browser: **Proxy → Open Browser**.

#### ZAP

- Intercept off by default → green icon = traffic allowed.
- Toggle via **red/green button** or **CTRL+B**.
- Forward using **Step** button.
- Offers **HUD** to intercept directly in browser.

![[zap_intercept_htb_on.jpg]]

![[zap_intercept_page.png]]
### Manipulating Requests

- Edit intercepted request before forwarding.
- Used to test:
    
    - SQLi
    - Command injection
    - Auth bypass
    - Upload bypass
    - XSS / XXE
    - Error handling
    - Deserialization issues

### Example

- Original request:  
    `ip=1`
    
- Modify via interception:  
    `ip=;ls;`
    
- If backend doesn’t validate input → command executed → returns directory listing.

## Intercepting Responses 
### Purpose

- Modify server responses before they reach the browser.
- Useful for:
    
    - Enabling disabled fields
    - Revealing hidden fields
    - Removing input restrictions
    - Making penetration testing easier

### Burp Suite

#### Enable Response Interception

- Go to **Proxy → Proxy Settings → Intercept Response**.
- Refresh browser with **CTRL+SHIFT+R**.
- Burp shows:
    - Request → Forward → Response → Edit → Forward.

##### Example

- Change:  
    `type="number"` → `type="text"`  
    `maxlength="3"` → `maxlength="100"`
    
- Browser now accepts any input (including payloads).
    
### ZAP

#### Intercepting

- With interception on:
    
    - **Step** → forwards request & intercepts response.
    - Edit response → **Continue** → browser renders modified page.

### ZAP HUD Features

- **Light bulb icon**: auto-enable disabled fields / show hidden fields.
- No need to intercept or refresh.
- **Comments feature**: shows locations of HTML comments on page.

### Key Use Cases

- Bypass front-end restrictions.
- Input payloads directly in the browser
- Enable/modify hidden UI elements quickly

## Automatic Modification 

### Purpose

- Auto-modify **all** requests or responses.
- Helps bypass restrictions without manually intercepting every time.
### Automatic Request Modification

#### Burp – Match & Replace

- Location: **Proxy → Proxy settings → HTTP match and replace rules**
- Example: Change User-Agent
    
    - **Type:** Request header
    - **Match:** `^User-Agent.*$`
    - **Replace:** `User-Agent: HackTheBox Agent 1.0`
    - **Regex:** On

- Burp automatically rewrites the header in every request.
#### ZAP – Replacer

- Shortcut: **CTRL+R**
- Example settings:
    
    - **Match Type:** Request Header
    - **Match:** User-Agent
    - **Replace:** HackTheBox Agent 1.0
    - **Enabled:** True
        
- Can apply to specific initiators or all HTTP(S) messages.

### Automatic Response Modification

#### Burp

- Location: **Proxy → Options → Match and Replace**
- Example: Modify input restrictions
    
    - **Type:** Response body
    - **Match:** `type="number"`
    - **Replace:** `type="text"`
        
- Add another rule: `maxlength="3"` → `maxlength="100"`.
- After refresh (CTRL+SHIFT+R), changes persist without manual interception.
    
### Outcome

- Input field restrictions removed automatically.
- Payloads can be used directly in the browser.
- No need to intercept requests/responses each time.


## Repeating Requests 

### Purpose

- Resend previous requests without re-intercepting.
- Quickly modify payloads and get instant responses.
- Crucial for command injection, enumeration, and repetitive testing.

### Proxy History

- Burp: **Proxy → HTTP History**
- ZAP: **History tab / HUD bottom pane**
- Shows all captured requests + responses.
- Supports filtering + sorting.

**Burp extra feature:**

- Shows both **original** and **edited** requests.

### Repeating Requests

#### Burp – Repeater

- Select request → **CTRL+R** to send to Repeater.
- **CTRL+SHIFT+R** to open Repeater tab.
- Edit payload → **Send** → view response.
- Tip: Right-click → **Change Request Method** (GET/POST/etc.).

#### ZAP – Request Editor

- Right-click request → **Open/Resend with Request Editor**.
- Modify request → **Send**.
- Change HTTP method via dropdown.
- Display options adjustable.

#### ZAP HUD

- Click request in HUD History pane.
- Use:
    
    - **Replay in Console** → view raw response.
    - **Replay in Browser** → render response.

### Key Benefit

- Modify only the payload (e.g., change command) and resend instantly.
- No need to intercept the request each time.

## Encoding/Decoding

### Why Encoding Matters

- Custom HTTP requests must be **properly encoded** or servers return errors.
- Important for sending payloads, bypassing filters, and modifying cookies.

### URL Encoding

- Prevents misinterpretation of special characters.
- Must encode:
    
    - **Space → %20**
    - **& → %26** (parameter separator)
    - **# → %23** (fragment identifier)

#### Burp

- Select text → Right-click → _Convert Selection → URL-encode key characters_.
- Shortcut: **CTRL+U**
- Option: encode automatically while typing.

#### ZAP

- Automatically URL-encodes request data before sending.
    
### Other Encodings Used in Web Apps

- HTML encoding
- Unicode
- Base64
- ASCII hex

Useful for decoding cookies, tokens, parameters, API responses, etc.

### Decoding Tools

**Burp Decoder Tab**

- Paste text → choose Decode as (Base64, Hex, etc.)

**ZAP Encoder/Decoder/Hash (CTRL+E)**

- Automatically attempts multiple decoding.
- Custom tabs can be added for quick workflows.
    

**Example:**  
Base64 cookie decoded → `{"username":"guest","is_admin":false}`

### Encoding (Creating Modified Payloads)

1. Modify decoded content (e.g., change `guest` → `admin`
2. Re-encode using original format (e.g., Base64).
3. Use the new value in Repeater/ZAP Request Editor.
    
### Key Takeaway

Encoding/decoding is essential for:

- Manipulating cookies
- Sending payloads
- Bypassing filters
- Interacting correctly with back-end servers

## Proxying Tools 

### Purpose

- Intercept and analyze HTTP(S) requests from **CLI tools**, scripts, and thick clients.
- Enables full proxy features (modification, repetition, decoding) outside the browser.
- Only proxy when needed—**slows tools down**.
### Using Proxychains

- Routes all outgoing traffic from a command-line tool through a proxy.
- Edit `/etc/proxychains.conf` → comment last line → add:
- `http 127.0.0.1 8080`
- **Run tools via:**
	`proxychains -q <tool> <args>`
- **Example:**
	`proxychains -q curl http://SERVER:PORT`
- Burp/ZAP will show the forwarded request.
    
### Using Metasploit with a Proxy

- Launch Metasploit:
	 `msfconsole`
- Set proxy for any module:
	`set PROXIES HTTP:127.0.0.1:8080`
    
- Example module:
	`use auxiliary/scanner/http/robots_txt set RHOST SERVER_IP set RPORT PORT run`
    
- Requests appear in Burp/ZAP history.

### General Concept

- Any tool capable of setting a proxy can be routed through Burp/ZAP:
    
    - CLI tools (curl, wget)
    - Scripts (Python requests, custom tools)
    - Thick clients (desktop apps)
- Enables inspection, modification, and replay of their web traffic.

# Web Fuzzer

## Burp Intruder

### Purpose

- Built-in **web fuzzer** for discovering files, directories, parameters, values, and brute-force attacks.
- Community edition **throttled to ~1 req/sec** → useful only for small tests.
- Pro version = full speed + advanced features.

### Workflow

#### 1. Send Request to Intrude
- In Proxy History → right-click → **Send to Intruder** (`CTRL+I`).
#### 2. Target Tab
- Shows host/port of request being fuzzed.
### Positions

- Add payload positions using **§ marker**.
- Example: fuzz directory → `GET /§DIRECTORY§/`
- Leave request structure untouched (keep final empty lines).

### Payloads

#### Payload Position & Type

- Payload Set **1** used for single § position.
- **Simple List** → basic wordlist fuzzing.
- Other types: Runtime file, Character substitution, etc.

#### Payload Configuration

- Load a wordlist → e.g. `/opt/useful/seclists/.../common.txt`
- Can add/remove entries or merge lists.

#### Payload Processing

- Add rules to transform or filter payloads.
- Example: skip lines starting with `.` using regex:  
    `^\..*$`

#### Payload Encoding

- Controls URL-encoding.
- Usually leave enabled.

### Settings

Useful options:

#### Grep – Match

- Highlight responses containing certain strings (e.g. **200 OK**).
- Clear defaults → add `"200 OK"`.

#### Grep – Extract

- Extract response snippets (optional).

#### Resource Pool

- Controls concurrency (leave defaults in Community edition).

### Launching the Attack

- Click **Start Attack**.
    
- Results show:
    
    - Payload
    - Status (200/404 etc.)
    - Response length
    - Match indicators (e.g., 200 OK)
        
- Sort by **Status**, **Length**, or **Grep-Match** to find valid results.
    

### Example Outcome

- Directory fuzzing finds `/admin/` returning **200 OK**.
- Visit manually to confirm.
### Uses

- Directory/file fuzzing
- Parameter fuzzing
- Password brute-force
- AD/OWA/VPN login spraying (Pro recommended due to speed)

## ZAP Fuzzer

### Purpose

- ZAP’s built-in web fuzzer.
- No throttling → **much faster** than Burp Community Intruder.
- Good for directory fuzzing, parameter fuzzing, brute-force, etc.
### How to Start Fuzzing

1. Capture a request (e.g., visit `/test/`).
2. In History → right-click request → **Attack → Fuzz**.

### Fuzz Configuration

#### 1. Fuzz Location

- Highlight target word (e.g., `test`) → **Add**.
- ZAP marks the fuzz position (green marker).

#### 2. Payloads

- Add → choose type:
    
    - **File** (load external wordlist)
    - **File Fuzzers** (built-in wordlists like dirbuster)
    - **Numberzz**, etc.
        
- Example: choose `dirbuster/directory-list-1.0.txt`.

#### 3. Processors (Optional)

- Modify each payload before insertion.    
- Options include:
    
    - URL Encode / Decode
    - Prefix / Postfix string
    - Base64 encode
    - Hashing (MD5, SHA-1/256/512)
    - Script (custom)
        
- Example: add **URL Encode** processors

#### 4. Options

- Increase speed: set **Concurrent Threads** ≈ 20.
- Strategy:
    
    - **Depth first** – finish all payloads for one position before next.
    - **Breadth first** – test each payload on each position before moving on.
        
- Adjust retries, max errors, redirect behavior, etc.

### Run the Fuzzer

- Click **Start Fuzzer**.
- Sort results by:
    
    - **HTTP Code** (look for 200)
    - **Response Size**
    - **RTT** (useful for time-based attacks)
    
### Example Result

- Hit: `/skills/` returned **200 OK**.
- View the request/response to confirm directory exists.
    
### Key Advantages

- Fast fuzzing (no paywall limits).
- Built-in wordlists.
- GUI processors for encoding/hashing.
- Easy response analysis for anomalies.

# Web Scanner 

## Burp Scanner
### Overview

- Burp Pro–only feature.
- Performs **Crawling**, **Passive Scanning**, and **Active Scanning**.
- Used to map, analyze, and discover vulnerabilities automatically.
### Starting a Scan

- **Options:**
    
    - Right-click request → **Scan / Passive Scan / Active Scan**
    - Dashboard → **New Scan**.
    - Scan **in-scope** items only (define in Target → Scope).
    
### Target Scope

- Add/remove URLs or directories to/from scope.
- Helps limit scanning to selected targets.
- Can exclude risky endpoints (e.g., logout URLs).
### Crawler

- Builds site map by following links and forms.
- Choose **Crawl** or **Crawl & Audit**.
- Supports preset configs (e.g., _Crawl strategy – fastest_).
- Can record login steps for authenticated scans.

### Passive Scanner

- No new requests—analyzes existing traffic only.
- Detects potential issues (missing headers, DOM XSS indicators, etc.).
- Shows **Severity** + **Confidence** levels.
- Quick but not fully reliable; identifies _possible_ issues.

### Active Scanner

- Full automated vulnerability scanner.
- Performs:
    
    - Crawl + directory fuzzing
    - Passive scan
    - Verification of findings
    - JS analysis
    - Active tests (XSS, SQLi, Command Injection, etc.)
        
- Highly thorough but slow.
- Configurable:
    
    - Audit checks (e.g., _critical issues only_)
    - Login details
    - Scan speed & behavior
    
### Viewing Results

- Dashboard → **Issue Activity**.
- Filter by severity and confidence.
- Each issue includes:
    
    - Description
    - Impact
    - Proof of request/response
    - Remediation guidance

### Reporting

- Target → Site Map → right-click → **Report issues**
- Export HTML report.
- Includes summaries, PoCs, and remediation tips.
- **Should not** be used as final client report—only supporting data.

## ZAP Scanner 

### Overview

- Free, built-in scanner: **Spider**, **Passive Scan**, **Active Scan**.
- Builds site map, analyzes responses, and tests for vulnerabilities.

### Spider (Crawler)

- Finds links + forms to map the site
- Start via:
    
    - History → **Attack → Spider**
    - HUD → right-side **Spider Start** button.
        
- Adds target to scope if needed.
- Shows discovered pages in **Sites Tree**.
- **Ajax Spider**: detects JS/AJAX-loaded links.

### Passive Scanner

- Runs automatically during Spidering.
- No extra requests sent.
- Detects:
    
    - Missing headers
    - Info leaks
    - Potential DOM XSS
        
- Alerts shown in:
    
    - **Page alerts** (HUD)
    - **Global Alerts tab** (ZAP UI)
    
### Active Scanner

- Sends attack payloads to all discovered pages + parameters.
    
- Tries for:
    
    - Command Injection
    - SQLi
    - XSS
    - Path traversal
    - And more
        
- Slower but thorough.
- Shows progress in HUD + main UI.
- High-severity findings usually indicate compromise potential.

### Alert Details

- Alerts categorized by: **High / Medium / Low / Informational**

- Includes:
    
    - Description
    - Example attack
    - Evidence (response snippet)
    - Remediation guidance
    - Request/response used for detection
        
### Reporting

- Export via **Report → Generate HTML Report**.
- Other formats: XML, Markdown.
- Report includes:
    
    - Summary of alerts
    - Details of each issue
    - Evidence + affected URLs



## Extensions 

### Purpose

- Extend functionality of Burp and ZAP.
- Add new features, scanners, encoders, fuzzing wordlists, automation, etc.
- Community-driven add-ons.
### Burp Extensions (BApp Store)

#### Access

- _Extender → BApp Store_
- Sort by popularity to find commonly used add-ons.

#### Installation

- Some require **Jython** or other dependencies.
- Installed extensions add new tabs or tools inside Burp.

#### Example

- **Decoder Improved**
    
    - Enhances encoding/decoding/hashing.
    - Provides many extra encoders & hash algorithms.

#### Popular Burp Extensions

- Active Scan++
- Backslash Powered Scanner
- Java Deserialization Scanner
- J2EEScan
- CMS Scanner
- Retire.js
- Autorize
- Wsdler
- CSP Auditor
- Additional Scanner Checks
- (.NET beautifier, Error Message Checks, etc.)
    
### ZAP Extensions (Marketplace)

#### Access

- _Manage Add-ons → Marketplace_

#### Add-on Types

- Release (stable)
- Beta/Alpha (experimental)

#### Example

- **FuzzDB Files + FuzzDB Offensive**
    
    - Adds comprehensive fuzzing wordlists:
        
        - OS Command Injection
        - Web payloads
        - Exploit patterns

#### Usage

- New wordlists appear under **File Fuzzers** in the ZAP Fuzzer
- Useful for automating exploit testing (e.g., command injection, LFI, etc.)

### Final Notes

- Both Burp and ZAP can be enhanced significantly with extensions.
- Extensions help automate scanning, increase payload coverage, beautify data, and assist exploitation.
- Mastery of these tools + their add-ons is essential for modern web pentesting workflows.

