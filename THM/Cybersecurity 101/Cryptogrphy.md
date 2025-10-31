
# Cryptography Basics 

## Importance of cryptography

**Purpose:** Secure communication against adversaries.

**Goals:**

- **Confidentiality** – hide data
- **Integrity** – prevent tampering
- **Authenticity** – verify identity

**Examples:**

- Encrypted logins (TryHackMe)
- SSH (Secure Shell) tunnels
- Bank site certificates (HTTPS/TLS)
- Hashes for file integrity

**Data Protection:** Encrypt **at rest** & **in motion**

**Compliance Standards:**

- **PCI DSS** – _Payment Card Industry Data Security Standard_ (credit cards)
- **HIPAA** – _Health Insurance Portability and Accountability Act_ (USA health data)
- **HITECH** – _Health Information Technology for Economic and Clinical Health Act_ (USA health data)
- **GDPR** – _General Data Protection Regulation_ (EU data)
- **DPA** – _Data Protection Act_ (UK data)

Q: What is the standard required for handling credit card information ? 
A: PCI DSS

## Plaintext to Ciphertext


![[5f04259cf9bf5b57aed2c476-1725293744539.svg]]

![[5f04259cf9bf5b57aed2c476-1725293763258.svg]]

- **Plaintext:** Original readable data (text, image, file, etc.)
- **Ciphertext:** Encrypted, unreadable form of plaintext
- **Cipher:** Algorithm used for encryption & decryption
- **Key:** Secret value used by cipher to transform data
- **Encryption:** Converts plaintext → ciphertext (using key + cipher)
- **Decryption:** Converts ciphertext → plaintext (using key + cipher)

Q: What do you call the encrypted plaintext ? 
A: ciphertext

Q: What do you call the process that returns the plaintext ? 
A: decryption 

## Historical Ciphers

### Caesar Cipher s

- **Origin:** Ancient cipher from 1st century BCE.
- **Idea:** Shift each letter by a fixed number (**key**) → new letters.
    - Example: **Plaintext:** TRYHACKME → **Key:** 3 → **Ciphertext:** WUBKDFNPH
- **Encryption:** Shift right by key value.
- **Decryption:** Shift left by same key.
- **Keys:** Only **25 possible keys** (since 26 letters → one full rotation).
- **Weakness:** Easily broken by **brute force**.
- **Other historical ciphers:**
    - **Vigenère Cipher** (16th century)
    - **Enigma Machine** (WWII)
    - **One-Time Pad** (Cold War)

Q: Knowing that XPRCTCRGNEI was encrypted using Caesar Cipher, what is the original plaintext ? 
A: ICANENCRYPT

## Types of Encryption 

###  Symmetric Encryption (Private Key Cryptography)

- **Same key** used for encryption & decryption.
- Key must be kept **secret** → hard to share securely.

![[5f04259cf9bf5b57aed2c476-1725293928434.svg]]

- **Examples:**
    - **DES** – 56-bit key, broken in 1999 (<24 hrs).
    - **3DES** – 168-bit key (effective 112-bit), deprecated (2019).
    - **AES** – Standard since 2001; key sizes: 128 / 192 / 256 bits.
- **Problem:** Key distribution & scalability.
### Asymmetric Encryption (Public Key Cryptography)

- Uses **two keys:**
    - **Public key** → encryption
    - **Private key** → decryption (kept secret)
- **Examples:** RSA, Diffie-Hellman, ECC
- **Key sizes:**
    - **RSA:** 2048 / 3072 / 4096 bits
    - **ECC:** 256-bit ≈ 3072-bit RSA security
- **Slower** than symmetric, but easier key sharing.
- Based on **one-way mathematical problems** (easy one way, infeasible to reverse).


Q: Should you trust DES ? (Yea/Nay)
A: Nay

A: When was AES adopted as an encryption standard ? 
A: 2001

## Basic Math 

### XOR (Exclusive OR)

