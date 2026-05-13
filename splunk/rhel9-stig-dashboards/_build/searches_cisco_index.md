# Cisco Syslog STIG Search Index

This index lists every Cisco syslog STIG search emitted in `searches_cisco.conf`,
and documents which (category, action) tuples are **N/A** for Cisco network
devices. The dashboard assembler should render an "N/A for this source"
placeholder panel for every N/A row below.

## Emitted searches

| Search Name | Category | Event Action | Outcome | Notes |
|---|---|---|---|---|
| stig_auth_logon_success_cisco | Authentication | Logon | success | IOS `%SEC_LOGIN-5-LOGIN_SUCCESS`, ASA `605005`/`611101`/`113004`, NX-OS auth success. |
| stig_auth_logon_failure_cisco | Authentication | Logon | failure | IOS `%SEC_LOGIN-4-LOGIN_FAILED`, ASA `113005`/`605004`/`611102`, NX-OS auth failed. |
| stig_auth_logoff_success_cisco | Authentication | Logoff | success | IOS `%SEC_LOGIN-5-LOGOUT`, ASA `611103`. Logoff failure has no Cisco signal. |
| stig_priv_config_change_success_cisco | Privileged | ConfigChange | success | IOS `%SYS-5-CONFIG_I`, ASA `111008`/`111010`, NX-OS `VSHD_SYSLOG_CONFIG_I`. |
| stig_priv_config_change_failure_cisco | Privileged | ConfigChange | failure | `%PARSER-4-` and `Invalid input` / `% Error` / authorization-denied markers. |
| stig_priv_escalation_success_cisco | Privileged | PrivEscalation | success | IOS `%SYS-5-PRIV_AUTH_PASS`, ASA `502103`/`308001`, NX-OS role change. |
| stig_priv_escalation_failure_cisco | Privileged | PrivEscalation | failure | IOS `%SYS-5-PRIV_AUTH_FAIL`, enable-denied messages. |
| stig_priv_root_access_success_cisco | Privileged | RootAccess | success | Successful logon with priv 15 / network-admin / level=15. |
| stig_priv_root_access_failure_cisco | Privileged | RootAccess | failure | Failed logon attempt targeting priv 15 / network-admin. |
| stig_priv_audit_log_access_cisco | Privileged | AuditLogAccess | success | Best-effort via `%SEC-6-IPACCESSLOGP` on syslog (UDP/514) flows. Cisco has no native local audit-log access event. |
| stig_priv_system_reboot_success_cisco | Privileged | SystemReboot | success | `%SYS-5-RELOAD`, `%SYS-6-BOOTTIME`, `%SYS-5-RESTART`, ASA `199015`, NX-OS `PFM_SYSTEM_RESET`. |
| stig_priv_password_reset_success_cisco | Privileged | PasswordReset | success | `%SYS-5-PASSWD_RESET`, ASA `502102`, `username X password` config lines. |
| stig_priv_password_reset_failure_cisco | Privileged | PasswordReset | failure | Password/secret commands with parser errors or permission denial. |
| stig_account_add_success_cisco | Account | AccountAdd | success | `username X privilege ...` via `%SYS-5-CONFIG_I`, ASA `502101`, NX-OS user create. |
| stig_account_add_failure_cisco | Account | AccountAdd | failure | `username` commands rejected by parser/authorization. |
| stig_account_modify_success_cisco | Account | AccountModify | success | `username X privilege/role/password/secret` updates. |
| stig_account_modify_failure_cisco | Account | AccountModify | failure | `username` modify commands rejected by parser/authorization. |
| stig_account_delete_success_cisco | Account | AccountDelete | success | `no username X`, ASA `502102`, NX-OS user delete. |
| stig_account_delete_failure_cisco | Account | AccountDelete | failure | `no username` commands rejected by parser/authorization. |
| stig_account_disable_success_cisco | Account | AccountDisable | success | `username X nopassword` / `shutdown` / `disable` via config-change context. |
| stig_dta_export_success_cisco | DTA | Export | success | `copy running-config tftp:/scp:/ftp:/http(s):` from device to external. |
| stig_dta_export_failure_cisco | DTA | Export | failure | Export `copy` with error / timeout / CPU_HOG / parser failure. |
| stig_dta_import_success_cisco | DTA | Import | success | `copy tftp:/scp:/ftp:/http(s):` from external to device store. |
| stig_dta_import_failure_cisco | DTA | Import | failure | Import `copy` with error / timeout / CPU_HOG / parser failure. |
| stig_groupmgmt_add_success_cisco | GroupMgmt | GroupAdd | success | NX-OS `role name X`, IOS/ASA `aaa group server X`. |
| stig_groupmgmt_add_failure_cisco | GroupMgmt | GroupAdd | failure | Role/aaa-group create commands rejected by parser/authorization. |
| stig_groupmgmt_modify_success_cisco | GroupMgmt | GroupModify | success | `rule` / `permit` / aaa `server` changes inside role / group context. |
| stig_groupmgmt_delete_success_cisco | GroupMgmt | GroupDelete | success | `no role name X`, `no aaa group server X`. |
| stig_groupmgmt_delete_failure_cisco | GroupMgmt | GroupDelete | failure | Role/aaa-group delete commands rejected by parser/authorization. |

## N/A tuples (assembler should render "no data — N/A for this source")

| Category | Event Action | Outcome | Reason |
|---|---|---|---|
| SRO | * | success | Cisco network devices have no filesystem ACL / SACL event surface. Splunk-side index ACLs are governed by the SIEM, not the device. |
| SRO | * | failure | Same — no device-emitted SRO events. |
| Authentication | Logoff | failure | Cisco does not emit a "logoff failed" mnemonic. Session teardown failures surface only as connection-reset events (`%ASA-6-302014` etc.), which are out of scope per spec. |
| Privileged | AuditLogAccess | failure | No Cisco mnemonic distinguishes failed access to local audit logs; the success-side search itself is already a best-effort proxy via ACL hits on syslog flows. |
| Account | AccountDisable | failure | No reliable Cisco signal for a failed disable; `username ... nopassword` parser errors collapse into `stig_account_modify_failure_cisco`. |
| GroupMgmt | GroupModify | failure | Role/aaa-group modify failures collapse into `%PARSER-4-` lines indistinguishable from the add/delete failure searches; covered there. |
| Health | * | * | Audit-storage health for syslog ingest lives on the Splunk side (indexer / forwarder health), not on the Cisco device. Out of scope for the device-side library. |

## Notes for the dashboard assembler

- Every emitted search returns the schema:
  `_time, event_category, event_action, source_host, source_type, outcome, user, details`.
- `source_type` is the literal string `"cisco"` for all rows, regardless of the
  underlying Splunk `sourcetype` (`cisco:ios` / `cisco:asa` / `cisco:nxos`).
- `source_host` is `coalesce(host, dvc_name, host_name)`; Cisco syslog parsers
  populate `host` from the syslog header in nearly all deployments.
- `user` defaults to `"unknown"` (or `"system"` for unattended reboot/boot
  events) when no user token is extractable from the message.
- The audit-log-access search is gated behind a `\`comment()\`` macro call
  flagging it as a best-effort proxy rather than a native event surface.
