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

## Authors

Created in 2026 by [Ryan Ronnander](https://github.com/ryan-ronnander) on behalf of [LINBIT](https://linbit.com).

Inspired by pre-collection Ansible contributions from [Matt Kereczman](https://github.com/kermat), [Ryan Ronnander](https://github.com/ryan-ronnander), [Michael Troutman](https://github.com/emteelb), and [Devin Vance](https://github.com/dvance).
