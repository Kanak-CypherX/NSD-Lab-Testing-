# Finding 05 — Insecure Direct Object Reference (IDOR) / Insecure File Permissions

| | |
|---|---|
| **Severity** | High |
| **Target** | `68.183.xx.xxx` |
| **CWE** | CWE-639: Authorization Bypass Through User-Controlled Key / CWE-732: Incorrect Permission Assignment |
| **Status** | Exploited Successfully |
| **Flag** | `flag{who_needs_safety_anyway}` |

## Description
A hint directed testing toward a restricted file, `secret.png`, located in a `/flags` directory previously discovered via Gobuster enumeration. The file was directly accessible by URL with no authentication or authorization check, and additionally contained steganographically hidden data protected only by a weak passphrase.

## Steps to Reproduce
1. Recall the `/flags` directory discovered earlier during Gobuster enumeration.
2. Directly request the restricted file by URL:
   ```
   http://68.183.92.182:8081/src/flags/secret.png
   ```
   *(Screenshot: `01-secret-png-access.png`)*
3. Download `secret.png` and run a steganography brute-force tool against it using `rockyou.txt`:
   ```bash
   stegseek secret.png /usr/share/wordlists/rockyou.txt
   ```
4. Successfully crack the passphrase and extract the hidden data.
   *(Screenshot: `02-steghide-bruteforce.png`)*

## Proof of Concept — Flag
```
flag{who_needs_safety_anyway}
```

## Impact
Lack of access control on the `/flags` directory allowed any unauthenticated user to directly access files that should have required proper authorization, combined with weak steganographic protection that was trivially brute-forced.

## Recommendation
- Enforce server-side authorization checks on every resource, not just those linked from the UI ("security through obscurity" is not access control).
- Do not rely on directory/file name secrecy — apply proper authentication and authorization middleware to sensitive paths.
- Do not use weak or dictionary-guessable passphrases for steganographic or encryption protection of sensitive data.

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/

## Screenshots to Add (folder: `Screenshots/IDOR/`)
| Filename | Content |
|---|---|
| `01-secret-png-access.png` | Direct URL access to `secret.png` in `/src/flags/` |
| `02-steghide-bruteforce.png` | Stegseek/Steghide brute-force cracking the passphrase and extracting the flag |
