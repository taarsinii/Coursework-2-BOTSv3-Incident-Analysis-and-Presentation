# Coursework 2: BOTSv3 Incident Analysis and Presentation

### **YouTube video presentation (max 10 minutes):**
##
# **1. Introduction**

Security Operations Centres (**SOCs**) are in charge of protecting enterprise environments through continuous log monitoring, threat detection, and incident response [1]. Splunk Enterprise and the BOTSv3 (**Boss of the SOC**) dataset, which includes real-world security telemetry such as AWS CloudTrail, S3 access logs, hardware inventory, and Windows host monitoring, are used in this assessment to simulate real-world SOC operations [2], [3].

This report focuses on investigative tasks that relate to SOC Tier-1 and Tier-2 workflows such as identifying user behaviour, detecting misconfigurations, checking host posture, and analysing indicators of compromise. Splunk Enterprise functions as the primary SIEM platform for searching, correlating, and interpreting security events in compliance with professional SOC processes [2].

**Scope of Investigation**

This investigation focusses on the following analytical tasks:

* Validating installation and ingestion of the BOTSv3 dataset.
* Performing specific SPL queries on AWS and endpoint data sources.
* Detecting cloud misconfigurations, including insecure S3 bucket permissions.
* Verifying user identities engaged in unauthorised activity.
* Analysing differences in host operating systems and endpoint setups

**Assumptions**

The investigation was based on the following assumptions:

* The BOTSv3 represents an enterprise, cloud-enabled infrastructure.
* There are no additional network logs accessible beyond the dataset provided.
* All unusual events are considered as a potential security issues that must be investigated.

The main objective of this report is to use Splunk-based analytical approaches to solve BOTSv3 200-level AWS investigation questions while also showing professional SOC reasoning, clear evidence presentation, and professional cybersecurity analysis [1], [4].

# **2. SOC Roles & Incident Handling Reflection**

Security Operations Centres (**SOCs**) use a structured tiered model to provide rapid detection, analysis, and remediation of security issues [1]. Each tier has specific responsibilities that facilitate continuous security monitoring.

### ➤ SOC Tier Responsibilities

| **SOC Tier** | **Responsibilities** | **Relevance to BOTSv3 Investigation** |
| :--- | :--- | :--- |
| **Tier 1:** Monitoring and Triage | Reviewing initial alerts, minimising noise, and confirming suspicious activity | Querying CloudTrail logs to detect unusual IAM activity and authentication anomalies (Q1-Q2) |
| **Tier 2:** Incident Analysis | In-depth investigation, contextual correlation, and incident scope determination | Analysis of S3 misconfigurations, public ACL changes, and unauthorised file uploads (Q4-Q7) |
| **Tier 3:** Threat Hunting and Advanced Response | Analysis of the root causes, long-term mitigating strategies, and intelligence-driven response | Finding risky endpoints, such BSTOLL-L.froth.ly, and suggesting architecture improvements (Q8) |

The investigative tasks performed throughout this investigation included Tier-1 and Tier-2 responsibilities such as alert verification, cloud misconfiguration analysis, and endpoint posture evaluation. The continual refinement of queries demonstrates how SOC analysts switch between data sources to validate assumptions and identify root causes [2].

### ➤ **Incident Handling Methodology (NIST Framework)**

The BOTSv3 exercise adheres to the NIST Incident Response Lifecycle, which offers a framework for enterprise issue handling.

| **NIST Phase** | **How This Applied in BOTSv3 Investigation** |
| :--- | :--- |
| **Preparation** | Validating the dashboard and making sure logs are accurately ingested and sourcetype configuration for early threat visibility. |
| **Detection and Analysis** | Identifying a publicly accessible S3 bucket, IAM anomalies, and missing MFA controls using CloudTrail telemetry. |
| **Containment** | Identifying the high-risk endpoint (**BSTOLL-L.froth.ly**) and figuring out which assets and people needed priority security. |
| **Eradication and Recovery** | Suggesting stronger **MFA** enforcement, better access control list (**ACL**) settings, and set up ongoing compliance monitoring. |

