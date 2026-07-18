# Finding 02 — UNION-Based SQL Injection 

| | |
|---|---|
| **Severity** | Critical |
| **Target** | `68.183.xx.xxx` |
| **CWE** | CWE-89: Improper Neutralization of Special Elements used in an SQL Command |
| **Status** | Exploited Successfully |
| **Flag** | `flag{stealthy_sql_dump}` |

## Description
The `dashboard.php` page contains a search bar and a `query` URL parameter that is passed directly into a SQL query without sanitization. This allowed a UNION-based SQL injection attack, which was used to append attacker-controlled `SELECT` statements to the original query and exfiltrate hidden administrative data not normally rendered by the application.

## Steps to Reproduce
1. Authenticate to the application (see Finding 01) and browse to `dashboard.php`, noting the search bar and upload field.
   *(Screenshot: `13-dashboard-page.png`)*
2. Determine the number of columns returned by the original query using `ORDER BY`, then craft a UNION-based payload against the `query` parameter:
   ```
   dashboard.php?query=1 UNION SELECT null,null,flag_column FROM hidden_table--
   ```
3. Submit the crafted request and observe hidden data — including Flag 2 — reflected in the page output.
   *(Screenshot: `14-union-sqli-flag2.png`)*

## Proof of Concept — Flag
```
FLAG2: flag{stealthy_sql_dump}
```

## Impact
UNION-based SQL injection allowed direct extraction of arbitrary data from any table in the database, bypassing the application's intended access controls and exposing data never meant to be surfaced to end users.

## Recommendation
- Use parameterized queries / prepared statements for the `query` parameter and every other database-bound input.
- Apply an allow-list validation approach for query parameters that influence SQL logic.
- Apply the principle of least privilege to the database account used by the web application (no access to tables outside its functional need).

## References
- OWASP: https://owasp.org/www-community/vulnerabilities/
- OWASP Top Ten: https://owasp.org/www-project-top-ten/

## Screenshots to Add (folder: `Screenshots/SQLi/`)
| Filename | Content |
|---|---|
| `13-dashboard-page.png` | `dashboard.php` showing search bar and upload field |
| `14-union-sqli-flag2.png` | UNION-based SQLi payload result revealing Flag 2 |
