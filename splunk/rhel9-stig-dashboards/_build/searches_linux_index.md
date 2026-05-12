# RHEL9 STIG Search Index

| Search Name | Category | Event Action | Outcome | Notes |
|---|---|---|---|---|
| stig_sro_create_success_linux | SRO | Create | success | auditd SYSCALL creat/open/openat with success=yes; covers any audit rule firing on file creation. |
| stig_sro_create_failure_linux | SRO | Create | failure | auditd SYSCALL creat/open/openat with success=no. |
| stig_sro_access_success_linux | SRO | Access | success | Driven by SYSCALL key=access (auditd rule convention for unauthorized read attempts). |
| stig_sro_access_failure_linux | SRO | Access | failure | Driven by SYSCALL key=access success=no. Most STIG-mandated access events land here. |
| stig_sro_delete_success_linux | SRO | Delete | success | unlink/unlinkat/rmdir/rename/renameat success=yes. |
| stig_sro_delete_failure_linux | SRO | Delete | failure | unlink/unlinkat/rmdir/rename/renameat success=no. |
| stig_sro_modify_success_linux | SRO | Modify | success | SYSCALL key=modify or write/truncate variants with success=yes. |
| stig_sro_modify_failure_linux | SRO | Modify | failure | Same as above with success=no. |
| stig_sro_perm_modify_success_linux | SRO | PermissionModify | success | SYSCALL key=perm_mod (chmod/setxattr) success=yes. |
| stig_sro_perm_modify_failure_linux | SRO | PermissionModify | failure | SYSCALL key=perm_mod success=no. |
| stig_sro_owner_modify_success_linux | SRO | OwnershipModify | success | SYSCALL key=owner_mod (chown/fchown/lchown) success=yes. |
| stig_sro_owner_modify_failure_linux | SRO | OwnershipModify | failure | SYSCALL key=owner_mod success=no. |
| stig_auth_logon_success_linux | Authentication | Logon | success | linux_secure "Accepted password/publickey/gssapi/keyboard-interactive" + auditd USER_LOGIN res=success. |
| stig_auth_logon_failure_linux | Authentication | Logon | failure | linux_secure "Failed password/publickey/authentication failure/Invalid user" + auditd USER_LOGIN res=failed. |
| stig_auth_logoff_success_linux | Authentication | Logoff | success | auditd USER_END/USER_LOGOUT + secure "session closed for user". outcome hardcoded "success" - auditd does not surface logoff failures. |
| stig_auth_logoff_failure_linux | Authentication | Logoff | failure | Rare USER_END res=failed; emitted empty-safe per spec. |
| stig_priv_audit_policy_change_success_linux | Privileged | AuditPolicyChange | success | CONFIG_CHANGE / DAEMON_CONFIG with key=audit-policy/audit-storage/audit-access res=success. |
| stig_priv_audit_policy_change_failure_linux | Privileged | AuditPolicyChange | failure | Same with res=failed. |
| stig_priv_mac_policy_change_success_linux | Privileged | MACPolicyChange | success | MAC_POLICY_LOAD/MAC_CONFIG_CHANGE/CONFIG_CHANGE key=mac-policy res=success. |
| stig_priv_mac_policy_change_failure_linux | Privileged | MACPolicyChange | failure | Same with res=failed. |
| stig_priv_config_change_success_linux | Privileged | ConfigChange | success | SYSCALL key=sysconfig/etc-passwd/etc-shadow/etc-group with success=yes. |
| stig_priv_config_change_failure_linux | Privileged | ConfigChange | failure | Same with success=no. |
| stig_priv_root_access_success_linux | Privileged | RootAccess | success | linux_secure "session opened for user root" + auditd USER_START/USER_LOGIN acct=root or auid=0 res=success. |
| stig_priv_root_access_failure_linux | Privileged | RootAccess | failure | "Failed password for root" + USER_LOGIN root res=failed. |
| stig_priv_escalation_success_linux | Privileged | PrivilegeEscalation | success | sudo COMMAND= lines, auditd USER_CMD res=success, su to root USER_AUTH res=success. |
| stig_priv_escalation_failure_linux | Privileged | PrivilegeEscalation | failure | sudo "incorrect password attempts" / "user NOT in sudoers" / "command not allowed", USER_CMD res=failed, su res=failed. |
| stig_priv_audit_log_access_success_linux | Privileged | AuditLogAccess | success | SYSCALL key=audit-access success=yes - reads/opens against /var/log/audit/*. |
| stig_priv_audit_log_access_failure_linux | Privileged | AuditLogAccess | failure | SYSCALL key=audit-access success=no. |
| stig_priv_system_boot_success_linux | Privileged | SystemBootShutdown | success | auditd SYSTEM_BOOT/SYSTEM_SHUTDOWN/DAEMON_START/DAEMON_END. These records have no res field; outcome hardcoded "success". |
| stig_priv_system_boot_failure_linux | Privileged | SystemBootShutdown | failure | systemd "Failed to start/stop" or "shutdown/reboot failed" lines in linux_messages_syslog. |
| stig_priv_password_reset_success_linux | Privileged | PasswordReset | success | auditd USER_CHAUTHTOK res=success + secure "password changed for" / "password updated successfully". |
| stig_priv_password_reset_failure_linux | Privileged | PasswordReset | failure | USER_CHAUTHTOK res=failed + secure "authentication token manipulation error" / "password unchanged". |
| stig_account_create_success_linux | Account | Create | success | auditd ADD_USER res=success / USER_MGMT op="adding-user" / SYSCALL key=usermod exe=useradd. |
| stig_account_create_failure_linux | Account | Create | failure | Same with res=failed / success=no. |
| stig_account_modify_success_linux | Account | Modify | success | USER_MGMT op="updating-user" res=success / SYSCALL key=usermod exe=usermod or chage success=yes. |
| stig_account_modify_failure_linux | Account | Modify | failure | Same with failed/no. |
| stig_account_delete_success_linux | Account | Delete | success | DEL_USER res=success / USER_MGMT op="deleting-user" / SYSCALL key=usermod exe=userdel. |
| stig_account_delete_failure_linux | Account | Delete | failure | Same with failed/no. |
| stig_account_disable_success_linux | Account | Disable | success | SYSCALL key=usermod success=yes invoking usermod -L, passwd -l, or chage -E 0/1. |
| stig_account_disable_failure_linux | Account | Disable | failure | Same with success=no. |
| stig_account_enable_success_linux | Account | Enable | success | SYSCALL key=usermod success=yes invoking usermod -U or passwd -u. |
| stig_account_enable_failure_linux | Account | Enable | failure | Same with success=no. |
| stig_dta_external_media_write_success_linux | DTA | ExternalMediaWrite | success | SYSCALL mount success=yes with key=usbdev or target /media/* /run/media/* + USER_DEVICE key=usbdev. Best available auditd signal for removable-media import/export. |
| stig_dta_external_media_write_failure_linux | DTA | ExternalMediaWrite | failure | Same mount filter with success=no. |
| stig_dta_external_media_read_success_linux | DTA | ExternalMediaRead | success | Kernel usb-storage / "Attached SCSI removable disk" lines from linux_messages_syslog and journald. No per-event outcome - outcome hardcoded "success" (attach event). |
| stig_group_create_success_linux | GroupMgmt | Create | success | ADD_GROUP res=success / GRP_MGMT op="adding-group" / SYSCALL key=groupmod exe=groupadd success=yes. |
| stig_group_create_failure_linux | GroupMgmt | Create | failure | Same with failed/no. |
| stig_group_modify_success_linux | GroupMgmt | Modify | success | GRP_MGMT op="updating-group" / SYSCALL key=groupmod exe=groupmod success=yes. |
| stig_group_modify_failure_linux | GroupMgmt | Modify | failure | Same with failed/no. |
| stig_group_delete_success_linux | GroupMgmt | Delete | success | DEL_GROUP res=success / GRP_MGMT op="deleting-group" / SYSCALL key=groupmod exe=groupdel success=yes. |
| stig_group_delete_failure_linux | GroupMgmt | Delete | failure | Same with failed/no. |
| stig_group_membership_change_success_linux | GroupMgmt | MembershipChange | success | USER_MGMT op=adding/removing-user-to/from-group + SYSCALL key=groupmod exe=gpasswd or usermod -G/-aG/-g success=yes. |
| stig_group_membership_change_failure_linux | GroupMgmt | MembershipChange | failure | Same with failed/no. |
| stig_health_audit_storage_warning_linux | Health | AuditStorage75 | failure | auditd DAEMON_RESOURCE + DAEMON_END reason=disk-full + syslog "disk_full_action / audit_log_full / remaining space is low / No space left on device". Primary log-driven panel per spec. |
| stig_health_audit_processing_failure_linux | Health | AuditProcessingFailure | failure | DAEMON_ABORT, ANOM_ABEND exe=auditd, kernel "audit: backlog limit exceeded" / "audit: lost messages" / "auditd: Audit daemon log" messages. |
