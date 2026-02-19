# Plan of Action & Milestones (POA&M)

**System:** RHEL 9 Server STIG Hardening
**STIG Version:** V2R6 (October 2025)
**Last Updated:** 2026-02-19

---

## Environment-Justified Exceptions

### Smartcard / CAC Authentication (IDAM Architecture)

**Decision:** IDAM with username/password authentication. No CAC/smartcard hardware deployed.
**Variable:** `stig_smartcard_auth: false`
**Mitigation:** Strong password policy (15+ chars, complexity, lockout after 3 attempts w/ 15 min auto-unlock, 60-day rotation).

| # | STIG Rule | CAT | Status | Justification |
|---|-----------|-----|--------|---------------|
| 1 | `configure_opensc_card_drivers` | II | Exception | No smartcard hardware. IDAM auth. |
| 2 | `install_smartcard_packages` | II | Exception | No smartcard hardware. IDAM auth. |
| 3 | `sssd_certificate_verification` | II | Exception | No PKI/cert auth. IDAM. |
| 4 | `sssd_enable_certmap` | II | Exception | No cert mapping. IDAM. |
| 5 | `sssd_enable_smartcards` | II | Exception | No smartcard auth. IDAM. |

### Docker Container Runtime

**Decision:** Docker CE required for containerized services (execution environments, future registry).
**Variable:** `server_docker_enabled: true`

| # | STIG Rule | CAT | Status | Justification |
|---|-----------|-----|--------|---------------|
| 1 | `user.max_user_namespaces=0` | II | Exception | Docker requires user namespaces for userns-remap. Set to non-zero when Docker enabled. |
| 2 | Listening ports (2376/tcp) | II | Exception | Docker TLS port required for remote management. Firewall restricted. |

**Mitigation:** Hardened `daemon.json` with userns-remap, no-new-privileges, icc=false, live-restore. `container-selinux` enforced.

### httpd Service (RPM Mirror)

**Decision:** httpd serves local RPM repositories to air-gapped clients.
**Variable:** `server_rpm_mirror_enabled: true`

| # | STIG Rule | CAT | Status | Justification |
|---|-----------|-----|--------|---------------|
| 1 | HTTP/HTTPS open ports | II | Exception | Required for RPM mirror. Firewall zone=drop, only HTTP/HTTPS allowed. |
| 2 | `httpd` service running | II | Exception | Required for RPM mirror. SELinux `httpd_sys_content_t` enforced. |

### NTP Server

**Decision:** Chrony serves time to client network.
**Variable:** `server_ntp_enabled: true`

| # | STIG Rule | CAT | Status | Justification |
|---|-----------|-----|--------|---------------|
| 1 | NTP port 123 open | II | Exception | Required for NTP server function. Restricted to `stig_ntp_allow_networks`. |
| 2 | chrony `port 0` (base) | II | Override | Base role sets port=0; ntp_server role overrides to port=123. |

### Deploy-Time Infrastructure Dependencies

| # | Rules (Count) | Variable | Status When Empty |
|---|---------------|----------|-------------------|
| 1 | Rsyslog TLS forwarding (5) | `stig_rsyslog_remote_server` | Open — no central syslog |
| 2 | Chrony NTP servers (2) | `stig_chrony_servers` | Open — no NTP configured |
| 3 | DNS resolution (2) | `stig_dns_servers` | Open — no static DNS |
| 4 | GRUB bootloader password (2 CAT I) | `stig_grub_password_hash` | Open — no boot password |
| 5 | Partitioning (6) | N/A (kickstart) | Open if not installed via kickstart |

### HBSS / Endpoint Protection

**Rule:** V-258072 (CAT II)
**Status:** Exception
**Mitigation:** fapolicyd, USBGuard, firewalld drop zone.
