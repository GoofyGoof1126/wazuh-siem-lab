# Rule 100002 — After-Hours Logon Detection

## What It Detects
Successful Windows logon on the domain controller 
outside business hours (6pm to 7am).
Mapped to MITRE ATT&CK T1078 (Valid Accounts).

## Why This Matters
Attackers with valid stolen credentials often access 
systems outside business hours to avoid detection by 
staff. A domain controller logon at 2am is unusual 
enough to warrant investigation regardless of whether 
the credentials are valid.

## Threshold Rationale
Business hours defined as 7am-6pm based on Pretorium 
Trust's operating hours. Any successful DC logon 
outside this window triggers the alert.

## False Positive Risk
- IT staff performing legitimate after-hours 
  maintenance
- Automated service accounts running scheduled tasks
  
Mitigation: investigate the account name in the alert. 
Service accounts follow naming conventions 
(svc-prefix). Named user accounts after hours warrant 
immediate follow-up.

## MITRE ATT&CK Mapping
- Technique: T1078 — Valid Accounts
- Tactic: Defense Evasion, Persistence, 
  Privilege Escalation, Initial Access

## Evidence
- Alert firing: evidence/wazuh-rule-100002-afterhours-fired.png
