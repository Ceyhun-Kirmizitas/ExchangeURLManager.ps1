# ExchangeURLManager.ps1

ExchangeURLManager.ps1 helps you review and configure **Exchange Server Client Access URLs and namespaces**. You can also compare settings between servers, create configuration backups, and restore them when needed.

It is useful for Exchange upgrades, side-by-side migrations, server replacements, and regular configuration reviews.

## Requirements

- Exchange Server 2016, 2019, or Subscription Edition (intended compatibility).
- Windows PowerShell 5.1, Exchange Management Shell, and appropriate Exchange administrative permissions.

[Download ExchangeURLManager.ps1](https://raw.githubusercontent.com/Ceyhun-Kirmizitas/ExchangeURLManager.ps1/main/ExchangeURLManager.ps1)

## What it does

- **Review:** Show the current Client Access configuration on one or more servers.
- **Configure:** Set internal and external namespaces, with Preview and confirmation before changes.
- **Interactive Configure:** Choose servers, components, and values through guided prompts.
- **Clone:** Compare a source server with one or more targets. Add `-ApplyChanges` to apply eligible differences.
- **Backup:** Save each server's configuration as JSON, with a readable TXT copy in the `ConfigBackups` folder by default.
- **Restore:** Restore a JSON backup to the same server. Use Clone for server-to-server changes.

## Parameters

| Parameter | Description |
|---|---|
| `-Review` | Review configuration without making changes. Requires `-Server`. |
| `-Backup` | Create a JSON backup and TXT report for each server. Requires `-Server`. |
| `-Restore` | Restore configuration from a JSON backup to the same server. Requires `-BackupFile`. |
| `-Interactive` | Start guided configuration. Optionally use `-Server` to specify servers. |
| `-Server` | One or more Exchange servers for Review, Backup, Configure, or Interactive Configure. |
| `-SourceServer` | Source Exchange server for Clone comparison. |
| `-TargetServer` | One or more target Exchange servers for Clone. |
| `-CompareOnly` | Explicit read-only Clone comparison; cannot be combined with `-ApplyChanges`. |
| `-ApplyChanges` | Allow Clone to apply supported changes after Preview and confirmation. |
| `-BackupFile` | JSON backup file used for same-server Restore. |
| `-BackupPath` | Optional folder for manual backups. |
| `-InternalNamespace` | Internal namespace for Configure. |
| `-ExternalNamespace` | External namespace for Configure. |
| `-AutodiscoverSCPNamespace` | Autodiscover SCP hostname for Configure. |
| `-ClearExternalUrls` | Clear supported external URLs and Outlook Anywhere ExternalHostname in Configure; cannot be used with `-ExternalNamespace`. |
| `-IncludeAuthentication` | Show authentication in Review or include supported authentication settings in Clone and Restore. |
| `-IncludePowerShellUrls` | Include PowerShell internal and external URLs in Configure, Clone, or Restore. |
| `-IncludeOutlookAnywhereSslRequirements` | Include Outlook Anywhere client SSL requirements in Clone or Restore. |
| `-OutlookAnywhereInternalClientsRequireSsl` | Set the Outlook Anywhere internal client SSL requirement in Configure. |
| `-OutlookAnywhereExternalClientsRequireSsl` | Set the Outlook Anywhere external client SSL requirement in Configure. |
| `-OutlookAnywhereDefaultAuthenticationMethod` | Set the Outlook Anywhere default authentication method in Configure (`Basic`, `Ntlm`, or `Negotiate`). |
| `-OutputFile` | Export commands or a report without applying configuration changes. |
| `-Help` | Show the built-in usage guide. |

## Examples

**Review two Exchange servers**

```powershell
.\ExchangeURLManager.ps1 -Review -Server EX01,EX02
```

**Configure internal and external namespaces**

```powershell
.\ExchangeURLManager.ps1 -Server EX01,EX02 -InternalNamespace mail.contoso.com -ExternalNamespace mail.contoso.com -AutodiscoverSCPNamespace autodiscover.contoso.com
```

**Start Interactive Configure**

```powershell
.\ExchangeURLManager.ps1 -Interactive
```

**Compare a source server with two targets**

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02,EX03
```

**Apply the Clone plan after Preview and confirmation**

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02,EX03 -ApplyChanges
```

**Back up two servers**

```powershell
.\ExchangeURLManager.ps1 -Backup -Server EX01,EX02
```

**Restore from a backup**

```powershell
.\ExchangeURLManager.ps1 -Restore -BackupFile C:\Temp\ExchangeURLManager-EX01-Manual.json
```

**Export required Clone commands without applying them**

```powershell
.\ExchangeURLManager.ps1 -SourceServer EX01 -TargetServer EX02 -ApplyChanges -OutputFile C:\Temp\ExchangeURL-Commands.txt
```

The backup filename in the Restore example is illustrative. Use the path to an actual JSON backup created by the script.

For detailed PowerShell help:

```powershell
Get-Help .\ExchangeURLManager.ps1 -Full
```

## Supported settings

- Internal and external URLs for OWA, ECP, EWS, MAPI, ActiveSync, and OAB.
- Autodiscover SCP (`AutoDiscoverServiceInternalUri`) and Outlook Anywhere hostnames.
- Optional authentication settings, PowerShell URLs, and Outlook Anywhere client SSL requirements when explicitly selected.

Migration-sensitive settings, including ASA/Kerberos status, EWS MRSProxyEnabled, Outlook Anywhere SSLOffloading, PowerShell authentication/RequireSSL, and Extended Protection, remain **Review Only**. Outlook Anywhere settings refer to RPC over HTTP, not MAPI over HTTP.

The script does not configure certificates, IIS bindings, DNS records, firewalls, load balancers, SPNs, ASA credentials, or Extended Protection.

## Safety

- Changes are shown in Preview and require confirmation. The script creates a fresh JSON backup before Apply and verifies the result afterward.
- Apply is blocked if a required backup fails, settings used by the plan change after Preview, or the proposed Exchange commands fail readiness checks. These include EWS DNS and Outlook Anywhere SSL checks.
- Clone keeps source-server-specific values from being applied blindly to other servers. Authentication settings are not automatically copied between different Exchange builds.
- If a command fails, remaining commands for that server are not run. The summary shows what succeeded, failed, or remains unchanged.
- `-OutputFile` writes a report or command file without applying changes. Readiness BLOCKER messages are shown in the console, not in the exported command file.

Apply failures and BLOCKERs return exit code `1`. Completed exports return `0`, even when a readiness BLOCKER is shown. For manual `-Backup`, check the per-server results because a failed backup does not always return a nonzero exit code.

## More information

[Changelog](CHANGELOG.md) · [Report an issue](https://github.com/Ceyhun-Kirmizitas/ExchangeURLManager.ps1/issues) · [Website](https://ceyhunkirmizitas.net/) · [MIT License](LICENSE)
