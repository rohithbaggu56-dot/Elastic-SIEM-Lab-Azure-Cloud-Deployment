# 🔐 Azure Elastic SIEM Lab – Real-World SSH Attack Detection & Investigation

## 📌 Overview

This project simulates a real SOC environment using Elastic SIEM deployed on Microsoft Azure.

A publicly exposed Windows VM attracted live SSH brute-force and password spraying attacks from the internet. These attacks were collected, detected, and investigated using Elastic SIEM and threat intelligence platforms.

This is not simulated traffic – all activity observed is from real-world attackers.

---

## 🧱 Lab Architecture

* **Cloud Platform:** Microsoft Azure
* **SIEM:** Elastic Stack (Elasticsearch + Kibana + Elastic Agent)
* **SIEM Server:** Ubuntu VM
* **Target System:** Windows VM (publicly exposed)
* **Log Source:** SSH authentication logs (`sshd`)
* **Ingestion Method:** Elastic Agent (Filebeat integration)

## 🧭 Architecture & Data Flow

This diagram represents the complete attack and detection pipeline used in this lab. It shows how real-world attack traffic flows from the internet into the SIEM, how it is processed, and how alerts are generated and investigated.

<img width="1836" height="1869" alt="elastic-siem-lab html_202604270929_1" src="https://github.com/user-attachments/assets/09df493e-c378-4d15-816f-94281a79265e" />

> Note: This diagram represents my lab setup and detection flow implemented during this project.

### Key Flow:
- External attackers target the exposed Windows VM
- Elastic Agent collects endpoint and log data
- Logs are ingested into Elasticsearch for indexing
- Kibana SIEM applies detection rules (EQL/KQL)
- Alerts are generated and optionally forwarded to Sentinel

## 📊 SIEM Dashboards

Elastic Security dashboards were used to monitor alerts, system activity, and detection rule performance in real time.

<img width="1920" height="1080" alt="Screenshot 2026-04-24 120023" src="https://github.com/user-attachments/assets/669c89fe-1114-4ffa-b673-99ddb799fe9d" />

---

## ⚙️ Environment Setup

This lab was deployed in Microsoft Azure to simulate a real-world SOC monitoring environment.


### Azure Infrastructure

- Ubuntu VM → Elastic SIEM (Elasticsearch + Kibana)
- Windows VM → Publicly exposed target system


### SIEM Configuration
- Installed Elastic Stack on Ubuntu server
- Enrolled Elastic Agent for endpoint telemetry
- Enabled Security Solution dashboards and detection rules

### Log Collection
- Monitored SSH authentication logs (sshd)

Captured:
- Failed login attempts
- Successful logins
- Source IP addresses

## 🌐 Attack Observation

Once the Windows VM was exposed to the internet, attack activity began almost immediately.

- Observed a high volume of repeated SSH login attempts within a short time window, indicating automated attack behavior
- Multiple external IPs targeting the system
- Clear pattern of automated attacks (high-frequency failures)
  
These events triggered detection rules in Elastic SIEM, generating alerts for further investigation.

---

## 🚨 Detection

Elastic SIEM detection rules identified suspicious SSH activity based on repeated authentication failures and abnormal login patterns.

### 🔹 Password Spraying Detection

Rule: Potential Password Spraying Attack via SSH

- Multiple failed login attempts from a single IP  
- Attempts across multiple user accounts  
- High frequency within a short time window

These detections were triggered automatically based on Elastic prebuilt rules for SSH attack patterns.

<img width="1920" height="1080" alt="Screenshot 2026-04-24 123453" src="https://github.com/user-attachments/assets/f44632d6-c785-4e5f-b36b-c0f04902b47d" />


### 🔹 SSH Brute Force Detection

Rule: Potential Successful SSH Brute Force Attack

- Multiple failed login attempts observed
- Followed by a successful authentication event
- Indicates possible credential compromise attempt

<img width="1920" height="1080" alt="Screenshot 2026-04-24 122132" src="https://github.com/user-attachments/assets/eb603a54-10d2-4c4d-91e8-b41afb660d12" />

---

## 🔍 Investigation Process


### 🔹 Step 1 – Alert Analysis

Reviewed alerts in Elastic SIEM to identify suspicious patterns and prioritize investigation.

