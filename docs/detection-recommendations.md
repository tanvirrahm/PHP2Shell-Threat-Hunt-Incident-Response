# Detection and Hunting Recommendations

> **Validation status:** These are detection hypotheses and analytic requirements derived from the supplied report. No executable KQL was supplied, and no query in this repository is represented as deployed, tested, or production-ready.

## D-01 — Web server spawning an interactive shell

**Objective:** Detect a web-server process launching a shell or unexpected interpreter.

**Logic:**
- Parent process is an Apache/PHP-FPM/Nginx worker or another web-server process.
- Child process is a shell such as `sh`, `bash`, or an unexpected interpreter.
- Prioritize command lines involving network discovery, credential files, service control, audit configuration, cron, or temporary directories.
- Enrich with account identity, process ancestry, file writes, and outbound network connections.

**Expected value:** High for identifying web-shell command execution.

**False-positive considerations:** Legitimate maintenance scripts, deployment jobs, and application health checks may spawn shells. Establish a baseline by host and service account.

## D-02 — Suspicious PHP webroot writes and web-shell requests

**Objective:** Identify a PHP process or web-server account creating executable server-side files in a web-accessible directory, and subsequent requests to those files.

**Logic:**
- Alert on creation or modification of `.php`, `.phtml`, or equivalent executable files in webroot by a web-server identity.
- Correlate file creation with HTTP requests to the new path.
- Flag unusual custom headers, especially when they carry encoded or obfuscated command-like content.
- Correlate multipart POST requests and unusual session identifiers with PHP session warnings and webroot file writes.

**Expected value:** High when file-integrity and web access telemetry are available.

**False-positive considerations:** CMS updates, plugin/theme installation, and deployment pipelines may legitimately write PHP files. Scope approved paths and deployment identities.

## D-03 — Web-server account attempting defense evasion or persistence

**Objective:** Detect a web-server identity modifying security controls or creating scheduled execution.

**Logic:**
- Alert when `www-data` or equivalent invokes `systemctl stop` against monitoring/security services.
- Alert on `auditctl -D` or other broad audit-rule modifications from an application service account.
- Alert on cron modification by a web-server identity.
- Alert on writes to executable temporary paths such as `/dev/shm` followed by scheduled execution.
- Correlate with process ancestry and recent web-shell or suspicious HTTP activity.

**Expected value:** High for detecting post-exploitation behavior.

**False-positive considerations:** Approved administrative scripts can change services or scheduled tasks. Restrict by host role, approved change window, and authorized parent process.

## Suggested telemetry

- Web access and error logs, including request method, URI, status, user agent, and selected headers.
- PHP-FPM/Apache process and file-integrity telemetry.
- Endpoint process creation with parent/child relationship and command line.
- File creation/modification events in webroot, temporary directories, and cron locations.
- Service-control events and audit-rule modification telemetry.
- DNS and outbound network connection metadata.
- WordPress authentication and database audit events where available.

## Validation plan

1. Reconstruct the original events from raw logs and document the exact fields available.
2. Implement the analytics in the target SIEM using the actual table and schema names.
3. Test against the incident time range and a benign baseline.
4. Measure false positives and confirm that expected events are retained.
5. Record query version, owner, test date, results, and deployment status.
6. Do not claim a detection caught the incident unless retrospective testing demonstrates that result.

## KQL status

No KQL query is included because the underlying table schemas and exact event fields were not supplied. Adding guessed table names or untested query syntax would create a misleading impression of a validated detection. Once the actual Sentinel/Defender schema and sample events are available, these analytics can be converted into executable KQL.
