# ansible-role-samba-dc

![GitHub](https://img.shields.io/github/license/jomrr/ansible-role-samba-dc) [![Build Status](https://travis-ci.org/jomrr/ansible-role-samba-dc.svg?branch=main)](https://travis-ci.org/jomrr/ansible-role-samba-dc)

**Ansible role for setting up samba as active directory domain controller.**

## Supported Platforms

- Archlinux
- Debian 10, 11
- Fedora 33, 34
- Ubuntu 18.04, 20.04

## Requirements

- Ansible 2.9 or higher
- python netaddr on ansible controller

## Variables

Variables and defaults for this role.

### defaults/main.yml

```yaml
---
---
# role: ansible-role-samba-dc
# file: defaults/main.yml

# The role is disabled by default, so you do not get in trouble.
# Checked in tasks/main.yml which uses a tasks block if enabled.
samba_dc_role_enabled: false

# Provision a DC or join an existing AD.
# true = provision, false=join.
# Set this to false, to join an additional DC to an AD Domain.
samba_dc_provision: true

# Location of the kerberos system keytab file.
# If not empty, then the following smb.conf parameters are set:
#   - kerberos method = secrets and keytab
#   - dedicated keytab file = {{ samba_dc_keytab_file }}
# Otherwise these parameters are omitted in smb.conf.
samba_dc_keytab: true

# Use RFC2307 AD Schema Extensions / NIS Extensions.
# see: https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller#Parameter_Explanation
samba_dc_rfc2307: true

# This uses the ansible fact ansible_domain as dns domain and realm by default.
# Upper and lowercase conversion is done automatically as needed.
# E.g. if samba_dc_dns_domain: "home.example.com" then
# realm = HOME.EXAMPLE.COM
# workgroup = HOME
# samba domain = HOME
# see: https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller#Parameter_Explanation
samba_dc_dns_domain: "{{ ansible_domain | default('samdom.localdomain') }}"

# Enable an AD site,
# see https://wiki.samba.org/index.php/Active_Directory_Sites.
# AFFECTS: samba_dc_role=domain controller and samba_prvision=true.
samba_dc_site: ""

# Interfaces to bind to. NOTE: loopback must be included on DCs.
samba_dc_interfaces: "{{ ansible_interfaces }}"
#  - eno0
#  - br-dmz

# Password of Active Directory Administrator account.
samba_dc_admin_password: "Chang3MeN0w!"

# Samba DNS Backend to use in provision command.
# can be SAMBA_INTERNAL or BIND9_DLZ.
# see: https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller#Setting_up_the_AD_DNS_back_end
samba_dc_dns_backend: "SAMBA_INTERNAL"

# Additional Nameserver used in /etc/resolv.conf, must be another DC or empty.
# Add DC Master here, if you JOIN an additional DC.
samba_dc_nameserver: ""

# DNS Forwarder on domain controllers.
# Setting a public DNS Server here is not a good idea.
# Use a local caching resolver instead to prevent flooding public dns servers
# with local domain queries. dnsmasq is easy to setup for this.
# The first entry also gets added to /etc/resolv.conf as a fallback,
# the rest goes to smb.confs dns_forwarders setting.
samba_dc_dns_forwarders:
  - "9.9.9.9"

# Dictionary for smb.conf variables.
# These will be added to the respective section in /etc/samba/smb.conf.
# For existing settings see templates/smb.conf.j2.
# STRUCTURE: { section1: [ "setting1", "setting2", ...] }, { section2: [...] }
# EXAMPLE:
# samba_dc_smb_conf:
#   global:
#     - "template shell = /bin/bash"
#     - "template homedir = /home/%D/%U"
#     - "tls enabled = yes"
#     - "tls keyfile = /etc/samba/tls/dc1.samdom.localdomain.key"
#     - "tls certfile = /etc/samba/tls/dc1.samdom.localdomain.crt"
#     - "tls cafile = /etc/samba/tls/samdom_ca.crt"
#     - "log level = 1 auth_json_audit:3@/var/log/samba/samba_audit.log"
#     - "full_audit:failure = none"
#     - "full_audit:success = pwrite write rename"
#     - "full_audit:prefix = IP=%I|USER=%u|MACHINE=%m|VOLUME=%S"
#     - "full_audit:facility = local7"
#     - "full_audit:priority = NOTICE"
#   netlogon:
#     - "vfs objects = dfs_samba4, acl_xattr, full_audit"
#   sysvol:
#     - "vfs objects = dfs_samba4, acl_xattr, full_audit"
samba_dc_smb_conf: {}

# Password settings for domain.
samba_dc_pwd_complexity: "on"
samba_dc_pwd_min_age: 1
samba_dc_pwd_max_age: 768
samba_dc_pwd_min_len: 12
samba_dc_pwd_history_len: 10

# Set the no_log option, when using samba-tool with --username and --password
samba_dc_no_log: true
```

## Dependencies

### ansible-role-hosts

Ensure with `hosts_ip_static: true` that the `127.0.1.1` dhcp pseudo-address from `/etc/hosts` gets replaced with the current ip addresses.

Also set `hosts_domain: 'samdom.localdomain'` so `hostname -f` shows the hosts fqdn.

>If you have a working dns setup already, you can skip this dependency.

## Example Playbook

```yaml
---
# role: ansible-role-samba-dc
# file: site.yml

- hosts: samba_dcs
  become: true
  gather_facts: true
  vars:
    hosts_role_enabled: true
    # set current ip addresses for hosts interfaces
    hosts_ip_static: true
    # set dns domain, if dns is not configured
    hosts_domain: 'samdom.localdomain'
    samba_dc_role_enabled: true
    samba_dc_dns_domain: 'samdom.localdomain'
  roles:
    - role: ansible-role-samba-dc
```

## License and Author

- Author:: [jomrr](https://github.com/jomrr/)
- Copyright:: 2021, [jomrr](https://github.com/jomrr/)

Licensed under [MIT License](https://opensource.org/licenses/MIT).
See [LICENSE](https://github.com/jomrr/ansible-role-samba-dc/blob/master/LICENSE) file in repository.

## References

- [ArchWiki - Samba/Active Directory domain controller](https://wiki.archlinux.org/title/Samba/Active_Directory_domain_controller)

- [SambaWiki](https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller)

- [oO.o’s Fedora 30 Samba AD DC Guide](https://forum.level1techs.com/t/oo-os-fedora-30-samba-ad-dc-guide/149475)

- [Tranquil.it](https://dev.tranquil.it/samba/en/samba_config_server/samba_active_directory_conf.html)
