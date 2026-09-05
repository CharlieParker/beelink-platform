# Bootstrap Playbook

This directory contains a single Ansible playbook that bootstraps a fresh BeeLink
DevOps Platform node with the core CLI tools needed for local development and
cluster provisioning.

It installs:

- Docker
- kubectl, k3d, Helm
- Terraform
- AWS CLI v2 and LocalStack
- Argo CD CLI
- Trivy and Checkov
- Supporting system packages

The playbook is **idempotent** and safe to re-run on a clean Ubuntu system.

---

## Why this playbook exists

This bootstrap was originally one monolithic play. Refactored into roles
2026-09-05 (Rung 1): `roles/common` (generic host prerequisites), `roles/docker`
(Docker install and the apt-repo/dpkg cleanup it historically needed),
`roles/k8s_tools` (kubectl, k3d, Argo CD CLI), `roles/cli_tools` (Helm, AWS
CLI v2), `roles/hashicorp` (Terraform, LocalStack), `roles/security_tools`
(Trivy, Checkov) and `roles/updates` (patch status, Ubuntu Pro/ESM state,
plus opt-in pinned-tool freshness and support/EOL reporting — see below).
Only the final cross-cutting verification loop stays as a plain task in
`bootstrap.yml`, since it checks everything every role installed rather than
belonging to any one of them.

Helm, AWS CLI v2, LocalStack and Checkov were all fixed to be properly
version-pinned as part of this pass — all four previously always installed
"whatever's currently latest" with no way to pin or verify a specific
version, the same bug already found and fixed in kubectl/k3d/Terraform/Argo
CD. Trivy remains deliberately unpinned (tracks its own apt repo).

## Running this playbook

`ansible.cfg` lives at the repo root (`beelink-platform/ansible.cfg`) and
supplies the inventory path and output formatting, so run from there with no
`-i` flag needed:

```
ansible-playbook ansible/bootstrap/bootstrap.yml -K
```

(Moved here 2026-09-05 — it previously sat in `ansible/`, one level away
from both the inventory it points to and the repo root everything else is
run from, so it was silently never being picked up.)

## Checking for stale pins

`roles/updates` can report whether any pinned tool has a newer release
available, and what each pin's support/EOL window looks like, by querying
GitHub/PyPI/HashiCorp/`endoflife.date`. This is opt-in — a plain run never
makes these calls, since a restricted network may not permit them at all:

```
ansible-playbook ansible/bootstrap/bootstrap.yml -K --tags version_check
```

Note `--tags version_check` on its own only runs *tagged* tasks, skipping
the rest of the play — fine for just checking freshness, but not a normal
provisioning run.

Considered and rejected: pulling in existing Galaxy roles/collections (e.g.
`geerlingguy.docker`) instead of hand-rolling `roles/docker`. That role's
value is mostly in setting up Docker Inc's own apt repo for `docker-ce` — the
opposite of this playbook's deliberate choice to install the distro `docker.io`
package to avoid containerd conflicts with k3d (see
`docs/digi2al-dna-prep.md` §6). Using it would mean overriding most of what it
does for no real simplification, so the roles here stay hand-rolled,
consistent with the rest of the playbook.

---

## Linting

This directory uses a relaxed `.ansible-lint` configuration because bootstrap
tasks often require:

- direct binary downloads  
- shell installers  
- imperative setup steps  

The relaxed rules apply **only** here.

---

## When to use

Run this playbook when:

- provisioning a new BeeLink development node  
- rebuilding after an OS reinstall  
- ensuring all core tooling is present and working  

It does **not** deploy applications or configure clusters.

---

## Verification

At the end of the run, each installed tool is checked using its `--version`
command to confirm the environment is ready for use.
