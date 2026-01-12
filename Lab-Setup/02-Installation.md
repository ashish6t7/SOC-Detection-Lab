# Installation Guide

## Prerequisites

**Software Downloads:**
- VirtualBox (installed)
- Windows 11 ISO
- Kali Linux ISO
- [Splunk Enterprise (Free Trial)](https://www.splunk.com/en_us/download.html)
- [Splunk Universal Forwarder](https://www.splunk.com/en_us/download/universal-forwarder.html)
- [Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Sysmon Config (SwiftOnSecurity)](https://github.com/SwiftOnSecurity/sysmon-config)

---

## Step 1: Virtual Machine Setup

### Windows 11 VM

**VM Configuration:**
1. Create new VM in VirtualBox
2. Allocate **7 GB RAM**, **3 CPU cores**
3. Network Adapter: **Bridged Adapter**
4. Install Windows 11 from ISO

![RAM allocation to Windows 11 VM](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/0a95f1c5c81dd9421ed1e2af9b4b01aa23738d18/Lab-Setup/Setup-Images/Windows%2011%20VM%20RAM.png)

![Bridged adaptor windows 11](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/f49a22ef1fbaf393c9ad89a6d563b09bd2c5eb95/Lab-Setup/Setup-Images/Windows11-bridged-networking.png)

**Set Static IP:**
- Open Network Settings → Change adapter options
- IPv4 Properties → Manual IP: `192.168.31.52`

### Kali Linux VM

**VM Configuration:**
1. Create new VM in VirtualBox
2. Allocate **2.5 GB RAM**, **2 CPU cores**
3. Network Adapter: **Bridged Adapter**
4. Install Kali Linux from ISO

![RAM allocation to Kali VM](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/0a95f1c5c81dd9421ed1e2af9b4b01aa23738d18/Lab-Setup/Setup-Images/Kali%20VM%20RAM%20.png)

![Bridged adaptor kali ](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/0a95f1c5c81dd9421ed1e2af9b4b01aa23738d18/Lab-Setup/Setup-Images/bridged-networking.png)

**IP Configuration:**
- Use DHCP (automatic)

---

## Step 2: Splunk Installation

### On Host Machine (Windows 11)

1. **Download Splunk Enterprise** (free trial, 60-day license)

2. **Install:**
   - Installation path: `C:\Program Files\Splunk`
   - Accept license agreement

3. **First-time setup:**
   - Access: http://localhost:8000
   - Create admin username and password
   - Complete setup wizard

![Splunk Enterprise Web Interface](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/fd88614796fdc55fce3929485c3330d154f53cb9/Lab-Setup/Setup-Images/Splunk%20web%20interface.png)

---

### On Windows VM

1. **Download Splunk Universal Forwarder**

2. **Install with configuration:**
   - During installation, when prompted:
   - **Receiving Indexer:** `192.168.31.50:9997`
   - Set admin username and password

![Universal Forwarder Installation - Receiving Indexer](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/5a6b8ecdff8eadbf4a3ae2bfdb4dcbf8e69c77ba/Lab-Setup/Setup-Images/universal%20forwarder%20port%209997.png))

**This configures the forwarder to send logs to your host Splunk instance.**

---

## Step 3: Sysmon Installation

**On Windows VM:**

1. Download Sysmon and SwiftOnSecurity config to `C:\Tools\`

2. **Open CMD as Administrator:**
```cmd
cd C:\Users\victim\Downloads\Sysmon
sysmon64.exe -accepteula -i sysmonconfig-export.xml
```

3. **Verify installation:**
```cmd
sc query Sysmon64
```

**Expected output:** `STATE: RUNNING`

![sysmon service running](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/7ff4594d1409b2623fc8da6fac1c29cb70748d3e/Lab-Setup/Setup-Images/sysmon%20Running%20CMD.png)

![Sysmon Service Running](https://github.com/ashish6t7/Enterprise-SOC-Lab/blob/c81dff87a1be96fe7e0d18b9b9587e8ed288bdb7/Lab-Setup/Setup-Images/sysmon%20running%20successfully.png)

---

## Installation Complete

**What we now have:**
- ✅ Host with Splunk Enterprise installed
- ✅ Windows VM with Universal Forwarder installed
- ✅ Windows VM with Sysmon monitoring installed
- ✅ Kali VM for attack simulation (optional)

---

**Next:** [Configuration Guide](./03-Configuration.md) to set up log forwarding and start collecting data.