- **Symbol:** ⊕ or ^
- **Rule:**
    - 0⊕0=0 0⊕1=1 1⊕0=1 1⊕1=0
- **Properties:**
    - A⊕A = 0
    - A⊕0 = A
    - **Commutative:** A⊕B = B⊕A
    - **Associative:** (A⊕B)⊕C = A⊕(B⊕C)
- **Use in Cryptography:**
    - **Encryption:** C = P ⊕ K
    - **Decryption:** P = C ⊕ K
    - (Simple symmetric method — key must be as long as plaintext)
### Modulo Operation (mod or %)

- **Definition:** X % Y → remainder of X ÷ Y
- **Examples:**
    - 25 % 5 = 0
    - 23 % 6 = 5
    - 23 % 7 = 2
- **Key Points:**
    - Not reversible (many x can satisfy x % n = r)
    - Result always in **0 ≤ result < divisor**
    - Widely used in cryptography (especially modular arithmetic with large numbers)

Q: What's 1001 ^ 1010 ?
A: 0011

Q: What's 118613842 % 9091 ? 
A: 3565

Q: What's 60 % 12 ? 
A: 0

---
# Public Key Cryptography Basics 

**In Cybersecurity:**

- **Authentication:** Verify sender’s identity.
- **Authenticity:** Ensure source is genuine.
- **Integrity:** Detect any modification.
- **Confidentiality:** Prevent eavesdropping or data leaks.

**Cryptography Use:**
- **Symmetric (Private Key):** Ensures **Confidentiality**.
- **Asymmetric (Public Key):** Ensures **Authentication**, **Authenticity**, and **Integrity**.

## Use of Asymmetric Encryption

### Key Exchange – Quick Notes

- **Asymmetric cryptography** is used to **securely exchange keys** for **symmetric encryption**.
- Asymmetric = **slow**, so used **only once** during key setup.
- After key exchange → communication uses **faster symmetric encryption**.

**Analogy:**

- **Secret code** → Symmetric key & cipher
- **Lock** → Public key
- **Lock’s key** → Private key

**Purpose:** Share the symmetric key **without exposing it** to eavesdroppers.

**Real-world addition:**

- **Digital signatures** and **certificates** verify the server’s identity during key exchange.

Q: In the analogy presented, what real object is analogous to the public key ? 
A: lock 

## RSA

### RSA 


**Purpose:**  
Public-key encryption for secure data transmission over insecure channels.

### Security Basis

- Based on the hardness of **factoring large composite numbers**  
  \( n = p x q \), where \( p \) and \( q \) are large prime numbers.

###  Key Setup

1. **Choose two large primes:**   \( p, q \)
2. **Compute modulus:**   \( n = p x q \)
3. **Compute Euler’s Totient:**  $( \varphi(n) = (p - 1)(q - 1) )$
4. **Choose public exponent:**  ( e ), such that  $( \gcd(e, \varphi(n)) = 1 )$
5. **Compute private exponent:**  \( d \), where   $( e \times d \equiv 1 \pmod{\varphi(n)} )$
### Keys

- **Public Key:** \( (n, e) \)  
- **Private Key:** \( (n, d) \)
### Encryption / Decryption

- **Encryption:**   $( c \equiv m^{e} \pmod{n} )$
- **Decryption:**  $( m \equiv c^{d} \pmod{n} )$

### RSA CTFs

- In real-world RSA, \( p \) and \( q \) are **hundreds of digits long**.  
- **Typical RSA key sizes:** ≥ 2048 bits.  
- RSA is **slower** than symmetric encryption — mainly used to **exchange symmetric keys**.  
- Important variables to know in CTFs:  
  `p`, `q`, `n`, `e`, `d`, `m`, `c`  
- Useful tools: `RsaCtfTool`, `rsatool`  #ctftip
- **Essence:** RSA uses the public key \( (n, e) \) for encryption and the private key \( d \) for decryption. Its security relies on the **difficulty of factoring** \( n \).


