
#tool #hydra 

Hydra basic authentication syntax: 

```bash
hydra -l admin -P /usr/share/wordlists/SecLists/Passwords/Common-Credentials/500-worst-passwords.txt enum.thm http-get /labs/basic_auth
```
