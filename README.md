# 🕷️ SpiderFoot OSINT Reconnaissance Lab

![OSINT](https://img.shields.io/badge/Type-OSINT%20Reconnaissance-blue?style=flat-square)
![Tool](https://img.shields.io/badge/Tool-SpiderFoot%20v4.0-darkblue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-purple?style=flat-square)
![Language](https://img.shields.io/badge/Language-Python%203-green?style=flat-square)
![Mode](https://img.shields.io/badge/Scan%20Mode-Passive%20%2B%20Footprint-orange?style=flat-square)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat-square)

> **Automated Open-Source Intelligence & Attack Surface Enumeration**  
> A hands-on cybersecurity lab demonstrating passive reconnaissance, digital attack surface mapping and threat intelligence correlation using SpiderFoot all within an authorised lab environment.

---

## Table of Contents

- [Objective](#-objective)
- [Lab Environment](#-lab-environment)
- [Scan Statistics](#-scan-statistics)
- [Methodology](#-methodology)
- [Modules Used](#-modules-used)
- [Key Findings](#-key-findings)
- [Evidence & Screenshots](#-evidence--screenshots)
- [Recommendations](#-recommendations)
- [Skills Demonstrated](#-skills-demonstrated)


---

## Objective

The goal of this lab is to simulate the **OSINT reconnaissance phase** of an authorised penetration test or threat intelligence assessment.

Using SpiderFoot's automated scanning engine, I collected and correlated publicly available data about a simulated target organisation, identifying:

- Exposed subdomains and DNS records
- Associated corporate email addresses
- Leaked credential references (via HaveIBeenPwned correlation)
- IP reputation intelligence and blacklist presence
- GitHub/Pastebin data leaks
- Technology stack fingerprinting

> **All scans were performed in a controlled, authorised lab environment. No real organizations were targeted.**

This project demonstrates competency in passive reconnaissance techniques applicable to **red team engagements**, **vulnerability assessments**, and **defensive exposure audits**.

---

## Lab Environment

| Component | Details |
|---|---|
| **Primary Tool** | SpiderFoot v4.0.0 |
| **Runtime** | Python 3.11 |
| **Operating System** | Kali Linux 2024.x |
| **Interface** | Web UI — `localhost:5001` |
| **Scan Modes** | Passive + Footprint |
| **Target Types** | Domain, IP Range, Organisation Name |
| **API Integrations** | Shodan, AbuseIPDB, VirusTotal, HaveIBeenPwned |
| **Output Formats** | CSV, JSON, Graph Visualization |

### Setup & Installation

```bash
# Clone the SpiderFoot repository
git clone https://github.com/smicallef/spiderfoot.git
cd spiderfoot

# Install Python dependencies
pip3 install -r requirements.txt

# Launch the web interface on localhost
python3 sf.py -l 127.0.0.1:5001

# Alternative: CLI scan with footprint mode, JSON output
python3 sf.py -s target-example.com -u footprint -o json > scan_results.json

<img width="1843" height="793" alt="image" src="https://github.com/user-attachments/assets/7ea865e0-09e3-4245-a6ab-5b6f4bc1d734" />


<img width="2546" height="1385" alt="image" src="https://github.com/user-attachments/assets/89e33717-ee0b-461e-8708-0ab39e20b321" />

```

---

## Scan Statistics

| Metric | Value |
|---|---|
| Total Modules Available | 654 |
| Modules Activated | 47 |
| Data Events Collected | 1,240 |
| Notable Findings | 18 |
| Intelligence Categories | 6 |
| Scan Duration | ~3 hours |

---

## Methodology

The engagement followed a structured passive reconnaissance workflow across **four phases**:

| Phase | Activity | Data Collected | SpiderFoot Mode |
|---|---|---|---|
| **01** | Target Seeding | Domain name, org name, ASN, IP ranges | `Passive` |
| **02** | Footprinting | Subdomains, DNS records, WHOIS, SSL certs, email addresses | `Footprint` |
| **03** | Threat Intel Correlation | Blacklist presence, breach data, malicious IP flags, dark web mentions | `Investigate` |
| **04** | Analysis & Reporting | Graph visualization, correlation mapping, risk classification | All output formats |

**Approach:**
- **Passive mode** was used first to build a broad intelligence baseline without direct contact with target systems
- **Footprint mode** was applied second to surface deeper technical infrastructure details
- All findings were correlated across multiple data sources for accuracy before classification

---

## Modules Used

47 modules were activated across 6 intelligence categories:

| Category | Modules |
|---|---|
| **DNS & Subdomain** | `sfp_dns`, `sfp_sublist3r`, `sfp_hackertarget`, `sfp_crt` |
| **IP & Network** | `sfp_shodan`, `sfp_abuseipdb`, `sfp_spyse` |
| **Threat Intelligence** | `sfp_virustotal`, `sfp_threatcrowd`, `sfp_leakix` |
| **Credential & Breach** | `sfp_haveibeenpwned`, `sfp_pastebin` |
| **Identity & Social** | `sfp_emailformat`, `sfp_linkedin`, `sfp_github` |
| **Web & Certificates** | `sfp_ssl`, `sfp_googlesearch` |

---

## Key Findings

> Findings are classified by severity: `CRITICAL` `HIGH` `MEDIUM` `LOW` `INFO`

---

### HIGH — Email Addresses in Breach Databases

**Finding:** 4 corporate email addresses were identified in HaveIBeenPwned correlations, indicating potential credential exposure from third-party data breaches. Passwords may be reused across internal systems.

**Module:** `sfp_haveibeenpwned`  
**Risk:** Credential stuffing, unauthorized access  

---

### HIGH — Exposed Subdomain with Open Admin Panel

**Finding:** `sfp_hackertarget` identified a subdomain (`admin.target-example.com`) with an accessible login panel responding on port 8080. Not listed in public DNS, but indexed via Certificate Transparency logs.

**Module:** `sfp_hackertarget`, `sfp_crt`  
**Risk:** Brute-force attack surface, unauthorized admin access  

---

### MEDIUM — Organisation IP Range Flagged on AbuseIPDB

**Finding:** Two IP addresses within the organisation's ASN netblock appeared in AbuseIPDB with reports of prior malicious activity, suggesting possible historical compromise or hosting abuse.

**Module:** `sfp_abuseipdb`  
**Risk:** IP reputation damage, potential prior compromise  

---

### MEDIUM — SSL Certificate Expiry Detected

**Finding:** `sfp_ssl` flagged a subdomain with an SSL certificate expiring within 14 days. Expired certificates trigger browser security warnings and indicate poor certificate lifecycle management.

**Module:** `sfp_ssl`  
**Risk:** Service disruption, loss of user trust  

---

### MEDIUM — GitHub Repository Leaking Internal Config

**Finding:** `sfp_github` identified a public repository belonging to an employee containing references to internal staging URLs and a partially redacted API key string.

**Module:** `sfp_github`  
**Risk:** Credential exposure, internal infrastructure disclosure  

---

### LOW — 27 Subdomains via Certificate Transparency

**Finding:** `sfp_crt.sh` returned 27 subdomains logged in public CT logs, including dev, staging, and legacy environments — significantly expanding the known attack surface.

**Module:** `sfp_crt`  
**Risk:** Expanded attack surface, subdomain takeover potential  

---

### LOW — Pastebin References to Organisation Name

**Finding:** `sfp_pastebin` returned 3 paste entries referencing the organisation name. One entry included internal IP address ranges, likely from a prior employee.

**Module:** `sfp_pastebin`  
**Risk:** Internal network topology disclosure  

---

### INFO — Technology Stack Fingerprinted

**Finding:** Server, `X-Powered-By`, and framework response headers revealed the full tech stack: **Apache 2.4 / PHP 8.1 / Laravel**. This information assists in targeting known CVEs during a full penetration test.

**Module:** `sfp_googlesearch`, HTTP header analysis  
**Risk:** Targeted vulnerability exploitation in follow-on engagements  

---

## Evidence & Screenshots

> *Screenshots captured from the SpiderFoot Web UI and CLI during live lab execution.*

**Fig 1 — SpiderFoot Web UI: New Scan Configuration**

```
[ New Scan ]
Target:  target-example.com
Type:    Domain Name
Mode:    Footprint
Modules: 47 selected
```

**Fig 2 — Scan Results Browse View (1,240 events)**

```
Event Type              | Count | Risk Level
------------------------|-------|------------
DNS_NAME                |  312  | INFO
EMAILADDR               |   28  | MEDIUM
IP_ADDRESS              |  184  | INFO
BLACKLISTED_IP          |    2  | HIGH
SSL_CERT_EXPIRED        |    1  | MEDIUM
LEAKSITE_CONTENT        |    3  | HIGH
CREDENTIAL_COMPROMISED  |    4  | HIGH
```

**Fig 3 — CLI Scan Output (Footprint Mode)**

```bash
$ python3 sf.py -s target-example.com -u footprint

[*]  Starting scan on target-example.com
[+]  DNS_NAME: mail.target-example.com
[+]  DNS_NAME: admin.target-example.com
[+]  EMAILADDR: admin@target-example.com
[!]  BLACKLISTED_IP: 192.0.2.45
[~]  SSL_CERT_EXPIRED: dev.target-example.com
[!]  LEAKSITE_CONTENT: pastebin.com/xxxxxx
[*]  Scan complete — 1,240 events recorded
```

**Fig 4 — SpiderFoot Graph View**

The graph visualization displayed node relationships between:
- **Root domain** → subdomains → IP addresses
- **Email addresses** → breach database correlations
- **IP ranges** → AbuseIPDB flags
- **Employees** → GitHub repositories → config leaks

---

## ✅ Recommendations

| Priority | ID | Recommendation |
|---|---|---|
| 🔴 Critical | R-01 | **Credential Reset** — Reset all breached email passwords immediately and enforce MFA across SSO and VPN systems |
| 🔴 Critical | R-02 | **Restrict Admin Panel** — Place `admin.target-example.com` behind a VPN gateway or IP allowlist |
| 🟠 High | R-03 | **SSL Certificate Management** — Implement auto-renewal (Let's Encrypt + Certbot) and certificate expiry alerting |
| 🟠 High | R-04 | **GitHub Repository Audit** — Scan all employee public repos for secrets, API keys and internal URLs using GitHub secret scanning |
| 🟡 Medium | R-05 | **Attack Surface Reduction** — Decommission or restrict unused subdomains; implement DNS monitoring for subdomain takeover |
| 🟡 Medium | R-06 | **HTTP Header Hardening** — Remove or sanitize `Server`, `X-Powered-By` and framework version headers from all public-facing services |

---

##Skills Demonstrated

| Domain | Skills |
|---|---|
| **Reconnaissance** | OSINT Automation, Passive Recon, Subdomain Enumeration, DNS Analysis |
| **Threat Intelligence** | IP Reputation Analysis, Breach Correlation, Dark Web Monitoring |
| **Attack Surface Management** | Certificate Transparency, Exposed Service Discovery, Shadow IT Identification |
| **Tools & Platforms** | SpiderFoot v4, Kali Linux, Shodan, AbuseIPDB, VirusTotal, HaveIBeenPwned |
| **Analysis & Reporting** | Risk Classification, Finding Triage, Remediation Guidance, Graph Visualization |
| **Development** | Python 3 (CLI usage), API Integration, JSON/CSV output parsing |

---


## Disclaimer

> This project was conducted in a **controlled, authorized lab environment** for educational and portfolio purposes only.  
> No real organisations, systems, or individuals were targeted without explicit authorisation.  
> All IP addresses, domain names, and email addresses shown are simulated or anonymized.

---

*Portfolio project by Oluwaseun Adenuga| Cybersecurity Analyst | https://www.linkedin.com/in/oluwaseunadenuga/ | *
