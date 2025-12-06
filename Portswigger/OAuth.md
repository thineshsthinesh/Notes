
# What is OAuth ? 

OAuth is an authorization framework that let's application requests limited access to user's account on another application without sharing login credentials. User's can choose which data they want to share. 

**Example:** An application can request email contacts list so it can suggest other users in that contact list

Mostly used as third party authentication services, allowing users to login using different website 

# How does OAuth 2.0 work ? 

There is three parties involved : 

- **Client application** - The website that want to access the user's data 
- **Resource owner** - The user 
- **OAuth service provider** - The website that controls the user's data and access to it. They provide the API for authorization server and resource server 


**Steps** 

1. **The client application** requests access, specifying which **grant type** to use and what kind of access they want 
2. **User** is prompted to **login** in to the **OAuth service** and give **consent** for the **requested access** 
3. **The client application** receives a **unique access token** to access the **requested data**. This **varies** based on the **grant type**
4. **The client application** uses this access token to make **API calls** and fetch the relevant data from the **resource owner**

# OAuth grant types 

## What is OAuth grant type ? 

**OAuth grant types** are the **flow of stages** in OAuth process, The grant type **affects** how the client application **communicates** with OAuth service at each stage 


## OAuth scopes 

For any **OAuth grant type**, The client application has to specify the kind of actions it want to perform on the **scope** parameter of the **authorization request**

**The scope** value used are **unique** to each OAuth service. The format **vary between providers**. Some even use **full URI**. 

**for example** : when requesting read access to contacts, 

```bash
scope=contacts 
scope=contacts.read 
scope=contact-list-r 
scope=https://oauth-authorization-server.com/auth/scopes/user/contacts.readonly
```

But, when used in **authentication** , **Standardized OpenID Connect** scopes are used. for **example:** `openid profile` will grant **predefined** basic information about the user.


## Authorization code grant type


The client application and OAuth service use a **series of HTTP requests** that initiate the flow. The user is asked for consent, if accepted the client application is granted the **authorization code**. The **client application** exchanges this code with the OAuth service to receive the **access token**, which is used with **API calls** to fetch the user data. 

All the communication after that is happen **server-to-server** over a **secure pre-configured channel**, this secure channel is established when the client application registers with the **OAuth service**. a `client secret` is generated which is used **authenticate the client application** 

![[oauth-authorization-code-flow.jpg]]


### 1. Authorization request 

Client app redirects user to the OAuth provider:

```
GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=code&scope=openid%20profile&state=random_csrf_value
```

 **Important parameters**

- **client_id** – unique identifier assigned to the client.
- **redirect_uri** – where the authorization code will be sent; must be validated properly.
- **response_type=code** – specifies Authorization Code flow.
- **scope** – defines what user data is requested (e.g., openid, profile).
- **state** – CSRF protection; must be returned unchanged.

### 2. User Login & Consent

- User logs in to the OAuth provider.
- OAuth provider displays requested permissions based on scopes.
- User consents.
- If previously approved and session is active → auto-consent.

### 3. Authorization Code Grant (Authorization Server → Client App)8

After consent, browser redirects:

```http
GET /callback?code=a1b2c3d4e5f6g7h8&state=ae13d489bd00e3c24 HTTP/1.1 
Host: client-app.com
```

 **Notes**:

- **Authorization code** is short-lived and usable only once.
- **state** must be validated to prevent CSRF.

### 4. Access Token Request (Client Server → OAuth Server)

Client app exchanges authorization code for an access token via secure back-channel:


```http
POST /token HTTP/1.1 
Host: oauth-authorization-server.com 
… 
client_id=12345&client_secret=SECRET&redirect_uri=https://client-app.com/callback&grant_type=authorization_code&code=a1b2c3d4e5f6g7h8
```

 **Key parameters**

- **client_secret** – proves client’s identity
- **grant_type=authorization_code** – indicates this flow.
- **code** – the received authorization code.

### 5. Access Token Grant (OAuth Server → Client)

Successful response:

```json
{   
"access_token": "z0y9x8w7v6u5",   
"token_type": "Bearer",   
"expires_in": 3600,   
"scope": "openid profile" 
}
```

 **Notes:**

- The access token represents permissions delegated by the user.
- Expires in a short period (typically 1 hour).

### 6. API Call to Resource Server

Client app requests user data using the access token:

```http
GET /userinfo 
Host: oauth-resource-server.com
Authorization: Bearer z0y9x8w7v6u5
```


### 7. Resource Grant

If valid:

```json
{   
"username": "carlos",   
"email": "carlos@example.com" 
}
```

### Final step:

- Client uses this data to create or map a user account.
- User is now authenticated in the client application.


## Implicit grant type 


