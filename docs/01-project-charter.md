# Project Charter — EventSphere Cloud Platform

| Field | Value |
|---|---|
| Status | Draft |
| Release | `v0.1.0` |
| Owner | Project maintainer |
| Project type | Cloud-native event management SaaS portfolio platform |
| Primary disciplines | Cloud Engineering, DevOps, DevSecOps, Platform Engineering |

## 1. Executive summary

EventSphere Cloud Platform modernizes event management from manual, spreadsheet-driven registration into a centralized web platform for attendees, organizers, and administrators. It is designed as a cloud-native system with automated infrastructure, repeatable deployments, measurable service health, and security controls integrated into delivery workflows.

The project is both a functional SaaS-style product and an engineering case study. Its implementation will demonstrate how an application moves from a documented design to AWS infrastructure, containers, automated CI/CD, Kubernetes, observability, and operational automation.

## 2. Business problem

A growing event company currently relies on forms, spreadsheets, and manual deployments. During high-demand registration windows, this creates slow user experiences, duplicate or incorrect registration data, limited visibility into platform health, risky releases, and difficult scaling.

## 3. Vision and proposed solution

Create a secure, role-aware event platform where:

- attendees discover events, book tickets, receive confirmations, and access QR-code tickets;
- organizers create events, manage registrations, upload event media, and export attendee lists;
- administrators manage users and organizers, audit activity, oversee events, and view operational reports.

The supporting platform will use AWS-focused Infrastructure as Code, containerized services, Jenkins delivery pipelines, Kubernetes orchestration, monitoring and alerting, and Ansible configuration automation.

## 4. Objectives

1. Deliver a working event-management application with attendee, organizer, and administrator roles.
2. Provision repeatable, secure AWS foundations with Terraform instead of manual console changes.
3. Support reproducible local development through Docker Compose and deploy workloads to Kubernetes.
4. Automate test, validation, security scanning, image publishing, and deployment through Jenkins.
5. Establish actionable monitoring, dashboards, alerts, logs, and operational runbooks.
6. Demonstrate DevSecOps controls including secret management, least-privilege IAM, infrastructure validation, dependency scanning, container scanning, and quality gates.
7. Produce clear, versioned documentation and evidence suitable for a technical portfolio.

## 5. Scope

### In scope

- React frontend, Flask REST API, PostgreSQL data store, S3-backed file storage, JWT authentication, role-based authorization, email notifications, QR-code tickets, dashboards, reporting/export, and audit logs.
- AWS networking, IAM, ECR, RDS, S3, load-balancing and monitoring foundations managed by Terraform.
- Docker, Jenkins, Kubernetes, Prometheus, Grafana, Alertmanager, and Ansible workstreams.

### Out of scope for the initial release

- Payment processing and real financial transactions.
- Native mobile applications.
- Guaranteed multi-region active-active availability.
- Handling production personal data or claiming compliance certification.

## 6. Success criteria

| Area | Measurable criterion |
|---|---|
| Functionality | Core role-based event and ticket workflows pass defined acceptance tests. |
| Repeatability | A documented command path can recreate local services and infrastructure environments. |
| Delivery | A Jenkins pipeline validates, tests, scans, builds, publishes, and deploys a release candidate. |
| Observability | Dashboards expose platform and application signals; alert rules are tested. |
| Security | No secrets committed; IAM, networking, scans, and validation follow the documented controls. |
| Portfolio | README, diagrams, ADRs, release notes, screenshots, and demo checklist support a complete walkthrough. |

## 7. Key stakeholders

| Stakeholder | Primary interest |
|---|---|
| Attendee | Fast, reliable discovery, booking, confirmations, and tickets. |
| Organizer | Event publishing, attendee management, exports, and event insight. |
| Platform administrator | User governance, reporting, auditability, and service oversight. |
| Engineering / DevOps | Safe delivery, availability, scalability, cost control, and recoverability. |
| Portfolio reviewer | Evidence of sound technical decisions and end-to-end delivery capability. |

## 8. Constraints and guiding assumptions

- The platform follows a hybrid learning model: local Docker/Minikube for early workloads and AWS for infrastructure-focused work.
- AWS deployments are deliberately cost-aware, tagged, and paired with teardown procedures.
- Secrets are never stored in Git; local development uses ignored environment files and deployed environments use an appropriate secret-management approach.
- The project favors production-inspired patterns while clearly documenting any simplifications made for an individual portfolio build.

## 9. Initial risks

| Risk | Mitigation |
|---|---|
| Unintended AWS cost | Budget alerts, resource tags, low-cost alternatives, explicit destroy/runbook steps. |
| Scope expansion | Sprint goals, definition of done, and a prioritized backlog. |
| Security misconfiguration | Least privilege, IaC validation, code review, scanning, and secure defaults. |
| Tool complexity | Build in thin vertical slices; each phase has independently testable deliverables. |
| Insufficient evidence | Capture screenshots, test output, diagrams, ADRs, and release notes continuously. |
