
# Introduction 

## Introduction 

- Deobfuscation is essential for **code analysis** and **reverse engineering**.
- Obfuscated JavaScript is commonly used in:
    
    - Malware
    - Hidden payload retrieval
    - Red/Blue team scenarios
        
- Without deobfuscation, the real behavior of the code remains unclear.

### Module Flow

1. **Locate JavaScript Code**
2. **Introduction to Code Obfuscation**
3. **How to Deobfuscate JavaScript**
4. **Decoding Encoded Messages**
5. **Basic Code Analysis**
6. **Sending Basic HTTP Requests**


## Source Code 

### Web Technologies Overview

- **HTML** → Defines structure, fields, parameters of a web page.
- **CSS** → Controls design and layout
- **JavaScript** → Handles functionality and client-side logic (runs in background)

Even though code is client-side, it is rendered by the browser, so users don’t usually see it unless they inspect the source.
### Viewing Source Code

- Use **CTRL + U** in the browser to view HTML source.
- Useful for analyzing client-side functionality.
- HTML may contain:
    
    - Comments
    - Developer notes
    - Sensitive information
    
### HTML Example

- Page appears simple (“Secret Serial Generator”) with no visible functionality.
- Viewing source allows us to see hidden structure and code.

### CSS

- Can be:
    
    - **Internal** → inside `<style>` tags in HTML.
    - **External** → separate `.css` file linked via:

		 `<link rel="stylesheet" href="style.css">`
        
- Example internal CSS shown (font sizes, styling, etc.).
    
### JavaScript

- Can be:
    
    - **Inline/internal** → inside `<script>` tags
    - **External** → separate `.js` file referenced via:

		`<script src="secret.js"></script>`
        
- In this case, the page references **secret.js** externally.
- Opening **secret.js** reveals highly obfuscated code:

	`eval(function(p,a,c,k,e,d){ ... })`

→ indicates compressed/obfuscated JavaScript.

# Obfuscation 

## Code Obfuscation
### What Is Obfuscation?

- Technique used to **make code hard for humans to read** while keeping functionality the same.
- Often results in **slower performance**.
- Usually done using an **automatic obfuscation tool**.
- Obfuscators may:
    
    - Create a **dictionary** of all words/symbols
    - Reconstruct the script at runtime using lookups.
        
- Example: Simple JavaScript transformed into unreadable `eval(function...)` code.
    
### Why JavaScript Is Commonly Obfuscated

- Interpreted languages (Python/PHP/JS) are distributed in source form.
- Python/PHP → server-side (hidden).
- **JavaScript → client-side**, delivered in cleartext to the browser.
- Because JS is exposed to the user, obfuscation is widely used.
    
### Use Cases of Obfuscation

1. **Protect Intellectual Property**
    
    - Prevent copying or reverse engineering.
        
2. **Security Layer**
    
    - Hide authentication or encryption logic.
    - _Note: client-side auth/encryption is not recommended._
        
3. **Malicious Purposes (most common)**
    
    - Used to evade IDS/IPS detection.
    - Hide malicious behavior or payload retrieval.

## Basic Obfuscation

### General

- Obfuscation is usually automated using tools.
- Malicious actors and developers may create **custom obfuscators** for stronger concealment.
    
### Running JavaScript Code

Example code:

`console.log('HTB JavaScript Deobfuscation Module');`

- Tested in **JSConsole** → prints the string via `console.log()`.

### Minifying JavaScript