- The **Implicit Grant Type** provides the access token **directly** after user consent.
- No authorization code → token is sent immediately.
- Entire flow happens via **browser redirects**.
- **Less secure** than the Authorization Code flow because:
    
    - No server-to-server back-channel.
    - Access token is exposed in the browser and URL fragment.
        
- Used mainly for:
    
    - **Single-page applications (SPAs)**
    - **Native desktop/mobile apps**
    - Clients that **cannot securely store a client_secret**

![[oauth-implicit-flow.jpg]]


### 1. Authorization Request (Browser → Authorization Server)

The client app starts the flow by redirecting the user to the authorization endpoint:

```http
GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=token&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1 
Host: oauth-authorization-server.com`
```

 **Key difference**

- **response_type=token**  
    → requests access token _directly_, without authorization code.

### 2. User Login & Consent

Same as in the Authorization Code flow:

- User logs in to the OAuth provider.
- User sees requested permissions and chooses to consent or deny.

### 3. Access Token Grant (Authorization Server → Redirect URI)

If the user consents, the OAuth provider redirects the browser:

```http
GET /callback#access_token=z0y9x8w7v6u5&token_type=Bearer&expires_in=5000&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1 
Host: client-app.com
```

**Key differences:**

- Token is returned in the **URL fragment** (`#...`)  
    → not automatically sent to the server.
- JavaScript in the browser must extract the token.

 **Security issue:**

- URL fragment can be exposed to:
    - Browser history
    - JavaScript injection
    - Referrer leaks (in some cases)

### 4. API Call (Browser → Resource Server)

The client-side script sends the access token in an API request:

```http
GET /userinfo HTTP/1.1
Host: oauth-resource-server.com 
Authorization: Bearer z0y9x8w7v6u5
```

 **Note**:

- Unlike authorization code flow, this API call is also made **from the browser**, not the backend.

### 5. Resource Grant (Resource Server → Application)

If token is valid:

```json
{     
"username": "carlos",     
"email": "carlos@carlos-montoya.net" 
}
```

Client app receives user data and logs the user in or uses the data as required.

### Why Implicit Flow Is Less Secure

- No **client_secret** used.
- No **secure server-to-server** token exchange.
- Token is exposed in:
    
    - URL fragment
    - Browser logs
    - Browser extensions
    - Potential XSS vulnerabilities

## OAuth Authentication 

- OAuth was designed for authorization but is now commonly used for user authentication (e.g., “Log in with social media”).
- OAuth flows stay the same; the difference lies in how the client uses the returned data.
- From the user’s perspective, OAuth authentication works like SSO.

### How OAuth Authentication Works

1. **User selects social login**
    
    - Client requests identifying data (e.g., email) from the OAuth provider.
        
2. **Access token received**
    
    - Client uses the access token to request user info from **/userinfo**.
        
3. **User is logged in**
    
    - The returned identity data (email/username) is used to authenticate the user.
    - The access token effectively replaces a password.

## OAuth Authentication Vulnerabilities
### Why OAuth Vulnerabilities Arise

- OAuth is **flexible and vague by design** → many security-critical settings are optional.
- Developers must configure most security features manually.
- Lack of built-in security, heavy reliance on:
    
    - correct configuration
    - custom input validation
        
- Developers unfamiliar with OAuth can easily misconfigure it.
- Some grant types send sensitive data through the **browser**, increasing exposure and attack opportunities.
    
### Identifying OAuth Authentication

-  “Log in with \<social media]>” usually indicates OAuth.
- Most reliable method: proxy through **Burp** and observe requests
- The first request of any OAuth flow is to **/authorization**, containing OAuth parameters like:
    
    - **client_id**
    - **redirect_uri**
    - **response_type**
        

Example:

`GET /authorization?client_id=12345 &redirect_uri=https://client-app.com/callback &response_type=token &scope=openid%20profile &state=ae13d489bd00e3c24`

### Recon Tips

- Study all requests/responses in the OAuth flow.
- Identify the provider from the authorization server's hostname.
- Providers often have public documentation for their endpoints and configuration.
- Check standard discovery endpoints for configuration info:
    
    - `/.well-known/oauth-authorization-server`
    - `/.well-known/openid-configuration`
        
- These often return JSON describing supported features and potential attack surface.

## Exploiting OAuth authentication vulnerabilities
### Vulnerabilities in the OAuth Client Application 

 **General Issues**

- Even when using a secure OAuth provider, the **client application** may introduce vulnerabilities.
- OAuth is **loosely defined**, especially on the client side, leaving many opportunities for misconfiguration.
- Many optional parameters and settings in each flow increase the risk of insecure implementations.

#### Improper Implementation of the Implicit Grant Type

- Implicit grant sends the **access token via the browser** as a URL fragment.
- Intended mainly for **single-page apps**, but often (incorrectly) used in normal web apps due to simplicity.
- Client apps typically use JavaScript to extract the token, then send it to their server in a **POST request** to create a session.
- The server receives:
    
    - User ID
    - Access token
        
