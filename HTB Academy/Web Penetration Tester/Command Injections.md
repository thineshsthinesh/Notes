
# Intro to Command Injections 

## Intro to Command Injection — Minimal Technical Notes

### Command Injection Overview

- **Command Injection** allows execution of arbitrary OS commands on the back-end server.
- Considered **critical** due to potential full system and network compromise.
- Occurs when **user-controlled input** is used in system command execution without proper sanitization.

### What Are Injection Vulnerabilities

- Ranked **#3 in OWASP Top 10 Web App Risks**.
- Occur when user input is interpreted as part of executable code or queries.
- Allows attackers to **alter intended logic** of the application.

### Common Injection Types

|Injection Type|Description|
|---|---|
|OS Command Injection|User input becomes part of an OS command|
|Code Injection|User input executed as code|
|SQL Injection|User input used in SQL queries|
|XSS / HTML Injection|User input rendered directly in web pages|

- Other injection types include: LDAP, NoSQL, XPath, HTTP Header, IMAP, ORM, etc
- Root cause: **unsanitized user input in executable queries**.

### OS Command Injection

- Happens when user input directly or indirectly influences a system command.
- All major languages provide functions to execute OS commands.
- If input is not sanitized, attackers can escape command boundaries and execute arbitrary commands.

#### PHP Example (Vulnerable)

`<?php if (isset($_GET['filename'])) {     system("touch /tmp/" . $_GET['filename'] . ".pdf"); } ?>`

**Issue**

- `filename` is user-controlled.
- Passed directly to `system()` without sanitization.
- Allows command chaining and arbitrary command execution.

#### NodeJS Example (Vulnerable)

``app.get("/createfile", function(req, res){     child_process.exec(`touch /tmp/${req.query.filename}.txt`); })``

**Issue**

- `filename` from GET request used directly in command.
- No input validation or escaping.
- Vulnerable to OS command injection.

### Key Points

- Command injection is **language-agnostic**.
- Affects web apps, binaries, and thick clients alike.
- Same exploitation techniques apply across platforms.
- Any function executing OS commands with unsanitized input is a potential attack surface.

# Exploitation 

## Detection
### Detection Principle

- **Detection = Exploitation** for basic OS Command Injection.
- Inject additional commands and observe **changes in output**.
- If output deviates from normal behavior, command injection exists.
- Focus here is on **basic command injection**:
    
    - User input is directly passed to an OS command execution function
    - No sanitization or escaping is applied

### Detection Scenario

- Application provides a **Host Checker** utility.
- User supplies an IP address.
- Output resembles a `ping` command result.

**Likely back-end command**

	`ping -c 1 OUR_INPUT`

- If input is unsanitized, additional commands may be injected.

### Command Injection Detection Method

1. Provide **expected input** (e.g., IP address).
2. Append an **injection operator**.
3. Append a **new command**.
4. Observe response/output changes.

### Command Injection Operators

|Operator|Character|URL-Encoded|Execution Behavior|
|---|---|---|---|
|Semicolon|`;`|`%3b`|Executes both commands|
|New Line|`\n`|`%0a`|Executes both commands|
|Background|`&`|`%26`|Both (second often first output)|
|Pipe|`|`|`%7c`|
|AND|`&&`|`%26%26`|Second runs if first succeeds|
|OR|`||`|
|Sub-shell|`` `cmd` ``|`%60%60`|Both (Linux only)|
|Sub-shell|`$(cmd)`|`%24%28%29`|Both (Linux only)|

---

### Key Notes

- Injection operators work **across languages and frameworks**.
- Applicable to:
    
    - PHP, NodeJS, .NET, etc.
    - Linux, Windows, macOS back-ends
        
- **Exception**:
    
    - `;` does **not** work in Windows CMD
    - Works in Windows PowerShell

