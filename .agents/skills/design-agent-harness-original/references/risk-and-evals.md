# Risk, evaluation, and rollout

Read this for production, unsupervised, high-impact, long-running, expensive, regulated, or multi-agent workflows.

## Set the autonomy budget

Rate each consequential action on:

- reversibility and recovery time;
- financial, privacy, security, legal, and reputational impact;
- scope of affected people, data, and systems;
- confidence that success and harm are observable;
- ambiguity of intent and policy;
- likelihood of duplicate, concurrent, or stale execution.

Use the rating to choose the maximum autonomy level per action, not for the workflow as a whole. A run may read autonomously, propose writes, and require approval for publication or deletion.

Define:

- least-privilege tools, data, paths, network, and secrets;
- approval points tied to effects;
- sandbox and environment boundaries;
- retry limits, timeouts, budgets, and circuit breakers;
- idempotency, compensation, rollback, and reconciliation;
- data retention, redaction, audit, and incident ownership;
- a human-visible stop and takeover path.

## Build an evaluation set

Derive cases from real examples and failures where possible. Include:

- ordinary success paths;
- boundary and malformed inputs;
- ambiguous requests that should trigger clarification or handoff;
- stale, missing, and contradictory context;
- tool timeouts, partial failures, and malformed responses;
- permission denial and missing secrets;
- duplicate triggers, retries, concurrency, and resume after interruption;
- model error, prompt injection, and untrusted tool/resource content;
- cases where the correct result is refusal, no action, or escalation.

Evaluate components separately where useful, then run end-to-end scenarios. Deterministic assertions belong in tests; model-judgment quality belongs in scenario evals with explicit rubrics.

## Measure what changes decisions

Choose measures tied to the workflow, such as:

- task completion and output quality;
- false-action and missed-action rates;
- unsafe or unauthorized action attempts;
- correct clarification, refusal, and escalation rates;
- recovery success after injected failures;
- human intervention and override rates;
- latency, tool calls, tokens, and monetary cost;
- duplicate effects and unreconciled state;
- drift by model, prompt, tool, or policy version.

Set acceptance thresholds and name the owner of failed gates. Avoid a single blended score when one severe failure should block rollout.

## Roll out by evidence

Use only the stages justified by risk:

1. fixture or simulation;
2. read-only observation;
3. shadow run against real inputs;
4. dry run with proposed effects;
5. approval-gated writes;
6. bounded autonomous writes with tight scope and budgets;
7. broader autonomy after measured success.

For each stage, specify entry criteria, exit criteria, telemetry, rollback, and stop conditions. Expansion is earned by evidence; regression returns the workflow to the last safe stage.

## Multi-agent controls

When several agents participate, add:

- typed handoff artifacts rather than reliance on conversational implication;
- isolated write scopes or a single writer;
- conflict and duplicate-work resolution;
- provenance for claims and changes;
- a final integrator accountable for the outcome;
- per-agent and total budgets, timeouts, and cancellation propagation.
