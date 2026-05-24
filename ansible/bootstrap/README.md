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

This bootstrap is intentionally **pragmatic and self‑contained**. It installs
tools directly from upstream sources and uses simple, reliable steps to bring a
machine to a ready‑to‑use state quickly.

A more maintainable long‑term approach would be to replace many of these manual
install steps with existing **Ansible roles and collections**, which already
provide well‑tested installers for tools like Docker, Helm, Terraform, Trivy,
and Checkov.

This playbook remains a solid baseline until that refactor happens.

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
