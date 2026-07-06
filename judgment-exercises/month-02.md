# Month 2 — Human Judgment Exercise

## Scenario
A Wazuh alert fires: 12 failed logon attempts on the domain
controller in 45 seconds, followed by a successful logon.
The source IP is an internal workstation. It is 11pm on a
Friday. You are alone monitoring.

## My Decision Log

### Immediate Action: Investigate or Escalate?
Escalate immediately while investigating simultaneously.

A successful logon after 12 failed attempts in 45 seconds
on a domain controller is a presumed compromise until proven
otherwise. Waiting to gather information before escalating
gives an attacker time to create persistence, dump
credentials, or move laterally. The escalation call happens
with what I have now — not after I feel ready.

At Pretorium Trust, escalation at 11pm Friday means
calling Dennis Dyer twice. If no answer, I send a WhatsApp
message immediately — I do not wait for a callback before
messaging:

"Potential brute-force with successful logon on DC01 at
11pm. Source workstation 192.168.56.10. Account
svc-sqlbackup now active. Do you want me to isolate the
workstation or hold pending your instruction?"

If no response within 10 minutes, I escalate to the CEO
using the same message. If both are unreachable, I
document every attempt with timestamps, continue
monitoring, and prepare a written incident summary for
first thing Monday. I flag the absence of an after-hours
escalation policy as a finding — an organisation that
cannot be reached during a Friday night breach has a
governance gap, not just a technical one.

### 3 Pieces of Evidence That Would Change My Decision
to Immediate Escalation

These would change "escalate and monitor" to
"isolate immediately without waiting for instruction":

1. The compromised workstation begins sending unusual
   outbound traffic volume to internal hosts it has no
   business reason to contact — indicating lateral
   movement rather than a single account compromise.
2. The workstation establishes a connection to an IP
   address absent from all network documentation —
   indicating command-and-control communication.
3. A second workstation begins showing the same
   failed-logon pattern within the same time window —
   indicating an automated attack tool, not a single
   user mistyping their password.

## What I Would Have Done Wrong Without This Exercise
I would have investigated first to "build a case" before
escalating — not realising that every minute spent
preparing to make a phone call is a minute an attacker
uses to entrench themselves. The phone call and the
investigation run in parallel, not in sequence.
