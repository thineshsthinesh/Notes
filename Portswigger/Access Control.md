
# Access Control 

## What is Access Control?

- Restricts **who/what** can perform actions or access resources.
- Depends on:
    
    - **Authentication** → verifies identity.
    - **Session management** → tracks user requests.
    - **Access control** → checks if the user is allowed to perform an action.
        
- Broken access control = common & critical vulnerability.
    
## Types of Access Control

### 1. Vertical Access Control

- Restricts **functions** based on **user roles**.
    
    - Example: Admin can delete users; normal user cannot.
        
- Enforces business rules (least privilege, separation of duties).
    

### 2. Horizontal Access Control

- Restricts **resources** to specific users.
    
    - Example: User can access **their own** bank account, not others'.
        

### 3. Context-Dependent Access Control

- Based on **application state** or **interaction flow**.
    
    - Example: User cannot edit shopping cart **after** payment.
    
## Broken Access Control Examples

### Vertical Privilege Escalation

- User accesses restricted admin-level functionality.
    
    - Example: Normal user accessing admin page to delete accounts.
        

### Unprotected Functionality

- Sensitive functions are not protected at all.
    
    - Example: Visiting:

- - `https://insecure-website.com/admin`
        
        accessible to any logged-in user.
        
- Admin URLs might be exposed via:
    
    - **robots.txt**

		`https://insecure-website.com/robots.txt`
    
- **URL brute-forcing** using wordlists.

## Lab: Unprotected admin functionality 
#lab #bac

*APPRENTICE*

LAB **solved**

This lab has an unprotected admin panel.
Solve the lab by deleting the user `carlos`.

### Writeup : 

- Navigate through the web application
- on **/robots.txt** it discloses the path **/administrator_panel**
- then go to that endpoint and we have full **admin access** 
- Delete the **carlos** user 
- Done 


## Obscured URLs Are Not Access Control

- Hiding sensitive functionality using unpredictable URLs is **not effective**.
- Example hidden admin URL:
	 `https://insecure-website.com/administrator-panel-yb556`
- Even if difficult to guess, users can still discover it.

### How Hidden URLs Leak

- URLs may appear in:
    
    - JavaScript files
    - HTML source
    - API responses
    - Comments
    - Logs

### Example JavaScript Leak

`var isAdmin = false; if (isAdmin) {     var adminPanelTag = document.createElement('a');     adminPanelTag.setAttribute('href', 'https://insecure-website.com/administrator-panel-yb556'); }`

- Script contains admin URL.
- Even non-admin users can **view source** or read JS files.
- So the “hidden” URL is fully exposed.
    
### Key Point

- Obscurity ≠ security.
- Access control must be **enforced server-side**, not hidden client-side.

##  Lab: Unprotected admin functionality with unpredictable URL
#lab #bac 

*APPRENTICE*

LAB **Solved**

This lab has an unprotected admin panel. It's located at an unpredictable location, but the location is disclosed somewhere in the application.

Solve the lab by accessing the admin panel, and using it to delete the user `carlos`.

### Writeup:

- Navigate to the URL 
- Look at the source code using *Ctrl + U* 
- There is a JavaScript function which leaks admin path 
```js
var isAdmin = false; 
if (isAdmin) { 
var topLinksTag = document.getElementsByClassName("top-links")[0];
var adminPanelTag = document.createElement('a'); adminPanelTag.setAttribute('href', '/admin-9561sr'); adminPanelTag.innerText = 'Admin panel'; topLinksTag.append(adminPanelTag); var pTag = document.createElement('p'); pTag.innerText = '|'; topLinksTag.appendChild(pTag); 
} 
```
- Which reveals the **/admin-9561sr** 
- We got access to the **admin panel** 
- Delete the **carlos** user 
- Done 

## Parameter-Based Access Control 

### What it is

Some applications assign a user’s role or permissions at login, then store that role in a **user-controllable location**, such as:

- Hidden form fields
- Cookies
- Query-string parameters

Example URLs:

`https://insecure-website.com/login/home.jsp?admin=true https://insecure-website.com/login/home.jsp?role=1`

---

### Why it's insecure

- Users can **modify** these values.
- Application then incorrectly grants access based on tampered parameters.
- Leads to unauthorized access, such as:
    
    - Accessing admin panels
    - Performing privileged actions
        
### Key issue

