# AI Powered SOC Automation Lab
End-to-End SOC Automation using Splunk, Sysmon, n8n, VirusTotal, AbuseIPDB, Gemini AI and DFIR-IRIS.

# AI Powered SOC Automation Lab

![Splunk](https://img.shields.io/badge/Splunk-Enterprise-black?logo=splunk)
![Sysmon](https://img.shields.io/badge/Sysmon-Microsoft-blue)
![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-orange?logo=n8n)
![DFIR-IRIS](https://img.shields.io/badge/DFIR--IRIS-Incident%20Response-purple)
![VirusTotal](https://img.shields.io/badge/VirusTotal-Threat%20Intelligence-blue)
![AbuseIPDB](https://img.shields.io/badge/AbuseIPDB-IP%20Reputation-red)
![Google Gemini](https://img.shields.io/badge/Google-Gemini%20AI-4285F4?logo=google)
![Windows](https://img.shields.io/badge/Windows%2010-Endpoint-0078D6?logo=windows)

---

# AI Powered SOC Automation Lab

This project demonstrates an end-to-end Security Operations Center (SOC) automation pipeline built in a virtual lab environment.

The objective of this project is to simulate how modern SOC teams automatically detect security events, enrich indicators of compromise (IOCs) using Threat Intelligence platforms, leverage Artificial Intelligence for incident triage, and automatically create incidents inside a DFIR platform.

Instead of manually investigating every alert, this workflow automates repetitive Tier-1 SOC analyst tasks while providing structured incident reports for further investigation.

---

# Project Objectives

- Detect suspicious PowerShell execution using Splunk
- Automate incident response using n8n
- Enrich file hashes using VirusTotal
- Enrich IP addresses using AbuseIPDB
- Generate AI-assisted incident analysis using Google Gemini
- Automatically create incidents inside DFIR-IRIS

---

# Architecture

<img width="1007" height="1024" alt="ChatGPT Image Jul 5, 2026, 11_52_13 PM" src="https://github.com/user-attachments/assets/35189493-ef31-42d4-990f-753a3b3ed68b" />


```

```

---

# Technologies Used

| Category | Technology |
|----------|------------|
| Endpoint Monitoring | Sysmon |
| SIEM | Splunk Enterprise |
| SOAR | n8n |
| Threat Intelligence | VirusTotal API |
| IP Reputation | AbuseIPDB API |
| AI Analysis | Google Gemini |
| Case Management | DFIR-IRIS |
| Operating Systems | Windows 10, Kali Linux |
| Automation | REST APIs |

---

# Workflow

This section demonstrates how a PowerShell execution event travels through the complete SOC automation pipeline, from detection to incident creation.

Step 1 – Endpoint Monitoring (Sysmon)

<img width="1024" height="768" alt="VirtualBox_windows 10_06_07_2026_00_26_28" src="https://github.com/user-attachments/assets/6113c5fb-28c2-4f32-94db-9f01c7ad5d6d" />


After a PowerShell command is executed on the Windows endpoint, Sysmon records a Process Creation (Event ID 1) containing valuable forensic information.

Information collected includes:

- Hostname
- Username
- Process Image
- Command Line
- SHA256 Hash
- Parent Process
- Event Timestamp

These logs provide the raw telemetry used by the SIEM for detection.

---

**Step 2 – Log Collection & Detection (Splunk)**

<img width="1920" height="1042" alt="image" src="https://github.com/user-attachments/assets/2698a026-dbd9-418c-8d7c-c23fa00c6cf6" />


The Sysmon logs are forwarded to Splunk Enterprise through the Splunk Universal Forwarder.

A custom SPL detection searches for suspicious PowerShell executions.

Detection Query
```text
index=* source="WinEventLog:Microsoft-Windows-Sysmon/Operational" "powershell.exe"
```
When the rule matches, Splunk automatically triggers a webhook.

---

**Step 3 – Webhook Trigger (n8n)**

<img width="1856" height="953" alt="image" src="https://github.com/user-attachments/assets/ee33eaf4-f4e1-4e06-9059-bd93c95b8eb5" />


Splunk sends the alert to n8n using an HTTP webhook.

The webhook receives:

- Host
- User
- PowerShell Image
- Command Line
- SHA256
- Source IP
- Timestamp

These values become the input for the automation workflow.

---

**Step 4 – VirusTotal Enrichment**

- VT_Malicious: 0
- VT_Suspicious: 0
- VT_Harmless: 0
- VT_Undetected: 70

The SHA256 hash is submitted to the VirusTotal API.

VirusTotal returns:

- Detection Count
- Malicious Score
- Suspicious Score
- Harmless Score
- Undetected Engines

This determines whether the executable has already been identified by security vendors.

---

**Step 5 – AbuseIPDB Enrichment**

- IP: 8.8.8.8
- Abuse Score: 0
- Country: US
- ISP: Google LLC
- Usage Type: Content Delivery Network

The source IP is queried against AbuseIPDB.

Information returned includes:

- Abuse Confidence Score
- ISP
- Country
- Usage Type

This provides additional context about the IP involved in the event.

---

**Step 6 – AI Security Analysis (Google Gemini)**

Prompt:

The following prompt is sent to Google Gemini after the alert has been enriched with VirusTotal and AbuseIPDB intelligence.

```text
You are a Tier-1 SOC Analyst.

Analyze the following security alert.

Host: <Hostname>

User: <Username>

Image: <Process Image>

Command Line: <Command Line>

SHA256: <SHA256 Hash>

Source IP: <Source IP>

Time: <Event Timestamp>

==============================
VirusTotal Results
==============================

Malicious: <Malicious Count>
Suspicious: <Suspicious Count>
Harmless: <Harmless Count>
Undetected: <Undetected Count>

==============================
AbuseIPDB Results
==============================

Abuse Score: <Abuse Confidence Score>
Country: <Country>
ISP: <ISP>
Usage Type: <Usage Type>

As a SOC analyst:

1. Summarize the incident.
2. Assess the risk (Low, Medium, High, or Critical).
3. Map the activity to the MITRE ATT&CK framework (include Technique ID and Technique Name where applicable).
4. Explain how the VirusTotal and AbuseIPDB results influence the investigation.
5. Recommend immediate investigation and containment actions.
6. State whether the incident should be escalated to a Tier-2 analyst.

Respond in the following format only:

Summary:
Risk:
MITRE ATT&CK:
Threat Intelligence:
Recommended Actions:
Escalation:
```



The enriched event is forwarded to Google Gemini.

The AI generates:

- Incident Summary
- Risk Assessment
- MITRE ATT&CK Mapping
- Threat Intelligence Interpretation
- Investigation Recommendations
- Escalation Decision

This simulates an automated Tier-1 SOC analyst.

---

**Step 7 – Incident Creation (DFIR-IRIS)**

<img width="1606" height="873" alt="image" src="https://github.com/user-attachments/assets/91661241-eb65-439b-bd55-405585113325" />


The enriched data and AI analysis are automatically submitted to DFIR-IRIS.

Each incident contains:

Original alert
VirusTotal results
AbuseIPDB results
AI-generated analysis
Investigation recommendations

The analyst receives a fully enriched incident instead of a raw alert.

---

# Features

- Automated SOC workflow
- PowerShell execution detection
- VirusTotal hash enrichment
- AbuseIPDB IP reputation lookup
- AI assisted incident analysis
- Automatic DFIR-IRIS incident creation
- API-driven workflow automation
- MITRE ATT&CK mapping
- Threat intelligence integration

---

# Skills Demonstrated

- Security Operations Center (SOC)
- Detection Engineering
- SIEM Administration
- SOAR Development
- Incident Response
- Threat Intelligence
- REST API Integration
- Docker
- Linux Administration
- Splunk SPL
- AI assisted Security Operations

#  What I Learned

Building this project provided hands-on experience in designing and implementing an end-to-end SOC automation workflow. Throughout the development process, I strengthened my understanding of how different security technologies work together in a modern Security Operations Center.

Key takeaways from this project include:

- Configuring Microsoft Sysmon to generate detailed Windows security telemetry.
- Creating Splunk detection rules using SPL to identify suspicious PowerShell execution.
- Designing and building automation workflows in n8n using webhooks and REST APIs.
- Integrating external threat intelligence platforms such as VirusTotal and AbuseIPDB to enrich security alerts.
- Using Google Gemini AI to automate Tier-1 SOC analysis, including incident summaries, MITRE ATT&CK mapping, and investigation recommendations.
- Automating incident creation in DFIR-IRIS with enriched security context.
- Understanding how SIEM, SOAR, threat intelligence, AI, and incident response platforms integrate to reduce manual analyst effort.

This project also improved my skills in API integration, troubleshooting, workflow automation, and documenting security engineering projects for real-world portfolio presentation.

