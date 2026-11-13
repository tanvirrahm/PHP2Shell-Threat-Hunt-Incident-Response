# MITRE ATT&CK Mapping

This is a provisional mapping from the behavior described in the supplied report. Technique mappings should be revisited against raw telemetry and the current MITRE ATT&CK knowledge base before external publication or operational use.

| Tactic | Technique | Why it may apply | Evidence / confidence |
|---|---|---|---|
| Reconnaissance | T1595 — Active Scanning | Scanning activity attributed by the source report to Nmap. | F-01; reported confirmed, exact scan details should be validated. |
| Initial Access | T1190 — Exploit Public-Facing Application | WordPress/PHP-facing endpoint was reportedly targeted through crafted multipart requests. | F-02; plausible mapping, exact exploit path not proven. |
| Execution | T1505.003 — Server Software Component: Web Shell | A PHP web shell named `health-check.php` was reportedly used to execute commands. | F-03; reported confirmed. |
| Execution | T1059.004 — Unix Shell | Apache spawned shell commands as `www-data`. | F-04; reported confirmed. |
| Discovery | T1082 — System Information Discovery | Host/system discovery commands were described. | F-05; reported confirmed. |
| Discovery | T1016 — System Network Configuration Discovery | `ip addr`, neighbor, and listening-socket discovery were described. | F-05; reported confirmed. |
| Discovery | T1049 — System Network Connections Discovery | Listening/network connection discovery was described. | F-05; reported, validate exact command output. |
| Credential Access | T1003.008 — OS Credential Dumping: /etc/passwd and /etc/shadow | The report describes an attempt to read `/etc/shadow`; success is unverified. | F-06; attempted/uncertain. Do not claim credential dumping succeeded. |
| Credential Access | T1552.001 — Unsecured Credentials: Credentials In Files | WordPress configuration and database-related credentials were reportedly accessed. | F-06; reported activity, validate exact file/output. |
| Defense Evasion | T1562.001 — Impair Defenses: Disable or Modify Tools | Commands attempted to stop Sysmon, auditd, and Splunk UF and clear audit rules. | F-07; attempts reported; outcomes unverified except Sysmon stop reportedly failed. |
| Persistence | T1053.003 — Scheduled Task/Job: Cron | A cron entry was reportedly added to run a helper from `/dev/shm`. | F-08; command reported, successful installation/execution unverified. |
| Collection | T1005 — Data from Local System | WordPress user-table information was reportedly queried and staged. | F-06/F-09; collection activity reported; exact contents not attached. |
| Exfiltration | No confirmed technique assigned | Base64 encoding is not proof of transfer to an external destination. | F-09; successful exfiltration not established. |

## Mapping caveats

- A technique is not confirmed solely because a command string appears; validate execution and outcome.
- The PHP session-upload-progress theory is not a distinct ATT&CK technique by itself and should remain a root-cause hypothesis.
- Do not map successful exfiltration without evidence of an outbound channel, destination, and transferred data.
- Technique names and identifiers should be checked against the current ATT&CK release when this report is maintained.
