# Palo Alto Networks STIG Search Library Index

| Search Name | Category | Event Action | Outcome | Notes |
|---|---|---|---|---|
| (n/a) | SRO | File/Filesystem Object | n/a | N/A for PAN firewalls — appliance has no general-purpose filesystem exposed to admin actions; SRO has no analog in PAN-OS syslog. |
| stig_auth_logon_success_pan_admin | Authentication | Admin Logon | success | `pan:system subtype=auth` event_id starts with `auth-success`. |
| stig_auth_logon_fail_pan_admin | Authentication | Admin Logon | fail | `pan:system subtype=auth` event_id starts with `auth-fail`. |
| stig_auth_logoff_pan_admin | Authentication | Admin Logoff | success | `pan:system subtype=auth` event_id `auth-logout`. |
| stig_auth_logon_success_pan_gp | Authentication | GlobalProtect Logon | success | GlobalProtect gateway login success; covers both `pan:globalprotect` and `pan:system`. |
| stig_auth_logon_fail_pan_gp | Authentication | GlobalProtect Logon | fail | GlobalProtect gateway login failure. |
| stig_auth_logoff_pan_gp | Authentication | GlobalProtect Logoff | success | GlobalProtect gateway logout success. |
| stig_priv_config_commit_success_pan | Privileged | Config Commit | success | `pan:config result=Succeeded` plus `pan:system` "commit succeeded". |
| stig_priv_config_commit_fail_pan | Privileged | Config Commit | fail | `pan:config result=Failed` plus `pan:system` "commit failed". |
| stig_priv_policy_change_success_pan | Privileged | Security/Audit Policy Change | success | `pan:config` with paths under rulebase security, network, device setting, log-settings. |
| stig_priv_policy_change_fail_pan | Privileged | Security/Audit Policy Change | fail | Same scope as above, `result=Failed`. |
| stig_priv_system_reboot_pan | Privileged | System Reboot/Restart | success | `pan:system subtype=general` matching reboot/system started/pan_init. |
| stig_priv_password_reset_pan | Privileged | Admin Password Reset | success | `event_id=mgmt-admin-password-changed`. |
| stig_priv_account_lockout_pan | Privileged | Admin Account Lockout | success | `event_id=mgmt-admin-account-locked`. |
| stig_priv_account_unlock_pan | Privileged | Admin Account Unlock | success | `event_id=mgmt-admin-account-unlocked`. |
| (n/a) | Privileged | Audit Log Access (read) | n/a | PAN-OS does not emit a distinct syslog record when an admin reads the system/config/audit log via WebUI or CLI `show log`. Placeholder. |
| stig_account_create_success_pan | Account | Admin Account Create/Modify | success | `pan:config cmd=set path=*mgt-config users*` excluding role/password/disable sub-paths. |
| stig_account_create_fail_pan | Account | Admin Account Create/Modify | fail | Same as above, `result=Failed`. |
| stig_account_delete_success_pan | Account | Admin Account Delete | success | `pan:config cmd=delete path=*mgt-config users*`. |
| stig_account_delete_fail_pan | Account | Admin Account Delete | fail | Same as above, `result=Failed`. |
| stig_account_disable_success_pan | Account | Admin Account Disable | success | `pan:config` set of `disabled yes` under `mgt-config users`. |
| stig_account_enable_success_pan | Account | Admin Account Enable | success | `pan:config` delete of `disabled` node under `mgt-config users`. |
| stig_dta_config_export_success_pan | DTA | Config Export/Save | success | `event_id=config-export-*` succeeded or "Saved configuration" message. |
| stig_dta_config_export_fail_pan | DTA | Config Export/Save | fail | `event_id=config-export-*` failed. |
| stig_dta_config_import_success_pan | DTA | Config Import/Load | success | `event_id=config-import-*` succeeded or "Loaded configuration from" message. |
| stig_dta_config_import_fail_pan | DTA | Config Import/Load | fail | `event_id=config-import-*` failed. |
| stig_groupmgmt_role_create_success_pan | GroupMgmt | Admin Role Create/Modify | success | `pan:config cmd=set path=*admin-role*`. |
| stig_groupmgmt_role_create_fail_pan | GroupMgmt | Admin Role Create/Modify | fail | Same, `result=Failed`. |
| stig_groupmgmt_role_delete_success_pan | GroupMgmt | Admin Role Delete | success | `pan:config cmd=delete path=*admin-role*`. |
| stig_groupmgmt_role_delete_fail_pan | GroupMgmt | Admin Role Delete | fail | Same, `result=Failed`. |
| stig_groupmgmt_user_role_assign_success_pan | GroupMgmt | User-to-Role Mapping Change | success | `pan:config` set/delete on `mgt-config users *role-based*`. |
| stig_groupmgmt_user_role_assign_fail_pan | GroupMgmt | User-to-Role Mapping Change | fail | Same, `result=Failed`. |
| stig_health_disk_usage_pan | Health | Disk/Log Partition Usage Warning | fail | `pan:system subtype=general` high/critical/medium severity with disk/log-partition wording. |
| stig_health_log_forwarding_fail_pan | Health | Log Forwarding Failure | fail | `pan:system` syslog/log-forwarding failure messages. |
| stig_health_audit_processing_fail_pan | Health | Audit/Log Processing Failure | fail | `pan:system` log-receiver/logd daemon errors. |
