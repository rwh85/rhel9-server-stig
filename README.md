# RHEL 9 Server STIG Hardening

Ansible-based STIG hardening for RHEL 9 infrastructure servers. Air-gapped hub server that serves STIG-hardened desktop clients.

## Architecture

| Component | Purpose |
|-----------|---------|
| `rhel9_stig_base` | Core STIG hardening (headless, no GUI) |
| `rpm_mirror` | Local RPM repository hosting (httpd + createrepo) |
| `ntp_server` | Chrony server mode for client networks |
| `docker` | Docker CE with hardened daemon configuration |
| `ansible_node` | Ansible control node for managing clients |

**SCAP Profile:** `stig` (not `stig_gui` — headless server)
**Firewall Default Zone:** `drop`
**Auth Model:** IDAM (username/password, no CAC/smartcard)
**Faillock:** `unlock_time = 900` (15 min auto-unlock, not permanent)

## Quick Start

```bash
# Install Galaxy dependencies
ansible-galaxy collection install -r requirements.yml

# Copy and edit inventory
cp inventory/hosts.example inventory/hosts
vim inventory/hosts

# Set deploy-time variables
vim inventory/group_vars/all.yml

# Run hardening
ansible-playbook -i inventory/hosts playbooks/harden.yml
```

## Role Toggles

Each role is independently toggleable in `inventory/group_vars/all.yml`:

| Variable | Default | Description |
|----------|---------|-------------|
| `server_rpm_mirror_enabled` | `true` | Local RPM repository hosting |
| `server_ntp_enabled` | `true` | Chrony NTP server mode |
| `server_docker_enabled` | `true` | Docker CE + hardening |
| `server_ansible_node_enabled` | `true` | Ansible control node |

## Deploy-Time Variables

These **must** be set for your environment. See [docs/deploy-variables.md](docs/deploy-variables.md) for full reference.

| Variable | Burns Rules | Description |
|----------|-------------|-------------|
| `stig_rsyslog_remote_server` | 5 | Central syslog server |
| `stig_chrony_servers` | 2 | NTP server list |
| `stig_dns_servers` | 2 | DNS server list |
| `stig_grub_password_hash` | 2 (CAT I) | GRUB bootloader password |
| `stig_smartcard_auth` | 6 | CAC/smartcard (false = IDAM) |

## STIG Exceptions

See [docs/poam.md](docs/poam.md) for documented POA&M items including:
- Smartcard/CAC exceptions (IDAM architecture)
- Docker container runtime exceptions
- httpd service for RPM mirror
- NTP server firewall rules

## Kickstart

Server kickstart template at `kickstart/rhel9-server-ks.cfg`. See [docs/kickstart.md](docs/kickstart.md).

Key differences from desktop:
- `text` + `skipx` (no GUI)
- `stig` profile (not `stig_gui`)
- Larger `/var` (40 GB for repos)
- Dedicated `/var/lib/docker` (20 GB)

## Related

- [rhel9-desktop-stig](../rhel9-desktop-stig/) — Sibling repo for desktop clients
