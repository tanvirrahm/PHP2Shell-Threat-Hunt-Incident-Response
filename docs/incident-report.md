# Incident Report: WordPress Web-Shell Compromise

## 1. Executive summary

A threat hunt of the lab WordPress server `debian13` (`192.168.1.120`) identified a web-shell compromise and remote command execution in the Apache web-server context `www-data`. The observed activity included reconnaissance, crafted multipart upload requests with attacker-selected PHP session identifiers, a web shell named `health-check.php`, host discovery, access to WordPress-related database material, attempts to interfere with monitoring, and commands intended to establish persistence.

The supplied report assesses that PHP session upload-progress behavior may have been abused to achieve code execution. The HTTP request bodies and a complete exploit trace were not available, so this precise mechanism remains a hypothesis rather than a proven root cause.

The incident was classified High. The host should be treated as compromised. The report states that containment, eradication, and recovery had not been performed at the time of analysis.

## 2. Incident details

| Attribute | Assessment |
|---|---|
| Incident date | 2026-10-23 |
| Hunt window stated in source report | 04:00–16:00 UTC |
| Host | `debian13` |
| Target address | `192.168.1.120` |
| Observed source address | `192.168.1.100` |
| Application | WordPress |
| Database | `wordpress` |
| Database account named in report | `wpuser` |
| Execution identity | `www-data` |
| Severity | High |
| Status | Open in source report |
| Analyst | [Analyst name] |

The source address is a private RFC1918 address in the lab context. It does not establish an external actor's identity or geographic origin.

## 3. Scope and impact

### Confirmed or reported activity

- Web-shell artifact `health-check.php` was observed.
- Commands were issued through the `X-HMG-Debug` HTTP header; the report describes ROT13 followed by Base64 decoding.
- Apache child processes launched shell commands as `www-data`.
- Two command sessions were identified in the supplied analysis.
- Host/network discovery and security-tool enumeration occurred.
- WordPress database configuration and user-table access were reported.
- Commands attempted to stop monitoring services and clear audit rules.
- Commands attempted to stage a hidden helper at `/dev/shm/.cache-helper` and add a recurring cron entry.

### Impact not fully established

- The report describes access to WordPress user records, including usernames, email addresses, registration dates, and password hashes. The actual raw records are not included in the material supplied for this repository.
- Base64 encoding indicates staging or transformation, not proof of successful off-host transfer.
- The outcome of attempts to read `/etc/shadow` and SSH keys is unverified; the source report assesses that shadow-file access likely failed.
- The outcome of the cron modification and execution of the helper are unverified.
- No root-level compromise or lateral movement was established.
- Whether real patient information or other regulated data existed in the lab database is unknown.

## 4. Attack narrative

### Reconnaissance

HTTP-enriched telemetry reportedly showed scanning activity from `192.168.1.100`, attributed in the source report to Nmap scripting-engine activity. The report records 1,419 requests and 102 distinct URIs; those counts should be treated as source-report figures until validated against the raw event set.

### Suspected initial access

The source report describes multipart POST requests to `/` and `/index.php` with attacker-selected `PHPSESSID` values. Repeated requests of 15,848 bytes and PHP session read/write warnings were considered consistent with abuse of PHP session upload-progress handling.

The request bodies and complete PHP-side execution path were not available. Accordingly, the exact exploit mechanism and the transition from session manipulation to web-shell creation are assessed, not directly demonstrated.

### Web shell and command execution

A web shell named `health-check.php` was observed. The supplied report describes commands delivered through an obfuscated `X-HMG-Debug` header, decoded using ROT13 and Base64. Apache spawned shell processes, and the commands ran as `www-data`.

### Post-compromise activity

The command history described in the source report included:
- Host, network, process, account, and listening-port discovery.
- Access to WordPress configuration and database-related material.
- Queries against the `wp_users` table.
- Attempts to stop Sysmon, auditd, and the Splunk Universal Forwarder.
- An attempt to clear audit rules using `auditctl -D`.
- Commands intended to create `/dev/shm/.cache-helper` and schedule it through cron.
- Base64 encoding of WordPress-related material.

The available narrative does not prove successful exfiltration, successful persistence, or successful shutdown of all monitoring components.

## 5. Root-cause assessment

**Working hypothesis:** PHP session upload-progress handling may have been abused through multipart requests and attacker-controlled session identifiers, ultimately contributing to server-side code execution.

**Confidence:** Moderate-to-low for the exact exploit mechanism; high for the observed web-shell execution and compromise.

Validation still required:
1. PHP version and effective session configuration at the time of the incident.
2. Whether `session.upload_progress.enabled` was active and how session identifiers were validated.
3. Relevant PHP session files and file-system timestamps.
4. WordPress/PHP endpoint behavior and any vulnerable plugin/theme or custom code.
5. The complete request/response and web-server error/access logs.
6. File metadata and content for `health-check.php`.

PHP upload-progress being enabled by itself is not sufficient to prove this specific RCE chain.

## 6. Contributing conditions (reported or to validate)

- PHP upload-progress behavior may have been exposed to attacker-controlled session identifiers.
- The web-server account could apparently write a PHP file into the webroot; verify directory ownership and permissions.
- The report describes `/dev/shm` as a staging location and cron use by `www-data`; validate actual permissions and resulting artifacts.
- Database credentials were stored in WordPress configuration and reportedly passed to a command; verify command-line exposure and process telemetry.
- Monitoring services and audit rules were targeted, increasing the risk of telemetry gaps.

## 7. Response status

| Response phase | Status in supplied report |
|---|---|
| Hunting and log review | Completed |
| Attack reconstruction | Completed as an assessment |
| IOC and ATT&CK mapping | Drafted |
| Detection engineering | Recommendations drafted; not deployed or tested |
| Evidence preservation | Not started |
| Containment | Not started |
| Eradication | Not started |
| Recovery | Not started |

## 8. Priority actions

1. Preserve volatile and persistent evidence before rebooting or deleting artifacts, where operationally safe.
2. Isolate the affected host from untrusted networks.
3. Preserve `health-check.php`, the reported `/dev/shm/.cache-helper`, cron configuration, process state, relevant PHP session files, and web/server logs.
4. Rotate the WordPress database password and any other secrets that may have been exposed.
5. Rotate WordPress authentication keys and salts; invalidate sessions.
6. Investigate all WordPress accounts and reset credentials where appropriate.
7. Rebuild from a known-good image if integrity cannot be established.
8. Patch and harden PHP, WordPress core, themes, and plugins; restrict webroot write access.
9. Validate audit and endpoint telemetry before returning the system to service.

See the [remediation plan](remediation-plan.md) for detailed actions.

## 9. Residual risk

Until containment and credential rotation are completed, assume the attacker may retain access to the host and know any secrets accessible to the compromised service account. Monitoring coverage may be incomplete because monitoring services and audit rules were targeted. Lateral movement, successful external data transfer, and the execution of the proposed cron persistence remain unresolved.

## 10. Conclusion

The supplied evidence supports a confirmed WordPress web-shell compromise and command execution as `www-data`. Credential-access activity and attempts at defense evasion and persistence were observed in the source analysis. The exact initial exploit path, successful exfiltration, and several command outcomes remain unverified. The incident should remain open until containment, eradication, recovery, and evidence-based validation are complete.
