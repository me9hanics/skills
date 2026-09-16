# Migrating an existing instruction-heavy harness

Read this when the workflow already exists as markdown notes, prompts, scripts, CI, hooks, or agent configuration. The aim is one source of truth per concern and an incremental migration that preserves behavior.

## Inventory by behavior

Inspect relevant instruction and automation surfaces. Trace referenced files before judging them. For each meaningful statement or mechanism, record:

- current location and consumers;
- behavior it is meant to cause;
- activation scope: always, path, user intent, lifecycle event, or external trigger;
- whether it guides judgment or can be enforced mechanically;
- evidence it still matters: recent use, failure, test, or owner;
- conflicts, duplication, and staleness;
- proposed disposition: `keep`, `move`, `rewrite`, `enforce`, or `retire`.

Do not classify whole files when their contents serve different roles.

## Put each concern at its natural home

| Content found | Natural home |
|---|---|
| Compact, nearly universal invariant or context pointer | Root instructions |
| Directory- or file-specific rule | Scoped/nested instructions |
| Domain vocabulary, schema, or durable fact | Canonical domain/reference document |
| Decision and load-bearing rationale | ADR or decision record |
| Reusable task judgment or procedure | Skill |
| Human-started lightweight recipe | Prompt/command |
| Deterministic transformation or check | Script, test, linter, or tool |
| Event-bound check, injection, audit, or block | Thin hook calling tested logic |
| External resource or operation | Tool/API/MCP boundary |
| Role-specific model/tool/permission bundle | Custom agent profile |
| Runtime state, history, or resume point | Explicit state store with ownership and retention |
| Secret or access restriction | Secret store and permission policy |
| Duplicate, obsolete, or speculative guidance | Retire after its consumers are redirected |

The category follows behavior, not the file extension. A markdown file may be the canonical domain source; a skill may point to it rather than copy it.

## Migration order

1. Capture representative current scenarios and failure cases so behavior can be compared.
2. Select the canonical source for each fact or rule.
3. Replace repeated text with precise pointers whose wording states when to follow them.
4. Extract on-demand workflows into skills and deterministic operations into scripts/tools.
5. Add hooks or policies only for event-bound requirements that need reliable enforcement.
6. Run the old and new paths against the same scenarios when the change is consequential.
7. Redirect every known consumer before retiring duplicated material.
8. Retire content only with the user's authorization; preserve history through version control or an explicit archive when needed.

Migrate in vertical slices. One trigger-to-evidence path should work before broad restructuring begins.

## Common migration traps

- Moving a long instruction file wholesale into a skill without separating facts, workflow, and enforcement.
- Loading all reference material at session start instead of using context pointers and on-demand retrieval.
- Expressing a security or correctness invariant only as prose when it can be checked.
- Turning every script into an agent step, adding nondeterminism to deterministic work.
- Adding hooks that are slow, stateful, opaque, or impossible to reproduce outside the lifecycle event.
- Creating several agents without explicit handoff contracts or a single owner of final state.
- Deleting old guidance before locating its consumers or establishing equivalent behavior.
- Packaging a plugin before the capabilities form a cohesive, reusable release unit.

## Migration deliverable

Include a table with: current artifact, concern, current consumers, disposition, destination/control, migration dependency, and verification. Call out rules whose intent cannot be established and recommend an owner decision rather than guessing.
