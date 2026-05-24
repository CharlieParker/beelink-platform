# Bootstrap Playbook

This directory contains the bootstrap playbook used to prepare a fresh BeeLink
DevOps Platform node with the core CLI tools required for development and local
cluster provisioning.

The playbook installs:

- Docker Engine
- kubectl, k3d, Helm
- Terraform
- AWS CLI v2
- LocalStack
- Argo CD CLI
- Trivy and Checkov
- Supporting system packages

It is idempotent and safe to re-run.

---

## Relaxed linting rules

This playbook uses a local `.ansible-lint` configuration with **more relaxed
rules** than the rest of the repository.

Reason: bootstrap tasks are intentionally **pragmatic and imperative**. They may:

- use shell installers where no module exists  
- download binaries directly  
- run one-off setup commands  
- prioritise reliability over strict style rules  

The relaxed linting applies **only** to this directory.

---

## When to use this playbook

Run this playbook when:

- provisioning a new BeeLink development node  
- rebuilding a machine after OS reinstall  
- ensuring all core tooling is installed and working  

It does not configure applications or deploy workloads.

---

## Verification

At the end of the run, the playbook verifies each installed tool by running its
`--version` command. This provides a quick confirmation that the environment is
ready for use.