- Minification = compressing code into **one long line**.
- Reduces readability but keeps functionality.
- Useful for long scripts.
- Tools: [Javascript-minifier](https://www.toptal.com/developers/javascript-minifier).
- Minified scripts commonly use **.min.js** extension.
- Minification works for many languages, not just JS.

### Packing JavaScript (Obfuscation)

- Packing heavily obfuscates code.
- Example tool: [BeautifyTools](https://beautifytools.com/javascript-obfuscator.php#).
- Produces code like:

	`eval(function(p,a,c,k,e,d){ ... })`

### Characteristics of Packer Obfuscation

- Uses the signature **function(p,a,c,k,e,d)**.
- Converts symbols/words into a **dictionary/list**.
- Reconstructs original code at runtime.
- Main cleartext strings may still appear

### Verification

- Running packed code in JSConsole gives the same output as the original.

### Limitation

- Although readability is reduced, **strings remain visible**, revealing some functionality.
- Stronger obfuscation methods may be needed to hide strings.

## Advanced Obfuscation 

### Goal

- Hide all readable strings and fully conceal original functionality.
- Produce code that is extremely difficult to reverse.
    
### Using Obfuscator.io

- Visit **obfuscator.io**.
- Enable **String Array Encoding → Base64**.
- Paste code → click **Obfuscate**.
- Output is highly obfuscated:
    
    - Uses long variable names like `_0x1ec6`
    - Contains Base64-encoded string arrays
    - Uses decoding functions to reconstruct values dynamically
        
- No readable remnants of original code.
- Still runs the same when executed in **JSConsole**.
    
### Trying Different Settings

- Obfuscation strength can be increased using more options.
- Always test output in JSConsole to ensure functionality remains.

### Extreme Obfuscation Examples

- Example provided with:
    
    - Complex boolean/string concatenations
    - Heavy use of `![]`, `!![]`, `+[]`, etc.
        
- Code is extremely long and unreadable but still runs correctly.
- Demonstrates performance impact:
    
    - Heavily obfuscated code may run slowly.
    
### Performance Note

- Stronger obfuscation = slower runtime.
- Important when scripts must execute efficiently.

### Other Obfuscators

- **JJ Encode**
- **AA Encode**
- These produce very slow or extremely unreadable code.
- Used mainly when:
    
    - Bypassing filters
    - Hiding malicious behavior
    - Avoiding detection

## Deobfuscation 

### Goal

- Convert obfuscated/minified JavaScript back into readable, understandable code.
    
### Beautify (Pretty Print)

- Minified JS appears in **one long line**.
- Beautifying restores **proper indentation and structure**.
- Tools:
    
    - **Browser Dev Tools** (e.g., Firefox → CTRL+SHIFT+Z → select script → `{ }` Pretty Print)
    - Online tools (Prettier, Beautifier)
        
- Beautifying helps formatting but **does NOT remove obfuscation**.

 **Example**

- Given packed code using:

	 `eval(function(p,a,c,k,e,d){ ... })`
    
- Beautifiers format it but it remains unreadable due to obfuscation.
    
### Deobfuscation Tools

- Some tools can fully **unpack** or **decode** packed JavaScript.
- Example tool: **UnPacker**
    
    - Paste obfuscated code → click UnPack.
    - Produces clear code, e.g.:
        

		 `function generateSerial() {   var xhr = new XMLHttpRequest;   var url = "/serial.php";   xhr.open("POST", url, true);   xhr.send(null); }`
 **Tip**

- Remove empty lines before running through a deobfuscator.

### How Packing Is Reversed

- Tools detect the signature of **packer-style obfuscation**.
- Another method:
    
    - Locate the **return value** of the packed function.
    - Replace the execution with `console.log()` to print the unpacked code.

### Reverse Engineering

- Automated tools work well **only for basic obfuscation**.
- More complex or **custom obfuscators** require:
    
    - Manual analysis
    - Understanding of JS behavior
    - Decoding strings, evaluating functions step-by-step
        
- As obfuscation depth increases, **manual reverse engineering becomes essential**.


# Deobfuscation Examples
## Code Analysis 

### Deobfuscated Code

```javascript
function generateSerial() {   
	var xhr = new XMLHttpRequest;   
	var url = "/serial.php";   
	xhr.open("POST", url, true);   
	xhr.send(null); 
};
```

- File contains only one function: **generateSerial()**.
    
### Understanding the Code

#### Variables

- **xhr** → new `XMLHttpRequest`
    
    - Used for making web requests (AJAX).
        
- **url** → `"/serial.php"`
    
    - Endpoint on the same domain.
        

#### Functions

- `xhr.open("POST", url, true)`
    
    - Prepares a POST request to `/serial.php`.
        
- `xhr.send(null)`
    
    - Sends the request with **no POST data**.
        
### What the Function Does

- Sends a **POST request** to `/serial.php`.
- Does **not** send any data.
- Does **not** read or process any response.
- Likely intended for **future use** (no visible HTML element calls it).

### Usefulness of Analysis

- By deobfuscating and inspecting the function:
    
    - We reveal an **unreleased/hidden feature**.
    - Such endpoints often contain **bugs or vulnerabilities**.
    - We can manually replicate the request to test server-side behavior.

## Decoding 

### Context

- Server response returned encoded text:
	`ZG8gdGhlIGV4ZXJjaXNlLCBkb24ndCBjb3B5IGFuZCBwYXN0ZSA7KQo=`
    
- Obfuscated code often includes **encoded text blocks** that are decoded during execution.
- Common encoding types:
    
    - **Base64**
    - **Hex**
    - **ROT13 / Caesar cipher**

### 1. Base64

#### Characteristics

- Uses A–Z, a–z, 0–9, +, /
- Ends with **= padding** (multiple of 4 length)
- Very easy to spot.

#### Encode

	`echo TEXT | base64`

#### Decode

	`echo BASE64_STRING | base64 -d`

### 2. Hex

#### Characteristics

- Only characters: **0–9, a–f**
- Represents ASCII values (e.g., a = 61, b = 62)
#### Encode

	`echo TEXT | xxd -p`

#### Decode

	`echo HEX_STRING | xxd -p -r`

### 3. Caesar / ROT13

#### Characteristics

- Shifts letters by fixed amount.
- **rot13** shifts by 13.
- Results still resemble readable text (e.g., http → uggc).

#### Encode / Decode (same command)

	`echo TEXT | tr 'A-Za-z' 'N-ZA-Mn-za-m'`

### Encoding Detection

- Use online tools like **Cipher Identifier** to guess the encoding.
- Many other encoding types exist.
- Some obfuscation uses **encryption** instead of encoding:
    
    - Requires a key.
    - Hard/impossible to decode if key is not present in script.

