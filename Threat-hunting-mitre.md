# IOC-Based Threat Hunting with MITRE ATT&CK Mapping

## What this is
Given a suspicious external IP and a file hash as IOCs, hunted for
matching activity in Wazuh's Threat Hunting module and produced a
risk-rated containment recommendation.

## IOCs investigated
- IP: `45.83.23.55`
- File hash: `e99a18c428cb38d5f260853678922e03`

## What I did
1. Searched both IOCs in Wazuh's Threat Hunting module (DQL, last 24h).
2. Found 2 alerts tied to the IP, 0 at critical severity (level 12+),
   1 auth failure, 0 auth successes.
3. Mapped the matched alerts to MITRE ATT&CK techniques: Password
   Guessing, SSH, and Sudo/Sudo Caching.
4. Found zero hits for the file hash — not observed executing or
   written to disk on the monitored endpoint.
5. Assessed overall risk and wrote a containment recommendation.

## Mini threat summary
| Field | Details |
|---|---|
| IOC Type | External IP + file hash |
| Observed Activity | SSH password-guessing, sudo/sudo-caching, 1 auth failure, 0 successes |
| Hash Status | Not observed anywhere in the environment |
| Risk Level | Low–Medium (max alert level 5, no criticals, no successful login) |
| Recommendation | Permanently block the IP, lower the active-response threshold for repeated SSH failures, keep monitoring for the hash |

## Key takeaway
Not every IOC hit means compromise — the alert levels and lack of
successful auth were what justified a "low-medium, monitor and block"
call instead of a full incident escalation.

## Screenshots
[screenshot: threat hunting search results]
<img width="875" height="436" alt="image" src="https://github.com/user-attachments/assets/504c9ff1-ef49-45c6-8056-539813773f77" />
