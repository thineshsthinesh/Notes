
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

| Operator   | Character   | URL-Encoded | Execution Behavior               |
| ---------- | ----------- | ----------- | -------------------------------- |
| Semicolon  | `;`         | `%3b`       | Executes both commands           |
| New Line   | `\n`        | `%0a`       | Executes both commands           |
| Background | `&`         | `%26`       | Both (second often first output) |
| Pipe       | `           | `           | `%7c`                            |
| AND        | `&&`        | `%26%26`    | Second runs if first succeeds    |
| OR         | `           |             | `                                |
| Sub-shell  | `` `cmd` `` | `%60%60`    | Both (Linux only)                |
| Sub-shell  | `$(cmd)`    | `%24%28%29` | Both (Linux only)                |

### Key Notes

- Injection operators work **across languages and frameworks**.
- Applicable to:
    
    - PHP, NodeJS, .NET, etc.
    - Linux, Windows, macOS back-ends
        
- **Exception**:
    
    - `;` does **not** work in Windows CMD
    - Works in Windows PowerShell

## Injecting Commands

### Basic Injection Attempt

**Payload**

`127.0.0.1; whoami`

**Resulting Command**

`ping -c 1 127.0.0.1; whoami`

- `;` executes both commands sequentially.
- Verified locally: both `ping` output and `whoami` output are returned.

### Front-End Validation Encountered

- Web app rejects input not matching IP format.
- Error appears **without a new HTTP request**.
- Network tab shows **no request sent** → validation is client-side only.

**Conclusion**

- Input validation is enforced on the **front-end**, not the back-end.
### Bypassing Front-End Validation

**Method**

- Intercept and modify requests using a web proxy (Burp/ZAP).

**Steps**

1. Configure browser to proxy traffic.
2. Submit a valid IP (e.g. `127.0.0.1`) via UI.
3. Intercept request.
4. Send to Repeater.
5. Modify parameter to injected payload:
	 `127.0.0.1; whoami`
6. URL-encode payload.
7. Send request directly to back-end.

### Successful Injection

- Response includes:
    
    - Normal `ping` output
    - Output of injected command (`whoami`)
        
- Confirms **OS Command Injection vulnerability**.

### Key Takeaways

- Front-end validation **does not protect** against injections.
- Back-end input must be sanitized and validated.
- Command injection is confirmed when:
    - Additional command output appears in response.
- Custom HTTP requests easily bypass client-side controls.

## Other Injection Operators 

### AND Operator (`&&`)

**Behavior**

- Executes the second command **only if the first succeeds** (exit code `0`).

**Payload**

`127.0.0.1 && whoami`

**Executed Command**

`ping -c 1 127.0.0.1 && whoami`

**Result**

- Both commands execute successfully.
- Injection confirmed when both outputs appear.

**Note**

- If the first command fails, the second command will **not** run.

### OR Operator (`||`)

**Behavior**

- Executes the second command **only if the first fails** (exit code `!= 0`).

#### Case 1: First command succeeds

`ping -c 1 127.0.0.1 || whoami`

- `ping` succeeds → `whoami` not executed.
    

#### Case 2: First command fails

**Payload**

`|| whoami`

**Executed Command**

`ping -c 1 || whoami`

**Result**

- `ping` fails → `whoami` executes.
- Cleaner output (only injected command result).

**Use Case**

- Useful when injected payload breaks original command.
- Helps ensure execution of attacker command.
    
### Why Operator Choice Matters

- Different operators affect:
    
    - Execution flow
    - Output visibility
    - Reliability of injection
        
- Choosing the right operator can:
    
    - Reduce noise
    - Improve payload success
    - Bypass basic filtering

### Common Injection Operators by Type

|Injection Type|Common Operators|
|---|---|
|SQL Injection|`' ; -- /* */`|
|Command Injection|`; &&`|
|OS Command Injection|`; &|
|LDAP Injection|`* ( ) &|
|XPath Injection|`' or and not substring concat count`|
|Code Injection|`' ; -- /* */ $() ${} #{} %{} ^`|
|Directory Traversal|`../ ..\\ %00`|
|Object Injection|`; &|
|XQuery Injection|`' ; -- /* */`|
|Shellcode Injection|`\x \u %u %n`|
|Header Injection|`\n \r\n \t %0d %0a %09`|

### Key Takeaways

- `&&` → second command runs on success.
- `||` → second command runs on failure.
- OR-based payloads can give **cleaner results**.
- Injection operators are **reusable across multiple vulnerability types**.
- Operator effectiveness depends on:
    
    - OS
    - Shell
    - Application behavior


# Filter Evasion 

## Identifying Filters 
### Why Filters Exist

Applications often try to stop injections by:

- **Blacklisting characters** (`;`, `|`, `&`, etc.)
- **Blacklisting commands** (`whoami`, `ls`, `cat`, etc.)
- Using a **WAF (Web Application Firewall)**

Even with these defenses, poor implementation often makes them bypassable.
### Detecting Filter / WAF Behavior

#### Observation

- Payloads like:

	`127.0.0.1; whoami 
	`127.0.0.1 && whoami `
	`127.0.0.1 || whoami``

now return:

- `Invalid input`

#### What this tells us

- The request **reaches the backend**
- The backend **detects something malicious**
- The response is rendered inside the app UI → **likely application-level filtering**
- A full block page or IP warning would suggest a **WAF**

### Breaking Down the Payload

Original payload:

`127.0.0.1; whoami`

Components:

1. Valid IP → allowed
2. `;` → suspicious
3. Space → suspicious
4. `whoami` → suspicious command

So the block is caused by **characters, commands, or both**.

### Identifying Blacklisted Characters

