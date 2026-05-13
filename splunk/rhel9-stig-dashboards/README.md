# RHEL9 STIG Compliance Dashboards (Splunk App)

## Overview

This Splunk app ships seven STIG-aligned event dashboards (plus an Overview)
covering the STIG-mandated audit-event categories — SRO (Security-Relevant
Object), Authentication, Privileged Rights, Account, DTA (Data Transfer /
External Media), Group Management, and Audit Health — for RHEL9 hosts and
the supported network devices (Cisco IOS/ASA/NX-OS and Palo Alto Networks
PAN-OS). Every panel normalises events to a single STIG schema so analysts
get a uniform `_time / event_category / event_action / source_host /
source_type / outcome / user / details` view regardless of the underlying
sourcetype.

## Prerequisites

- **Splunk Enterprise 8.2+** — Dashboard Studio v2 is required for the
  bundled views in `default/data/ui/views/`.
- **Indexes:** `index=os_linux` and `index=network` must exist and be
  searchable by the role the app is installed for.
- **RHEL9 hosts** forwarding via the Splunk Universal Forwarder plus the
  Splunk Add-on for Unix and Linux (`Splunk_TA_nix`). Required sourcetypes:
  - `linux_secure` (`/var/log/secure`)
  - `auditd` (`/var/log/audit/audit.log`)
  - `linux_audit` (alternate auditd sourcetype on some deployments)
  - `journald` (systemd journal)
  - `linux_messages_syslog` (`/var/log/messages`)
- **Cisco** devices sending syslog into `index=network` with one of the
  sourcetypes `cisco:ios`, `cisco:asa`, or `cisco:nxos`.
- **Palo Alto Networks** firewalls forwarding syslog via the **Splunk Add-on
  for Palo Alto Networks**, producing the sourcetypes `pan:system`,
  `pan:config`, and `pan:globalprotect`.

## Install

```bash
# From the repo root
cp -a splunk/rhel9-stig-dashboards/ $SPLUNK_HOME/etc/apps/rhel9_stig_dashboards/
$SPLUNK_HOME/bin/splunk restart
```

Verify the saved searches loaded:

```bash
$SPLUNK_HOME/bin/splunk btool savedsearches list --debug | grep stig_
```

You should see 117 stanzas attributed to
`etc/apps/rhel9_stig_dashboards/default/savedsearches.conf`.

## Dashboards

| View | Label | Purpose |
|---|---|---|
| `stig_overview` | STIG - Overview | Cross-category roll-up: event counts by category and source, current N/A coverage matrix, audit-health quick status. |
| `stig_sro_events` | STIG - Security Relevant Object Events | File/object Create / Access / Delete / Modify / Permission-Modify / Ownership-Modify events from auditd (Linux only — Cisco / PAN are N/A). |
| `stig_authentication` | STIG - Authentication Events | Logon / Logoff success and failure across Linux (linux_secure + auditd), Cisco (IOS/ASA/NX-OS auth mnemonics), and PAN (admin + GlobalProtect). |
| `stig_privileged_rights` | STIG - Privileged Rights Events | Audit Policy / MAC Policy / Config / Root Access / Privilege Escalation / Audit Log Access / System Boot-Shutdown / Password Reset events. |
| `stig_account_events` | STIG - Account Events | User Account Create / Modify / Delete / Disable / Enable events across all three sources. |
| `stig_dta_events` | STIG - Data Transfer (DTA) Events | Removable-media import/export on Linux (auditd usbdev), `copy` import/export on Cisco, config-export / config-import on PAN. |
| `stig_group_management` | STIG - Group Management Events | Group / role Create / Modify / Delete and user-to-group membership changes across all three sources. |
| `stig_other_health` | STIG - Audit Health | Audit storage / processing-failure warnings (Linux + PAN); Cisco device health is out of scope. |

## Required panel schema

Every panel returns the same eight-field schema:

```
_time, event_category, event_action, source_host, source_type, outcome, user, details
```

The first five (`_time`, `source_host`, `source_type`, `outcome`, `user`) are
the STIG-mandated audit-event fields. `event_category` / `event_action` /
`details` are added for dashboard context — `details` is a free-form blob
of the message body / SPL-extracted fields for analyst drill-down.

## STIG categories covered

