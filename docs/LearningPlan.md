# 30‑Day DevOps Learning Plan (BeeLink)

A focused, progressive 30‑day plan of hands‑on exercises you can run on your BeeLink.  
Each exercise includes **objective**, **steps to try**, **timebox**, and **success criteria**. Work in order — later exercises build on earlier ones. Commit artifacts (Helm charts, Terraform modules, CI workflows, runbooks) to your mono‑repo.

---

## Week 1 — Core Kubernetes & Packaging (Days 1–7)

- **Day 1 — Build a reproducible multi‑node k3d cluster**  
  **Objective:** Create a 3‑node cluster (1 server, 2 agents) pinned to a k3s version.  
  **Steps:** `k3d cluster create dev --servers 1 --agents 2 --image rancher/k3s:v1.27.4-k3s1`; save kubeconfig to repo.  
  **Timebox:** 1–2 hours  
  **Success:** `kubectl get nodes` shows 3 Ready nodes; kubeconfig reproducible.

- **Day 2 — Deploy a demo microservice**  
  **Objective:** Deploy a simple API (nginx or demo app) using Deployment, Service, ConfigMap, Secret.  
  **Steps:** Create YAMLs, `kubectl apply -f`, test via port‑forward or ClusterIP.  
  **Timebox:** 2 hours  
  **Success:** App responds to HTTP requests; can scale replicas.

- **Day 3 — Rolling updates and rollbacks**  
  **Objective:** Practice rolling update and rollback.  
  **Steps:** Change image tag, `kubectl rollout status`, `kubectl rollout undo`.  
  **Timebox:** 1 hour  
  **Success:** Update completes; rollback restores previous version.

- **Day 4 — Create a Helm chart**  
  **Objective:** Package the demo app as a Helm chart with values overrides.  
  **Steps:** `helm create demo`, parameterise image/replicas/env, install with overrides.  
  **Timebox:** 2–3 hours  
  **Success:** `helm install` deploys app; values file changes behavior.

- **Day 5 — Publish chart to local repo**  
  **Objective:** Host chart in a local chart repo or OCI registry and install from it.  
  **Timebox:** 2 hours  
  **Success:** Chart install via repo/OCI works.

- **Day 6 — Commit and document**  
  **Objective:** Add scripts, chart, and README to mono‑repo.  
  **Timebox:** 1 hour  
  **Success:** Repo contains reproducible steps.

- **Day 7 — Buffer / refine**  
  **Objective:** Add probes, resource requests/limits, or tidy configs.  
  **Timebox:** 1–2 hours

---

## Week 2 — Infrastructure as Code & CI/CD (Days 8–14)

- **Day 8 — Terraform basics**  
  **Objective:** Create Terraform module to provision S3 bucket and IAM role (use sandbox AWS).  
  **Timebox:** 3 hours  
  **Success:** `terraform apply` creates resources; state stored.

- **Day 9 — Remote state & locking**  
  **Objective:** Configure remote state (S3 + DynamoDB) or simulate locking.  
  **Timebox:** 2 hours  
  **Success:** State locking demonstrated.

- **Day 10 — Provision EKS (or simulate)**  
  **Objective:** Use a community Terraform EKS module or simulate with k3d.  
  **Timebox:** 3–4 hours  
  **Success:** kubeconfig merges; `kubectl get nodes` for provisioned cluster.

- **Day 11 — GitHub Actions: build & push**  
  **Objective:** Create workflow to build container and push to registry.  
  **Timebox:** 2–3 hours  
  **Success:** Workflow completes; image in registry.

- **Day 12 — Add Terraform plan to CI**  
  **Objective:** Add `terraform plan` job and PR plan output.  
  **Timebox:** 2 hours  
  **Success:** PR shows plan; gating works.

- **Day 13 — Add security scans to CI**  
  **Objective:** Integrate Trivy (image) and Checkov (IaC) into pipeline.  
  **Timebox:** 2–3 hours  
  **Success:** Pipeline fails on seeded issue; fix and pass.

- **Day 14 — Buffer & document CI**  
  **Objective:** Store secrets securely; document pipeline steps.  
  **Timebox:** 1–2 hours

---

## Week 3 — Observability & Tracing (Days 15–21)

