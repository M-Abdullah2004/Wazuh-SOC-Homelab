# 🛡️ Wazuh SOC Home Lab

A hands-on, multi-phase Security Operations Center (SOC) home lab built using Wazuh SIEM to simulate real-world threat detection, network monitoring, intrusion detection, and automated response in a virtualized environment.

---

# 📋 Table of Contents

- [Overview]
- [Objectives](#objectives)
- [Lab Architecture](#lab-architecture)
- [Technologies Used](#technologies-used)
- [Phase 1 — Core SIEM Deployment](#phase-1--core-siem-deployment)
- [Phase 2 — Detection & Automated Response](#phase-2--detection--automated-response)
- [Phase 3 — Network Monitoring & Threat Intelligence](#phase-3--network-monitoring--threat-intelligence)
- [Phase 4 — pfSense Virtual Firewall](#phase-4--pfsense-virtual-firewall)
- [Skills Demonstrated](#skills-demonstrated)
- [Conclusion](#conclusion)

---

# 📖 Overview

This project documents the step-by-step creation of a functional SOC (Security Operations Center) home lab using Wazuh as the central SIEM platform. The environment was developed across multiple phases, gradually adding new layers of security monitoring and defensive capabilities.

The lab simulates an enterprise-style SOC environment, providing practical experience with:

- Centralized log monitoring
- Endpoint visibility
- Threat detection
- Network intrusion monitoring
- Threat intelligence integration
- Automated incident response
- Firewall monitoring and segmentation

The goal of the project was to gain hands-on experience with real-world SOC operations, tools, and workflows used by security analysts in production environments.

---

# 🎯 Objectives

- Build a complete SOC home lab environment
- Deploy and configure Wazuh SIEM
- Simulate attacker and endpoint systems
- Monitor logs and security events
- Implement automated detection and response
- Integrate network intrusion detection
- Configure a virtual firewall for perimeter security
- Gain practical SOC analyst experience

---

# 🖥️ Lab Architecture

The entire environment runs inside **VirtualBox** on a **Windows 11** host machine.

Each virtual machine uses:

- **NAT Adapter** → Internet access
- **Host-Only Adapter** → Isolated communication between VMs

## Virtual Machine Configuration

| VM | Role | Operating System | RAM | CPU | Storage |
|----|------|------------------|-----|-----|---------|
| Wazuh Server | SIEM Manager, Indexer, Dashboard | Linux Mint | 8 GB | 4 Cores | 50 GB |
| Kali Linux | Attack Simulation & Suricata NIDS | Kali Linux | 3 GB | 3 Cores | 50 GB |
| Windows Endpoint | Monitored Workstation | Windows 10 | 3 GB | 3 Cores | 50 GB |
| pfSense Firewall | Virtual Network Gateway | pfSense 2.7.2 | Default | Default | Default |

---

# 🛠️ Technologies Used

- Wazuh SIEM
- VirtualBox
- Linux Mint
- Kali Linux
- Windows 10
- pfSense Firewall
- Suricata IDS/IPS
- Sysmon
- Windows Event Logging
- Threat Intelligence Feeds
- Network Traffic Monitoring
- Firewall Rule Configuration

---

# 🔹 Phase 1 — Core SIEM Deployment

The first phase focused on building the foundational SOC infrastructure using Wazuh.

## Tasks Performed

- Installed and configured Wazuh Server
- Configured Wazuh Dashboard and Indexer
- Installed Wazuh agents
- Connected Windows endpoint to Wazuh
- Verified log ingestion and monitoring
- Configured internal VM networking

## Outcome

Successfully established centralized log collection and monitoring through the Wazuh dashboard.

---

# 🔹 Phase 2 — Detection & Automated Response

This phase introduced endpoint visibility and automated security response mechanisms.

## Tasks Performed

- Installed Sysmon on Windows endpoint
- Forwarded Sysmon logs into Wazuh
- Created custom detection rules
- Configured active response features
- Simulated attacks from Kali Linux
- Monitored alerts and security events

## Outcome

The environment successfully detected suspicious activity and generated automated security alerts and responses.

---

# 🔹 Phase 3 — Network Monitoring & Threat Intelligence

This phase focused on network-level visibility and threat intelligence integration.

## Tasks Performed

- Installed and configured Suricata NIDS
- Monitored traffic between virtual machines
- Integrated Suricata alerts with Wazuh
- Added threat intelligence feeds
- Performed IOC-based detection
- Investigated suspicious network activity

## Outcome

The SOC lab gained network intrusion detection capabilities and improved threat visibility across the environment.

---

# 🔹 Phase 4 — pfSense Virtual Firewall

The final phase added perimeter security and network segmentation using pfSense.

## Tasks Performed

- Deployed pfSense firewall
- Configured WAN and LAN interfaces
- Created firewall rules
- Implemented traffic filtering
- Integrated pfSense logs into Wazuh
- Tested network segmentation and access control

## Outcome

The firewall enhanced perimeter defense, traffic monitoring, and network security within the SOC environment.

---

# 💡 Skills Demonstrated

- SIEM Deployment & Management
- Security Monitoring
- Log Analysis
- Endpoint Detection & Monitoring
- Threat Detection & Response
- IDS/IPS Configuration
- Firewall Administration
- Threat Intelligence Integration
- Network Traffic Analysis
- Virtualized Lab Deployment
- SOC Operations Workflow
- Incident Investigation

---

# ✅ Conclusion

The Wazuh SOC Home Lab successfully simulated a layered enterprise-style SOC environment using industry-relevant security tools and technologies.

Through multiple deployment phases, the project demonstrated practical implementation of:

- Centralized monitoring
- Endpoint visibility
- Network intrusion detection
- Threat intelligence enrichment
- Firewall security
- Automated incident response

This lab significantly strengthened hands-on cybersecurity skills relevant to SOC Analyst and Blue Team roles.

---
