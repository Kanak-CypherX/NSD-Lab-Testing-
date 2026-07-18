# NCPT Testing Report

## Overview
This repository documents a full black-box penetration test carried out against two target machines as part of lab exam. The engagement covered network reconnaissance, web application enumeration, vulnerability scanning, exploitation, and post-exploitation flag retrieval.

## Objective
The objective of this assessment was to simulate a real-world attacker's approach against the two in-scope hosts, identify exploitable vulnerabilities, retrieve flags planted by the lab administrators, and produce a professional report documenting each finding with evidence, impact, and remediation guidance.

## Scope
- **Target 1:** `68.183.xx.xxx` — web application hosting a login portal, dashboard, and file upload functionality (8 flags to retrieve)
- **Target 2:** `209.38.xxx.xxx` — host exposing SSH (2 flags to retrieve)

Testing followed the constraints provided for the exercise, including use of `rockyou.txt` for all brute-force/dictionary attacks.

## Repository Structure
```
NCPT-Testing-Report/
│
├── README.md                  # This file
├── Methodology.md             # Testing methodology & phases
├── Tools-Used.md              # Tools used during the engagement
│
├── Findings/                  # One markdown file per vulnerability
│   ├── Finding-01-SQL-Injection.md
│   ├── Finding-02-UNION-SQLi.md
│   ├── Finding-03-Sensitive-Data-Exposure.md
│   ├── Finding-04-Command-Injection.md
│   ├── Finding-05-IDOR.md
│   ├── Finding-06-XSS.md
│   ├── Finding-07-Ping-Injection.md
│   ├── Finding-08-Unrestricted-File-Upload.md
│   └── Finding-09-Weak-SSH-Credentials.md
│
└── Screenshots/
    ├── Reconnaissance/
    ├── Enumeration/
    ├── SQLi/
    ├── Sensitive-Data-Exposure/
    ├── Command-Injection/
    ├── IDOR/
    ├── XSS/
    ├── File-Upload/
    └── SSH/
```

## Summary of Findings

| # | Flag | Vulnerability | Target | Severity |
|---|------|---------------|--------|----------|
| 1 | `flag{hack_the_planet_Admin}` | SQL Injection (Auth Bypass) | Critical |
| 2 | `flag{stealthy_sql_dump}` | UNION-Based SQL Injection |  Critical |
| 3 | `FLAG{XSS_Captured_Revealed}` | Sensitive Data Exposure (Client-Side) | Medium |
| 4 | `FLAG(GET-SHELL-OR-DIE)` | Command Injection | Critical |
| 5 | `flag{who_needs_safety_anyway}` | IDOR / Insecure File Permissions | High |
| 6 | `FLAG{unsafe_but_fun_cookie}` | Reflected XSS | Medium |
| 7 | `FLAG{Ping_Injection_Rocks}` | Command Injection (Ping) | Critical |
| 8 | `flag{Congratulationsss!!!fun_time_shell_on_fire}` | Unrestricted File Upload → RCE |Critical |
| 9 | `flag{ssh_conquered_successfully}` | Weak SSH Credentials (Brute-Force) | Critical |

## Overall Risk Rating
**Critical.** The target application fundamentally fails to treat user input as untrusted, resulting in chained injection vulnerabilities (SQLi, Command Injection, XSS), broken access control (IDOR), and unrestricted file upload — collectively allowing full compromise of the web application and underlying host. The secondary target's SSH service was compromised via weak/guessable credentials.

## Conclusion
9 flags across both targets were successfully retrieved. The assessment demonstrates that a lack of input validation, weak authentication controls, and misconfigured file permissions can be chained together to achieve full remote code execution and host compromise.

## Disclaimer
This engagement was conducted in an authorized, isolated CTF lab environment for educational purposes only. None of the techniques documented here should be used against systems without explicit written authorization.
