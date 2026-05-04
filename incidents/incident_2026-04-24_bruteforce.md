# Incident Report – Potential Successful SSH Brute Force Attack

**Date/Time:** April 24, 2026  
**Analyst:** Rohith Baggu  
**Environment:** Azure Elastic SIEM Lab (Ubuntu SIEM server + Windows VM agent)  

---

## 📌 Summary
Elastic SIEM generated a **high‑severity alert** titled *“Potential Successful SSH Brute Force Attack”*.  
This alert was triggered after multiple failed SSH login attempts were followed by a successful authentication from the same source IP.  
Threat intelligence validation confirmed the source IP had a history of malicious activity.

---

## 🛡️ Detection Details
- **Rule Name:** Potential Successful SSH Brute Force Attack  
- **Severity:** High  
- **Risk Score:** 73  
- **MITRE ATT&CK Mapping:**
  - TA0006 – Credential Access
    - T1110 – Brute Force
    - T1110.001 – Password Guessing
    - T1110.003 – Password Spraying
  - TA0001 – Initial Access
    - T1078 – Valid Accounts  
- **Alert Timestamp:** Apr 24, 2026 @ 10:53:01.344  
- **Alert Reason:** Authentication event with process `sshd` from source IP `157.50.158.21` targeting user `azureuser` on host `suricata-vm`.

📸 Evidence: <img width="1920" height="1080" alt="Screenshot 2026-04-24 122132" src="https://github.com/user-attachments/assets/693f36cf-577c-4671-932b-59340634d125" />

---

## 🔍 Investigation Timeline
1. **Initial Alert Review**  
   - Elastic SIEM flagged multiple failed SSH attempts followed by a successful login.  
   - Alert severity set to *High* due to credential compromise risk.

2. **Log Analysis (KQL)**  
   - Query: `event.outcome:"failure" AND process.name:"sshd"`  
   - Verified repeated failures from source IP `157.50.158.21` prior to success.  
   - Confirmed automated attack behavior (rapid attempts).

3. **Threat Intelligence Validation**  
   - AbuseIPDB: Source IP reported **371 times**, 100% confidence of abuse.  
   - VirusTotal: **8/94 vendors** flagged IP as malicious.  
   - Activity included brute force attempts and post‑exploitation behavior.

📸 Evidence: 
<img width="1920" height="1080" alt="Screenshot 2026-04-24 124330" src="https://github.com/user-attachments/assets/911b1d85-493c-4049-85d5-645891d71312" />

<img width="1920" height="1080" alt="Screenshot 2026-04-24 122726" src="https://github.com/user-attachments/assets/b22b2a10-367e-45ed-ad46-4a8ccc2c8c8c" />

<img width="1920" height="1080" alt="Screenshot 2026-04-24 122705" src="https://github.com/user-attachments/assets/6c4b21ac-e199-48e0-bc02-99a64d0839f1" />

---

## 📊 Impact Assessment
- **Compromised Account:** `azureuser` (Windows VM)  
- **Attack Vector:** SSH brute force  
- **Potential Risk:** Unauthorized access to VM, lateral movement, persistence setup  
- **Alert Volume:** 1 high‑severity alert, preceded by 48 low‑severity password spraying alerts  

---

## ✅ Analyst Conclusion
The attacker successfully brute‑forced credentials for the `azureuser` account.  
This incident demonstrates the importance of:
- Enforcing strong password policies  
- Limiting SSH exposure to the internet  
- Implementing firewall rules and network segmentation  
- Continuous monitoring with SIEM + threat intelligence enrichment  

---

## 📂 Incident Artifacts
- Elastic SIEM alert JSON (archived)  
- Screenshots: `/screenshots/bruteforce_alert.png`, `/screenshots/discover_failed_logins.png`, `/screenshots/abuseipdb.png`, `/screenshots/virustotal.png`  
- KQL queries used during investigation  