- But the server **has no password or secret** to validate against—so it implicitly trusts this data.
    
##### Resulting Vulnerability

- Because the POST request with token + user data is exposed in the browser, an attacker can intercept and **modify these parameters**.
    
- If the client application **does not verify** that the access token matches the claimed user:
    
    - Attacker can submit altered data.
    - Server trusts it.
    - **Attacker can impersonate any user**.

#### Lab: Authentication bypass via OAuth implicit flow
#lab
**APPRENTICE**


This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the client application makes it possible for an attacker to log in to other users' accounts without knowing their password.

To solve the lab, log in to Carlos's account. His email address is `carlos@carlos-montoya.net`.

You can log in with your own social media account using the following credentials: `wiener:peter`.

[ACCESS THE LAB](https://portswigger.net/academy/labs/launch/f12c0972c9b99d48a344c37883cce2e82b5a29c5fa1dbb51d0860363f805ad70?referrer=%2fweb-security%2foauth%2flab-oauth-authentication-bypass-via-oauth-implicit-flow)


**Writeup:** 

- Open **burpsuite** on the background and notice the traffic of the authentication flow 
- It uses **implicit grant type**
- The server uses **/authenticate** endpoint with email, username and **access token**
- By changing the **email** value to the victim account `carlos@carlos-montoya.net` we got the session cookie 
- We can access the **victim** account using the session cookie 
- *Lab solved*


###  Flawed CSRF Protection (OAuth) 

- OAuth flows _should_ use a **state** parameter as a CSRF token.
- **state** must be:
    
    - Unguessable
    - Tied to the user’s session
    - Returned unchanged at the end of the OAuth flow
        
- Purpose:
    
    - Prevent attackers from forcing a victim to complete an OAuth flow they did not initiate.
        

#### If state is missing

- OAuth flow becomes vulnerable to **CSRF-like attacks**.
- Attacker can:
    
    1. Start their own OAuth flow (using their social media account).
    2. Trick the victim into completing that flow.
    3. Victim’s account gets **linked to attacker’s OAuth identity**.
    
#### Impact Example

- Site supports login via password **or** social login (OAuth).
- Without state:
    
    - Attacker binds victim’s local account to attacker’s social account.
    - Attacker can then log in as the victim.


#### Lab: Forced OAuth profile linking

**PRACTITIONER**


This lab gives you the option to attach a social media profile to your account so that you can log in via OAuth instead of using the normal username and password. Due to the insecure implementation of the OAuth flow by the client application, an attacker can manipulate this functionality to obtain access to other users' accounts.

To solve the lab, use a CSRF attack to attach your own social media profile to the admin user's account on the blog website, then access the admin panel and delete `carlos`.

The admin user will open anything you send from the exploit server and they always have an active session on the blog website.

You can log in to your own accounts using the following credentials:

- Blog website account: `wiener:peter`
- Social media profile: `peter.wiener:hotdog`

[ACCESS THE LAB](https://portswigger.net/academy/labs/launch/6277e415ef43d61680e72e8facbeceacf6e3e4957f85c443470fa06ad977b666?referrer=%2fweb-security%2foauth%2flab-oauth-forced-oauth-profile-linking)


**Writeup :** 

- Login using given credentials 
- There is a attack social media option attack a social media and observe the **OAuth flow** 
- It seems the OAuth doesn't use **state** parameter to prevent **csrf attacks** 
- now start **interception** and try to link the social media once again 
- This time capture the **access code grant request** and drop it and generate a csrf PoC and the PoC to exploit server
- When the **admin visit that page** the social account will get **added to the admin account** 
- We can **login** using the social account to get **admin access** and delete the carlos user 


### Leaking Authorization Codes & Access Tokens 
#### Core Problem

- OAuth service fails to **validate redirect_uri** properly.
- Attacker can supply a malicious redirect_uri to **capture codes or tokens** sent through the victim’s browser.

#### How the Attack Works

- Authorization responses (codes/tokens) are sent to the URL in **redirect_uri**.
- If redirect_uri is weakly validated:
    
    - Attacker tricks victim into initiating an OAuth flow.
    - Code/token gets sent to an **attacker-controlled URL**.
        

#### Authorization Code Flow Impact

- Attacker steals **authorization code** before use.
- Attacker sends stolen code to the **legitimate client /callback** endpoint.
- Client application:
    
    - Exchanges code → token
    - Logs attacker in as the _victim_ (session is already valid)
        
- No need for attacker to know:
    
    - Client secret
    - Resulting access token

#### Impact

- Full account takeover on any client app linked to this OAuth provider.
- Attacker gains victim’s data and access to all connected services.
    

#### Important Note

- **state or nonce does NOT prevent this attack**
    
    - Attacker can generate valid state/nonce values from their own browser.


