# Evidence Register

## Important limitation

This repository was prepared from a written incident-report narrative. The underlying raw logs, packet captures, screenshots, forensic images, and original query files were not included in the material supplied for publication. The evidence IDs below are references used in that narrative; they are not files present in this repository.

Do not interpret this index as proof that the artifacts have been independently reviewed here. Add sanitized source artifacts only when they are available and approved for publication.

## Referenced evidence

| Reference | Source described in report | Purpose | Availability in this repository |
|---|---|---|---|
| E-01 | HTTP-enriched telemetry | Reconnaissance and request-pattern analysis | Not attached |
| E-02 | HTTP-enriched telemetry / multipart request metadata | Crafted POST requests and PHP session identifier patterns | Not attached |
| E-03 | Apache/PHP error and web telemetry | PHP session warnings and request correlation | Not attached |
| E-04 | Sysmon/process telemetry | Command execution, discovery, credential access, defense-evasion and persistence attempts | Not attached |
| E-05 | Web-shell artifact or related telemetry | `health-check.php` observation | Not attached |
| Q-06 | Decoding/analysis reference mentioned in source report | Interpretation of the obfuscated `X-HMG-Debug` channel | Original query or procedure not attached |

## Evidence acquisition checklist

When the source artifacts are available, record:

- Evidence ID and original filename.
- Source host and collection method.
- Collector and collection timestamp, including timezone.
- Original timestamp field and any known clock offset.
- SHA-256 hash and file size.
- Redactions applied and reason.
- Relevant event IDs, record IDs, or line ranges.
- Query or command used to extract the evidence.
- Chain-of-custody and storage location.

## Recommended artifacts to add

- Redacted Apache access and error logs covering the hunt window.
- Relevant HTTP-enriched events, including request metadata and safe, sanitized header values.
- Sysmon process-creation events and relevant file/network events.
- auditd and Splunk Universal Forwarder service/audit-rule telemetry.
- Preserved copy of `health-check.php` and any recovered helper file, stored safely as inert evidence.
- Sanitized crontab snapshot and relevant PHP session/configuration artifacts.
- Original hunt queries and query results.

## Safety and privacy

Never commit active credentials, password hashes, session cookies, private keys, real personal information, or unredacted patient data. Treat suspicious scripts as malware: do not execute them on a normal workstation. Use a controlled analysis environment and follow the organization's evidence-handling process.