Q: Knowing that p = 4391 and q = 6659. What is n ?
A: 29239669

Q: Knowing that p = 4391 and q = 6659. What is  ϕ(n) ? 
A: 29228620

## Diffie-Hellman Key Exchange 


**Diffie–Hellman Key Exchange (DHKE)**

**Purpose:** Establishes a shared secret key over an insecure channel for symmetric encryption.

**Steps:**

1. Agree on public values: prime **p**, generator **g**.
2. Alice picks private **a**, Bob picks private **b**.
3. Compute public keys: **A = gᵃ mod p**, **B = gᵇ mod p**.
4. Exchange A and B.
5. Compute shared key: **S = gᵃᵇ mod p** (Alice: Bᵃ mod p, Bob: Aᵇ mod p).
	![[5f04259cf9bf5b57aed2c476-1728439878360.svg]]
    

**Notes:**

- Real systems use large primes for security.
- Often paired with RSA — DH for key exchange, RSA for authentication.
- Used in TLS, SSH, and other secure protocols.


Q: Consider p = 29, g = 5, a = 12. What is A ? 
A: 7

Q: Consider p = 29, g = 5, b = 17. What is B ? 
A: 9

Q: Knowing that p = 29, a = 12, and you have B from the second question, what is the key calculated by Bob ? (key = B^a mod p)
A: 24

Q: Knowing that p=29, b = 17, and you have A from the first question, what is the key calculated by Alice ? (key = A^b mod p)
A: 24

## SSH 

**Server Authentication**

- SSH shows server fingerprint on first connect; verify it to avoid MITM.
- Key saved in `~/.ssh/known_hosts`.

**Client Authentication**

- Prefer **key-based** auth over passwords.
- Generate key: `ssh-keygen -t ed25519` (or `rsa`, `ecdsa`).
- Public key → server (`~/.ssh/authorized_keys`); private key → keep secret, protect with passphrase.

**Usage**

- Connect: `ssh -i privateKey user@host`.
- Set permissions: `chmod 600 privateKey`.
- Public key on target = persistent access (backdoor risk).

**Best Practices**

- Never share private key.
- Verify host fingerprint.
- Always use strong passphrase

Q: Check the SSH Private Key in ~/Public-Crypto-Basics/Task-5. What algorithm does the key use ? 
A: RSA

## Digital Signatures and Certificates 

**Purpose:**  
Used to verify the _authenticity_ (who sent it) and _integrity_ (not altered) of a digital document or message.

**How it works:**
- Based on **asymmetric cryptography**.
- Sender signs a message using their **private key**.
- Receiver verifies it using the sender’s **public key**.
- Common method: sender encrypts a **hash** of the document; receiver compares it with their own hash.

**Difference from Electronic Signatures:**

- Digital signatures use cryptographic keys → ensure integrity.
- Electronic signatures (e.g., pasted images) → provide no security.

**Certificates (Public Key Infrastructure - PKI):**

- Prove the identity of entities (e.g., websites).
- Issued and validated by **Certificate Authorities (CAs)** forming a _chain of trust_.
- Used in **HTTPS/TLS** to confirm website authenticity.
- Free certificates available via **Let’s Encrypt**.

Q: What does a remote web server use to prove itself to the client ? 
A: Certificate

Q: What would you use to get a free TLS certificate for your website ? 
A: Let's Encrypt 

## PGP and GPG 

**Purpose:**  
Used for **encryption**, **digital signing**, and **email security** (confidentiality + integrity).

- **PGP**: Original encryption software.
- **GPG**: Open-source implementation of the **OpenPGP** standard.

**Uses:**

- Encrypt files/emails for confidentiality.
- Digitally sign messages to verify authenticity and integrity.

**Key Generation (example):**