This exercise highlighted the importance of data visibility as without CloudTrail or S3 access logs, important misconfigurations would have gone undetected [5], [6]. Furthermore, iterative search refinement simulates real-world SOC operations, where uncertainty leads to pivoting across various data sources. If this were a live SOC event, automation like Splunk risk-based alerting and Security Orchestration, Automation and Response (**SOAR**) processes would speed up the response, lowering both the Mean Time to Detect threats (**MTTD**) and the Mean Time to Respond (**MTTR**) [2], [1].

Overall, the investigation highlighted how Splunk's technical findings effectively improve operational resilience by guiding SOC strategy, improving detection rules, and increasing cloud security governance.

# 3. Installation & Data Preparation

An effective Security Operations Centre (**SOC**) needs a reliable centralised log management and detection platform. Splunk Enterprise was installed locally for this investigation in order to simulate SOC log ingestion, analysis capabilities, and investigation processes using the BOTSv3 dataset.

## 3.1 Overview of Splunk Installation

| **Resource** | **Purpose** |
| :--- | :--- |
| **Splunk Enterprise** (local server) | The main SIEM platform for log ingestion, searching, alerting, dashboards, and monitoring security events. |
| **Splunk Universal Forwarder** | Securely forwards logs from endpoints to the Splunk indexer, which supports enterprise-scale data collecting. |
| **BOTSv3 Dataset** | Provides realistic enterprise security logs for threat analysis and security analyst training based on known attack scenarios. |

This setup simulates a functional Security Operations Center (**SOC**) environment by centralising cloud and endpoint telemetry into a single Security Information and Event Management (**SIEM**) platform, allowing for real-time threat detection and analysis [1], [2].

## **3.2 Steps for Installation**

**1.Download and install Splunk Enterprise**

* Local instance running under Windows.

* Admin access is configured using secure login.

**2.Start the Splunk Web**

* Accessed using the browser interface.

  <img width="532" height="268" alt="image" src="https://github.com/user-attachments/assets/8a8968f4-3d8a-4fbb-91fb-0298b98baffb" />
  <img width="532" height="268" alt="image" src="https://github.com/user-attachments/assets/5a2df9d4-baae-43d2-bde1-1da156cdbb6f" />

## **3.3 BOTSv3 Dataset Ingestion**

* BOTSv3 was downloaded from GitHub.
  Link: **https://github.com/splunk/botsv3**

  <img width="725" height="367" alt="image" src="https://github.com/user-attachments/assets/be1d9d47-c779-4da5-86d4-117efb1eef50" />

* The **BOTSv3** dataset was imported into Splunk by selecting the **Add Data** then **Upload process**.

<img width="727" height="364" alt="image" src="https://github.com/user-attachments/assets/5407a12a-9a10-4597-89a9-fb672d55ed41" />

<img width="728" height="367" alt="image" src="https://github.com/user-attachments/assets/c58a37d3-635f-448f-ae61-5a8420f9daaf" />

<img width="726" height="366" alt="image" src="https://github.com/user-attachments/assets/b0b44af1-ad3c-43cb-82e2-9902261f0dc1" />

* All logs have been added to the Splunk botsv3 index.

* The dataset includes many main source types that are frequently used in SOC investigations:

| **Sourcetype** | **Purpose** |
| :--- | :--- |
| **aws:cloudtrail** | It analyses IAM activity, authentication behaviour, and changes to cloud configuration. |
| **aws:s3:accesslogs** | It records file uploads, downloads, and access attempts. |
| **hardware** | It provides information about server hardware and system configurations. |
| **winhostmon** | It includes the Windows operating system and host information. |

These sourcetypes work together to provide the visibility necessary in an enterprise SOC.

## **3.4 Validation of the Dataset Ingestion**

Several kinds of validation checks were run to verify the successful data ingestion:

**Index Event Count Verification:**
<img width="354" height="39" alt="image" src="https://github.com/user-attachments/assets/8ce59ace-8351-46c0-a645-0f0f970781de" />

<img width="532" height="268" alt="image" src="https://github.com/user-attachments/assets/f6ccd56d-677a-40d6-b79b-c352f6dffe0b" />

➤ This proved that the events are successfully indexed.

**Sourcetype Verification:**
<img width="354" height="39" alt="image" src="https://github.com/user-attachments/assets/776f73d8-d300-4a41-ab3d-788eff58aad1" />

