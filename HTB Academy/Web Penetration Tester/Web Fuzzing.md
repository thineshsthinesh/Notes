
# Introduction 

## Introduction 

### Fuzzing vs Brute-forcing

- **Fuzzing** → sends wide range of unexpected/malformed inputs to trigger abnormal behaviour.
    
- **Brute-forcing** → tries many possible values for one specific guess (passwords, IDs).

### Why Fuzz?

- Find hidden vulnerabilities.
- Test input validation.
- Automate discovery.
- Simulate attacker behaviour.

### Essential Concepts 
#### Wordlist

- List of words/values used as fuzzing input.
    
- _Examples:_
    
    - Generic → admin, login, password, backup
    - App-specific → productID, addToCart

#### Payload

- Actual data sent to the app.
- _Example:_ `' OR 1=1 --`

#### Response Analysis

- Examine server responses for anomalies.
    
- _Examples:_
    
    - Normal → `200 OK`
    - Suspicious → `500 Error` with DB message (SQLi hint)

#### Fuzzer

- Tool that automates payload sending + response analysis.
- _Examples:_ ffuf, wfuzz, Burp Intruder

#### False Positive

- Tool flags a vulnerability that isn’t real.
- _Example:_ 404 for a non-existent directory.

#### False Negative

- Real vulnerability that the fuzzer fails to detect.
- _Example:_ subtle logic flaw in payment flow.

#### Fuzzing Scope

- Defines which part of the app to fuzz.
- _Example:_ only fuzz login page, or specific API endpoint.

## Tooling
### Install Requirements

- Need **Go**, **Python3**, **pipx**.
- Install on Debian/Ubuntu:
    
    - `sudo apt update`
    - `sudo apt install -y golang python3 python3-pip pipx`
        
    - `pipx ensurepath` + `sudo pipx ensurepath --global`
        
- Verify installs:
    
    - `go version`
    - `python3 --version`

### FFUF

- Fast web fuzzer written in Go.
- Install:  
    `go install github.com/ffuf/ffuf/v2@latest`

**Use Cases:**

- Directory/file enumeration
- Parameter discovery
- Brute-forcing inputs
### Gobuster

- Simple & fast content discovery tool in Go. 
- Install:  
    `go install github.com/OJ/gobuster/v3@latest`

**Use Cases:**

- Directory & file discovery
- DNS subdomain enumeration
- WordPress content detection
### FeroxBuster

- Recursive content discovery tool written in Rust.
    
- Install:  
    `curl -sL https://raw.githubusercontent.com/epi052/feroxbuster/main/install-nix.sh | sudo bash -s $HOME/.local/bin`
    

**Use Cases:**

- Recursive scanning
- Finding unlinked/hidden content
- High-performance brute-forcing

### wfuzz / wenum

- _wenum_ = actively maintained fork of **wfuzz**.
- Great for parameter fuzzing and flexible fuzzing tasks.
- Install with pipx:
    
    - `pipx install git+https://github.com/WebFuzzForge/wenum`
    - `pipx runpip wenum install setuptools`
        

**Use Cases:**

- Directory/file enumeration
- Parameter fuzzing
- Brute-forcing values

# Directory and File Fuzzing 

## Directory and File Fuzzing 

### Purpose

- Find hidden directories/files not linked on the website.
- Hidden assets may contain:
    
    - Sensitive data (configs, backups, logs)
    - Outdated/vulnerable content
    - Dev/staging resources
    - Hidden/undocumented functionality

### Why It Matters

- Expands attack surface understanding.
- Hidden areas often poorly secured.
- Info found can help later exploitation.

### Wordlists

- Wordlists supply directory/file names to test.
- Sources: common names, breaches, known vuln dirs.
- Tools (ffuf, wfuzz) rely on external wordlists.
    
- **SecLists common lists:**
    
    - `common.txt`
    - `directory-list-2.3-medium.txt`
    - `raft-large-directories.txt`
    - `big.txt`

### How Fuzzing Works (ffuf)

1. Provide wordlist.
2. Use `FUZZ` placeholder in URL.  
3. ffuf replaces FUZZ with each word.
4. Analyze responses (status codes, sizes, etc.).

Example target format:  
`http://IP:PORT/FUZZ`

### Directory Fuzzing (ffuf)

Command:

```bash
ffuf -w /usr/share/seclists/.../directory-list-2.3-medium.txt -u http://IP:PORT/FUZZ
```

- Found example: `/w2ksvrus` (Status 301)
### File Fuzzing

Common extensions to fuzz:

- `.php`, `.html`, `.txt`, `.bak`, `.js`

**Command:**

`ffuf -w /usr/share/seclists/.../common.txt -u http://IP:PORT/w2ksvrus/FUZZ -e .php,.html,.txt,.bak,.js -v`

Example finds:

