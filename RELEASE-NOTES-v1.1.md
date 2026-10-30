# ExchangeURLManager.ps1 v1.1 — Release Notes

**Release date:** 2026-10-30  
**Platform:** Exchange Server 2016, Exchange Server 2019, and Exchange Server Subscription Edition; Windows PowerShell 5.1.

Version 1.1 focuses on safer Apply behavior and clearer Clone reporting. It retains the existing Review, Configure, Interactive Configure, Clone, Backup, and Restore workflows.

## What's changed

- **Outlook Anywhere SSL safety:** Automatic Apply is blocked when a planned `Set-OutlookAnywhere` change would produce SSL requirement settings rejected by Exchange. The check considers the resulting hostnames and client SSL requirement values for each side; it does not change SSLOffloading.
- **Target-specific Clone matches:** Internal URLs, Autodiscover SCP, and Outlook Anywhere InternalHostname values can show `In sync (target-specific)` when the target already uses its own name correctly. No Set-* command is generated for those values.
- **Stronger drift protection:** Both the target configuration and the live Clone source are checked again before Apply. Changes made after Preview block Apply.
- **Better status reporting:** Each attempted Set-* operation is reported as `Applied`, `Failed`, or `Not run`; verification distinguishes planned changes from Review Only differences.
- **Exit codes:** BLOCKER/Apply/verification and required pre-change backup failures exit `1`; cancellation, completed read-only operations, and exports return `0`.
- **Usability:** Clearer Help, console summaries, and Preview labels (`Reference` for Review Only values).

## Important notes

- `-OutputFile` is non-destructive. Readiness BLOCKER messages appear in the console, not in the exported command TXT.
- `-Backup` manual backup failures require checking the per-server summary; nonzero exit codes are not guaranteed for that mode.
- Certificates, IIS bindings, DNS records, firewalls, load balancers, ASA credentials, SPNs, and Extended Protection configuration remain outside scope.
- Exchange environments vary. Review the Preview and backups in your environment.

See [README.md](README.md) for examples, [CHANGELOG.md](CHANGELOG.md) for version history, and [LICENSE](LICENSE) for terms.
