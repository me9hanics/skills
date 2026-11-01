# Agent harness design reference

Use this reference to consider the whole agent system without turning the design into a shopping list. A primitive earns a recommendation only when it owns a clear responsibility, addresses a named requirement or failure mode, and has evidence that can verify it.

Product terminology and file formats change. Treat vendor names below as discovery cues and examples, then verify the target harness's current documentation, installed configuration, precedence rules, and available lifecycle events before proposing exact files.

## The high-level model

**Agent = model + harness.** The model supplies learned capabilities; the harness supplies everything that selects, instructs, equips, constrains, runs, observes, and coordinates it. Architecture work is usually harness work.

Three overlapping ownership views are useful:

| View | What it contains | Architecture implication | Notes |
|---|---|---|---|
| **Inner harness** | Provider/runtime capabilities such as reasoning behavior, native tool calling, context-window handling, compaction, and built-in delegation | Usually select and configure these capabilities; replace the runtime only when a hard requirement cannot be expressed outside it | The exact boundary is product-specific. The underlying model and its maintained runtime may be exposed as one product surface. |
| **Middle harness** | Instructions, prompts, skills, custom-agent profiles, tools/MCP, and lifecycle hooks close to the agent loop | Start here for most interactive agent engineering | “Middle harness” is useful but not standardized; it emphasizes the programmable layer between model and application workflow. |
| **Outer harness** | The launcher, workflow or graph, durable state, triggers, execution environment, permissions, feedback, evaluation, observability, and multi-run coordination | Use it when work must be repeatable, event-driven, resumable, governed, or integrated into a larger system | Some authors use “outer harness” for everything developer-controlled, including the middle layer. Resolve the boundary by responsibility rather than terminology. |

Another useful view is by system layer:

| Layer | Main question | Typical primitives | Notes |
|---|---|---|---|
| **Knowledge and behavior** | What should the agent know and how should it approach the task? | system/developer instructions, `AGENTS.md`, rules, references, prompts, skills, templates, memory | Minimize always-loaded material; preserve canonical sources and precise pointers. |
| **Cognition and orchestration** | Who decides, how is work decomposed, and how do decisions compose? | model/runtime configuration, agents, subagents, teams, planners, routers, workflow graphs | A named role and a separate runtime context are different design choices. |
| **Perception and action** | What can the agent observe and change? | tools, APIs, MCP, LSP, retrieval, browser/UI control, scripts, sensors | Separate read capabilities from write capabilities where possible. |
| **Control and execution** | When may work run, where does it run, and what constrains effects? | hooks, policies, approvals, sandboxes, containers, worktrees, triggers, schedules, queues, secrets | Sandbox boundaries and approval policies are separate controls. |
| **Ecosystem and operations** | How is the system shipped, measured, governed, and evolved? | plugins, marketplaces, SDKs, telemetry, evals, audit, rollout controls | Packaging, control, and distribution are different axes even when one product bundles them. |

### Control-loop roles

Map every proposed component into the workflow's control loop. This prevents instructions, hooks, tests, and tools from being treated as interchangeable.

| Role | Question | Examples | Notes |
|---|---|---|---|
| **Trigger** | What starts or resumes work? | user prompt, schedule, webhook, issue, CI event, monitor event | Define duplicate-delivery and concurrency behavior for non-human triggers. |
| **Guide / feedforward control** | What steers behavior before action? | constraints, templates, context pointers, plans, checklists, pre-action rules | Guidance reduces error likelihood but does not prove compliance. |
| **Decision** | Where is ambiguity or judgment resolved? | main agent, specialist agent, policy engine, router, human | State the decision owner and confidence or escalation threshold. |
| **Actuator** | What produces an effect? | file write, command, API call, deploy, browser click, message send | Give consequential actuators narrow permissions and explicit side-effect contracts. |
| **Sensor / feedback control** | What reports the result after or during action? | tests, linter, type checker, DOM, screenshot, logs, metrics, diff review | Feedback closes the loop only when its result reaches a decision point. |
| **Constraint / governor** | What prevents unacceptable effects? | permissions, allowlists, sandbox, approval, policy, circuit breaker | Put machine-checkable invariants here rather than relying only on prose. |
| **State / memory** | What persists between steps or runs? | checkpoint, database, task ledger, artifact, curated memory | Define ownership, retention, invalidation, and reconciliation. |
| **Recovery / handoff** | How does the loop stop, repair, or return control? | retry budget, compensation, rollback, checkpoint, human escalation | Every autonomous loop needs a terminal condition and a human-visible takeover path. |

