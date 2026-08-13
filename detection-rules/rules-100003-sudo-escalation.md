# Rule 100003 — Sudo Privilege Escalation Detection

## What It Detects
Any sudo command execution on LinuxVM-LAB where a 
user escalates to root privileges.
Mapped to MITRE ATT&CK T1548.003 (Sudo and Sudo Caching).

## Why This Matters
Sudo abuse is one of the most common Linux privilege 
escalation techniques. An attacker with a low-privilege 
shell who runs sudo to gain root access would trigger 
this rule — providing detection coverage for post-exploitation
activity on the Wazuh manager host itself.

## Threshold Rationale
Any single sudo execution triggers the alert at level 10.
No frequency threshold needed — a single sudo event on 
a server is meaningful and warrants review.

## False Positive Risk
Legitimate administrative tasks by IT staff using sudo.
Mitigation: investigate the COMMAND field in the alert.
Legitimate commands (apt-get, systemctl) differ from 
attack patterns (bash, python, nc).

## MITRE ATT&CK Mapping
- Technique: T1548.003 — Sudo and Sudo Caching
- Tactic: Privilege Escalation, Defense Evasion

## Parent Rule
Built on Wazuh rule 5402 (Linux sudo execution)

## Evidence
- Alert firing: evidence/wazuh-rule-100003-sudo-fired.png