- Command: `gpg --full-gen-key`
- Choose key type (e.g., RSA, ECC), curve, and expiry.
- Provide name, email, and comment → generates **public** and **private** keys.

**Practical Usage:**

- Share **public key** with others for encrypted communication.
- Use **private key** to decrypt and sign messages.
- Backup keys securely.
- Import backup: `gpg --import backup.key`
- Decrypt message: `gpg --decrypt message.gpg`

**CTF Tip:**  #ctf-tip

If a private key is **passphrase-protected**, use `gpg2john` + **John the Ripper** to attempt cracking.

Q: Use GPG to decrypt the message in `~/Public-Crypto-Basics/Task-7`. What secret word does the message hold ?
commands used : `gpg --import tryhackme.key && gpt --decrypt message.gpg`
A: Pineapple 

---
# Hashing Basics 

## Hash Functions 


**Definition:**  

A _hash function_ takes input data of any size and produces a fixed-size output called a **hash** or **digest**.

**Key Properties:**

- **One-way:** Impossible (or impractical) to reverse.
- **Deterministic:** Same input → same output.
- **Avalanche Effect:** A tiny input change → drastically different output.
- **Fixed length:** Output size remains constant (e.g., 256 bits for SHA-256).
- **Fast computation:** Easy to compute for any input.

**Example:**

- Input files differ by 1 bit (`T` → `U`), but their MD5/SHA1/SHA256 hashes differ completely.
    
**Common Algorithms:**

- MD5 → Insecure
- SHA-1 → Insecure
- SHA-256, SHA-512 → Secure and widely used

**Output Encoding:**  
Usually shown in **hexadecimal** or **base64** format.

### Why Hashing Is Important

- **Data integrity:** Verifies data hasn’t changed (e.g., file verification).
- **Password protection:** Servers store password _hashes_, not passwords.
- **Digital signatures, certificates, and blockchain** use hashes for verification.

### Hash Collisions

- **Collision:** Two different inputs → same hash output.
- Due to the **pigeonhole principle** (limited outputs, unlimited inputs).
- **Good hash functions** make collisions extremely rare.
- **MD5 & SHA-1** are vulnerable; **use SHA-256 or stronger.**


Q: What is the SHA256 hash of the passport.jpg file in ~/Hashing-Basics/Task-2 ? 
A: 77148c6f605a8df855f2b764bcc3be749d7db814f5f79134d2aa539a64b61f02

Q: What is the output size in bytes of the MD5 hash function ? 
A: 16

Q: If you have an 8-bit hash output, how many possible hash values are there ? 
A: 265
## Insecure password storage authentication

**Main Uses:**

- **Password Storage (Authentication):** Verifies a user’s password without storing it in plain text. Unlike password managers, authentication only checks if the password is correct.
- **Data Integrity:** Ensures data hasn’t been changed or tampered with.
### Insecure Password Storage Practices

1. **Plaintext Storage:**
    
    - Storing passwords directly is highly insecure.
    - Example: _RockYou breach_ – over 14 million plaintext passwords leaked, now found in `rockyou.txt`.
        
2. **Using Deprecated Encryption:**
    
    - Encrypting passwords instead of hashing allows decryption.
    - Example: _Adobe breach_ – used weak encryption and stored password hints in plaintext, exposing user data.

3. **Using Insecure Hash Functions:**
    
    - Weak or unsalted hashes can be cracked easily.
    - Example: _LinkedIn 2012 breach_ – passwords hashed with unsalted **SHA-1**, later cracked.

**Best Practice:**  
Store passwords using **strong, salted, and slow hashing algorithms** like **bcrypt**, **scrypt**, or **Argon2** to protect against brute-force and rainbow table attacks.

Q: What is the 20th password in rockyou.txt ? 
A: qwerty

## Using Hashing for secure password storage

**Concept:**

- Instead of storing passwords, store their **hash values** using a secure hash function.
- If the database is leaked, attackers must **crack each hash** individually.
### Problem: Duplicate Passwords

