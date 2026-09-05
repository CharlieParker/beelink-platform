# ADR-0001: Sudo model for the `ansible` automation account

**Status:** Proposed — flip to Accepted once Charlie's reviewed and committed this.
**Date:** 2026-09-05

## Context

The `ansible` account is used by `ansible-playbook` (currently just `bootstrap.yml`,
`become: true`) to configure the Beelink. Its current sudoers entry is:

```
User ansible may run the following commands on beelink:
    (ALL : ALL) ALL
```

Full scope (any command, as any user), no `NOPASSWD` tag — so every playbook run is
launched with `ansible-playbook ... -K`, prompting for the sudo password interactively.

A separate account, `cp`, holds `(ALL : ALL) ALL` **with** `NOPASSWD`, used only as a manual
break-glass account (created when `ansible`'s own login password was lost). `cp` is not used
by any automation and its fate is intentionally out of scope for this ADR.

`sshd -T` confirms both `PasswordAuthentication` and `PubkeyAuthentication` are enabled
system-wide. Noted for completeness; not addressed here — this box is on a home LAN, not
exposed to the open internet, so it's a low-priority follow-up rather than something this
ADR needs to resolve.

## Options considered

1. **Scope `ansible`'s sudo to a specific command allow-list, with `NOPASSWD`.** Rejected.
   Most of `bootstrap.yml`'s tasks are Ansible *modules* (`apt`, `systemd`, `file`, `pip`,
   `get_url`), not literal shell commands — Ansible executes these by shipping a generated
   Python script to the target (or piping it via stdin, under pipelining) and running it as
   `python3 <randomly-named-temp-path>`. A sudoers command allow-list can't meaningfully
   restrict that: either `python3` execution is allowed, which is unrestricted code
   execution as root under another name, or it's blocked, which breaks nearly every module
   task in the playbook. Command-scoping only works cleanly for tasks that use `shell:` /
   `command:` directly against fixed binaries, which is a small minority here.
2. **Ansible Vault-encrypted `become_pass`, keeping full scope.** Rejected for now. This
   would remove the interactive `-K` prompt, but doesn't reduce `ansible`'s privilege at
   all — it's still full root — and it relocates the secret rather than removing it: either
   a vault password typed every run (no improvement over today), or a vault password file on
   disk (a standing secret, with no current driver such as a scheduled or CI-triggered run
   to justify taking on that risk).
3. **Leave the current model unchanged.** Chosen. See Decision.

## Decision

Keep `ansible`'s sudo model as it is today: `(ALL : ALL) ALL`, password-gated, run
interactively via `ansible-playbook ... -K`. No sudoers changes.

The real trust boundary for this account is not sudoers granularity — it's **who can SSH in
as `ansible`, and who can push a change to this repository that a future automated run would
execute.** Worth keeping in mind when reasoning about this account's blast radius, since
sudoers scoping was shown above not to meaningfully narrow it.

**Revisit this ADR when a role needs to run unattended** — for example, a scheduled
`updates` role, or a CI pipeline running the playbook without a human present. At that
point, vaulted `become_pass` (not command-scoping, for the reason in Option 1) is the
recommended next step, with real thought given to where the vault password itself lives.

## Consequences

- No change to the current day-to-day workflow — `-K` and the password prompt stay.
- `cp`'s `NOPASSWD:ALL` remains a separate, accepted exception: manual, break-glass, never
  used by automation. Its own hygiene (SSH auth method, whether it should exist at all) is a
  candidate for a future ADR, not this one.
- The next trigger to watch for is the first unattended/scheduled use of this playbook —
  when that's proposed, this ADR should be revisited rather than assumed still valid.
