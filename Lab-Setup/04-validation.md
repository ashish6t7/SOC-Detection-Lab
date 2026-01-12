# Lab Validation

After completing the installation and configuration, this section demonstrates successful log collection and forwarding.

---

## 1. Verify Log Ingestion

**Search for logs in Splunk:**
```spl
index=windows sourcetype=* | stats count by sourcetype
```

**Expected output:**

![Log Sources Confirmed](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/3a0a555908e23c26f119301e1dcfda46d0a99456/Lab-Setup/Setup-Images/07-Splunk%20receiving%20Logs%20(on%20HOST).png)

**Results: for last 5 minutes** 

- `WinEventLog:Security` -  Running
- `WinEventLog:System` - Running
- `WinEventLog:Application` - Running
- `XmlWinEventLog:Microsoft-Windows-Sysmon/Operational` - Running

✅ All log sources are being collected successfully.

---

## Lab Status: ✅ Fully Operational

All components are functioning correctly:
- ✅ Splunk receiving logs on port 9997
- ✅ Universal Forwarder sending logs from Windows VM
- ✅ Windows Event Logs (Security, System, Application) ingesting
- ✅ Sysmon logs ingesting 
- ✅ Logs are current (real-time collection)

**The lab is ready for threat detection analysis.**

---

**Next Steps:**
- Return to [README](../README.md) for project overview
- Explore threat detection use cases (Security-Incident-Detection repository)

---

## **Final project structure:**
```
Enterprise-SOC-Lab/
│
├── README.md
│
└── Lab-Setup/
    ├── 01-Architecture.md       
    ├── 02-Installation.md       
    ├── 03-Configuration.md      
    ├── 04-Validation.md         
    └── Setup-Images/
```
