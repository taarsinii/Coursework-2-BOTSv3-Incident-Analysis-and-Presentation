# Coursework 2: BOTSv3 Incident Analysis and Presentation

# 1. Introduction

Security Operations Centres (SOCs) are in charge of protecting enterprise environments through continuous log monitoring, threat detection, and incident response [1]. Splunk Enterprise and the BOTSv3 (Boss of the SOC) dataset, which includes real-world security telemetry such as AWS CloudTrail, S3 access logs, hardware inventory, and Windows host monitoring, are used in this assessment to simulate real-world SOC operations [2], [3].

This report focuses on investigative tasks that relate to SOC Tier-1 and Tier-2 workflows such as identifying user behaviour, detecting misconfigurations, checking host posture, and analysing indicators of compromise. Splunk Enterprise functions as the primary SIEM platform for searching, correlating, and interpreting security events in compliance with professional SOC processes [2].

Scope of Investigation

This investigation focusses on the following analytical tasks:

* Validating installation and ingestion of the BOTSv3 dataset.
* Performing specific SPL queries on AWS and endpoint data sources.
* Detecting cloud misconfigurations, including insecure S3 bucket permissions.
* Verifying user identities engaged in unauthorised activity.
* Analysing differences in host operating systems and endpoint setups

Assumptions

The investigation was based on the following assumptions:

* The BOTSv3 represents an enterprise, cloud-enabled infrastructure.
* There are no additional network logs accessible beyond the dataset provided.
* All unusual events are considered as a potential security issues that must be investigated.

The main objective of this report is to use Splunk-based analytical approaches to solve BOTSv3 200-level AWS investigation questions while also showing professional SOC reasoning, clear evidence presentation, and professional cybersecurity analysis [1], [4].

## 2. SOC Roles & Incident Handling Reflection

Security Operations Centres (SOCs) use a structured tiered model to provide rapid detection, analysis, and remediation of security issues [1]. Each tier has specific responsibilities that facilitate continuous security monitoring.

### ➤ SOC Tier Responsibilities

| SOC Tier | Responsibilities | Relevance to BOTSv3 Investigation |
| :--- | :--- | :--- |
| **Tier 1:** Monitoring and Triage | Reviewing initial alerts, minimising noise, and confirming suspicious activity | Querying CloudTrail logs to detect unusual IAM activity and authentication anomalies (Q1-Q2) |
| **Tier 2:** Incident Analysis | In-depth investigation, contextual correlation, and incident scope determination | Analysis of S3 misconfigurations, public ACL changes, and unauthorised file uploads (Q4-Q7) |
| **Tier 3:** Threat Hunting and Advanced Response | Analysis of the root causes, long-term mitigating strategies, and intelligence-driven response | Finding risky endpoints, such BSTOLL-L.froth.ly, and suggesting architecture improvements (Q8) |

The investigative tasks performed throughout this investigation included Tier-1 and Tier-2 responsibilities such as alert verification, cloud misconfiguration analysis, and endpoint posture evaluation. The continual refinement of queries demonstrates how SOC analysts switch between data sources to validate assumptions and identify root causes [2].

### ➤ Incident Handling Methodology (NIST Framework)

The BOTSv3 exercise adheres to the NIST Incident Response Lifecycle, which offers a framework for enterprise issue handling.

| NIST Phase | How This Applied in BOTSv3 Investigation |
| :--- | :--- |
| **Preparation** | Validating the dashboard and making sure logs are accurately ingested and sourcetype configuration for early threat visibility. |
| **Detection and Analysis** | Identifying a publicly accessible S3 bucket, IAM anomalies, and missing MFA controls using CloudTrail telemetry. |
| **Containment** | Identifying the high-risk endpoint (BSTOLL-L.froth.ly) and figuring out which assets and people needed priority security. |
| **Eradication and Recovery** | Suggesting stronger **MFA** enforcement, better access control list (**ACL**) settings, and set up ongoing compliance monitoring. |

This exercise highlighted the importance of data visibility as without CloudTrail or S3 access logs, important misconfigurations would have gone undetected [5], [6]. Furthermore, iterative search refinement simulates real-world SOC operations, where uncertainty leads to pivoting across various data sources. If this were a live SOC event, automation like Splunk risk-based alerting and Security Orchestration, Automation and Response (SOAR) processes would speed up the response, lowering both the Mean Time to Detect threats (MTTD) and the Mean Time to Respond (MTTR) [2], [1].

Overall, the investigation highlighted how Splunk's technical findings effectively improve operational resilience by guiding SOC strategy, improving detection rules, and increasing cloud security governance.

## 3. Installation & Data Preparation

An effective Security Operations Centre (SOC) needs a reliable centralised log management and detection platform. Splunk Enterprise was installed locally for this investigation in order to simulate SOC log ingestion, analysis capabilities, and investigation processes using the BOTSv3 dataset.

### 3.1 Overview of Splunk Installation

| Resource | Purpose |
| :--- | :--- |
| **Splunk Enterprise** (local server) | The main SIEM platform for log ingestion, searching, alerting, dashboards, and monitoring security events. |
| **Splunk Universal Forwarder** | Securely forwards logs from endpoints to the Splunk indexer, which supports enterprise-scale data collecting. |
| **BOTSv3 Dataset** | Provides realistic enterprise security logs for threat analysis and security analyst training based on known attack scenarios. |

This setup simulates a functional Security Operations Center (SOC) environment by centralising cloud and endpoint telemetry into a single Security Information and Event Management (SIEM) platform, allowing for real-time threat detection and analysis [1], [2].

### 3.2 Steps for Installation

1.Download and install Splunk Enterprise

* Local instance running under Windows.

* Admin access is configured using secure login.

2.Start the Splunk Web

* Accessed using the browser interface.

  <img width="532" height="268" alt="image" src="https://github.com/user-attachments/assets/8a8968f4-3d8a-4fbb-91fb-0298b98baffb" />
  <img width="532" height="268" alt="image" src="https://github.com/user-attachments/assets/5a2df9d4-baae-43d2-bde1-1da156cdbb6f" />

### 3.3 BOTSv3 Dataset Ingestion

* BOTSv3 was downloaded from GitHub.
  Link: https://github.com/splunk/botsv3

  <img width="725" height="367" alt="image" src="https://github.com/user-attachments/assets/be1d9d47-c779-4da5-86d4-117efb1eef50" />

* The BOTSv3 dataset was imported into Splunk by selecting the Add Data then Upload process.

<img width="727" height="364" alt="image" src="https://github.com/user-attachments/assets/5407a12a-9a10-4597-89a9-fb672d55ed41" />

<img width="728" height="367" alt="image" src="https://github.com/user-attachments/assets/c58a37d3-635f-448f-ae61-5a8420f9daaf" />

<img width="726" height="366" alt="image" src="https://github.com/user-attachments/assets/b0b44af1-ad3c-43cb-82e2-9902261f0dc1" />

* All logs have been added to the Splunk botsv3 index.

* The dataset includes many main source types that are frequently used in SOC investigations:

| Sourcetype | Purpose |
| :--- | :--- |
| **aws:cloudtrail** | It analyses IAM activity, authentication behaviour, and changes to cloud configuration. |
| **aws:s3:accesslogs** | It records file uploads, downloads, and access attempts. |
| **hardware** | It provides information about server hardware and system configurations. |
| **winhostmon** | It includes the Windows operating system and host information. |