- Access control must be enforced **server-side**, not based on client-modifiable values.

## Lab: User role controlled by request parameter

*APPRENTICE*

LAB **Solved**

This lab has an admin panel at `/admin`, which identifies administrators using a forgeable cookie.

Solve the lab by accessing the admin panel and using it to delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

### Writeup : 

- Open and login using the given credentials 
- When seeing the cookies there is a **Admin** which is said to **false**
- Change that to **true** and access **/admin**
- Delete **carlos** user lab solved 
- Done 

## Lab: User role can be modified in user profile

*APPRENTICE*

LAB **Solved**

This lab has an admin panel at `/admin`. It's only accessible to logged-in users with a `roleid` of 2.

Solve the lab by accessing the admin panel and using it to delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`


### Writeup :

- Run **burpsuite on the background** and login in to the application using given credentials
- There is a **email change** feature when changing email it responds with role id which is interesting 
- with the email on the **change email** request add **"roleid" : 2** to try to change our role id 
- It successfully got changed and we got access to **/admin** page 
- Delete the **carlos** user 
- Done 



## Broken access control resulting from platform misconfiguration 

- Some applications enforce access controls **at the platform layer**, restricting access to specific **URLs and HTTP methods** based on the user’s role.
    
- Example rule

		`DENY: POST, /admin/deleteUser, managers`
    
    This denies POST access to `/admin/deleteUser` for users in the _managers_ group.
    
- Various things can go wrong, leading to **access control bypasses**.
- Some application frameworks support **non-standard HTTP headers** that can override the URL in the original request, such as:
    
    - `X-Original-URL`
    - `X-Rewrite-URL`
        
- If a website uses **front-end controls** to restrict access based on the URL, but the application allows the URL to be overridden via a request header, the restriction can be bypassed
- Example bypass request:


```
POST / HTTP/1.1 
X-Original-URL: /admin/deleteUser
```

## Lab: URL-based access control can be circumvented

*PRACTITIONER*

LAB **Solved**

This website has an unauthenticated admin panel at `/admin`, but a front-end system has been configured to block external access to that path. However, the back-end application is built on a framework that supports the `X-Original-URL` header.

To solve the lab, access the admin panel and delete the user `carlos`.

### Writeup 

- Try accessing the admin panel it gives a **access denied** 
- we try to use the header **X-Orignal-URL** to **/admin** then try accessing / we got the admin page 
- It seems the delete user path is **/admin/delete?username** so we modify the request like 
```
GET /?username=carlos HTTP/2
Host: 0a58006b0452731380347c50002300ee.web-security-academy.net
X-Original-Url: /admin/delete
Cookie: session=4SmNg8M3gyHlsjDhcOHASXvesgobyhbD
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:146.0) Gecko/20100101 Firefox/146.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Sec-Gpc: 1
Referer: https://0a58006b0452731380347c50002300ee.web-security-academy.net/
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Te: trailers
```

that deletes the **Carlos** user and we got access 



- Attack can target the **HTTP method**.
- Front-end controls restrict based on **URL + method**.
- Some sites allow actions via **different methods**.
- If attacker uses **GET (or another method)** on a restricted URL, they can **bypass platform-layer access control**.
- Done

## Lab: Method-based access control can be circumvented

*PRACTITIONER*

LAB **Solved**

This lab implements access controls based partly on the HTTP method of requests. You can familiarize yourself with the admin panel by logging in using the credentials `administrator:admin`.

To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator.

### Writeup: 

- When **exploring** the admin page panel using admin credentials we came across a user upgrade functionality 
- It's a post request to upgrade the user 
- We just login as wiener and change the request method 
- We then use the URL to make a request to **/admin-roles?username=wiener&action=upgrade**
- Done 


## URL-Matching Discrepancies

**Issue**

- Different components may interpret request paths differently.
- Access control may fail if endpoint matching is inconsistent.

**Common Causes**

- **Case sensitivity mismatch**
    
    - `/ADMIN/DELETEUSER` mapped to `/admin/deleteUser`
    - Backend tolerant, access control strict → bypass
        
- **Spring `useSuffixPatternMatch`**
    
    - Enabled by default prior to Spring 5.3
    - `/admin/deleteUser.anything` maps to `/admin/deleteUser`
    - Access control may not apply correctly to suffixed paths
        
- **Trailing slash handling**
    
    - `/admin/deleteUser` vs `/admin/deleteUser/`
    - Treated as different endpoints on some systems
    - Appending `/` may bypass restrictions

**Impact**

- Unauthorized access to protected functionality due to path normalization differences.
    
## Horizontal Privilege Escalation

**Definition**

- A user accesses resources belonging to another user of the same privilege level.

**Example**

`https://insecure-website.com/myaccount?id=123`

