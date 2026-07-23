# Scope, Assumptions, Risks, and Success Criteria

| Field | Value |
|---|---|
| Status | Draft |
| Related release | `v0.1.0` |
| Related documents | Project Charter, BRD, SRS, User Stories, Technology Stack Selection |

## 1. Purpose

This document establishes delivery boundaries for EventSphere. It records what will be built, what will deliberately not be built in the initial release path, the assumptions on which the design depends, material delivery risks, and the evidence required to call the portfolio release successful.

A documented boundary is a delivery control: it prevents a feature request or technology from entering the project merely because it is interesting.

## 2. Product scope

### 2.1 In scope — business capabilities

| Area | Included capability | Requirement references |
|---|---|---|
| Identity | Registration, login, JWT-based authentication, profile management, attendee/organizer/admin roles | FR-01 to FR-04 |
| Event lifecycle | Create, edit, publish, unpublish, cancel, complete, browse, search, and filter events | FR-05 to FR-09 |
| Booking | Capacity-aware booking, booking history, ticket identifiers, QR tickets, confirmation notification attempt | FR-10 to FR-14 |
| Organizer operations | Registration view, attendee export, event-scoped analytics, media upload | FR-09, FR-15 to FR-17 |
| Administration | User/organizer governance, event oversight, administrative dashboard, audit review | FR-18 to FR-20 |
| API | Versioned REST API, validation, consistent errors, health endpoint | FR-21 to FR-24 |

### 2.2 In scope — platform engineering capabilities

| Area | Included capability | Expected evidence |
|---|---|---|
| Infrastructure | Terraform modules for reviewed AWS foundations | Module README, plan output, architecture diagram, cost/teardown guidance |
| Containers | Dockerfiles and Docker Compose local environment | Reproducible build and documented `docker compose` workflow |
| Delivery | Declarative Jenkins pipeline with defined quality gates | Jenkinsfile, build evidence, test/scan output |
| Orchestration | Kubernetes manifests for services, configuration, ingress, probes, scaling, and safe rollout | Local Minikube deployment and rollout/rollback evidence |
| Observability | Metrics, dashboards, alerts, and health signals | Prometheus config, Grafana dashboards, tested alert evidence |
| Automation | Ansible playbooks/roles for defined server configuration work | Idempotent playbook runs and verification evidence |
| Documentation | Design, build, security, operation, recovery, and portfolio documentation | Versioned Project Book, ADRs, runbooks, diagrams |

## 3. Explicit exclusions

The following are intentionally excluded from the initial EventSphere release. They may become future enhancements only after the core platform is complete and stable.

| Exclusion | Reason |
|---|---|
| Payment processing, refunds, and cardholder data | Introduces financial, legal, security, and PCI DSS obligations outside the portfolio scope. |
| Native mobile applications | Web platform is the delivery priority; responsive web design meets initial access needs. |
| Multi-region active-active architecture | High complexity and cost; availability/recovery design will be proportionate to the project stage. |
| Public multi-tenant marketplace onboarding | Requires deeper tenant isolation, legal workflows, abuse controls, and support processes. |
| Formal compliance certification | The project can demonstrate controls but cannot claim SOC 2, ISO 27001, or PCI certification. |
| Full centralized logging/tracing platform | Metrics and alerts are core scope; logs/traces will be documented as phased operational enhancements unless needed earlier. |
| 24/7 staffed support and formal on-call rotation | Runbooks and alert design are in scope; organizational staffing is not. |
| Production personal data | Demos use synthetic data only. |

## 4. Delivery boundaries by environment

| Environment | Purpose | Data policy | Initial technology direction |
|---|---|---|---|
| Local development | Fast implementation and repeatable developer feedback | Synthetic/local data only | Docker Compose, local PostgreSQL, local configuration |
| Local Kubernetes | Validate manifests, probes, rollout, and observability behavior | Synthetic data only | Minikube |
| AWS learning/deployment target | Validate Terraform, AWS integrations, and documented cloud practices | Synthetic data only | Cost-reviewed AWS services |
| Production-style portfolio release | Demonstrate a release-ready operating model | Synthetic/demo data only | Documented target architecture and evidence |

> “Production-style” means production-inspired design and controls. It does not claim that the platform is handling real customers, has contractual SLAs, or has completed a compliance audit.

## 5. Assumptions

| ID | Assumption | Impact if invalid |
|---|---|---|
| ASM-01 | The project is maintained by a small engineering team or individual using disciplined review practices. | Delivery plan and operational ownership would need redesign for a larger team. |
| ASM-02 | Local Docker and Minikube are available for early development. | Local workflow and environment strategy must change. |
| ASM-03 | An AWS account can be used for carefully scoped experimentation. | AWS phase becomes design-only or requires an alternative cloud/sandbox. |
| ASM-04 | GitHub is the source-control and review system. | CI triggers, branch protection, templates, and workflow integrations must change. |
| ASM-05 | Email delivery can be abstracted behind a provider interface. | Notification implementation and testing strategy must change. |
| ASM-06 | Initial event capacity and traffic can be simulated with test data and load tests. | Real workload data would be required to set performance targets. |
| ASM-07 | The platform uses synthetic demo data only. | Privacy, consent, retention, and legal obligations significantly increase. |

