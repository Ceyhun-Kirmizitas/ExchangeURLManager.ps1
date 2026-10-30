# ExchangeURLManager.ps1

PowerShell tool for reviewing, configuring, backing up, cloning, and restoring supported **Exchange Server Client Access URLs and namespaces**.

It is intended for side-by-side deployments, upgrades, migrations, server replacement, and day-to-day configuration review. Version 1.1 is intended for Exchange Server 2016, Exchange Server 2019, and Exchange Server Subscription Edition.

## Download

- [ExchangeURLManager.ps1](ExchangeURLManager.ps1)
- [Raw script](https://raw.githubusercontent.com/Ceyhun-Kirmizitas/ExchangeURLManager.ps1/main/ExchangeURLManager.ps1)

Run it in Windows PowerShell 5.1 with Exchange Management Shell available.

## Modes

**Review** (`-Review`) reads supported configuration on one or more servers without making changes. Use `-IncludeAuthentication` to display authentication details.

**Configure** (default) prepares URL and namespace values, shows a Preview, requires confirmation, creates fresh pre-change JSON backups, applies changes, and verifies the results.

**Interactive Configure** (`-Interactive`) offers Common namespace, Select components, and Advanced per-component configuration. It uses the same safety and Apply workflow as Configure.

**Clone** (`-SourceServer`, `-TargetServer`) compares a live source to one or more targets. It is read-only unless `-ApplyChanges` is specified. Source-specific internal URLs, Autodiscover SCP, and Outlook Anywhere InternalHostname are shown as **In sync (target-specific)** if the target already uses its own server name in the same place; no command is generated for those settings.

**Backup** (`-Backup`) creates a versioned JSON file and a readable TXT companion per server. The JSON is the Restore artifact.

**Restore** (`-Restore`, `-BackupFile`) restores settings to the **same** server. Use Clone for server-to-server work.

## Supported settings

- OWA, ECP, EWS, MAPI, ActiveSync, and OAB: InternalUrl and ExternalUrl.
- Autodiscover SCP: AutoDiscoverServiceInternalUri.
- Outlook Anywhere (RPC over HTTP): InternalHostname and ExternalHostname.
- Optional with explicit switches: selected authentication settings, PowerShell InternalUrl/ExternalUrl, and Outlook Anywhere client SSL requirement values.
- **Review Only**: ASA/Kerberos status, EWS MRSProxyEnabled, Outlook Anywhere SSLOffloading, PowerShell authentication and RequireSSL, and Extended Protection.

Outlook Anywhere settings concern the legacy RPC over HTTP endpoint, not MAPI over HTTP. The script does **not** configure certificates, IIS bindings, DNS records, firewall/NAT, load balancers, SPNs, ASA deployment, or Extended Protection.

## Safety behavior

- Review, Backup, and Clone comparison do not change Exchange configuration. Clone Apply requires `-ApplyChanges`.
- Configure, Interactive Configure, Restore, and Clone Apply use **Preview -> confirmation -> fresh pre-change JSON backup -> target/source drift check -> Apply -> verification**. All required pre-change backups must succeed before Apply starts.
- Generated `Set-*` cmdlets and required parameters are checked against the current Exchange Management Shell before Apply.
- Unresolvable proposed EWS URL hostnames block automatic Apply.
- When `Set-OutlookAnywhere` is planned, Apply is blocked if its resulting SSL requirement configuration would be rejected by Exchange (SSLOffloading is False while a side with a configured hostname does not require SSL). SSLOffloading itself is not changed by this script.
- A change to a planned or accompanying target value after Preview blocks Apply. Clone also re-reads the source values used by the plan.
- Within each target, Apply stops after the first failed command; the remaining operations are shown as `Not run`. Verification reports planned changes separately from Review Only differences.
- With `-OutputFile`, the script generates a command or report file without applying configuration. Readiness BLOCKERs appear in the console; the command TXT does not include those BLOCKER messages.

**Exit codes:** `0` for completed operations, cancellation, and completed exports (including exports that display readiness BLOCKERs); `1` for an Apply BLOCKER, mandatory pre-change backup failure, drift, Apply failure, or verification failure. For manual `-Backup`, review the per-server backup summary: an unsuccessful manual backup does not currently guarantee a nonzero exit code.

## Parameters

| Parameter | Purpose |
|---|---|
| `-Review` | Read-only review. Requires `-Server`. |
| `-Backup` | JSON/TXT backup. Requires `-Server`. |
| `-Restore` / `-BackupFile` | Same-server Restore from JSON. |
| `-Interactive` | Guided Configure. |
| `-Server` | One or more Exchange servers in Review, Backup, or Configure. |
| `-SourceServer` / `-TargetServer` | Clone source and one or more targets. |
| `-CompareOnly` | Explicit read-only Clone comparison; cannot combine with `-ApplyChanges`. |
| `-ApplyChanges` | Enables Clone Apply; without it, Clone only compares. |
| `-BackupPath` | Alternative backup location. |
| `-InternalNamespace` / `-ExternalNamespace` | Configure namespace values. |
| `-AutodiscoverSCPNamespace` | Configure Autodiscover SCP host. |
| `-ClearExternalUrls` | Clear supported ExternalUrl values and OA ExternalHostname; exclusive with `-ExternalNamespace`. |
| `-IncludeAuthentication` | Selected authentication settings in Clone/Restore; show authentication in Review. |
| `-IncludePowerShellUrls` | Opt in to PowerShell InternalUrl/ExternalUrl. |
| `-IncludeOutlookAnywhereSslRequirements` | Opt in to OA client SSL requirement values in Clone/Restore. |
| `-OutlookAnywhereInternalClientsRequireSsl` / `-OutlookAnywhereExternalClientsRequireSsl` | Configure client SSL requirement values. |
| `-OutlookAnywhereDefaultAuthenticationMethod` | Configure OA default authentication (Basic, Ntlm, Negotiate). |
| `-OutputFile` | Export command/report file without changing Exchange. |
| `-Help` | Built-in usage guide. |

## Examples

Read-only review:

```powershell
.\ExchangeURLManager.ps1 -Review -Server EX01,EX02
```

Configure namespaces:

```powershell
.\ExchangeURLManager.ps1 -Server EX01,EX02 -InternalNamespace mail.contoso.com -ExternalNamespace mail.contoso.com -AutodiscoverSCPNamespace autodiscover.contoso.com
```

Guided Configure:

```powershell
.\ExchangeURLManager.ps1 -Interactive
```

Compare source and targets (read-only):

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02,EX03
```

Clone Apply after Preview and confirmation:

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02,EX03 -ApplyChanges
```

Create backups:

```powershell
.\ExchangeURLManager.ps1 -Backup -Server EX01,EX02
```

Restore to the server named in the JSON backup:

```powershell
.\ExchangeURLManager.ps1 -Restore -BackupFile C:\Temp\ExchangeURLManager-EX01-Manual.json
```

Export Clone commands without applying:

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02 -ApplyChanges -OutputFile C:\Temp\ExchangeURL-Commands.txt
```

Built-in help and full PowerShell help:

```powershell
.\ExchangeURLManager.ps1 -Help
Get-Help .\ExchangeURLManager.ps1 -Full
```

## Requirements

- Exchange Server Mailbox environment, Exchange Management Shell, appropriate Exchange administrative permissions.
- Windows PowerShell 5.1.

Review the Preview and backup files before making production changes. Use at your own risk; see the script's caution and [LICENSE](LICENSE).

## Changelog

See [CHANGELOG.md](CHANGELOG.md) and [release notes](RELEASE-NOTES-v1.1.md).

## Feedback and issues

[GitHub Issues](https://github.com/Ceyhun-Kirmizitas/ExchangeURLManager.ps1/issues) | [Website](https://ceyhunkirmizitas.net/)

## License

MIT. See [LICENSE](LICENSE).