- Same passwords → same hashes.
- Allows attackers to identify users with identical passwords and use **rainbow tables** to crack them.

**Rainbow Table:**

- A precomputed lookup table mapping hashes to plaintext passwords.
- Saves cracking time but requires large storage.
- Example sites: **CrackStation**, **Hashes.com**.
### Protection: Using a Salt

- **Salt:** A random, unique value added to each password before hashing.
- Ensures identical passwords create **different hashes**.
- Salts are **stored with the hash** and don’t need to be secret.
- Algorithms like **Bcrypt**, **Scrypt**, and **Argon2** handle salting automatically.

**Example Steps:**

1. Choose secure hashing (Argon2, Bcrypt, Scrypt, PBKDF2).
2. Generate a unique salt (e.g., `Y4UV*^(=go_!`).
3. Append salt to password (e.g., `AL4RMc10kY4UV*^(=go_!`).
4. Hash the combined string.
5. Store both the hash and the salt.
    
### Why Not Use Encryption?

- Encryption requires storing a **decryption key**.
- If the key is compromised, **all passwords can be decrypted**.
- Hashing avoids this risk since it’s **one-way** and cannot be reversed.

Q: Manually check the hash “4c5923b6a6fac7b7355f53bfe2b8f8c1” using the rainbow table above.
A: inS3CyourP4\$\$

Q: Crack the hash “5b31f93c09ad1d065c0491b764d04933” using an online tool.
A: tryhackme 

Q: Should you encrypt passwords in password-verification systems ? Yea/Nay 
A: Nay

## Recognizing password hashes 


**Goal:** Given a hash, identify its type, crack it, and recover the password.

**Recognition:**

- Use a mix of **context + tools** (hashID, online recognizers). Tools can be unreliable — verify results.
- Look for **prefixes**, length, and encoding. Web app DB hashes are more likely MD5 than NTLM.
- Consult resources (Hashcat example hashes, application docs) when unsure.

**Linux (/etc/shadow):**

- Only root-readable. Format: `user:$prefix$options$salt$hash:...`
- `$prefix` reveals algorithm (common prefixes):
    
    - `$y$` — yescrypt
    - `$gy$` — gost-yescrypt
    - `$7$` — scrypt
    - `$2b$`, `$2y$`, `$2a$`, `$2x$` — bcrypt
    - `$6$` — sha512crypt
    - `$md5` — SunMD5
    - `$1$` — md5crypt
- Example: `$y$j9T$<salt>$<hash>` → yescrypt with params.
    

**Windows (SAM):**

- Uses **NTLM** (MD4-based). Visual format similar to MD4/MD5 — use context
- SAM contains **NT hash** and legacy **LM hash**. Tools like **mimikatz** can dump these.

**Cracking aids & risks:**

- **Rainbow tables** work against unsalted hashes — salts prevent reuse and rainbow attacks.
- Use Hashcat/John the Ripper with correct hash mode and salt handling.
- Automated recognition + correct Hashcat mode → much faster cracking.

**Remember:**

- Always verify tool output with context (application, storage location).
- Research the application and format when in doubt — many hash types are ambiguous by appearance.

Q: What is the hash size in yescrypt ? 
A: 256

Q:: What's the Hash-mode listed for Cisco-ASA MD5 ? 
A: 2410

Q: What hashing algorithm is used in Cisco-IOS if it starts with \$9$ ? 
A: scrypt 

## Password Cracking 

**Fundamental idea**

- Hashes are not decryptable. You recover passwords by guessing inputs, hashing them (including salt if present) and comparing to the target hash.
- Use wordlists (e.g., rockyou.txt), rules, or brute force to generate guesses.

**Salt effects**

- A unique salt per user prevents rainbow-table reuse and forces cracking per-hash (attacker must include the salt when hashing guesses).
- Salts are stored with the hash and are not secret.

**Hardware: GPUs vs CPUs**