<img width="532" height="268" alt="image" src="https://github.com/user-attachments/assets/9a9ff0b2-b1d9-4a34-a6b3-b07b3ee6ad68" />

➤ This query confirmed the existence of AWS logs, S3 access logs, Windows host metadata, and hardware inventory information.

## **3.5 Explanation of SOC for Setup Design**

The setup fulfils key operational needs of SOC:

| **SOC Requirement** | **How Configuration Helps Achieve It** |
| :--- | :--- |
| **Centralised visibility** | Splunk gathers all logs from the cloud, endpoints, and infrastructure. |
| **Threat identification abilities** | S3 Access Logs and CloudTrail provide information about suspicious file activity, IAM abuse, and misconfigurations. |
| **Monitoring of assets and systems** | Hardware and Windows host data can be used to profile hosts and detect anomalies. |
| **Investigating an incident** | Splunk searches or queries, field extraction, and dashboards provide precise reconstruction of attacker behaviour. |

# **4. Guided Questions (Q1-Q8)**

This section covers the full investigation of the BOTSv3 AWS-focused 200-level question set. Each query is answered using Splunk Search Processing Language (SPL), which includes supporting evidence, analysis, and clear mapping to SOC operations. Queries were run on the botsv3 index with CloudTrail, S3 Access Logs, hardware, and Windows host telemetry. The answers include both the technical outcome and the security relevance in a SOC setting [3], [5], [6].

## **Q1- Identifying IAM Users Accessing AWS Services**

### **➤ Goal**

The goal of question 1 is to identify all **IAM** (Identity and Access Management) users that accessed any AWS service whether the action was successful or unsuccessful. 

### **➤ SPL Query**

<img width="421" height="119" alt="image" src="https://github.com/user-attachments/assets/b8d5cd55-c85a-495f-a1df-bfb794602802" />

### Explanation of Query:

<img width="951" height="337" alt="image" src="https://github.com/user-attachments/assets/45ec5fd0-ef0c-433a-8c3b-5aa7c3482a16" />

### **➤ Result**

**bstoll,btun,splunk_access,web_admin**

<img width="683" height="267" alt="image" src="https://github.com/user-attachments/assets/73ebfcb4-9467-4860-a100-b4a91cb2e0b0" />

### **➤ SOC Relevance** 

Identifying which IAM users are accessing AWS services is important for identity-related threat detection. This visibility allows the SOC to:

* Create behavioural baselines for each user and detect any unusual patterns that can indicate compromised credentials.
* Detect unusual authentication patterns that indicate insider threats or brute-force attacks.
* Supports compliance and audit requirements by keeping reliable records of identity-related activity.
* Verify if access attempts are legitimate or potentially malicious during triage by comparing IAM user activity with other security alerts. 

This is in line with SOC Tier-1 triage, in which analysts verify whether user activity is normal, and SOC Tier-2 analysis, in which identity logs are connected with other alerts to identify potential threats.

## **Q2- Identifying AWS API Activity Without MFA**

### **➤ Goal**

The goal of question 2 is to identify which CloudTrail field shows AWS API calls conducted without MFA enabled.

### **➤ SPL Query**

<img width="660" height="105" alt="image" src="https://github.com/user-attachments/assets/05bb316b-1559-46ea-a68c-25eb111b94d0" />

### **Explanation of Query:**

<img width="986" height="287" alt="image" src="https://github.com/user-attachments/assets/65d4ab52-236f-48e7-9d7b-36e928fb69c3" />

### **➤ Result**

**userIdentity.sessionContext.attributes.mfaAuthenticated**

<img width="683" height="268" alt="image" src="https://github.com/user-attachments/assets/7f0172ca-87da-4cae-b79c-f4e74d10f3a3" />

### **➤ SOC Relevance** 

Multi-factor authentication (**MFA**) is a crucial security security measure. When API actions are performed without MFA, alerts can help in identifying:

* Account takeovers - unauthorised access to user accounts.
* Escalation of privileges - attempt to obtain more privileges beyond the authorised scope.
* Violations of policy - actions that disobey established security policies.

This is important for SOC detection and prevention operations.

