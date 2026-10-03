# Mediroza General Hospital — Penetration Testing Project

> **Batch B083 — Week 4 | Networkwalks Penetration Testing Program**
> A black-box penetration test conducted against a simulated hospital web application, chaining SQL injection, misconfigured directory access, and weak password practices to achieve full compromise of patient and corporate data.



## ⚠️ Disclaimer

This project was conducted in a **controlled, educational lab environment** under **explicit written authorization** from Networkwalks as part of a training program. Mediroza General Hospital is a **simulated target** built for this exercise.

**These techniques must never be applied to any system without explicit written permission from the owner.** Unauthorized access to computer systems is illegal under laws such as the Computer Fraud and Abuse Act (US), the Computer Misuse Act (UK), and equivalent legislation worldwide.


## 📋 Project Overview

| **Client** | Mediroza General Hospital |
| **Target** | `https://medirozahospital.com` |
| **Engagement Type** | Full black-box penetration test |
| **Duration** | 5 days |
| **Authorization** | Written permission granted by client |

**Scope:** Full black-box penetration test — identify vulnerabilities, exploit them to demonstrate real impact, and document all findings in a professional report.

**Rules of Engagement:**
- Testing limited to the target domain only
- No social engineering
- No denial of service
- No testing outside agreed scope


## 🎯 Objectives (Milestones)

| Milestone | Objective | Status |

| **M1** | Gain unauthorized access and retrieve 3 confidential patient lab report PDFs | ✅ Complete |
| **M2** | Crack the encryption on all 3 retrieved files | ✅ Complete |
| **M3** | Find staff salaries and shareholder details of the hospital | ✅ Complete |
| **M4** | Write a professional penetration testing report | ✅ Complete |


## 🔍 Key Findings

| ID | Finding | Severity | CVSS 3.1 | Milestone |
|---|---|:---:|:---:|:---:|
| F1 | SQL Injection — Patient Portal Authentication Bypass | 🔴 Critical | 9.8 | M1 |
| F2 | Exposed Sensitive Database Backup (`/old/` directory) | 🔴 Critical | 7.5 | M1 / M3 |
| F3 | Weak Passwords on Encrypted Patient Report PDFs | 🟠 High | 6.8 | M2 |
| F4 | Verbose SQL Error Messages (Information Disclosure) | 🟡 Medium | 5.3 | M1 |
| F5 | Reliance on `robots.txt` as an Access Control Mechanism | 🟡 Medium | 5.3 | M1 |

**Full technical details, CWE/OWASP mappings, evidence, and remediation guidance for each finding are in the [complete report](./Mediroza_Penetration_Testing_Report.docx).**



## 🛠️ Methodology

### Stage 1 — Reconnaissance
- Passive recon: WHOIS, DNS enumeration
- Active recon: `nmap` service/version scanning
- Technology fingerprinting: `whatweb`
- Subdomain discovery via DNS brute-force

### Stage 2 — Content & Attack Surface Discovery
- `robots.txt` / `sitemap.xml` analysis
- Homepage source review
- Login portal identification (`/patient/login.php`, `/staff/login.php`)

### Stage 3 — Vulnerability Analysis & Exploitation
- Manual authentication testing
- SQL injection payload testing
- Direct enumeration of `robots.txt`-disallowed paths

### Stage 4 — Post-Exploitation Data Analysis
- PDF decryption and password recovery
- Structured analysis of exposed database backup

---

## 🧰 Tools Used

| Tool | Purpose |
|---|---|
| `nmap` | Port scanning and service/version detection |
| `whatweb` | Web technology fingerprinting |
| `dig` / `whois` | DNS and domain reconnaissance |
| `curl` | Manual HTTP requests and SQL injection testing |
| `gobuster` | Directory/content discovery |
| `qpdf` / `exiftool` | PDF encryption analysis and metadata inspection |
| John the Ripper / `hashcat` | Offline password hash cracking |
| `pikepdf` (Python) | Direct PDF password brute-forcing |
| `rockyou.txt` | Password wordlist |

---

## 💥 Attack Chain Summary

```
Recon (nmap, whatweb)
        │
        ▼
robots.txt discloses /patient/, /staff/, /old/
        │
        ├──────────────────────────────┐
        ▼                              ▼
SQL Injection on                  /old/ directory listing
/patient/login.php                exposes database backup
        │                              │
        ▼                              ▼
Auth bypass → Patient Portal      mediroza_db_backup_2019.sql
        │                         (staff salaries, shareholder
        ▼                          register — M3 complete)
3 encrypted PDF lab reports
retrieved (M1 complete)
        │
        ▼
Dictionary attack cracks all
3 PDF passwords (M2 complete)
        │
        ▼
Full patient PHI + corporate
financial data disclosed
```

**Proof-of-concept — authentication bypass:**
```bash
curl -A "Mozilla/5.0" -s -i -X POST https://medirozahospital.com/patient/login.php \
  -d "username=admin'-- -&password=anything"
```
Result: `HTTP/2 302` redirect to `portal.php` with a valid session cookie — no credentials required.

---

> Raw evidence (HTTP captures, decrypted PDFs, exposed database backup) is withheld from this public repository to avoid re-exposing sensitive simulated patient and staff data. Available on request for grading/review purposes.

---

## 📌 Top Recommendations

1. **Immediate:** Take `/old/` offline; patch the SQL injection vulnerability in `/patient/login.php`
2. **Short-term:** Implement parameterized queries across the application; disable verbose error output; disable directory listing
3. **Medium-term:** Full source code audit; deploy a WAF; establish a secure backup/decommissioning policy
4. **Governance:** Secure coding training, mandatory code review, and a tested data breach response plan

Full prioritized remediation plan available in Section 5 of the [report](./Mediroza_Penetration_Testing_Report.docx).

---
## 🙏 Acknowledgement

Special thanks to Waqas Karim (CCIE) and the NETWORKWALKS team for providing the learning resources, practical tasks, and guidance throughout the internship program.


## 👤 Author

Penetration tester — Networkwalks Batch B083
*Submitted as part of Week 4 project coursework.*

---

<sub>This repository documents an authorized training exercise only. No real systems, patients, or individuals were involved.</sub>