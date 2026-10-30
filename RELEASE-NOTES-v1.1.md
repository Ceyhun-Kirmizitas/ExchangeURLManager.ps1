# ExchangeURLManager.ps1 v1.1 — Release Notes

**Release date:** 2026-10-30  
**Compatibility:** Intended for Exchange Server 2016, Exchange Server 2019, and Exchange Server Subscription Edition. Windows PowerShell 5.1.

Version 1.1 improves Apply safety, Clone comparisons, and verification reporting.

## What's changed

- Added an Outlook Anywhere SSL check to prevent changes that Exchange would reject.
- Apply now returns exit code `1` when an operation is blocked or fails.
- Improved Clone handling of server-specific URLs and hostnames that already match the target server.
- Improved Apply and verification results to clearly show successful, failed, and remaining changes.
- Fixed source and target configuration changes going undetected after Preview.

## Notes

- Clone is read-only unless `-ApplyChanges` is specified.
- `-OutputFile` exports commands or reports without changing Exchange settings.
- Review the Preview and backups before applying changes.

See [README.md](README.md) for usage examples and [CHANGELOG.md](CHANGELOG.md) for version history.
