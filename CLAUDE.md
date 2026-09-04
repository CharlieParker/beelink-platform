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

**Rung 0 — Make the lab trustworthy: in progress.**
Recon commands issued; patching not yet done. See `docs/digi2al-dna-prep.md` §11 for the
full ladder.

- The Beelink (`192.168.1.130`, Ubuntu 22.04.5, user `ansible`) has been bootstrapped once
  and left untouched since May 2026. It needs patching. It is on **Wi-Fi** (`wlp3s0`), and
  disk was at 51% of 98 GB.
- `ansible/bootstrap/bootstrap.yml` runs successfully but is a single monolithic play with
  a lot of Docker-repo cleanup scar tissue. Refactoring it into roles is Rung 1.
- Nothing has been deployed to a cluster yet. No CI. No application.

*Keep this section current — it is the fastest way for a new session to pick up the thread.*

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
