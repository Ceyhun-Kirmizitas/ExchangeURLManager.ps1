# Changelog

All notable changes to **ExchangeURLManager.ps1** are documented here.

## 1.1 - 2026-10-30

### Changes

- Added an Outlook Anywhere SSL check to prevent changes that Exchange would reject.
- Apply now returns exit code `1` when an operation is blocked or fails.

### Improvements

- Improved Clone handling of server-specific URLs and hostnames that already match the target server.
- Improved Apply and verification results to clearly show successful, failed, and remaining changes.

### Bug Fixes

- Fixed source and target configuration changes going undetected after Preview.

## 1.0 - 2026-10-02

### New Features

- Initial release with Review, Configure, Interactive Configure, Clone, Backup, and Restore modes.
- Added support for Exchange Client Access URLs and namespaces, including OWA, ECP, EWS, MAPI, ActiveSync, OAB, Autodiscover, and Outlook Anywhere.
- Added Configure and Interactive Configure with Preview, confirmation, automatic backup, Apply, and verification.
- Added server-to-server Clone, read-only by default. Changes require `-ApplyChanges`.
- Added JSON Backup and same-server Restore, with TXT backup reports.
- Added optional authentication, PowerShell URL, and Outlook Anywhere SSL settings.
- Added safety checks for server identities, Exchange command compatibility, configuration changes after Preview, and required backups.
- Added command and report export with `-OutputFile`, without changing Exchange settings.

### Important Notes

- Server-specific settings remain Review Only during Clone.
- Authentication settings are not automatically copied between different Exchange builds.
