# Technology Stack Selection

| Field | Value |
|---|---|
| Status | Draft |
| Related release | `v0.1.0` |
| Decision scope | Initial EventSphere platform stack |

## 1. Purpose

This document records why EventSphere uses its selected technologies, what alternatives were considered, and which constraints apply. The stack is selected to meet documented product and operational requirements while remaining learnable, maintainable, cost-aware, and portfolio-demonstrable.

Selections are **initial decisions**, not permanent doctrine. Any material change must be documented through an Architecture Decision Record (ADR).

## 2. Decision criteria

All technology choices are evaluated against the following criteria.

| Criterion | Description |
|---|---|
| Requirement fit | Supports the functional, security, operational, and quality requirements in the SRS. |
| Operational maturity | Has proven production patterns, observability options, and supportable failure modes. |
| Security | Supports least privilege, secure configuration, scanning, patching, and auditability. |
| Maintainability | Has understandable conventions, documentation, testing support, and a healthy ecosystem. |
| Portability | Avoids unnecessary lock-in and can support local development plus cloud deployment. |
| Cost awareness | Allows local development and low-cost AWS experimentation; cost drivers are visible. |
| Learning value | Provides relevant Cloud/DevOps/Platform Engineering experience without tool sprawl. |

## 3. Selected stack at a glance

| Capability | Initial selection | Rationale summary |
|---|---|---|
| Cloud platform | AWS | Broad cloud-service coverage and relevant infrastructure patterns. |
| Infrastructure as Code | Terraform | Declarative, modular, provider-agnostic infrastructure automation. |
| Backend API | Python + Flask | Lightweight, explicit REST API service suitable for incremental design. |
| Frontend | React | Mature component ecosystem for role-specific web experiences. |
| Relational data | PostgreSQL | Strong relational integrity, transactions, and operational maturity. |
| Object storage | Amazon S3 | Durable object storage abstraction for event-media assets. |
| Authentication | JWT with backend RBAC | Supports stateless API authentication with server-enforced authorization. |
| Containers | Docker + Docker Compose | Reproducible build/runtime units and local multi-service development. |
| CI/CD | Jenkins | Declarative pipeline experience and enterprise-recognizable automation model. |
| Orchestration | Kubernetes; Minikube initially | Portable deployment primitives and scalable-workload learning path. |
| Observability | Prometheus, Grafana, Alertmanager | Open, widely adopted metrics, visualization, and alerting stack. |
| Configuration automation | Ansible | Idempotent server configuration and operational automation. |
| Caching / async enablement | Redis, deferred | Adopt only if a documented workload requires it. |

## 4. Cloud platform — AWS

### Decision

Use AWS as the primary cloud platform. Early development remains hybrid: local Docker and Minikube are used where this minimizes cost and speeds feedback; AWS-focused Terraform is designed and applied only through cost-reviewed phases.

### Why AWS

- Supports the planned networking, IAM, load-balancing, container registry, object storage, database, monitoring, and secrets patterns.
- Provides credible material for Cloud and DevOps interview discussion.
- Encourages design around managed services, tagging, budgets, IAM, and the Well-Architected Framework.

### Alternatives and trade-offs

| Alternative | Strength | Why not primary initially |
|---|---|---|
| Azure | Strong enterprise and Microsoft ecosystem integration | AWS is selected for the project’s planned service mapping and learning focus. |
| Google Cloud | Strong data and Kubernetes ecosystem | Fewer planned project artifacts map directly to the intended AWS service portfolio. |
| Local-only | No cloud bill and rapid iteration | Does not demonstrate cloud network, IAM, managed-service, tagging, or cost-management practices. |

### Constraints

- No resource is provisioned until expected cost, tags, security posture, and teardown procedure are documented.
- Free Tier eligibility is account- and date-dependent; it is never assumed to mean zero cost.
- Multi-AZ or high-availability features are assessed against both the requirement and their cost.

## 5. Infrastructure as Code — Terraform

### Decision

Use Terraform for AWS infrastructure. Modules will isolate reusable capabilities such as networking, IAM, container registry, storage, database, and observability foundations.

