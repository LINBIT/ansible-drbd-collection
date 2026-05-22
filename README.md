# Ansible Collection - linbit.drbd

Installs the DRBD kernel module and userspace tools.
DRBD is the foundational block replication layer used by LINSTOR and DRBD Reactor.

## Roles

| Role | Description |
|---|---|
| `drbd_install` | Installs DRBD kernel module, userspace packages, and opens replication firewall ports |

## Dependencies

| Collection | Purpose |
|---|---|
| `ansible.posix` | firewalld management |
| `community.general` | package management |

## Licensing

This collection is licensed under the MIT License.

See [LICENSE](LICENSE) for the full text.
