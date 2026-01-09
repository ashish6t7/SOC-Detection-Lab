# Configuration

## Splunk Configuration (on Host)

### Enable Receiving Port (in Splunkweb)
1. Settings → Forwarding and receiving → Configure receiving
2. New Receiving Port: `9997`
3. Save

### Create Index (in splunk web)
1. Settings → Indexes → New Index
2. Index Name: `windows`
3. Save

## Universal Forwarder Configuration (on Windows VM)

### Configure inputs.conf

Create file: `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf`
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

### Restart Service

- Windows + R - services.msc -  SplunkForwarder - **Restart**, OR:
```cmd
net stop SplunkForwarder
net start SplunkForwarder
```

## Firewall Configuration

### Host (Inbound Rule)
- Port: 9997 TCP
- Action: Allow
- Profiles: All

### Windows 11 VM (Outbound Rule)
- Port: 9997 TCP
- Action: Allow
- Profiles: All

## Troubleshooting

### Issue: Sysmon Logs Not Appearing, but Event Logs were...

**Problem:** Sysmon service running as wrong account

**Solution:**
1. Open `services.msc`
2. Right-click Sysmon64 → Properties
3. Logon tab → Select "Local System account"
4. Restart service

*This issue took 10+ hours to discover - always check service permissions!*

---

**Next:** [Detection Use Cases](../Detection-Use-Cases/)
