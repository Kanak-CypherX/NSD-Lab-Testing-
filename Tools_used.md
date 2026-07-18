# Tools Used

| Tool | Purpose | Phase |
|---|---|---|
| **Nmap** | Port scanning, service/version detection, NSE vulnerability scripts | Reconnaissance |
| **Gobuster** | Directory & file brute-forcing on the web application | Enumeration |
| **Nikto** | Automated web server vulnerability/misconfiguration scanning | Enumeration |
| **sqlmap** | Automated SQL injection detection & exploitation | Injection Testing |
| **Browser DevTools** | Inspecting HTML/DOM source, decoding client-side data (`atob()`) | Sensitive Data Exposure |
| **Stegseek / Steghide** | Steganography brute-force & extraction from image files | IDOR / Post-Recon |
| **Hydra** | Dictionary/brute-force attack against SSH login | Weak SSH Credentials |
| **rockyou.txt** | Password wordlist used for all brute-force attacks (per lab constraints) | Multiple |
| **Netcat (`nc -lvnp`)** | Controlled listener for callbacks / shell verification | Post-Exploitation |
| **LinPEAS** | Automated Linux privilege escalation enumeration | Post-Exploitation |
| **p0wny-shell (shell.php)** | PHP web shell used post file-upload exploitation | Exploitation |
| **cURL / Browser** | Manual HTTP request crafting and payload delivery | Exploitation |
| **Online URL Encoder** | Encoding XSS payloads for delivery via URL parameters | XSS |
| **Online Morse Code Translator** | Decoding a Morse-encoded flag | Command Injection |

## Environment
- Attacker machine: Kali Linux

