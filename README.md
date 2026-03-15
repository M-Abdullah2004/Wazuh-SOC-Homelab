# Wazuh-SOC-Homelab
SOC Home Lab – Deploying Wazuh for Log Monitoring, Threat Detection, and Automated Response

## Project Overview
This project demonstrates the deployment of a Security Operations Center (SOC) home lab using the Wazuh security monitoring platform.

The objective of this lab was to understand how security teams collect logs, analyze them, detect suspicious activity, and respond to potential threats using a SIEM-based environment.

The environment was later expanded by implementing File Integrity Monitoring (FIM), custom detection rules, and automated response mechanisms to simulate real SOC monitoring and response workflows.

## Lab Environment
Monitoring Server: Linux Mint (Wazuh Server)  
Endpoint Agent 1: Windows 10  
Endpoint Agent 2: Kali Linux  

Both endpoints were configured with Wazuh agents to send logs to the central Wazuh server for monitoring and analysis.

## Wazuh Components

### Wazuh Manager
The Wazuh Manager collects logs from monitored systems, analyzes them using built-in and custom detection rules, and generates alerts when suspicious activity is detected.

### Wazuh Indexer
The Wazuh Indexer stores and organizes security event data, allowing efficient searching and analysis of logs during investigations.

### Wazuh Dashboard
The Wazuh Dashboard provides a graphical interface where alerts, logs, and monitored systems can be visualized and analyzed in real time.

## Implemented Security Features

### File Integrity Monitoring (FIM)
Configured Wazuh Syscheck to monitor sensitive directories on the monitored systems. Alerts were generated whenever files were created or modified, demonstrating how SOC teams detect unauthorized file changes.

### Custom Detection Rules
Created custom Wazuh rules to detect specific security events such as:
- New user account creation
- SSH brute-force login attempts

These rules helped simulate how detection engineering works in a SOC environment.

### Active Response Automation
Configured Wazuh Active Response to automatically react to detected threats. During testing, repeated SSH login attempts triggered the configured rule and Wazuh executed a firewall action using iptables to block the source IP address.

## Dashboard Access
Protocol: HTTPS  
Port: 443  

## Skills Demonstrated
- Security monitoring  
- Log analysis  
- SIEM fundamentals  
- Wazuh deployment  
- Detection engineering  
- File Integrity Monitoring (FIM)  
- Automated incident response  
- SOC workflow  

## Learning Outcome
This project provided hands-on experience with building and operating a SOC monitoring environment using Wazuh. It helped demonstrate how SOC analysts monitor endpoints, detect suspicious activity, analyze alerts, and implement automated response mechanisms within a real-world security monitoring workflow.

## Project Documentation
Detailed lab reports, screenshots, and configurations used in this project can be found in the **Project Documentation** folder within this repository.
