# BeeLink DevOps Platform

Used to test DevOps tools and techniques on a BeeLink mini‑PC.

## Setup (one-time)

This repo uses a single `.venv` at the repo root for everything — running
`bootstrap.yml`, `ansible-lint`, and Molecule. There's no separate
control-node environment; one Ansible/Python setup covers both, since this
project is just bootstrapping home-lab machines and testing that bootstrap
against VMs, not running mixed toolchains against different targets.

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
ansible-galaxy collection install -r requirements.yml
```

- `requirements.txt` — pins the pip packages this venv needs: `ansible-core`,
  `ansible-lint`, and Molecule itself (renamed from `requirements-molecule.txt`
  2026-09-07 — `ansible-core`/`ansible-lint` are used well outside Molecule,
  so a Molecule-specific name was misleading now that this is the only venv).
- `requirements.yml` — Ansible Galaxy collections, installed separately from
  `ansible-galaxy` rather than pip. Currently just `community.general`,
  which supplies the `yaml` stdout callback `ansible.cfg` selects
  (`stdout_callback = yaml`) — it isn't bundled in `ansible-core` itself.
  Skip this and `ansible-playbook` fails immediately with `Invalid callback
  for stdout specified: yaml`, before any tasks run. See `requirements.yml`
  for why the version is pinned where it is.

Activate `.venv` (`source .venv/bin/activate`) before running `ansible-playbook`,
`ansible-lint`, or `molecule`; `deactivate` returns you to your normal shell.

## Running the bootstrap

The bootstrap installs core tooling (Docker, kubectl, k3d, Terraform, Trivy, Checkov, AWS CLI, etc.) and verifies the installation at the end.

Update `inventory/hosts.ini` with your BeeLink’s host and user, then run:
```bash
ansible-playbook -i inventory/hosts.ini ansible/bootstrap/bootstrap.yml -K
```

## Molecule tooling (local)

Molecule (Ansible role testing) uses the same `.venv` set up above — no
separate environment needed.

```bash
# Move into the molecule directory
cd ansible/bootstrap
# & run moleculte commands there. With create there is a fresh DHCP lease each run.
molecule create -s default

# Proxy/Bastion login
ssh -i ~/.ssh/id_ed25519 -o ProxyJump=ansible@<beelink-ip> ansible@<current VM address>
```

### Logging into the VM Molecule creates

The VM only exists on the Beelink's own private network (`virbr0`), so it's never directly
reachable from your laptop — `molecule login` doesn't account for this and will hang/time
out. Log in manually instead, via the Beelink as a bastion:

```bash
# Find the VM's current address — it's a fresh DHCP lease every `molecule create`, so
# this changes each run. Molecule records it here after create.yml finishes:
cat ~/.ansible/tmp/molecule.*/instance_config.yml

# SSH in via the Beelink as a bastion (-o ProxyJump). Swap in the address from above.
ssh -i ~/.ssh/id_ed25519 -o ProxyJump=ansible@192.168.1.130 ansible@<current VM address>
```
