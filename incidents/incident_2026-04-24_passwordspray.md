# Incident Report – Potential Password Spraying Attack via SSH

**Date/Time:** April 24, 2026  
**Analyst:** Rohith Baggu  
**Environment:** Azure Elastic SIEM Lab (Ubuntu SIEM server + Windows VM agent)  

---

## 📌 Summary
Elastic SIEM generated multiple **low‑severity alerts** titled *“Potential Password Spraying Attack via SSH”*.  
This detection was triggered by repeated failed login attempts from a single source IP targeting multiple user accounts within a short time frame.  
Threat intelligence validation confirmed the source IPs had a history of malicious activity.

---

## 🛡️ Detection Details
- **Rule Name:** Potential Password Spraying Attack via SSH  
- **Severity:** Low  
- **Risk Score:** 21  
- **MITRE ATT&CK Mapping:**
  - TA0006 – Credential Access
    - T1110 – Brute Force
    - T1110.001 – Password Guessing
    - T1110.003 – Password Spraying  
- **Alert Volume:** 48 alerts within 24 hours  
- **Top Source IPs:**
  - `139.59.42.82` – 79.2% of attempts  
  - `39.96.157.154` – 10.4%  
  - `157.50.158.21` – 8.3%  
  - `204.216.160.78` – 2.1%  

📸 Evidence: <img width="1920" height="1080" alt="Screenshot 2026-04-24 123453" src="https://github.com/user-attachments/assets/b38113f1-21cf-405d-a5c2-4f0de4e802a1" />

---

## 🔍 Investigation Timeline
1. **Initial Alert Review**  
   - Elastic SIEM flagged multiple failed SSH attempts from single IPs.  
   - Alert severity set to *Low* but volume was high.

2. **Log Analysis (KQL)**  
   - Query: `event.outcome:"failure" AND process.name:"sshd"`  
   - Verified continuous failures across multiple usernames.  
   - Confirmed automated attack behavior (rapid attempts).

3. **Threat Intelligence Validation**  
   - AbuseIPDB: Source IP `204.216.160.78` reported **371 times**, 100% confidence of abuse.  
   - VirusTotal: **8/94 vendors** flagged IP as malicious.  
   - Activity included brute force attempts and post‑exploitation behavior.

📸 Evidence:  
- `/screenshots/discover_failed_logins.png`  
- `/screenshots/abuseipdb.png`  
- `/screenshots/virustotal.png`

---

## 📊 Impact Assessment
- **Accounts Targeted:** Multiple usernames (e.g., `baikal`, `azureuser`)  
- **Attack Vector:** SSH password spraying  
- **Potential Risk:** Account compromise if weak/common passwords are used  
- **Alert Volume:** 48 low‑severity alerts in 24 hours  

---

## ✅ Analyst Conclusion
Attackers attempted password spraying against multiple accounts using common passwords.  
Although no confirmed compromise occurred in this case, the activity demonstrates the importance of:
- Enforcing strong password policies  
- Monitoring for repeated failed logins across accounts  
- Blocking abusive IPs at firewall/network level  
- Continuous SIEM monitoring with MITRE ATT&CK mapping  

---

## 📂 Incident Artifacts
- Elastic SIEM alert JSON (archived)  
- Screenshots: `/screenshots/password_spraying_alerts.png`, `/screenshots/discover_failed_logins.png`, `/screenshots/top_ips.png`, `/screenshots/abuseipdb.png`, `/screenshots/virustotal.png`  
- KQL queries used during investigation  
