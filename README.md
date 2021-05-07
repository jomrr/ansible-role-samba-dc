# ansible-role-samba-dc

![GitHub](https://img.shields.io/github/license/jam82/ansible-role-samba-dc) [![Build Status](https://travis-ci.org/jam82/ansible-role-samba.svg?branch=main)](https://travis-ci.org/jam82/ansible-role-samba)

**Ansible role for setting up samba.**

## Supported Platforms

- Debian 11

## Requirements

Ansible 2.9 or higher.

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
# role: ansible-role-samba-dc
# file: site.yml

- hosts: samba_dcs
  become: true
  gather_facts: true
  vars:
    samba_dc_role_enabled: true
  roles:
    - role: ansible-role-samba-dc
```

## License and Author

- Author:: [jam82](https://github.com/jam82/)
- Copyright:: 2021, [jam82](https://github.com/jam82/)

Licensed under [MIT License](https://opensource.org/licenses/MIT).
See [LICENSE](https://github.com/jam82/ansible-role-samba-dc/blob/master/LICENSE) file in repository.

## References

- [ArchWiki](https://wiki.archlinux.org/)
