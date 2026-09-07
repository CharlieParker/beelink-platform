# CLAUDE.md — beelink-platform

Instructions for Claude working in this repository.

## What this is

Charlie's practice ground for an upcoming **DevOps Engineer** role at **Digi2al**, on the
Royal Navy's **DNA (Data & Navy Applications)** programme. He has accepted the role and is
waiting on SC clearance; the start date is unknown and could be a week away or months.

The goal of everything here is **his capability on day one**, not the artefacts themselves.

**Read `docs/digi2al-dna-prep.md` before proposing any substantial work.** It holds the
research on the company and programme, the gap analysis against the job spec, the agreed
constraints, and the value-ordered plan. This file is only the working agreement.

## Working agreement

- **Propose before doing.** Set out the options and the trade-offs, then wait. Do not
  produce finished work he hasn't agreed to the shape of.
- **He runs the commands.** Charlie executes things in his own terminal and pastes results
  back. That is deliberate — it is how he learns. Do not run commands against the Beelink
  on his behalf, and do not offer to.
- **Explain every command.** See the next section. This is the most important instruction
  in this file.
- **Scope to the session.** He tracks his usage window. Plan work that will actually finish
  inside the remaining budget rather than opening threads that get abandoned. If something
  won't fit, say so and propose a smaller first slice.
- **Be honest.** Say plainly when an approach is a lab shortcut rather than real practice,
  when something on the plan is low value, or when he's about to spend effort on the wrong
  thing. Agreeable but wrong is the failure mode to avoid.
- **Markdown, plain text.** New documents in Markdown. No .docx unless asked.

## Explaining commands

Every shell command offered to Charlie gets a short breakdown underneath it. The purpose is
**command-line literacy** — he should finish each session able to write the command himself
next time, not just paste it.

Format: the command in a fenced block, then bullets. Cover what the command is, what each
non-obvious flag does, and any argument worth understanding. Skip the genuinely obvious
(`ls`, `cd`), and don't pad — one clause per bullet is usually right.

**Example of the expected style:**

```bash
sudo apt full-upgrade -y
```

- `sudo` — run as root; package installation needs system-wide write access
- `apt` — Debian/Ubuntu package manager front-end
- `full-upgrade` — upgrades packages *and* will remove installed packages if that's needed
  to complete an upgrade. Plain `upgrade` never removes anything, so it silently skips
  those. On a lab box `full-upgrade` is the right default
- `-y` — assume yes to prompts. Fine here; think twice on anything production-shaped

```bash
[ -f /var/run/reboot-required ] && cat /var/run/reboot-required
```

- `[ -f PATH ]` — shell test, true if PATH exists and is a regular file
- `&&` — run the right-hand side only if the left-hand side succeeded
- `/var/run/reboot-required` — Debian/Ubuntu convention: this file's existence means a
  patched kernel or library needs a reboot to take effect

**Also worth explaining as they come up:** why a flag exists rather than only what it does,
what the output means (not just how to get it), what the dangerous variant of a command is,
and when a command is a habit worth keeping versus a one-off.

**Verbosity dial — currently: `full`.** Charlie can say "brief" (one bullet per command,
only the non-obvious parts) or "off" at any time. If he does, update this line.

## Explaining terms

Same instinct as commands: any acronym or piece of jargon gets spelled out in plain
language the first time it's used in a session — what the letters stand for (if it's an
acronym) and what the thing actually is in this context. Don't assume it's already known,
and don't wait to be asked. Once explained, it doesn't need re-explaining every time it
recurs in the same session unless he asks for a refresher.

## ADRs