- Multiple alerts triggered within a short time window  
- Repeated SSH authentication failures observed  
- High alert volume from specific source IPs  

<img width="1920" height="1080" alt="Screenshot 2026-04-24 121250" src="https://github.com/user-attachments/assets/af0bf645-a1da-49f3-b797-8eece7fbdd47" />


### 🔹 Step 2 – Log Analysis (KQL)

Used KQL to investigate failed SSH authentication attempts:

- event.outcome: "failure" and process.name: "sshd"
- Confirmed continuous failed login attempts
- Identified automated attack behavior (rapid, repeated attempts)
- Verified activity across multiple usernames

<img width="1920" height="1080" alt="Screenshot 2026-04-24 124330" src="https://github.com/user-attachments/assets/6df8a391-abe2-486e-afaf-f24d43515a07" />

### 🔹 Step 3 – Identify Top Attacking IPs

Analyzed alerts by source IP to identify primary attackers.

- One IP responsible for thousands of login attempts
- Additional IPs showed lower but consistent activity

<img width="1920" height="1080" alt="Screenshot 2026-04-24 123359" src="https://github.com/user-attachments/assets/0ece50ff-e94a-43d7-9794-ce8004033837" />

### 🔹 Step 4 – Threat Intelligence Validation

Validated suspicious IP addresses using external threat intelligence sources.

#### AbuseIPDB
IP reported multiple times for malicious activity
Confidence of abuse: 100%

<img width="1920" height="1080" alt="Screenshot 2026-04-24 122705" src="https://github.com/user-attachments/assets/383f7527-731d-489a-97c4-4acf3fd2e7ad" />

#### VirusTotal
Multiple security vendors flagged the IP as malicious
Indicators included brute-force attempts and suspicious activity

<img width="1920" height="1080" alt="Screenshot 2026-04-24 122726" src="https://github.com/user-attachments/assets/fe62455c-a85d-40cf-aceb-291b4cef8944" />


## 📊 Key Metrics

- Total SSH failed login attempts: ~8,900+
- Total alerts generated: 52+
- Password spraying alerts: 48
- High severity brute-force alerts: 1
- Unique attacking IPs observed: 4+
- Highest attacking IP attempts: ~8,000+
- AbuseIPDB confidence: 100% (371 reports)
- VirusTotal detections: 8/94 vendors

---

## 📌 Key Findings

- Publicly exposed systems are quickly targeted by automated attacks  
- Password spraying is commonly used before brute-force success  
- One IP showed strong malicious reputation across multiple platforms  
- Successful authentication after failures indicates potential compromise attempt  
- Attack traffic originated from multiple geographic locations
  
---

## 🛡️ Recommendations & Mitigation

Based on the observed SSH brute-force and password spraying activity, the following actions are recommended:

### 🔹 Immediate Actions
- Block identified malicious IP addresses at firewall / NSG level  
- Disable password-based SSH authentication  
- Enforce SSH key-based authentication  

### 🔹 Hardening Measures
- Change default SSH port to reduce automated scanning noise  
- Implement account lockout policies after multiple failed attempts  
- Restrict SSH access to trusted IP ranges only  

### 🔹 Monitoring Improvements
- Enable alerting for repeated failed login attempts  
- Create dashboards to track attack trends over time  
- Integrate SIEM alerts with automated response (SOAR)  

### 🔹 Long-Term Security Enhancements
- Enable Multi-Factor Authentication (MFA) where applicable  
- Regularly review authentication logs for anomalies  
- Conduct periodic security assessments of exposed systems
  
---

## 🛡️ MITRE ATT&CK Mapping

* **TA0006 – Credential Access**

  * T1110 – Brute Force
  * T1110.003 – Password Spraying

* **TA0001 – Initial Access**

  * T1078 – Valid Accounts

---

## 🎯 What This Project Demonstrates

* SIEM deployment in a cloud environment
* Detection rule analysis and validation
* Log investigation using KQL
* Identifying attacker patterns
* Threat intelligence correlation
* Real-world SOC investigation workflow

---

## 📎 Conclusion

This project replicates a real SOC workflow:

* Detection → Investigation → Validation

It demonstrates hands-on experience with:

* Elastic SIEM
* Azure infrastructure
* Real-world attack data

---