- Changing `id=123` to another user’s ID may expose their data.

**Typical Cause**

- Missing authorization checks on user-controlled identifiers.

**Vulnerability Type**

- **IDOR (Insecure Direct Object Reference)**
    
    - Direct use of user-supplied parameters to access resources
    - No validation that the resource belongs to the authenticated user

**Impact**

- Unauthorized data access
- Account data exposure
- Abuse of user-specific functionality

## Lab: User ID controlled by request parameter

*APPRENTICE*

LAB **Solved**


This lab has a horizontal privilege escalation vulnerability on the user account page.

To solve the lab, obtain the API key for the user `carlos` and submit it as the solution.

You can log in to your own account using the following credentials: `wiener:peter`

### Writeup: 

- Login using the given credentials on **my account** 
- Notice the GET request https://0a59008503b2ef5f802c4954003c0024.web-security-academy.net/my-account?id=wiener 
- Change the id parameter to **carlos** we got acess as carlos 
- submit the API key of carlos 
- Done 

## Unpredictable Identifiers (GUIDs) — Security Note

- Some applications use **globally unique identifiers (GUIDs)** instead of predictable values (e.g., incrementing IDs) to reference users or objects.
- This design **reduces the risk of ID guessing and enumeration**, as GUIDs are not easily predictable.
- However, **security depends on secrecy**, not just randomness.
    

**Key Risk**

- GUIDs may be **disclosed elsewhere in the application**, such as:
    
    - User messages
    - Reviews or comments
    - Activity feeds
    - Public profiles or logs
        
**Impact**

- If an attacker can obtain another user’s GUID, they may:
    
    - Access or manipulate that user’s data
    - Bypass access controls
        
- This leads to **Insecure Direct Object Reference (IDOR)** vulnerabilities.

## Lab: User ID controlled by request parameter, with unpredictable user IDs

*APPRENTICE*

LAB **Solved**

This lab has a horizontal privilege escalation vulnerability on the user account page, but identifies users with GUIDs.

To solve the lab, find the GUID for `carlos`, then submit his API key as the solution.

You can log in to your own account using the following credentials: `wiener:peter`

### Writeup 

- Login using given credentials 
- Notice that the **my-account** has id parameter which GUID (Globally Unique identifier)
- Just explore the website it seems there is a post where **carlos** is the author 
- Clicking on it revealed a id parameter 
- replace the **my-account** id with the new found id and we got access to **carlos** dashboard 
- Copy the API key and submit 
- Done 


## Lab: User ID controlled by request parameter with data leakage in redirect

*APPRENTICE*

LAB **Solved**


This lab contains an access control vulnerability where sensitive information is leaked in the body of a redirect response.

To solve the lab, obtain the API key for the user `carlos` and submit it as the solution.

You can log in to your own account using the following credentials: `wiener:peter`

### Writeup 

- Login using the given credentials 
- notice the dashboard has the parameter id set to **wiener** 
- Change that to **carlos** and intercept the response it contains the **API Key** of carlos 
- submit the **API Key**
- Done 

## Horizontal → Vertical Privilege Escalation

- **Horizontal escalation**: Accessing another user’s account at the same privilege level.
- **Vertical escalation**: Gaining higher privileges (e.g., admin access).

**Link between them**

- Horizontal access can be leveraged to compromise a **more privileged user**.
- If the targeted user is an **administrator**, horizontal → vertical escalation occurs.

**Common technique**

