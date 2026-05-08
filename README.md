# Lab-01-Nmap-Reconnaisance

## Lab 01 — Network Reconnaissance with Nmap
## Objective
Simulate how an attacker performs network reconnaissance against a Windows target, and document the findings the way a SOC analyst would — identifying open ports, exposed services, and potential attack vectors.

## Tools Used

• Nmap 7.95 — Network scanner and port discovery
• Wireshark — Packet capture and traffic analysis
• Kali Linux Terminal — Attacker platform

## Attack Simulation — Attacker Side (Kali Linux)

## Step 1 — Verify Connectivity
Before scanning, confirm the attacker machine can reach the target:
ping 192.168.20.10
Result: Successful ICMP replies confirmed target is reachable on the isolated network.

## Step 2 — Service Version Scan
Run Nmap with service version detection to identify open ports and running services:
nmap -sV 192.168.20.10

## Step 3 — Save Results to File
Export scan output for documentation and GitHub upload:
nmap -sV 192.168.20.10 -oN nmap-scan-results.txt

## Nmap Findings
| Port | State | Service | Version / Info |
| ---- | ----- | ------- | ------------ |
| 135/tcp | OPEN | msrpc | Microsoft Windows RPC |
| 139/tcp | OPEN | netbios-ssn | Microsoft Windows NetBIOS-SSN |
| 445/tcp | OPEN | microsoft-ds | SMB — Windows File Sharing |
| 997 ports | CLOSED | — | TCP RST response received |

## OS Detection Result:

Windows — CPE: cpe:/o:microsoft:windows

⚠️ Note: First scan showed all 1000 ports as FILTERED because Windows Firewall was active. After disabling the firewall, the 3 ports above became visible. This demonstrates the effectiveness of host-based firewalls.

## Detection — Defender Side (Wireshark Analysis)
Wireshark was running on the Kali machine during the Nmap scan, capturing all traffic on the eth0 interface in real time.
| Metric | Value |
| ------ | ----- |
| Total Packets Captured | 2,248 packets |
| Packets Dropped | 0 (0.0%) |
| Capture Interface | eth0 |
| Capture File | wireshark_eth05WIAM3.pcapng |
| Source IP (Attacker) | 192.168.20.11 |
| Destination IP (Target) | 192.168.20.10 |

## Key Protocol Observations
| Protocol | Observation | Significance |
| ------- | ----------- | ------------- |
| TCP SYN | Kali sending SYN packets to all 1000 ports sequentially | Classic port scan pattern — triggers IDS alerts |
| TCP RST/ACK | Windows responding to closed ports | Confirms port states |
| SMB | Server Message Block traffic on port 445 | File sharing protocol — high value target |
| NBSS | NetBIOS Session Service activity | Windows network discovery traffic |
| MS Browser | Microsoft Windows Browser Protocol | Windows network neighborhood traffic |

## Indicators of Compromise (IOCs)
| IOC | Details |
| ---- | ------- |
| Source IP | 192.168.20.11 (Kali Linux — Attacker) |
| Target IP | 192.168.20.10 (Windows 10 — Defender) |
| Scan Type | TCP SYN Service Version Detection (-sV) |
| Ports Targeted | Top 1000 TCP ports |
| Date / Time | 2026-03-14 at 06:48 EDT |
| Scan Duration | ~22 seconds |
| Tool Identified | Nmap 7.95 (visible in packet headers) |
| Packets Generated | 2,248 packets captured |

## Security Observations
## Port 445 (SMB) — High Risk
Port 445 runs the SMB (Server Message Block) protocol used for Windows file sharing. This is the exact port exploited by the WannaCry ransomware in 2017, which infected over 200,000 systems worldwide. In a production environment, this port should be blocked at the perimeter firewall or closely monitored via SIEM rules.

## Port 135 (RPC) — Lateral Movement Risk
Port 135 runs Microsoft RPC (Remote Procedure Call), commonly used by attackers for lateral movement within enterprise networks. It should be restricted to trusted hosts only. 

## Windows Firewall Effectiveness
When Windows Firewall was enabled, all 1000 scanned ports appeared as FILTERED — meaning the firewall successfully blocked Nmap from fingerprinting the system. This is a strong demonstration that host-based firewalls are an effective first line of defense.

## Scan Speed as Detection Signal
The entire scan of 1000 ports completed in approximately 22 seconds. In a real SIEM environment (e.g., Splunk, Microsoft Sentinel), this rapid sequential connection pattern would immediately trigger a port scan alert rule.
## What I Learned
• How to perform network reconnaissance using Nmap with service detection
• How to interpret Nmap results and understand what open ports reveal about a target
• How to capture and analyze live network traffic using Wireshark
• How to identify a port scan pattern from TCP SYN/RST packets in a packet capture
• The security significance of SMB (port 445) and RPC (port 135)
• How Windows Firewall affects scan results — FILTERED vs OPEN vs CLOSED
• How to document findings as Indicators of Compromise (IOCs)
• The importance of isolated lab environments for safe security testing


## About Me
I'm Saravanan — transitioning from a background in Food Technology into Cybersecurity with a focus on Security Operations Center (SOC) Analysis and Blue Team operations.
My background in Food Technology taught me quality control, process monitoring, and systematic documentation — skills that translate directly into SOC work where attention to detail and structured investigation are critical.
This lab documents my hands-on learning journey as I build real skills in threat detection, log analysis, network forensics, and incident response.


## "The best way to learn cybersecurity is to break things in a safe environment and understand why they broke."
⭐ If this lab helped you, give the repo a star!