### Selection posture

- Choose by activation semantics: **location** suggests scoped instructions; **user intent** suggests a prompt or skill; **lifecycle event** suggests a hook; **time or external event** suggests an automation or trigger.
- Keep judgment in model-facing instructions and deterministic mechanics in tested code. Use executable controls for enforceable invariants.
- Keep each fact or rule in one authoritative source. Treat copied summaries as caches with an invalidation cost.
- Use the **minimum harness** that closes `trigger -> context -> decision -> action -> observation -> evaluation -> recovery/record`.
- Evaluate packaging, orchestration, and control independently. A plugin is not inherently “deeper” than a hook; it may simply package one.
- Map vendor-neutral recommendations to the actual target. Unsupported product features are gaps or implementation work, not assumed capabilities.

## Knowledge and behavior

| Primitive | What it is | Choose it when | Prefer something else when | Notes |
|---|---|---|---|---|
| System/developer instructions | High-precedence behavior and policy supplied by the harness or application | A builder controls the runtime and needs universal behavior across its runs | The rule belongs only to one repository, path, or task | Keep this surface compact and stable; use it for core boundaries and pointers rather than a complete operating manual. |
| Persistent repository instructions | Context injected because of repository location | A compact invariant or context pointer applies to nearly every task in that scope | The content is a workflow, long reference, or only sometimes relevant | Examples include `AGENTS.md`, `CLAUDE.md`, and `.github/copilot-instructions.md`. In Codex, instruction discovery commonly walks from broader scope toward the current directory so nearer guidance can override broader guidance; verify current precedence. |
| Override or local instructions | A higher-priority or machine/user-local instruction layer | A deliberate exception must supersede general instructions without rewriting the shared source | The variation should be committed and shared, or can be expressed as path scope | Examples include `AGENTS.override.md` and `CLAUDE.local.md`. Keep secrets out of instruction files; make the reason and intended scope of an override obvious. |
| Path-scoped instructions/rules | Rules activated by directory, path pattern, or file type | A rule follows a bounded context, subtree, generated area, or file class | Activation depends on user intent, time, or runtime events | Examples include nested `AGENTS.md`, child `CLAUDE.md`, `.claude/rules/*.md`, and Copilot `*.instructions.md` with path matching. Check how overlapping scopes compose and which file wins. |
| Review-specific instructions | Guidance loaded for a review surface rather than ordinary implementation | Review criteria genuinely differ from coding criteria | The same invariant applies to all tasks and belongs in shared instructions | Some hosts support files such as `REVIEW.md` or review-agent profiles. Keep findings criteria separate from implementation style when that distinction changes behavior. |
| Domain/reference documentation | Canonical facts, vocabulary, schemas, policies, or rationale consulted on demand | Knowledge is durable, substantial, and should be shared by several workflows | The content directs a reusable task; use a skill, prompt, or workflow | Examples include `CONTEXT.md`, `GLOSSARY.md`, architecture docs, runbooks, schemas, and ADRs. These names are often project conventions, not harness standards. Point to them instead of duplicating them into instructions. |
| Specification and work ledger | A durable statement of desired behavior, decisions, tasks, and status | Multi-step work needs a shared contract, resumability, or handoff | The task is small enough to finish reliably within one run | Examples include issue trackers, `spec.md`, `backlog.md`, task graphs, and spec-kit-style artifacts. Select the lightest system that provides the needed state and accountability. |
| Reusable prompt/command | An explicitly invoked prompt template or short recipe | A human starts a lightweight, repeatable interaction | The capability needs bundled reference, scripts, autonomous discovery, or substantial branching | Examples include `/review`, `/deploy`, and `*.prompt.md`. A command is an invocation surface; it may call a skill or workflow behind the scenes. |
| Skill | A discoverable or explicitly invoked capability bundle with instructions and optional references, scripts, and assets | Reusable judgment or a multi-step capability should load on demand | Behavior must execute on every event, or a machine-checkable invariant must be enforced | Common shape: `SKILL.md` plus optional `references/`, `scripts/`, `assets/`, and host metadata. A skill can be team/project-specific even though portability is a major benefit. Its description is a context pointer used for discovery when implicit invocation is enabled. |
| Template or output style | A reusable artifact shape, scaffold, schema, or presentation convention | Consistent output structure materially improves downstream use | The requirement is an invariant that needs executable validation | Templates are feedforward controls. Validate generated artifacts separately rather than assuming conformity from the prompt. |

