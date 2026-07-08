# Rule 100001 — Brute Force Detection

## What It Detects
5 or more failed Windows logon attempts from the same
source IP within 60 seconds on the domain controller.
Mapped to MITRE ATT&CK T1110 (Brute Force).

## Why This Threshold
5 failures in 60 seconds eliminates legitimate mistyped
passwords (1-2 events) while catching automated tools
that typically attempt 10-100 passwords per minute.

Lower threshold = more false positives from users locking
themselves out.
Higher threshold = attacker gets more attempts before
detection fires.

## False Positive Risk
A legitimate user who mistyped their password and retried
rapidly from the same workstation. Mitigated by the
same_field constraint on data.win.eventdata.ipAddress —
only counts failures from the same source IP, preventing
false positives from multiple users failing simultaneously.

## Rule XML
Parent rule: 60122 (Wazuh built-in Windows failed logon)
Custom rule: 100001 (frequency correlation on parent)

## MITRE ATT&CK Mapping
- Technique: T1110 — Brute Force
- Tactic: Credential Access

## Evidence
- Alert firing confirmed: evidence/wazuh-custom-rule-100001-fired.png
- Rule file: local_rules.xml on LinuxVM-LAB

## What Would Have Happened Without This Rule
5 individual failed logon events (rule 60122, level 5)
would appear in the dashboard with no correlation and no
alert. An analyst would need to manually spot the pattern
across multiple events. This rule surfaces the attack
automatically at level 10, ensuring it appears in
high-priority alert queues without manual investigation.
