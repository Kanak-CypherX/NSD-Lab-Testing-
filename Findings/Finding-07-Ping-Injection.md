# Finding 07 — Command Injection via Ping Field

| | |
|---|---|
| **Severity** | Critical |
| **Target** | `68.183.xx.xxx` |
| **CWE** | CWE-78: Improper Neutralization of Special Elements used in an OS Command |
| **Status** | Exploited Successfully |
| **Flag** | `FLAG{Ping_Injection_Rocks}` |

## Description
The search bar on `dashboard.php` passes user input directly into a system `ping` command on the server. By appending shell metacharacters and additional commands to the expected IP/hostname input, arbitrary command execution was achieved (a specific variant of OS command injection, sometimes referred to as "ping injection").

## Steps to Reproduce
1. Following the in-app hint to "try ping injection," locate the search field on `dashboard.php`.
   *(Screenshot: `01-ping-injection-hint.png`)*
2. Submit a crafted payload appending a command to the expected ping target:
   ```
   127.0.0.1; cat flag7.txt
   127.0.0.1 && whoami
   ```
3. Observe that the application executes the injected command and returns both the ping output and the flag.
   *(Screenshot: `02-ping-injection-success.png`)*

## Proof of Concept — Flag
```
FLAG{Ping_Injection_Rocks}
```

## Impact
As with the earlier command injection finding, this allows arbitrary command execution on the host with the web server's privileges, enabling data theft, lateral movement, or full server compromise.

## Recommendation
- Never construct shell commands by concatenating user input.
- Use language-native networking libraries (e.g. an ICMP library) instead of shelling out to `ping`.
- If a shell call is unavoidable, strictly validate that input matches a valid IP/hostname format via an allow-list regex before use, and use APIs that pass arguments as an array rather than a single interpreted string.
- Apply the principle of least privilege to the web server process.

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/

## Screenshots to Add (folder: `Screenshots/Command-Injection/`)
| Filename | Content |
|---|---|
| `01-ping-injection-hint.png` | In-app hint pointing toward ping injection on the search bar |
| `02-ping-injection-success.png` | Successful ping injection execution revealing Flag 7 |