- **Day 15 — Deploy kube‑prometheus‑stack**  
  **Objective:** Install Prometheus, Alertmanager, node exporters via Helm.  
  **Timebox:** 3 hours  
  **Success:** Prometheus shows targets; Grafana accessible.

- **Day 16 — Add Loki + Promtail**  
  **Objective:** Centralise pod logs and query in Grafana.  
  **Timebox:** 2 hours  
  **Success:** Logs searchable in Grafana Explore.

- **Day 17 — Add Tempo + OpenTelemetry**  
  **Objective:** Instrument demo app with OTEL SDK and deploy Tempo.  
  **Timebox:** 3–4 hours  
  **Success:** Traces visible in Grafana; follow request path.

- **Day 18 — Dashboards & alerts**  
  **Objective:** Build dashboards (CPU/memory per namespace, error rate) and alert rules.  
  **Timebox:** 2–3 hours  
  **Success:** Alerts trigger on simulated conditions.

- **Day 19 — Alerting notifications**  
  **Objective:** Configure Alertmanager to send alerts (Slack/webhook/email).  
  **Timebox:** 2 hours  
  **Success:** Alert delivered on trigger.

- **Day 20 — Observability runbook**  
  **Objective:** Write triage runbook for latency, errors, and restarts.  
  **Timebox:** 2 hours  
  **Success:** Runbook committed.

- **Day 21 — Practice triage**  
  **Objective:** Simulate regression and triage using metrics/logs/traces.  
  **Timebox:** 2 hours

---

## Week 4 — Chaos, Security, Reliability & Platform (Days 22–30)

- **Day 22 — Install LitmusChaos**  
  **Objective:** Deploy Litmus operator and CRDs into k3d.  
  **Timebox:** 2 hours  
  **Success:** Litmus CRDs present; operator running.

- **Day 23 — Run chaos experiments**  
  **Objective:** Pod delete and CPU hog experiments against demo app.  
  **Timebox:** 2 hours  
  **Success:** Chaos events recorded; app behavior observed.

- **Day 24 — Correlate chaos with observability**  
  **Objective:** Use dashboards/traces to show chaos impact.  
  **Timebox:** 2 hours  
  **Success:** Timeline shows metric spikes and trace latency.

- **Day 25 — Kubernetes security basics**  
  **Objective:** Implement NetworkPolicies, tighten RBAC, enable PodSecurity admission.  
  **Timebox:** 3 hours  
  **Success:** Traffic restricted; RBAC enforced.

- **Day 26 — IaC & image security sweep**  
  **Objective:** Run Checkov on Terraform and Trivy on images; remediate issues.  
  **Timebox:** 2 hours  
  **Success:** Scans pass after fixes.

- **Day 27 — Incident simulation & RCA**  
  **Objective:** Create an incident (e.g., DB connection failure), run incident response, produce RCA.  
  **Timebox:** 3–4 hours  
  **Success:** Service restored; RCA documented.

- **Day 28 — Backup & restore**  
  **Objective:** Use Velero or snapshots to backup and restore a namespace.  
  **Timebox:** 3 hours  
  **Success:** Namespace restored to fresh cluster.

- **Day 29 — GitOps & platform engineering**  
  **Objective:** Scaffold GitOps repo and deploy ArgoCD to manage apps and add‑ons.  
  **Timebox:** 3 hours  
  **Success:** ArgoCD syncs repo and shows healthy apps.

- **Day 30 — Consolidate portfolio**  
  **Objective:** Tidy mono‑repo: README, runbooks, Terraform modules, Helm charts, CI workflows, and a 1‑page demo script.  
  **Timebox:** 3–4 hours  
  **Success:** End‑to‑end demo reproducible in 30–60 minutes.

---

## Deliverables (by Day 30)
- Reproducible mono‑repo with scripts, Helm charts, Terraform modules, and CI workflows.  
- Runbooks for triage, incident response, and backup/restore.  
- Observability dashboards and alert rules.  
- CI gates with Trivy and Checkov.  
- Documented chaos experiments and RCA.  
- Short demo script to walk an interviewer through your work.

---

## Next steps (post 30 days)
- Convert repeatable pieces into Ansible roles or a collection.  
- Add Molecule tests for roles and unit tests for Terraform modules.  
- Practice live demos and incident walkthroughs.