Keep always-loaded instructions small. A context pointer should say what its target contains and under which branch to read it. Do not infer that an instruction filename is portable across hosts.

## Cognition and orchestration

| Primitive | What it is | Choose it when | Prefer something else when | Notes |
|---|---|---|---|---|
| Model/runtime configuration | Choice of model, reasoning effort, context limits, native tools, and built-in planning/delegation behavior | Quality, latency, modality, context size, or cost materially changes the workflow | The failure is missing domain context, a bad tool contract, or absent feedback | This sits near the inner-harness boundary. Measure the configured system rather than attributing every failure to the model. |
| Custom agent/profile | A named configuration combining role, instructions, model, tools, permissions, or output expectations | A stable role genuinely needs a distinct capability or authority envelope | Only a reusable procedure changes; use a skill | Examples include `*.agent.md` and product-defined agent configuration. A profile is configuration; it becomes a runtime agent only when instantiated. |
| Subagent | A child runtime loop with its own context window | Context isolation, parallel independent work, or specialist review materially improves the task | Coordination cost exceeds the saved context, elapsed time, or quality gain | A subagent can read many files and return a compact result, making it a context-engineering tool. Define input/output contracts, timeout, permissions, and the parent that owns integration. |
| Agent team | Multiple runtime agents that coordinate, potentially peer-to-peer, around shared work | Work has real parallelism, separable ownership, shared task state, and conflict resolution | Parent-to-child delegation or a single loop is sufficient | Distinguish a team from a decorative collection of personas. Specify communication topology, shared state, write ownership, budgets, and cancellation. |
| Planner/router | Logic that chooses the next branch, tool, agent, or workflow based on state | Several paths require deliberate selection under uncertainty | A fixed decision table or script can route reliably | Planning may be an inner-harness capability, a prompt pattern, a dedicated agent, or outer orchestration. Avoid a planner that only restates the task. |
| Workflow/graph engine | Explicit nodes, transitions, checkpoints, and routing outside the conversational loop | Durable execution, resumability, auditability, or deterministic transitions dominate | The path is short and conversational judgment is central | Keep state transitions and effect ownership explicit. Let agent nodes handle judgment without hiding the workflow state inside prose. |
| Multi-run orchestration | Logic coordinating repeated agent runs, retries, review stages, or competing candidates | The outer harness must decide when to run, how to judge success, and how to combine results | One bounded agent run closes the loop | Use typed handoffs, idempotency, budgets, and a final integrator. This may be implemented with an agent SDK, job system, or workflow engine. |

Subagents and teams are not organizational theater. A separate context or agent earns its cost through isolation, concurrency, specialization, independent review, or a distinct permission boundary.

## Perception and action