## **Q3 - Identifying the Processor Number of Web Servers**

### **➤ Goal**

The goal of question 3 is to identify the processor number used by the web servers. The **hardware** source type in Splunk was chosen because it records comprehensive telemetry for every host, including CPU, memory, storage, and network data.

### **➤ SPL Query**

<img width="354" height="56" alt="image" src="https://github.com/user-attachments/assets/68fb630a-181c-47f7-b1da-11d5033fcbda" />

### **Explanation of Query:**

<img width="796" height="167" alt="image" src="https://github.com/user-attachments/assets/445d7c6e-0e51-4a72-9fd2-d15fb3ab55b9" />

### **➤ Result**

**E5-2676**

<img width="934" height="365" alt="image" src="https://github.com/user-attachments/assets/0fb38ecd-d682-4e1a-b0f7-326624fb79a5" />

### **➤ SOC Relevance**

Hardware inventory data contributes to the SOC's capacity to profile and protect important infrastructure. Analysing processor models and server specifications allows analysts to:

* Assess whether systems fulfil security baselines and patch support needs.
* Determine which hardware may be vulnerable or outdated, raising the chance of exploitation.
* Help forensic investigations by linking host identity to performance abnormalities or system-based attacks.
* Improve response and recovery plans by making sure systems are properly documented and traceable.

This is in line with SOC Tier-2 responsibilities including asset management, impact assessment, and system profiling during incident investigations.

## **Q4 - Event ID of Public S3 Bucket Misconfiguration**

### **➤ Goal**

The goal of question 4 is to identify the specific Event ID of the API call that made an S3 bucket public.

### **➤ SPL Query**

<img width="689" height="102" alt="image" src="https://github.com/user-attachments/assets/6594f11a-6691-4e6b-b4df-e1f61bb2d885" />

### **Explanation of Query:**

<img width="1023" height="311" alt="image" src="https://github.com/user-attachments/assets/96694b69-c13d-4db1-975a-030da7209f6f" />

### **➤ Result**

**ab45689d-69cd-41e7-8705-5350402cf7ac**

<img width="933" height="364" alt="image" src="https://github.com/user-attachments/assets/0013a9ef-8ecb-43d0-9014-900a9e6f7d4b" />

### **➤ SOC Relevance**

S3 buckets that are publicly accessible pose a significant cloud security risk. Identifying the particular API call and event ID related with a misconfiguration allows the SOC to:

* Determine whether the exposure was unintentional or malicious by tracing its root cause.
* Set up automated alerts whenever important bucket permissions are changed.
* Remove public access in a timely manner by working with cloud developers.
* Maintain forensic accountability so that auditors may identify the user who made a high-risk configuration change.

This shows how SOC teams prevent cloud misconfiguration threats from getting worse and is in line with the **Analysis and Containment** phases of incident handling.

## **Q5 - Identifying Bud’s Username**

### **➤ Goal**

The goal of question 5 is to identify the username that made the S3 bucket publicly accessible.This task use the evidence get from the **question 4**.

### **➤ Result**

**bstoll** 

<img width="930" height="364" alt="image" src="https://github.com/user-attachments/assets/5b30b742-571e-4d07-8da1-ec9888c9f996" />

### **➤ SOC Relevance**

One of the main responsibilities of SOC is to identify the specific person who made a risky or unauthorised change to the cloud configuration. Identifying that the user account **"bstoll"** was the source of the modification allows the SOC to:

* Ensure accountability by assigning the incident to a specified IAM identity.
* Determine whether the user's actions indicate insider threat behaviour, compromised credentials, or an unintentional misconfiguration.
* Check for further anomalies by comparing the user's previous actions with other AWS logs.
* Effectively escalate by notifying cloud technical teams or management so quick corrective measures can be implemented.

This helps the **Analysis and Containment** phases of the incident response lifecycle by ensuring that risky actions are rapidly assigned to responsible users for corrective action.

## **Q6- Public S3 Bucket Name**

### **➤ Goal**

The goal of question 6 is to identify the S3 bucket that was made public..This task use the evidence get from the **question 4**.

### **➤ Result**

**frothlywebcode**

<img width="934" height="365" alt="image" src="https://github.com/user-attachments/assets/df4cf5c2-7f0e-4d27-80ec-eb78e2a45ec8" />