- `dblclk.html`
- `index.html

## Recursive Fuzzing
### What It Is

- Automated fuzzing that **discovers nested directories**.
- Saves time by fuzzing deeper levels without manual input.
### How It Works

1. **Initial fuzzing** at root (`/`).
2. **New directories discovered** → added as new fuzzing targets.
3. **Repeat** until max depth reached or no new dirs found.

### Why Use It

- Efficient for large sites.
- Ensures deeper coverage.
- Reduces manual effort.
- Reveals hidden, nested assets.
    
### Recursive Fuzzing with ffuf

**Command:**

`ffuf -w wordlist -u http://IP:PORT/FUZZ -e .html -recursion`

**Key flags**

- `-recursion` → enable recursive fuzzing
- `-recursion-depth` → limit depth
- `-ic` → ignore wordlist comments
- `-rate` → limit requests/sec
- `-e` → specify extensions

### Example Findings

- ffuf discovers:
    
    - `/level1/`
    - `/level1/level2/`
    - `/level1/level3/`
    - `index.html` files inside them
        
- One file contains the flag:  
    **HTB{r3curs1v3_fuzz1ng_w1ns}**
    
### Be Responsible

- Recursive fuzzing can generate **huge amounts of traffic**.
    
- Adjust:
    
    - `-rate` — slow request speed
    - `-timeout` — avoid hanging
    - `-recursion-depth` — reduce depth

# Parameter and Value Fuzzing 

## Parameter and Value Fuzzing 

### Purpose

- Test how web apps handle manipulated parameter values.
- Identify vulnerabilities (XSS, SQLi, hidden functionality, logic flaws).
    
### GET vs POST Parameters

#### GET

- Visible in URL (`?param=value`).
- Used for searches, filters, non-sensitive actions.
- Example: `/search?query=fuzz&category=security`.

#### POST

- Sent in request body (hidden from URL).
- Used for sensitive data (login, forms).
- Encoded as:
    
    - `application/x-www-form-urlencoded` → key=value pairs
    - `multipart/form-data` → for file uploads
        
### Why Fuzz Parameters?

- Reveal hidden features/endpoints.
- Access unauthorized data (ID manipulation).
- Trigger vulnerabilities through unexpected inputs.
- Discover valid parameter values.
-
### GET Parameter Fuzzing (wenum)

#### Steps:

1. Identify parameter via manual request:  
    `curl http://IP/get.php` → Missing param.
2. Test with sample:  
    `curl http://IP/get.php?x=1` → Invalid value.
3. Fuzz values:
	`wenum -w common.txt --hc 404 -u "http://IP/get.php?x=FUZZ"`
4. Look for unique 200 OK response → valid value found.

### POST Parameter Fuzzing (ffuf)

#### Steps:

1. Identify param:  
    `curl -d "" http://IP/post.php` → Missing param `y`.
2. Fuzz using ffuf:
	`ffuf -u http://IP/post.php -X POST \ -H "Content-Type: application/x-www-form-urlencoded" \ -d "y=FUZZ" -w common.txt -mc 200`
3. Look for 200 OK → valid value found.

### Key Tool Options

#### wenum

- `-w` → wordlist
- `--hc` → hide status codes
- `FUZZ` → placeholder for injected values

#### ffuf

- `-X POST` → request method
- `-d "y=FUZZ"` → POST body fuzzing
- `-mc` → match response codes
- `-H` → set headers

# Virtual Host and Subdomain Fuzzing 

## Virtual Host & Subdomain Fuzzing 

### Virtual Hosts

- Multiple websites hosted on one server/IP.
- Identified via **Host header** in HTTP requests.
- Used to serve different sites from same infrastructure.
- Risk: **Misconfigured vhosts** may expose internal apps.

### Subdomains

- DNS-based subdivisions of a main domain (e.g., _blog.example.com_).
- Used to separate services/functions.
- Risk: **Subdomain takeover** if DNS mismanaged.

### Gobuster

A tool for:

- Directory & file discovery
- Subdomain enumeration
- Virtual host discovery
    
### Gobuster VHost Fuzzing

#### Requirements:

- Add vhost to `/etc/hosts`:  
    `echo "IP inlanefreight.htb" | sudo tee -a /etc/hosts`
#### Command:

`gobuster vhost -u http://inlanefreight.htb:81 \ -w /usr/share/seclists/Discovery/Web-Content/common.txt \ --append-domain`

#### Key Flags:

- `vhost` → vhost fuzzing mode
- `--append-domain` → turns `admin` into `admin.inlanefreight.htb`
- Look for **Status 200** → valid vhost
### Gobuster Subdomain Fuzzing

#### Command:

`gobuster dns -d inlanefreight.com \ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`

#### Notes:

- `dns` → DNS fuzz mode
- Valid subdomain = resolves via DNS
- Output shows discoveries as `Found: sub.example.com`
- In latest versions, use `--do` / `--domain` instead of `-d`

# Filtering Fuzzing Output
## Filtering Fuzzing Output

Filtering reduces noise → faster identification of meaningful results.
### Gobuster Filters

- `-s` → include status code
- `-b` → exclude status codes
- `--exclude-length` → filter by response size  
    Example:

`gobuster dir -u URL -w wordlist -s 200,301 --exclude-length 0`

### FFUF Filters

- `-mc` → match status codes
- `-fc` → filter status codes
- `-fs` / `-ms` → filter/match size
- `-fw` / `-mw` → filter/match word count
- `-fl` / `-ml` → filter/match line count
- `-mt` → match time

**Example:**

`ffuf -u URL/FUZZ -w wordlist -mc 200 -fs 0-1023`

### wenum Filters

- `--hc` / `--sc` → hide/show status codes
- `--hl` / `--sl` → hide/show line count
- `--hw` / `--sw` → hide/show word count
- `--hs` / `--ss` → hide/show size
- `--hr` / `--sr` → regex filtering

**Example:**

`wenum -w wordlist --sc 200,302 -u URL/FUZZ`

### Feroxbuster Filters

- `--dont-scan` → skip paths
- `-S` → filter size
- `-X` → filter regex
- `-W` / `-N` → filter word/line counts
- `-C` → exclude status codes

**Example:**

`feroxbuster -u URL -w wordlist -s 200 -S 10240 -X "error"`

# Validating Findings 

## Validating Findings 

### Why Validate

- Confirms real vulnerabilities (removes false positives).
- Helps assess impact & severity.
- Ensures issues are reproducible.
- Provides evidence for reporting.

### Manual Verification Steps

1. **Reproduce request** (curl/browser).
2. **Analyze response** for unexpected behavior, errors, sensitive output.
3. **Attempt safe exploitation** (PoC only, no harmful actions).
4. **Follow ethical guidelines**; avoid damaging systems or accessing sensitive data.

### Backup Directory Example

- Fuzzer finds `/backup/` → could contain sensitive items:
    
    - DB dumps
    - Config files (keys, creds)
    - Source code
        
- Validate safely using `curl`.
    
### Using curl for Validation

**Check directory listing:**

`curl http://IP:PORT/backup/`

→ If listing appears, directory listing vulnerability confirmed.

**Check file metadata safely:**

`curl -I http://IP:PORT/backup/password.txt`

Look for:

- **Content-Type** → file type
- **Content-Length** → non-zero indicates real data

This confirms risk **without reading sensitive content**.
### Key Principle

Validate findings **responsibly**: prove the vulnerability **without exploiting or exposing sensitive data**.

# Web APIs 

## Web APIs

### What is a Web API?

- Interface allowing applications to communicate over the web.
- Acts as a bridge between client (browser/app) and server.
- Used for data exchange and triggering actions across systems.
    
### Types of Web APIs

#### 1. REST (Representational State Transfer)

- Stateless, resource-based.
- Uses HTTP methods (GET, POST, PUT, DELETE).
- Data formats: JSON, XML.
- Example: `GET /users/123`

#### 2. SOAP (Simple Object Access Protocol)

- Formal, XML-based protocol.
- Uses SOAP envelopes.
- Strong built-in security and reliability.
- Used in enterprise systems.
- Example: 
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:tem="http://tempuri.org/">
   <soapenv:Header/>
   <soapenv:Body>
      <tem:GetStockPrice>
         <tem:StockName>AAPL</tem:StockName>
      </tem:GetStockPrice>
   </soapenv:Body>
