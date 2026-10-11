---
name: communication-barrier-breaker
description: "Discussion starter: explains what I typically mean and care about, and how you should adapt your behavior accordingly. Load this skill once when I start a new session."
---

# Communication barrier breaker

## My style of working

### Workflows
- When working on projects, I prefer to decide the "overview" iteratively - how things should *look* in the implementation and so on - through back-and-forth discussions with agents. Therefore, I care greatly about how the resulting code will look, what I should expect to see, and similar details. This is true for data models (what data classes should I have? "data contracts"? what should shared data look like?), but it also applies to implementations, code-quality tools, and so on.
    - This is generally worthwhile unless I ask for something to be implemented quickly.

- My workflow is often hybrid and iterative: I formulate ideas, create mocks, and give them to AI for analysis ("completion"), evaluation, and challenges, usually with mock code. I then consider those points, respond to them, clarify further, perhaps implement some things along the way, and iterate.
  - Notably, entire designs can change after a few iterations.
  - A user correction after an iteration can easily invalidate the surrounding reasoning, not just one part (or "sentence"). The agent may need to revisit its recommendations.
  - I also use AI engineering tools such as AI skills.

- My requests include both "help me think" and "do the work" (or often "complete the work"). For example, "what is missing?" and "what would you suggest?" typically call for advice rather than implementation, with small examples where useful. Exploration, proposal, or implementation modes.
    - "Do the work," when it covers a larger chunk, should still be approached carefully. Reverts are not always easy because of substantial uncommitted work and sketches, so do not rely on Git when the baseline is already dirty. Before making risky changes, briefly present the exact proposed action and ask, "Can I?"
    - Before a broad edit, summarize the exact files and behavioral changes you propose. Approval should be tied to that concrete change set.

- I often create mock implementations for AI to understand (or complete). This is a way of suggesting how I envision the implementation, but it is not final.
    - I typically do not provide full examples, only "brainstorming" examples of how the data or code might look, leaving room for good design. This is especially apparent when I deliberately leave parts empty or use "..." in places. This typically indicates that it is an "idea scratch," not a final "implementation."

- For pipelines, I primarily value modularity and easy reproducibility. I value orchestration and visibility. Pipelines should be able to start from a later stage when given that stage's input. I do not pay for services such as Apache Airflow or other DAG tools, but I draw inspiration from them. Kedro is a tool I like for data pipelines. With Python, I like to define dataclasses for the different parts: what the input data looks like, how the output should be passed, and so on, perhaps including some metadata.
    - On the other hand, I do not create a dataclass (and object) after every step. I create them only at points relevant to the user. Often, this means just the input, the final output, and perhaps one intermediate stage.
    - I like to identify each stage's input, output, and "ownership," as well as whether it is X-neutral or X-specific when relevant.
- I often like pipelines - whether data-driven, AI-engineering, or otherwise - to produce artifacts and organize them in folders such as *thing*/*date*/raw, .../semi-processed, .../processed, and so on.

- I prefer conceptual minimality to "surgical" compatibility work.
"Before preserving compatibility, it helps to ask: who is the concrete consumer that needs it? If none is known, it may be an optional cost." & "Which component actually consumes this?"

### Note-taking

- When writing notes, I capture a lot of raw material with little decision value; it is mainly "wants and needs" brainstorming. This includes:
    - brainstorming with AI, copied discussions, and written-down suggestions
    - discussions with colleagues or others
    - my own intuitions and suggestions for later
    - any source of information that could affect later decisions
- However, these notes lose decision relevance very quickly. Most were never based on the full picture, and over time I have found better ways to communicate my vision. Their main purpose is to keep "snippets of separate information" available somewhere so that we do not lose aspects worth considering.
    - "Historical notes are evidence and idea reservoirs. Evidence to synthesize, not requirements to preserve."
    - Decision drift is common, and old notes become obsolete.

- I plan to process some of the raw notes. I am actively planning to put them in notes/raw/ while extracting the concrete statements and information as bullet points into more condensed, processed Markdown files, with less emphasis on historically irrelevant claims. Insight is more valuable here than an embodied but obsolete implementation. These files would also be continuously updated.

- AI often "overfits" to these notes. But the core value is always thoughtful, no-fluff, minimally sufficient implementation that accounts for what has been discussed since. The goalposts have shifted over time, and the goal has become clearer.
- Treat proposed names, mock code, folder trees, and copied discussion notes as candidates or points for future consideration, not accepted decisions.

- If I ask you to write a note, try to label the following for future readers: observed facts (and their temporality); inferred intent; recommendations versus decisions I have explicitly accepted; open decisions; and legacy behavior intentionally dropped.

- I also leave other "digital footprints," such as folders, placeholders, and example code. When these become outdated, it is probably worth suggesting that they be removed while their useful information is processed and condensed into a single archive note (perhaps an existing note among other "archives").

- These footprints add uncommittable clutter to the repository, so watch out for them.

## Mutual understanding

- AI agents commonly try to "preserve" an existing design to "avoid breaking existing downstream workflows." My typical workflows neither require nor favor this.
What is actually relevant to me is a good, maintainable, clear, and understandable design. I prefer a more greenfield approach: design the right solution first, then convert from the old design afterward if necessary.
Backward compatibility is typically not a default requirement for my work.

- Because my workflows are mainly personal or internal, they typically have no external policies, and I own the decisions.

- Good design does not mean considering every case and handling each with a special case that shapes the overall design. Good design is elegance. It means elegant code that is not overspecified and is very easy for a human to understand. I strongly prefer not adding complexity to the code for niche scenarios. This runs counter to typical agentic design, in which agents define many classes, methods, tests, and so on to cover every possibility at once.
Example: I have seen two parallel pipelines that overlapped heavily, yet agents insisted on keeping their implementations separate (with two different classes and so on). In fact, the preferred design would consider where classes, dataclasses, and functions are needed and how they fit together ("elegantly"). In this case, a data model clearly fit (hence a dataclass), but one was enough, with each pipeline calling the same shared pipeline functionality and handling it slightly differently. The two were split only in the "main" code; all functionality was shared.
- I believe that for a complex system to work close to optimally, it must find the golden middle ground; no extreme is near-optimal. Neither no abstraction nor total abstraction is desirable. Instead, simplicity should encompass complexity. We should aim for the sweet spot on the spectrum between compactness and task specificity (neither the most general solution nor the minimal design in the Kolmogorov-complexity sense).

- Minimal implementation means minimal conceptual complexity, not minimal edits to a poor existing structure. I often prefer replacing an awkward design over preserving it.
    - "More code exists" is not evidence that it should be preserved.

- Before using an old implementation, explain what unique behavior it contains and whether that behavior is still wanted. Try to provide a comparative analysis of it against the new expectations.
  - During a transition, I prefer not to add compatibility bridges, repair legacy imports, or preserve a runnable intermediate state unless a concrete current "consumer" or similar need has been identified.

- I prefer facts, estimates, and hypotheses to be separated. (Previous conclusions may also have been too strong and need correction.)
