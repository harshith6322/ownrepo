# DevOps Interview Notes

## 1. DevOps

DevOps is a set of practices, cultural philosophies, and tools that combine software development and IT operations to deliver high‑quality software faster and more reliably.

## 2. DevSecOps

DevSecOps integrates security into every stage of the software development lifecycle, making security a shared responsibility across development, security, and operations teams.

## 3. Benefits of DevOps

- Improved collaboration between teams
- Faster and more frequent delivery
- Increased reliability and availability
- Better scalability
- Enhanced security (when integrated)
- Reduced lead time for changes and faster recovery

## 4. What do you take into consideration when choosing a tool/technology?

- Maturity and stability vs. cutting edge
- Community size and ecosystem (plugins, integrations, support)
- Architecture (agent vs. agentless, master vs. masterless)
- Learning curve and team expertise
- Licensing, cost, and vendor lock‑in
- Compatibility with existing workflows and CI/CD pipelines

## 5. Can you describe which tool or platform you chose to use in some of the following areas and how?

- CI/CD: Jenkins, CircleCI, Travis CI, Drone, Argo CD, Zuul
- Provisioning / IaC: Terraform, CloudFormation
- Configuration management: Ansible, Puppet, Chef
- Monitoring & alerting: Prometheus, Nagios
- Logging / log processing: Logstash, Graylog, Fluentd
- Code review: Gerrit, Review Board
- Code coverage: Cobertura, Clover, JaCoCo
- Issue tracking: Jira, Bugzilla
- Containers & orchestration: Docker, Podman, Kubernetes, Nomad
- Testing frameworks: Robot Framework, Serenity, Gauge

Be prepared to explain why you chose a specific tool and how you used it in practice.

## 6. Differences between DevOps and SRE

- DevOps: cultural/process practices to improve speed and efficiency of development and deployment.
- SRE: applies software engineering to operations, focusing on reliability, uptime, and scalability of production systems.

## 7. SRE responsibilities

- Define and enforce SLOs/SLIs and manage error budgets
- Incident response, blameless postmortems, and root cause analysis
- Automation to reduce operational toil
- Capacity planning, performance tuning, and reliability engineering
- Improve monitoring, observability, and alerting

## 8. What are SLIs, SLOs, and SLAs?

SLI: actual measurement (latency, error rate)

SLO: target reliability (99.9%)

SLA: business contract with penalties

## 9. What is your approach when you don’t know something?

Break problem down

Read docs

Test hypotheses

Ask clearly

---

## 10. DevOps Core Principles (CAMS)

DevOps is built on **CAMS**:

- **Culture** – shared ownership, collaboration, blameless mindset
- **Automation** – eliminate manual, repeatable work
- **Measurement** – track performance, reliability, and business impact
- **Sharing** – knowledge, failures, learnings across teams

💡 Interview angle: DevOps is **not tools**, it’s **how teams work**.

---

## 11. DevOps Lifecycle (End-to-End View)

- Plan → Code → Build → Test → Release → Deploy → Operate → Monitor → Feedback
- Feedback loop is **critical** for continuous improvement
- Shift-left testing & security, shift-right monitoring & learning

---

## 12. DevSecOps – Deeper View

DevSecOps means:

- **Security is a shared responsibility**
- Security baked into:
  - Design
  - Development
  - CI/CD
  - Runtime

- Automate security checks instead of manual gates

### Key DevSecOps Principles

- Shift-left security
- Least privilege access
- Secure by default configurations
- Continuous vulnerability scanning
- Secrets never hard-coded

💡 Interview angle:

> “Security should slow attackers, not developers.”

---

## 13. DevOps vs DevSecOps vs SRE vs Platform Engineering

### DevOps

- Focus: **Speed + collaboration**
- Goal: Faster, safer delivery

### DevSecOps

- Focus: **Security everywhere**
- Goal: Reduce risk without slowing delivery

### SRE

- Focus: **Reliability & resilience**
- Goal: Keep systems **available, scalable, predictable**

### Platform Engineering

- Focus: **Developer productivity**
- Goal: Internal platforms that abstract infra complexity

💡 Interview tip: Many companies now run **DevOps + SRE + Platform teams together**.

---

## 14. Platform Engineering (Must-Know in 2025)

Platform Engineering provides:

- Self-service infrastructure
- Standardized deployment paths
- Guardrails, not gates

### Platform Team Responsibilities

- Build Internal Developer Platform (IDP)
- Abstract cloud/K8s complexity
- Golden paths for deployment
- Standard observability & security

💡 Interview quote:

> “Platform teams build the road; developers drive on it.”

---

## 15. What Is an Internal Developer Platform (IDP)?

An IDP typically offers:

- Self-service provisioning
- CI/CD templates
- Observability out of the box
- Security & compliance defaults

Outcome:

- Dev teams focus on **code**
- Ops teams focus on **stability**

---

## 16. Reliability vs Availability vs Resilience

- **Availability** – system is up (uptime %)
- **Reliability** – system works correctly over time
- **Resilience** – system recovers from failure

💡 High availability ≠ high reliability
💡 Resilience matters more than preventing failures

---

## 17. Failure Is Inevitable (SRE Mindset)

Modern systems assume:

- Servers will fail
- Networks will fail
- Deployments will fail

So we design for:

- Graceful degradation
- Auto-healing
- Fast rollback
- Observability

💡 Interview gold line:

> “We don’t prevent all failures — we reduce blast radius.”

---

## 19. Observability vs Monitoring

### Monitoring

- Known metrics
- Predefined alerts
- “Is the system up?”

### Observability

- Metrics + Logs + Traces
- Understand **why** something broke
- Debug unknown issues

💡 Interview angle:

> Monitoring tells _something broke_, observability tells _why_.

---

## 20. Incident Management Concepts

Good incident response includes:

- Clear severity levels
- Fast detection
- Clear ownership
- Communication
- Post-incident learning

### Blameless Postmortems

- No finger-pointing
- Focus on systems & processes
- Prevent recurrence

---

## 21. Mean Time Metrics (MTTR, MTTD)

- **MTTD** – Mean Time To Detect
- **MTTR** – Mean Time To Recover
- **MTBF** – Mean Time Between Failures

Goal:

- Reduce MTTD and MTTR
- MTTR matters more than uptime %

---

## 24. Shift-Left vs Shift-Right

### Shift-Left

- Testing, security early
- Catch bugs faster
- Lower fix cost

### Shift-Right

- Production monitoring
- Feature flags
- Real user feedback

Modern teams do **both**.

---

## 25. GitOps (Conceptual, Not Tool)

Git as:

- Source of truth
- Audit log
- Desired state

Benefits:

- Traceability
- Easy rollback
- Declarative operations

---

## 28. Anti-Patterns Interviewers Look For

🚫 DevOps = one person
🚫 Security as final gate
🚫 Manual approvals everywhere
🚫 SSH into prod regularly
🚫 No metrics or observability

---

## 29. How to Answer “What Is DevOps to You?”

Strong sample structure:

1. Cultural change
2. Automation
3. Feedback loops
4. Reliability and security
5. Business value

---
