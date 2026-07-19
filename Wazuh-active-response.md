# Automated Firewall Blocking via Wazuh Active Response

## What this is
Configured Wazuh's Active Response module to automatically block
brute-force SSH attackers at the firewall level — no manual analyst
action required.

## What I did
1. Enabled `firewall-drop` active-response in `ossec.conf` on the
   monitored Kali endpoint.
2. Tied it to three rule IDs: the built-in brute-force rules (5710,
   5716) plus my custom foreign-IP rule (100200), with a 600-second
   auto-expiring block.

\`\`\`xml
<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <rules_id>5710,5716,100200</rules_id>
  <timeout>600</timeout>
</active-response>
\`\`\`

3. Verified the agent and SSH service were both active on the endpoint.
4. Generated real failed SSH login attempts to trigger the rules.
5. Confirmed the block took effect via `iptables -L -n -v` — DROP
   rules appeared in the INPUT/FORWARD chains, with matched packets
   incrementing against the blocked source IP.

## Result
Offending IP was automatically dropped at the firewall within seconds
of crossing the failure threshold, and auto-unblocked after 10 minutes
to avoid permanent lockouts from false positives.

## Key takeaway
This is the difference between detection and containment — most
tutorials stop at "alert fires," this closes the loop by actually
acting on it automatically.

## Screenshots
[screenshot: iptables output]
<img width="813" height="648" alt="image" src="https://github.com/user-attachments/assets/19e4b601-e689-40e7-ac2e-39f859f12c44" />

[screenshot: agent status]
<img width="813" height="283" alt="image" src="https://github.com/user-attachments/assets/77bac6c1-2e20-48ad-9a4a-8d4c42d2059f" />

