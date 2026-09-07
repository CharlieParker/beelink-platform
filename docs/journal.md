# Session journal

## 2026-09-05 — Rung 0: patching, pin bump, and a real idempotency bug

- **Done:** `apt full-upgrade` + reboot (kernel 5.15.0-181 → 191). Descoped the DHCP
  reservation deliberately (box stays powered on now; documented as a lab shortcut, not the
  professional pattern). Bumped kubectl/k3d/terraform/Argo CD pins to current, pinned Argo CD
  CLI for the first time, left Trivy deliberately unpinned. Removed `docker-buildx` from the
  Docker-Inc purge list. Added a read-only patch-status report (pending upgrades,
  reboot-required) to the playbook. Removed the stale `ai-net` Docker network manually over
  SSH.
- **Broke:** Bumping the version variables alone did nothing — kubectl/k3d/terraform's install
  tasks only checked file *existence*, not version, so the old binaries never got replaced
  until that guard logic was fixed. Argo CD's task had the same bug plus no pin at all,
  silently frozen on `v3.4.2` while claiming to track `/latest/`. A `docker-buildx` package
  name collision between the purge and install task lists caused Docker to restart on every
  single playbook run, whether or not it was needed.
- **Learned:**
  - A version pin is only as real as the check that enforces it — `creates:`/`stat.exists`
    guards check presence, not correctness, and can make a bumped variable a complete no-op.
  - `get_url`'s `force: true` does real content comparison, not just "always re-download" —
    the right idempotent primitive for "did the source at this URL actually change."
  - `unarchive` already does its own content-based idempotency; a `creates:` guard on top of
    it actively disables that, it doesn't add safety.
  - `kubectl` should stay within one minor version of the cluster it talks to; k3d's default
    k3s version is a separate, independently-moving target from k3d's own version number.
  - WSL2's NAT'd virtual network is a poor vantage point for LAN discovery (`nmap`, `.local`
    mDNS) even when point-to-point SSH works fine through it.
- **Next:** One Rung 0 item still outstanding: Ubuntu Pro/ESM enrollment — named in the
  original plan but never added to the tracked checklist until today, caught only by
  cross-checking the checklist against the plan's own wording. Confirmed (not just suspected)
  that Helm, LocalStack, and AWS CLI v2 all share Argo CD's original bug: no version pin, and
  an existence-only guard that freezes them on whatever was installed first. Also decided
  today where cross-repo tracking lives once Rung 2+ spans sibling repos: the rung ladder
  (prep doc) and this journal both stay centralized here in `beelink-platform`, since they
  need to stay private regardless of which repo does the work — sibling repos get clean
  history and no job-prep narration. Rung 1: refactor into roles + Molecule, add an `updates`
  role covering OS patches *and* tool-pin freshness, fix Helm/LocalStack/AWS CLI's pinning.

---


One entry per working session. Newest at the top.

Format: **date — what was done · what broke · what was learned · next**

---

## 2026-09-04 (late evening) — Rung 0: AI-workload cleanup + LV extend

- **Done:** Confirmed `/opt/ollama` (20 GB) and `/opt/open-webui` (889 MB) were dead
  local-LLM experiments — `ollama.service` was disabled/inactive, `docker ps -a` showed no
  containers at all — and removed both directories. Extended the root LV by `+100G`
  (100G → 200G, not the originally-planned `+300G`) via `lvextend` then `resize2fs`; `/` now
  sits at 197G total, 30G used, 158G available, with ~274 GB left unallocated in the VG.
- **Broke:** Nothing.
- **Learned:**
  - `du`'s `-x`/`--one-file-system` and `df`'s `-x`/`--exclude-type` share a letter but mean
    different things entirely — always check the man page for the specific binary, not a
    neighbouring one.
  - LVM snapshots are point-in-time *views* but variable-cost storage: copy-on-write means a
    snapshot's size grows only as blocks change on the origin after it was taken, not at
    creation time.
  - `vgs`/`pvs` report LVM-level free space (unallocated extents in the volume group); `df`
    reports filesystem-level free space. Two different layers, two different meanings of
    "free."
  - An LV is measured in extents (4 MiB units here) — `lvextend` just reassigns free extents
    from the volume group to the LV; growing live is cheap and safe, shrinking is not.
  - Chose a smaller `+100G` extend over the original `+300G` plan, now that 21 GB of the
    "used" figure turned out to be disposable — preferring to extend again later with
    evidence over guessing big upfront.
