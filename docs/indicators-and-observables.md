# Indicators and Observables

These are lab-context observables extracted from the supplied report. They are not all independently validated indicators of compromise. Private IP addresses are retained as internal lab values and defanged in prose.

| Observable | Type | Context | Confidence / handling |
|---|---|---|---|
| `192.168.1[.]100` | Private IPv4 address | Source associated with scanning and subsequent crafted HTTP requests. | Observed in source report; not public attribution. |
| `192.168.1[.]120` | Private IPv4 address | Target WordPress host. | Lab target. |
| `health-check.php` | Filename / web path | Reported PHP web shell. | High-priority artifact to preserve and hash. |
| `X-HMG-Debug` | HTTP header name | Reported command channel; content described as ROT13 then Base64 encoded. | Strong behavioral observable; header presence alone is not malicious in every environment. |
| `PHPSESSID` values with prefixes `recon_`, `exploit_`, `ph2sh_`, `pwned_` | HTTP session identifier patterns | Reported in multipart POST activity. | Contextual lab observables; correlate with endpoint and request behavior. |
| Multipart POST to `/` and `/index.php` | HTTP behavior | Repeated requests reported at 15,848 bytes with PHP session warnings. | Behavioral indicator; validate raw request metadata. |
| `/dev/shm/.cache-helper` | File path | Reported intended hidden helper location. | Creation/execution unverified; preserve if present. |
| `*/5` cron schedule | Scheduled-task pattern | Reported recurring cron command. | Validate actual crontab content and execution. |
| `apache2 → sh -c` | Process relationship | Shell commands reportedly spawned by Apache. | Strong behavioral detection opportunity. |
| `system("id");` | Command string | Example decoded command described in the source report. | Use as contextual evidence, not a standalone IOC. |

## Handling guidance

- Hash recovered files with SHA-256 and record the collection time, source path, and acquisition method.
- Do not publish real secrets, password hashes, session tokens, or personal data.
- A filename or command pattern should be correlated with process ancestry, user identity, timestamps, and network activity.
- Do not treat Base64 content as proof of exfiltration. Look for destination, transfer protocol, byte counts, and matching outbound telemetry.
- Do not block private lab addresses in production based on this case study.
