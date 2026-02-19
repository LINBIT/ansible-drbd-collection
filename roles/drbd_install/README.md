drbd_install
============

Install DRBD kernel module and userspace tools.

Requirements
------------

None.

Role Variables
--------------

See `defaults/main.yml` and `vars/`.

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
    - ansible.builtin.include_role:
        name: linbit.drbd.drbd_install
```

License
-------

MIT

Author Information
------------------

[LINBIT](https://linbit.com)
