# OPNsense to ELK: Centralized Logging Pipeline

## 📌 Project Overview
Successfully architected and deployed a security logging pipeline to stream live firewall events from an **OPNsense** gateway to an **Elasticsearch** and **Kibana** (ELK) stack. This setup provides real-time visibility into network traffic and security events.

## 🛠️ Tech Stack
* **OPNsense:** Open-source firewall/router.
* **Logstash:** Server-side data processing pipeline.
* **Elasticsearch:** Distributed search and analytics engine.
* **Kibana:** Data visualization and exploration.

## 🚀 Key Challenges & Troubleshooting
### 1. Linux Privileged Port Restriction (The 514 Error)
* **Problem:** Logstash failed to bind to UDP port 514 with a `Permission denied` error.
* **Root Cause:** In Linux, only the root user can bind to ports below 1024.
* **Solution:** Successfully reconfigured the pipeline to listen on port **5514** and updated OPNsense remote targets. This maintained security best practices by allowing Logstash to run as a non-root service.

### 2. Service Stability
* **Problem:** Encountered `ECONNREFUSED` on port 9200.
* **Resolution:** Debugged service dependencies and optimized the boot order to ensure Elasticsearch was fully initialized before Logstash attempted to stream data.
