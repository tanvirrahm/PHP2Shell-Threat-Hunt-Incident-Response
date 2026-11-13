# Findings Register

Confidence describes the strength of the conclusion in the supplied report, not an independent re-analysis of raw logs.

| ID | Finding | Evidence reference in source report | Status / confidence | Notes |
|---|---|---|---|---|
| F-01 | Reconnaissance/scanning against the WordPress host from `192.168.1.100` | E-01, E-03 | Reported as confirmed | Source report attributes 1,419 requests and 102 URIs to Nmap scripting-engine activity; validate counts and attribution against raw events. |
| F-02 | Crafted multipart POST requests with attacker-selected `PHPSESSID` values | E-02, E-03 | Delivery reported confirmed; exploit mechanism assessed | PHP session read/write warnings reportedly coincided. Request bodies and exact exploit chain are unavailable. |
| F-03 | Web shell `health-check.php` and obfuscated `X-HMG-Debug` command channel | E-05; Q-06 | Reported as confirmed | ROT13 and Base64 decoding described. Preserve the file and obtain its hash/content before removal. |
| F-04 | Apache spawned shell commands as `www-data` in two sessions | E-04 | Reported as confirmed | Supports command execution in the web-server context. |
| F-05 | Host, network, process, account, and security-tool discovery | E-04 | Reported as confirmed | Commands included `ip addr show`, `ip neigh`, and `ss -tlnp`. |
| F-06 | WordPress configuration and user-table access; attempts to read other sensitive files | E-04 | Access activity reported; scope partly unverified | User-table query and encoded output described. Success of `/etc/shadow` and SSH-key reads is unverified. |
| F-07 | Attempts to stop monitoring services and clear audit rules | E-04 | Attempts confirmed in report; outcomes mixed/unverified | Sysmon stop reportedly failed. auditd/Splunk outcomes and `auditctl -D` effect are unverified. |
| F-08 | Commands to create `/dev/shm/.cache-helper` and add a cron entry | E-04 | Attempt reported; installation/execution unverified | Do not describe persistence as established until artifacts and execution are validated. |
| F-09 | WordPress-related material Base64-encoded | E-04 | Staging reported; exfiltration unproven | Encoding alone does not establish outbound transfer. |
| F-10 | Lateral movement | E-04 | Undetermined | Absence of identified evidence is not proof that lateral movement did not occur. |

## Analyst interpretation

The strongest supported conclusion is compromise of the web application and command execution as `www-data`. The report's exact PHP session-upload-progress exploit explanation is a hypothesis requiring corroboration. The report also describes data-access and staging activity, but no complete evidence of successful exfiltration was supplied.

## Evidence follow-up

For each finding, attach the original event identifiers, timestamps, source log, and a redacted excerpt when available. Record the exact query used to retrieve it. Do not manufacture event IDs or log excerpts to fill gaps.
