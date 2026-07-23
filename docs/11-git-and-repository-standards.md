# Git and Repository Standards

**Status:** Draft  
**Applies from:** Sprint 0

## Branching model

The repository uses a practical GitHub Flow model:

- `main` — stable, release-ready history; protected when hosted on GitHub.
- `feature/<short-description>` — scoped implementation work.
- `docs/<short-description>` — documentation-only work.
- `fix/<short-description>` — defect correction.
- `chore/<short-description>` — maintenance, tooling, or repository operations.

Work is merged through pull requests. Direct commits to `main` should be avoided once branch protection is enabled.

## Pull request expectations

Each pull request should include:

1. A concise problem and solution description.
2. Linked issue or sprint task where available.
3. Test or validation evidence.
4. Documentation updates when behavior, architecture, operations, or security are affected.
5. A note of security and cost impact, when applicable.

## Commit convention

Use Conventional Commit-inspired messages:

```text
feat(application): add organizer event creation endpoint
docs(architecture): add initial network design
infra(terraform): create VPC module
fix(api): reject expired JWTs
chore(ci): add formatting validation
```

## Release tags

Milestones use annotated semantic version tags:

```text
v0.1.0  Planning and Design
v0.2.0  Terraform Infrastructure
...
v1.0.0  Production-style Portfolio Release
```

## Initial GitHub branch protection recommendation

For `main`, require pull requests, successful required checks, resolved review conversations, and no force pushes. The exact enforcement settings will be recorded when the remote GitHub repository is configured.
