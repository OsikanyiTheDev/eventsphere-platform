# User Stories and Acceptance Criteria

| Field | Value |
|---|---|
| Status | Draft |
| Related requirements | [SRS](03-software-requirements-specification.md) |
| Target release | `v0.1.0` requirements baseline |

## 1. Purpose

This document turns the SRS into user-centered, testable delivery scenarios. Each story is traceable to one or more SRS requirements and will later inform API tests, frontend tests, end-to-end tests, and release acceptance checks.

## 2. Story conventions

- **Priority:** Must = required for a viable platform release; Should = planned after the core flow; Could = valuable but deferrable.
- **Acceptance criteria:** written in Given/When/Then style so they can be translated into automated tests.
- **Definition of done:** a story is not complete solely because its interface works; it must meet applicable security, logging, test, and documentation expectations.

## 3. Attendee stories

### US-ATT-01 — Create an account

**As an** attendee, **I want** to create an account, **so that** I can manage bookings and retrieve my tickets.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | FR-01, FR-03, SEC-01, SEC-03 |

**Acceptance criteria**

```gherkin
Given I provide a unique valid email and a password that meets the published policy
When I submit the registration form
Then the platform creates my account with the attendee role
And it does not store or return my plaintext password.

Given an account already exists for an email address
When I attempt to register using that address
Then the platform rejects the request safely
And it does not expose unnecessary account information.
```

### US-ATT-02 — Authenticate securely

**As an** attendee, **I want** to sign in securely, **so that** I can access my profile and tickets.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | FR-02, SEC-01, SEC-03 |

**Acceptance criteria**

```gherkin
Given I have valid account credentials
When I sign in
Then I receive an authenticated session/token response appropriate to my role.

Given I supply invalid credentials
When I attempt to sign in
Then access is denied with a safe generic response
And the response does not reveal whether the email or password was incorrect.
```

### US-ATT-03 — Discover events

**As an** attendee, **I want** to browse and filter published events, **so that** I can find relevant events efficiently.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | FR-05, FR-06 |

**Acceptance criteria**

```gherkin
Given published and draft events exist
When I browse public events
Then I can see only published events.

Given I apply a supported keyword, date, category, or location filter
When I submit the filter
Then the results contain matching published events or a clear empty-result state.
```

### US-ATT-04 — Book an available event

**As an** attendee, **I want** to book a ticket for an available event, **so that** I can attend it.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | FR-10, FR-11, FR-14, SEC-02 |

**Acceptance criteria**

```gherkin
Given I am authenticated and an event is published with remaining capacity
When I submit a booking request
Then exactly one confirmed booking is created for the successful request
And the platform records the updated availability according to the event rules.

Given an event is cancelled, unpublished, or at capacity
When I attempt to book it
Then the platform rejects the booking
And no booking record is created.
```

### US-ATT-05 — Retrieve ticket and booking history

**As an** attendee, **I want** to see my bookings and retrieve my QR ticket, **so that** I can prove my registration.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | FR-12, FR-13, SEC-02 |

**Acceptance criteria**

```gherkin
Given I have a confirmed booking
When I view my booking history
Then I can see that booking and retrieve its unique QR-code ticket.

Given I request another attendee's booking or ticket identifier
When I am not authorized for that record
Then the platform denies access without returning ticket data.
```

## 4. Organizer stories

### US-ORG-01 — Create and manage an event

**As an** organizer, **I want** to create and edit events that I own, **so that** I can publish accurate event information.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | FR-07, FR-08, SEC-02, SEC-07 |

**Acceptance criteria**

```gherkin
Given I am an authenticated organizer
When I create an event with valid required information
Then a draft event is created with me as its organizer.

Given I own a draft event
When I publish it after meeting publication requirements
Then its status becomes published and it becomes discoverable.

Given another organizer owns an event
When I attempt to alter it
Then the platform denies the request and records the applicable audit event.
```

### US-ORG-02 — Upload event media

**As an** organizer, **I want** to upload an event image, **so that** attendees can recognize my event.

| Attribute | Value |
|---|---|
| Priority | Should |
| Traces to | FR-09, SEC-02 |

**Acceptance criteria**

```gherkin
Given I own an event and select a permitted image type within the size limit
When I upload the image
Then the platform stores the file through the approved storage path and associates it with my event.

Given I upload an unsupported type or oversized file
When the file is validated
Then the upload is rejected safely and no unapproved file is made available.
```

### US-ORG-03 — View registrations and export attendees

**As an** organizer, **I want** to view and export attendees for my event, **so that** I can prepare event operations.

| Attribute | Value |
|---|---|
| Priority | Must for view; Should for export |
| Traces to | FR-15, FR-16, FR-20, SEC-02, SEC-07 |

**Acceptance criteria**

```gherkin
Given I own an event with confirmed bookings
When I view its registrations
Then I see only the approved attendee fields for that event.

Given I request an attendee export for my event
When the export succeeds
Then the file contains only approved fields for that event
And the export action is captured in the audit trail.
```

