# Wazuh-SOC-Homelab
SOC Home Lab – Deploying Wazuh for Log Monitoring, Threat Detection, and Automated Response

## Project Overview
This project demonstrates the deployment of a Security Operations Center (SOC) home lab using the Wazuh security monitoring platform.

The objective of this lab was to understand how security teams collect logs, analyze them, detect suspicious activity, and respond to potential threats using a SIEM-based environment.

The lab was initially built to simulate a basic SOC monitoring setup and was later expanded with additional capabilities including File Integrity Monitoring (FIM), custom detection rules, vulnerability detection, and automated response mechanisms.

## Lab Environment
Attacker Machine: Kali Linux  
Monitoring Server: Wazuh Server  
Target System: Windows 10  

## Wazuh Components

### Wazuh Manager
The Wazuh Manager collects logs from monitored systems, analyzes them using built-in and custom detection rules, and generates alerts when suspicious activity is detected.

### Wazuh Indexer
The Wazuh Indexer stores and organizes security event data, allowing efficient searching and analysis of logs during investigations.

### Wazuh Dashboard
The Wazuh Dashboard provides a graphical interface where alerts, logs, and monitored systems can be visualized and analyzed in real time.

## Implemented Security Features

### File Integrity Monitoring (FIM)
Configured Wazuh Syscheck to monitor sensitive directories and generate alerts whenever files were created or modified.

### Custom Detection Rules
Created custom Wazuh rules to detect specific security events such as:
- New user account creation
- SSH brute-force login attempts
- USB device insertion

### Vulnerability Detection
Enabled Wazuh vulnerability detection to identify known vulnerabilities present on monitored systems.

### Active Response Automation
Configured Wazuh Active Response to automatically respond to detected threats.  
During testing, repeated SSH brute-force attempts triggered a response that blocked the attacking IP using firewall rules.

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
- Threat detection workflows  
- Automated incident response  
- SOC workflow  

## Learning Outcome
This project provided hands-on experience with building and operating a SOC monitoring environment. It helped demonstrate how SOC analysts detect threats, investigate alerts, and implement automated responses within a real-world security monitoring workflow.

## Project Documentation
Detailed lab reports, screenshots, and configurations used in this project can be found in the **Project Documentation** folder within this repository.