- **Parameter tampering**:

		/myaccount?id=456`
    
    - Changing `id` to another user’s ID.

**Impact when targeting admin**

- Access to admin account page.
- Possible outcomes:
    
    - View or reset admin password.
    - Change admin account details.
    - Direct access to privileged/admin-only functionality.
## Lab: User ID controlled by request parameter with password disclosure

*APPRENTICE*

LAB **Solved**


This lab has user account page that contains the current user's existing password, prefilled in a masked input.

To solve the lab, retrieve the administrator's password, then use it to delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

### Writeup

- Login in using the given credentials 
- there is a **change password** feature which pre filled the password 
- Change the **id** from **wiener** to **administrator** 
- The **admin password** is leaked on the page 
- Login using **admin credentials** 
- Access the **admin panel** and delete the **carlos** user 



## Insecure Direct Object References (IDOR) 

- IDOR is a **type of access control vulnerability**.
- Occurs when an application **uses user-supplied input to directly access objects**.
- An attacker can **modify this input** to gain **unauthorized access** to other objects.
- Results from **improper access control enforcement**.
- Popularized through inclusion in the **OWASP Top Ten (2007)**.
- Represents a common **implementation flaw** that enables **access control bypass**.


## Lab: Insecure direct object references

*APPRENTICE*

LAB **Solved**

This lab stores user chat logs directly on the server's file system, and retrieves them using static URLs.

Solve the lab by finding the password for the user `carlos`, and logging into their account.


### Writeup

- There is **live chat** feature which seems to store the chats 
- while inspecting through burp found out that it uses **numbers** to **identify** different user chats 
- Also there is **download feature** at the endpoint **/download-transcript/2.txt** 
- by changing the **2** to **1** we got **carlos** user's chat which leaks the password of that user 
- Login using that password 
- Done 


## Access Control Vulnerabilities in Multi-Step Processes 

- Occur when **multi-step workflows** have **inconsistent access control enforcement**.
- Common in processes requiring **multiple inputs** or **review/confirmation steps**.
- Some steps may be **properly protected**, while others are **left unprotected**.
- Applications may **incorrectly assume step order** is always followed.
- Attackers can **skip protected steps** and directly access an **unprotected later step**.
- Results in **unauthorized execution of sensitive actions** by submitting required parameters directly.

## Lab: Multi-step process with no access control on one step

*PRACTITIONER*

LAB **Solved**

This lab has an admin panel with a flawed multi-step process for changing a user's role. You can familiarize yourself with the admin panel by logging in using the credentials `administrator:admin`.

To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator.


### Writeup 

- As instructed login using **admin credentials** 
- there is **promotion feature** which can upgrade users 
- It requires confirmation next as well 
- We just inspect the whole flow on burp 
- It seems the whole process involves **3 stages** 
	1. **Access to admin panel** 
	2. **Select user and operation** 
	3. **confirmation** 
- We just login using our own account and repeat the final **POST** request 
- with the data "**action=upgrade&confirmed=true&username=wiener**" to **/admin-roles** page 
- We are now admin 
- Done

## Referer-Based Access Control

- Access control decisions are based on the **HTTP Referer header**.
- Referer indicates the **page that initiated the request**.
- Main page (e.g., `/admin`) may be **properly protected**, while sub-pages rely only on Referer checks.
- Applications **trust the Referer header** instead of enforcing real authorization.
- The Referer header is **fully attacker-controlled**.
- Attackers can **forge requests with a fake Referer** to access **sensitive endpoints**.
- Leads to **unauthorized access to protected functionality**.

## Lab: Referer-based access control

*PRACTITIONER*

LAB **Solved**


This lab controls access to certain admin functionality based on the Referer header. You can familiarize yourself with the admin panel by logging in using the credentials `administrator:admin`.

To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator.

### Writeup

- Use admin credentials to **all the functionality** and run **burp** in the background
- There is a **user promotion function** which is interesting 
- **upgrade** carlos user to admin 
- inspect the traffic there is a traffic to **/admin-roles?username=carlos&action=upgrade**
- Now login with the normal user credentials 
- Make request such that **/admin-roles?username=wiener&action=upgrade**
- We got "unauthorized"  as response 
- Try changing the Referer header to match the admin request as abc.web-security-academy.net/admin
- It went through and we became **admin** 
- Done 


## Location-Based Access Control

- Access control is enforced based on the **user’s geographical location**.
- Common in **banking** and **media services** due to legal or business restrictions.
- Can often be **bypassed using VPNs, web proxies**, or **client-side geolocation manipulation**.
- Relies on **weak trust assumptions** about user location.

## Preventing Access Control Vulnerabilities 

- Do **not rely on obfuscation** as a security control
- **Deny access by default** unless a resource is explicitly public.
- Use a **centralized, application-wide access control mechanism**.
- Require developers to **explicitly define allowed access** for each resource.
- **Audit and test access controls** regularly to verify correct behavior.


