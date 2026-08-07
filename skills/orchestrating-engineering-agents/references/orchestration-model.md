# Orchestration Model

Use this reference when choosing roles, risk routes, state transitions, or parallel execution.

## Design principle

A role exists only when separating that responsibility increases independence, focus, or evidence quality. Do not model an org chart for its own sake.

## Role pool

| Role | Primary responsibility | Activate when | Must not own alone |
| --- | --- | --- | --- |
| Orchestrator | State, routing, dependencies, budgets, evidence ledger | Multi-step or multi-agent work | Final technical truth without evidence |
| Scout | Locate relevant code, docs, tests, constraints | Repository is unfamiliar or change surface is unclear | Architecture approval |
| Planner | Decompose work and define acceptance strategy | Task has multiple dependencies or uncertainty | Sole implementation approval |
| Architect | Evaluate boundaries, interfaces, migration and long-term structure | Cross-cutting or structurally significant change | Final acceptance after authoring the design and implementation |
| Test Designer | Specify regression, acceptance, edge and failure cases | Behavior changes or risk is medium+ | Declare implementation correct without running tests |
| Builder | Implement a bounded change | Code/configuration must change | Sole review of its own change |
| Verifier | Run deterministic checks and map evidence to criteria | Any material implementation | Rewrite criteria merely to obtain green results |
| Adversarial Reviewer | Seek bypasses, unsafe assumptions and hostile edge cases | Security, permissions, untrusted input, data integrity, concurrency, high risk | Make product-policy decisions for the user |
| Quality Reviewer | Review maintainability, complexity and repository consistency | Medium+ change or significant refactor | Replace executable correctness checks |
| Acceptance Tester | Exercise the user-visible workflow | User-facing or integration behavior | Infer hidden correctness from UI success alone |

One worker may hold compatible roles when risk is low. Separate conflicting roles such as author and final approver.

## Risk routing

Assess risk across these dimensions:

- **Security:** authentication, authorization, secrets, permissions, untrusted input, remote execution, trust boundaries.
- **Data:** destructive writes, migrations, irreversible transformations, user data, consistency guarantees.
- **Compatibility:** public APIs, file formats, protocols, persisted state, external integrations.
- **Concurrency:** races, locking, ordering, distributed state, retries, idempotency.
- **Architecture:** new subsystem, cross-cutting dependency, boundary change, major refactor.
- **Deployment:** production configuration, infrastructure, rollback difficulty, live traffic.
- **Uncertainty:** unclear requirements, undocumented behavior, unfamiliar technology, incomplete reproduction.

### Low

Typical characteristics:

- local and reversible;
- narrow change surface;
- existing tests clearly cover behavior;
- no trust, persistence, migration, compatibility, or production implications.

Default route: Builder -> deterministic verification.

### Medium

Typical characteristics:

- multiple files or components;
- behavior change with reasonable testability;
- moderate uncertainty;
- user-visible workflow or integration boundary.

Default route: Planner if decomposition helps -> Builder -> Verifier/Reviewer.

### High

Any of these is usually enough:

- authentication or authorization;
- secrets or sensitive data;
- database/schema migration;
- public API or persisted-format change;
- meaningful concurrency or distributed-state behavior;
- large architectural boundary change;
- production deployment behavior;
- difficult rollback;
- a defect could cause data loss or security impact.

Default route: Planner/Architect -> Test Designer when useful -> Builder -> Verifier -> Adversarial Reviewer -> final architecture/quality review.

### Critical

Use when an error can create severe or irreversible impact, especially in production systems, privileged security boundaries, destructive data operations, or changes requiring an owner decision.

Critical routing adds a human gate before the irreversible action or decision. The rest of the work should continue without unnecessary pauses.

## Default finite-state machine

```text
PROPOSED
  -> TRIAGED
  -> PLANNED
  -> READY
  -> IMPLEMENTING
  -> VERIFYING
  -> REVIEWING
  -> ACCEPTED
```

Terminal exception states:

```text
BLOCKED
NEEDS_HUMAN
REJECTED
```

Recovery transitions:

```text
VERIFYING -> IMPLEMENTING
REVIEWING -> IMPLEMENTING
REVIEWING -> PLANNED
IMPLEMENTING -> PLANNED
```

## Transition guards

| Transition | Minimum guard evidence |
| --- | --- |
| PROPOSED -> TRIAGED | Objective understood; risk and uncertainty classified |
| TRIAGED -> PLANNED | Acceptance criteria, non-goals, affected surfaces, and constraints recorded |
| PLANNED -> READY | Dependencies resolved; work packets bounded; write conflicts addressed; required tools available |
| READY -> IMPLEMENTING | Owner and scope assigned for each executable node |
| IMPLEMENTING -> VERIFYING | Expected artifacts/diff exist and implementation worker has stopped modifying them |
| VERIFYING -> REVIEWING | Required deterministic checks ran; failures are resolved or explicitly carried as findings |
| REVIEWING -> ACCEPTED | Acceptance criteria mapped to evidence; no blocking findings; residual risk recorded |
| ANY -> NEEDS_HUMAN | A material decision cannot be inferred safely or an irreversible action needs explicit authority |
| ANY -> BLOCKED | Required dependency, environment, permission, or information is unavailable |
| ANY -> REJECTED | The requested solution cannot meet its contract or violates a hard constraint |

States are control-plane facts. A worker cannot move the global state merely by returning a success message.

## Execution graph rules

Model work as a DAG whenever there are independent nodes.

Safe parallelism usually includes:

- repository scouting in different subsystems;
- test design and implementation planning;
- threat analysis and architecture analysis;
- read-only reviews of independent surfaces;
- implementation slices isolated by file/module and stable interface contracts.

Serialize or isolate:

- edits to the same file;
- schema plus code that depends on the schema before the contract is fixed;
- migrations and consumers with uncertain ordering;
- shared mutable test fixtures;
- branch-wide formatting while other writers are active;
- dependency upgrades that rewrite the same lockfile.

When concurrent writes are valuable, use runtime-supported branches/worktrees or another isolated workspace. Assign one integration owner. Merge only after each branch has its own evidence and the integrated result is reverified.

## Context isolation

Give workers only what they need:

- task contract;
- relevant source and tests;
- interfaces and invariants;
- exact write scope;
- required commands;
- expected result schema.

Independent reviewers should normally receive the requirements and produced artifact/diff, not the implementer's conclusion. This reduces anchoring and correlated reasoning.

## Correlated agents

Multiple workers are not automatically independent. Correlation rises when they share:

- the same model;
- the same system prompt or role prompt;
- the same long context;
- the same proposed solution before analysis;
- the same tests and assumptions.

Increase useful diversity through different review objectives, fresh contexts, alternative models when available, deterministic tooling, mutation/fault injection, or independently generated tests. Do not convert correlated agreement into a confidence percentage.

## Retry policy

Classify a failure before retrying:

- **Implementation defect:** return to Builder.
- **Plan defect:** return to Planner/Architect.
- **Requirement defect:** `NEEDS_HUMAN` if materially ambiguous.
- **Test defect:** repair the test only with evidence that the test is wrong.
- **Environment defect:** `BLOCKED` if unavailable; otherwise repair environment and rerun.

Default to two repair cycles for the same root cause. A third recurrence should trigger deeper diagnosis, route change, or escalation rather than another blind retry.
