Sanity test ignore-list rationale
=================================

This collection is pure Ansible roles (no Python modules or plugins).
`ansible-test sanity` passes cleanly without any ignore entries.
This directory exists as scaffolding so a future Python module addition
has a place to land its `ignore-X.Y.txt` file.

Run from the collection root:

```sh
ansible-test sanity
```
