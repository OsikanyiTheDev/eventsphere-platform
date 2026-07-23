# EventSphere Project Book

The Project Book is the source of truth for why EventSphere exists, what it must do, how it is designed, and how it will be operated. Documents are maintained as Markdown to support change reviews and release traceability.

## Parts

| Part | Subject | Planned document |
|---|---|---|
| I | Business | [Project Charter](01-project-charter.md), [business requirements](02-business-requirements-document.md), scope, stakeholders, assumptions, risks |
| II | Requirements | [Software Requirements Specification (SRS)](03-software-requirements-specification.md), personas, [user stories and acceptance criteria](04-user-stories-and-acceptance-criteria.md) |
| III | Architecture | Context, high-level, low-level, network, data-flow, component, and deployment designs |
| IV | Infrastructure | [Technology stack selection](05-technology-stack-selection.md), Terraform, state, IAM, environments, naming, tagging, and security model |
| V | Application | Frontend, backend, database, API, authentication, authorization, and storage design |
| VI | DevOps | Git workflow, quality gates, Jenkins, Docker, Kubernetes, and releases |
| VII | Operations | Monitoring, logging, alerting, backups, disaster recovery, scaling, and hardening |
| VIII | Portfolio | Evidence checklist, demo script, recruiter talking points, and interview preparation |

## Document lifecycle

- Documents begin as **Draft** during design.
- Significant technical decisions receive an ADR under `architecture/adr/`.
- Documents are reviewed through pull requests with the code they affect.
- `v0.1.0` is the initial planning-and-design release.
