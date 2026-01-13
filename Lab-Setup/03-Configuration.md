# Configuration

## 1. Splunk Configuration (Host)

### Enable Receiving Port

**In Splunk Web:**

1. Settings → Forwarding and receiving → Configure receiving
2. New Receiving Port: `9997`
3. Save

![Splunk Receiving Port Configuration](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/45351a30976b663c60fbac1e98a216f92380726e/Lab-Setup/Setup-Images/receiving%20port%209997%20.png)

---

### Create Index

**In Splunk Web:**

1. Settings → Indexes → New Index
2. Index Name: `windows`
3. Save

![Splunk Windows Index Creation](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/8d74615c662d5684bc23284d2a417f691462c57f/Lab-Setup/Setup-Images/02-new%20INDEX%20created.png)

---

## 2. Universal Forwarder Configuration (Windows VM)

### Configure inputs.conf

**This configuration collects:**
- Windows Event Logs (Security, System, Application)
- Sysmon Logs (Operational)

**Create file:** `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf`

**Insert the following:**
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

![Universal Forwarder inputs.conf Configuration](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/b6a7c9e27da035783e594b77a43eb657eaad1d8b/Lab-Setup/Setup-Images/02-inputs-conf%20(Windows%2011%20VM)%20.png)

---

### Restart Forwarder Service

**Option 1 (GUI):**
- Press `Windows + R`
- Type `services.msc` and press Enter
- Find `SplunkForwarder` service
- Right-click → Restart

**Option 2 (Command Line):**
```cmd
net stop SplunkForwarder
net start SplunkForwarder
```

---

## 3. Firewall Configuration

### Host - Inbound Rule

**Allow Splunk to receive logs on port 9997:**

- **Rule Type:** Port
- **Protocol:** TCP
- **Port:** 9997
- **Action:** Allow the connection
- **Profiles:** Domain, Private, Public (all)

![Host Firewall Inbound Rule](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/b6a7c9e27da035783e594b77a43eb657eaad1d8b/Lab-Setup/Setup-Images/04-Splunk%20inbound%20rule%20-%20port%209997%20(HOST).png)

---

### Windows VM - Outbound Rule

**Allow Universal Forwarder to send logs to host:**

- **Rule Type:** Port
- **Protocol:** TCP
- **Port:** 9997
- **Action:** Allow the connection
- **Profiles:** Domain, Private, Public (all)

![VM Firewall Outbound Rule](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/b6a7c9e27da035783e594b77a43eb657eaad1d8b/Lab-Setup/Setup-Images/03-splunk%20outbound%20rule%20-%20port%209997%20(Windows%2011%20VM).png)

---

## 4. Troubleshooting

### Issue: Sysmon Logs Not Appearing

**Problem:**  
Windows Event Logs (Security, System, Application) were successfully forwarded to Splunk, but Sysmon logs were missing from the `windows` index.

**Root Cause:**  
Sysmon service was running under the wrong account, preventing log access by the Universal Forwarder.

**Solution:**

1. Open `services.msc` (Windows + R → type `services.msc`)
2. Locate **Sysmon64** service
3. Right-click → **Properties**
4. Navigate to **Log On** tab
5. Select **"Local System account"**
6. Click **Apply** → **OK**
7. Restart the Sysmon service

![Sysmon Service Permission Fix](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/b6a7c9e27da035783e594b77a43eb657eaad1d8b/Lab-Setup/Setup-Images/05-sysmon%20troubleshoot.png)

**Verification:**

After restarting the service, check Splunk for Sysmon logs:
```spl
index=windows sourcetype=* | stats count by sourcetype
```

If sysmon logs appear, the issue is resolved :

![sysmon logs received successfully](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/3a0a555908e23c26f119301e1dcfda46d0a99456/Lab-Setup/Setup-Images/07-Splunk%20receiving%20Logs%20(on%20HOST).png)

---

**Lesson Learned:**  
This issue took **16 hours** to troubleshoot across multiple attempts. Always verify service account permissions when logs fail to forward, especially for specialized log sources like Sysmon.

**Key troubleshooting steps for similar issues:**
- Check service status (`services.msc`)
- Verify service account (should be Local System for most security services)
- Review firewall rules (inbound/outbound)
- Check Universal Forwarder logs: `C:\Program Files\SplunkUniversalForwarder\var\log\splunk\splunkd.log`

---

**Next:** [Lab Validation and Final result](../Lab-Setup/04-validation.md)
