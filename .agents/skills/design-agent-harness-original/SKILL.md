---
name: design-agent-harness-original
description: Design or audit an agent-driven workflow and recommend the minimum effective harness architecture.
---

# Design Agent Harness

Turn a pipeline, workflow, or instruction-heavy repository into an evidence-driven agent harness design. Consider the full harness, then recommend only the parts whose complexity is justified.

Use the current conversation as the brief. Inspect relevant repository artifacts when available: workflow code, instructions, skills, hooks, tool configuration, tests, CI, domain docs, ADRs, logs, and recent failures. Ask at most three focused questions only when their answers would materially change the architecture. A design or audit request authorizes read-only inspection, not implementation.

## 1. Frame the workflow

Write down:

- the outcome and the human or system that values it;
- triggers, inputs, outputs, external systems, and durable state;
- which steps are deterministic and which require judgment;
- frequency, latency and cost limits, reversibility, and blast radius;
- current pain or observed failures;
- the evidence that would prove success.

Model it as a **control loop**:

`trigger -> assemble context -> decide -> act -> observe -> evaluate -> recover/escalate -> record`

Account for every transition that can fail. Mark unknowns as assumptions instead of silently filling them in.

## 2. Find the control points

For each observed or credible failure mode, identify:

- prevention: what can stop it before action;
- detection: what observable signal reveals it;
- recovery: retry, compensate, roll back, or hand off;
- authority: who or what may approve the effect;
- learning: what should persist for the next run.

Separate needs for guidance, judgment, action, feedback, state, and enforcement. This separation is the basis for choosing primitives.

## 3. Allocate the minimum harness

Read [references/primitives.md](references/primitives.md) before selecting components. If replacing an existing collection of notes, prompts, or agent configuration, also read [references/migration.md](references/migration.md). For production, unsupervised, high-impact, long-running, or multi-agent workflows, read [references/risk-and-evals.md](references/risk-and-evals.md).

Apply these rules:

- Tie every component to a requirement or failure mode. Prefer the **minimum harness** that closes the loop.
- Put stable facts in an authoritative source and expose concise context pointers; avoid copying the same rule into several instruction surfaces.
- Use prose to guide judgment and executable controls to enforce machine-checkable invariants.
- Keep hooks event-bound, fast, deterministic, idempotent, and thin. Put substantial logic in a testable script or service.
- Give every autonomous loop a stop condition, retry budget, and escalation path.
- Give every consequential action an explicit permission boundary and recovery story.
- Treat packaging as a distribution decision, not a behavior primitive: recommend a plugin only when a cohesive bundle must be installed or versioned together.
- Speak in vendor-neutral primitives first, then map them to features actually supported by the target harness. Verify current vendor-specific behavior from installed or official documentation when the design depends on it.

## 4. Stress the design

Walk through at least:

- a normal run;
- ambiguous or incomplete input;
- stale or conflicting context;
- a tool or external-service failure;
- a denied permission or unavailable secret;
- interruption, retry, or duplicate delivery;
- an incorrect model decision;
- the point where a human must regain control.

Revise any component that cannot detect or recover from the failure it is meant to address. For meaningful autonomy, define evaluation and rollout gates before recommending expansion.

## 5. Deliver the decision

Lead with a short verdict. Then provide:

1. **Workflow model** — scope, assumptions, and the control loop.
2. **Recommended architecture** — a table with concern/failure, chosen primitive, trigger, responsibility, evidence, and why a simpler mechanism is insufficient.
3. **Coverage ledger** — mark each family below `use now`, `later if`, or `not justified`, with one-line reasoning:
   - knowledge and context;
   - cognition and orchestration;
   - tools, MCP, and semantic interfaces;
   - hooks, permissions, sandbox, and triggers;
   - tests, sensors, monitors, telemetry, and evals;
   - memory and durable state;
   - packaging, distribution, and harness APIs;
   - human supervision and handoff.
4. **Artifact map** — proposed files or services, each with one responsibility and one source of truth. Include a keep/move/rewrite/retire map when auditing an existing harness.
5. **Safety and evaluation plan** — permissions, stop conditions, recovery, representative scenarios, and measurable acceptance gates.
6. **Implementation slices** — the smallest useful vertical slice first, followed by conditional extensions. Separate recommendation from speculation.
7. **Rejected complexity** — plausible primitives considered but not justified yet.

Use a diagram only when the workflow or responsibility boundaries are hard to understand in prose. Make confidence and unresolved choices explicit. If the user asked for implementation, obtain agreement only for choices that materially alter scope or risk, then implement the smallest accepted slice and verify its success criteria.
