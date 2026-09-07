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

# Move into the molecule directory
cd ansible/bootstrap
# & run moleculte commands there. With create there is a fresh DHCP lease each run.
molecule create -s default

# Proxy/Bastion login
ssh -i ~/.ssh/id_ed25519 -o ProxyJump=ansible@<beelink-ip> ansible@<current VM address>
```

Activate `.venv` (`source .venv/bin/activate`) before running any `molecule` command;
`deactivate` returns you to your normal shell. This venv's `ansible-core` is
deliberately separate from whatever you use for everyday `bootstrap.yml` runs.

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