- **Charlie writes ADR content from ADR-0002 onward.** It's deliberate practice — the
  judgement calls an ADR captures (what trade-off matters, what you'd defend in review) are
  the point, and no real review panel expects an AI-drafted one. Claude's role for future
  ADRs is coaching and critique (is the Context honest, does Consequences actually follow,
  should Status move from Proposed to Accepted), not drafting.
- **ADR-0001 is the one exception** — written by Claude as a worked example so Charlie had a
  concrete template before writing his own. Treat its format (Status/Date/Context/Options
  considered/Decision/Consequences) as the convention going forward.
- Live in `docs/adr/`, numbered sequentially, kebab-case filenames
  (`000N-short-title.md`).

## Learning posture

- **Explain the why, not just the how.** The mechanism matters more than the recipe.
- **Name the alternatives.** When there's a choice — k3d vs k3s, Helm vs Kustomize, Vault vs
  SOPS — say what the options are and what the trade-off is, then recommend one.
- **Distinguish lab from real.** Flag explicitly when something is a genuine industry pattern
  versus a shortcut taken because this is one Beelink on a home network. He needs to know
  which of his habits will survive contact with a real platform team.
- **Connect back to the job spec.** When an exercise evidences a named must-have or
  nice-to-have, say which one. It keeps the effort targeted.
- **Suggest what's next.** Don't wait to be asked. At the end of a piece of work, propose the
  next thing worth practising and why.
- **Let him struggle a little.** If he's debugging something, offer the next diagnostic step
  rather than the answer, unless he asks for the answer or is clearly stuck.

## Current state

**Rung 0 — Make the lab trustworthy: complete (8 of 8 checklist items, 2026-09-05).**
**Rung 1 — Make the existing work good: roles refactor + pinning complete 2026-09-05, Molecule and the PR/CI flow still open.** See the Rung 1 checklist below.
See `docs/digi2al-dna-prep.md` §11 for the full ladder.

**Ladder re-cut 2026-09-07** after a research pass on Digi2al's live job adverts, the Royal
Navy's public code and Palantir's own docs (prep doc §13, evidence and sources there). What
changed, in one line each:

- **Rung 2 shrank** from 4–5 days to 1–1.5 and became "something worth deploying" — FastAPI +
  React/TypeScript against `@royalnavy/react-component-library`. It exists to unblock Rung 3.
- **Rung 4b is new** — containerise and pipeline a Spring Boot app he did *not* write. That is
  now where the Java must-have lives, because it is a build-and-image exercise, not app dev.
- **Rung 6 retargeted** to provider-agnostic Terraform; AWS/EKS specifics demoted to a side
  exercise (the wider estate is Azure/GCP as much as AWS).
- **Rung 8's demo aims at the platform, not the app.**
- **Added alongside:** an afternoon on Foundry's free Developer Tier (open, UK in scope), and
  20 minutes reading on Tekton.

**Kubernetes, Helm, CI/CD and GitOps (Rungs 3–4) are the centre of gravity** — the only things
named in all four of Digi2al's current engineering adverts. Weight proposals accordingly.

### The Beelink, as surveyed 2026-09-04

| | |
|---|---|
| Host / access | `beelink` at `192.168.1.130`, SSH as `ansible` |
| CPU / RAM | AMD Ryzen 7 3750H, 8 threads, **13 GiB RAM**, 4 GiB swap |
| Disk | 477 GB NVMe. Root LV extended to **200 GB of a 474 GB volume group — 274 GB unallocated** |
| Disk in use | 30 GB. `/opt/ollama` (20 GB) and `/opt/open-webui` (889 MB) confirmed as old local-LLM experiments and removed 2026-09-04 |
| Network | Wi-Fi on `wlp3s0`. `eno1` exists but is unplugged, and the router has no spare ports. Address is DHCP (not reserved) — deliberately left that way, see checklist |
| OS | Ubuntu 22.04.5, kernel 5.15.0-181-generic. `unattended-upgrades` enabled, but the box is often powered off so patches lag |
| Cluster state | **None.** No k3d cluster has ever been built. Only a `hello-world` image. Stale `ai-net` Docker bridge left over |

### Open problems

1. ~~k3d is broken~~ **Resolved 2026-09-05.** k3d bumped to v5.9.0 (Docker 29.1.3 needs API
   ≥1.44; old k3d v5.6.0 only spoke 1.43).
2. **The pins had rotted, and — worse — bumping them didn't work.** kubectl, k3d, and
   Terraform's install tasks only checked "does a file already exist," not "does the installed
   version match the pin" — so editing the variable silently did nothing. Fixed 2026-09-05:
   kubectl → v1.37.0, k3d → v5.9.0, terraform → 1.16.1, all now version-*checked*, not just
   version-*named*. Argo CD CLI had the same bug plus no pin at all (pointed at `/latest/`,
   which then silently froze on first install) — now pinned to v3.5.2 with the same fix.
   Trivy deliberately left unpinned (tracks its own apt repo; a scanner benefits from current
   signatures more than a stable pin).
   **Resolved 2026-09-05 (Rung 1):** Helm, LocalStack and AWS CLI v2 all had the identical
   install-once-then-freeze defect — fixed the same way (check installed version against the
   pin, only reinstall on mismatch). `roles/updates` now also reports (opt-in, `--tags
   version_check`) whether any pin has a newer release available, querying GitHub/PyPI/
   HashiCorp — the "refresh process" gap this item originally flagged.
3. ~~374 GB of unallocated LVM space waiting on `lvextend` + `resize2fs`.~~ **Resolved
   2026-09-04**: extended the root LV by `+100G` (100G → 200G) after clearing the old AI
   workload data, rather than the originally-planned `+300G` — kept ~274 GB unallocated in
   the VG for LVM snapshots and future flexibility. Growing an LV live is cheap and safe, so
   there's little cost to extending again later against real evidence of need.
4. **Credential hygiene.** The `ansible` account's password was lost and reset via a second
   account, `cp`, which has `(ALL) NOPASSWD: ALL` — unrestricted passwordless root. Fine for
   a lab, exactly what a Secure by Design review would flag. Flagged as the first ADR topic:
   should the automation account get NOPASSWD sudo, and what compensates for it?
5. ~~LocalStack's CLI doesn't run on this box's Python.~~ **Resolved 2026-09-07.**
   `localstack --version` (and every other `localstack` subcommand) failed with a
   `SyntaxError` at import time in LocalStack 2026.5.0's own bundled code — an f-string with
   nested double quotes (`f"...{A["runtime_version"]}..."`), valid only from Python 3.12
   onward (PEP 701), but this box runs 3.10. Found 2026-09-05 while fixing LocalStack's
   version pin (worked around there by checking `pip3 show` instead of running the broken
   CLI). Confirmed already fixed upstream in 2026.5.1, the very next release — the
   `localstack/localstack` GitHub repo was archived 2026-03-23 (read-only, no new issues),
   so no upstream ticket was filed; the fix landing already made one moot. Bumped the pin in
   `bootstrap.yml` from `2026.5.0` to `2026.5.1`, and switched the verify task in
   `roles/hashicorp` back from `pip3 show localstack` to the real `localstack --version` —
   also added it back into the top-level `verify_cmds` loop in `bootstrap.yml`. A clean run
   of `localstack --version` is the confirmation that both the pin and the underlying CLI
   bug are fixed.

### Rung 0 checklist

- [x] Identify what is in `/opt` and `/usr` — 20 GB `/opt/ollama` + 889 MB
  `/opt/open-webui`, old local-LLM experiments; service confirmed disabled and no running
  containers, then removed
- [x] Extend the root LV — `lvextend -L +100G` (not the originally-planned `+300G`) then
  `resize2fs`; confirmed with `df -h /`: 197G total, 30G used, 158G available
- [x] `sudo apt update && sudo apt full-upgrade -y` — done 2026-09-05, rebooted for a kernel
  bump (5.15.0-181 → 5.15.0-191). Added a read-only playbook task reporting pending-upgrade
  count and reboot-required status on every run, so this stays visible going forward.
- [x] ~~DHCP reservation for `192.168.1.130`~~ — **descoped 2026-09-05.** Confirmed key already
  present (see below) but address is still plain DHCP, not reserved. Decided against a router
  reservation: low job-spec relevance (home-router DHCP admin isn't the "networking" the spec
  means), box now stays powered on rather than often-off, and the failure mode if the lease
  ever moves is a loud `UNREACHABLE!` from Ansible, not silent drift — fix by updating
  `hosts.ini`. Explicit lab shortcut, not the professional pattern; revisit if it ever actually
  bites.
- [x] Remove the stale `ai-net` Docker network — done 2026-09-05, `docker network rm ai-net`
  run manually over SSH (machine-specific stale resource, not worth codifying in the
  playbook)
- [x] Bump the pins in `bootstrap.yml` — done 2026-09-05, see Open Problem #2 above for the
  idempotency bug this surfaced and fixed along the way
- [x] Enable Ubuntu Pro/ESM (free, up to 5 personal machines) — attached 2026-09-05,
  `esm-apps`/`esm-infra`/`livepatch` all enabled. Re-ran `apt update && apt full-upgrade -y`
  afterwards to pull the previously-gated ESM packages; no reboot required;
  `apt list --upgradable` clean.
- [x] Add Charlie's SSH public key to the `ansible` account — already present in
  `~/.ssh/authorized_keys` (confirmed 2026-09-05), no action needed

### Rung 1 checklist

- [x] ADR-0001 on the `ansible` account's sudo model — written 2026-09-05, decision: leave
  the current model unchanged (see `docs/adr/0001-ansible-sudo-model.md` for why command-
  scoping and vaulted `become_pass` were both considered and rejected). ADR-0002 onward are
  Charlie's to write; this one was a Claude-drafted worked example.
- [x] Refactor `bootstrap.yml` into roles — done 2026-09-05: `common`, `docker`,
  `k8s_tools`, `cli_tools`, `hashicorp`, `security_tools`, `updates`. Verified with a clean
  full run (`ok=55 changed=1 failed=0`) — the one `changed` is an already-understood,
  deliberate apt-cache-refresh task, not a regression.
- [x] Fix the Helm/LocalStack/AWS CLI/Checkov pinning gap — done alongside the refactor,
  same check-then-reinstall pattern as kubectl/k3d/Terraform/Argo CD. Helm pinned to 3.x
  deliberately, not 4 (see `roles/cli_tools`) — v3's security-fix window closes 2026-11-11,
  revisit before then.
- [x] `roles/updates` freshness + support/EOL reporting (opt-in, `--tags version_check`) —
  covers what's checkable (GitHub/PyPI/HashiCorp releases APIs, `endoflife.date` for
  Ubuntu/Kubernetes); AWS CLI and most of the rest aren't tracked anywhere structured, noted
  as such in the report rather than silently skipped.