### **➤ SOC Relevance**

Identifying which S3 bucket, "**frothlywebcode**," was made accessible to the public is important for assessing the level of exposure. This information enables SOC analysts to:

* Identify the sensitivity of the data in the bucket and assess the possibility for data leakage.
* Check historical access logs for any unusual external access to determine the scope of the impact.
* Prioritise remediation actions by working with the cloud team to disable public permissions.
* When sensitive or regulated data exists in the exposed bucket, initiate compliance reporting.

This procedure complies with SOC containment and risk mitigation actions, ensuring that misconfigured cloud assets are immediately secured to prevent exploitation.

## **Q7- Identifying Uploaded Text File**

### **➤ Goal**

The goal of question 7 is to identifying the name of the text file that was uploaded to the **“frothlywebcode”** S3 bucket while it was accessible to the public.

### **➤ SPL Query**

<img width="689" height="71" alt="image" src="https://github.com/user-attachments/assets/6ae5c63e-3ca6-4f35-a80c-76555c933470" />

### **Explanation of Query:**

<img width="1005" height="430" alt="image" src="https://github.com/user-attachments/assets/589e2fa3-c58a-4ed8-bef6-c7cea99fd9a7" />

### **➤ Result**

**OPEN_BUCKET_PLEASE_FIX.txt**

<img width="934" height="363" alt="image" src="https://github.com/user-attachments/assets/4f63e569-8930-4de0-badd-91c45fa6b575" />

### **➤ SOC Relevance**

Identifying the uploaded file "**OPEN_BUCKET_PLEASE_FIX.txt**" shows how analysts monitor interactions with cloud resources that have been compromised or improperly configured. This helps SOC operations by allowing for:

* Verification of whether attackers used the publicly available bucket to upload malicious files.
* Identifying whether the file involves activities like testing, reconnaissance, or exploitation.
* Analysing of external interactions with vulnerable cloud storage as part of a wider threat intelligence analysis.
* Maintaining of forensic evidence to help post-incident investigations and avoid future cloud configuration risks.

This is in accordance with SOC detection and investigation processes, allowing analysts understand the scope of activity during a cloud misconfiguration problem.

## **Q8- Identifying FQDN of the Endpoint Running a Different Windows Operating System Edition**

### **➤ Goal**

The goal of question 8 is to identify FQDN of the endpoint that is running a different Windows operating system edition than the other hosts. To extract the Fully Qualified Domain Name (**FQDN**) of the outlier endpoint, firstly determine the correct data source, then find the relevant field holding OS information, and then compare hostnames.

### ** Keywords:**

In order to find an appropriate data source, basic keyword searches were started with:

* **winhostmon**
* **windows**
* **OS**

These keywords showed that the **winhostmon sourcetype** provides metadata about the endpoint's operating system. The **OS** field in this sourcetype clearly provides the operating system edition information necessary for this investigation.

### **➤ SPL Query- Step 1: Identify the Host with a Different OS**

<img width="327" height="51" alt="image" src="https://github.com/user-attachments/assets/774f57df-e18d-41b6-9f7a-0d8fdaf2b244" />

### **Explanation of Query:**

<img width="858" height="427" alt="image" src="https://github.com/user-attachments/assets/c6c187dc-413c-4fb2-9695-62b3e37f10e0" />

### **➤ SPL Query- Step 2: Extract the Fully Qualified Domain Name (FQDN)**

<img width="496" height="109" alt="image" src="https://github.com/user-attachments/assets/c04acdce-d140-415d-b8f4-4bf03965ef54" />

### **Explanation of Query:**

<img width="715" height="488" alt="image" src="https://github.com/user-attachments/assets/afdeb25e-cc9b-4341-bd1a-9a989ca927e9" />

### **➤ Result**

**BSTOLL-L.froth.ly**

<img width="935" height="366" alt="image" src="https://github.com/user-attachments/assets/1bf7637a-e12c-43aa-a149-f4ae31d8116d" />

### **➤ SOC Relevance**

