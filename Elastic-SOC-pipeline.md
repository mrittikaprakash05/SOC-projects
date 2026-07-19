# End-to-End SOC Pipeline (Kali → Elastic Agent → Detection Rule)

## What this is
Built a personal SOC lab from scratch: a monitored Kali Linux endpoint,
an Elastic Agent with Elastic Defend (EDR) forwarding telemetry to
Elastic Cloud Security, and a custom detection rule that fires real
alerts on simulated attacks.

## Stack
- Oracle VirtualBox (Kali Linux 2025.4)
- Elastic Cloud — Elastic Security Serverless
- Elastic Agent 9.4.3 + Elastic Defend integration (Complete EDR)

## What I did
1. Provisioned an Elastic Security project and configured Elastic
   Defend for full endpoint telemetry (process, file, network events).
2. Enrolled the Kali VM as a monitored endpoint via the Elastic Agent.
3. Simulated two categories of attacker behavior:
   - SSH brute-force login attempts (scripted with `sshpass`)
   - Post-exploitation recon commands (`whoami`, `id`, `uname -a`,
     `cat /etc/passwd`, `sudo -l`)
4. Queried the ingested telemetry in Kibana Discover using KQL:
   - `process.name : "sshd" or message : "Failed password"` → 31 matching events
   - `process.name : "whoami" or process.name : "id" or ...` → 18 matching events
5. Built a two-panel Kibana dashboard visualizing overall process
   activity vs. the isolated attack/recon events.
6. Authored a custom Threshold detection rule ("SSH Brute Force
   Detection – Kali SOC Lab") that alerts when 5+ sshd events occur
   per agent, on a 5-minute schedule with a 1-minute lookback.
7. Validated the full pipeline by generating a fresh batch of failed
   logins and confirming the rule fired a real alert.

## Result
Full pipeline confirmed working end-to-end: endpoint → agent → SIEM →
detection rule → alert.

## Key takeaway
Detection rules only evaluate events within their configured lookback
window — the first batch of simulated attacks predated the rule and
never triggered it. Rule design has to account for timing, not just logic.
