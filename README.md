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

## What Was Built
- Wazuh 4.9.2 all-in-one installation (indexer, manager, 
  Filebeat, dashboard)
- DC01-LAB enrolled as active agent
- Windows Security Event Log ingestion verified: 
  Event ID 4624 (logon) and 4625 (failed logon) confirmed 
  in Threat Hunting dashboard
- MITRE ATT&CK telemetry active on domain controller
- CIS Windows Server 2019 benchmark SCA scan running

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
- **Week 3:** Custom detection rule — 5+ failed logons in 
  60 seconds triggers alert, tested against DC01-LAB
- **Month 4:** Kerberoasting detection via Event ID 4769 
  forwarded to this SIEM

## What Would Have Happened Without This
No visibility into authentication events on the domain 
controller. The Kerberoasting attack in Month 4 would be 
undetectable. Every subsequent detection rule in this roadmap 
depends on this pipeline being operational.
