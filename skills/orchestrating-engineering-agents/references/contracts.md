# Contracts and Evidence Ledger

Use structured contracts so workers exchange artifacts and evidence instead of relying on long conversational summaries.

The exact serialization can be YAML, JSON, a typed object, or an in-memory structure. Preserve the fields and semantics that matter; do not create repository files solely to satisfy this reference.

## Task contract

```yaml
task:
  id: AUTH-014
  objective: Allow users to reset a forgotten password safely.
  non_goals:
    - Redesign account settings.
  acceptance_criteria:
    - Reset tokens expire after 30 minutes.
    - Reset tokens can be used only once.
    - The response does not reveal whether an email is registered.
  affected_surfaces:
    - authentication
    - email
    - database
  invariants:
    - Existing sessions remain valid unless product policy says otherwise.
  constraints:
    - Preserve the current mail provider abstraction.
  risk: high
  unknowns: []
  required_evidence:
    - unit tests
    - integration tests
    - security review
    - acceptance scenario
  rollback:
    required: true
    strategy: Revert migration and application change together.
```

Keep acceptance criteria observable. Avoid criteria such as "clean", "robust", or "secure" unless they are decomposed into verifiable properties.

## Work packet

Every dispatched worker should receive a bounded packet.

```yaml
work_packet:
  id: AUTH-014-impl
  role: builder
  objective: Implement single-use password reset tokens.
  depends_on:
    - AUTH-014-plan
  inputs:
    - task contract
    - relevant auth service
    - token persistence interface
    - existing auth tests
  write_scope:
    - src/auth/reset.*
    - tests/auth/reset.*
  forbidden_scope:
    - unrelated account settings
  invariants:
    - no account enumeration
    - old token invalid after successful reset
  required_checks:
    - targeted auth tests
    - typecheck
  output:
    - changed files
    - commands run
    - evidence
    - unresolved risks
  stop_when:
    - required dependency is unavailable
    - task contract is materially contradictory
    - implementation would require changing forbidden scope
```

A worker may ask for missing information only when it cannot continue safely from the packet and repository state.

## Worker result

```yaml
worker_result:
  packet_id: AUTH-014-impl
  status: completed | blocked | needs_human | failed
  artifacts:
    changed_files: []
    notes: []
  evidence:
    commands:
      - command: pytest tests/auth/reset.py
        exit_code: 0
        summary: 8 passed
  assumptions: []
  unresolved_risks: []
  suggested_next_state: VERIFYING
```

`suggested_next_state` is advisory. The orchestrator checks transition guards before moving global state.

## Review finding

Use findings rather than generic approval prose.

```yaml
finding:
  id: REV-003
  severity: blocking | high | medium | low | note
  category: requirements | correctness | architecture | security | quality | acceptance
  claim: Reset token remains valid after password change.
  evidence:
    file: src/auth/reset.py
    location: consume_token
    reproduction: Run the token twice after a successful password change.
  violated_criterion: Reset tokens can be used only once.
  recommended_action: Mark the token consumed transactionally with the password update.
  confidence: high
```

A review result should distinguish:

- **confirmed finding:** supported by code, execution, or a reproducible argument;
- **suspected risk:** plausible but not yet proven;
- **question:** requires product or owner intent;
- **non-issue:** investigated and dismissed with reason.

Do not inflate severity to make a review look useful.

## Verification record

```yaml
verification:
  criterion: Reset tokens can be used only once.
  evidence_type: integration_test
  command: pytest tests/auth/reset.py::test_token_single_use
  exit_code: 0
  observed: First use succeeds; second use is rejected.
  fresh: true
```

Every material acceptance criterion should map to one or more verification records or an explicit statement that it could not be verified.

## Orchestration ledger

Maintain an append-only logical ledger during multi-step work. It can remain in runtime state unless persistence is useful.

```yaml
ledger:
  task_id: AUTH-014
  current_state: REVIEWING
  route: high
  active_packets: []
  completed_packets:
    - AUTH-014-plan
    - AUTH-014-tests
    - AUTH-014-impl
    - AUTH-014-verify
  decisions:
    - id: DEC-001
      decision: Keep existing mail provider abstraction.
      reason: Task contract constraint.
      evidence: Existing interface is sufficient.
  findings:
    open: []
    resolved:
      - REV-003
  evidence:
    - criterion: token expiry
      record: VER-001
  repair_cycles:
    single_use_token: 1
  residual_risks: []
```

The ledger exists to prevent state from being lost across workers and retries. It is not a substitute for source control, test output, or repository history.

## Decision ownership

Classify decisions explicitly:

- **Agent-resolvable:** implementation detail constrained by existing code and requirements.
- **Evidence-resolvable:** answerable by tests, source inspection, docs, benchmarks, or tools.
- **Owner-resolvable:** product intent, destructive policy, legal choice, irreversible action, unsupported tradeoff.

The orchestrator should resolve the first two without unnecessary user interruption. Route the third to `NEEDS_HUMAN` only when it blocks safe progress.

## Evidence hierarchy

Prefer stronger evidence when available:

1. Reproduced behavior in the relevant environment.
2. Automated acceptance/integration test.
3. Targeted regression/unit test.
4. Build, type, lint, static, or security tool output.
5. Direct source/diff inspection.
6. Independent reasoned review.
7. Implementer self-report.

Lower levels can complement higher ones but should not replace an available stronger check.

## Final acceptance matrix

Before `ACCEPTED`, create a compact mapping:

```text
Criterion                         Evidence                 Status
Token expires in 30 minutes       integration test VER-1  PASS
Token is single-use               regression test VER-2   PASS
No account enumeration            API tests + review      PASS
Rollback path works               migration rehearsal     PASS
```

If a material row is `UNKNOWN`, the overall decision should not silently become `ACCEPTED`.
