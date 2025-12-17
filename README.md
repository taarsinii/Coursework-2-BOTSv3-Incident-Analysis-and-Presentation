# COMP3010 Security Operations & Incident Management

1.Introduction 
Security Operations Centres (SOCs) are in charge of protecting enterprise environments through continuous log monitoring, threat detection, and incident response [1]. Splunk Enterprise and the BOTSv3 (Boss of the SOC) dataset, which includes real-world security telemetry such as AWS CloudTrail, S3 access logs, hardware inventory, and Windows host monitoring, are used in this assessment to simulate real-world SOC operations [2], [3].

This report focuses on investigative tasks that relate to SOC Tier-1 and Tier-2 workflows such as identifying user behaviour, detecting misconfigurations, checking host posture, and analysing indicators of compromise. Splunk Enterprise functions as the primary SIEM platform for searching, correlating, and interpreting security events in compliance with professional SOC processes [2].

Scope of Investigation

This investigation focusses on the following analytical tasks:

Validating installation and ingestion of the BOTSv3 dataset.
Performing specific SPL queries on AWS and endpoint data sources.
Detecting cloud misconfigurations, including insecure S3 bucket permissions.
Verifying user identities engaged in unauthorised activity.
Analysing differences in host operating systems and endpoint setups

Assumptions

The investigation was based on the following assumptions:

The BOTSv3 represents an enterprise, cloud-enabled infrastructure.
There are no additional network logs accessible beyond the dataset provided.
All unusual events are considered as a potential security issues that must be investigated.

The main objective of this report is to use Splunk-based analytical approaches to solve BOTSv3 200-level AWS investigation questions while also showing professional SOC reasoning, clear evidence presentation, and professional cybersecurity analysis [1], [4].

