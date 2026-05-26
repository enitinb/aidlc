# AI-DLC Demo: Serverless URL Shortener API

A short, runnable end-to-end demo of the **AI-Driven Life Cycle (AI-DLC)** methodology using nothing but copy-paste prompts. Inspired by [awslabs/aidlc-workflows](https://github.com/awslabs/aidlc-workflows) but **does not depend on the steering rules being installed** — each prompt is self-contained and embodies the methodology (role assignment, planning file with checkboxes, approval gate, then execution).

## What this demo produces

A working serverless URL shortener API on AWS with a complete artifact tree organized under a single project folder:

```
url-shortener-demo/              # Project folder (name chosen at start)
├── aidlc/                       # All AI-DLC artifacts in one place
│   ├── prompts.md               # Every prompt used, in order (audit trail)
│   ├── inception/
│   │   ├── stories_plan.md
│   │   ├── user_stories.md
│   │   ├── units_plan.md
│   │   └── units/
│   │       └── url-shortener-api.md
│   ├── construction/
│   │   ├── domain_design_plan.md
│   │   ├── tasks_plan.md
│   │   └── url-shortener-api/
│   │       └── domain_model.md
│   └── operations/
│       ├── deployment_plan.md
│       ├── prerequisites.md
│       ├── deployment_runbook.md
│       ├── validation_report.md
│       ├── observability_plan.md
│       ├── observability.md
│       ├── observability_validation.md
│       ├── incident_response_plan.md
│       ├── severity_matrix.md
│       ├── runbooks/
│       │   ├── dynamodb_throttling.md
│       │   └── lambda_errors.md
│       ├── postmortem_template.md
│       ├── maintenance_plan.md
│       ├── patching.md
│       ├── backup_restore.md
│       ├── cost_management.md
│       ├── prr_plan.md
│       └── production_readiness_checklist.md
├── src/
│   ├── handlers/                # Lambda handlers per endpoint
│   ├── domain/                  # Aggregates, entities, repositories, services
│   └── infrastructure/          # DynamoDB repository
├── infra/                       # CDK TypeScript stack
└── tests/
```

## The system under construction

A serverless URL shortener API on AWS exposing three endpoints:

| Method | Path | Behavior |
|---|---|---|
| POST | `/shorten` | Takes a long URL, returns a short code |
| GET | `/{code}` | 301 redirect to the original URL |
| GET | `/{code}/stats` | Returns the click count |

**Targets:** 100 req/sec, p99 < 300ms, single region `us-east-1`. API only — no frontend.

**Stack:** API Gateway + Lambda (Python) + DynamoDB, deployed via CDK (TypeScript).

## How to run the demo

1. Open any AI coding agent (Claude Code, Amazon Q Developer, Cursor, Cline, Kiro, Copilot Chat, etc.) in a parent workspace folder.
2. Paste the **Universal Instructions** (see section below) first. The agent confirms understanding and asks for a project name.
3. Reply with a project name (e.g., `url-shortener-demo`). The agent creates the project folder and `aidlc/` structure inside it.
4. Paste **Prompt 1.1**. The agent appends it to `aidlc/prompts.md`, writes `aidlc/inception/stories_plan.md` with checkboxes, and asks for your approval.
5. Say *"approved, proceed"*. The agent executes the plan and ticks the boxes.
6. Repeat with **Prompts 1.2 → 3.5** in order.
7. Optional: use **Prompt 3.6** if anything breaks along the way.

Total run time ≈ 25–30 minutes if you approve quickly. The final PRR prompt (3.5) is the closer — it can legitimately flag a gap and refuse to mark the system production-ready, which is the methodology's whole point.

## Operations phase coverage

The Operations phase prompts map 1:1 to the [official AI-DLC future scope](https://github.com/awslabs/aidlc-workflows/blob/main/aidlc-rules/aws-aidlc-rule-details/operations/operations.md):

| Future-scope item | Prompt | Produces |
|---|---|---|
| Deployment planning and execution | 3.1 | `deployment_plan.md`, `prerequisites.md`, `deployment_runbook.md`, `validation_report.md`, CDK stack |
| Monitoring and observability setup | 3.2 | CloudWatch dashboard (3 widgets), 1 alarm → SNS, structured JSON logs |
| Incident response procedures | 3.3 | Severity matrix, 2 runbooks, postmortem template |
| Maintenance and support workflows | 3.4 | Patching policy, DynamoDB PITR + restore drill, $50 budget alarm |
| Production readiness checklists | 3.5 | Blocking PASS / FAIL / GAP audit across all prior artifacts |

---

# Universal Instructions

**These instructions apply to every prompt in this README. Issue them once at the start of the demo, before Prompt 1.1, so the agent applies them throughout the session.**

````
Universal instructions for this session (apply to every prompt that follows):

1. Project folder: Before doing anything else, ask me for a project name. Create a single top-level folder with that name and place ALL artifacts inside it — both the aidlc/ documentation tree and the source code (src/, infra/, tests/, etc.). Nothing should be created outside this project folder.

2. Folder layout inside the project folder:
   - aidlc/                  — all methodology artifacts (inception, construction, operations, prompts.md)
   - src/                    — application code
   - infra/                  — infrastructure as code
   - tests/                  — tests

3. Prompt audit trail: All prompts must be stored verbatim, in order, in aidlc/prompts.md. For every prompt I send you, append it to that file before doing anything else.

4. Create the aidlc/ folder and prompts.md file if they do not already exist.

5. Folder naming: Use aidlc/ (no leading dot, not hidden). Never create .aidlc/ or aidlc-docs/ — keep folder names consistent across the whole session.

6. Confirm your understanding of these universal instructions now, and ask me for the project name before I send the first prompt.
````

After the agent confirms and you provide a project name, proceed with the prompts in order below.

---

# The Prompts

## Inception

### Prompt 1.1 — User Stories

````
Your Role: You are an expert product manager and are tasked with creating well defined user stories that become the contract for developing the system as mentioned in the Task section below. Plan for the work ahead and write your steps in an md file (aidlc/inception/stories_plan.md) with checkboxes for each step in the plan. If any step needs my clarification, add a note in the step to get my confirmation. Do not make critical decisions on your own. Upon completing the plan, ask for my review and approval. After my approval, you can go ahead to execute the same plan one step at a time. Once you finish each step, mark the checkboxes as done in the plan.

Your Task: Build user stories for the high-level requirement as described here:

Build a serverless URL shortener API on AWS. The API exposes three endpoints:
- POST /shorten with a long URL returns a short code
- GET /{code} returns a 301 redirect to the original URL
- GET /{code}/stats returns the click count for the code

Target 100 req/sec, p99 under 300ms, single region us-east-1. API only — no frontend.

For each user story, ensure acceptance criteria includes:
- Functional requirements
- API contract requirements (request/response shape, status codes)
- Non-functional requirements

Create an aidlc/inception/ directory for the stories_plan.md file and write the user stories to user_stories.md in the aidlc/inception/ directory.
````

### Prompt 1.2 — Units

````
Your Role: You are an expert software architect and are tasked with grouping the user stories into units that can be built independently as mentioned in the Task section below. Plan for the work ahead and write your steps in an md file (aidlc/inception/units_plan.md) with checkboxes for each step in the plan. If any step needs my clarification, add a note in the step to get my confirmation. Do not make critical decisions on your own. Upon completing the plan, ask for my review and approval. After my approval, you can go ahead to execute the same plan one step at a time. Once you finish each step, mark the checkboxes as done in the plan.

Your Task: Refer to the user stories in aidlc/inception/user_stories.md file. For this demo, group all user stories into a single cohesive unit named "url-shortener-api" covering the three endpoints (shorten, redirect, stats). Write the unit's user stories and acceptance criteria to aidlc/inception/units/url-shortener-api.md. Clearly identify the API contract for each endpoint. Do not start the technical systems design yet.
````

---

## Construction

### Prompt 2.1 — Domain Model

````
Your Role: You are an expert software architect and are tasked with designing the domain model using Domain Driven Design for the unit of the software system. Refer to the Task section for more details.

Plan for the work ahead and write your steps in an md file (aidlc/construction/domain_design_plan.md) with checkboxes for each step in the plan. If any step needs my clarification, add a note in the step to get my confirmation. Do not make critical decisions on your own. Upon completing the plan, ask for my review and approval. After my approval, you can go ahead to execute the same plan one step at a time. Once you finish each step, mark the checkboxes as done in the plan.

Your Task: Refer to aidlc/inception/units/url-shortener-api.md for the unit and corresponding user stories. Design the Domain Driven Design domain model with all tactical components including aggregates, entities, value objects, domain events, policies, repositories, and domain services. Create a new aidlc/construction/ folder, write the design details in aidlc/construction/url-shortener-api/domain_model.md.
````

### Prompt 2.2 — Implementation

````
Your Role: You are an expert software engineer and are tasked with creating a plan to implement a scalable API according to a Domain Driven Design domain model. Refer to the Task section for more details.

Plan for the work ahead and write your steps in an md file (aidlc/construction/tasks_plan.md) with checkboxes for each step in the plan. If any step needs my clarification, add a note in the step to get my confirmation. Do not make critical decisions on your own. Upon completing the plan, ask for my review and approval. After my approval, you can go ahead to execute the same plan one step at a time. Once you finish each step, mark the checkboxes as done in the plan.

Your Task: Refer to aidlc/inception/units/url-shortener-api.md for the unit, and aidlc/construction/url-shortener-api/domain_model.md for the domain model.

Generate a complete API implementation including:
- Python Lambda handlers — one per endpoint — in src/handlers/
- Domain layer: aggregates, entities, repositories, services — in src/domain/
- DynamoDB repository implementation in src/infrastructure/
- Unit tests in tests/
- Directory structure with clean separation between handlers, domain, and infrastructure
- A README.md with local run instructions using SAM local or a Python test harness

Keep it minimal — one file per concern, no unnecessary frameworks.
````

---

## Operations

### Prompt 3.1 — Deployment Planning and Execution

````
Your Role: You are an experienced Cloud Architect. Before you start the task as mentioned below, please do the planning and write your steps in a aidlc/operations/deployment_plan.md file with checkboxes against each step in the plan. If any step needs my clarification, please add it to the step to interact with me and get my confirmation. Do not make critical decisions on your own. Once you produce the plan, ask for my review and approval. After my approval, you can go ahead to execute the same plan one step at a time. Once you finish each step, mark the checkboxes as done in the plan.

Task: Refer to the unit in aidlc/inception/units/, the domain model and design in aidlc/construction/, and the source code in src/. Complete the following:
- Generate an end-to-end deployment of the API on AWS using Infrastructure as Code with CDK in TypeScript
- Stack components: API Gateway + Lambda (one function per endpoint) + DynamoDB
- Name the CDK stack exactly `UrlShortenerApiStack` (so cleanup commands target only this stack)
- Document all prerequisites for the deployment in aidlc/operations/prerequisites.md
- Produce a deployment runbook in aidlc/operations/deployment_runbook.md with concrete `cdk deploy UrlShortenerApiStack` commands

Once I approve the plan:
- Follow the best practice of clean, simple, explainable coding
- Validate the deployment by creating a validation plan (curl tests against the three endpoints), generate a validation report at aidlc/operations/validation_report.md
- Review the validation report and fix all identified issues, then update the report
````

### Prompt 3.2 — Monitoring and Observability Setup

````
Your Role: You are an experienced Site Reliability Engineer. Before you start the task below, please plan and write your steps in aidlc/operations/observability_plan.md with checkboxes against each step. If any step needs my clarification, add it to the step to get my confirmation. Do not make critical decisions on your own. Produce the plan, ask for my review and approval. After approval, execute the plan one step at a time and tick the checkboxes as you go.

Task: Refer to the NFRs in aidlc/inception/user_stories.md (100 req/sec, p99 < 300ms) and the deployed stack from aidlc/operations/deployment_plan.md. Add lightweight observability to the existing CDK stack:
- A single CloudWatch dashboard with three widgets: API request count, API p99 latency, API 5xx error rate
- One CloudWatch alarm: API 5xx rate > 1% over 5 minutes, routed to an SNS topic
- Enable structured JSON logging in the Lambda handlers (request id, endpoint, status, duration)

Document the setup in aidlc/operations/observability.md. Briefly validate by hitting the API and confirming metrics appear on the dashboard. Record results in aidlc/operations/observability_validation.md.
````

### Prompt 3.3 — Incident Response Procedures

````
Your Role: You are an experienced Incident Commander. Before you start the task below, please plan and write your steps in aidlc/operations/incident_response_plan.md with checkboxes against each step. If any step needs my clarification, add it to the step to get my confirmation. Do not make critical decisions on your own. Produce the plan, ask for my review and approval. After approval, execute the plan one step at a time and tick the checkboxes as you go.

Task: Refer to the architecture in aidlc/construction/ and the alarm from aidlc/operations/observability.md. Produce:
- Severity matrix (SEV1/SEV2/SEV3) with definitions and response times in aidlc/operations/severity_matrix.md
- Two runbooks in aidlc/operations/runbooks/: dynamodb_throttling.md and lambda_errors.md. Each runbook must have: detection signal, immediate mitigation, diagnosis steps, recovery steps, verification.
- A blameless postmortem template at aidlc/operations/postmortem_template.md
````

### Prompt 3.4 — Maintenance and Support Workflows

````
Your Role: You are an experienced Platform Engineer. Before you start the task below, please plan and write your steps in aidlc/operations/maintenance_plan.md with checkboxes against each step. If any step needs my clarification, add it to the step to get my confirmation. Do not make critical decisions on your own. Produce the plan, ask for my review and approval. After approval, execute the plan one step at a time and tick the checkboxes as you go.

Task: Using the deployment, observability, and incident response artifacts already produced, generate:
- Patching policy in aidlc/operations/patching.md covering Lambda runtime upgrades and Python dependency updates (cadence, Dependabot config, security patch SLA)
- Backup and restore procedures in aidlc/operations/backup_restore.md — enable DynamoDB PITR in the CDK stack and document a restore drill with concrete steps
- Cost guardrail: add a $50/month AWS Budget alarm to the CDK stack, documented in aidlc/operations/cost_management.md
````

### Prompt 3.5 — Production Readiness Checklist

````
Your Role: You are a senior Production Readiness Reviewer running a blocking go-live gate. The system cannot be marked production ready until every item passes. Before you start the task below, please plan and write your steps in aidlc/operations/prr_plan.md with checkboxes against each step. If any step needs my clarification, add it to the step to get my confirmation. Do not make critical decisions on your own. Produce the plan, ask for my review and approval. After approval, execute the plan one step at a time and tick the checkboxes as you go.

Task: Audit all artifacts under aidlc/ and src/ against a production readiness checklist. Produce aidlc/operations/production_readiness_checklist.md as a table with one row per item, marked PASS / FAIL / GAP, with evidence (file path or artifact reference) and a remediation note for anything that is not PASS:

- Architecture documented and reviewed
- Security: IAM least privilege, encryption at rest, no secrets in code
- Reliability: alarm tested, retries configured, DynamoDB capacity sized correctly
- Performance: smoke test confirms API responds under 300ms
- Observability: dashboard live, alarm routed to SNS, structured logs flowing
- Operability: two runbooks present, postmortem template ready
- Data: DynamoDB PITR enabled, restore drill documented
- Cost: $50/month budget alarm active

Do not mark the system ready until all items are PASS. If any item is FAIL or GAP, list the fix needed and stop.
````

### Prompt 3.6 — Debug (Optional, if something breaks)

````
Your Role: You are an expert software engineer tasked with debugging issues with the demo API.

Resolve the issue below and any other issues to ensure the API works end-to-end. Update aidlc/operations/validation_report.md with the fix.

Issue:
[paste error message or describe failing behavior]
````

---

## Methodology principles in these prompts

Every prompt follows the same five-step pattern lifted from AI-DLC:

1. **Role assignment** — `Your Role: You are an expert ...`
2. **Plan first, with checkboxes** — agent writes a `*_plan.md` listing concrete steps
3. **Flag clarifications** — agent embeds questions inline, doesn't make critical decisions alone
4. **Human approval gate** — agent stops and asks for review before executing
5. **Execute and tick** — works one step at a time, ticking boxes as it goes

This is what makes the output auditable: every artifact has a sibling plan file showing exactly what was decided and approved.

## Cleanup after the demo

The CDK stack is named `UrlShortenerApiStack`. Destroy **only** that stack — never use a bare `cdk destroy`, which would target every stack in the app. Run from inside your project folder:

```bash
cd <project-name>/infra && cdk destroy UrlShortenerApiStack
```

Confirm with `y` when prompted. To preview what will be removed without deleting:

```bash
cd <project-name>/infra && cdk diff UrlShortenerApiStack
```

DynamoDB tables with PITR enabled and S3 buckets with content may need manual cleanup via the AWS console after the stack is destroyed.

> If you renamed the stack in `infra/bin/*.ts`, substitute that name above. Run `cdk ls` from `<project-name>/infra/` to confirm the exact stack name before destroying.

## References

- [AI-DLC Methodology Blog](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)
- [AI-DLC Workflows Repo](https://github.com/awslabs/aidlc-workflows)
- [Method Definition Paper](https://prod.d13rzhkk8cj2z0.amplifyapp.com/)

## License

MIT-0 (matching the upstream AI-DLC repo).