- [x] Relocate `ansible.cfg` to the repo root — it was sitting in `ansible/`, one level away
  from both the inventory it points to and the repo root everything else runs from, so
  `stdout_callback = yaml` was silently never taking effect. Fixed 2026-09-05.
- [ ] Molecule tests — in progress (2026-09-07): `create.yml`/`destroy.yml` done and
  proven (full create → login → destroy → create → login cycle, including SSH bastion
  routing to the VM via the Beelink — see README "Logging into the VM Molecule creates").
  Still to write: the real seven-role `converge.yml` and `verify.yml` assertions, then a
  first full `molecule test` run. See journal 2026-09-07 for the bugs found/fixed along
  the way.
- [ ] Repo on a real PR-with-CI flow — branch protection, `ansible-lint`/`yamllint` in
  GitHub Actions. Not started.

*Keep this section current — it is the fastest way for a new session to pick up the thread.*

## Model choice

Routine work in this repo — running through the rungs, explaining commands, editing the
playbook — is well served by **Sonnet at medium effort**, and it uses the usage window far
more slowly. Reach for **Opus** when the task is open-ended research, synthesis into a
document, or debugging that has already resisted two or three attempts.

## Navigation

| Path | What's in it |
|---|---|
| `docs/digi2al-dna-prep.md` | Research, gap analysis, constraints, the plan. Start here. |
| `docs/LearningPlan.md` | An earlier 30-day plan. Superseded — kept for reference. See prep doc §7. |
| `docs/journal.md` | Running session log. Read the last few entries at the start of a session. |
| `ansible/bootstrap/` | The bootstrap playbook. Rung 1 refactors this. |
| `inventory/hosts.ini` | Beelink host and user. |
| `ignoreme/` | The job spec PDF. Not for publication. |

## Constraints

- **No AWS spend.** EKS is therefore not practisable; prep doc §10 covers the substitutions
  and how to talk about the gap honestly. Don't propose work that requires a paid cloud
  account without flagging the cost first.
- **One machine.** The Beelink is the lab. His working laptop stays out of it.
- **This repo stays private** — it contains home network topology and personal notes. Public
  portfolio repos are welcome as siblings in the `Digi2alDNAPractice` directory.
- **Vetting in progress.** Nothing published should look like it relates to real Navy
  systems. Keep everything unclassified and generic.

## Journal

Append a short entry to `docs/journal.md` at the end of any session that did real work:
date, what was done, what broke, what was learned, what's next. Two minutes, and it is most
of the raw material for the demo script at Rung 8.