| Primitive | What it is | Choose it when | Prefer something else when | Notes |
|---|---|---|---|---|
| Script or CLI | Deterministic executable logic exposed through a stable command contract | An operation is repeatable, locally testable, and benefits from exact behavior | The step requires open-ended judgment | Make arguments, exit codes, output format, dry-run behavior, and side effects explicit. Skills and hooks can call the same tested script. |
| Native tool/API | A concrete read or write capability available directly to the agent or harness | The capability already exists with adequate semantics and permissions | A shared external integration needs a portable protocol boundary | Tools are actuators and sometimes sensors: shell, file edit, search, browser, code execution, database client, deploy, or messaging. Tool schemas should be narrow and structured. |
| MCP server | A standardized protocol boundary exposing external tools and resources | Several agent clients need a reusable, permissioned integration to databases, SaaS, repositories, browsers, or internal APIs | A one-off local tool is simpler, or the target has a better native integration | Think “protocol for agent-facing capabilities,” not the capability itself. Configuration may appear as `mcp.json`, `.mcp.json`, or host settings. Review trust, authentication, resource content, and write permissions. |
| LSP / semantic code intelligence | A code-specific semantic interface for symbols, definitions, references, types, diagnostics, implementations, and call hierarchy | Refactoring or navigation needs compiler-aware structure | Text search plus compiler/test output answers the question cheaply | Configuration may appear as `lsp.json` or `.lsp.json`. Some harnesses feed diagnostics after edits; verify whether this is automatic or must be called. |
| Browser/UI automation | Perception and action through a rendered interface | The UI is the real interface or no adequate API exists | A supported API or connector is more stable and auditable | Treat DOM, screenshot, and accessibility state as sensors; clicks and typing as actuators. Add visual verification for layout-dependent outcomes. |
| Retrieval/search | Runtime selection of relevant knowledge from a larger or changing corpus | Injecting everything would be stale or exceed context, and relevance can be searched | A small canonical reference file is clearer and cheaper | Define corpus authority, freshness, ranking/fallback, citations, and behavior when retrieval is empty or contradictory. |
| Tool discovery / deferred schemas | Loading tool definitions only when needed | The tool catalog is large enough that always-present schemas waste context or confuse selection | The small fixed tool set is clear and stable | Tool search is context management as well as capability discovery. Evaluate missed-tool and wrong-tool rates. |

Prefer structured inputs and outputs. Separate read from write tools, expose dry-run modes, and make untrusted tool/resource content visible as data rather than instructions.

## Hooks, control, and execution

| Primitive | What it is | Choose it when | Prefer something else when | Notes |
|---|---|---|---|---|
| Hook | Lifecycle middleware that intercepts an event around the agent loop | A known event needs deterministic context injection, validation, audit, notification, approval, or a narrow block | Behavior requires long-running work, nuanced conversational judgment, or recovery inside the main task | Possible events include session/prompt start, before/after tool use, permission request, file or working-directory change, subagent/task start/stop, compaction, instruction/config loading, and stop. Hosts expose different sets. A hook may run a shell command, HTTP call, one-shot model check, or agent, but blocking logic should be fast, deterministic, idempotent, reproducible, and actionable. Keep substantial logic in tested code. |
| Permission/approval policy | Rules governing which capabilities or effects may run automatically or require consent | Tool, path, network, command, data, secret, or action classes need least privilege | The concern is a behavioral preference rather than authority | Approval policy answers “who authorizes this effect?” It does not by itself isolate the process from the machine or network. |
| Sandbox | Isolation limiting what a process can access or affect | Untrusted code, broad tools, external content, or risky writes need containment | The task is read-only and the boundary adds no meaningful safety | Sandbox answers “what can this process technically reach?” Keep it distinct from approval policy. Test escape-relevant assumptions and required writable paths. |
| Execution environment | The compute and dependency envelope in which work runs | Reproducibility, resource limits, platform dependencies, or data locality matter | The host environment is already adequate and stable | Includes container/VM image, filesystem, network/firewall, CPU/RAM/GPU, storage, CI runner, cloud environment, and environment variables. Record version and lifecycle. |
| Worktree/branch/ephemeral workspace | An isolated change surface for concurrent or experimental work | Parallel edits, review, rollback, or untrusted transformations need separation | A single writer and small reversible change are sufficient | Define ownership, merge/integration strategy, cleanup, and how shared/generated state crosses the boundary. |
| Trigger/schedule/webhook/automation | A time- or event-driven way to start work without an interactive prompt | Recurring review, issue/PR intake, alert investigation, or scheduled maintenance is desired | A human should decide whether and when the workflow begins | Examples include scheduled agents, CI events, issue triggers, log alerts, and cloud coding agents. Authenticate events, handle duplicates, and define missed-run recovery. |
| Queue/lease/idempotency key | Delivery and concurrency controls for asynchronous work | Runs can repeat, overlap, outlive a process, or compete for the same resource | Runs are synchronous, singular, and short-lived | Specify deduplication scope, lease expiry, retry semantics, poison-message handling, and reconciliation of partial effects. |
| Secret/config store | Externalized credentials and environment-specific configuration | Values require injection, rotation, scope control, or separation from code/prompts | Values are non-sensitive, stable, and appropriately versioned | Examples include product `settings.json`, `config.toml`, environment configuration, and secret managers. Manifests and settings are control metadata, not agent instructions. |
| Policy/guardrail engine | Deterministic rules or a separate classifier evaluating proposed inputs, outputs, or actions | Governance requires centralized allow/deny/redact/escalate decisions | A narrow tool permission or deterministic hook fully expresses the rule | Measure false allows and false blocks. Model-based classifiers are probabilistic controls and need thresholds, fallback, audit, and appeal paths. |

