
**🌐 Languages:**  
🇬🇧 English | 🇫🇮 [Suomi](README.fi.md)

# Vastaamo Data Breach — Incident Post-Mortem Report  
### Technical Case Study — Satu Ikola  
*(Updated to reflect legal and investigative developments through 2026)*

---

## 📝 Vastaamo Case Summary

<p align="center">
  <img 
    src="https://raw.githubusercontent.com/SatuIkola/vastaamo-incident-postmortem/main/images/vastaamo_banner_900x300.png"
    width="750"
    alt="Vastaamo Case Summary Banner">
</p>

<p align="center">
  🇫🇮 <a href="README.fi.md">Read in Finnish</a>
  &nbsp;•&nbsp;
  🇬🇧 <a href="README.md">Read in English</a>
</p>

---

## **1. Executive Summary**

The Vastaamo breach (2017–2020) is one of the most severe privacy incidents in Finland’s history.  
A publicly exposed MySQL database, passwordless administrative accounts, lack of monitoring, and unencrypted psychotherapy notes enabled attackers to access, exfiltrate, and eventually leak sensitive data of **over 33,000 patients**.

**Key consequences:**
- **Bankruptcy** of the company (2021)  
- **608,000 € GDPR fine**  
- Direct blackmail attempts against patients  
- CEO convicted of data protection crimes  
- Attacker sentenced to **6 years 3 months** (pending final appellate ruling in 2026)

This report analyzes the breach from a SOC / Blue Team perspective, provides a MITRE ATT&CK mapping, highlights defensive failures, and documents current legal developments.

---

## **2. Timeline of Events**

### **2017-11 — Port 3306 opened**
MySQL database port opened for maintenance and left exposed for **~16 months**.

### **2017-12 — First unauthorized login**
Attacker gains access using weak credentials:
- `username: vastaamo`
- `password: zuukka66`
- `root` account had **no password**

### **2018-11 — 1GB data exfiltrated**
~1GB of patient data transferred to an external VPN.  
Due to missing logging, the event was never detected.

### **2019-03-13 — Port closed**
After 16 months of exposure, the database port is finally closed.

### **2019-03-15 — Ransom attack**
Two days later, the database is wiped and a ransom note appears demanding cryptocurrency.

### **2020-09–10 — Blackmail & leak**
- Management receives new extortion messages  
- Full database is published on the dark web  
- Individual patients are directly targeted and blackmailed  

### **2023–2024 — Arrest and sentencing**
Attacker arrested and sentenced to **6 years 3 months** imprisonment.

---

## **3. Root Cause Analysis (RCA)**

### **Security Misconfiguration**
- MySQL port 3306 publicly exposed  
- No firewall restrictions  
- Passwordless `root` account  

### **Weak Access Control**
- Weak static passwords  
- No MFA  
- Poor privileged account management  

### **Lack of Logging & Monitoring**
- 1GB exfiltration undetected  
- No SIEM correlation  
- No outbound anomaly detection  
- No alerts for unusual SQL activity  

### **Unencrypted Sensitive Data**
- Psychotherapy notes stored in plaintext  
- Clear GDPR violation  

### **Governance Failures**
- No clear cybersecurity ownership  
- No change management  
- No incident response plan  
- No risk assessments proportional to data sensitivity  

---

## **4. MITRE ATT&CK Mapping**

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

## **5. Attack Path Diagram**

```mermaid
graph TD
    A[Internet] -->|Port 3306 Open| B(MySQL Server)
    B -->|root / no password| C{Unauthorized Access}
    C --> D[Full Patient Database]
    D -->|1GB Exfiltration| E[Mylvan VPN Server]
    C --> F[Backdoors Installed]
    F --> G[Undetected Access for 12+ Months]
    G --> H[Database Deletion + Ransom Note]

    style B fill:#e8d7ff,stroke:#333,stroke-width:1px
    style C fill:#ff6666,stroke:#333,stroke-width:2px
    style H fill:#ff3333,stroke:#333,stroke-width:2px
```

---

## **6. Defensive Failures**

### **1. No SIEM or security monitoring**
- No anomaly detection  
- No mass-download alerts  
- No long-term intrusion detection  

### **2. No encryption**
Sensitive psychotherapy records were stored in plaintext → severe GDPR breach.

### **3. Identity & Access Management failures**
- Passwordless administrator account  
- Weak static passwords  
- No MFA  

### **4. Lack of risk management**
A critical production database was exposed to the internet for over a year without detection.

### **5. Poor change management**
Port 3306 was opened for maintenance and forgotten.  
No documentation, no ownership, no review process.

---

## **7. Recommended Technical Controls**

- Do not expose databases directly to the internet  
- Enforce MFA and strong password policies  
- Encrypt all sensitive data (at rest + in transit)  
- Deploy SIEM such as Microsoft Sentinel to detect:
  - Unusual SQL queries  
  - Large outbound transfers  
  - Impossible travel logins  
- Implement network segmentation  
- Apply least privilege principles across systems  

---

## **8. SOC & Blue Team Lessons Learned**

### **Microsoft Sentinel would have detected:**
- Large-scale outbound data exfiltration  
- Anomalous SQL query patterns  
- Logins from unusual geographic locations  
- Persistence or lateral movement activity  

### **Defender XDR would have surfaced:**
- Passwordless admin accounts  
- Suspicious backdoor processes  
- Multi-signal correlation across identity, network, and endpoint  

> **Key lesson:** Most catastrophic breaches are preventable with basic security hygiene: secure configuration, IAM discipline, monitoring, and governance.

---

## **9. Business Impact**

- **Bankruptcy (2021)**  
- **608,000 € GDPR fine**  
- CEO convicted of data protection crimes  
- Over 33,000 victims exposed to privacy and identity theft risks  
- Severe long-term reputational and financial damage  

---

## **10. Current Status (2025–2026)**

### 🔎 Key developments
- **Patrick Newhard (USA)** charged for sending Vastaamo-related extortion emails  
- **Aleksanteri Kivimäki** released from custody pending appeal (September 2025)  
- **Court of Appeal ruling expected by 28 February 2026**  
- Victim compensation process ongoing via State Treasury  
- Investigators exploring possible additional collaborators  

For full details, see the Finnish report:  
🇫🇮 [README.fi.md](README.fi.md)

---

## **11. Contact**

**Satu Ikola**  
GitHub: https://github.com/SatuIkola  
LinkedIn: https://www.linkedin.com/in/satu-ikola

---