- GPUs accelerate hashing massively due to thousands of parallel cores — ideal for many hash types.
- Some algorithms (bcrypt, scrypt, Argon2 when tuned) are memory- or CPU-bound to limit GPU advantage.
- VMs often lack direct GPU access and incur overhead; cracking is faster on the host with native GPU access.

**Tools**

- **Hashcat** (GPU-accelerated) and **John the Ripper** (CPU-first) are the primary tools.
- Typical Hashcat syntax: `hashcat -m <hash_mode> -a <attack_mode> hashfile wordlist`
    
    - Example: `hashcat -m 3200 -a 0 hash.txt /usr/share/wordlists/rockyou.txt` (bcrypt mode, straight attack).

**Operational tips**

- Identify hash type (prefix, length, context) before choosing mode.
- Prefer targeted wordlists and rules before full brute force.
- Avoid online rainbow-table lookups if you want to learn; they shortcut the learning process.

**Security takeaway**

- Use salted, slow, memory-hard hashing (bcrypt/scrypt/Argon2) to increase cracking cost and mitigate GPU attacks.

Q: Use `hashcat` to crack the hash, `$2a$06$7yoU3Ng8dHTXphAg913cyO6Bjs3K5lBnwq5FJyA6d01pMSrddr1ZG`, saved in `~/Hashing-Basics/Task-6/hash1.txt`.
A: 85208520

Q: Use `hashcat` to crack the SHA2-256 hash, `9eb7ee7f551d2f0ac684981bd1f1e2fa4a37590199636753efe614d4db30e8e1`, saved in saved in `~/Hashing-Basics/Task-6/hash2.txt`.
A: halloween

Q: Use `hashcat` to crack the hash, `$6$GQXVvW4EuM$ehD6jWiMsfNorxy5SINsgdlxmAEl3.yif0/c3NqzGLa0P.S7KRDYjycw5bnYkF5ZtB8wQy8KnskuWQS3Yr1wQ0`, saved in `~/Hashing-Basics/Task-6/hash3.txt`.
A: spaceman 

Q: Crack the hash, `b6b0d451bbf6fed658659a9e7e5598fe`, saved in `~/Hashing-Basics/Task-6/hash4.txt`.
A: funforyou 

## Hashing for Integrity checking 

**Purpose:**

- Ensures a file or message hasn’t been changed or corrupted.
- Same input → same hash; even a 1-bit change → completely different hash.

**Integrity Checking:**

- Compare a file’s hash (e.g., **SHA256**) with the official or expected hash.
- Matching hashes confirm the file is authentic and unaltered.
- Commonly used for verifying downloaded files or detecting tampering.
- Can also identify duplicate files (same hash → identical content).

**Example:**

- `sha256sum file.iso` → verify output matches the checksum provided by the source (e.g., Fedora ISO checksum).

### HMAC (Hash-based Message Authentication Code)

**Definition:**

- Combines a **secret key** with a **hash function** to verify both **authenticity** and **integrity** of data.

**Purpose:**

- Confirms the sender’s identity (authenticity).
- Ensures the message hasn’t been altered (integrity).

**Process Overview:**

1. Pad the secret key to the block size.
2. XOR key with **ipad** and **opad** constants.
3. Inner hash: `H((K ⊕ ipad) || M)`
4. Outer hash: `H((K ⊕ opad) || inner_hash)`
5. Result = **HMAC value** (fixed length).

![[5f04259cf9bf5b57aed2c476-1725294564965.svg]]

**Formula:**  
`HMAC(K, M) = H((K ⊕ opad) || H((K ⊕ ipad) || M))`

**Use Cases:**

- API authentication, secure message verification, TLS, and digital signatures.

Q: What is SHA256 hash of `libgcrypt-1.11.0.tar.bz2` found in `~/Hashing-Basics/Task-7`?
A: 09120c9867ce7f2081d6aaa1775386b98c2f2f246135761aae47d81f58685b9c

