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

This bootstrap was originally one monolithic play. It's being refactored
in place into roles (Rung 1, started 2026-09-05): `roles/common` (generic host
prerequisites), `roles/docker` (Docker install and the apt-repo/dpkg cleanup
it historically needed), `roles/k8s_tools` (kubectl, k3d, Argo CD CLI) and
`roles/cli_tools` (Helm, AWS CLI v2) have landed so far. Terraform, LocalStack,
Trivy, Checkov, verification and patch-status reporting are still plain tasks
in `bootstrap.yml`, moving into their own roles (`hashicorp`, `security_tools`,
`updates`) incrementally.

Helm and AWS CLI v2 were also fixed to be properly version-pinned as part of
this pass — both previously always installed "whatever's currently latest"
with no way to pin or verify a specific version, the same bug already found
and fixed in kubectl/k3d/Terraform/Argo CD.

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