### US-ORG-04 — Review event analytics

**As an** organizer, **I want** to see registration analytics for my events, **so that** I can make operational decisions.

| Attribute | Value |
|---|---|
| Priority | Should |
| Traces to | FR-17, SEC-02 |

**Acceptance criteria**

```gherkin
Given I own one or more events
When I open the organizer dashboard
Then I see the documented registration metrics for only my events.
```

## 5. Administrator stories

### US-ADM-01 — Govern users and organizers

**As an** administrator, **I want** to manage user and organizer status, **so that** the platform remains governed and safe.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | FR-18, FR-20, SEC-02, SEC-07 |

**Acceptance criteria**

```gherkin
Given I am an authenticated administrator
When I perform an authorized user or organizer governance action
Then the change is persisted
And the audit record contains the actor, action, target, timestamp, and outcome.

Given I am not an administrator
When I attempt the same action
Then the backend denies the request.
```

### US-ADM-02 — Review platform summaries

**As an** administrator, **I want** to view user, event, and booking summaries, **so that** I can oversee the platform.

| Attribute | Value |
|---|---|
| Priority | Should |
| Traces to | FR-19, SEC-02 |

**Acceptance criteria**

```gherkin
Given I am an authorized administrator
When I open the administrative dashboard
Then I can view the defined platform summary measures.
```

### US-ADM-03 — Review audit records

**As an** administrator, **I want** to review sensitive actions, **so that** I can investigate misuse or operational changes.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | FR-20, SEC-07 |

**Acceptance criteria**

```gherkin
Given I am an authorized administrator
When I query audit records using a supported filter
Then I receive matching audit entries without secrets or plaintext credentials.
```

## 6. Engineering and operational stories

### US-OPS-01 — Provision reviewed infrastructure

**As an** engineering operator, **I want** to provision infrastructure from reviewed Terraform code, **so that** environments are consistent and auditable.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | NFR-04, OPS-01, SEC-05 |

**Acceptance criteria**

```gherkin
Given approved Terraform configuration and environment inputs
When I run documented validation and plan commands
Then validation succeeds or reports actionable errors before resources are changed.

Given an approved plan
When I apply it using authorized credentials
Then only the declared, correctly tagged resources are created or changed.
```

### US-OPS-02 — Deliver a validated application image

**As an** engineering operator, **I want** a Jenkins pipeline to validate and publish an immutable image, **so that** deployments are repeatable.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | NFR-05, OPS-02, SEC-06 |

**Acceptance criteria**

```gherkin
Given a change is eligible for the delivery pipeline
When Jenkins runs the pipeline
Then it executes the defined linting, tests, security checks, image build, and publish stages.

Given a required quality gate fails
When the pipeline reaches that stage
Then it stops before deployment and records actionable failure evidence.
```

### US-OPS-03 — Observe and respond to service health

**As an** engineering operator, **I want** dashboards and alerts for service health, **so that** I can detect and respond to degradation.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | NFR-03, OPS-04 |

**Acceptance criteria**

```gherkin
Given platform metrics are being collected
When I open the operations dashboard
Then I can review defined availability, latency, error-rate, and resource-health signals.

Given a defined alert threshold is breached for its evaluation period
When Alertmanager evaluates the alert
Then a notification is routed to the configured receiver with a response reference.
```

### US-OPS-04 — Restore data and service safely

**As an** engineering operator, **I want** a documented backup and restore process, **so that** service data can be recovered after an incident.

| Attribute | Value |
|---|---|
| Priority | Must |
| Traces to | NFR-06, OPS-05 |

**Acceptance criteria**

```gherkin
Given an approved backup and restore procedure
When an operator performs a scheduled recovery exercise in a non-production environment
Then the process produces documented evidence of the restore result and recovery timing.
```

## 7. Story-to-test mapping

| Test layer | Primary evidence |
|---|---|
| Unit tests | Validation, business rules, authorization helpers, service logic |
| API/integration tests | Endpoint status codes, payload contracts, persistence, role/ownership denial cases |
| Frontend tests | Form behavior, route protection, user-visible states |
| End-to-end tests | Registration, login, event creation, booking, ticket retrieval, governance flows |
| Infrastructure validation | `terraform fmt`, `validate`, plan review, policy/scanner results |
| Pipeline validation | Jenkins stage results, image scan reports, deployment/smoke-test evidence |
| Operational validation | Dashboard screenshots, alert test evidence, restore exercise record |

## 8. Initial backlog ordering

1. Identity, backend authorization foundation, and health endpoint.
2. Event model and organizer event lifecycle.
3. Public event discovery and attendee booking.
4. Ticket generation, booking history, and notification abstraction.
5. Organizer registrations/export and audit records.
6. Administration and analytics dashboards.
7. DevOps capabilities are introduced in their planned phases, with requirements carried forward from this baseline.