| STIG category | Dashboard file |
|---|---|
| Security Relevant Object (SRO) | `default/data/ui/views/stig_sro_events.xml` |
| Authentication | `default/data/ui/views/stig_authentication.xml` |
| Privileged Rights | `default/data/ui/views/stig_privileged_rights.xml` |
| Account Management | `default/data/ui/views/stig_account_events.xml` |
| Data Transfer (DTA) | `default/data/ui/views/stig_dta_events.xml` |
| Group Management | `default/data/ui/views/stig_group_management.xml` |
| Audit Health | `default/data/ui/views/stig_other_health.xml` |

## Source coverage matrix

Cells marked `N/A` are not emitted by that platform; the dashboards render
an "N/A for this source" placeholder panel. See the per-source index files
under `_build/` for the authoritative reasoning.

| Category | Linux (RHEL9 / auditd) | Cisco (IOS / ASA / NX-OS) | Palo Alto (PAN-OS) |
|---|---|---|---|
| SRO | ✓ | N/A | N/A |
| Authentication | ✓ | ✓ | ✓ |
| Privileged Rights — Config / Escalation / Root / Reboot / PasswordReset | ✓ | ✓ | ✓ |
| Privileged Rights — Audit Policy / MAC Policy | ✓ | N/A | ✓ (policy-change) |
| Privileged Rights — Audit Log Access | ✓ | ✓ (best-effort proxy) | N/A |
| Account Management | ✓ | ✓ | ✓ |
| DTA | ✓ | ✓ | ✓ |
| Group Management | ✓ | ✓ | ✓ |
| Audit Health | ✓ | N/A | ✓ |

## Customizing for your environment

- **auditd `key=` tags** — Linux SPL panels filter on auditd rule keys
  (`key=access`, `key=modify`, `key=perm_mod`, `key=owner_mod`, `key=usbdev`,
  `key=audit-policy`, `key=audit-access`, `key=usermod`, `key=groupmod`,
  `key=mac-policy`, `key=sysconfig`, `key=etc-passwd`, `key=etc-shadow`,
  `key=etc-group`, etc.). The conventions used are documented in
  `_build/searches_linux_index.md`. If your audit rules use different keys,
  update the `key=` clauses in the affected stanzas of
  `default/savedsearches.conf`.
- **Index names** — `index=os_linux` and `index=network` are hardcoded in
  the SPL. Globally rename if your deployment uses different index names:

  ```bash
  sed -i 's/index=os_linux/index=YOUR_LINUX_INDEX/g'  default/savedsearches.conf
  sed -i 's/index=network/index=YOUR_NETWORK_INDEX/g' default/savedsearches.conf
  ```

## Limitations

The following coverage gaps are documented in the per-source index files
under `_build/`. The dashboards render an explicit "N/A for this source"
placeholder for every one of them so reviewers can confirm the gap is
intentional rather than a missing forwarder:

- **Cisco — SRO (all actions, both outcomes):** network devices have no
  filesystem ACL / SACL event surface; index ACLs live on the SIEM, not the
  device.
- **Cisco — Authentication Logoff failure:** IOS / ASA / NX-OS emit no
  "logoff failed" mnemonic; session-teardown failures only surface as
  connection-reset events, which are out of scope per spec.
- **Cisco — Privileged AuditLogAccess failure:** no Cisco mnemonic
  distinguishes a failed read of local audit logs; the success-side search
  is itself a best-effort proxy via `%SEC-6-IPACCESSLOGP` on syslog flows.
- **Cisco — Account Disable failure:** no reliable signal; parser errors on
  `username … nopassword` collapse into the modify-failure search.
- **Cisco — GroupMgmt Modify failure:** role / aaa-group modify failures
  produce `%PARSER-4-` lines indistinguishable from add/delete failures and
  are covered by those searches.
- **Cisco — Audit Health (all):** audit-storage health for syslog ingest
  lives on the Splunk indexer / forwarder side, not on the device.
- **Palo Alto — SRO (all):** PAN-OS appliances expose no general-purpose
  filesystem; SRO has no analog in PAN-OS syslog.
- **Palo Alto — Privileged AuditLogAccess (read):** PAN-OS does not emit a
  distinct syslog record when an admin reads the system / config / audit
  log via WebUI or CLI `show log`. Rendered as a placeholder panel.
