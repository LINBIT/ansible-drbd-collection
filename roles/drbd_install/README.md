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
| `drbd_install_module_version` | `""` | Pin the DRBD module package to a version, for example `9.2.17`; also locks it against upgrades |
| `drbd_install_force_dkms` | `false` on most RHEL-family, `true` on Oracle Linux 9+ | Use `drbd-dkms` instead of prebuilt `kmod-drbd` on RHEL-family (see [DKMS on RHEL-family](#dkms-on-rhel-family)) |
| `drbd_install_drbdproxy` | `false` | Optionally install DRBD Proxy alongside the DRBD stack |
| `drbd_install_firewall_rules` | `true` | Manage firewall rules for DRBD ports; set `false` to skip |
| `drbd_install_firewall_ports` | `7000-8000/tcp` | Ports to open in firewalld or UFW for DRBD replication |
| `drbd_install_force_reconfigure` | `false` | Force the install/configure block to re-run when DRBD 9 is present, re-asserting firewall, module, and package-lock state |
| `drbd_install_mask_shutdown_service` | `false` | Mask `drbd-graceful-shutdown.service`; set `true` for Pacemaker-managed stacks |

See `defaults/main.yml` and `vars/` for additional variables.

DKMS on the Debian family
-------------------------

On the Debian family (Debian, Ubuntu, Proxmox VE), the role installs the kernel headers matching the running kernel's flavor or edition (for example `linux-headers-cloud-amd64` on Debian cloud images or `linux-headers-aws` on Ubuntu AWS kernels) so DKMS can build DRBD for the booted kernel.
Cloud and specialized kernels are handled automatically.

DKMS on RHEL-family
-------------------

`drbd_install_force_dkms` installs `drbd-dkms` instead of the prebuilt `kmod-drbd`.
It automatically installs EPEL, enables the CRB/PowerTools repository, and pulls the unversioned `kernel-devel` (or `kernel-uek-devel` on UEK).
It defaults to `true` on Oracle Linux 9+ because LINBIT does not ship kmod packages for that distribution.
Debian and Ubuntu always use DKMS regardless of this flag.

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
