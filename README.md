Multi-Source Security Monitoring Pipeline (SIEM Lab)
📌 Project Overview
This project demonstrates the architecture and deployment of a centralized security logging pipeline. I integrated network-level telemetry from an OPNsense Firewall and endpoint-level telemetry from Windows Sysmon/Audit Logs into a centralized ELK Stack (Elasticsearch, Logstash, Kibana).

This setup provides a unified "single pane of glass" for monitoring network traffic and host-based process activity.

🛠️ Implementation Steps
1. Network Layer: OPNsense Firewall
Source: OPNsense System and Filter logs.

Transport: Configured Remote Syslog targets to forward data via UDP.

Port Optimization: Successfully remapped the standard port 514 to Port 5514 to ensure the Logstash service could bind to the interface without requiring root privileges (following the Principle of Least Privilege).

2. Endpoint Layer: Windows Auditing & Sysmon
Audit Policy: Enabled advanced Windows logging for Logon/Logoff events and Process Tracking.

Sysmon: Deployed Sysmon (System Monitor) with a customized configuration to capture process creation (Event ID 1) and network connections (Event ID 3).

Data Shipper: Installed and configured Winlogbeat to securely ship these events from the Windows host to the Elasticsearch VM.

3. Data Processing: Logstash & Elasticsearch
Pipeline Logic: Developed a custom Logstash configuration to listen for UDP traffic on port 5514.

Indexing: Created a dynamic indexing strategy (opnsense-logs-%{+YYYY.MM.dd}) to allow for efficient data retention and searching.

Validation: Used the Elasticsearch REST API (_cat/indices) to verify successful data ingestion and index health.

🛡️ Critical Challenges & Resolutions
⚠️ The "Privileged Port" Conflict (EACCES)
The Problem: Logstash failed to start when assigned to port 514.

The Cause: Linux kernel restrictions prevent non-root users from binding to ports below 1024.

The Resolution: I pivoted the architecture to use port 5514 on both the sender (OPNsense) and the receiver (Logstash). This resolved the permission error while keeping the service secure.

⚠️ Elasticsearch Connectivity (ECONNREFUSED)
The Problem: Encountered a 502 Bad Gateway error in Kibana.

The Cause: Elasticsearch service was either inactive or crashed due to memory constraints during boot.

The Resolution: Managed service dependencies using systemctl, optimized the VM RAM allocation, and verified the "heartbeat" of the database using curl before restarting the ingestion pipeline.

🔍 Visualizing the Data
Once the pipeline was stabilized, I utilized Kibana to explore the data:

Network Analysis: Monitored "Pass" vs "Block" firewall rules to identify potential scanning activity.

Endpoint Analysis: Filtered Sysmon logs to track process parent-child relationships, a critical step in detecting malicious execution.

📂 Repository Structure
/logstash-configs/: Contains the .conf file for OPNsense ingestion.

/winlogbeat-configs/: Contains the winlogbeat.yml configuration for Windows telemetry.

/screenshots/: Contains proof of index creation and live log views in Kibana.
