# Changelog

All notable changes to **ExchangeURLManager.ps1** are documented here.

## 1.0 - 2026-10-02

- Initial release of ExchangeURLManager.ps1.
- Added read-only Review mode for supported Exchange Server Client Access URLs, namespaces, authentication settings, and related configuration.
- Added parameter-based and Interactive Configure modes with Preview, confirmation, automatic pre-change backup, Apply, and post-change verification.
- Added live server-to-server Clone comparison with read-only behavior by default and explicit `-ApplyChanges` opt-in.
- Added JSON Backup and same-server Restore workflows with human-readable TXT companion output.
- Added support for OWA, ECP, EWS, MAPI, ActiveSync, OAB, Autodiscover virtual directory, Autodiscover SCP, Outlook Anywhere, and optional PowerShell URL handling.
- Added optional authentication handling with `-IncludeAuthentication`.
- Added optional PowerShell URL handling with `-IncludePowerShellUrls`.
- Added optional Outlook Anywhere SSL requirement handling with `-IncludeOutlookAnywhereSslRequirements`.
- Added source-server-specific value protection so source-specific URL, hostname, and SCP values remain Review Only during Clone.
- Added canonical Exchange server identity validation to block source/target alias collisions and duplicate targets.
- Added mixed-build Clone authentication protection so authentication settings remain Review Only when source and target Exchange builds differ.
- Added runtime validation of generated `Set-*` commands and required companion parameters against the current Exchange Management Shell before export or Apply.
- Added target refresh and drift detection before Apply; Apply is blocked when configuration changes after Preview.
- Added mandatory pre-change JSON backup creation before Apply begins.
- Added post-Apply verification with remaining-change reporting.
- Added namespace validation and PowerShell URL scheme preservation.
- Added controlled handling for unreadable targets, backup failures, Restore validation failures, and verification errors.
- Added built-in `-Help`, comment-based PowerShell help, command export with `-OutputFile`, and console paging.
- Validated version 1.0 in a live lab on Exchange build 15.2.1748.10 with Windows PowerShell 5.1, including successful Clone Apply and post-Apply verification with zero remaining changes.
- Mixed-build Clone authentication behavior was not runtime-validated.