Hooks are closest to middleware around the agent loop. Use them for event-bound behavior, not as a default home for general workflow instructions.

## Feedback, observability, and evaluation

“Sensor” is a useful conceptual category, not a universally standardized API. Separate pull-based checks, rich observations, and continuously active monitors.

| Primitive | What it is | Choose it when | Prefer something else when | Notes |
|---|---|---|---|---|
| Test/linter/type checker | A repeatable, pull-based assertion or diagnostic | A machine-checkable property can establish correctness | The property is observable only in a live or rendered environment | Examples include unit/integration/e2e tests, formatters, security scanners, type checks, and CI gates. A failing check is useful only if the agent can interpret and act on it. |
| Diff/static review | Inspection of proposed changes before integration | Risk is visible in a code/configuration/document diff | Runtime behavior, rendering, or live state is the real concern | May be human, rule-based, or agent-assisted. Keep author and independent-review roles separate when independence matters. |
| Sensor / feedback channel | A bounded observation of current state during or after action | The agent must see whether an effect occurred or the environment changed | A deterministic return value already proves the result | Examples include command output, DOM, screenshot, API read-back, database query, logs, metrics, and traces. Name the decision that consumes each signal. |
| Observability | Structured evidence for understanding executions and system state | Operators or agents need logs, metrics, traces, lineage, token/context usage, cost, or latency | A local debug message is sufficient and no operational decision depends on history | Instrument correlation IDs, versions, inputs/outputs with redaction, tool calls, approvals, and outcomes. OpenTelemetry is one possible export mechanism. |
| Monitor/event stream | A sensor that stays active or periodically observes and emits change events | CI, deployment, logs, files, or external state changes over time | A bounded synchronous check is enough | Examples include log tailing, CI watchers, deployment watchers, and polling services. Define cadence, backpressure, deduplication, stop conditions, and who handles emitted events. |
| Evaluation suite | Representative, repeatable scenarios and rubrics for probabilistic or end-to-end behavior | Model judgment, tool selection, recovery, or orchestration quality must be measured | Deterministic tests fully define correctness | Include happy paths, ambiguity, stale/conflicting context, tool failure, denied permission, duplicate triggers, unsafe requests, and correct no-action/escalation cases. Gate rollout on severe dimensions separately rather than hiding them in one average. |
| Trace/audit log | Reconstructable record of decisions, actions, approvals, versions, and data access | Compliance, debugging, incident response, or accountability requires provenance | Ephemeral local output is sufficient | Audit is a governance record, not merely verbose logging. Define retention, access, redaction, tamper resistance, and the owner who reviews it. |
| Cost/latency/context meter | Resource feedback about the agent loop | Budgets or service levels affect routing, termination, or model/tool choice | Usage is negligible and no decision responds to it | Feed measurements into thresholds, budgets, or optimization work. Recording a number without a consumer does not close the loop. |
| Human checkpoint | A deliberate point for review, choice, approval, or takeover | Ambiguity, irreversibility, policy, or consequence exceeds the autonomy budget | Reliable automation and recovery make automatic action acceptable | Place checkpoints at effect boundaries, not arbitrary step counts. Show the proposed action, evidence, uncertainty, and recovery implications. |