- **Next:** Remaining Rung 0 checklist — `apt full-upgrade` (reboot if required), DHCP
  reservation for `192.168.1.130`, remove the stale `ai-net` Docker network, bump the version
  pins in `bootstrap.yml` (k3d v5.9.0 first), add Charlie's SSH key to the `ansible` account.

## 2026-09-04 (evening) — Rung 0 recon

- **Done:** Wrote `CLAUDE.md` and project instructions. Removed `docs/` from `.gitignore` and
  committed. Full recon of the Beelink.
- **Broke:**
  - Ran the first recon block in local WSL instead of over SSH. Tells were `uname -r` showing
    `-microsoft-standard-WSL2`, a `brave-browser` package, and the `172.25.x` WSL NAT address.
    Fix: start any recon with `hostname; whoami; uname -r`, or use `ssh host 'commands'` so
    there is no session to lose track of.
  - Forgot the `ansible` account's password. Recovered via the `cp` account, which turned out
    to have `(ALL) NOPASSWD: ALL`. Convenient; also the biggest privilege-escalation path on
    the box.
  - k3d cannot reach Docker: daemon 29.1.3 requires API ≥1.44, k3d v5.6.0 speaks 1.43.
- **Learned:**
  - Ubuntu's guided LVM install caps the root LV — 100 GB of a 474 GB volume group here. The
    "54% full" reading was an artefact of an artificially small slice, not real pressure.
  - Volume and filesystem are separate layers: `lvextend` grows one, `resize2fs` the other.
  - `sudo -l` lists your own permitted commands, and `secure_path` explains why some binaries
    vanish under sudo.
  - Pinned is not the same as maintained. Every version pin in the playbook has rotted.
  - `sort -h` for human-readable sizes; `du -x` to stay on one filesystem.
- **Next:** Work the Rung 0 checklist in `CLAUDE.md`, starting with the `/opt` and `/usr`
  drill-down and the LV extend.

---

## 2026-09-04 (afternoon)

- **Done:** Researched Digi2al, the DNA programme and MOD Secure by Design. Produced
  `docs/digi2al-dna-prep.md` (gap analysis against the job spec + value-ordered plan).
  Agreed constraints: no AWS spend, one node, repo stays private. Added `CLAUDE.md`.
- **Broke:** Nothing yet.
- **Learned:** Digi2al's Kraken team *is* a DNA development team — 500+ Palantir Foundry
  apps for the Navy. MOD replaced certificate-based accreditation with Secure by Design
  (continuous assurance cases), which reframes pipeline gates as assurance evidence.
- **Next:** Rung 0 — recon the Beelink, then patch it.

## 2026-09-05 (later) — Rung 0 closed: Ubuntu Pro/ESM

