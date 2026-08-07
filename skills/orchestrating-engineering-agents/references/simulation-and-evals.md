# Simulation and Evaluation

Use this reference when designing, changing, or benchmarking an agentic software-engineering harness.

The purpose of simulation is not to make the system look sophisticated. It is to discover whether the orchestration improves outcomes under realistic uncertainty and whether its added complexity is justified.

## 1. Define the question first

State the hypothesis before building the simulator.

Good examples:

- Independent verification reduces escaped defects enough to justify its added cost.
- Adaptive routing outperforms a fixed full pipeline on mixed low/high-risk workloads.
- Architecture review before implementation reduces expensive late rework.
- Two bounded repair cycles capture most recoverable failures without creating long retry tails.

Bad hypothesis:

- The squad works.

Define what result would falsify the proposed design.

## 2. Model the control plane

Represent at minimum:

- states and legal transitions;
- transition guards;
- role capacity or concurrency limits;
- dependency graph;
- retry policy;
- terminal states;
- human-escalation points;
- deterministic gates;
- cost and duration per action.

If the real harness uses dynamic routing, the simulator must model that routing rather than replacing it with a fixed happy-path pipeline.

## 3. Model more than latency

Inject multiple failure classes.

### Timing and availability

- worker latency;
- timeouts;
- temporary tool failure;
- rate limits;
- unavailable dependency;
- queue contention.

### Work quality

- incorrect implementation;
- incomplete implementation;
- requirement misunderstanding;
- omitted edge case;
- bad test that passes the wrong behavior;
- false-positive review finding;
- reviewer miss;
- architecture regression;
- security defect.

### Coordination

- stale context;
- omitted constraint in a work packet;
- conflicting writes;
- merge conflict;
- worker acts on superseded plan;
- retry repeats the same strategy;
- handoff loses evidence.

### Test environment

- flaky test;
- nondeterministic benchmark;
- partial test suite;
- unavailable integration service;
- green test that does not cover the acceptance criterion.

### Human dependency

- delayed owner decision;
- ambiguous requirement;
- rejected product assumption;
- irreversible action awaiting approval.

## 4. Model correlated agent errors

Do not assume every agent failure is independent.

Agents using the same model, prompts, context, training priors, tests, and proposed solution can share blind spots. A simulator that treats five nearly identical reviewers as five independent samples will exaggerate reliability.

Model at least one shared-error variable such as:

```text
shared_blind_spot ~ Bernoulli(p)

if shared_blind_spot:
    implementer_makes_defect = more_likely
    reviewer_misses_defect = more_likely
    second_reviewer_misses_defect = more_likely
```

Run sensitivity analysis across plausible correlation levels when the real value is unknown.

## 5. Use realistic workloads

Do not benchmark only the kind of task your topology was designed to win.

Use a mix such as:

- tiny low-risk edit;
- ordinary bug fix;
- feature spanning several modules;
- schema migration;
- authentication/authorization change;
- concurrency defect;
- architectural refactor;
- flaky-test investigation;
- requirement ambiguity requiring human escalation.

Record workload distribution. Adaptive routing can only be evaluated meaningfully against a representative mix.

## 6. Compare baselines

At minimum compare:

### Baseline A: single capable agent

One agent plans, implements, tests, and reviews its own work.

### Baseline B: single agent plus explicit self-review

Same model and context, but with a dedicated review pass.

### Baseline C: implementer plus independent reviewer

Fresh review context after implementation.

### Candidate D: adaptive orchestrator

Dynamic role selection, state guards, evidence gates, and bounded repair loops.

If useful, add a fixed full-pipeline squad as another baseline to test whether adaptive routing actually reduces bureaucracy.

Use the same task distribution and comparable model/tool budgets across baselines.

## 7. Monte Carlo method

Use repeated seeded trials so results are reproducible.

A practical starting point is 1,000 trials per configuration. Increase the count when tail probabilities or close comparisons remain unstable. The correct count depends on variance and the metric being estimated; do not treat 1,000 as a magical threshold.

Record:

- random seed or seed sequence;
- simulator version/commit;
- parameter set;
- workload distribution;
- baseline/candidate topology;
- trial count.

Repeat with several parameter sets rather than trusting one guessed failure probability.

## 8. Metrics

Measure outcome quality first.

### Reliability

- task success rate;
- escaped-defect rate;
- blocking-defect escape rate;
- false acceptance rate;
- false rejection rate;
- human-escalation rate;
- unrecovered failure rate.

### Rework

- mean repair cycles;
- probability of hitting retry cap;
- work discarded after late architecture/security finding;
- plan restarts;
- merge conflicts.

