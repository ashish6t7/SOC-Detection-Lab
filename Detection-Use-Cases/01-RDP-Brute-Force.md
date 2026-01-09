# RDP Brute Force Detection

## Overview

Remote Desktop Protocol (RDP) brute force attacks involve multiple failed login attempts from the same source IP, attempting to guess credentials.

## Event ID

**Windows Event ID 4625** - Failed Logon Attempt

## Detection Query
```spl
index=windows EventCode=4625 LogonType=3
| stats count by src_ip, TargetUserName
| where count > 10
| sort - count
| eval threat_level = if(count > 50, "Critical", if(count > 20, "High", "Medium"))
| table src_ip, TargetUserName, count, threat_level
```

## Query Breakdown

- `EventCode=4625`: Failed login attempts
- `LogonType=3`: Network logon (RDP)
- `stats count by src_ip, TargetUserName`: Count failures per IP and user
- `where count > 10`: Threshold for suspicious activity
- `eval threat_level`: Categorize severity

## Visualization
```spl
index=windows EventCode=4625 LogonType=3
| timechart span=1h count by src_ip
```

![RDP Brute Force Timeline](./images/rdp-brute-detection.png)

## MITRE ATT&CK Mapping

- **Technique:** T1110.001 (Brute Force: Password Guessing)
- **Tactic:** Credential Access

## Indicators of Compromise (IOCs)

- Multiple failed logins (>10) from single IP
- Failed attempts across multiple user accounts
- Time-clustered attempts (within minutes)

## Recommended Actions

1. **Immediate:**
   - Block source IP at firewall
   - Reset passwords for targeted accounts
   - Investigate source IP (internal vs external)

2. **Long-term:**
   - Implement account lockout policy after multiple failed login attempts
   - Enable Multi-Factor Authentication (MFA)
   - Use VPN for RDP access
   - Monitor for Event ID 4624 (successful login) after multiple 4625 Event IDs

---

**Next:** [Lateral Movement Detection](./02-Lateral-Movement.md)