## 6. Constraints

| ID | Constraint | Design response |
|---|---|---|
| CON-01 | Cloud cost must be controlled. | Cost review, tags, budgets, local-first validation, teardown guidance, and low-cost alternatives. |
| CON-02 | Security must be built into delivery, not postponed. | Secret hygiene, least privilege, scanning, validation, RBAC, audit records, and reviews. |
| CON-03 | Each phase must be understandable and demonstrable. | Small vertical increments, documentation, test evidence, and release notes. |
| CON-04 | The technology stack is intentionally broad. | Avoid parallel tool implementation; activate tools only in their planned phase. |
| CON-05 | No shortcuts that hide core concepts. | Explain designs, alternatives, commands, configuration, and operational trade-offs. |
| CON-06 | Portfolio claims must be accurate. | Clearly state local/demo limitations and avoid unsupported production/compliance claims. |

## 7. Risk assessment

### 7.1 Risk scoring method

Likelihood and impact are scored from 1 (low) to 5 (high). Risk score = likelihood × impact.

| Score | Classification | Response expectation |
|---:|---|---|
| 1–5 | Low | Track; address during normal work. |
| 6–12 | Medium | Define mitigation and review at sprint boundaries. |
| 15–25 | High | Mitigate before affected implementation or accept explicitly with rationale. |

### 7.2 Risk register

| ID | Risk | Likelihood | Impact | Score | Mitigation / contingency | Owner |
|---|---|---:|---:|---:|---|---|
| RSK-01 | Unexpected AWS charges | 3 | 5 | 15 | Estimate costs; use tags/budgets; prefer local validation; document destroy procedures; review paid resources before apply. | Platform owner |
| RSK-02 | Scope growth prevents milestone completion | 4 | 4 | 16 | Maintain explicit exclusions; prioritize Must stories; defer enhancements to backlog. | Product owner |
| RSK-03 | Secrets or sensitive configuration are exposed | 2 | 5 | 10 | `.gitignore`, secret scanning, no real secrets in examples, least-privilege credentials, review checklist. | Platform owner |
| RSK-04 | Authorization defects expose another user's/event owner's data | 3 | 5 | 15 | Backend RBAC/ownership enforcement, negative tests, audit records, secure API review. | Application owner |
| RSK-05 | Booking concurrency causes over-capacity events | 3 | 4 | 12 | Design transactions/constraints; create concurrency-focused tests before claiming capacity correctness. | Application owner |
| RSK-06 | Toolchain complexity causes stalled delivery | 3 | 4 | 12 | Follow phase gates; complete thin slices; use ADRs to avoid revisiting settled decisions without cause. | Project owner |
| RSK-07 | Jenkins or Kubernetes operational overhead exceeds learning value | 3 | 3 | 9 | Start locally and incrementally; document minimum viable topology; avoid premature production hosting. | Platform owner |
| RSK-08 | Terraform state is mishandled or exposed | 2 | 5 | 10 | Design remote state/locking before shared use; never commit state; restrict access and document recovery. | Platform owner |
| RSK-09 | Lack of monitoring delays incident detection | 3 | 4 | 12 | Define service-level signals, dashboards, alert rules, and runbooks before release. | Operations owner |
| RSK-10 | Portfolio evidence is not captured during work | 3 | 3 | 9 | Capture diagrams, tests, pipeline output, dashboards, and release notes at each milestone. | Project owner |

## 8. Success criteria and evidence

| ID | Success criterion | Evidence required by `v1.0.0` |
|---|---|---|
| SC-01 | Core user journeys work securely. | Automated tests and demo evidence for registration, authentication, event management, booking, ticket retrieval, and governance. |
| SC-02 | Infrastructure is repeatable and reviewable. | Terraform modules, validations, plan evidence, tags, state strategy, cost review, and destroy guidance. |
| SC-03 | The application is reproducible locally. | Documented Docker Compose workflow, health checks, test data, and setup guide. |
| SC-04 | Delivery is automated and controlled. | Jenkinsfile, quality gate results, image/version evidence, deployment and smoke-test evidence. |
| SC-05 | Kubernetes behavior is demonstrated. | Manifests, probe/resource configuration, rollout/rollback evidence, and scaling test where applicable. |
| SC-06 | Operations are observable. | Dashboards, alerts, metrics configuration, alert test, and linked response guidance. |
| SC-07 | Security is demonstrably integrated. | Secret-handling approach, scan/validation evidence, IAM/network design, RBAC tests, and audit evidence. |
| SC-08 | Operations are recoverable. | Backup/restore design, recovery test evidence, incident/runbook documentation. |
| SC-09 | Portfolio presentation is professional and accurate. | README, Project Book, diagrams, ADRs, screenshots, release notes, and transparent limitations. |

## 9. Scope-control procedure

Any proposed addition must answer the following before work begins:

1. Which business objective or requirement does it satisfy?
2. Is it Must, Should, or future backlog work?
3. What security, cost, operational, and documentation impact does it create?
4. Does it threaten the current sprint definition of done?
5. Does it require an ADR or a change to existing requirements?

If it does not satisfy a current requirement or milestone objective, it is placed in the future-enhancements backlog rather than added immediately.
