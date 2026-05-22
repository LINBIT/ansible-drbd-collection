# LINBIT DRBD Collection

The `linbit.drbd` Ansible collection for installing and configuring [DRBD®](https://linbit.com/drbd/).

## Requirements

- ansible-core 2.16 or newer

## Installation

Install directly from GitHub using `requirements.yml` until LINBIT publishes to Ansible Galaxy:

```yaml
# requirements.yml
collections:
  - name: linbit.common
    source: https://github.com/LINBIT/ansible-common-collection.git
    type: git
  - name: linbit.drbd
    source: https://github.com/LINBIT/ansible-drbd-collection.git
    type: git
```

```bash
ansible-galaxy collection install -r requirements.yml
```

To upgrade to the latest commits on the default branch:

```bash
ansible-galaxy collection install -r requirements.yml --upgrade
```

See [using Ansible collections](https://docs.ansible.com/ansible/latest/collections_guide/) for more details.

## Roles

| Role | Description |
|---|---|
| [`drbd_install`](roles/drbd_install/README.md) | Installs DRBD kernel module, userspace packages, and opens replication firewall ports |

## Licensing

This collection is licensed under the MIT License.

See [LICENSE](LICENSE) for the full text.

## Authors

Created in 2026 by [Ryan Ronnander](https://github.com/ryan-ronnander) on behalf of [LINBIT](https://linbit.com).

Inspired by pre-collection Ansible contributions from [Matt Kereczman](https://github.com/kermat), [Ryan Ronnander](https://github.com/ryan-ronnander), [Michael Troutman](https://github.com/emteelb), and [Devin Vance](https://github.com/dvance).
