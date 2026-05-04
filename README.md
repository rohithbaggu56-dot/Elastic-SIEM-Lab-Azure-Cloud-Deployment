# 🔐 Azure Elastic SIEM Lab – Real-World SSH Attack Detection & Investigation

## 📌 Overview

This project simulates a real SOC environment using Elastic SIEM deployed on Microsoft Azure.

A publicly exposed Windows VM attracted **live SSH brute-force and password spraying attacks from the internet**. These attacks were collected, detected, and investigated using Elastic SIEM and threat intelligence platforms.

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

### 1. Azure Infrastructure

* Deployed two virtual machines:

  * Ubuntu Server (Elastic SIEM)
  * Windows VM (target system)
* Enabled public internet access to simulate a real-world exposed system

### 2. SIEM Configuration

* Installed and configured Elastic Stack on Ubuntu
* Enrolled Elastic Agent for log collection
* Enabled Security Solution dashboards and detection rules

### 3. Log Collection

* Monitored SSH authentication logs
* Captured:

  * Failed login attempts
  * Successful logins
  * Source IP addresses

---

## 🌐 Attack Observation

After exposing the Windows VM to the internet:

* Immediate increase in SSH login attempts
* Multiple IPs attempting authentication
* Clear pattern of automated attacks

📷 **Initial Alerts Overview**
<img width="1920" height="1080" alt="Screenshot 2026-04-24 121250" src="https://github.com/user-attachments/assets/4ae47755-887e-4491-88ca-adb679a7ceeb" />

---

## 🚨 Detection

### 🔹 Password Spraying Detection

* Rule triggered: **Potential Password Spraying Attack via SSH**
* Behavior observed:

  * Same IP → multiple usernames
  * High number of failed attempts in short time

📷 **Password Spraying Alerts**
<img width="1920" height="1080" alt="Screenshot 2026-04-24 123453" src="https://github.com/user-attachments/assets/f8a296b1-3617-488f-aad1-33218d94b657" />

---

### 🔹 SSH Brute Force Detection

* Rule triggered: **Potential Successful SSH Brute Force Attack**
* Behavior observed:

  * Multiple failures followed by a successful login

📷 **Brute Force Alert Details**
<img width="1920" height="1080" alt="Screenshot 2026-04-24 122132" src="https://github.com/user-attachments/assets/e94dfd8e-adb8-47af-8b5a-cea302e8be0c" />

---

## 🔍 Investigation Process

### 🔹 Step 1 – Alert Analysis

* Reviewed alert summary in Elastic SIEM
* Identified high alert volume from specific IPs
* Noted repeated authentication failures

📷 **Alert Summary Dashboard**
👉 *(Add: alert count / severity image)*

---

### 🔹 Step 2 – Log Analysis (KQL)

Used KQL query to isolate failed SSH attempts:

```kql
event.outcome: "failure" and process.name: "sshd"
```

* Observed continuous failed login attempts
* Confirmed automated attack behavior

📷 **Log Analysis in Discover**
<img width="1920" height="1080" alt="Screenshot 2026-04-24 124330" src="https://github.com/user-attachments/assets/5442e460-01c3-4937-b704-89edbc7e2c3d" />

---

### 🔹 Step 3 – Identify Top Attacking IPs

* Aggregated alerts by `source.ip`
* Identified top attackers:

  * High-frequency IP generating thousands of attempts
  * Multiple additional IPs with lower activity

📷 **Top Source IP Visualization**

<img width="1920" height="1080" alt="Screenshot 2026-04-24 123359" src="https://github.com/user-attachments/assets/dde774c8-5510-41bd-acce-57e34172f19f" />
---

### 🔹 Step 4 – Threat Intelligence Validation

#### ✅ AbuseIPDB

* IP reported multiple times for malicious activity
* **Confidence of abuse: 100%**
* Indicates known attacker infrastructure

<img width="1920" height="1080" alt="Screenshot 2026-04-24 122705" src="https://github.com/user-attachments/assets/baf09c12-f363-4dd1-a42e-22ce04af9c0b" />

---

#### ✅ VirusTotal

* Multiple vendors flagged IP as malicious
* Observed indicators:

  * SSH brute-force attempts
  * Suspicious activity patterns

<img width="1920" height="1080" alt="Screenshot 2026-04-24 122726" src="https://github.com/user-attachments/assets/a7d0f633-e23b-4628-8a04-79c3367ec374" />

---

## 📊 Key Metrics

* Total SSH failed login attempts: **~8,900+**
* Total alerts generated: **52+**
* Password spraying alerts: **48**
* High severity brute-force alerts: **1**
* Unique attacking IPs observed: **4+**
* Highest attacking IP attempts: **~8,000+**
* AbuseIPDB confidence: **100% (371 reports)**
* VirusTotal detections: **8/94 vendors**

---

## 📌 Key Findings

* Publicly exposed systems are quickly targeted by automated attacks
* Password spraying is commonly used before brute-force success
* One IP showed strong malicious reputation across multiple platforms
* Successful authentication after failures indicates potential compromise attempt
* Attack traffic originated from multiple geographic locations

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

## 🚀 Future Improvements

* Automate IP blocking using response actions
* Integrate SOAR for incident response
* Add geolocation-based alerting
* Build long-term attack trend dashboards

---

## 📎 Conclusion

This project replicates a real SOC workflow:

* Detection → Investigation → Validation

It demonstrates hands-on experience with:

* Elastic SIEM
* Azure infrastructure
* Real-world attack data

---
