# BeeLink DevOps Platform

Used to test DevOps tools and techniques on a BeeLink mini‑PC.

## Running the bootstrap

The bootstrap installs core tooling (Docker, kubectl, k3d, Terraform, Trivy, Checkov, AWS CLI, etc.) and verifies the installation at the end.

Update `inventory/hosts.ini` with your BeeLink’s host and user, then run:
```bash
ansible-playbook -i inventory/hosts.ini ansible/bootstrap/bootstrap.yml
```
