drbd_install
============

Install DRBD kernel module and userspace tools.
Re-running the role upgrades DRBD packages when newer versions are available.
Set `drbd_install_package_state: present` to skip upgrades and only install missing packages.

Requirements
------------

None.

Role Variables
--------------

| Variable | Default | Description |
|---|---|---|
| `drbd_install_package_state` | `latest` | Package state: `latest` installs or upgrades, `present` installs only |
| `drbd_install_module_version` | `""` | Pin the DRBD module package to a specific version, for example `9.2.17` or `9.3.0`. Also locks the package in the OS package manager to prevent accidental upgrades. |
| `drbd_install_force_dkms` | `false` on most RHEL-family, `true` on Oracle Linux 9+ | Use `drbd-dkms` instead of the prebuilt `kmod-drbd` on RHEL-family distros. Automatically installs EPEL, enables CRB/PowerTools, and pulls unversioned `kernel-devel` (or `kernel-uek-devel` on UEK). Defaults to `true` on Oracle Linux 9+ because LINBIT does not ship kmod packages for that distro. Debian/Ubuntu always use DKMS regardless of this flag. |
| `drbd_install_firewall_rules` | `true` | Manage firewall rules for DRBD ports; set `false` to skip |
| `drbd_install_firewall_ports` | `7000-8000/tcp` | Ports to open in firewalld or UFW for DRBD replication |

See `defaults/main.yml` and `vars/` for additional variables.

Dependencies
------------

None.

Example Playbook
----------------

Install latest version of DRBD on any supported OS:

```yaml
- name: Install DRBD
  hosts: all
  any_errors_fatal: true
  become: true
  tasks:
    - ansible.builtin.import_role:
        name: linbit.drbd.drbd_install
```

Install specific DRBD kernel module version (locks version in package manager):

```yaml
- name: Install DRBD 9.3.1
  hosts: all
  any_errors_fatal: true
  become: true
  tasks:
    - ansible.builtin.import_role:
        name: linbit.drbd.drbd_install
      vars:
        drbd_install_module_version: '9.3.1'
```

License
-------

MIT

Author Information
------------------

[LINBIT](https://linbit.com)
