# PHP2Shell Threat Hunt & Incident Response

A lab-based SOC threat-hunting case study documenting a suspected PHP session-upload-progress abuse chain, a confirmed WordPress web-shell compromise, command execution as `www-data`, credential-access activity, defense-evasion attempts, and persistence attempts.

> **Assessment:** Web application compromise and remote command execution are confirmed by the supplied incident report. The precise initial exploit mechanism is assessed, not conclusively demonstrated. Successful outbound exfiltration, persistence execution, and several defense-evasion outcomes remain unverified.

## Case summary

| Field | Value |
|---|---|
| Environment | CyberRange / lab scenario (real-world data exposure not verified) |
| Affected host | `debian13` — `192.168.1.120` |
| Application | WordPress |
| Observed source | `192.168.1.100` (private lab address; attribution unknown) |
| Hunt window | 2026-10-23, 04:00–16:00 UTC (source clock alignment not independently validated) |
| Severity | High |
| Status | Open in the supplied report; containment and recovery were not performed |
| Execution context | `www-data` |
| Primary artifact | `health-check.php` web shell |

## What happened

The investigation identified reconnaissance traffic, crafted multipart POST requests using attacker-selected PHP session identifiers, PHP session warnings, and subsequent execution through Apache child processes. A web shell named `health-check.php` accepted commands through an obfuscated `X-HMG-Debug` header. Commands ran as `www-data`.

The activity included host and network discovery, access to WordPress database-related material, querying the `wp_users` table, attempts to interfere with security monitoring, and commands intended to create a hidden helper under `/dev/shm` and schedule it through cron.

The report describes Base64 encoding of collected material, but does not establish that the material was successfully transferred off-host.

## Key findings

- Confirmed: web-shell presence and command execution as `www-data`.
- Confirmed: discovery and credential-access commands were issued.
- Confirmed: WordPress user records were queried and encoded for staging, according to the source report.
- Attempted: stopping Sysmon, auditd, and the Splunk Universal Forwarder; deleting audit rules.
- Reported: Sysmon stop attempt failed; other monitoring outcomes are unverified.
- Attempted: create `/dev/shm/.cache-helper` and a recurring cron entry; successful installation and execution are unverified.
- Not established: root compromise, lateral movement, or successful external exfiltration.

## Repository guide

- [Incident report](docs/incident-report.md)
- [Findings register](docs/findings-register.md)
- [Attack timeline](docs/attack-timeline.md)
- [MITRE ATT&CK mapping](docs/mitre-attack-mapping.md)
- [Indicators and observables](docs/indicators-and-observables.md)
- [Detection and hunting recommendations](docs/detection-recommendations.md)
- [Remediation plan](docs/remediation-plan.md)
- [Evidence register and limitations](evidence/README.md)

## Method and evidence limitations

This repository is based on the supplied incident-report narrative, not on independently uploaded raw logs, packet captures, screenshots, or forensic images. The evidence register identifies the evidence references described by that report; it does not imply the underlying artifacts are included here. No fabricated log entries, hashes, packet contents, or executable KQL detections have been added.

Times from different telemetry sources are kept distinct because source timezone and clock offsets were not reconciled. The exact transition from PHP session handling to web-shell creation is an assessed hypothesis: HTTP request bodies and the complete exploit chain were not available in the material provided.

## Responsible disclosure and data handling

This is presented as a lab investigation. The private IP addresses are retained as lab observables and are not evidence of public-internet attribution. The scenario references a healthcare website, but the presence of real patient information or regulated health data has not been verified. Do not publish real credentials, password hashes, personal data, tokens, or unredacted production logs.

## Analyst

Analyst name intentionally left as a fill-in field until confirmed for publication.

## Status

Analysis and recommendations documented. Containment, eradication, recovery, and validation of detections were not performed in the supplied report.
