# ExchangeURLManager.ps1

PowerShell tool for reviewing, configuring, backing up, cloning, and restoring supported **Exchange Server Client Access URL and namespace** configuration.

The script is designed for Exchange Server side-by-side deployments, upgrades, migrations, replacement-server work, and day-to-day Client Access configuration review.

Intended for Exchange Server 2016, Exchange Server 2019, and Exchange Server Subscription Edition.

## Download

- [ExchangeURLManager.ps1](ExchangeURLManager.ps1)
- [Raw download](https://raw.githubusercontent.com/Ceyhun-Kirmizitas/ExchangeURLManager.ps1/main/ExchangeURLManager.ps1)

## Modes

### Review

Reads supported Client Access configuration from one or more Exchange Mailbox servers.

Review is read-only. Authentication details are hidden by default and can be displayed with `-IncludeAuthentication`.

### Configure

Builds a desired Client Access URL/namespace configuration for one or more Exchange servers.

The script shows a Preview, asks for confirmation, creates a fresh pre-change JSON backup, applies the supported changes, and verifies the result.

### Interactive Configure

Guided configuration mode for common namespace, selected-component, or per-component configuration.

Interactive Configure uses the same planning, backup, Apply, and verification engine as parameter-based Configure.

### Clone

Uses a live Exchange server as the source and compares supported settings with one or more target servers.

Clone is read-only by default. Use `-ApplyChanges` explicitly to enter the Apply path.

### Backup

Creates one versioned JSON backup per server plus a human-readable TXT companion.

JSON is the Restore artifact.

### Restore

Builds a same-server Restore plan from a JSON backup created by this script.

Cross-server Restore is blocked. Use Clone for server-to-server migration.

## Managed configuration

The supported scope includes:

- OWA InternalUrl / ExternalUrl
- ECP InternalUrl / ExternalUrl
- EWS InternalUrl / ExternalUrl
- MAPI InternalUrl / ExternalUrl
- ActiveSync InternalUrl / ExternalUrl
- OAB InternalUrl / ExternalUrl
- Autodiscover virtual directory InternalUrl / ExternalUrl
- Autodiscover SCP
- Outlook Anywhere InternalHostname / ExternalHostname
- Selected authentication settings when explicitly requested
- PowerShell InternalUrl / ExternalUrl when explicitly requested
- Outlook Anywhere InternalClientsRequireSsl / ExternalClientsRequireSsl when explicitly requested

Migration-sensitive settings such as ASA/Kerberos visibility, EWS MRSProxyEnabled, Outlook Anywhere SSLOffloading, PowerShell authentication, PowerShell RequireSSL, and Extended Protection remain Review Only.

## Safety behavior

- Review and Backup are read-only.
- Clone is read-only by default.
- Clone Apply requires the explicit `-ApplyChanges` switch.
- `-CompareOnly` and `-ApplyChanges` cannot be used together.
- A fresh pre-change JSON backup is required before Apply starts.
- Apply is blocked if the fresh pre-change configuration no longer matches the Preview.
- Generated `Set-*` commands and required companion parameters are validated against the current Exchange Management Shell before command export or Apply.
- Source-server-specific URL, hostname, or SCP values that directly reference the source Exchange server remain Review Only during Clone.
- Short name and FQDN aliases are resolved so the source server cannot also be used as a target and duplicate targets cannot bypass validation.
- Clone authentication is Apply-capable only when source and target run the same Exchange build and the current Exchange Management Shell exposes the required setting.
- For Clone Apply with `-IncludeAuthentication`, run the script from Exchange Management Shell on a server matching the target Exchange build.
- PowerShell URLs require explicit `-IncludePowerShellUrls`.
- In Configure mode, each PowerShell URL keeps its current HTTP/HTTPS scheme; HTTP is used when the current scheme cannot be read.
- Restore is same-server only.
- `-OutputFile` exports commands or reports only and does not apply Exchange configuration changes.
- Post-Apply verification re-reads the target configuration and reports remaining differences.

## Parameters

| Parameter | Description |
|---|---|
| `-Review` | Runs read-only Review mode. |
| `-Backup` | Runs read-only Backup mode. |
| `-Restore` | Runs Restore mode with `-BackupFile`. |
| `-Interactive` | Runs guided Interactive Configure mode. |
| `-Server` | One or more Exchange servers for Review, Backup, Configure, or Interactive Configure. |
| `-SourceServer` | Live source Exchange server for Clone. |
| `-TargetServer` | One or more target Exchange servers for Clone. |
| `-CompareOnly` | Explicitly requests read-only Clone comparison. |
| `-ApplyChanges` | Explicitly enables the Clone Apply path. |
| `-BackupFile` | ExchangeURLManager JSON backup used by Restore. |
| `-BackupPath` | Optional Backup output path. |
| `-InternalNamespace` | Internal Client Access namespace for Configure mode. |
| `-ExternalNamespace` | External Client Access namespace for Configure mode. |
| `-AutodiscoverSCPNamespace` | Autodiscover SCP namespace for Configure mode. |
| `-ClearExternalUrls` | Clears supported external URLs and Outlook Anywhere ExternalHostname in Configure mode. |
| `-IncludeAuthentication` | Includes supported authentication settings in Clone or Restore. |
| `-IncludePowerShellUrls` | Includes PowerShell InternalUrl / ExternalUrl. |
| `-IncludeOutlookAnywhereSslRequirements` | Includes Outlook Anywhere SSL requirement values in Clone or Restore. |
| `-OutlookAnywhereInternalClientsRequireSsl` | Explicit Configure value for InternalClientsRequireSsl. |
| `-OutlookAnywhereExternalClientsRequireSsl` | Explicit Configure value for ExternalClientsRequireSsl. |
| `-OutlookAnywhereDefaultAuthenticationMethod` | Explicit Configure value for Outlook Anywhere default authentication. |
| `-OutputFile` | Writes a report or exports required `Set-*` commands without applying changes. |
| `-Help` | Displays the built-in usage guide. |

## Examples

Review one server:

```powershell
.\ExchangeURLManager.ps1 -Review -Server EX01
```

Review multiple servers:

```powershell
.\ExchangeURLManager.ps1 -Review -Server EX01,EX02
```

Configure common namespaces:

```powershell
.\ExchangeURLManager.ps1 -Server EX01,EX02 -InternalNamespace mail.contoso.com -ExternalNamespace mail.contoso.com -AutodiscoverSCPNamespace autodiscover.contoso.com
```

Start Interactive Configure:

```powershell
.\ExchangeURLManager.ps1 -Interactive
```

Compare a source server with target servers:

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02,EX03
```

Compare supported authentication settings:

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02,EX03 -IncludeAuthentication
```

Apply a reviewed Clone plan:

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02,EX03 -ApplyChanges
```

Create backups:

```powershell
.\ExchangeURLManager.ps1 -Backup -Server EX01,EX02
```

Preview a same-server Restore:

```powershell
.\ExchangeURLManager.ps1 -Restore -BackupFile C:\Temp\EX01-ExchangeURL.json
```

Export required commands without applying changes:

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02 -ApplyChanges -OutputFile C:\Temp\ExchangeURL-Commands.txt
```

Built-in help:

```powershell
.\ExchangeURLManager.ps1 -Help
```

Full PowerShell help:

```powershell
Get-Help .\ExchangeURLManager.ps1 -Full
```

## Requirements

- Exchange Server Mailbox server environment
- Windows PowerShell 5.1
- Exchange Management Shell / required Exchange administrative permissions

## Notes

Version 1.0 was live-lab validated on Exchange build 15.2.1748.10 using Windows PowerShell 5.1.

Validation included parser and built-in Help checks, read-only comparison paths, backup and Restore validation paths, Preview drift blocking, command export, pre-change backup creation, Clone Apply, and post-Apply verification with zero remaining changes.

Mixed-build Clone authentication behavior was not runtime-validated.

The script does not manage certificates, private keys, IIS bindings, firewall/NAT, load balancers, DNS records, ASA credential deployment, SPNs, or Extended Protection changes.

Always review the Preview or exported commands and test the script in your environment before production use.

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## Feedback and issues

For bugs, feedback, or feature requests, use [GitHub Issues](https://github.com/Ceyhun-Kirmizitas/ExchangeURLManager.ps1/issues).

Website: [ceyhunkirmizitas.net](https://ceyhunkirmizitas.net/)

## License

MIT. See [LICENSE](LICENSE).
