# Digi2al / DNA — Pre-Start Prep Plan

**Purpose:** what I found out about the employer, the client and the programme, and what
looks like the highest-value use of the interim period before SC clearance lands.

**Status:** research + proposal. Nothing here is committed to a schedule yet.
Last updated: 2026-09-04 (rev 2 — constraints agreed, sequencing re-cut).

---

## 1. TL;DR

- **Digi2al** is a small-ish UK digital transformation consultancy (founded 2013, based in
  Hove) that does user-centred design, cloud engineering, complex code, low-code, cyber and
  managed services for MOD, Royal Navy, DfE, FCDO, MoJ and others.
- **DNA (Data & Navy Applications)** is the Royal Navy's in-house software house inside Navy
  Digital. Digi2al is already deeply embedded there — their **Kraken** team *is* a DNA
  development team, and has built **500+ applications on Palantir Foundry** for the Navy.
- So the job is: **DevOps/platform engineering for a government software house**, in a
  hybrid cloud + secure/accredited context, alongside low-code (Foundry) and pro-code
  (Java/React) delivery teams.
- The single highest-leverage thing to build is **one realistic vertical slice** — a
  Java + React + Postgres + Keycloak app, containerised, Helm-packaged, GitOps-deployed to
  k3s on the Beelink, with a GitHub Actions pipeline that has real security gates and real
  observability behind it. That one artefact evidences ~80% of the "must have" list.
- **EKS is the one must-have you can't practise**, given the decision to avoid AWS spend —
  §10 sets out how to cover the concepts anyway and how to talk about the gap honestly.
- The second highest-leverage thing is **not technical**: fluency in *how government builds
  software* — GDS Service Standard, NCSC secure design principles, and MOD **Secure by
  Design**. This is what separates a competent DevOps engineer from one who can operate
  inside MOD from week one.

---

## 2. The company — Digi2al

| | |
|---|---|
| Founded | 2013 (Companies House 08407866) |
| HQ | Hove, East Sussex |
| Sectors | Defence & Security; Government & Public Services; Health & Human Performance; Climate & Environment |
| Named clients | MOD, Royal Navy, DfE, FCDO, MoJ |
| Membership | ADS Group (UK aerospace/defence/security trade body) |

**Eight service lines:** Service Design & UX · Cloud Engineering · Complex Code · Low Code ·
Cyber Security · OSINT Brokerage & Threat Intelligence · Managed Services · Digi2al Labs.

**How they describe their own practice** (worth echoing back in conversation):

- "Creators, not just advisors" — they build, they don't only consult.
- Iterative agile development, adhering to the **GDS Service Manual** and **NCSC
  secure-by-design principles**. That's a direct quote from their services page and it tells
  you exactly which rulebooks the delivery teams work to.
- Low-code delivery on **Palantir Foundry**, Microsoft Power Platform and Pega.
- User research and co-creation with the client rather than pre-baked solutions.
- Culture words they use: "passionate geekery", curiosity, nimble, adaptive, collaborative.

**Read:** they are a delivery partner embedded in client teams, not a body shop. Expect to
be a named engineer in a multidisciplinary Navy team, not sitting in a Digi2al silo.

---

## 3. The client — Navy Digital and DNA

