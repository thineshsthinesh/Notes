
# Race Conditions 

- Race conditions are **logic flaws** caused by **concurrent request processing** without proper synchronization.
- Occur when **multiple threads access shared data simultaneously**, leading to unintended behavior.
- Exploited by sending **carefully timed requests** to cause a collision.
- The vulnerable time period is called the **race window**.
- Impact depends on the **application logic and functionality** involved.
    
## Limit Overrun Race Conditions

- Most common race condition type.
- Allow attackers to **exceed application-imposed limits**.
- Caused by a gap between **validation (check)** and **state update (use)**.
- Classified as **TOCTOU (Time-of-Check to Time-of-Use)** flaws.
    

### Typical Vulnerable Flow

1. Check if action was already performed
2. Perform the action
3. Update the database

- Concurrent requests during this flow can exploit the **temporary sub-state**.
    
### Common Exploitation Examples

- Redeeming a **single-use gift card** multiple times
    
- Applying a **discount code** repeatedly
    
- **Over-withdrawing** or transferring funds
    
- Reusing a **CAPTCHA solution**
    
- Bypassing **rate limits / anti-brute-force controls**
    
### Detection & Exploitation (High-Level)

- Identify a **single-use or rate-limited endpoint**.
- Send **multiple parallel requests** in rapid succession.
- Goal: **align multiple race windows** to force a collision.
    
### Burp Suite Support

- Burp Repeater (2023.9+) supports **parallel request attacks**.
- Techniques used:
    
    - **HTTP/1:** Last-byte synchronization
    - **HTTP/2:** Single-packet attack
        
- Single-packet attack allows **20–30 requests in one TCP packet**, reducing network jitter.
- Sending many requests helps overcome **server-side latency (jitter)** during discovery.

### Lab: Limit overrun race conditions

*APPRENTICE*

LAB  **solved**


This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.

To solve the lab, successfully purchase a **Lightweight L33t Leather Jacket**.

You can log in to your account with the following credentials: `wiener:peter`.

For a faster and more convenient way to trigger the race condition, we recommend that you solve this lab using the [Trigger race conditions](https://github.com/PortSwigger/bambdas/blob/main/CustomAction/ProbeForRaceCondition.bambda) custom action. This is only available in Burp Suite Professional.

