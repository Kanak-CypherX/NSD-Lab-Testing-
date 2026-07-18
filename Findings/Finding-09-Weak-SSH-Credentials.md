# Finding 09 — Weak SSH Credentials Susceptible to Brute-Force Attack

| | |
|---|---|
| **Severity** | Critical |
| **Target** | `209.38.xxx.xxx` |
| **CWE** | CWE-521: Weak Password Requirements / CWE-307: Improper Restriction of Excessive Authentication Attempts |
| **Status** | SSH Brute-Force Success |
| **Flag** | `flag{ssh_conquered_successfully}` |

## Description
The SSH service on the second target host was configured with a user account protected only by a weak, dictionary-guessable password, and with no account lockout or rate-limiting policy in place. This allowed an automated dictionary/brute-force attack using Hydra and the `rockyou.txt` wordlist to successfully guess valid credentials and obtain interactive shell access.

## Steps to Reproduce
1. Perform an Nmap scan against the target to enumerate open ports and services.
   *(Screenshot: `01-nmap-target2.png`)*
2. Run Nmap NSE vulnerability scripts for additional context.
   *(Screenshot: `02-nmap-vuln-script.png`)*
3. Note an additional IP disclosed in the scan's traceroute output, running an HTTP service on port 80.
   *(Screenshot: `03-traceroute-ip.png`)*
4. Browse to that IP and observe a GPON home gateway login page; default credentials were attempted without success.
   *(Screenshot: `04-gpon-login-page.png`)*
5. Using an in-app hint ("your name can be username"), run Hydra against SSH with `***` as the username and `rockyou.txt` as the password list:
   ```bash
   hydra -l *** -P /usr/share/wordlists/rockyou.txt ssh://209.38.120.181
   ```
   *(Screenshot: `05-hydra-bruteforce.png`)*
6. Successfully log in via SSH with the cracked credentials and retrieve Flag 1.
   *(Screenshot: `06-ssh-login-success-flag1.png`)*
7. Run LinPEAS on the compromised host to enumerate local privilege escalation vectors.
   *(Screenshot: `07-linpeas.png`)*

## Proof of Concept — Flag
```
Flag 1: flag{ssh_conquered_successfully}
```

## Impact
A weak, brute-forceable SSH password grants an attacker full interactive shell access to the host, enabling data theft, malware deployment, use of the host as a pivot for further attacks, and — depending on further privilege escalation — full system compromise.

## Recommendation
- **Disable password authentication:** Enforce SSH key-based authentication, which is immune to password-guessing attacks.
- **Implement lockout policies:** Enforce a strong password policy and configure account lockout after a small number of failed login attempts.
- **Restrict access:** Use a tool such as Fail2Ban to automatically block malicious source IPs, and configure a firewall to limit SSH access to trusted source addresses only.
- Change any default credentials on exposed management interfaces (e.g. the GPON gateway identified during this test).

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/

## Screenshots to Add (folder: `Screenshots/SSH/`)
| Filename | Content |
|---|---|
| `01-nmap-target2.png` | Nmap port/service scan of Target 2 |
| `02-nmap-vuln-script.png` | Nmap NSE vulnerability script scan results |
| `03-traceroute-ip.png` | Additional IP disclosed in traceroute, running port 80 |
| `04-gpon-login-page.png` | GPON home gateway login page |
| `05-hydra-bruteforce.png` | Hydra brute-force command and successful result |
| `06-ssh-login-success-flag1.png` | Successful SSH login, Flag 1 retrieved |
| `07-linpeas.png` | LinPEAS output run on the compromised host |
