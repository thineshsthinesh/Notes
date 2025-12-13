
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

