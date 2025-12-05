
# HTTP Fundamentals

## Hypertext Transfer Protocol (HTTP)

### What is HTTP

- Application-level protocol for communication on the web.
- Client sends request → server responds with resource.
- Default port: **80** (HTTP), **443** (HTTPS).
- Uses URLs to locate resources.


### URL Components

![[url_structure.png]]

|Component|Example|Purpose|
|---|---|---|
|**Scheme**|http://|Protocol to use.|
|**User Info**|admin:pass@|Optional credentials.|
|**Host**|inlanefreight.com|Server/domain name or IP.|
|**Port**|:80|Network port; defaults 80/443.|
|**Path**|/dashboard.php|Resource location.|
|**Query**|?login=true|Parameters for server processing.|
|**Fragment**|#status|Client-side navigation.|

Mandatory: **scheme + host**.

### HTTP Flow (Basic)

![[HTTP_Flow.png]]

1. Browser resolves domain via **DNS** → gets IP
2. Sends HTTP **GET** request to server.
3. Server returns **response + status code** (e.g., 200 OK).
4. Browser renders returned HTML.

Browsers check **/etc/hosts** before DNS.

### cURL Basics

- Command-line tool to send HTTP requests.
- Prints raw HTML (no rendering).
- Useful for automation and pentesting.

### Common cURL Commands

- `curl URL` → basic request.
- `curl -O URL` → download file using remote name.
- `curl -o file URL` → save output to custom filename.
- `curl -s URL` → silent mode (no progress output).
- `curl -h` or `curl --help all` → view help.
- Important flags:
    
    - `-d` send POST data
    - `-i` include headers
    - `-u user:pass` authentication
    - `-A` custom user-agent
    - `-v` verbose request details

## Hypertext Transfer Protocol Secure (HTTPS)

### Why HTTPS

- HTTP sends data in **clear-text** → vulnerable to MITM attacks.
- HTTPS encrypts all communication → attackers cannot read intercepted data.
- Now the default for most websites; browsers discourage HTTP.
    
### How HTTPS Looks in Traffic

- HTTP request: credentials visible (e.g., POST username/password).
	![[https_clear.png]]
- HTTPS request: data appears as **encrypted TLS application data**.
	![[https_google_enc.png]]
- Identified by **https://** and **lock icon** in browser.
- DNS queries may still reveal domains → use encrypted DNS/VPN.
    
### HTTPS Flow 

![[HTTPS_Flow.png]]

1. User visits site with **http://**
2. Server returns **301 redirect** → send user to **https:// (port 443)**
3. **TLS Handshake**:
    
    - Client Hello
    - Server Hello + certificate
    - Key exchange + verification
    
4. Encrypted HTTP communication begins.

Modern browsers protect against downgrade attacks.

### cURL & HTTPS

- cURL handles HTTPS automatically.
- If SSL certificate is invalid → cURL blocks request:
    `curl: (60) SSL certificate problem`
- To ignore certificate issues (only for testing):
    `curl -k https://example.com`
    
## HTTP Requests & Responses 

### HTTP Request

![[raw_request.png]]

- Client → Server.
- Contains: method, path, version + headers + optional body.
- **Request line:**
    
    - **Method:** action (GET, POST, etc.)
    - **Path:** resource (e.g., /users/login.html)
    - **Version:** HTTP/1.1 or HTTP/2
        
- **Headers:** Host, User-Agent, Cookies, etc.
- **Body:** used mainly in POST/PUT requests.

### HTTP Response

![[raw_response.png]]

- Server → Client.
- First line: **HTTP version + status code** (e.g., 200 OK).
- Then response headers → body.
- Body may be **HTML, JSON, images, PDFs**, etc.

### Using cURL

- `curl URL` → sends request & shows body.
- `curl -v URL` → shows **full request + full response**.
    
- Displays:
    
    - Exact request line
    - All headers
    - Status code + response headers
    - Response body

- `-vvv` → even more protocol-level details.
    