</soapenv:Envelope>
```

#### 3. GraphQL

- Single endpoint API.
- Client requests _exact_ data required.
- Avoids over-fetching/under-fetching.
- Strong typing and introspection.
- Example :
```
query {
  user(id: 123) {
    name
    email
  }
}
```
### Advantages of Web APIs

- Standardized way to access server data.
- Enables integration of external services (OAuth, payments, maps).
- Supports microservices architecture.
- Promotes automation, modularity, and easier scaling.

### Web Server vs Web API

|Feature|Web Server|Web API|
|---|---|---|
|Purpose|Serve web pages|Let apps communicate & exchange data|
|Communication|HTTP|HTTP/S, SOAP, more|
|Data Format|HTML/CSS/JS|JSON, XML|
|User Interaction|Direct (browser)|Indirect (apps)|
|Access|Usually public|Public, private, or partner|
|Example|Website content|Mobile app fetching weather data|

### API Fuzzing Focus

- Instead of directories/files → fuzz **API endpoints** and **parameters**.
- Pay attention to:
    
    - Accepted data formats (JSON/XML)
    - Required fields
    - Error messages
    - Parameter manipulation possibilities

## Identifying Endpoints 

### REST APIs

#### Concept

- Built around **resources** identified by unique **URLs (endpoints)**.
- Endpoints follow a **hierarchical structure**.
    
    - `/users` – users collection
    - `/users/123` – user with ID 123
    - `/products`, `/products/456`

#### Parameters

|Type|Description|Example|
|---|---|---|
|**Query**|After `?`, used for filtering/sorting|`/users?limit=10&sort=name`|
|**Path**|Inside the URL path|`/products/{id}`|
|**Request Body**|In POST/PUT/PATCH body|`{ "name": "New Product" }`|

#### Discovering REST Endpoints

- **API Documentation** (Swagger/OpenAPI, RAML)
- **Network Traffic Analysis** (Burp Suite, Browser Dev Tools)
- **Parameter Name Fuzzing** (ffuf, wfuzz)
    
### SOAP APIs

#### Concept

- XML-based protocol.
- Uses a **single endpoint URL**.
- Operations and parameters defined in **WSDL**.
- Parameters appear in **SOAP XML body**.

#### Example SOAP Request Structure

```xml
<soapenv:Envelope>   <soapenv:Body>     <lib:SearchBooks>       <lib:keywords>cybersecurity</lib:keywords>       <lib:author>Dan Kaminsky</lib:author>     </lib:SearchBooks>   </soapenv:Body> </soapenv:Envelope>
```

#### Discovering SOAP Endpoints

- **WSDL Analysis** – operations, input/output parameters, datatypes.
- **Network Traffic Analysis** – Wireshark, tcpdump.
- **Fuzzing** – find hidden/undocumented operations.

### GraphQL APIs

#### Concept

- Usually **one endpoint** (e.g., `/graphql`).
- Uses **queries** (read) and **mutations** (write).
- Clients request **exact fields needed**.

#### Query Components

- **Field** – specific data
- **Relationship** – linked data
- **Nested Object** – deeper traversal
- **Argument** – modify query (filter/paginate)

#### Query Example

`query {   user(id: 123) {     name     email     posts(limit: 5) {       title       body     }   } }

#### Mutation Components

- **Operation** – action
- **Arguments** – input
- **Selection** – return fields

#### Mutation Example

`mutation {   createPost(title: "New Post", body: "Content") {     id     title   } }`

#### Discovering Queries & Mutations

- **Introspection** – retrieve entire schema.
- **API Documentation** – examples, structures, arguments.
- **Network Traffic Analysis** – observe real queries/mutations.

## API Fuzzing

### What is API Fuzzing?

- Specialized fuzzing for web APIs.
- Sends automated, modified requests to endpoints.
- Modifications include:
    
    - Altering parameter values
    - Changing headers
    - Reordering parameters
    - Using unexpected data types/formats
        
- Goal: trigger errors/crashes to reveal vulnerabilities (input validation, injection, auth issues).

### Why Fuzz APIs?

- **Find Hidden Vulnerabilities** (undocumented endpoints/parameters).
- **Test Robustness** against malformed input.
- **Automate Security Testing** for large input combinations.
- **Simulate Real Attacks** before attackers do.
    
### Types of API Fuzzing

#### 1. Parameter Fuzzing

- Tests query params, headers, bodies.
- Can reveal:
    
    - Injection attacks (SQLi, command injection)
    - XSS
    - Parameter tampering

#### 2. Data Format Fuzzing

- Manipulates JSON/XML structure/content/encoding.
- Exposes parsing errors, buffer overflows, improper special-character handling.

#### 3. Sequence Fuzzing

- Tests sequences of API calls.
- Uncovers:
    
    - Race conditions
    - IDOR
    - Authorization bypass
        
- Targets API logic and state management.

### Exploring the API

- API documentation at: `/docs`
- Documented endpoints:
    
    - GET `/`
    - GET `/items/{item_id}`
    - DELETE `/items/{item_id}`
    - PUT `/items/{item_id}`
    - POST `/items/`
        
- APIs may contain **undocumented/hidden endpoints**.
    
### Fuzzing the API (Example)

Commands:

`git clone https://github.com/PandaSt0rm/webfuzz_api.git cd webfuzz_api pip3 install -r requirements.txt python3 api_fuzzer.py http://IP:PORT`

### Fuzzer Output Summary

- Many invalid endpoints → 404
- Valid endpoints discovered:
    
    - `/cz...` (undocumented)
    - `/docs`
        
- 405 on `/items` → wrong HTTP method used
- Accessing hidden endpoint:
- `curl http://localhost:8000/cz... {"flag":"<snip>"}`
    
### Parameter Fuzzing Impact

Can reveal:

- **BOLA** (Broken Object-Level Authorization)
- **Broken Function-Level Authorization**
- **SSRF** (server makes unintended internal/external requests)


