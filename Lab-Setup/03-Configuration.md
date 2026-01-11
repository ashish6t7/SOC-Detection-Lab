# Configuration

## 1. Splunk Configuration (on Host)

### - Enable Receiving Port (in Splunk web)
1. Settings → Forwarding and receiving → Configure receiving
2. New Receiving Port: `9997`
3. Save

![image alt](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/45351a30976b663c60fbac1e98a216f92380726e/Lab-Setup/Setup-Images/receiving%20port%209997%20.png)

### - Create Index (in Splunk web)
1. Settings → Indexes → New Index
2. Index Name: `windows`
3. Save
   
![image alt](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/8d74615c662d5684bc23284d2a417f691462c57f/Lab-Setup/Setup-Images/02-new%20INDEX%20created.png)

## 2. Universal Forwarder Configuration (on Windows VM)

### - Configure inputs.conf

-this inputs.conf includes both Windows "Event Logs" and "Sysmon logs"

Create file: `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf` and insert the following code:
```ini
[default]
host = WindowsVM

[WinEventLog://Security]
disabled = 0
index = windows
sourcetype = WinEventLog:Security

[WinEventLog://System]
disabled = 0
index = windows
sourcetype = WinEventLog:System

[WinEventLog://Application]
disabled = 0
index = windows
sourcetype = WinEventLog:Application

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = windows
sourcetype = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
renderXml = true
```

![image alt](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/b6a7c9e27da035783e594b77a43eb657eaad1d8b/Lab-Setup/Setup-Images/02-inputs-conf%20(Windows%2011%20VM)%20.png)

### - Restart Service

- Windows + R - services.msc -  SplunkForwarder - **Restart**, OR:
```cmd
net stop SplunkForwarder
net start SplunkForwarder
```

## 3. Firewall Configuration

### - Host (Inbound Rule)
- Port: 9997 TCP
- Action: Allow
- Profiles: All

![image alt](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/b6a7c9e27da035783e594b77a43eb657eaad1d8b/Lab-Setup/Setup-Images/04-Splunk%20inbound%20rule%20-%20port%209997%20(HOST).png)

### - Windows 11 VM (Outbound Rule)
- Port: 9997 TCP
- Action: Allow
- Profiles: All
  
![image alt](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/b6a7c9e27da035783e594b77a43eb657eaad1d8b/Lab-Setup/Setup-Images/03-splunk%20outbound%20rule%20-%20port%209997%20(Windows%2011%20VM).png)

## 4. Troubleshooting

### - Issue: Sysmon Logs Not Appearing, but Event Logs were...

**Problem:** Sysmon service running as wrong account

**Solution:**
1. Open `services.msc`
2. Right-click Sysmon64 → Properties
3. Logon tab → Select "Local System account"
4. Restart service
   
![image alt](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/b6a7c9e27da035783e594b77a43eb657eaad1d8b/Lab-Setup/Setup-Images/05-sysmon%20troubleshoot.png)

*This issue took 10+ hours to discover - always check service permissions!*

