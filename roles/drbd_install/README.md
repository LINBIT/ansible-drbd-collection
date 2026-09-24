# drbd_install

Install DRBD kernel module and userspace tools.
Rerunning the role skips package installation when DRBD is already installed.
Set `drbd_install_package_state: latest` to upgrade DRBD packages on reruns.

## Requirements

None.

## Role variables

| Variable | Default | Description |
|---|---|---|
| `drbd_install_package_state` | `present` | Package state: `latest` installs or upgrades, `present` installs only |
| `drbd_install_module_version` | `""` | Pin the DRBD module package to a version, for example `9.2.17`; also locks it against upgrades |
| `drbd_install_force_dkms` | `false` on the Red Hat family, `true` on Oracle Linux 9+ | Use `drbd-dkms` instead of prebuilt `kmod-drbd` on the Red Hat family (see [DKMS on the Red Hat family](#dkms-on-the-red-hat-family)) |
| `drbd_install_drbdproxy` | `false` | Optionally install DRBD Proxy alongside the DRBD stack |
| `drbd_install_spaas` | `true` | Let the DKMS build fetch compat patches from `spaas.drbd.io`; set `false` on air-gapped hosts so the build fails fast into the local `spatch` fallback |
| `drbd_install_firewall_rules` | `true` | Manage firewall rules for DRBD ports; set `false` to skip |
| `drbd_install_firewalld_zone` | `""` | firewalld zone to open the DRBD replication ports in; empty uses the default zone, set it when a zone is bound to a source; ignored by UFW |
| `drbd_install_firewall_ports` | `7000-8000/tcp` | Ports to open in firewalld or UFW for DRBD replication |
| `drbd_install_force_reconfigure` | `false` | Force the install and configure block to rerun when DRBD 9 is present, reapplying the firewall, module, and package lock state |
| `drbd_install_mask_shutdown_service` | `false` | Mask `drbd-graceful-shutdown.service`; set `true` for Pacemaker-managed stacks |

See `defaults/main.yml` and `vars/` for additional variables.

### DKMS on the Debian family

On the Debian family (Debian, Ubuntu, Proxmox VE), the role installs the kernel headers matching the running kernel's flavor or edition (for example `linux-headers-cloud-amd64` on Debian cloud images or `linux-headers-aws` on Ubuntu AWS kernels) so DKMS can build DRBD for the booted kernel.
Cloud and specialized kernels are handled automatically.

The `drbd-dkms` package ships pre-generated compat patches for common distribution kernels.
When the running kernel is not covered, the DKMS build fetches a patch from LINBIT's spatch-as-a-service at `spaas.drbd.io`.
If the package install fails, for example on a host without internet access, the role installs `coccinelle` and reruns the install so DKMS can generate the patch with a local `spatch`.
If the build still fails, the role fails with the package manager error.
On air-gapped hosts, set `drbd_install_spaas: false` so the build skips the service instead of waiting for the connection to time out on every attempt.
Debian 13 packages coccinelle 1.3, which builds DRBD. Older releases ship coccinelle 1.1.1, which might not.

### DKMS on the Red Hat family

`drbd_install_force_dkms` installs `drbd-dkms` instead of the prebuilt `kmod-drbd`.
It automatically installs EPEL, enables the CRB/PowerTools repository, and pulls the `kernel-devel` package matching the running kernel (or `kernel-uek-devel` on UEK), plus `perl-interpreter`, which the DRBD compat prep step needs and the `drbd-dkms` RPM does not require.
On UEK kernels, the role also installs `kernel-uek-modules` for the running kernel, because DRBD links against the `lru_cache` module that package ships.
The default is `true` on Oracle Linux 9+ because LINBIT does not ship kmod packages for that distribution.
Debian and Ubuntu always use DKMS regardless of this flag.

EPEL 9 and EPEL 10 do not package coccinelle, and EPEL 8 ships coccinelle 1.0.7, which is too old to build DRBD, so the local `spatch` fallback is not available for the Red Hat OS family.
When the DKMS build fails, the role fails with the tail of the DKMS `make.log` so the cause, for example an unreachable `spaas.drbd.io`, is visible in the play output.
Hosts that cannot reach `spaas.drbd.io` need a kernel that the shipped compat cache covers, or the prebuilt `kmod-drbd` package where LINBIT provides one.

## Dependencies

None.

## Example playbook

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

## License

MIT

## Author information

[LINBIT](https://linbit.com)
