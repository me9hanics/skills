---
name: communication-barrier-breaker
description: "Discussion starter: explains what I typically mean and care about, and how you should adapt. Load once at the start of a session."
---

# Communication barrier breaker

## My style of working

### Workflow modes

- A repo/project or area may be **incomplete**, **suboptimal**, or **well-working**, depending on the part and scope considered. These descriptions can overlap. Tasks may involve extending capabilities, fixing behavior, or designs (of an area, towards a well-working system, etc.).

- I distinguish separate workflow modes / states-situations:
    - **Design mode**: Establish what a well-working system/area should look like, typically from scratch (incompleteness). The state of refining the design - implementation comes in later cycles.
    - **Transition mode**: The state of moving a suboptimal system toward a well-working one. Typically, develop the replacement separately, such as in a new folder, drawing on the old implementation where useful.
    - **Maintain mode**: Maintaining a well-working system. Its design is already proper; fundamental changes are not expected.

- **Extensions** improve or add capabilities; **fixes** correct behavior. Both can happen in well-working or suboptimal systems. During a transition, the current implementation may still receive fixes and extensions while redesign is deferred.

### My typical workflows

- I prefer to establish the overall design iteratively through back-and-forth discussions with agents. I care about what the resulting code will look like and what I should expect to see. This includes data models—which dataclasses or data contracts to use, what shared data should look like—as well as implementations and code-quality tools.
    - This is generally worthwhile unless I ask for something to be implemented quickly.

- My workflow is often hybrid and iterative: I formulate ideas and mocks, usually with mock code, then ask AI for analysis ("idea completion"), evaluate, and challenge them. I consider the feedback, clarify, sometimes implement parts along the way, and iterate.
    - Entire designs can change after a few iterations.
    - A correction can invalidate the surrounding reasoning, not just one detail. Revisit recommendations accordingly.
    - I also use AI engineering tools such as skills.

- My requests include both "help me think" and "do the work" / "complete the work." Questions such as "what is missing?" and "what would you suggest?" typically call for advice, with small examples where useful. Distinguish exploration, proposal, and implementation.
    - Approach larger tasks carefully. Substantial uncommitted work and sketches can make reverts difficult, so do not rely on Git when the baseline is dirty. Before risky changes, present the exact proposed action and ask, "can I?"
    - Before a broad edit, summarize the exact files and behavioral changes proposed. Tie approval to that concrete change set.

- I often create mock implementations for AI to understand or complete. These suggest how I envision the implementation; they are not final.
    - My examples are usually brainstorming sketches, leaving room for design. Empty sections or `...` often signal an "idea scratch," not a finished implementation.

- For pipelines, I value modularity, reproducibility, orchestration, and visibility. A pipeline should support starting at a later stage when given that stage’s input. I draw inspiration from Apache Airflow and other DAG tools without paying for those services. I like Kedro for data pipelines. In Python, I like to define dataclasses for different parts: to describe inputs, outputs, etc. and sometimes metadata.
    - On the other hand I like dataclasses only at points relevant to the user, often just the input, final output, and perhaps one intermediate stage.
    - I value identifying each stage’s input, output, and ownership, and whether it is X-neutral or X-specific where relevant.

- I often like pipelines (data, AI engineering, or other) to produce artifacts organized into folders such as `thing/date/raw`, `.../semi-processed`, or `.../processed`.

- I prefer conceptual minimality to "surgical" compatibility work. Before preserving compatibility, ask which concrete consumer needs it. If none is known, compatibility may be an optional cost. Also: "Which component actually consumes this?"

### Note-taking

- My notes capture a lot of raw material with little decision value, mainly "wants and needs" brainstorming, including:
    - copied AI discussions and suggestions
    - conversations with colleagues or others
    - my own intuitions and ideas for later
    - other information that could affect future decisions

- Such notes quickly lose decision relevance. Most never reflected the full picture, and I find better ways to communicate my vision over time. Their main purpose is to retain all snippets of information available to not lose aspects worth considering.
    - Historical notes are evidence and idea reservoirs: material to synthesize, not requirements to preserve.
    - Decision drift is common, and old notes become obsolete.

- I plan to put raw notes in `notes/raw/` and extract concrete information as bulletpoints into condensed, continuously updated Markdown files. Less weight to outdated claims, insight is more valuable here than obsolete implementations.

- AI often overfits to these notes. But the core value is always thoughtful, no-fluff, minimally sufficient implementation (informed by subsequent discussions). The goal may have changed or become clearer.

- Treat proposed names, mock code, folder trees, and copied discussions as candidates for consideration, not accepted decisions.

- When writing notes for me, distinguish observed facts and when they apply; inferred intent; recommendations versus explicitly accepted decisions; open decisions; and legacy behavior intentionally dropped. Label these inline where practical, without proliferating headings.

- I also leave "digital footprints": folders, placeholders, and example code. When outdated, suggest removing them while condensing useful information into an archive note, possibly an existing one.
    - Watch for the uncommittable clutter these leave in the repository.

## Mutual understanding

- Agents often preserve existing designs to avoid breaking downstream workflows. My work typically does not require or favor this. What is primary is clear, understandable, maintainable design. I prefer to design the right solution first, then convert from the old design if necessary. Backward compatibility is generally not a default requirement in my work.

- My workflows are mainly personal or internal, typically without external policies, and I own the decisions.

- Good design is elegant, understandable, and sufficiently specified. I strongly prefer keeping niche scenarios from complicating the overall design. Agents often add classes, methods, and tests to cover every possibility at once.
    - For example, agents have insisted on separate implementations for two heavily overlapping pipelines. One shared data model and shared functions were sufficient, with each pipeline handling them slightly differently. Only the orchestration in the "main" code needed to differ.

- I believe that to achieve close-to-optimality in a complex system, it needs a middle ground between no abstraction and total abstraction; no extreme is near-optimal. Simplicity should encompass complexity. Aim for the sweetspot between compactness and task specificity, rather than maximum generality or minimum size in the Kolmogorov-complexity sense.

- Minimal implementation means minimal conceptual complexity, not minimal edits to a poor structure. I often prefer replacing an awkward design over preserving it.
    - "More code exists" is not evidence that it should be preserved.

- Before reusing an old implementation, explain its unique behavior, whether that behavior is still wanted, and how it compares with the new expectations.
    - During a transition, avoid compatibility bridges, legacy import repairs, or preserving a runnable intermediate state unless a concrete current consumer or other need has been identified.

- Distinguish facts, estimates, and hypotheses. Earlier conclusions may have been too strong and need correction.