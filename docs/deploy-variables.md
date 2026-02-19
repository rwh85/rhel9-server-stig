# Deploy-Time Variable Reference

All variables are defined in `inventory/group_vars/all.yml`.

## Role Toggles

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `server_rpm_mirror_enabled` | bool | `true` | Enable RPM mirror role |
| `server_ntp_enabled` | bool | `true` | Enable NTP server role |
| `server_docker_enabled` | bool | `true` | Enable Docker role |
| `server_ansible_node_enabled` | bool | `true` | Enable Ansible node role |

## Infrastructure (MUST set at deploy time)

| Variable | Type | Default | Burns Rules | Description |
|----------|------|---------|-------------|-------------|
| `stig_rsyslog_remote_server` | string | `""` | 5 | Central syslog server hostname/IP |
| `stig_rsyslog_port` | string | `"514"` | — | Syslog port |
| `stig_rsyslog_protocol` | string | `"tcp"` | — | Syslog protocol |
| `stig_chrony_servers` | list | `[]` | 2 | NTP server hostnames/IPs |
| `stig_chrony_maxpoll` | int | `16` | — | Max polling interval |
| `stig_dns_servers` | list | `[]` | 2 | DNS server IPs |
| `stig_dns_search_domain` | string | `""` | — | DNS search domain |
| `stig_grub_password_hash` | string | `""` | 2 (CAT I) | GRUB password hash from `grub2-mkpasswd-pbkdf2` |
| `stig_grub_superuser` | string | `"root"` | — | GRUB superuser name |
| `stig_smartcard_auth` | bool | `false` | 6 | Enable CAC/smartcard (false = IDAM) |

## NTP Server

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `stig_ntp_allow_networks` | list | `["192.168.0.0/16"]` | CIDRs allowed to query NTP |

## Authentication

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `cypher_password_min_length` | int | `15` | Minimum password length |
| `cypher_lockout_attempts` | int | `3` | Failed attempts before lockout |
| `cypher_lockout_time` | int | `900` | Lockout duration (seconds). **900 = 15 min auto-unlock** |
| `cypher_lockout_interval` | int | `900` | Fail counting interval |
| `cypher_password_max_days` | int | `60` | Max password age |
| `cypher_ssh_admin_user` | string | `"stigadmin"` | SSH admin username |
| `cypher_ssh_admin_password` | string | (see all.yml) | SSH admin password |

## RPM Mirror

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `rpm_mirror_base_path` | string | `/var/repos` | Base path for repositories |
| `rpm_mirror_repos` | list | (see all.yml) | Repository definitions |
| `rpm_mirror_server_name` | string | `{{ ansible_fqdn }}` | httpd ServerName |

## Docker

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `docker_firewall_ports` | list | `["2376/tcp"]` | Ports to open in firewall |
| `docker_storage_driver` | string | `"overlay2"` | Docker storage driver |
| `docker_log_driver` | string | `"journald"` | Docker log driver |
| `docker_registry_mirror` | string | `""` | Local registry mirror URL |

## Ansible Node

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `ansible_node_user` | string | `"ansible"` | Ansible automation user |
| `ansible_node_ee_image` | string | `"ghcr.io/rwh85/ansible-execution-env:latest"` | Execution environment image |
| `ansible_node_local_registry` | string | `""` | Local registry for air-gap |
