# Splunk Dashboard Documentation

**Dashboard Name:** Brute Force Attack Detection Dashboard  
**Layout:** Grid (Dashboard Studio)  
**Time Range:** Real-time (30-second or 1-minute window)

---

### Real-World Note
In a real SIEM environment, analysts do **not** know the attacker’s IP address at the start of an incident.  
The main dashboard panels below show activity from **all sources**. Once suspicious behavior is detected, analysts can filter by `source_address` (or `src_ip`) to focus on the attacker.

---

## Dashboard Panels

### 1. Failed Logon Attempts (Main Spike Chart)
spl
index=main sourcetype="WinEventLog:Security" EventCode=4625
| timechart span=30s count as "Failed Logons"

Visualization: Line Chart
Title: Failed Logon Attempts (Real-Time Spike)

### 2. Top Attacking IPs (Key Detection Panel)
spl
index=main sourcetype="WinEventLog:Security" EventCode=4625
| rex field=Message "Source Network Address:\s+(?<src_ip>\S+)"
| stats count by src_ip
| sort -count

Visualization: Bar Chart
Title: Top Attacking IPs

### 3. Attack Type Breakdown (RDP vs SMB/Network)
spl
index=main sourcetype="WinEventLog:Security" EventCode=4625
| eval Attack_Type = case(Logon_Type="10", "RDP", Logon_Type="3", "SMB/Network", true(), "Other")
| stats count by Attack_Type

Visualization: Pie Chart or Column Chart
Title: Brute Force by Attack Type

### 4. Scan / Recon Activity (Nmap + netdiscover)
spl
index=main sourcetype="WinEventLog:Security" (EventCode=5156 OR EventCode=5157 OR EventCode=5158)
| timechart span=15s count as "Scan Activity"

Visualization: Line Chart
Title: Nmap / Recon Activity

### 5. Successful Logins (Proof of Compromise)
spl
index=main sourcetype="WinEventLog:Security" EventCode=4624 Logon_Type=10
| stats count as "Successful RDP Logins"

Visualization: Single Value
Title: Successful Logins

### How to Build the Dashboard
1. In Splunk Web → Dashboards → Create New Dashboard → Dashboard Studio → Grid
2. Add a Time Range input and set it to Real-time
3. Click + Add Chart for each panel and paste the searches above
4. Set all panels to refresh every 10 seconds