**DNA = Data & Navy Applications.** Described by its own head as the Navy's "Software
House". It builds mobile apps and digital services for sailors and for Navy business
processes — e.g. **MyNavy** (the Navy's mobile app), access-control apps, incident-reporting
apps with geolocation, and data/analytics products.

**Kraken** is the DNA data-as-a-service capability, awarded an enterprise licence to run
Navy-wide. Digi2al's Kraken team built 500+ Foundry apps covering things like helicopter
availability, aircraft maintenance tasking, and senior-leadership resource-allocation
workflows.

### The strategic frame — Royal Navy Digital and Data Plan

The Navy's published plan sets the direction the platform work serves. Things worth
knowing by name:

- Ambition: "data and evidence driven in everything we do, from the strategic HQ to the
  tactical edge."
- **Cloud-first**, hyperscale cloud spanning multiple security classifications, data-centric
  architecture with open standards and APIs.
- **Secure-by-design** by default, zero-trust architectures.
- Principles that will shape your day job: **"Reuse Before Buy, Before Build"**,
  system-of-systems disaggregation, common standards and patterns, deployed-user focus,
  rapid value delivery.
- Delivery cadence: portfolio-based funding with **three-month delivery increments**.
- Related programme names you'll hear: NELSON, Navy X, Kraken, OPNET.

### What this implies for the DevOps role

1. **Multi-classification** matters. Pipelines and platforms may need to work at OFFICIAL
   and above — the spec's "nice to have" of doing all of this "at OS or S level" is code for
   OFFICIAL-SENSITIVE and SECRET. Practically: constrained networks, mirrored artefact
   registries, no casual `curl | bash`, approved base images.
2. **Hybrid, not pure cloud.** The spec says "cloud and hybrid infrastructure" and lists
   networking, identity, monitoring, logging, **patching and backup** as platform components
   you support. That's a more ops-heavy remit than a typical product-company DevOps role.
3. **Enabling teams.** "Developing common services, manuals etc. to support delivery teams
   in taking ownership of their services" — this is platform engineering + golden paths +
   documentation, not a ticket queue.

---

## 4. MOD practices you should be able to talk about

### Secure by Design (SbD)

The MOD replaced the old certificate-based **accreditation** model with **Secure by
Design**. This is the single most useful piece of MOD context to absorb, because the job
spec explicitly mentions "MOD cybersecurity and accreditation standards".

What changed:

| Old accreditation model | Secure by Design |
|---|---|
| Certificate at a point in time | Continuous assurance through-life |
| Template documents written for approval | Evidence-based **assurance case** |
| One accreditor owns security | Whole-team responsibility |
| Security bolted on at the end | Security as an engineering design activity |

Practical consequences for a DevOps engineer: security risk identified early and revisited;
artefacts are evidence (scan results, SBOMs, policy-as-code, dashboards), not Word
documents; assurance teams act as enablers giving you tooling and patterns. Every pipeline
gate you build is potentially assurance-case evidence — that's a good framing to bring.

### The other rulebooks

- **GDS Service Manual + Service Standard** (14 points) — how UK government builds services.
  Digi2al says they work to it. Skim the whole standard; know the phases: Discovery → Alpha
  → Beta → Live.
- **NCSC Secure Design Principles** and NCSC Cloud Security Principles (14).
- **Technology Code of Practice** — the criteria government spend is judged against.
- **SFIA 4** — your role level. Read the SFIA level 4 description ("enable"): works under
  general direction, full accountability for own work, influences team, plans own work to
  meet objectives. Useful for calibrating expectations and for your first objectives
  conversation.

---

## 5. The role, decoded

From the spec, sorted by how much of it I can practise on the Beelink.

### Must-haves — directly practisable

| Requirement | Practisable here? | Current exposure |
|---|---|---|
| Infrastructure as code, repeatable & version-controlled | Yes | Ansible bootstrap exists; Terraform not yet used |
| AWS + Terraform | Partly (LocalStack + free tier) | Terraform installed, unused |
| Docker + Kubernetes ecosystem | Yes | Docker + k3d installed |
| **EKS specifically** | Only on real AWS | None |
| Helm | Yes | Helm installed |
| CI/CD pipelines, YAML-based | Yes (GitHub Actions) | None in repo yet |
| Linux, Bash/shell | Yes — it's the whole substrate | Strong-ish, deepen |
| GitHub / VCS | Yes | Repo exists, no CI, no PR flow |
| Building & maintaining containers | Yes | Not yet |
| **Java** | Yes | Gap — flagged |
| **React** | Yes | Gap — flagged |
| Agile scrum | Simulate + read | Gap |

### Nice-to-haves — cheap wins on the Beelink

Immutable infrastructure (**Packer**) · HashiCorp **Vault** · **Ansible** (already started —
turn it into roles) · Observability (**Prometheus, Grafana**; Splunk is read-only theory) ·
Containerised services: **Postgres, Redis, Kafka, Keycloak, ELK** · RedHat **OpenShift**
(OKD/CRC is heavy — treat as optional reading).

### The two gaps most people would skip

**Java and React are on the must-have list.** That's unusual for a DevOps spec, and it's
there because DNA ships Java back-ends and React front-ends. You are not expected to be an
application developer — you're expected to be able to *build, containerise, test and
pipeline* those stacks without needing hand-holding: understand Maven/Gradle, JVM memory
and container sizing, multi-stage builds, `npm ci` vs `npm install`, build caching, and how
to make a sane image out of both. **Do not skip these.** A demo app you wrote yourself is
worth more here than another Nginx deployment.

**Palantir Foundry.** Not in the spec, but it is the dominant technology in the DNA
estate. You don't need to learn it — but knowing what Foundry is, what an ontology is, and
where low-code fits vs pro-code will make you legible to your team on day one. Worth an
hour of reading; check whether Palantir's free developer tier is currently open if you want
hands-on.

---

## 6. Where the Beelink is now

From `ansible/bootstrap/bootstrap.yml`, the node already has:

Docker (distro `docker.io` stack, chosen to avoid containerd conflicts with k3d) · kubectl
v1.30.0 · k3d v5.6.0 · Helm · Argo CD CLI · Terraform 1.8.5 · AWS CLI v2 · LocalStack ·
Trivy · Checkov · base tooling (git, jq, python3).

Assessment: **the toolbox is right; the shape of the playbook is the problem.** It's a
single monolithic play with a lot of Docker-repo cleanup scar tissue. That's fine as a
bootstrap, but "implementing IaC using approved tooling and **patterns**" is on the
must-have list — so refactoring it is itself a deliverable, not a chore.

Also worth noting: `inventory/hosts.ini` pins the Beelink at `192.168.1.130` with an
`ansible` user.

### Live state, observed 2026-09-04

From the SSH login banner:

| Observation | Comment |
|---|---|
| Ubuntu 22.04.5 LTS, kernel 5.15.0-181 | Fine. 22.04 is supported to Apr 2027 (ESM to 2032). |
| Last login: 24 May 2026 | Box untouched for ~3.5 months. |
| 15 updates pending, 5 of them security; +4 more via ESM | Needs patching. Update list is itself stale. |
| Ubuntu Pro / ESM not enabled | Free for personal use on up to 5 machines. Worth enabling. |
| Disk 51.3% of 97.87 GB used | **Watch this.** Container images, k3d clusters and Prometheus TSDB eat disk fast. |
| Memory usage 2% | Need the absolute figure — Beelinks are commonly 8/16/32 GB and that decides how much of the stack can run at once. |
| Temperature 38 °C | Healthy idle. |
| IPv4 on `wlp3s0` | **The node is on Wi-Fi.** Fine for a lab, but it makes the address DHCP-dependent and adds latency and flakiness that will occasionally look like a Kubernetes bug. Ethernet + a DHCP reservation (or static IP) is the cheap fix. |
| Global IPv6 address present | Just be aware the box is v6-addressable on the LAN. |

**Actions arising:** patch it; enable unattended security updates; enable Ubuntu Pro/ESM;
pin the address; keep an eye on disk. Do the patching *twice* — once by hand to see it, then
again as an Ansible role, because "patching" is named explicitly in the job spec as a
platform component you support.

---

## 7. Assessment of the existing 30-day learning plan

**What's good:** the progression is sensible, it's timeboxed, and it lands on GitOps and a
portfolio consolidation. Days 15–21 (observability) and 22–24 (chaos) are genuinely
well-chosen for a reliability-focused role.

**What I'd change:**

1. **No application.** The plan deploys "nginx or demo app" throughout. That skips Java and
   React entirely — two explicit must-haves — and makes every later exercise less realistic
   (no meaningful traces, no interesting build pipeline, no real dependency scanning).
2. **Terraform against real AWS from day 8.** Now ruled out by the no-AWS-spend decision
   (§10). LocalStack is already installed — use it for the S3/IAM/ECR loops, and cover the
   EKS-specific concepts by substitution rather than by spending.
3. **Chaos engineering is over-weighted.** Litmus across days 22–24 is three days on a
   nice-to-have that isn't in the spec at all. Compress to one day.
4. **Missing items that ARE in the spec:** identity (Keycloak/OIDC), backup *and* patching
   as platform concerns, secrets management, supply-chain/SBOM, containerised services
   (Postgres/Redis/Kafka/ELK), immutable infrastructure/Packer, and Ansible roles + Molecule.
5. **No defence-context work at all.** Nothing on Secure by Design, GDS, air-gapped or
   restricted-network patterns.
6. **Nothing on writing.** "Good communication skills... ability to convey complex technical
   concepts clearly and concisely" is a stated must-have, and "developing common services,
   **manuals** etc." is a responsibility. Runbooks and ADRs should be first-class outputs.

**Recommendation:** keep the plan as a reference, but re-cut it around the vertical slice
below rather than following it day by day.

---

## 8. Proposed shape: three tracks

Rather than 30 sequential days, three parallel tracks with one shared artefact.

### Track A — The vertical slice (the main event, ~60% of time)

Build one small but *real* system and take it all the way. Everything else hangs off it.

**The system:** a Java (Spring Boot) API + React front-end + Postgres, with Keycloak in
front for OIDC auth. Deliberately chosen to mirror the DNA stack.

**The journey, in stages:**

| Stage | What you build | What it evidences |
|---|---|---|
| A1 | The app itself — thin but honest. A real domain, real endpoints, a DB migration. | Java, React |
| A2 | Multi-stage Dockerfiles for both; non-root, pinned base images, small final images. | Building & maintaining containers |
| A3 | k3s cluster on the Beelink (k3d or real k3s — see note below). Deployments, Services, ConfigMaps, Secrets, probes, resource requests/limits. | Kubernetes |
| A4 | Helm chart with values-driven envs (dev/stage), plus Postgres, Redis and Keycloak as dependencies. | Helm, containerised services |
| A5 | GitHub Actions: build → test → scan → push to registry → publish chart. Branch protection and PR flow enforced on yourself. | CI/CD, YAML pipelines, GitHub |
| A6 | Security gates that actually fail: Trivy (image + filesystem), Checkov (IaC), SBOM generation (Syft/`docker sbom`), dependency scanning, secret scanning (gitleaks). | Security checks & quality gates, SbD evidence |
| A7 | Argo CD on the cluster, app-of-apps pattern, environments as directories. Deployment is a git commit. | GitOps, immutable/declarative infra |
| A8 | kube-prometheus-stack + Loki + OpenTelemetry traces from the Java app. Dashboards for RED metrics; alerts that fire. | Observability |
| A9 | Terraform: state, modules, and the AWS-shaped bits (ECR, S3, IAM) against **LocalStack**; plus the `kubernetes`, `helm` and `docker` providers managing the real cluster. | Terraform, IaC patterns |
| A10 | Runbooks: triage, incident response, backup/restore, onboarding a new service onto the platform. | Communication, "manuals to support delivery teams" |

**Note on k3d vs k3s — and on having only one machine.** Good news: a second physical node
is largely unnecessary. k3d agents are real kubelets in containers, so a 3-node k3d cluster
gives you genuine scheduling, taints and tolerations, affinity, cordon, drain, PodDisruption
Budgets and node-failure behaviour. What it can't give you is node-level operations — systemd,
kubelet on metal, disk pressure, OS patching, certificate rotation — and those *are* named in
the spec ("networking, identity, monitoring, logging, patching and backup").

So run both, on the one box: **k3d** for disposable multi-node exercises, and a **real
single-node k3s** installed on the metal for the operational concerns. If you later want a
second "real" node without a second machine, a local VM (multipass or LXD) joined as a k3s
agent gets you most of the way — memory permitting.

### Track B — Platform and infrastructure depth (~25%)

Standalone pieces that don't fit the slice:

1. **Refactor the bootstrap playbook** into proper Ansible roles (`docker`, `k8s_tools`,
   `hashicorp`, `security_tools`) with defaults, handlers, and **Molecule** tests. Directly
   evidences "repeatable, version-controlled infrastructure to support consistency across
   environments". High value, low cost — the code already exists.
2. **Packer** — build a golden image (an AMI on free tier, or a local VM image) with the
   base tooling baked in. Then make the Ansible role the Packer provisioner. That's the
   "immutable infrastructure" nice-to-have, demonstrated properly.
3. **Vault** — run it in the cluster, do dynamic Postgres credentials and/or External
   Secrets Operator. Alternatively SOPS + age if you want the lighter version. Secrets
   handling is a guaranteed interview and day-one topic in defence.
4. **Backup and restore** — Velero against MinIO on the Beelink. Prove a namespace restores
   into a fresh cluster. Also do a boring OS-level one: what's your patching story for the
   Beelink itself, and is it automated?
5. **Restricted-network simulation** — the most defence-flavoured exercise available to you.
   Stand up a local registry (Harbor or `registry:2`) plus a package mirror, then bring up
   the whole stack on a cluster with egress blocked by NetworkPolicy. If you can deploy with
   no internet, you understand what OS/S-level delivery actually costs.
6. **Kubernetes security** — NetworkPolicies, RBAC that's actually least-privilege, Pod
   Security admission, and a policy engine (Kyverno) enforcing e.g. "no `:latest`, no root,
   signed images only".
7. **Chaos** — compress to a single day. Pod delete + CPU hog, correlate with your
   dashboards, write it up.

### Track C — Practice, context and communication (~15%)

Cheap, high-signal, and the part most engineers neglect.

1. **Read and take notes on:** MOD Secure by Design (understand the assurance case); GDS
   Service Standard (all 14 points); NCSC Secure Design Principles and Cloud Security
   Principles; the Royal Navy Digital and Data Plan (you'll be able to quote its principles
   back — "reuse before buy, before build" is a real conversation-shaper).
2. **Self-assess against SFIA level 4** — read the level 4 ("enable") descriptors for the
   relevant skills (IaC, systems installation/decommissioning, availability management,
   security operations) and note honestly where you're above and below. Useful ammunition
   for your first objectives conversation.
3. **Scrum literacy** — you'll be in agile multidisciplinary teams with Jira. If you've not
   worked a formal scrum, read the Scrum Guide (it's ~13 pages) and be able to talk about
   refinement, sprint goals, and how DevOps work gets sized when it's enabling work rather
   than features.
4. **Write things up as you go.** An ADR per significant decision in the repo, a README that
   a stranger could follow, and one page per runbook. This is the artefact that proves the
   "convey complex technical concepts clearly and concisely" must-have, and it costs almost
   nothing extra if done continuously.
5. **A 10-minute demo script.** By the end you should be able to walk someone from git push
   to running, observed, secured service in ten minutes. That's your first-week credibility.
6. **Foundry orientation** — one hour understanding what Foundry/ontology is and where
   low-code sits in the DNA estate.

---

## 9. Certifications — worth it or not?

All of these cost money, so treat the whole section as optional.

| Cert | Cost/effort | Verdict |
|---|---|---|
| **HashiCorp Terraform Associate** | ~$70, ~2 weeks part-time | **Best value.** Directly on the must-have list, and the study maps onto Track A9/B. |
| **CKA (Certified Kubernetes Administrator)** | ~$395, ~4–6 weeks | **Strongest signal** if you want one credential. Hands-on, and the curriculum overlaps almost entirely with what you're building anyway. |
| AWS Solutions Architect Associate | ~$150 | **Deprioritised** given the decision to avoid AWS spend — studying for it without a live account is dry and low-retention. Revisit once you're inside and have a company account to play in. |
| CKS (Kubernetes Security) | High, requires CKA | Great fit for defence, but a post-start goal. |
| Scrum certs (PSM I) | ~$200 | Skip. The Scrum Guide gets you 90% of the value free. |

**If you only do one:** CKA. **If you want the fastest credible win:** Terraform Associate.
**If you'd rather spend nothing:** a well-documented repo plus a 10-minute demo is worth more
in a conversation than either.

---

## 10. Decisions and constraints (agreed 2026-09-04)

These fix the shape of everything above.

### No AWS spend

**Decision:** avoid AWS; free tier only if unavoidable.

**Consequence:** hands-on **EKS is off the table.** EKS has no free tier — the control plane
alone is charged per hour, before any nodes or NAT gateway. Since EKS is on the must-have
list, here's the substitution:

| EKS concept | How to cover it without AWS |
|---|---|
| Managed control plane | Understand what k3s gives you for free vs what EKS charges for; be able to articulate the trade. |
| Node groups / autoscaling / Karpenter | Read-only study. Know the vocabulary and the failure modes. |
| IRSA / EKS Pod Identity (workload → cloud IAM) | The transferable concept is **OIDC federation for workload identity**. Practise it for real with Keycloak or Vault's Kubernetes auth on your cluster — the mental model is identical. |
| AWS VPC CNI | Compare against k3s's Flannel; understand what a CNI actually does. Optionally swap k3s to Cilium — that's a genuinely valuable exercise. |
| AWS Load Balancer Controller / Ingress | Traefik (k3s default) or ingress-nginx locally. The controller pattern is the same. |
| EBS CSI driver / storage classes | local-path (k3s default) plus Longhorn or MinIO for something closer to real. |
| EKS provisioning via Terraform | Read `terraform-aws-modules/eks` properly — the module source is genuinely educational. Run `terraform` against **LocalStack** for S3/IAM/ECR shapes. |

**Be honest about this in interviews and stand-ups:** "I've built and operated Kubernetes
end-to-end on bare k3s and k3d; I understand the EKS delta conceptually but haven't run a
production EKS cluster." That's a far better position than pretending, and the gap closes in
days once you're inside with a company account.

If you ever change your mind, one deliberate 2-day EKS burst is the right shape: fixed
budget, billing alarm set *before* creating anything, `terraform destroy` every night, and
beware the NAT gateway — it's the sneaky cost, not the control plane.

### One machine

The Beelink is the lab. Your working laptop stays out of it. See the k3d/k3s note in §8 —
this costs you very little.

### Unknown clearance date

**This is the important one.** The date could be a week away or months. So the plan must be
**value-ordered, not calendar-ordered**: every stage should leave behind something complete,
and stopping at any point should still be a win. §11 is re-cut accordingly.

### Repository strategy

- **`beelink-platform` stays private.** It contains your home network topology, host
  inventory and personal notes. Correct call.
- **Public portfolio work goes in separate repos** under the `Digi2alDNAPractice` directory —
  the vertical-slice app, its Helm chart and its pipeline are all fine to publish. Nothing in
  them touches Navy systems or your home network.
- This mirrors real practice: private infrastructure repos, public or shared application
  repos. Worth being deliberate about, and worth being able to explain why.

### Housekeeping

`docs/*` is currently in `.gitignore`, so both `LearningPlan.md` and this document are
untracked. Since the repo is private, there's little reason to ignore them — and version
history on your own plan is genuinely useful. Suggest ignoring `ignoreme/` (which holds the
job spec PDF) and tracking `docs/`.

### Other practical notes

- **Vetting period conduct.** Keep the lab entirely unclassified and open-source. Nothing
  published should look like it relates to real Navy systems. And respond fast to anything
  UKSV asks for — clearance is the critical path to your start date, not this plan.
- **Don't over-index on tools.** "Ability to collaborate effectively and communicate technical
  concepts clearly" is a stated must-have. A tidy repo with three good runbooks beats twelve
  half-finished tool spikes.
- **Hardware limits.** Confirm the Beelink's RAM before planning. Watch disk (already at 51%).
  Trim Prometheus retention, and don't run Kafka and ELK alongside everything else.
- **Sustainability.** 2–3 focused hours a day beats weekend binges, and leaves you fresh for
  the actual start date.

---

## 11. Value-ordered ladder (replaces the calendar)

**Status (updated 2026-09-05):** Rung 0 complete. Detailed checklist in
`beelink-platform/CLAUDE.md`. Rung 1: roles refactor and version pinning complete 2026-09-05; Molecule and the PR/CI flow still open — see the Rung 1 checklist there.

Because the start date is unknown, work in rungs. **Each rung ends with something finished.**
If clearance lands tomorrow, you stop and you've still gained something real.

### Rung 0 — Make the lab trustworthy (half a day)

Patch and harden the Beelink, enable unattended security updates and Ubuntu Pro/ESM, fix the
address, confirm RAM and disk headroom, sort the `.gitignore`.
**Leaves behind:** a box you can rely on, and a first honest look at "patching" as a platform
concern.

### Rung 1 — Make the existing work good (2–3 days)

Refactor `bootstrap.yml` into proper Ansible roles with defaults, handlers and **Molecule**
tests. Add an `updates`/patching role. Put the repo on a real PR-with-CI flow (branch
protection, `ansible-lint` and `yamllint` in GitHub Actions).
**Leaves behind:** demonstrable "repeatable, version-controlled infrastructure" — the
must-have you can most cheaply evidence, using code that already exists.
**Why first:** highest value per hour of anything on this list.

### Rung 2 — Close the Java/React gap (4–5 days)

Spring Boot API + React front-end + Postgres. Thin but honest: a real domain, real endpoints,
a DB migration, a few tests. Multi-stage Dockerfiles for both — non-root, pinned bases, small
final images.
**Leaves behind:** a public portfolio repo, and the two must-haves nobody else in your
position bothers to close.

### Rung 3 — Run it properly (4–5 days)

Real k3s on the metal. Deployments, Services, ConfigMaps, Secrets, probes, resource
requests/limits. Helm chart with dev/stage values. Add Keycloak for OIDC and Redis for
sessions/cache.
**Leaves behind:** Kubernetes and Helm evidenced on something real, plus the containerised
services the spec names.

### Rung 4 — Pipeline it, with teeth (3–4 days)

GitHub Actions: build → test → scan → push → publish chart. Gates that genuinely fail the
build: Trivy (image + filesystem), Checkov (IaC), SBOM via Syft, gitleaks. Then Argo CD and
GitOps — deployment becomes a commit.
**Read MOD Secure by Design during this rung** and write a one-page note framing each gate as
assurance-case evidence. That note is a differentiator.
**Leaves behind:** CI/CD, security gates, GitOps — and the vocabulary to discuss them in MOD
terms.

### Rung 5 — Observe it (3 days)

kube-prometheus-stack, Loki, OpenTelemetry traces from the Java app. RED dashboards. Alerts
that actually fire. A triage runbook. One chaos day (pod delete, CPU hog) correlated against
the dashboards.
**Leaves behind:** observability evidenced end-to-end, plus your first runbook.

### Rung 6 — Infrastructure as code, properly (3 days)

Terraform: state, modules, LocalStack for the AWS shapes, `kubernetes`/`helm` providers for
the cluster. Packer golden image with the Ansible role as provisioner — that's "immutable
infrastructure" demonstrated rather than claimed. Secrets via Vault or SOPS + age.
**Leaves behind:** Terraform and the HashiCorp nice-to-haves.

### Rung 7 — Make it defence-flavoured (3–4 days)

The **restricted-network exercise**: local registry (Harbor or `registry:2`) plus a package
mirror, then deploy the whole stack on a cluster with egress blocked by NetworkPolicy. Add
Kyverno policies (no `:latest`, no root, signed images). Least-privilege RBAC. Velero +
MinIO backup/restore proven into a fresh cluster.
**Leaves behind:** the single most relevant exercise on this list for OFFICIAL-SENSITIVE and
above delivery, and one almost nobody has done.

### Rung 8 — Consolidate (2 days)

README, ADRs, runbooks, a 10-minute demo script from git push to running observed service.
Terraform Associate exam if you want it.
**Leaves behind:** the thing you actually walk someone through in week one.

### Running alongside, in the gaps

Reading (Secure by Design, GDS Service Standard, NCSC principles, the Navy Digital and Data
Plan, the Scrum Guide, SFIA 4 descriptors), an hour on Palantir Foundry orientation, and an
ADR written at the moment of each real decision rather than retrofitted.

### If clearance lands early

| Time available | Do |
|---|---|
| ~1 week | Rungs 0–1 |
| ~2 weeks | Rungs 0–2 |
| ~4 weeks | Rungs 0–4, then jump to 8 |
| ~6 weeks | Rungs 0–5, then 7 and 8 |
| 8 weeks+ | The lot |

---

## 12. Still open

- **How much RAM does the Beelink have?** Decides how much of the stack runs concurrently.
- **Ethernet available where it sits?** Wi-Fi is workable but will occasionally masquerade as
  a Kubernetes problem.
- Do you want the Terraform Associate / CKA spend, or keep this zero-cost?

---

## Sources

- [Digi2al — home](https://www.digi2al.com/)
- [Digi2al — services](https://www.digi2al.com/services)
- [Digi2al — about / meet the team](https://www.digi2al.com/meet-the-team)
- [Digi2al — Kraken case study](https://www.digi2al.com/case-studies/kraken)
- [Digi2al Ltd — ADS Group member profile](https://www.adsgroup.org.uk/members/digi2al-ltd-1/)
- [Digi2al Ltd — supplier profile, Companies House 08407866](https://www.publicsupply.co.uk/suppliers/08407866)
- [Defence Digital blog — In Conversation with Ben Holloway, Head of Data and Navy Applications](https://defencedigital.blog.gov.uk/2022/12/06/in-conversation-with-ben-holloway-head-of-data-and-navy-applications/)
- [Defence Digital blog — Navy Digital category](https://defencedigital.blog.gov.uk/category/navy-digital)
- [Royal Navy Digital and Data Plan (PDF)](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/1121170/Royal_Navy_Digital_and_Data_Plan.pdf)
- [Logiq — MOD Secure by Design guide](https://www.logiq.co.uk/insights/mod-secure-by-design/)
- Local: `ignoreme/Digi2al - DNA - DevOps - Job Spec.pdf`, `inventory/LearningPlan.md`,
  `ansible/bootstrap/bootstrap.yml`

---

## 13. Research pass — Digi2al, DNA and Foundry (2026-09-07)

**Why this section exists.** §5 asserts that Java and React are on the must-have list because
"DNA ships Java back-ends and React front-ends", and flags Palantir Foundry as an aside worth
an hour. Both claims were reasoning from the job spec plus one case study. This section is an
attempt to test them against primary public sources — company hiring documents, public
government code repositories, Palantir's own product documentation, and the MOD/Palantir
contract record.

**Confidence framing up front.** Everything below is inference from public material. Job
adverts describe what an organisation wants to *buy*, not necessarily what it *runs*. Public
repositories skew towards the things it is safe to open-source. **The job spec is a primary
source about the actual team, and outranks everything here wherever they conflict.** This
research should move emphasis, not overrule the spec.

### 13.1 The honest ledger — what could not be found

Stated first, so the rest is read in proportion:

- **No public source states the Foundry-versus-bespoke ratio inside DNA.** Not in percentages,
  not in headcount, not in application counts split by build method. Nobody publishes that.
  Any confident number would be invented.
- **Digi2al has no engineering blog and effectively no public code.** The GitHub organisation
  `github.com/digi2al` holds a single forked repository (`coding-tests`), last touched
  September 2016, no public members. No conference talks found.
- **The historical DevOps Engineer advert could not be retrieved.** Search engines still index
  `digi2al.com/careers/devops-engineer`, `/software-engineer` and `/junior-devops-engineer`,
  but all three now return 404 — postings are served from Workable and expire. So the advert
  Charlie was hired against could not be compared against its siblings.
- **LinkedIn profiles are not retrievable without a login**, so no individual engineer's stack
  could be verified.
- **No public source names Java anywhere in the Royal Navy / Navy Digital estate**, beyond a
  trivial `java-hello-world-with-maven` sample repo. Absence of evidence here is genuinely
  weak evidence — but it is consistent across four independent places one would expect to
  find it.

### 13.2 Digi2al's current live vacancies — the most useful find

Digi2al runs its hiring through Workable. Ten roles were live on 2026-09-07, all posted in
August 2026. Notably, **there is no DevOps Engineer vacancy** — consistent with the role
being filled. The engineering-shaped ones are worth reading in full; the highlights:

**Software Engineer** (London, permanent, SC, SFIA 5) — for a *data fusion platform* bridging
"tactical edge sensors in low-bandwidth environments and strategic HQs":

- Python for microservices and APIs (FastAPI/Flask), TDD, async, OOP
- Docker, multi-stage builds, minimal base images, **Kubernetes/K3s at the tactical edge**
- **"Palantir Platform Integration: Building integrations against Palantir APIs/SDKs, custom
  widgets/functions, and webhooks to extend core platform capabilities"**
- DIL (Disconnected, Intermittent, Limited bandwidth) architectures, store-and-forward, offline sync
- CI/CD packaging software into deployable Docker artifacts, MOD cyber security standards
- **No Java. No React.**

**Maritime Tech Director** (Portsmouth, £120k) — the technical director *for the DNA software
house itself*. This is the single most relevant document found, because it is a description of
the technical estate Charlie is joining, written by the people who run it:

- *"sufficient technical depth to lead teams using technologies such as **Python, TypeScript,
  React** and relational or non-relational database technologies"* — **Java is not named**
- *"platform and delivery tooling such as **Docker, Kubernetes, GitHub Actions, Tekton** and
  associated DevSecOps toolchains"*
- *"enterprise and SaaS platforms ... including **Microsoft Power Platform and Palantir
  Foundry**, with the ability to judge where platform use is appropriate and where bespoke
  engineering or open approaches are preferable"*
- An explicit remit to *"reduce strategic dependency on individual suppliers and platforms by
  promoting open standards, modular design, interoperable interfaces, reusable components and
  data portability"* and avoid *"unnecessary vendor lock-in"*
- Also: technical debt, DevSecOps maturity, build/buy/configure/integrate/reuse/retire decisions

**Solution Architect – Contractor** (Portsmouth, £650/day, SC, **department: DNA**):

- *"Experience with Containerisation technologies such as **Kubernetes**"*
- *"Understanding of modern Security principles; **Secure by Design, Zero Trust Architecture
  and Software Defined Networks**"*
- *"Familiarity with the **GDS lifecycle** and the requirements for passing a **Digital Service
  Assessment**"*
- *"Experience of Modern Application platforms; **MS Power Platform, Palantir Foundry or
  equivalent**"*
- *"Understanding of Cloud Architectures across **Azure, AWS** and other platforms"*

**Security Platform Engineer** (London, £70k–£100k, **DV**) — worth noting because it is *not*
Navy work at all: it is Digi2al staffing Google Public Sector's UK SecOps private cloud team.
Requirements: Kubernetes security (workload isolation, RBAC, NetworkPolicies), Falco,
Prometheus, Grafana, **Terraform, Helm, ArgoCD**, Python/Go/Bash, SIEM/IDS/CWPP, detection
engineering, service mesh (Istio/Linkerd).

**Director of Innovation and Engineering** (London, £100k) — reveals where the commercial bets
are: solutions *"optimized for **Google Cloud Platform's Secret Community Cloud** platform,
scaling internal **Microsoft (M365/Azure/Power Platform)** integrations, and developing joint
partner propositions within the **emerging Palantir ecosystem framework**"*. Also names the
TS4 commercial vehicle, uncrewed systems C2, multi-sensor data fusion and tactical edge.

**Company boilerplate worth updating §2 with:** Digi2al now describes itself as *"an
employee-owned defence-focused digital services consultancy ... vendor-agnostic UK Crown
Commercial Services accredited supplier"* that has *"delivered over 120 cloud-hosted digital
services across the public sector"*, spanning *"artificial intelligence, data science, **low
code and complex code software engineering**, cyber security, cloud engineering, and
user-centred design"*. Employee Ownership Trust from day one. Low-code and complex-code are
deliberately named as **separate** service lines.

### 13.3 The strongest single finding — the Royal Navy publishes its own React design system

This is hard, primary, current evidence, and it settles the React question:

- **`github.com/Royal-Navy/standards-toolkit`** — the Royal Navy Design System. A Lerna
  monorepo: **React 18, TypeScript, styled-components, Storybook, Jest, ESLint/Prettier**,
  ~6,800 commits. It publishes five public npm packages under `@royalnavy`:
  `react-component-library`, `design-tokens`, `icon-library`, `fonts`, `eslint-config-react`.
- **The guidance site is explicit**: *"The following 'view layer libraries' are currently
  supported: **React**"* — React is the only supported view layer, and the guidance says it
  *"will become part of the MOD Service Assessments in due time"*.
- The design system's own learning-resources page tells Navy developers to learn **ES2015+,
  React and Webpack**. There is also a `cra-template-royalnavy` package on npm.
- **It is actively maintained.** `Royal-Navy/design-system` (TypeScript) last updated
  **2 September 2026** — five days before this research.
- The same organisation also holds `navy-data-platform-get-started-guide`,
  `navy-digital-data-platform-api-mocking-tool`, and `ripsaw` (Python).

For comparison, `github.com/defencedigital` (65 repos): `moduk-frontend` — the MOD.UK Design
System — is **TypeScript**, updated **7 September 2026**; there is Python (SWAP processors,
NiFi tooling), Go (`semver-generator`), Terraform (`terraform-aws-rds`), a fork of Red Hat's
`red-hat-developer-hub-software-templates` (Backstage/RHDH scaffolding wired to **Tekton,
ArgoCD, OpenShift, Helm**), and exactly one Java repository: `java-hello-world-with-maven`,
last touched 2023.

*Caveat on the RHDH repo:* it is a fork of Red Hat's upstream templates, so it evidences
interest in an internal developer platform, not a choice of application language. But it does
corroborate **Tekton** appearing in the Tech Director spec — that pairing points at
OpenShift-flavoured CI somewhere in the estate, which is also the one thing on §5's
nice-to-have list (RedHat OpenShift) that the prep doc dismissed as "optional reading".

**One counter-data-point on MyNavy.** §3 and the Digi2al DNA case study both list MyNavy among
delivered applications. The publicly documented build of MyNavy is **Umbraco** (an open-source
.NET headless CMS) plus Ucommerce and Umbraco Forms, as a progressive web app with an API
gateway and identity system, delivered by agency Great State, hosted on military servers in the
UK. So even the flagship "Navy app" is neither Java/React nor Foundry. That is a vendor case
study and the app has been iterated since, so treat it as one data point, not the whole story —
but it does reinforce the general shape: **the DNA estate is heterogeneous, and no single stack
describes it.**

### 13.4 Foundry in the MOD — the contractual picture

Well documented, and larger than §5's "aside" framing implies:

- **December 2022:** MOD awards Palantir a **£75m Enterprise Agreement**, three years.
- **May 2021:** Palantir and the Royal Navy extend contracts specifically — Foundry used for
  workforce planning and strength modelling, supply chain and equipment inventory, equipment
  health alerting, training/promotion modelling, and the COVID "Recognised People Picture"
  built in under 48 hours. Palantir's own framing: Foundry empowers *"naval personnel, as well
  as civilians and contractors, most without programming expertise"*.
- **September 2024/2025:** Strategic Partnership Agreement — Palantir commits **£1.5bn** UK
  investment, London designated European defence headquarters.
- **December 2025 / effective 1 April 2026 – 31 March 2029:** a new enterprise agreement worth
  **£240.6m**, awarded by **direct award** under the Procurement Act 2023 rather than
  competitively.
- **Hansard, 10 February 2026** — a Commons debate on exactly this. The MOD transparency notice
  stated **only Palantir** could run the service and that switching would carry *"significant
  cost"*. The minister acknowledged: *"We take vendor lock-in very seriously"*, committed to
  regular contract review and to supporting British AI companies, and confirmed UK defence data
  remains sovereign and resident in the UK.

**Read this alongside the Maritime Tech Director advert.** The organisation is simultaneously
(a) contractually deeper into Foundry than ever and (b) hiring a technical director whose
written remit is to reduce platform dependency and judge *"where bespoke engineering or open
approaches are preferable"*. That tension is live, unresolved, and is exactly the thing a new
platform engineer should understand and have no ideological position on.

### 13.5 The prep doc's low-code framing needs correcting

§5 treats Foundry as "low-code" and pro-code as the separate Java/React world. Palantir's own
documentation says it is a spectrum, and the pro-code end is substantial:

| Foundry surface | Nature | Language |
|---|---|---|
| Ontology Manager | Configuration | none — objects, links, action types |
| Pipeline Builder | Visual / low-code | none |
| Workshop | *"no-code, object-oriented builder for operational applications"* | none (extensible) |
| Custom Widgets | Hybrid — extends Workshop with custom front-end code | JS/TS |
| Slate | Drag-and-drop builder with escape hatches | HTML/CSS/JavaScript |
| Code Repositories | Code | pipelines and Functions |
| Functions | Code | **Python or Java** |
| Ontology SDK (OSDK) | Code — generates typed SDKs | **npm (TypeScript), pip/Conda (Python), Maven (Java)**, OpenAPI |
| OSDK React apps | Code | *"completely customizable user interfaces using **React**"* |

Palantir maintains `palantir/ontology-starter-react-app` as the official starter, and runs a
*"Frontend & OSDK Developer"* training track. **React is Foundry's own documented pro-code
front-end path.** Java appears as a Functions language and an OSDK target — not as a Spring
Boot microservice tier.

And the Digi2al Software Engineer advert describes precisely this pattern in the wild:
integrations *"against Palantir APIs/SDKs, custom widgets/functions, and webhooks to extend
core platform capabilities"*. So a large amount of the pro-code work in this estate is likely
**around** Foundry rather than instead of it.

On Kraken's 500+ applications, Digi2al's own case study says they were *"created
collaboratively by both developers and non-technical users"* and describes Kraken as *"a
mixture of users and developers"*. The citizen-developer signal is real. That does not make
Kraken the whole estate — the DNA case study separately lists Navy Schedule Service, MyNavy,
Navy Data Platform, First Look & Discoveries and TLSM, and describes a *"buy-before-build"*
strategy *"emphasising low-code solutions, particularly Palantir Foundry, **alongside custom
development**"*.

**Answering §12's open question:** Palantir's free **Developer Tier is currently open, and the
UK is one of the supported countries** (sign-up via `build.aip.com`). Hands-on Foundry is
available at zero cost.

### 13.6 The straight recommendation

**React — keep it, and it is better justified than the spec alone suggested.** Three
independent sources converge: it is the Royal Navy's only supported view layer in their own
actively-maintained public design system; it is named in the DNA technical director's language
list; and it is Foundry's own pro-code front-end path. Of everything on the must-have list,
React is the item this research *strengthened*.

**Java — downgrade, do not delete.** It is on the spec, which is a primary source about the
actual team and is not overruled by absence elsewhere. But nothing public corroborates it: not
the DNA tech director's language list (Python, TypeScript, React), not the current Software
Engineer advert (Python), not the Navy's or Defence Digital's public repositories, not
Foundry's dominant paths. The most plausible reconciliation is the one §5 already reached —
*some* DNA back-ends are Java and a DevOps engineer must be able to build and pipeline them.
That is a **build-and-containerise competence, not an application-development one**, and it
does not need four to five days.

What it does need, concretely: take an unfamiliar Maven or Gradle repository, understand its
build, produce a sane multi-stage image (layered JAR, non-root, pinned base), size the JVM
inside a container memory limit, cache dependencies properly in CI, and diagnose why the build
is slow or the container is OOM-killed. One to one and a half days.

**Python has a better public claim than Java, and is not on the spec at all.** It is the
Software Engineer advert's primary language, a Foundry Functions and transforms language, the
OSDK's second target, and the scripting language named in the Security Platform Engineer role
alongside Go and Bash. Worth folding in — cheap, because he will write Python for tooling
regardless.

**Kubernetes / Helm / CI-CD / GitOps — unambiguously the best value, no change.** It is the
only thing named in *all four* Digi2al engineering adverts: K8s/K3s at the tactical edge
(Software Engineer), Kubernetes security with Helm and ArgoCD (Security Platform Engineer),
containerisation (DNA Solution Architect), and Docker/Kubernetes/GitHub Actions/Tekton (Tech
Director). Rungs 3 and 4 are where the time belongs.

**Terraform — keep, but retarget away from AWS-specificity.** The cloud picture is contested.
The spec says AWS and EKS; the DNA architect role says *"Azure, AWS and other platforms"* with
Azure first; the innovation director role is optimising bids for **Google Cloud Secret
Community Cloud** and Microsoft Azure/M365/Power Platform. Recommendation: keep Rung 6, but
make it about state, modules, providers and drift — the `kubernetes` and `helm` providers, and
LocalStack for the AWS shapes — rather than deep EKS study. This materially *reduces* the cost
of the no-AWS-spend constraint in §10: the EKS gap matters less than it looked.

**Foundry — read, plus one afternoon hands-on, but build nothing.** The Developer Tier is open
and the UK is in scope, so the cost of going from reading to touching is an afternoon. The goal
is vocabulary and one honest opinion: ontology as objects/links/actions, the Workshop vs Slate
vs OSDK distinction, Functions, Pipeline Builder, and where the platform boundary sits. It
makes him legible on day one and lets him ask the right week-one question — *"what's on
Foundry, what's bespoke, and who decides?"* — knowing from the Tech Director advert that the
organisation is actively asking it too.

**Tekton — twenty minutes of reading, no build.** It is the one tool in the DNA technical
director's list that appears nowhere on the ladder. Knowing it is Kubernetes-native,
OpenShift-flavoured CI, and how it differs from GitHub Actions, is sufficient.

### 13.7 Proposed changes to the ladder

| Track | §11 as written | Proposed | Reason |
|---|---|---|---|
| Rung 2 — Java/React | 4–5 days | **2–3 days, re-cut** | React stays and grows; Java shrinks to build-and-pipeline |
| Rungs 3–4 — K8s/Helm/CI/GitOps | 7–9 days | **unchanged, highest priority** | the only thing in all four Digi2al engineering adverts |
| Rung 6 — Terraform | 3 days | **unchanged, retargeted** | provider-shaped, not EKS-shaped |
| Foundry orientation | 1 hour reading | **1 hour reading + 1 afternoon on Developer Tier** | tier is open, UK in scope, zero cost |
| Tekton | absent | **20 minutes reading** | named by the DNA tech director, nowhere on the ladder |

**Re-cut of Rung 2, specifically:**

- **Front end:** React + TypeScript, and consume **`@royalnavy/react-component-library`** from
  npm. Building the demo UI against the Royal Navy's actual public design system is a
  differentiating hour of work, and it is public, open-source and unclassified, so it does not
  touch the vetting constraint in §10.
- **Back end:** write the application itself in **Python/FastAPI** — it matches the current
  Software Engineer advert, the Foundry Functions/transforms path, and the OSDK's second
  target. Then, *separately*, **containerise and pipeline a Java application he did not
  write**: clone a public Spring Boot sample and treat the Maven build, layered JAR, JVM
  container flags and CI dependency caching as the exercise.

That second half covers the Java must-have at exactly the depth §5 described — *"build,
containerise, test and pipeline those stacks without needing hand-holding"* — and it is a
truer simulation of a DevOps engineer's real relationship with a language they do not own than
writing a toy Spring Boot app would be.

### 13.8 Confidence, restated

- Job adverts describe what an organisation wants to buy, not what it runs today.
- The absence of Java in public material is weak evidence taken alone; it is only worth
  anything because it is consistent across four places one would expect to find it.
- The React finding is the strongest, because it rests on running, actively-maintained,
  publicly-versioned code rather than on prose.
- **Nothing here justifies dropping a spec must-have.** Java stays on the list. It moves from
  "build an app" to "build someone else's app, well".

### Sources (this section)

- [Digi2al — careers](https://www.digi2al.com/careers) and the live Workable board:
  [Digi2al Limited — current openings](https://apply.workable.com/digi2al-limited/)
- [Digi2al — Software Engineer (data fusion platform)](https://apply.workable.com/digi2al-limited/j/742D2BE89D/)
- [Digi2al — Maritime Tech Director (DNA software house)](https://apply.workable.com/digi2al-limited/j/05FDDDE72F/)
- [Digi2al — Solution Architect, Contractor (dept: DNA)](https://apply.workable.com/digi2al-limited/j/CE55ADE98B/)
- [Digi2al — Security Platform Engineer (Google Public Sector UK SecOps)](https://apply.workable.com/digi2al-limited/j/491A59E2DA/)
- [Digi2al — Director of Innovation and Engineering](https://apply.workable.com/digi2al-limited/j/03D2EA687A/)
- [Digi2al — DNA Software House case study](https://www.digi2al.com/case-studies/dna-software-house)
- [Digi2al — Kraken case study](https://www.digi2al.com/case-studies/kraken)
- [Digi2al — Navy Schedule Service case study](https://www.digi2al.com/case-studies/navy-schedule-service)
- [github.com/digi2al](https://github.com/digi2al) — one forked repo, last active 2016
- [Royal Navy Design System — standards-toolkit (GitHub)](https://github.com/Royal-Navy/standards-toolkit)
- [Royal Navy Design System — development guidance](https://design-system.navy.digital.mod.uk/guidance/development)
- [Royal Navy Design System — learning resources](https://design-system.navy.digital.mod.uk/guidance/learning-resources)
- [github.com/Royal-Navy — repositories](https://github.com/orgs/Royal-Navy/repositories)
- [github.com/defencedigital — repositories](https://github.com/orgs/defencedigital/repositories)
- [cra-template-royalnavy on npm](https://www.npmjs.com/package/cra-template-royalnavy)
- [Umbraco — MyNavy case study](https://umbraco.com/case-studies-testimonials/mynavy/)
- [Palantir docs — App building overview](https://www.palantir.com/docs/foundry/app-building/overview)
- [Palantir docs — Application reference](https://www.palantir.com/docs/foundry/getting-started/application-reference)
- [Palantir docs — Ontology SDK overview](https://www.palantir.com/docs/foundry/ontology-sdk/overview)
- [Palantir docs — OSDK React applications](https://www.palantir.com/docs/foundry/ontology-sdk-react-applications/overview)
- [palantir/ontology-starter-react-app (GitHub)](https://github.com/palantir/ontology-starter-react-app)
- [Palantir Learn — Frontend & OSDK Developer training track](https://learn.palantir.com/page/training-track-frontend-osdk-developer)
- [Palantir Developer Community — Foundry developer accounts / Developer Tier availability](https://community.palantir.com/t/release-timeline-for-foundry-developer-accounts/2044)
- [Palantir Technologies and the Royal Navy Extend Contracts (2021)](https://www.businesswire.com/news/home/20210506006341/en/Palantir-Technologies-and-the-Royal-Navy-Extend-Contracts)
- [UK MOD awards Palantir £75m Enterprise Agreement (2022)](https://www.prnewswire.com/news-releases/uk-ministry-of-defence-awards-palantir-75-million-enterprise-agreement-301707783.html)
- [UKAuthority — MoD continues data analytics contract with Palantir (£240.6m, 2026–2029)](https://www.ukauthority.com/articles/mod-continues-data-analytics-contract-with-palantir)
- [Hansard — Ministry of Defence: Palantir Contracts, 10 February 2026](https://hansard.parliament.uk/Commons/2026-02-10/debates/FECED111-4FA8-4343-BFAF-18D6C13808D0/MinistryOfDefencePalantirContracts)
- [Defence Digital blog — Navy Digital category](https://defencedigital.blog.gov.uk/category/navy-digital)
- [Crown Commercial — Navy Digital Sustainment of Architecture Services (ND-SAS)](https://production-contractawardservice-ui.crowncommercial.gov.uk/digital-outcomes/opportunities/opportunity-details/project/64257)
