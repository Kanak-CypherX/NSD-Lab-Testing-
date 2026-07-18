# Finding 04 — Command Injection

| | |
|---|---|
| **Severity** | Critical |
| **Target** | `68.183.xx.xxx` |
| **CWE** | CWE-78: Improper Neutralization of Special Elements used in an OS Command |
| **Status** | Exploited Successfully |
| **Flag** | `FLAG(GET-SHELL-OR-DIE)` |

## Description
A URL parameter (`cmd`) is passed unsanitized into a system call on the server, allowing arbitrary operating system command execution. The output of the injected command revealed a Morse-code-encoded string, which — once translated — yielded Flag 4.

## Steps to Reproduce
1. Based on an in-application hint, test a URL parameter for command injection.
   *(Screenshots: `17-hint-flag4-a.png`, `17-hint-flag4-b.png`)*
2. Append a command-injection payload to the vulnerable parameter, e.g.:
   ```
   page.php?cmd=;id
   page.php?cmd=;cat flag4.txt
   ```
3. Confirm arbitrary command execution via the returned output.
   *(Screenshot: `19-command-injection-success.png`)*
4. Observe the returned flag value is in Morse code.
   *(Screenshot: `20-morse-code-flag.png`)*
5. Convert the Morse code to text using an online translator: https://dnschecker.org/morse-code-translator.php

## Proof of Concept — Flag
```
FLAG(GET-SHELL-OR-DIE)
```

## Impact
Command injection allows an attacker to execute arbitrary commands with the privileges of the web server process, potentially leading to full server compromise, data theft, or use of the host as a pivot point for further attacks.

## Recommendation
- Never pass user input directly to shell/system calls.
- Use safe, parameterized APIs (e.g. language-native library calls) instead of shelling out wherever possible.
- If shelling out is unavoidable, strictly allow-list expected input and use functions that avoid shell interpretation (e.g. `execve`-style APIs without a shell).
- Run the web application process with least privilege.

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/

## Screenshots to Add (folder: `Screenshots/Command-Injection/`)
| Filename | Content |
|---|---|
| `17-hint-flag4-a.png` | In-app hint pointing toward command injection |
| `17-hint-flag4-b.png` | Continuation of the hint / parameter discovery |
| `19-command-injection-success.png` | Successful command execution via `cmd` parameter |
| `20-morse-code-flag.png` | Flag returned in Morse code format |