Q: What's the hashcat mode number for HMAC-SHA512 (key = $pass) ? 
A: 1750 

Q: Use `base64` to decode `RU5jb2RlREVjb2RlCg==`, saved as `decode-this.txt` in `~/Hashing-Basics/Task-8`. What is the original word?
A: ENcodeDEcode 

---
# John the Ripper: The Basics 
#tools

## Basic Terms 

**What is a hash?**

- Fixed-length representation of data of any size produced by a hashing algorithm (e.g., MD4, MD5, SHA-1, NTLM).
- Example: `polo` → MD5 `b53759f3ce692de7aff1b5779d3964da`.

**Security property:**

- One-way function: easy to compute hash from input, hard to recover input from hash.
- Small input changes cause large, unpredictable changes in output (avalanche effect).

**Why “hard to reverse”?**

- Hashing is easy (P); finding preimages is computationally infeasible (NP-like difficulty in practice).

**How hashes are cracked:**

- **Dictionary attack:** Hash many candidate words and compare.
- **Brute force:** Try all possible combinations.
- **Rainbow tables:** Precomputed hash→plaintext tables (defeated by salts).
- Tools: **John the Ripper** (CPU-focused), **Hashcat** (GPU-accelerated).

Q: What is the most popular version of John the Ripper ? 
A: Jumbo John 

Q: Which website's breach was the rockyou.txt wordlist created from ?
A: rockyou.com

## Cracking Basic Hashes 

### Basic syntax

`john [options] [file]`

### Wordlist (auto-detect)

`john --wordlist=/path/to/wordlist hashfile`  
John will attempt to auto-detect the hash format.

### Identify hashes 

Download and run the hash identifier used earlier:  
`wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py` 

`python3 hash-id.py`  

(Use this plus context; results are probabilistic.)

## Format-specific cracking**  
`john --format=<format> --wordlist=/path/to/wordlist hashfile`

- Find format names: `john --list=formats`    
- Some standard hashes require `raw-` prefix (e.g. `raw-md5`).

**Useful commands**

- Show cracked: `john --show hashfile`
- Status/restore: `john --status` / `john --restore`
- Show cracked passwords: `john --show hashfile`

**Operational tips**

- Verify hash type (prefix/length/context) before choosing `--format`.
- Prefer targeted wordlists and rules before brute force.
- Don’t rely solely on automatic detection—explicit `--format` when possible.

Q: What type of hash is hash1.txt ? 
A: md5

Q: What is the cracked value of hash1.txt ? 
A: biscuit

Q: What type of hash is hash2.txt ? 
A: sha1

Q: What is the cracked value of hash2.txt ? 
A: kangeroo

Q: What type of hash is hash3.txtx ? 
A: sha256

Q: What is the cracked value of hash3.txt ?
A: microphone 

Q: What type of hash is hash4.txt ? 
A: whirlpool 

Q: What is the value of hash4.txt ? 
A: colossal 

## Cracking Windows Authentication Hashes 

**What it is:**

- Windows password hash format commonly called **NTLM** or **NThash**.
- Based on **MD4** of the password encoded in **UTF-16LE** (no salt).
- Typical form: 32-hex characters (128-bit).    

**Where stored:**

- **SAM** (local accounts) or **NTDS.dit** (Active Directory).

**How attackers obtain it:**

- Dump SAM/NTDS (e.g., with `secretsdump`, `ntdsutil`) or use **Mimikatz** to extract hashes.

**Offensive notes:**

- **Pass-the-hash**: often usable directly without cracking.
- **Cracking**: fast to brute-force/GPU-accelerated due to no salt and MD4 speed → use Hashcat/John with appropriate mode.
- Weak password policies make NTLM hashes easy targets.

Q: What do we need to set the --format  flag to in order to crack this hash ? 
A: nt

Q: What is the cracked value of this password ? 
A: mushroom 


