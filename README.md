# Offensive-Security-Labs

# Network-Defense

# 🛡️ Cybersecurity Red Team Labs

## Overview

This lab is designed to simulate **realistic cyber attacks** on both **endpoints** and **network environments** in order to develop and demonstrate **Blue Team detection, analysis, and response skills**. It provides a hands-on platform to practice identifying, investigating, and mitigating threats using industry-standard tools and frameworks.

---

## 🎯 Objectives

- Simulate endpoint-based attacks (e.g., privilege escalation, process injection, credential access)
- Simulate network-based attacks (e.g., port scanning, man-in-the-middle, data exfiltration)
- Use security tools to detect and respond to threats in real-time
- Build and tune detection rules, dashboards, and alerts
- Gain hands-on experience with logs, events, and forensic data

---

## 🧰 Tools & Technologies Used

| Category        | Tools/Technologies                  |
|----------------|--------------------------------------|
| Endpoint Security | Sysmon, Wazuh Agent, Windows Event Logs |
| Network Monitoring | Wireshark, Suricata, tcpdump       |
| SIEM/Log Analysis | Wazuh |
| Offensive Simulation | Kali Linux, Metasploit,|
| Detection Frameworks | MITRE ATT&CK,  |
| Virtualization | VMware Fusion    |

---
## 📌 Example Attacks Simulated in these labs

| Category | Technique | MITRE ID |
|---------|-----------|----------|
| Privilege Escalation | `runas`, `whoami`, `mimikatz.exe` | T1055, T1003 |
| Reconnaissance | `netstat`, `systeminfo`, `arp -a` | T1087, T1016 |
| Network Discovery | `nmap`, `arp-scan` | T1046 |
| Lateral Movement | RDP login attempts, shared folder access | T1021 |
| Malware Execution | `nc.exe`, obfuscated PowerShell scripts | T1059 |
| File Integrity | Create/edit/delete sensitive files | N/A |

---

## ✅ Blue Team Focus Areas

- **Log Collection & Analysis**: Forward logs to Wazuh and analyze alerts
- **Detection Engineering**: Write custom rules for suspicious processes and behavior
- **Alert Tuning**: Reduce false positives, increase detection accuracy
- **Forensics & Investigation**: Examine Sysmon, Event Logs, PCAPs
- **Dashboards**: Create visualizations in Kibana for SOC-style monitoring

---