A dashboard nobody or nothing acts on is visibility, not control. Every critical feedback signal needs a named consumer, decision, and response window.

## Context management and state

| Primitive/technique | What it is | Choose it when | Prefer something else when | Notes |
|---|---|---|---|---|
| Progressive disclosure | Loading instructions, references, or tools only when the active branch needs them | The full corpus would dilute attention or consume context | Nearly all material is required on every run | Skills and context pointers are common implementations. A weak pointer can make necessary material effectively unreachable. |
| Path-scoped context | Loading rules because the current work touches a relevant location | Domain or implementation rules align with repository structure | Relevance depends on task intent rather than path | Keep nested scopes coherent and verify precedence. This is context selection, not access control. |
| Context isolation | Giving a subagent or subprocess a separate context and returning a compact artifact | Exploration, independent review, or specialization would flood the main context | Shared conversational nuance is more important than isolation | Require provenance and sufficient evidence in the returned artifact; compression can hide uncertainty. |
| Compaction/summarization | Replacing older conversational state with a smaller representation | Long-running work would otherwise exceed context | Exact earlier wording or evidence must remain directly available | Preserve decisions, unresolved assumptions, artifact paths, failures, and authorization boundaries. Make critical state durable outside compaction when possible. |
| Curated memory | Persisted selected knowledge outside the immediate conversation | Stable user/project preferences or learned facts should survive sessions | Facts are cheap to retrieve from a canonical repository source | Define who writes, reviews, corrects, expires, and scopes memory. Automatic memory can fossilize implementation habits or stale conclusions, making behavior less deterministic; use it deliberately. |
| Durable workflow state/checkpoint | Explicit machine-readable state enabling resume, reconciliation, and orchestration | Work spans runs, processes, approvals, or external events | One run can complete atomically | Store state transitions and effect identifiers, not only prose summaries. Version schemas and handle replay. |
| Branching/forking | Exploring alternative conversation or execution states from a shared point | Competing designs, hypotheses, or implementations benefit from independent exploration | One path can be evaluated cheaply and reversibly | State how branches are compared, selected, merged, or discarded. Avoid concurrent writes to the same state without ownership rules. |
| Deferred tool schemas/tool search | Discovering and loading capability definitions on demand | A large catalog would crowd the prompt or impair tool selection | The fixed tool set is small | Measure discovery failures and preserve permission visibility even when schemas are deferred. |

Context is a controlled resource. Optimize not only token count but also relevance, authority, freshness, and recoverability.

## Human supervision and interaction surfaces

| Primitive | What it is | Choose it when | Prefer something else when | Notes |
|---|---|---|---|---|
| Plan/review mode | A separated phase for inspecting intent, approach, or changes before effects | The user benefits from a checkpoint before implementation or integration | The action is routine, reversible, and already authorized | A mode is an interaction contract, not proof of correctness. Define what is frozen until approval. |
| Diff/annotation interface | A structured UI for line-, object-, or artifact-level feedback | Chat would make precise revision cumbersome | A simple accept/reject question is enough | Preserve annotations as inputs to the next decision and show what changed in response. |
| Canvas/visual workspace | A persistent spatial or artifact-oriented surface for joint reasoning | Relationships, layout, or iterative composition are difficult in linear chat | Prose or a small diagram communicates the decision clearly | Decide whether the canvas is authoritative state, a derived view, or a temporary scratchpad. |
| Steering/interrupt/cancel | A way for humans to redirect or stop live work | Runs are long, costly, or capable of consequential effects | Work is atomic and finishes before intervention is useful | Cancellation must propagate to tools, subagents, queued work, and monitors where possible. Report partial effects. |
| Approval and handoff artifact | A structured request transferring responsibility to or from a human | The next actor needs evidence, choices, and explicit authority | Informal conversation reliably supplies all necessary context | Include decision requested, options, recommendation, uncertainty, completed effects, pending effects, and recovery path. |