## Cracking /etc/shadow hashes 

**File location & access**

- `/etc/shadow` stores Linux password hashes and is readable only by `root`.
- Must have privileged access to obtain hashes.

**Why unshadow**

- John expects passwd+shadow combined. Use `unshadow` to merge `/etc/passwd` and `/etc/shadow` into a John-compatible file

**Unshadow syntax**

- `unshadow /path/to/passwd /path/to/shadow > unshadowed.txt`
- You may provide full files or just the relevant user lines.

**Cracking with John**

- Basic wordlist:  
    `john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt`
- If auto-detection fails, specify format:  
    `john --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt`

**Tips**

- Identify hash prefix (`$6$`, `$2b$`, `$y$`, etc.) to pick correct format.
- Use targeted wordlists + rules before brute force.
- Respect legal/ethical constraints when handling real hashes.

Q: What is the root password ? 
A: 1234 

## Single Crack Mode 


- **Purpose:** Generate guesses from user info (username, GECOS) using mangling rules.    
- **GECOS** is General Electric Comprehensive Operating System, user account related records are in GECOS field, user's full name, office number etc.
- **Word mangling:** Mutations like `Markus1`, `MArkUs!`, etc. based on username data.
- **GECOS:** John can use full name/home dir/etc. from `/etc/passwd` to build candidates.
- **File format:** `username:hash` (required).
- **Command:** `john --single --format=<format> <file>`
- **Tip:** Good for weak, user-derived passwords.

Q: What is Joker's password ? 
A: Jok3r


## Custom Rules 


**Purpose:**  
Define custom mangling rules in `john.conf` to generate password candidates matching known patterns (useful when users follow predictable formats).

**Location:**  
`/etc/john/john.conf` or `/opt/john/john.conf` (TryHackMe AttackBox).

**Key syntax (simple):**

- Rule block header: `[List.Rules:NameOfRule]`
    
- Common modifiers:
    
    - `Az` — append characters to the end
    - `A0` — prepend characters to the start
    - `c` — capitalize positionally (e.g., first letter)
- Character sets in quotes: `"[0-9]"`, `"[A-Z]"`, `"[!@#]"`

**Example:**  
To convert `polopassword` → `Polopassword1!`:

```yaml
[List.Rules:PoloPassword]
cAz"[0-9] [!£$%@]"
```

**Usage:**  
`john --wordlist=/path/wordlist --rule=PoloPassword /path/hashfile`

**Tips:**

- Combine modifiers for precise patterns (prefix, case, digits, symbols).    
- Check `john --list=formats` and existing Jumbo rules for examples and debugging.

Q: What do custom rules allow us to exploit ? 
A: Password complexity predictability 

Q: What rule would we use to add all capital letters to the end of the word ? 
A: Az"\[A-Z]"

Q: What flag would we use to call a custom rule called THMRules ? 
A: --rule=THMRules 

## Zip, Rar, SSH with john 

**Zip2John**

- Converts password-protected `.zip` → hash for John.
- **Cmd:** `zip2john file.zip > zip_hash.txt`
- **Crack:** `john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt`

### 2. Rar2John

- Converts `.rar` → hash for John.
- **Cmd:** `rar2john file.rar > rar_hash.txt`
- **Crack:** `john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt`

### 3. SSH2John

- Converts SSH private key (`id_rsa`) → hash for John.
- **Cmd:** `python3 /opt/john/ssh2john.py id_rsa > id_rsa_hash.txt`
- **Crack:** `john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt`

Q: What is the password for the secure.zip file ? 
A: pass123

Q: What is the content of the flag inside the zip file ?
A: THM{w3ll_d0ne_h4sh_r0y4l}

Q: What is the password for the secure.rar file ? 
A: password 

Q: What is the contents of the flag inside the zip file ? 
A: THM{r4r_4rch1ve5_th15_t1m3}

Q: What is the SSH private key password ? 
A: mango 


---

