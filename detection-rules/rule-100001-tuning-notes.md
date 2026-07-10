# Rule 100001 — Tuning Notes

## Before Tuning
Rule fired on any 5 failed logons within 60 seconds 
regardless of source. During lab testing, this caused 
the rule to fire on distributed failed logons from 
multiple sources simultaneously — not a true brute 
force pattern.

## Change Made
Added same_field constraint on 
data.win.eventdata.ipAddress — rule now only fires 
when 5+ failures originate from the same source IP 
within the 60-second window.

## Why This Reduces False Positives
A user mistyping their password and a different user 
doing the same thing simultaneously would previously 
trigger the rule. With same_field, only a single 
source attempting 5+ failures triggers the alert — 
which is the actual brute force pattern.

## Trade-off
Distributed brute force attacks (multiple source IPs, 
one attempt each) will not trigger this rule. That 
attack pattern requires a different rule correlating 
on the target account name instead of source IP.

## Alert Volume Impact
Without same_field: rule fired during normal business 
hours when multiple users had authentication issues 
simultaneously.
With same_field: rule fires only on concentrated 
single-source attack patterns.
