# Incident Report — Accidental Wazuh Stack Upgrade

**Date:** September 9, 2026
**Severity:** Medium
**Duration:** ~3 hours

## What Happened
Accidentally ran `sudo apt-get install wazuh-agent` on 
LinuxVM-LAB. APT resolved a conflict between wazuh-agent 
and wazuh-manager by removing the manager and upgrading 
the entire Wazuh stack from 4.9.2 to 4.14.7.

## Impact
- Wazuh manager removed
- Dashboard certificates missing (path changed in 4.14)
- API credentials mismatched
- DC01-LAB agent disconnected (version mismatch 4.9.2 vs 4.14.7)
- Custom detection rules lost (not backed up in repo)
- ~3 hours of lab downtime

## Root Cause
1. Running install commands without verifying the package 
   name first
2. Custom rules not backed up to GitHub
3. No package pinning to prevent accidental upgrades

## Resolution
1. Reinstalled wazuh-manager
2. Copied dashboard certificates to new 4.14 path
3. Reset API credentials via wazuh-passwords-tool
4. Upgraded DC01-LAB agent to 4.14.7 to match manager
5. Fixed ossec.conf server address (was 0.0.0.0)
6. Re-enrolled DC01-LAB agent

## Prevention
1. Pinned Wazuh packages with apt-mark hold
2. Verify package names before running apt-get install

## Lessons Learned
Always verify apt-get commands before running them on 
production-equivalent systems. Package managers resolve 
conflicts silently and destructively.
