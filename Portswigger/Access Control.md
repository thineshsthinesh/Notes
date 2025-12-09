
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

## # Lab: Unprotected admin functionality with unpredictable URL
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


