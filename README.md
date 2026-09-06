# BeeLink DevOps Platform

Used to test DevOps tools and techniques on a BeeLink mini‑PC.

## Running the bootstrap

The bootstrap installs core tooling (Docker, kubectl, k3d, Terraform, Trivy, Checkov, AWS CLI, etc.) and verifies the installation at the end.

Update `inventory/hosts.ini` with your BeeLink’s host and user, then run:
```bash
ansible-playbook -i inventory/hosts.ini ansible/bootstrap/bootstrap.yml -K
```
## Molecule tooling (local)

Molecule (Ansible role testing) needs its own Python environment — it is not required
to run `bootstrap.yml` itself. Set it up once from the repo root:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements-molecule.txt
```

Activate `.venv` (`source .venv/bin/activate`) before running any `molecule` command;
`deactivate` returns you to your normal shell. This venv's `ansible-core` is
deliberately separate from whatever you use for everyday `bootstrap.yml` runs.