### Browser DevTools

- Open with **F12** or **Ctrl+Shift+I**.
- Use **Network tab** to see requests & responses.
- Shows:
    
    - Status code
    - Method (GET/POST)
    - URL/path
        
- Can inspect:
    
    - Headers
    - Response body (pretty or raw view)
        
- "Filter URLs" helps search specific requests.

## HTTP Headers

### What Headers Do

- Carry extra info between client ↔ server.
- Some used in both directions, some request-only, some response-only.
- Format: `Header-Name: value`.

### 1. General Headers (request + response)

- **Date** – time message originated.
- **Connection (close/keep-alive)** – whether to keep TCP connection open.
    
### 2. Entity Headers (describe content/body)

- **Content-Type** – type of data (HTML, JSON, etc.), charset.
- **Media-Type** – similar to Content-Type.
- **Boundary** – separates multipart/form-data parts.
- **Content-Length** – size of message body.
- **Content-Encoding** – encoding/transform (gzip, etc.).
    
### 3. Request Headers (client → server)

- **Host** – domain/IP requested; important for virtual hosts.
- **User-Agent** – identifies browser/client.
- **Referer** – page that initiated request (easily spoofed).
- **Accept** – what media types the client accepts.
- **Cookie** – session or stored client data.
- **Authorization** – credentials/tokens.

### 4. Response Headers (server → client)

- **Server** – server software info.
- **Set-Cookie** – issues cookies to client.
- **WWW-Authenticate** – tells client what auth is required.
    
### 5. Security Headers

- **Content-Security-Policy (CSP)** – restricts allowed scripts/resources; mitigates XSS.
- **Strict-Transport-Security (HSTS)** – forces HTTPS only.
- **Referrer-Policy** – controls Referer header exposure.
    
### cURL Header Options

- `-I` → send **HEAD** request; show only response headers.
- `-i` → show headers + body for normal request.
- `-v` / `-vvv` → verbose: full request + response details.
- `-H "Header: value"` → set custom headers.
- `-A "User-Agent"` → change User-Agent.

### Browser DevTools

- Network tab → inspect any request.
- Shows: request headers, response headers, cookies.
- “Raw” view displays unformatted headers.

# HTTP Methods 

## HTTP Methods and Codes 

### Common Request Methods

- **GET** – Fetch resource; data can be passed in URL query string.
- **POST** – Send data in body (forms, files, uploads).
- **HEAD** – Same as GET but returns headers only (no body).
- **PUT** – Create/replace resource (dangerous if unrestricted).
- **DELETE** – Remove resource (can cause DoS if misconfigured).
- **OPTIONS** – Returns allowed HTTP methods for the server.
- **PATCH** – Partial update to an existing resource.

_Modern apps mostly use GET/POST; APIs often use PUT/DELETE._

### HTTP Status Codes 
#### Status Code Classes

- **1xx** – Informational.
- **2xx** – Success.
- **3xx** – Redirection.
- **4xx** – Client error.
- **5xx** – Server error.
#### Common Examples

- **200 OK** – Successful request.
- **302 Found** – Redirect to another URL.
- **400 Bad Request** – Invalid/malformed request.
- **403 Forbidden** – Client lacks permission; may indicate blocked input.
- **404 Not Found** – Resource does not exist.
- **500 Internal Server Error** – Server failed to process request.


## GET Method

- Default method used when visiting any URL.
- Requests resource using query parameters in URL.
- Browser may trigger many GET calls internally (JS, CSS, images).
- View activity via **Browser DevTools → Network tab**.
    
### HTTP Basic Authentication

- Authentication handled by **web server**, not application.
- Server returns:
    
    - **401 Unauthorized**
    - `WWW-Authenticate: Basic realm="..."`
        
- Credentials are sent as:
    
    - `Authorization: Basic <base64(username:password)>`
    
#### Methods to authenticate:

1. `curl -u user:pass URL`
2. `curl http://user:pass@URL`
3. `curl -H "Authorization: Basic base64..." URL`
    
_Base64 ≠ encryption; simply encoding._

### Authorization Header

- Basic Auth example:  
    `Authorization: Basic YWRtaW46YWRtaW4=`
- Modern apps use:  
    `Authorization: Bearer <JWT token>`
- Header can be manually added using:  
    `curl -H "Authorization: ..." URL`
    
### GET Parameters

- Data appended to URL:  
    `search.php?search=le`
- Used for searching, filtering, API calls, etc.
- Inspect via **DevTools → Network**.
- Can replicate request with cURL:  
    `curl 'http://IP/search.php?search=le' -H 'Authorization: Basic ...'`
    
### Convenient tools:

- **Copy as cURL** (from DevTools).
- **Copy as Fetch** (for JS testing in console).


## POST Method 
### Why POST is used

- Sends data in **request body**, not URL.
- Benefits:
    
    - **Not logged** in URLs (good for large data/files).
    - **Less encoding** — supports binary data.
    - **More data** — avoids URL length limits.
 
### POST in Login Forms

- Modern login forms send credentials using POST body:  
    `username=admin&password=admin`
    
- Use cURL to send POST data:  
    `curl -X POST -d 'username=admin&password=admin' URL`
    
- Add `-L` to follow redirects.
    
### Authenticated Cookies

- Server sets cookie after login:  
    `Set-Cookie: PHPSESSID=xxxx; path=/`
    
- Use cookie in later requests:
    
    - `curl -b 'PHPSESSID=xxxx' URL`
    - or `curl -H 'Cookie: PHPSESSID=xxxx' URL`
        
- Browser DevTools → Storage tab allows editing/adding cookies manually.

_Valid cookie = authenticated session → often enough to bypass login._
### POST Requests with JSON

- Some APIs send JSON data:  
    `{"search":"london"}`
- Must set header:  
    `Content-Type: application/json`
- Example cURL request:  
    `curl -X POST -d '{"search":"london"}' -b 'PHPSESSID=xxx' -H 'Content-Type: application/json' URL/search.php`
    
### Using DevTools & Fetch

- DevTools → Network → right-click request → **Copy as cURL** or **Copy as Fetch**.
- Allows quick replication of POST requests for testing.


## CRUD API 

### APIs

- APIs allow interaction with backend/database via HTTP methods.
- Endpoint structure often:  
    `api.php/table/row` → e.g., `/city/london`.
  
### CRUD Operations

|Operation|HTTP Method|Purpose|
|---|---|---|
|**Create**|POST|Add new entry.|
|**Read**|GET|Retrieve entry/data.|
|**Update**|PUT (or PATCH)|Modify existing entry.|
|**Delete**|DELETE|Remove entry.|

### 1. READ (GET)

- `curl http://IP/api.php/city/london`
- Returns JSON.
- Pipe to jq for formatting:  
    `curl -s URL | jq
- Empty string returns all rows: `/city/`

### 2. CREATE (POST)

- Send JSON + set Content-Type:  
    `curl -X POST URL -d '{"city_name":"HTB_City","country_name":"HTB"}' -H 'Content-Type: application/json'`
    
- Confirm by reading the new entry with GET.
  
### 3. UPDATE (PUT)

- Specify the row in URL, send JSON body:  
    `curl -X PUT URL/city/london -d '{"city_name":"New_HTB_City","country_name":"HTB"}' -H 'Content-Type: application/json'`
    
- PUT usually replaces entire entry.
- Might create new entry on some APIs if it doesn't exist.
    
### 4. DELETE (DELETE)

- Remove entry:  
    `curl -X DELETE URL/city/New_HTB_City`
    
- Reading afterward should return: `[]`
    
### Authentication

- Real APIs usually require:
    
    - **Cookies** (session)
    - **Authorization headers** (JWT/Bearer tokens)
        
- Without proper access control, modification/deletion becomes a vulnerability.