#### Strategy: Binary Reduction

Remove parts of the payload until it works.
#### Step 1: Test base input

`127.0.0.1`

✅ Works

### Step 2: Add one character at a time

`127.0.0.1;`

❌ **Invalid input**

**Conclusion**

- `;` is **blacklisted**
    
## What This Means

- The filter is likely doing something like:

	`$blacklist = [';', '&', '|', ...]; if (strpos($_POST['ip'], $char) !== false) {     echo "Invalid input"; }`

- The check is:
    
    - Simple
    - Character-based
    - Not context-aware

This is **good news** from an attacker’s perspective.

### Next Logical Tests

You should now test **each operator individually**:

| Operator       | Expected Result |
| -------------- | --------------- |
| `;`            | ❌ blocked       |
| `&&`           | ❓               |
| `              |                 |
| `              | `               |
| `&`            | ❓               |
| newline `%0a`  | ❓               |
| subshell `$()` | ❓               |

This tells you:

- Which operators are filtered
- Which are still usable
- Whether encoding helps
### Key Takeaways

- “Invalid input” ≠ safe application
- Blacklists are:
    
    - Easy to fingerprint
    - Easy to bypass
    
- Always:
    
    - Reduce payloads
    - Test one character at a time
    - Compare behavior

## Bypassing Space Filters 

When applications blacklist **spaces**, attackers can still separate command arguments using alternative shell features. Below are **practical, commonly working bypasses** (Linux-focused, but some apply elsewhere).

### 1. Using a Newline as the Injection Operator

If `;`, `&&`, `||` are blocked, **newline** is often still allowed.

- Newline (URL-encoded): `%0a`

**Test**

`127.0.0.1%0a`

✅ Works → newline is **not blacklisted** and can be used as the command separator.

### 2. Identifying the Space Filter

Attempting:

`127.0.0.1%0a whoami`

❌ `Invalid input`

This confirms:

- Newline is allowed
- **Space character is blocked**

### 3. Space Bypass Techniques

#### A) Tabs Instead of Spaces

Tabs are treated as whitespace by shells.
- Tab (URL-encoded): `%09`

**Payload**

`127.0.0.1%0a%09whoami`

✅ Works  
**Why:** Shell interprets tab as a space.

#### B) Using `$IFS` (Internal Field Separator)

`$IFS` expands to a space/tab by default.

**Payload**

`127.0.0.1%0a${IFS}whoami`

✅ Works  
**Why:** Variable expansion recreates whitespace at runtime.

> Tip: `${IFS}` is safer than `$IFS` if certain characters are filtered.

#### C) Bash Brace Expansion

Brace expansion implicitly separates arguments.

**Local Example**

`{ls,-la}`

**Injection Payload**

`127.0.0.1%0a{ls,-la}`

✅ Works  
**Why:** Bash expands `{cmd,arg}` → `cmd arg` without literal spaces.

### Why These Bypasses Work

- Filters often check **raw input**, not **shell-expanded input**
- Shell features (`IFS`, tabs, braces) recreate spaces **after** filtering
- Blacklists rarely cover **all valid shell syntax**
### Key Takeaways

- Space filters are **weak defenses**
- Common bypasses:
    
    - `%09` (tab)
    - `${IFS}`
    - Brace expansion `{cmd,arg}`
        
- Combine with allowed operators (like newline `%0a`) for clean execution
- Always test **one character at a time** to fingerprint filters

## Bypassing Other Blacklisted Characters

When filters block characters like `/`, `\`, or `;`, we can **generate those characters indirectly** at runtime instead of typing them literally. Below are **reliable, real-world bypass techniques** for Linux and Windows.

### 1. Linux — Using Environment Variable Substrings

Shell variables often **contain useful characters**. We can extract **exact characters** using substring syntax:

`${VAR:offset:length}`

#### Getting a Slash `/`

The `$PATH` variable usually starts with `/`:

`${PATH:0:1}`

 **Expands to:**

`/`

Other useful variables:

- `${PWD}`
- `${HOME}`
    
#### Getting a Semicolon `;`

Some environment variables contain `;`, such as `LS_COLORS`.
Example:

`${LS_COLORS:10:1}`

Expands to:

`;`

**Use:**

`printenv`

to inspect all environment variables and find characters you need.

#### Practical Payload Example

Bypassing **semicolon + space** filters:

`127.0.0.1${LS_COLORS:10:1}${IFS}`

Expands to:

`127.0.0.1;` 

✔ Filter bypassed  
✔ Injection operator restored

### 2. Windows — CMD Environment Variable Expansion

Windows variables can also be sliced.
#### Getting a Backslash `\` (CMD)

`%HOMEPATH:~6,-11%`

Why it works:

- `%HOMEPATH%` → `\Users\htb-student`
- Slice isolates the `\`
### 3. Windows — PowerShell Character Indexing

PowerShell treats strings as arrays.
#### Getting a Backslash `\`

`$env:HOMEPATH[0]`

#### Exploring variables

`Get-ChildItem Env:`

Use indexing to extract any needed character.
### 4. Character Shifting (Linux)

When a character is blocked, you can **derive it from a nearby ASCII value**.

### Example: Producing `\`

ASCII:

- `[` = 91
- `\` = 92

Command:

`echo $(tr '!-}' '"-~'<<<[)`

✔ Outputs:

`\`

## Key Takeaways

- Blacklists are **fundamentally weak**
- Characters can be:
    
    - Extracted from environment variables
    - Generated via substring slicing
    - Reconstructed using ASCII shifts
        
- These bypasses work because:
    
    - Filtering happens **before shell expansion**
    - The shell reconstructs characters **after validation**

