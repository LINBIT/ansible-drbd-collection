drbd_install
============

Install DRBD kernel module and userspace tools.

Requirements
------------

None.

Role Variables
--------------

| Variable | Default | Description |
|---|---|---|
| `drbd_install_firewall_rules` | `true` | Manage firewall rules for DRBD ports; set `false` to skip |
| `drbd_install_firewall_ports` | `7000-8000/tcp` | Ports to open in firewalld or UFW for DRBD replication |

See `defaults/main.yml` and `vars/` for additional variables.

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: Install DRBD
  hosts: all
  become: true
  tasks:
    - ansible.builtin.import_role:
        name: linbit.drbd.drbd_install
```

License
-------

MIT

Author Information
------------------

[LINBIT](https://linbit.com)
