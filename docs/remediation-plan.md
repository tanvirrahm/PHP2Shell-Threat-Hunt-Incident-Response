# Remediation and Recovery Plan

The source report states that containment, eradication, and recovery had not been performed. The actions below are recommendations, not completed response steps.

## Priority 0 — Preserve and contain

- Coordinate with the incident owner and preserve evidence before destructive changes, where safe.
- Isolate the affected host from untrusted networks while retaining a controlled forensic/management path if required.
- Capture volatile state where feasible: running processes, network connections, logged-on users, mounted filesystems, and memory.
- Preserve `health-check.php`, `/dev/shm/.cache-helper` if present, cron files, PHP session files, webroot metadata, and relevant logs.
- Acquire forensic copies and calculate SHA-256 hashes; document chain of custody.
- Preserve web access/error logs, PHP logs, Sysmon, auditd, Splunk UF, firewall, and network telemetry.

## Priority 1 — Rotate exposed secrets

- Rotate the WordPress database account password and any other secrets accessible to the compromised web-server identity.
- Rotate WordPress authentication keys and salts to invalidate existing authentication cookies.
- Review WordPress administrator accounts and recent account changes.
- Reset affected user passwords if password hashes were exposed; use a risk-based notification and response process.
- Review credentials reused on other systems and rotate them where necessary.
- Avoid placing secrets in command-line arguments, shell history, or logs.

## Priority 2 — Eradicate and rebuild

- Remove the web shell and unauthorized files only after preserving them for analysis.
- Inspect the complete webroot, plugin/theme directories, upload directories, temporary locations, cron configuration, systemd units, and startup locations for additional persistence.
- Validate whether `/dev/shm/.cache-helper` exists and whether any scheduled task references it.
- If integrity cannot be established, rebuild the host from a known-good image rather than relying on manual cleanup.
- Reinstall WordPress core, themes, and plugins from trusted sources; remove unsupported or unused components.

## Priority 3 — Harden

- Patch the operating system, PHP runtime, WordPress core, themes, and plugins.
- Review PHP session configuration and disable upload-progress functionality if it is not needed.
- Validate strict session handling and session identifier generation.
- Ensure the web-server identity cannot write executable PHP files into the application code directories.
- Separate user uploads from executable web content and disable script execution in upload paths.
- Apply least privilege to the web-server account; restrict cron and service-control permissions.
- Restrict outbound network access from the web tier to required destinations.
- Protect secrets using appropriate secret-management mechanisms and least-privilege database permissions.

## Priority 4 — Restore and monitor

- Restore service only from a verified clean state.
- Confirm logging and endpoint monitoring are healthy and centrally reporting.
- Validate audit rules and alert on attempts to disable or modify security tooling.
- Run the detection tests in [detection recommendations](detection-recommendations.md).
- Monitor for renewed web-shell requests, suspicious PHP writes, web-server child shells, unexpected cron activity, and unusual outbound connections.
- Document recovery validation and obtain incident-owner sign-off.

## Closure criteria

Do not close the incident until:
- The host is contained and its integrity is restored or it has been rebuilt.
- Potentially exposed credentials and WordPress authentication secrets are rotated.
- Persistence locations have been inspected and addressed.
- Monitoring and audit telemetry are validated.
- Lateral movement and data-transfer scope have been investigated to a defensible conclusion.
- Any required privacy, legal, or regulatory assessment is completed by the responsible organization.
