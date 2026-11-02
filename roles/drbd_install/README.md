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
| `drbd_install_force_dkms` | `true` on the Debian OS family, `false` on the Red Hat OS family, `true` on Oracle Linux 9+ | Build the module with `drbd-dkms` instead of installing a prebuilt module package: `kmod-drbd` on the Red Hat OS family (see [DKMS on the Red Hat family](#dkms-on-the-red-hat-family)), `drbd-module-<kernel>` on Debian and Ubuntu (see [Prebuilt modules on Debian and Ubuntu](#prebuilt-modules-on-debian-and-ubuntu)); Proxmox VE always uses DKMS |
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

### Prebuilt modules on Debian and Ubuntu

The LINBIT customer repository ships a prebuilt, LINBIT-signed module package per kernel release, named `drbd-module-<kernel>` after `uname -r`, for Debian and Ubuntu on amd64 and arm64.
Set `drbd_install_force_dkms: false` to install it instead of `drbd-dkms`.
The role then skips the kernel headers, the `spatch` fallback and the DKMS checks, and pins, holds and verifies the module package the same way it does `drbd-dkms`.
The package depends on `drbd-ueficert`, which installs the LINBIT Secure Boot certificate.
Enroll the certificate with `mokutil` yourself.

> [!IMPORTANT]
> A prebuilt module covers only the running kernel, and there is no package that follows kernel upgrades.
> After a kernel upgrade and reboot, the node has no DRBD module until the role runs again and installs the package for the new kernel.
> DKMS rebuilds the module for new kernels by itself, which is why it stays the default.

LINBIT builds the packages only for some kernel flavors, and they can trail the distribution archive by several kernel releases.
If no configured repository provides `drbd-module-<kernel>` for the running kernel, or none at the version that `drbd_install_module_version` names, the role fails and lists the available versions.
It does not fall back to DKMS.
The public Ubuntu PPA and the Proxmox VE repositories carry only `drbd-dkms`, so the prebuilt module needs the customer repository, and Proxmox VE always uses DKMS regardless of the flag.

Switching an installed host between DKMS and prebuilt modules takes effect when the install block runs, for example with `drbd_install_force_reconfigure: true`.
The role installs the selected module package, then removes the package of the other build method, even when a version pin holds it.
As after any DRBD package upgrade, the loaded module stays in memory until you unload DRBD or reboot the node.

### DKMS on the Red Hat family

`drbd_install_force_dkms` installs `drbd-dkms` instead of the prebuilt `kmod-drbd`.
It automatically installs EPEL, enables the CRB/PowerTools repository, and pulls the `kernel-devel` package matching the running kernel (or `kernel-uek-devel` on UEK), plus `perl-interpreter`, which the DRBD compat prep step needs and the `drbd-dkms` RPM does not require.
On UEK kernels, the role also installs `kernel-uek-modules` for the running kernel, because DRBD links against the `lru_cache` module that package ships.
The default is `true` on Oracle Linux 9+ because LINBIT does not ship kmod packages for that distribution.

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

Install the prebuilt module package for the running kernel on Debian or Ubuntu, pinned to DRBD 9.3.3:

```yaml
- name: Install the prebuilt DRBD 9.3.3 module
  hosts: all
  any_errors_fatal: true
  become: true
  tasks:
    - ansible.builtin.import_role:
        name: linbit.drbd.drbd_install
      vars:
        drbd_install_force_dkms: false
        drbd_install_module_version: '9.3.3'
```

## License

MIT

## Author information

[LINBIT](https://linbit.com)