### Advantages

- Declarative desired-state model, reviewable plans, reusable modules, and broad provider ecosystem.
- Clear workflow: format, initialize, validate, plan, review, apply, and destroy.
- Supports separation of environment configuration from reusable infrastructure modules.

### Alternatives and trade-offs

| Alternative | Strength | Trade-off |
|---|---|---|
| AWS CloudFormation | Native AWS integration | Less portable and less aligned with the selected multi-tool learning objective. |
| AWS CDK | Programming-language abstractions | Adds programming abstraction over IaC before the underlying AWS resources are fully understood. |
| Pulumi | General-purpose languages and strong abstractions | Good option, but Terraform has more direct visibility into declarative infrastructure state for this project. |
| Manual console work | Fast for exploration | Not reproducible, difficult to review, and unsuitable as an implementation record. |

### Security and operations requirements

- Remote state and locking strategy must be designed before shared cloud application.
- State files are sensitive and excluded from Git.
- `terraform fmt`, `validate`, plan review, and applicable security scanning are CI quality gates.

## 6. Application stack — Flask, React, PostgreSQL

### Backend: Python + Flask

**Decision:** Build the REST API with Flask using an application-factory structure, explicit configuration, validated request schemas, database migrations, tests, and layered service boundaries.

| Advantages | Trade-offs |
|---|---|
| Small surface area makes HTTP, authentication, validation, and architecture visible for learning. | More conventions must be selected and enforced than in a highly opinionated framework. |
| Mature Python ecosystem for testing, QR generation, email providers, and data utilities. | Async/background job patterns require deliberate design rather than being automatic. |
| Appropriate for a focused REST API and incremental modular monolith. | A large product might favor stronger framework defaults or separately deployed services. |

**Alternatives:** Django / Django REST Framework offers more built-in features and admin capabilities, but Flask better supports deliberately learning API architecture. FastAPI offers excellent typing and async capabilities; it remains a valid future alternative but is not selected to avoid changing stack midstream.

### Frontend: React

**Decision:** Build a role-aware single-page web application with React.

| Advantages | Trade-offs |
|---|---|
| Mature ecosystem, reusable components, strong testing/tooling support. | Client-side complexity requires attention to state, accessibility, and security boundaries. |
| Clear separation from REST API allows independent containerization and deployment. | SEO/public-rendering needs may later favor SSR, which is not in initial scope. |

**Alternatives:** Angular provides stronger built-in conventions but a heavier learning model. Vue has an approachable developer experience but React is selected for its ecosystem and job-market relevance.

### Database: PostgreSQL

**Decision:** Use PostgreSQL as the system of record for users, roles, events, bookings, tickets, and audit logs.

| Advantages | Trade-offs |
|---|---|
| ACID transactions and constraints are appropriate for booking/capacity integrity. | Schema migrations must be designed and tested carefully. |
| Strong relational queries, indexing, JSON support, and mature backup/recovery options. | Database scaling requires planning; it is not made horizontally elastic by default. |

**Alternatives:** MySQL is a credible relational option but PostgreSQL is selected for its feature set and team preference. MongoDB is flexible for documents but weaker as the default source of truth for transactional booking relationships.

## 7. Storage, identity, and optional caching

### Amazon S3

**Decision:** Use S3-compatible object storage patterns for event images and permitted file assets; local development will use a documented local-compatible approach if needed.

**Why:** Application binaries or user-uploaded media do not belong in PostgreSQL tables or container filesystems. Object storage supports separation of durable assets from application workloads.

**Security expectations:** private-by-default access, restricted upload types/sizes, generated identifiers, least-privilege IAM, encryption configuration, and no public bucket policy by default.

### JWT authentication and RBAC

**Decision:** Use signed JSON Web Tokens for authenticated API access, with authorization enforced by backend role and ownership checks.

**Trade-off:** JWTs simplify stateless API authentication but require secure signing key handling, expiry, refresh/revocation design, and careful client storage decisions. They do not replace backend authorization.

### Redis — deferred decision

