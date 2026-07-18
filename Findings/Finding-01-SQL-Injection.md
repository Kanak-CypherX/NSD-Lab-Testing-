# Finding 01 — SQL Injection (Authentication Bypass & Data Dump)

| | |
|---|---|
| **Severity** | Critical |
| **Target** | `68.183.xx.xxx` |
| **CWE** | CWE-89: Improper Neutralization of Special Elements used in an SQL Command |
| **Status** | Exploited Successfully |
| **Flag** | `flag{hack_the_planet_Admin}` |

## Description
The application's login form fails to sanitize user-supplied input in the `password` parameter, allowing a classic boolean-based blind SQL injection. Prior to exploitation, a publicly accessible `Dockerfile` was also discovered, exposing plaintext database name, username, and password. Combining the exposed credentials context with `sqlmap`, the underlying database was enumerated and the full `users` table was dumped, yielding valid administrator credentials.

## Steps to Reproduce
1. Browse the web application and identify the login page at `/login.php`.
   *(Screenshot: `04-login-page.png`)*
2. Enumerate exposed files via Gobuster and locate a publicly accessible `Dockerfile` containing plaintext DB credentials.
   *(Screenshot: `05-dockerfile-credentials.png`)*
3. Test the login form's `password` field for SQL injection:
   ```
   ' OR '1'='1
   ```
   Confirm a boolean-based blind SQLi condition.
   *(Screenshot: `06-sqli-boolean-based.png`)*
4. Run `sqlmap` against the login request to enumerate databases and tables:
   ```bash
   sqlmap -u "http://68.183.xx.xxx:8081/login.php" \
     --data="username=admin&password=test" \
     --batch --dbs
   ```
5. Enumerate the discovered tables — `comments`, `posts`, `shadowpress`, and `users`.
   *(Screenshots: `07-table-comments.png`, `08-table-posts.png`, `09-table-shadowpress.png`, `10-table-users.png`)*
6. Dump the `users` table and extract admin credentials:
   ```
   ad**n : not*****assword
   ```
   *(Screenshot: `11-dumped-credentials.png`)*
7. Log into the application using the exfiltrated credentials to access the admin dashboard and retrieve Flag 1.
   *(Screenshot: `12-admin-login-flag1.png`)*

## Proof of Concept — Flag
```
FLAG1: flag{hack_the_planet_Admin}
```

## Impact
An unauthenticated attacker can bypass authentication entirely and extract the full contents of the application database, including all user credentials, leading to full account takeover and further lateral compromise of the application.

## Recommendation
- Use parameterized queries / prepared statements for all database interactions — never concatenate user input into SQL.
- Apply strict server-side input validation and output encoding.
- Remove build artifacts such as `Dockerfile` from publicly accessible web roots; store secrets in environment variables or a secrets manager, never in version-controlled/deployed files.
- Enforce strong, hashed (e.g. bcrypt/argon2) password storage and rotate any credentials found exposed.

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/

## Screenshots to Add (folder: `Screenshots/SQLi/`)
| Filename | Content |
|---|---|
| `04-login-page.png` | The discovered `login.php` page |
| `05-dockerfile-credentials.png` | Exposed Dockerfile with plaintext DB credentials |
| `06-sqli-boolean-based.png` | Boolean-based blind SQLi confirmation on password field |
| `07-table-comments.png` | `comments` table enumerated via sqlmap |
| `08-table-posts.png` | `posts` table enumerated via sqlmap |
| `09-table-shadowpress.png` | `shadowpress` table enumerated via sqlmap |
| `10-table-users.png` | `users` table enumerated via sqlmap |
| `11-dumped-credentials.png` | Dumped admin credentials from users table |
| `12-admin-login-flag1.png` | Logged in as admin, Flag 1 visible on dashboard |
