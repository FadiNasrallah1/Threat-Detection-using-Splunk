# Lab Rebuild Guide – Splunk + Forwarder (Daily Reset Version)

**Last Updated:** April 10, 2026  
**Total Rebuild Time:** ~12–15 minutes

### Lab Details
- **Kali Linux**: 10.0.2.6 (today) → Splunk Enterprise 9.4.3  
- **Windows Server 2022**: 10.0.2.15 → Universal Forwarder 9.4.0  
- **Kali Credentials**: `kaliuser` / `Password.3!!`  
- **Windows Credentials**: `Administrator` / `Password.1!!`  

**Goal:** Real-time Windows Security logs (EventCode 4625) in Splunk for Hydra RDP brute-force demo.

**Note:** IP addresses may change after a VM reset. Always use the current IPs for that day.

---

## 1. Splunk Enterprise 9.4.3 on Kali Linux

Run these commands as user `kaliuser`:

Update package list with sudo apt update

1. Download Splunk
wget -O splunk.tgz https://download.splunk.com/products/splunk/releases/9.4.3/linux/splunk-9.4.3-237ebbd22314-linux-amd64.tgz

2. Extract
sudo tar -xvzf splunk.tgz -C /opt

3. Set ownership
sudo chown -R kaliuser:kaliuser /opt/splunk

4. Start Splunk (first time)
cd /opt/splunk/bin
sudo ./splunk start --accept-license

5. Set credentials when prompted:
    Username: kaliuser
    Password: Password.3!!

 6. Enable boot-start and receiving port
sudo ./splunk enable boot-start
sudo ./splunk enable listen 9997 -auth kaliuser:Password.3!!

 7. Restart Splunk
sudo ./splunk restart

Verify port 9997 is listening:

bash

sudo ss -tuln | grep 9997

You should see: 0.0.0.0:9997 or *:9997

Open Firefox → http://10.0.2.6:8000
Login: kaliuser / Password.3!!

## 2. Additional Tools on KaliPassword List (60 passwords)

bash

nano ~/passwords.txt

Paste the following list into nano:

password
admin
123456
letmein
welcome
qwerty
abc123
password1
iloveyou
monkey
jesus
sunshine
princess
flower
superman
batman
trustno1
ninja
hello123
admin123
welcome123
password123
12345678
123456789
qwerty123
abc123456
letmein123
adminadmin
passw0rd
Password1
Summer2026
Winter2025
Spring2026
Fall2025
Company123
User12345
Test123
Secret123
AdminPass
Login123
Backup2026
Server123
Database1
Security1
Cyber2026
Hackme123
Root123
Toor123
Changeme
Default123
Temp123
Guest123
Demo123
Lab123
Student123
Class2026
Capstone1
Password.1!!

Save: Ctrl + O → Enter
Exit: Ctrl + X

Verify:

bash

wc -l ~/passwords.txt

Install Hydrabash

sudo apt install hydra -y

Install xfreerdpbash

sudo apt install freerdp3-x11 -y

## 3. Splunk Universal Forwarder 9.4.0 on Windows Server 2022
Login as: Administrator / Password.1!!

3.1 Install Forwarder
 1. Download Splunk Universal Forwarder 9.4.0 (Windows 64-bit .msi).
 2. Run the .msi as Administrator.
 3. Installer settings:
  Deployment Server: Leave completely blank
  Receiving Indexer: 10.0.2.6:9997
  Service Account: Local System

3.2 Create inputs.conf (Critical Step)
Run in Command Prompt as Administrator:

cmd
mkdir "C:\Program Files\SplunkUniversalForwarder\etc\system\local" 2>nul

(
echo [WinEventLog://Security]
echo disabled = false
echo start_from = oldest
echo current_only = false
echo index = main
) > "C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf"

3.3 Restart Forwardercmd

net stop SplunkForwarder && net start SplunkForwarder

## 4. Quick VerificationOn Windows (Command Prompt as Administrator):

cmd
type "C:\Program Files\SplunkUniversalForwarder\var\log\splunk\splunkd.log" | findstr /i "Connected to"

You should see: Connected to idx=10.0.2.6:9997

On Kali Splunk Web (real-time search):

spl

index=main sourcetype="WinEventLog:Security"

Force test events on Windows:Lock screen (Ctrl + Alt + Del → Lock)
Log back in (try wrong password a few times)
Run gpupdate /force

## 5. Common Daily Fixes
Problem                                                Fix

No events in Splunk                          Re-run inputs.conf commands + restart forwarder

Port 9997 not listening on Kali              sudo ./splunk enable listen 9997 -auth kaliuser:Password.3!! then restart Splunk

“No stanzas found” in log                    Re-create inputs.conf

Telnet not working on Windows                powershell -Command "Enable-WindowsOptionalFeature -Online -FeatureName TelnetClient -All"


## 6. Windows Preparation for Attacks

Enable RDP
  Right-click Start → System → Remote Desktop → Enable Remote Desktop
  Advanced settings → Uncheck “Require devices to use Network Level Authentication”

Enable Nmap Scan LoggingRun in PowerShell as Administrator:

powershell

auditpol /set /subcategory:"Filtering Platform Connection" /success:enable /failure:enable

Verify:

powershell

auditpol /get /subcategory:"Filtering Platform Connection"
