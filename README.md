# Wazuh SIEM Lab — Month 2: Security+ Sprint + SIEM Evidence

## Purpose
Deployed a Wazuh 4.9.2 all-in-one SIEM on LinuxVM-LAB (Ubuntu, 
192.168.56.13) and enrolled DC01-LAB (Windows Server 2019, 
192.168.56.10) as a monitored agent. This builds directly on the 
lab environment from Month 1 and closes the SIEM usage gap in 
my CV.

## Environment
| Component | Role | IP |
|---|---|---|
| LinuxVM-LAB | Wazuh Manager + Indexer + Dashboard | 192.168.56.13 |
| DC01-LAB | Monitored Windows Agent | 192.168.56.10 |

## Detection Coverage Summary
| Rule | MITRE Technique | What It Catches | Severity |
|---|---|---|---|
| 100001 | T1110 Brute Force | 5+ failed logons from same IP in 60s | 10 |
| 100002 | T1078 Valid Accounts | Successful DC logon outside business hours | 10 |
| 100003 | T1548.003 Sudo Caching | Unexpected sudo execution on Linux host | 12 |
| 100004 | T1548.003 Sudo Caching | Known admin sudo commands (noise reduction) | 3 |

## Lab Architecture
![SIEM Topology](architecture/wazuh-siem-topology.png)

Two-node lab: Wazuh all-in-one manager on LinuxVM-LAB 
collecting Windows Security Event Logs from DC01-LAB 
(domain controller) and Linux auditd logs from its own 
host. Custom detection rules cover the three most common 
AD and Linux attack patterns.

## What Was Built
- Wazuh 4.9.2 all-in-one installation (indexer, manager, 
  Filebeat, dashboard)
- DC01-LAB enrolled as active agent
- Windows Security Event Log ingestion verified: 
  Event ID 4624 (logon) and 4625 (failed logon) confirmed 
  in Threat Hunting dashboard
- MITRE ATT&CK telemetry active on domain controller
- CIS Windows Server 2019 benchmark SCA scan running
- Rule 100002: After-hours logon detection on DC 
  (MITRE T1078)
- Rule 100001 tuned: same_field constraint added to 
  reduce false positives, before/after documented
- Rule 100001: Brute force detection — 5+ failed logons 
  from same source IP in 60 seconds (MITRE T1110)
- Linux auditd log forwarding from LinuxVM-LAB — 
  identity, privilege escalation, sudo, and SSH 
  config changes monitored
- Rule 100003: Sudo privilege escalation detection 
  on Linux host (MITRE T1548.003)

## Problems Encountered and Resolved
### 1. Filebeat installation failure
**Error:** Filebeat installation failed mid-install, triggering 
auto-cleanup cascade that corrupted package state.
**Root cause:** Insufficient disk space (9.9GB available vs 
~20GB minimum required).
**Fix:** Removed unused desktop snaps (firefox, gnome, mesa) 
freeing 3GB, removed old kernel version, resized VDI from 
25GB to 50GB using VBoxManage modifymedium --resize 51200.

### 2. JVM startup timeout
**Error:** wazuh-indexer service start operation timed out.
**Root cause:** 2.8GB RAM insufficient for OpenSearch JVM 
heap initialization.
**Fix:** Increased VM RAM allocation from 2.8GB to 7GB in 
VirtualBox settings.

### 3. Corrupted package state
**Error:** dpkg exit status 127 on wazuh-manager removal — 
pre-removal script called missing binary /var/ossec/bin/wazuh-control.
**Fix:** Neutered broken prerm and postrm maintainer scripts, 
forced package removal, manually cleaned /var/ossec.

### 4. Agent connectivity failure
**Error:** DC01-LAB could not reach Wazuh manager on ports 
1514/1515.
**Root cause:** Static IP assigned to wrong network adapter 
inside Windows Server.
**Fix:** Reassigned 192.168.56.10 to the correct VirtualBox 
host-only adapter inside DC01-LAB network settings.

### 5. Host machine browser unreachable
**Root cause:** Windows Firewall blocking traffic to 
192.168.56.0/24 subnet.
**Fix:** Added permanent inbound/outbound firewall rules 
allowing traffic to lab subnet.

## What This Enables
- **Month 4:** Kerberoasting detection via Event ID 4769 
  forwarded to this SIEM
- **Month 7:** Sentinel analytics rules built on same 
  detection logic (frequency correlation, time-based 
  filtering)
- **Month 8:** auditd rules provide baseline for container 
  security work — same audit principles apply to 
  container runtime monitoring

## What Would Have Happened Without This
No visibility into authentication events on the domain 
controller. The Kerberoasting attack in Month 4 would be 
undetectable. Every subsequent detection rule in this roadmap 
depends on this pipeline being operational.

**Without rule 100001 (brute force):**
An attacker running a password spray against DC01-LAB 
would generate dozens of Event ID 4625 entries that 
appear as individual low-severity events. No automated 
alert would fire. An analyst would need to manually 
correlate the pattern — something that rarely happens 
at 2am on a Friday.

**Without rule 100002 (after-hours logon):**
A threat actor using stolen credentials to access the 
domain controller outside business hours would appear 
as a normal successful logon event (level 3). No 
escalation would occur. The compromise could persist 
for days before discovery.

**Without rule 100003 (sudo escalation):**
An attacker with a low-privilege shell on LinuxVM-LAB 
who escalated to root via sudo would be invisible. 
The Wazuh manager itself would be compromised with no 
detection — effectively blinding the entire SIEM.

**Without auditd forwarding:**
Changes to /etc/passwd, /etc/shadow, or /etc/sudoers 
on LinuxVM-LAB would be undetected. An attacker 
establishing persistence by adding a backdoor user 
would leave no trail in Wazuh.
