````markdown
# 🛡️ Wazuh SOC Home Lab

A hands-on, multi-phase Security Operations Center (SOC) home lab built on Wazuh, simulating real-world threat detection, network monitoring, and automated response in a virtualized environment.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Lab Architecture](#lab-architecture)
- [Phase 1 — Core SIEM Deployment](#phase-1--core-siem-deployment)
- [Phase 2 — Detection & Automated Response](#phase-2--detection--automated-response)
- [Phase 3 — Network Monitoring & Threat Intelligence](#phase-3--network-monitoring--threat-intelligence)
- [Phase 4 — pfSense Virtual Firewall](#phase-4--pfsense-virtual-firewall)
- [Technologies Used](#technologies-used)
- [Skills Demonstrated](#skills-demonstrated)

---

## Overview

This project documents the step-by-step construction of a functional SOC home lab using Wazuh as the central SIEM platform. The lab was built progressively across four phases, each adding a new layer of security capability — from basic log collection to network-level intrusion detection, threat intelligence enrichment, and perimeter firewall integration.

The goal was to simulate an enterprise-grade SOC environment at home, gaining hands-on experience with the tools and workflows used by real security analysts.

---

## Lab Architecture

The lab runs entirely inside **VirtualBox** on a Windows 11 host. Each VM uses two network adapters:
- **NAT** — for internet access
- **Host-Only** — for isolated inter-VM communication

| VM | Role | OS | RAM | CPU | Storage |
|----|------|----|-----|-----|---------|
| Wazuh Server | SIEM Manager, Indexer, Dashboard | Linux Mint | 8 GB | 4 | 50 GB |
| Attacker | Attack simulation, Suricata NIDS | Kali Linux | 3 GB | 3 | 50 GB |
| Endpoint | Monitored workstation | Windows 10 | 3 GB | 3 | 50 GB |
| Firewall | Network gateway (Phase 4) | pfSense 2.7.2 | — | — | — |

````
Host Computer (Windows 11)
└── VirtualBox
    ├── Virtual Network 1: NAT (Internet Access)
    │   ├── Wazuh Server (Linux Mint)
    │   ├── Attacker VM (Kali Linux)
    │   └── Windows 10 Endpoint
    └── Virtual Network 2: Host-Only (Isolated Lab)
        ├── Attack Traffic → Windows 10
        └── Log Traffic → Wazuh Server
````

> All machines were assigned static IPs within the Host-Only network subnet. Specific addresses are omitted intentionally.

---

## Phase 1 — Core SIEM Deployment

**Goal:** Deploy the Wazuh stack, connect agents, and simulate the first attack.

### Components Deployed

| Component | Function |
|-----------|----------|
| **Wazuh Manager** | Central log collection and rule-based alert engine |
| **Wazuh Indexer** | Stores and indexes events for search and threat hunting |
| **Wazuh Dashboard** | Web UI for alert visualization and analysis (HTTPS, port 443) |

### Wazuh Agent Installation (Kali Linux)

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent-amd64.deb
sudo WAZUH_MANAGER='<wazuh-server-ip>' WAZUH_AGENT_NAME='KaliAttacker' dpkg -i wazuh-agent.deb
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

> Replace `<wazuh-server-ip>` with the static IP assigned to your Wazuh Server on the Host-Only network.

### Attack Simulation — SSH Brute Force

Used **Hydra** from Kali Linux to simulate a brute-force attack against the Windows 10 endpoint:

```bash
hydra -l username -P passwords.txt ssh://<target-ip>
```

> Replace `<target-ip>` with the static IP of your monitored endpoint on the Host-Only network.

**Detection flow:**
1. Kali sends repeated SSH login attempts to the target endpoint
2. The endpoint logs authentication events
3. Wazuh agent collects and forwards logs to the Manager
4. Wazuh rules detect the suspicious pattern and generate alerts
5. Alerts appear on the dashboard with rule ID, severity, and source

### Results

| Metric | Value |
|--------|-------|
| Total alerts | 516 |
| Authentication failures | 11 |
| Authentication successes | 3 |
| Top alert group | SCA (426 events) |

---

## Phase 2 — Detection & Automated Response

**Goal:** Implement proactive monitoring, custom detection rules, and automated IP blocking.

### 1. File Integrity Monitoring (FIM)

Configured Wazuh's `syscheck` module to monitor sensitive directories on both Linux and Windows endpoints.

**Linux directories monitored:** `/etc`, `/var/log`

**Windows paths monitored:** `C:\Windows\System32`, registry keys

FIM tracked file creation, modification, and deletion in real time, generating alerts with MD5/SHA1/SHA256 checksums, permissions, and timestamps.

### 2. Custom Detection Rules

Created custom rules in `/var/ossec/etc/rules/local_rules.xml`:

**Rule: New Linux User Account Created**
```xml
<rule id="100004" level="10">
  <if_sid>5501</if_sid>
  <description>New Linux user account created</description>
  <mitre>
    <id>T1136</id>
  </mitre>
</rule>
```

**Rule: SSH Brute Force Detection**
```xml
<rule id="100002" level="12" frequency="6" timeframe="120">
  <if_matched_sid>5710</if_matched_sid>
  <same_source_ip/>
  <description>SSH brute force attack detected: >5 failures from same IP in 2 min</description>
  <mitre><id>T1110</id></mitre>
  <group>authentication_failure,brute_force,ssh</group>
</rule>
```

### 3. Active Response Automation

Configured Wazuh to automatically block attacking IPs using `firewall-drop` upon triggering the SSH brute force rule:

```xml
<active-response>
  <command>firewall-drop</command>
  <location>any</location>
  <rules_id>100002</rules_id>
  <timeout>300</timeout>
</active-response>
```

**Outcome:** After exceeding the threshold, Wazuh automatically inserted an `iptables DROP` rule for the attacker's source address — confirmed via `iptables -L -n`.

---

## Phase 3 — Network Monitoring & Threat Intelligence

**Goal:** Extend visibility from host-based to network-level detection, and enrich alerts with external threat intelligence.

### 1. Suricata — Network Intrusion Detection (NIDS)

Installed Suricata on the Kali Linux machine to monitor network traffic on the internal lab interface.

```bash
sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt-get update && sudo apt-get install suricata
```

Configured Suricata to listen on the Host-Only network interface and load rules from `/etc/suricata/rules`.

**Wazuh integration** — added Suricata's `eve.json` log as a monitored localfile:

```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
</localfile>
```

**Test:** Ran an Nmap scan from another VM → Suricata generated ICMP/scan alerts → alerts forwarded and visible in the Wazuh dashboard (Rule ID `86601`).

### 2. VirusTotal Integration — Threat Intelligence Enrichment

Integrated VirusTotal with Wazuh's File Integrity Monitoring (FIM/syscheck) module to automatically scan file hashes against VirusTotal's multi-engine database.

```xml
<ossec_config>
  <integration>
    <name>virustotal</name>
    <api_key>YOUR_API_KEY</api_key>
    <group>syscheck</group>
    <alert_format>json</alert_format>
  </integration>
</ossec_config>
```

Created a custom trigger rule that fires when new `.exe`, `.dll`, or `.ps1` files are created, automatically submitting them to VirusTotal for reputation analysis.

**Outcome:** Alerts enriched with detection ratio and reputation data, providing deeper context for triage beyond raw file events.

---

## Phase 4 — pfSense Virtual Firewall

**Goal:** Add a dedicated network perimeter firewall, route all VM traffic through it, and integrate firewall logs with Wazuh.

### 1. Deployment

pfSense 2.7.2 (FreeBSD) was installed as a VirtualBox VM.

> **Note:** A boot loop issue was encountered during installation — resolved by detaching the ISO immediately after the installer finished writing to disk, allowing the VM to boot from the installed disk.

### 2. Network Interfaces

| Interface | Adapter | Purpose |
|-----------|---------|---------|
| WAN (em0) | NAT | Internet access via host machine |
| LAN (em1) | Host-Only | Internal gateway for all lab VMs |

pfSense was assigned a static IP on the LAN interface, which was then set as the default gateway on all other VMs. All outbound traffic routes through pfSense before reaching the internet.

### 3. Firewall Rule — Block ICMP to Internet

Created a LAN rule in pfSense to block all outbound ICMP (ping) traffic:

| Parameter | Value |
|-----------|-------|
| Action | Block |
| Interface | LAN |
| Protocol | ICMP |
| Source | LAN subnet |
| Destination | Any (internet) |

**Verified:** Outbound pings to external addresses were dropped by pfSense; pings to the pfSense LAN interface itself remained operational, confirming only internet-bound ICMP was affected.

### 4. Wazuh Integration

Configured pfSense to forward syslog events to the Wazuh Manager on port 514 (UDP). Added a custom Wazuh rule to parse pfSense filterlog events:

```xml
<!-- Rule for pfSense -->
<rule id="100005" level="5">
  <match>filterlog</match>
  <same_source_ip/>
  <description>pfSense firewall event</description>
</rule>
```

**Outcome:** Firewall block events and interface activity appeared as alerts in the Wazuh dashboard — adding network perimeter visibility on top of the existing host-based monitoring stack.

---

## Technologies Used

| Tool | Purpose |
|------|---------|
| **Wazuh** | SIEM — log collection, alerting, FIM, active response |
| **VirtualBox** | Hypervisor for all virtual machines |
| **Linux Mint** | Wazuh server OS |
| **Kali Linux** | Attack simulation and Suricata NIDS |
| **Windows 10** | Monitored endpoint |
| **Suricata** | Network Intrusion Detection System (NIDS) |
| **VirusTotal API** | Threat intelligence enrichment |
| **pfSense** | Virtual firewall and network gateway |
| **Hydra** | SSH brute-force simulation tool |
| **Nmap** | Network scanning for NIDS testing |

---

## Skills Demonstrated

- Virtualization and virtual networking (VirtualBox, NAT, Host-Only adapters)
- SIEM deployment and agent management (Wazuh)
- Endpoint monitoring and File Integrity Monitoring (FIM)
- Custom detection rule authoring (XML, MITRE ATT&CK mapping)
- Active response and automated firewall-based IP blocking
- Network intrusion detection (Suricata, deep packet inspection)
- Threat intelligence API integration (VirusTotal)
- Firewall configuration and policy enforcement (pfSense)
- Centralized log aggregation and multi-source alert correlation
- Security event triage and SOC workflow documentation

---

*Built by Muhammad Abdullah | CYBERSTER*
````
