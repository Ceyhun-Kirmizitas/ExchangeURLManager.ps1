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

## Validation

- **[OFFLINE]** The updated RC1 regression harness passed all **77/77 checks** on Windows PowerShell 5.1.17763.8385 (2026-10-30). Parser check P01 reported 0 errors, and S01 verified UTF-8 BOM, CRLF-only line endings, and an ASCII script body.
- **[LAB / LAB-APPLY]** A1 plus mandatory L1–L5 and optional O1–O4 test scenarios passed in Exchange LAB. The O4 failure scenario used a separate instrumented test copy to inject an error. Both test servers were restored to their initial backup state.
- A new Exchange LAB cycle is not planned for the release-only text and encoding changes.

## Important notes

- `-OutputFile` is non-destructive. Readiness BLOCKER messages appear in the console, not in the exported command TXT.
- `-Backup` manual backup failures require checking the per-server summary; nonzero exit codes are not guaranteed for that mode.
- Certificates, IIS bindings, DNS records, firewalls, load balancers, ASA credentials, SPNs, and Extended Protection configuration remain outside scope.
- Tested scenarios do not establish exhaustive coverage of every Exchange topology. Review the Preview and backups in your environment.

See [README.md](README.md) for examples, [CHANGELOG.md](CHANGELOG.md) for version history, and [LICENSE](LICENSE) for terms.