Redis will not be introduced merely to add another tool. It becomes justified if a designed workload needs caching, rate-limit counters, job queues, or distributed coordination. If adopted, it must have explicit data classification, eviction, availability, and monitoring decisions.

## 8. Containers and local environment — Docker and Docker Compose

### Decision

Package frontend and backend services as Docker images. Use Docker Compose to provide a reproducible local multi-service environment, including PostgreSQL and only the dependencies needed for local workflows.

### Why

- Prevents environment drift between developer machines and CI.
- Establishes immutable-image practices used later by Jenkins and Kubernetes.
- Makes service dependencies, health checks, network paths, volumes, and environment variables explicit.

### Trade-off

Docker Compose is a local integration environment, not a production orchestrator. It will be superseded by Kubernetes deployment manifests for orchestrated workloads.

## 9. CI/CD — Jenkins

### Decision

Use a declarative `Jenkinsfile` as the primary delivery pipeline definition.

### Planned quality gates

```text
Checkout → lint/format → unit tests → integration tests → dependency scan
→ container build → image scan → image publish → deploy → smoke test → notify
```

### Alternatives and trade-offs

| Alternative | Strength | Why Jenkins is selected |
|---|---|---|
| GitHub Actions | Native GitHub integration and low operational overhead | Jenkins better demonstrates traditional enterprise CI/CD administration and declarative pipelines. |
| GitLab CI | Strong integrated DevSecOps features | Requires a different source-control platform than the project’s GitHub-first approach. |
| Manual deployments | Low setup effort | Fails repeatability, traceability, and quality-gate requirements. |

Jenkins adds operational responsibility: controller/agent hardening, credential management, plugin control, backups, and least-privilege cloud access must be documented.

## 10. Orchestration — Kubernetes and Minikube

### Decision

Use Kubernetes manifests for the application runtime. Start with Minikube to develop and verify deployment behavior locally; later document adaptation to a managed Kubernetes service if justified by the production-deployment phase.

### Why

Kubernetes provides portable, declarative constructs for deployments, services, configuration, secrets, ingress, resource limits, health probes, rolling updates, rollbacks, and autoscaling.

### Trade-off

Kubernetes has meaningful operational complexity and is not automatically the right solution for every application. We use it because orchestration is a stated platform objective, while documenting that a smaller production workload might appropriately use ECS/Fargate or another managed container service.

## 11. Observability — Prometheus, Grafana, Alertmanager

### Decision

Use Prometheus for metrics collection, Grafana for visualization, and Alertmanager for alert routing/deduplication. Application health endpoints and metrics will be exposed intentionally rather than relying only on infrastructure metrics.

### Why

The stack supports the required visibility into resource health, availability, request latency, error rate, and workload state. It is widely recognizable and integrates well with Kubernetes.

### Trade-off

This is a metrics-centric stack. Centralized logs and distributed tracing are separate concerns and will be scoped as operational enhancements rather than claimed as complete on day one.

## 12. Configuration automation — Ansible

### Decision

Use Ansible for idempotent server configuration tasks that Terraform should not own: package installation, OS hardening, users, Docker/Nginx setup, monitoring agents, and repeatable configuration.

### Why Terraform and Ansible coexist

```text
Terraform: creates cloud resources and their desired infrastructure topology.
Ansible: configures operating systems and software running inside provisioned hosts.
```

Terraform can provision an EC2 instance; Ansible can configure it consistently. Keeping those responsibilities distinct makes changes clearer and safer.

## 13. Architecture decision backlog

| Decision | Status | Planned phase |
|---|---|---|
| Environment model (local/dev/staging/prod) | Pending | Architecture |
| AWS account/region and cost guardrails | Pending | Architecture / Terraform |
| State backend, locking, and bootstrap sequence | Pending | Terraform |
| Email provider and delivery/failure model | Pending | Application architecture |
| JWT expiry, refresh, revocation, and client-storage policy | Pending | Security/application design |
| Database migration framework and seed strategy | Pending | Application design |
| Kubernetes ingress and TLS strategy | Pending | Kubernetes design |
| Centralized logging and tracing scope | Pending | Operations design |
| Redis adoption | Deferred | Application design, only if justified |
