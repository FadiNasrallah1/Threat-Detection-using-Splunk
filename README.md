# Threat-Detection-using-Splunk
In this project, we focus on how cyberattacks can be simulated in a controlled lab environment and detected using Splunk. The goal is to better understand how attackers operate and how security teams can identify and respond to these threats in real time.
Designed and implemented a hands-on cybersecurity lab to simulate real-world attack scenarios and detect malicious activities using Splunk SIEM in real time.

The lab includes multiple stages of the attack lifecycle, including brute force attempts, unauthorized remote access, and post-exploitation activities. It demonstrates both Red Team (offensive security) and Blue Team (defensive monitoring) techniques, showcasing the ability to build, attack, detect, and defend within a controlled environment.

# Tools & Technologies

- Splunk Enterprise 9.4.3
- Splunk Universal Forwarder 9.4.0
- Kali Linux
- Windows Server 2022
- Netdiscover
- Namap
- Hydra
- xfreerdp
- Windows Firewall

# Architecture

<img width="1536" height="1024" alt="Image" src="https://github.com/user-attachments/assets/7f1dd6fc-7832-43c1-9981-f9cf552d1a86" />


# Attacks Simulation

1. Reconnaissance — NetDiscover & Nmap

Performed network reconnaissance to discover live hosts and identify open ports on the target machine.

Discover live hosts on the subnet
netdiscover -r 10.0.2.0/24

Scan target for open ports and services
nmap -sV -p 3389 <TARGET_IP>

Goal: Confirm target IP and verify RDP port 3389 is open before launching the attack.

2. RDP Brute Force — Hydra

Simulated a brute force attack against the Windows Server RDP service using a custom password list.

hydra -l Administrator -P /usr/share/wordlists/rockyou.txt rdp://<TARGET_IP>
Detection: EventCode 4625 — Failed Logon

3. Remote Desktop Takeover — xfreerdp

Gained full remote desktop access after successfully cracking the password.

xfreerdp /u:Administrator /p:<CRACKED_PASSWORD> /v:<TARGET_IP>
Detection: EventCode 4624 — Successful Logon

# Blue Team Defense

- Block attacking IP via Windows Firewall inbound rule
- Enable account lockout after 5 failed login attempts
- Real-time monitoring with Splunk dashboards
- Automated alerts triggered on threshold breach

#Block attacker IP via PowerShell
New-NetFirewallRule -DisplayName "Block Attacker" `
  -Direction Inbound `
  -RemoteAddress <ATTACKER_IP> `
  -Action Block
# Repository Structure

splunk-siem-lab/
├── docs/
│   ├── architecture.png
│   └── dashboard.png
├── recon/
│   └── netdiscover_nmap.md
├── attack/
│   ├── hydra_commands.md
│   └── xfreerdp_commands.md
├── detection/
│   └── splunk_queries.md
├── defense/
│   └── firewall_rules.md
├── LICENSE
└── README.md



⚠️ Disclaimer

All attacks were performed in an isolated virtual machine environment for educational purposes only. No real systems were targeted. This project is intended to demonstrate SOC analyst skills in a controlled lab setting.
