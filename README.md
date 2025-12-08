
# Vastaamo Breach — Incident Post-Mortem Report  
### A Technical Case Study by Satu Ikola

---

### **Topics:**  
MySQL · Incident Response · Blue Team · SOC · Microsoft Sentinel · Microsoft Defender XDR · MITRE ATT&CK · Data Exfiltration · Threat Hunting · GDPR · Ransomware · Cybersecurity Governance

---
---

## 📘 Table of Contents

- [1. Executive Summary](#1-executive-summary)
- [2. Timeline of Events](#2-timeline-of-events)
- [3. Root Cause Analysis (RCA)](#3-root-cause-analysis-rca)
- [4. MITRE ATTCK-Mapping](#4-mitre-attck-mapping)
- [5. Attack Path Diagram](#5-attack-path-diagram)
- [6. What Went Wrong (Defensive Failures)](#6-what-went-wrong-defensive-failures)
- [7. Recommended Controls (Technical)](#7-recommended-controls-technical)
- [8. Lessons Learned (Blue-Team--soc)](#8-lessons-learned-blue-team--soc)
- [9. Business Impact](#9-business-impact)
- [10. Contact](#10-contact)

---


## 1. Executive Summary

The Vastaamo breach (2017–2020) is one of the most severe cybersecurity incidents in Finnish history.  
A forgotten and publicly exposed database port, passwordless accounts, missing monitoring, and unencrypted psychotherapy records allowed attackers to exfiltrate and eventually leak data of **33,000 patients**.

The breach resulted in:

- The company’s **bankruptcy (2021)**
- The CEO’s conviction for privacy violations
- Over **20,000 criminal reports** from victims
- A **6-year 3-month** prison sentence for the attacker (2024)

This report analyzes the incident from a SOC / Blue Team perspective, mapping the actions to MITRE ATT&CK and highlighting defensive failures and actionable lessons.

---

## 2. Timeline of Events

### **2017-11 — Port 3306 opened**
MySQL database port opened for maintenance and left exposed for **16 months**.

### **2017-12 — First unauthorized access**
Attacker gains access using weak credentials:
- `username: vastaamo`
- `password: zuukka66`
- `root` account with **no password**

### **2018-11 — 1 GB patient database exfiltrated**
Approximately 1GB of psychotherapy records is transferred to an external VPN.  
Due to lack of logging or monitoring, this remains undetected.

### **2019-03-13 — Port closed**
After 16 months of exposure, the port is finally shut.

### **2019-03-15 — Ransom attack**
Two days later the database is wiped and a ransom message left demanding cryptocurrency.

### **2020-09–10 — Blackmail & leak**
- CEO and IT staff receive extortion messages  
- The full patient database is published on the dark web  
- Individual victims are directly blackmailed

### **2023–2024 — Arrest & sentencing**
Attacker arrested (2023) and sentenced (2024) to 6 years 3 months.

---

## 3. Root Cause Analysis (RCA)

### **Security Misconfiguration**
- MySQL port 3306 exposed publicly  
- Firewall allowed unrestricted access  
- `root` account had **no password**

### **Weak Access Control**
- Static, guessable passwords  
- No MFA  
- Poor privileged account management

### **Lack of Logging & Monitoring**
- 1GB data exfiltration undetected  
- No SIEM correlation  
- No outbound traffic anomaly detection  
- No alerting on unusual database activity

### **Unencrypted Sensitive Data**
- Psychotherapy notes stored in plaintext  
- Direct GDPR violation

### **Governance Failures**
- No single owner for cybersecurity  
- No change management (port opened & forgotten)  
- No incident response plan  
- Minimal risk management despite sensitive data


---

## 4. MITRE ATT&CK Mapping


| Attack Phase        | Technique ID | Technique Name                          |
|---------------------|-------------|-----------------------------------------|
| Initial Access      | T1190       | Exploit Public-Facing Application       |
| Credential Access   | T1110       | Brute Force / Credential Guessing       |
| Privilege Escalation| T1068       | Exploitation for Privilege Escalation   |
| Defense Evasion     | T1070       | Log Deletion / Lack of Logging          |
| Collection          | T1005       | Data from Local System                  |
| Exfiltration        | T1041       | Exfiltration Over C2 Channel            |
| Impact              | T1486       | Data Encrypted / Destroyed for Impact   |

---

## 5. Attack Path Diagram

```mermaid
graph TD
    A[Internet] -->|Port 3306 Open| B(MySQL Server)
    B -->|root / no password| C{Unauthorized Access}
    C --> D[Full Patient Database]
    D -->|1GB Exfiltration| E[Mylvan VPN Server]
    C --> F[Backdoors Installed]
    F --> G[Undetected Access for 12+ Months]
    G --> H[Database Deletion + Ransom Note]

    style B fill:#ffb3b3,stroke:#333,stroke-width:2px
    style C fill:#ff6666,stroke:#333,stroke-width:2px
    style H fill:#ff3333,stroke:#333,stroke-width:2px
```

More diagrams are available in the [`diagrams/`](diagrams) folder.

---

## 6. What Went Wrong (Defensive Failures)

### **1. No SIEM or monitoring**
- No outbound anomaly detection  
- No detection of mass downloads  
- No indicator correlation  
- No long-term intrusion detection

### **2. No encryption**
Psychotherapy notes stored in plaintext → directly linkable to victims.

### **3. Identity and access management failures**
- Passwordless `root` account  
- Weak static passwords  
- No MFA

### **4. No risk management**
Critical production services left exposed for over a year.

### **5. No change management**
Port opened for maintenance and forgotten.

---

## 7. Recommended Controls (Technical)

- Close unnecessary ports; never expose databases publicly  
- Enforce MFA and strong password policies  
- Encrypt all sensitive data at rest and in transit  
- Deploy Microsoft Sentinel or similar SIEM  
- Enable alerts for:
  - Unusual SQL queries  
  - Large outbound transfers  
  - Impossible travel logins  
- Apply network segmentation  
- Implement least privilege access

---

## 8. Lessons Learned (Blue Team / SOC)

### **Microsoft Sentinel would have detected:**
- Mass data exfiltration  
- Anomalous query patterns  
- Foreign IP logins  
- Lateral movement / persistence  

### **Defender XDR would have surfaced:**
- Passwordless admin accounts  
- Suspicious processes/backdoors  
- Cross-domain correlation (identity + network + endpoint)

**Key learning:**  
> Most catastrophic breaches are preventable with basic monitoring, governance, and secure configuration.

---

## 9. Business Impact

- **Bankruptcy (2021)**  
- **CEO convicted (2023)**  
- **608 000€ GDPR fine**  
- Long-term reputational and financial damage  
- 33,000+ victims exposed to severe privacy harm  

---

## 10. Current Status (2025–2026) — Latest Developments in the Vastaamo Case

This section summarizes the most recent developments in the Vastaamo case during late 2025 and early 2026. It includes updates on legal proceedings, new suspects, victim compensation, and ongoing investigative efforts.

---

### 🔎 Key New Developments

| Topic | Description |
|-------|-------------|
| **New suspect: Patrick Newhard (USA)** | U.S. citizen Patrick Newhard has been charged with sending extortion emails related to the Vastaamo breach. He is not suspected of the intrusion itself, but of involvement in the extortion campaign. He was extradited from Estonia to the United States. |
| **Kivimäki released from custody (Autumn 2025)** | Aleksanteri Kivimäki was released from detention in September 2025 pending the Court of Appeal’s ruling. The District Court’s sentence of **6 years and 3 months** remains valid during the appeal. |
| **Court of Appeal proceedings concluded in late 2025; final ruling in February 2026** | The main hearings took place from 19 August to 26 November 2025. The Court of Appeal will issue its final decision **by 28 February 2026**. |
| **Victim compensation ongoing** | The Finnish State Treasury continues to process compensation claims. Several victim groups and legal representatives consider the compensation levels insufficient given the severity of the harm. |
| **Potential broader criminal involvement** | The Newhard case indicates that several individuals may have been involved in the extortion phase. International investigations are ongoing. |
| **GDPR fine remains in force** | The €608,000 administrative fine imposed on Vastaamo remains valid due to inadequate logging, poor security controls, and data protection failures. |

---

### 🧭 Summary: What Is Final — What Remains Open

#### ✔ Finalized
- Kivimäki’s District Court sentence: **6 years 3 months imprisonment**  
- **€608,000 GDPR fine**  
- Leakage of psychotherapy data confirmed  
- Widely regarded as the most severe privacy breach in Finnish history  

#### ⏳ Pending / Ongoing
- **Final Court of Appeal ruling due by 28 February 2026**  
- Potential new suspects or charges  
- Assessment of compensation levels  
- Clarification of international involvement in the extortion phase  

---

### 🌐 Sources

- Bitdefender — *US citizen charged in Vastaamo extortion case*  
  https://www.bitdefender.com/en-us/blog/hotforsecurity/vastaamo-psychotherapy-hack-us-citizen-charged-in-latest-twist-of-notorious-data-breach

- Databreaches.net — *Kivimäki walks free during appeal*  
  https://databreaches.net/2025/09/11/kivimaki-walks-free-during-appeal-over-vastaamo-data-breach

- Helsinki Court of Appeal — *Final ruling expected by 28 February 2026*  
  https://tuomioistuimet.fi/hovioikeudet/helsinginhovioikeus/fi/index/tiedotteet/2025/paakasittelyvastaamo-asiassaalkaaelokuussa2025r241302.html

- State Treasury of Finland — *Victims of the Vastaamo data breach*  
  https://www.valtiokonttori.fi/en/services/services-related-to-compensation-and-accidents/vastaamo

- EDPB — *Administrative fine imposed on Vastaamo*  
  https://www.edpb.europa.eu/news/national-news/2022/administrative-fine-imposed-psychotherapy-centre-vastaamo-data-protection_en

- Have I Been Pwned — *Leak overview*  
  https://haveibeenpwned.com/Breach/Vastaamo

---

### 📌 Summary

The Vastaamo case remains an active legal and cybersecurity matter.  
The **Court of Appeal’s final ruling in February 2026** will define ultimate legal responsibility.  
Meanwhile, new suspects, international investigations, and victim compensation processes continue to shape the case's significance.

---


## 11. Contact

**Satu Ikola**  
GitHub: https://github.com/SatuIkola  
LinkedIn: https://www.linkedin.com/in/satu-ikola

---