- **Done:** Attached the Beelink to Ubuntu Pro's free personal subscription (`sudo pro
  attach`) — `esm-apps`, `esm-infra` and `livepatch` all enabled. Re-ran
  `apt update && apt full-upgrade -y`; no reboot required afterwards; `apt list --upgradable`
  came back empty. Updated the Rung 0 checklist in `CLAUDE.md` (8/8) and the status line in
  `docs/digi2al-dna-prep.md` §11 to "Rung 0 complete".
- **Broke:** Nothing.
- **Learned:** ESM-gated packages don't show up in a plain `apt update` at all until the
  subscription is attached — they're not "held", they're simply not in scope. Also confirmed
  `bootstrap.yml`'s patch-status task is read-only (reports pending count, never upgrades),
  so the actual `full-upgrade` still has to be run by hand each cycle.
- **Next:** Rung 1 — refactor `bootstrap.yml` into roles with Molecule tests, an `updates`
  role, ADR-0001 on the `ansible`/`cp` sudo situation, and the Helm/LocalStack/AWS CLI
  pinning gap. Going through it slower this time, documenting each piece as it lands rather
  than at the end.

## 2026-09-05 (later still) — ADR-0001 and ADR authorship convention

- **Done:** Wrote `docs/adr/0001-ansible-sudo-model.md` — decision: keep `ansible`'s current
  sudo model (full scope, password-gated via `-K`) unchanged. Considered and rejected
  scoping sudo to a command allow-list (doesn't work against Ansible's module-execution
  model — modules run as generated Python scripts, not fixed shell commands) and vaulted
  `become_pass` (removes the prompt but not the privilege, and relocates rather than removes
  the secret, with no current unattended-run need to justify it). Revisit trigger: the first
  time a role needs to run unattended. Added an "ADRs" section to `CLAUDE.md`: Charlie writes
  ADR content from ADR-0002 onward as deliberate practice; Claude drafted ADR-0001 only as a
  worked example and coaches rather than drafts from here.
- **Broke:** Nothing.
- **Learned:** Ansible modules execute as a generated Python script (or piped via stdin under
  pipelining), not as literal shell commands — so sudoers command-matching can't meaningfully
  scope most `bootstrap.yml` tasks; only `shell:`/`command:` tasks against fixed binaries are
  actually scopable that way.
- **Next:** Rung 1's main piece — refactor `bootstrap.yml` into roles with Molecule tests.

## 2026-09-05 (evening) — Rung 1 roles refactor + pinning complete

- **Done:** Finished refactoring `bootstrap.yml` into roles (`common`, `docker`,
  `k8s_tools`, `cli_tools`, `hashicorp`, `security_tools`, `updates`) — every task
  relocated, name-for-name diffed against the original file each step to catch
  losses (caught and fixed one: a mid-edit slip briefly deleted the Terraform and
  LocalStack tasks entirely). Fixed the Helm/LocalStack/AWS CLI v2/Checkov pinning
  gap flagged last session, same check-then-reinstall pattern as kubectl/k3d/
  Terraform/Argo CD. Deliberately hand-rolled `roles/docker` rather than pulling in
  `geerlingguy.docker` — that role's value is setting up Docker Inc's own apt repo,
  the opposite of this playbook's deliberate `docker.io` choice. Pinned Helm to the
  latest 3.x (`v3.21.4`), not 4 (released Nov 2025) — real teams are still on 3;
  its security-fix window closes 2026-11-11, revisit then. Added `roles/updates`:
  read-only patch status, Ubuntu Pro/ESM state (schema happened to match my
  best-guess exactly), and opt-in (`--tags version_check`) pinned-tool freshness +
  support/EOL reporting via GitHub/PyPI/HashiCorp/`endoflife.date` APIs — genuinely
  new capability, not just a refactor. Wrote ADR-0001 (ansible sudo model — decision:
  leave unchanged, command-scoping doesn't work cleanly against Ansible's module
  execution model, vaulted `become_pass` just relocates the secret) as a worked
  example; ADR-0002 onward are Charlie's to write. Relocated `ansible.cfg` to the
  repo root — it was one level away from the inventory and repo root everyone
  actually works from, so `stdout_callback = yaml` was silently inert. Final full
  run: `ok=55 changed=1 failed=0`.
- **Broke:** LocalStack 2026.5.0's own CLI doesn't run on this box's Python 3.10 —
  a genuine upstream `SyntaxError` (nested-quote f-string, valid only from 3.12).
  Not our bug, but a real blocker for Rung 6's actual LocalStack exercises — logged
  as Open Problem #5 in `CLAUDE.md`, worth a GitHub issue upstream at some point.
  Worked around it here by checking `pip3 show localstack` instead of the CLI.
- **Learned:** Ansible only auto-discovers `ansible.cfg` in the exact CWD, never a
  parent/child directory. Ansible modules (as opposed to `shell:`/`command:` tasks)
  execute as a generated Python script, not a literal shell command — so sudoers
  command-scoping can't meaningfully restrict them. `get_url`'s `force: true` still
  does content-based idempotency (compares the fetched bytes, only reports
  `changed` if they actually differ) — it isn't a blunt "always redownload and
  overwrite" flag. Ansible's default stdout callback prints embedded `\n` in a
  `debug` message as literal text, not a line break — only the `yaml` callback (or
  restructuring to one `debug` per loop item) renders it as real lines.
- **Next:** Molecule tests (proposal first — driver choice, what's actually being
  tested), then the PR/CI flow (branch protection, ansible-lint/yamllint in GitHub
  Actions). After that, Rung 2: the Java/React/Postgres vertical slice.

## 2026-09-06 — Molecule setup: KVM/libvirt on the Beelink, venv + scenario scaffold

- **Done:** Confirmed the Beelink supports hardware virtualization (AMD-V, all 8 cores
  reporting `svm`) and installed `qemu-kvm`/`libvirt-daemon-system`/`libvirt-clients`/
  `virtinst` there — `ansible` was already in the `libvirt` group, `libvirtd` active with
  its default `virbr0` NAT network up. Settled the driver/scenario shape: one holistic
  Molecule scenario converging all seven roles in the real `bootstrap.yml` order, against a
  delegated KVM VM created and destroyed on the Beelink itself (not the laptop) — chosen
  over LXD for maximal isolation (no shared-kernel/nested-Docker doubt for the `docker`/
  `k8s_tools` roles) and over per-role scenarios for simplicity, both acceptable trade-offs
  given this is meant as an infrequent PR-gate-style run rather than a fast inner loop.
  Built a dedicated Python venv (`.venv`, gitignored) for Molecule tooling, kept deliberately
  separate from the `ansible-core` used for everyday `bootstrap.yml` runs. Pinned
  `molecule==26.8.0`, `molecule-plugins[docker]==26.7.15`, `ansible-core==2.16.19` — the
  highest release satisfying both "Python 3.10 compatible" (this WSL venv's interpreter) and
  molecule's own `!=2.17.*` constraint, found via two failed pin attempts (2.21.3, then
  2.17.14) that each surfaced one of those constraints — and `ansible-lint==26.8.0`, added
  after discovering the venv's `ansible-core` collided with the separately `~/.local`-
  installed `ansible-lint`/2.17.14 whenever `.venv` was active (a CLI/importable-module
  version mismatch caused by `ansible-lint` reaching across environments via `PATH`; fixed
  by giving the venv its own matching `ansible-lint` instead). `requirements-molecule.txt`
  records the pins; README updated with venv setup instructions.
  Scaffolded the scenario: `molecule init scenario` (note: `--driver-name` has been removed
  from this Molecule release's CLI — it now always scaffolds the core `default` driver, the
  modern name for the old "delegated" driver, with no extra collection dependency) generated
  `ansible/bootstrap/molecule/default/` (`molecule.yml`, `converge.yml`, `create.yml`,
  `destroy.yml`, `verify.yml`) — still generic stub content, not yet written for the
  Beelink/KVM target.
- **Broke:** Nothing on the Beelink. Working-copy confusion on the laptop side: `~/dev/DNA`
  in WSL turned out to be a symlink into `/mnt/c/Users/mrpar/dev/Digi2alDNAPractice` (the
  Windows-mounted path Cowork sees), not a separate native-ext4 clone — so there's only ever
  been one copy of the repo, but everything on it (including this pip install) pays the
  WSL↔Windows filesystem-crossing tax. Left as-is for now; pointing the connected folder at
  a `\\wsl.localhost\...` UNC path instead would fix this properly but wasn't done this
  session.
- **Learned:** pip's resolver fully resolves before writing anything to disk, so a failed
  `pip install` (version-not-found or ResolutionImpossible) leaves no partial/conflicting
  state behind — safe to just retry with corrected pins. Large multi-file packages
  (`ansible-core` especially, also `pygments`) make the `/mnt/c` filesystem-crossing cost
  most visible, since it's dominated by many-small-file I/O rather than raw bytes
  transferred.
- **Next:** Write `create.yml`/`destroy.yml` to provision and tear down a KVM VM on the
  Beelink via `virt-install`/`virsh` (base image, unattended provisioning method, and how
  Molecule's dynamic inventory picks up the new VM's connection details are all still open),
  then rewrite `converge.yml` to apply the real seven-role `bootstrap.yml` order, then
  `verify.yml` assertions, then a first `molecule converge` run.


## 2026-09-07 — Molecule create.yml/destroy.yml: KVM VM provisioning, working end to end

- **Done:** Wrote `create.yml`/`destroy.yml` for the KVM/libvirt scenario scaffolded
  2026-09-06. `create.yml`: resolves the Beelink's address/user from the shared
  `inventory/hosts.ini` (single source of truth, not restated), delegates the libvirt work
  to the Beelink via `add_host` + `delegate_to`, builds a qcow2 overlay against a
  once-downloaded Ubuntu 22.04 base image, generates a NoCloud cloud-init seed ISO reusing
  the Beelink's own already-trusted SSH key (no new key material to manage), boots via
  `virt-install`, polls libvirt's own DHCP leases for the new VM's address, waits for SSH,
  and writes Molecule's instance record. `destroy.yml` mirrors the lookup/delegation setup,
  tears the VM down tolerantly (`failed_when: false`, since Molecule runs destroy both
  before create and after verify), and always resets the instance record to empty. Full
  create → login → destroy → create → login cycle proven working, including a genuine
  SSH bastion pattern (VM is only reachable from the Beelink's own `virbr0` network, never
  directly) via `ansible_ssh_common_args: -o ProxyJump=...` on the instance record — Ansible
  itself (and therefore future `converge`/`verify` runs) picks this up correctly through
  normal inventory resolution.
  Also fixed a real latent bug in the stub `molecule.yml`: `--inventory=/path/to/inventory.yml`
  was a literal placeholder pointing nowhere and would have failed every `molecule` command;
  removed, with a comment explaining why a *global* `--inventory` pointing at the real
  Beelink would be actively dangerous once `converge.yml`/`verify.yml` (which run
  `hosts: all` against the disposable VM) exist — it would merge the real Beelink into that
  `all` and risk converging straight onto it.
  Added a "Explaining terms" section to this file's working agreement (acronyms/jargon get
  spelled out unprompted, same as commands). Updated README with venv/molecule invocation
  steps and how to log into the VM manually (see below).
- **Broke (several, all found and fixed live):**
  1. `delegate_to: beelink_target` silently ran locally instead of over SSH — a play-level
     `connection: local` (correct for the localhost-only parts of `create.yml`, copied from
     Molecule's own scaffold convention) leaks into delegated tasks too unless the delegated
     host's vars explicitly set `ansible_connection: ssh`. Real gotcha, not obvious from the
     docs; cost the most debugging time.
  2. Jinja's `regex_search()` returns a **list** whenever a capture group is requested, even
     one group — `beelink_user` came back as `["ansible"]`. Fixed with a lookbehind
     (`(?<=ansible_user=)\S+`) so it returns a plain string with no group needed.
  3. Never created `/var/lib/libvirt/images/base/` — `get_url` doesn't create missing
     destination directories, so the download failed at the final "move into place" step
     (99 seconds in, i.e. after the actual download had already completed).
  4. `vm_name` referenced `molecule_scenario_name`, which — unlike `molecule_instance_config`
     — isn't actually injected into `create.yml`/`destroy.yml` by this Molecule version.
     Switched to a fixed name (`molecule-bootstrap-test`); fine given only one instance is
     ever needed.
  5. `molecule.yml` never declared a `platforms:` section at all (missing from the original
     `molecule init scenario` stub) — `molecule login`/`list` need it to know what instance
     name to look for, even though `create.yml`'s own instance-config dump was correct
     independently.
  6. `identity_file: ""` (meant as "no explicit key, fall back to agent/default") produced a
     *worse* failure than omitting the key: Molecule built a malformed SSH command
     (`ssh -i -o ControlMaster=auto ...`, `-i` swallowing the next flag as a filename).
     Needed the real path (`ssh -G ansible@192.168.1.130 | grep -i identityfile` against the
     Beelink to confirm which default-list key was actually authorized), resolved via
     Jinja's `expanduser` filter.
  7. **`molecule login` doesn't read `ansible_ssh_common_args`** — it only recognises a
     fixed set of instance-config fields (`instance`/`address`/`user`/`port`/`identity_file`)
     and silently ignores anything else, so it kept trying to connect to the VM directly and
     timing out even after the bastion routing was added and confirmed working via a manual
     `ssh -i ... -o ProxyJump=... ansible@<address>`. This is a genuine limitation of
     Molecule's convenience command, not a bug in our files — `converge`/`verify` will still
     honour it correctly since they go through full Ansible inventory resolution, not
     `login`'s narrower logic. Manual SSH (documented in the README now) is the correct way
     to poke around the VM interactively for as long as this scenario needs a bastion hop.
- **Learned:** A play-level `connection:` keyword can silently override host-level
  `ansible_connection` for delegated tasks unless the delegated host's vars restate it
  explicitly — worth remembering for any future `delegate_to` work. `ssh -G user@host` is a
  clean way to see SSH's fully-resolved config (including which identity file it would
  actually try) without connecting. `get_url` is genuinely idempotent via conditional
  HTTP (`304 Not Modified`), not just "skip if file exists" — confirmed live on the second
  `create` run. `virsh net-dhcp-leases` is a guest-agent-free way to discover a freshly
  booted VM's address.
- **Next:** `converge.yml` — apply the real seven-role `bootstrap.yml` order against the VM
  (inherits the bastion routing "for free" via inventory resolution). Then `verify.yml`
  assertions, then a first full `molecule test` run. Not started this session by choice —
  good checkpoint after `create`/`destroy` rather than pushing straight on.
