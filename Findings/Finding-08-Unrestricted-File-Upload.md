# Finding 08 — Unrestricted File Upload → Remote Code Execution

| | |
|---|---|
| **Severity** | Critical |
| **Target** | `68.183.xx.xxx` |
| **CWE** | CWE-434: Unrestricted Upload of File with Dangerous Type |
| **Status** | Exploited Successfully |
| **Flag** | `flag{Congratulationsss!!!fun_time_shell_on_fire}` |

## Description
The file upload field on `dashboard.php` fails to validate or restrict uploaded file types, allowing a PHP web shell (`shell.php`) to be uploaded. The shell was then reachable via the previously discovered `/uploads` directory, granting arbitrary command execution on the server.

## Steps to Reproduce
1. Following the hint that "file upload could give RCE," locate the upload field on `dashboard.php`.
   *(Screenshot: `01-upload-hint.png`)*
2. Prepare a PHP web shell (public reference used: p0wny-shell — https://github.com/flozz/p0wny-shell/blob/master/shell.php).
3. Upload `shell.php` through the unrestricted upload field.
4. Browse to the `/uploads` directory (discovered earlier via Gobuster) and confirm `shell.php` is present and executable.
   *(Screenshot: `02-shell-uploaded-directory.png`)*
5. Access `shell.php` via its URL to obtain command execution, and browse to the known default Ubuntu document root (`/var/www/html`) to locate and read Flag 8.
   *(Screenshot: `03-document-root-flag8.png`)*

## Proof of Concept — Flag
```
flag{Congratulationsss!!!fun_time_shell_on_fire}
```

## Impact
Unrestricted file upload leading to a web shell gives an attacker full remote code execution on the server with the privileges of the web server process — effectively complete compromise of the host, including the ability to read, modify, or delete any file accessible to that process, pivot to internal networks, or establish persistence.

## Recommendation
- Validate uploaded files by content (magic bytes / MIME sniffing), not just file extension or client-supplied `Content-Type`.
- Store uploaded files outside the web root, or in a location where script execution is disabled (e.g. via web server configuration).
- Rename uploaded files to non-guessable, non-executable identifiers.
- Apply an allow-list of permitted file types and enforce strict size limits.
- Run the web server process with least privilege and isolate the upload storage from sensitive application code/data.

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/
- Shell reference: https://github.com/flozz/p0wny-shell/blob/master/shell.php

## Screenshots to Add (folder: `Screenshots/File-Upload/`)
| Filename | Content |
|---|---|
| `01-upload-hint.png` | In-app hint pointing toward the upload field |
| `02-shell-uploaded-directory.png` | `/uploads` directory listing showing `shell.php` |
| `03-document-root-flag8.png` | Web shell browsing `/var/www/html`, revealing Flag 8 |
