# Attack Timeline

## Time-handling note

The source report states a hunt window of **2026-10-23 04:00–16:00 UTC**, but it also contains timestamps from different sources that have not been reconciled. Apache error-log times are presented separately from HTTP-enriched and Sysmon times. Do not assume all clocks use UTC or share the same offset.

## Timeline from supplied report

| Source time as reported | Source | Activity | Interpretation |
|---|---|---|---|
| 07:52:15–07:52:17 | HTTP enriched | Nmap-like scanning activity | Reconnaissance; source report associates it with `192.168.1.100`. |
| 07:53:35–07:57:00 | HTTP enriched | Multipart POST activity with session identifiers using prefixes such as `recon_`, `exploit_`, `ph2sh_`, and `pwned_` | Suspected exploit delivery. Exact request bodies and mechanism unavailable. |
| 02:18:05 onward | Apache error log | PHP session read/write warnings | Kept as Apache source time; timezone and clock alignment not established. |
| Not preserved | Apache access log | First request to `health-check.php` | The source report says the first request was not preserved. |
| 12:21:07–12:26:06 | Sysmon | Session 1: discovery, credential access, helper/cron commands, monitoring-stop attempts | Commands reportedly ran as `www-data`. |
| 14:01:23–14:19:10 | Sysmon | Session 2: repeated discovery/credential activity, audit-rule deletion attempt, monitoring-stop attempts, Base64 staging, cron command, attacker log review | Outcomes of several commands are unverified. |

## Sequence reconstruction

1. Scanning and web reconnaissance were observed.
2. Multipart POST requests containing attacker-selected session identifiers followed.
3. PHP session warnings were observed in Apache error telemetry.
4. A web shell named `health-check.php` was present and accepted an obfuscated command channel.
5. Apache spawned shell commands as `www-data`.
6. The operator performed host/network discovery and accessed WordPress-related material.
7. Monitoring interference and persistence commands were attempted.
8. WordPress-related material was Base64-encoded; successful transfer off-host was not established.

## Timeline gaps

- The first access-log event for `health-check.php` was not preserved.
- HTTP request bodies were unavailable, limiting exploit reconstruction.
- Source timezone and clock offsets have not been reconciled.
- The reported 14:13:55 telemetry concern needs validation against the original event set; the source report warns that telemetry may be incomplete after this point.
- The exact creation and execution times of the proposed helper and cron task are not established.

When raw logs are added, preserve original timestamps and add normalized UTC in a separate column only after documenting each source's timezone and clock offset.
