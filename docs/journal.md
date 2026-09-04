# Session journal

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
