# ansible-role-samba-dc-dc

![GitHub](https://img.shields.io/github/license/jam82/ansible-role-samba-dc-dc) [![Build Status](https://travis-ci.org/jam82/ansible-role-samba-dc.svg?branch=main)](https://travis-ci.org/jam82/ansible-role-samba-dc)

**Ansible role for setting up samba.**

## Supported Platforms

- Debian 11

## Requirements

- Ansible 2.9 or higher
- python-netaddr (Archlinux) installed on controller
- python3-netaddr (Debian, Fedora, etc.) installed on controller

## Variables

Variables and defaults for this role.

### defaults/main.yml

```yaml
samba_dc_role_enabled: false
```

## Dependencies

None.

## Example Playbook

```yaml
---
# role: ansible-role-samba-dc-dc
# file: site.yml

- hosts: samba_dcs
  become: true
  gather_facts: true
  vars:
    samba_dc_role_enabled: true
  roles:
    - role: ansible-role-samba-dc-dc
```

## License and Author

- Author:: [jam82](https://github.com/jam82/)
- Copyright:: 2021, [jam82](https://github.com/jam82/)

Licensed under [MIT License](https://opensource.org/licenses/MIT).
See [LICENSE](https://github.com/jam82/ansible-role-samba-dc-dc/blob/master/LICENSE) file in repository.

## References

- [ArchWiki - Samba/Active Directory domain controller](https://wiki.archlinux.org/title/Samba/Active_Directory_domain_controller)

- [SambaWiki](https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller)

- [oO.o’s Fedora 30 Samba AD DC Guide]([oO.o’s Fedora 30 Samba AD DC Guide](https://forum.level1techs.com/t/oo-os-fedora-30-samba-ad-dc-guide/149475))

- [Tranquil.it](https://dev.tranquil.it/samba/en/samba_config_server/samba_active_directory_conf.html)
