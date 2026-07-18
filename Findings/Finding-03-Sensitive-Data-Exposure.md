# Finding 03 — Sensitive Data Exposure (Client-Side)

| | |
|---|---|
| **Severity** | Medium |
| **Target** | `68.183.xx.xxx` |
| **CWE** | CWE-200: Exposure of Sensitive Information to an Unauthorized Actor |
| **Status** | Exploited Successfully |
| **Flag** | `FLAG{XSS_Captured_Revealed}` |

## Description
A flag value was found embedded directly in the page's HTML source, hidden from normal view using `display: none;` CSS and encoded in Base64. Because the data was only hidden client-side (not withheld server-side), any user with access to browser developer tools could view and decode it — a classic sensitive data exposure issue.

## Steps to Reproduce
1. Open the target page and inspect the HTML source using browser Developer Tools.
   *(Screenshot: `01-hidden-flag-html-source.png`)*
2. Locate the element hidden via `display: none;` and un-hide it (or read its raw text/attribute) to reveal a Base64-encoded string.
3. Decode the string using the browser console:
   ```javascript
   atob("<base64_string>")
   ```
   *(Screenshot: `02-base64-decode-console.png`)*

## Proof of Concept — Flag
```
FLAG{XSS_Captured_Revealed}
```

## Impact
Sensitive data hidden only via client-side CSS/JS is fully visible to any user inspecting page source, browser storage, or network traffic. This can lead to disclosure of secrets, tokens, or other information never intended to reach the client at all.

## Recommendation
- Never rely on client-side hiding (`display:none`, JS obfuscation) to protect sensitive data — if it must not be seen, it must not be sent to the browser.
- Perform authorization and data-filtering decisions strictly server-side.
- Audit all pages for sensitive values embedded in HTML comments, hidden fields, or inline scripts.

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/

## Screenshots to Add (folder: `Screenshots/Sensitive-Data-Exposure/`)
| Filename | Content |
|---|---|
| `01-hidden-flag-html-source.png` | DevTools showing the hidden (`display:none`) Base64 flag element in the DOM |
| `02-base64-decode-console.png` | Browser console using `atob()` to decode the flag |
