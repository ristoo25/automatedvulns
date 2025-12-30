# 🛡️ Automated Vulnerability Lifecycle & SOC Orchestration

![Python](https://img.shields.io/badge/Built%20With-Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Jira](https://img.shields.io/badge/Integration-Jira-0052CC?style=for-the-badge&logo=jira&logoColor=white)
![Tenable](https://img.shields.io/badge/Scanner-Tenable.io-002F6C?style=for-the-badge&logo=tenable&logoColor=white)
![Burp Suite](https://img.shields.io/badge/Scanner-Burp%20Suite%20Enterprise-FF6633?style=for-the-badge&logo=burpsuite&logoColor=white)

> **Architectural Showcase:** An automated middleware solution connecting **Tenable.io** and **Burp Suite Enterprise** with **Jira** to enforce a complete vulnerability lifecycle (Detection &rarr; Remediation &rarr; Verification).

---

## ⚠️ Proprietary Code Notice
**Please Note:** Due to Non-Disclosure Agreements (NDA) and the enterprise nature of the infrastructure where this tool was deployed, the source code cannot be publicly shared. 

This repository serves as a **technical case study** documenting the architecture, logic, and operational impact of the automation I engineered. It demonstrates my ability to design **SOAR** workflows, manage API integrations, and drive security posture improvements.

---

## 📖 Project Overview
The goal of this project was to move beyond simple scanning and create a **dynamic vulnerability lifecycle**. 

By bridging the gap between scanners (Tenable/Burp Enterprise) and the ticketing system (Jira), I eliminated manual triage and enabled real-time status synchronization. The system intelligently filters assets, ensuring the SOC team only interacts with websites that contain **active, verified vulnerabilities**, drastically reducing alert fatigue.

### Key Capabilities
* 🔄 **Full Lifecycle Sync:** Automatically closes tickets when scans confirm a fix, and **reopens** them if the vulnerability resurfaces.
* 🏗️ **Hierarchical Asset Management:** Implemented a logic where websites act as parent entities and vulnerabilities as sub-tasks.
* 🎯 **Relevance Filtering:** Automated logic checks for active sub-tasks; if a website has no open vulnerabilities, it is marked as "Clean" and removed from the SOC queue.
* 📊 **Metrics & Strategy:** Structured data to allow for reporting on MTTR (Mean Time To Remediate) and Risk Density, aiding in prioritization.
* 📉 **Noise Reduction:** Filters out 'Info' level findings while retaining 'Low' through 'Critical' for full audit trails.

---

## 🏗️ Logic Flow & Lifecycle

The system maintains a strict Parent-Child relationship to keep the SOC workspace clean.

```mermaid
graph TD
    A[Scan Data Ingestion] -->|Tenable / Burp Enterprise| B(Python Middleware)
    B --> C{Map to Asset/Website}
    
    C --> D{Check Active Vulnerabilities}
    D -->|New Vulnerability Found| E["Create/Link Sub-task to Website Parent"]
    D -->|Vulnerability Fixed| F["Auto-Close Sub-task"]
    D -->|Vulnerability Reappeared| G["Auto-Reopen Sub-task (Regression)"]
    
    H{Website Status Check}
    E --> H
    F --> H
    G --> H
    
    H -->|Has Active Sub-tasks?| I["Mark Parent: ACTION REQUIRED"]
    I --> J[Route to SOC Queue]
    
    H -->|No Active Sub-tasks?| K["Mark Parent: CLEAN"]
    K --> L[Remove from SOC View]
    
    J --> M[Reporting & Metrics]