Human supervision is part of the harness, not an exception to it. Design for legible state and reversible intervention.

## Packaging, distribution, and deeper extension

| Primitive | What it is | Choose it when | Prefer something else when | Notes |
|---|---|---|---|---|
| Plugin/package | An installable, versioned bundle of agent capabilities | Cohesive skills, agents, hooks, MCP/LSP configuration, executables, monitors, or styles must be installed and upgraded together | One local skill or tool is the whole deliverable | A plugin packages primitives; it does not replace their design. Contents and manifest namespaces vary by host. Treat portability claims as something to verify with the actual standard and products. |
| Marketplace/catalog | A registry through which packages are discovered, trusted, versioned, and selected | Many packages need organizational or public distribution and lifecycle management | A direct install path and small audience are sufficient | Adding a marketplace/catalog generally registers a source; it does not necessarily install every listed plugin. Define publisher trust, review, pinning, revocation, and update policy. |
| Extension | Host-loaded code or configuration that adds behavior beyond ordinary skills/tools | The host offers a supported extension point for UI, context, events, or execution | A portable primitive expresses the same requirement | Extension systems can intercept events, inject context, change prompts, inspect usage, or customize compaction. Power increases upgrade, trust, and portability costs. |
| Agent SDK/API | Programmatic access to maintained agent-loop capabilities | Custom routing, state, streaming, tool mediation, multi-agent coordination, or embedding is required | Product configuration, skills, tools, and hooks already express the workflow | Clarify which responsibilities the SDK retains—tool loop, compaction, tracing, approvals—and which the application must implement. |
| RPC/headless mode | A process boundary for driving an agent runtime from another application | An existing harness should be controlled as a service or embedded component | An SDK or CLI invocation is simpler | Define protocol versioning, session/state ownership, cancellation, streaming, authentication, and crash recovery. |
| Custom outer harness | Application-owned launcher or workflow controlling one or more agent runs | Product-level routing, evaluation, durable state, or integration cannot live in configuration | An off-the-shelf workflow or agent SDK is adequate | Keep deterministic state and policies outside the model; make agent decisions explicit nodes with observable contracts. |
| Harness fork/modification | Direct modification of the agent runtime itself | A required control point is unavailable and strategic value exceeds ongoing maintenance cost | Upstream extension points, an SDK, or an outer orchestrator can supply it | Highest maintenance and compatibility burden. Define ownership, upstream merge strategy, security review, and an exit condition. |

Configuration files such as `hooks.json`, `mcp.json`, `lsp.json`, `settings.json`, `config.toml`, and plugin manifests are host-specific representations of these primitives. Discover the schema; do not design from filenames alone.

### Extension-depth heuristic

A rough escalation path is:

`instructions -> skill/prompt -> script/tool/MCP/LSP -> hook/policy -> outer orchestration/SDK -> runtime modification`

Move deeper only when the shallower layer cannot express a named requirement. Evaluate plugins and marketplaces on a separate packaging/distribution axis: a plugin can bundle components from several depths.

## Cross-cutting design record

For every selected primitive, specify:

- requirement or failure mode addressed;
- owner and source of truth;
- activation condition and precedence;
- inputs, outputs, and side effects;
- permission, trust, data, and isolation boundary;
- durable state and concurrency behavior;
- timeout, retry, idempotency, cancellation, and stop conditions;
- observable success and failure signals;
- human escalation and recovery path;
- versioning, portability, and target-specific mapping;
- cost and operational burden;
- removal, simplification, or expansion condition.

For every plausible primitive not selected, record `later if` or `not justified` with the condition that would change the decision. This is how the architecture demonstrates broad consideration without accumulating speculative machinery.
