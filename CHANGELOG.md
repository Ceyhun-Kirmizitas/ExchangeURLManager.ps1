# Changelog

All notable changes to **ExchangeURLManager.ps1** are documented here.

## 1.1 - 2026-10-30

### Changes

- Return exit code `1` for BLOCKER, required pre-change backup failure, drift, Apply failure, and verification failure. Completed command exports remain non-destructive and return `0`, including when readiness BLOCKERs are displayed.
- Block automatic Apply if a planned `Set-OutlookAnywhere` operation would be rejected because SSLOffloading is False and a configured client hostname side does not require SSL. The script does not change SSLOffloading.

### Improvements

- Identify server-specific internal URL, Autodiscover SCP, and Outlook Anywhere InternalHostname values that already match the target's own server name as **In sync (target-specific)**, without generating Set-* commands.
- Show the resulting Outlook Anywhere client SSL requirement values in Preview.
- Report Apply results per command (`Applied`, `Failed`, `Not run`) and separate planned-change verification from Review Only differences.
- Label Review Only values as `Reference` in Preview instead of `New`.
- Simplify console messages, built-in Help, examples, and the feedback footer.

### Bug Fixes

- Detect target configuration changes after Preview even when the generated Set-* command text would remain unchanged.
- Re-read the Clone source before Apply and block when source values used by the plan have changed.

## 1.0 - 2026-10-02

- Initial release with Review, Configure, Interactive Configure, Clone, Backup, and Restore modes.
- Added supported Exchange Client Access URL/namespace configuration, selected optional authentication and PowerShell URL settings, and migration-sensitive Review Only settings.
- Added JSON backups with TXT companion files and same-server Restore.
- Added default read-only Clone comparison and explicit `-ApplyChanges`.
- Added Preview, confirmation, mandatory pre-change backup, target drift protection, command validation, and post-Apply verification.
- Added built-in Help, full PowerShell help, and command/report export with `-OutputFile`.
- Version 1.0 was validated in an Exchange 2019 LAB on build 15.2.1748.10 with Windows PowerShell 5.1; mixed-build Clone authentication was not runtime-validated.
