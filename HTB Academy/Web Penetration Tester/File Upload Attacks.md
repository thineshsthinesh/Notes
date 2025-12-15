
# Intro to File Upload Attacks 

## File Upload Attacks
- Common feature: image, media, document uploads
- Risk: attacker-controlled files stored on server
- Severity: usually **High / Critical**

### Root Causes

- Missing server-side validation
- Weak extension checks
- Weak MIME/content checks
- Unauthenticated upload endpoints
- Outdated/vulnerable upload libraries
    
### Worst Case

- **Unauthenticated arbitrary file upload**
    
- Upload of executable scripts
    
- Direct path to **RCE**
    

---

### Common Exploits

- Web shell upload
    
- Reverse shell upload
    
- Command execution on backend
    

---

### Bypass Techniques

- Double extensions
    
- MIME type spoofing
    
- Content-type mismatch
    
- Insecure filters
    

---

### Restricted Upload Abuse

- XSS via uploaded files
    
- XXE via XML-based uploads
    
- DoS (large/malformed files)
    
- File overwrite (configs/system files)
    

---

### Key Impact

- Server compromise
    
- Data exposure
    
- Privilege escalation
    
- Network pivoting