Identifying endpoints that different from normal operating system configurations is an important responsibility of a Security Operations Centre. Starting with keywords like **winhostmon**, **windows**, and **OS** allowed us to identify the appropriate data source for assessing host operating system information. The **OS** field in the winhostmon sourcetype showed the operating system editions across endpoints.

The investigation found that **BSTOLL-L.froth.ly** was the only endpoint running **Microsoft Windows 10 Enterprise**, whereas all other hosts were running **Windows 10 Pro**, resulting in a unique system.

The result is important from the perspective of SOC because:

* Systems with different operating system editions frequently have higher privileges, additional functionality, or administrative roles, which makes them more valuable targets for attackers.
* Endpoint configuration differences may indicate misconfiguration, policy exceptions or specialised user roles, all of which require further monitoring.
* Detecting the Fully Qualified Domain Name (**FQDN**) enables accurate host-level correlation across multiple log sources, resulting in faster investigation and response.
* Patch management validation, access control reviews, and host-based intrusion detection should give priority to these endpoints.

This investigation is in accordance with SOC Tier-2 analysis responsibilities, where analysts identify high-risk assets by comparing operating system metadata and endpoint telemetry. It also helps with the Detection and Analysis phase of the incident response lifecycle by increasing visibility into endpoint posture and reducing blind spots in host monitoring.

# 5. Conclusion

This investigation showed how a Security Operations Centre (**SOC**) may use Splunk Enterprise to identify, evaluate, and address security threats throughout a cloud-enabled enterprise environment [2]. A systematic SOC-driven strategy was used to investigate AWS activity, cloud storage misconfigurations, host posture, and endpoint operating system variations by conducting evidence-based analysis using the BOTSv3 dataset [3].
The results revealed many serious security issues, including the detection of multiple IAM users interacting with AWS services, the presence of API activity that occurred without multi-factor authentication, and the exposing of a publicly accessible S3 bucket. Further investigation showed that the misconfigured bucket was actively used, as indicated by a successful text file upload during the exposure time. Furthermore, endpoint analysis showed a distinct Windows host running a different operating system edition, indicating a potentially high-value system that requires additional monitoring [5], [6].

This BOTSv3 Splunk-based investigation highlighted the necessity of centralised log visibility, correct asset profiling, and cross-source correlation for analysing complicated cloud and endpoint-based issues. The ability to switch between CloudTrail logs, S3 access logs, hardware telemetry, and Windows host monitoring mimics real-world SOC operations and Tier-2 investigative techniques. Without extensive logging from these sources, critical misconfigurations and risky behaviours would have gone undetected.

Futhermore, the key lessons from this investigation include the importance of requiring multi-factor authentication for all privileged and API-based access, implementing automated alerts for cloud misconfigurations like public S3 buckets, and maintaining consistent endpoint baselines for quickly detecting anomalous hosts. The further improvements could include risk-based alerting, automated remediation playbooks based on SOAR, and continuous compliance monitoring for reducing an average time to detect and respond to similar issues [2], [7].

In summary, the investigation shows how organised investigative techniques, critical analysis, and proactive security measures are also important to successful SOC operations as tooling. The use of Splunk in this investigation demonstrates how data-driven detection and response can significantly enhance an organization's security posture while complying with industry-recognised SOC standards [1], [2].

# References

[1] NIST, “Computer Security Incident Handling Guide,” NIST SP 800-61 Rev. 2, 2012.

[2]Splunk Inc., “Splunk Enterprise Documentation,” 2024. [Online]. Available: https://docs.splunk.com

[3]Splunk Inc., “Boss of the SOC (BOTS) Dataset Version 3,” GitHub Repository, 2024. [Online]. Available: https://github.com/splunk/botsv3

[4]NIST, “The NIST Cybersecurity Framework,” 2024. [Online]. Available: https://www.nist.gov/cyberframework

[5]Amazon Web Services, “AWS CloudTrail User Guide,” 2024. [Online]. Available: https://docs.aws.amazon.com/awscloudtrail/

[6]Amazon Web Services, “Managing Access with ACLs in Amazon S3,” 2024. [Online]. Available: https://docs.aws.amazon.com/AmazonS3/

[7]Splunk Inc., “Security Orchestration, Automation, and Response (SOAR),” 2024. [Online]. Available: https://www.splunk.com/en_us/software/soar.html