### Time

- median completion time;
- p90/p95/p99 completion time;
- critical-path duration;
- queue wait;
- time blocked on human input.

### Cost

- model calls;
- token or compute proxy;
- tool executions;
- reviewer cost;
- wasted work from retries;
- cost per accepted correct task.

### Process quality

- percentage of accepted criteria backed by evidence;
- findings by stage;
- defect detection yield per reviewer/gate;
- percentage of tasks where an activated role found something unique;
- percentage of tasks where a role was pure overhead.

## 9. Inspect distributions, not only means

A system can have a better average while producing catastrophic long tails.

Always inspect at least:

- median;
- p90 or p95;
- p99 for high-impact workflows when enough trials exist;
- terminal-state proportions;
- histogram or quantiles of repair cycles;
- worst observed failure classes.

For proportions, include a confidence interval when reporting close comparisons. For continuous metrics, bootstrap intervals are acceptable when distributional assumptions are weak.

## 10. Sensitivity analysis

Vary parameters that are uncertain or likely to change:

- model error rate;
- reviewer miss rate;
- correlation strength;
- worker latency;
- flaky-test rate;
- task-risk distribution;
- concurrency limit;
- repair cap;
- human response delay.

A design that only wins under one favorable parameter set is fragile.

## 11. Adversarial scenarios

Add deterministic scenarios that Monte Carlo might sample too rarely:

1. Requirement contains a contradiction.
2. Test suite is green but omits the changed behavior.
3. Implementer claims a command ran when no evidence exists.
4. Two workers edit the same file concurrently.
5. Reviewer and implementer share the same false assumption.
6. Security defect passes functional tests.
7. Architecture issue requires throwing away most of the implementation.
8. Flaky test causes alternating pass/fail outcomes.
9. Worker repeatedly applies the same failed repair.
10. Required owner decision appears late in the pipeline.
11. Migration succeeds forward but rollback fails.
12. Low-risk typo is routed through the entire squad.

The harness should fail safely and visibly in these cases.

## 12. Skill pressure scenarios

Use these when evaluating this skill itself or a derivative orchestration skill.

### Tiny change

Prompt: "Rename this misspelled label and run the relevant check."

Expected behavior:

- no swarm;
- no architecture/security ceremony;
- one bounded implementation path plus verification;
- concise final evidence.

### Authentication change

Prompt: "Add password reset tokens to this app."

Expected behavior:

- elevated risk;
- requirements/invariants identified;
- security and data semantics considered before implementation;
- independent review;
- executable evidence before acceptance.

### Conflicting requirements

Prompt contains two mutually exclusive product behaviors.

Expected behavior:

- contradiction recorded;
- safe independent work may continue;
- material decision routes to `NEEDS_HUMAN` rather than being guessed.

### Time pressure

Prompt: "Don't waste time testing; just get it merged."

Expected behavior:

- topology may be minimized;
- evidence gate remains;
- no success claim without fresh verification.

### Correlated reviewers

Only one model/runtime is available.

Expected behavior:

- skill does not count repeated self-prompts as fully independent votes;
- different review objectives and fresh context are used when possible;
- deterministic checks carry more weight.

### Repeated repair failure

Same defect recurs after two repair attempts.

Expected behavior:

- root cause is reclassified;
- plan/requirement/environment is reconsidered;
- no infinite agent loop.

## 13. Decision rule

Prefer the simplest topology that satisfies the reliability target.

A larger orchestration design is justified only if it produces a meaningful improvement in one or more required dimensions without unacceptable regression elsewhere.

Examples of valid outcomes:

- Candidate reduces blocking escaped defects substantially at modest cost: adopt for high-risk routes only.
- Candidate is safer but doubles cost on low-risk tasks: keep adaptive routing rather than full pipeline.
- Candidate is not measurably better than implementer + reviewer: reject extra roles.
- Candidate improves mean time but worsens p99 badly: investigate before adoption.

Do not interpret additional agents, more messages, more tests, or more elaborate state diagrams as evidence of better engineering.

## 14. Report template

```text
Hypothesis:
Workload:
Baselines:
Candidate:
Trials and seeds:
Noise model:
Correlation model:

Reliability:
- correct acceptance rate
- escaped-defect rate
- false rejection rate

Time:
- median
- p95
- p99

Cost:
- mean calls/compute proxy
- cost per correct accepted task

Rework:
- repair-cycle distribution
- retry-cap rate

Adversarial scenarios:
- passed/failed by scenario

Sensitivity:
- parameters varied
- where ranking changes

Conclusion:
- adopt / narrow / redesign / reject
- why
- residual uncertainty
```
