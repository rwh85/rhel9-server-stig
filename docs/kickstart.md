# Server Kickstart Guide

## Overview

The kickstart template at `kickstart/rhel9-server-ks.cfg` provisions a headless RHEL 9 server with STIG compliance baked in from install.

## Before You Use It

Replace all `CHANGEME` values:

1. **Root password hash:**
   ```bash
   python3 -c "import crypt; print(crypt.crypt('your-password', crypt.mksalt(crypt.METHOD_SHA512)))"
   ```

2. **User password hash:** Same method as above for `stigadmin`.

3. **LUKS passphrase:** Replace `CHANGEME_LUKS_PASSPHRASE` with a strong passphrase.

## Partition Layout

Server-optimized for hosting repos and Docker:

| Mount | Size | Purpose |
|-------|------|---------|
| `/` | 20 GB | Root filesystem |
| `/home` | 5 GB | User home (minimal on server) |
| `/tmp` | 5 GB | Temporary files |
| `/var` | 40 GB | **RPM repos, packages, logs** |
| `/var/log` | 10 GB | System logs |
| `/var/log/audit` | 5 GB | Audit logs |
| `/var/tmp` | 2 GB | Persistent temp |
| `/var/lib/docker` | 20 GB | Docker storage |
| swap | 4 GB | Swap space |

**Minimum disk:** ~120 GB

## Key Differences from Desktop Kickstart

| Setting | Desktop | Server |
|---------|---------|--------|
| Profile | `stig_gui` | `stig` |
| Packages | `@gnome-desktop` | `@core` only |
| Display | GUI | `text` + `skipx` |
| `/var` | 10 GB | 40 GB |
| `/var/lib/docker` | — | 20 GB |
| `/home` | 10 GB | 5 GB |

## Post-Install

The `%post` section:
1. Initializes AIDE database
2. Enables core services (auditd, firewalld, chronyd, etc.)
3. Sets default target to `multi-user.target`
4. Deploys DoD login banner
5. Sets FIPS crypto policy
6. Creates repo directory structure
7. Installs guest agent (QEMU/Proxmox)

## Customization

- **Disk device:** Change `sda` to match your hardware (e.g., `nvme0n1`, `vda`)
- **Network:** Switch from DHCP to static if needed
- **Hostname:** Update `cypher-server-01` to your naming convention
- **Partition sizes:** Adjust based on available disk space
