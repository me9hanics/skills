---
name: hanics-writing-style
description: Write or revise professional documents and messages in Hanics's style, including cover letters, technical descriptions, explanations, and brief factual reporting. Use for his authored deliverables or requests to write in his style; not ordinary discussion with an assistant. Compress supplied text while preserving every relevant informative statement.
---

# Hanics's writing style

Write compact, honest, informative prose with a recognizable human perspective. Treat this as guidance for the authored text, not for how to converse with the user.

## Choose the approach

Infer the audience, purpose, genre, and permitted extent of editing from the request. Compose or restructure when useful and authorized; preserve the composition when asked for local edits. If `sentences-rephrase` is also requested, its sentence-local boundary still applies.

Read only the references relevant to the writing; combine them when the document serves more than one purpose:

- [Correspondence](references/correspondence.md): cover letters, applications, outreach, and professional emails.
- [Technical descriptions](references/technical.md): documenting a system, process, proposal, or body of information.
- [Teaching](references/teaching.md): helping the document's audience understand a concept.
- [Factual reporting](references/reporting.md): short attributed reports, news digests, or factual updates.

Current user instructions take precedence. Use applicable genre guidance and user-authored or approved examples to refine this style. Treat old AI descriptions and unapproved drafts as weaker evidence. Keep this skill usable without access to the user's chat history or personal repositories.

## Shared voice

- **Concrete and direct.** Prefer ordinary verbs and specific information. Explain what someone did, what happened, or how something works. Convey significance through evidence and reasoning rather than corporate phrasing, inflated adjectives, or stock enthusiasm.
- **Dense but readable.** Omit words that add flow without adding meaning when the result remains clear. Compact phrasing can be slightly elliptical; repair accidental grammar errors without automatically expanding it into formal prose. Vary sentence length naturally. Use longer sentences for connected ideas and shorter ones for clear turns in the reasoning.
- **Reasoned and self-contained.** Give enough context for the intended audience. Preserve useful causal links and explain how and why, instead of listing outcomes alone. Use paragraphs for connected reasoning and lists or tables for genuinely parallel information.
- **Reflective where it belongs.** Preserve personal observations, motivation, judgments, and what an experience taught or changed. Let reflection explain the connection between experience and the present point. Ground it in supplied material; factual reporting and impersonal documentation need no added personal commentary.
- **Honest in both directions.** State supported contributions directly while retaining genuine limits and uncertainty. Keep experience separate from interest, intention from accomplishment, possibility from demonstrated result, and the writer's contribution from the team's. Preserve meaningful qualification without adding empty hedging.
- **Human.** Allow purposeful warmth, characteristic phrasing, and natural personal details. Match the relationship and language rather than imposing ceremonial politeness or a uniformly academic register.

## Compress without losing information

Compression applies across genres. Preserve **every relevant informative statement**, including qualifications and relationships, in some explicit form. A shorter text is successful only when the relevant meaning survives.

1. Identify the source's distinct informative statements and the scope the user wants retained. For a new document drawn from a broad collection, select material relevant to its purpose. For compression of an already-selected passage, retain its information unless the user authorizes exclusions. Length pressure alone does not make a fact irrelevant.
2. Remove verbal overhead and genuine repetition. Combine related statements, share repeated context, and change structure when permitted. Keep actors, quantities, chronology, conditions, exceptions, attribution, uncertainty, and causal links attached to the claims they qualify.
3. Compare source and result in both directions: each relevant source statement must map to wording in the result, and each output claim must be supported. Restore any lost distinction; remove invented connections or implications. Perform this check internally rather than emitting a coverage table by default.

There is no target deletion percentage. If a hard length limit cannot accommodate the relevant information, make the conflict explicit and offer the closest faithful version or a concrete scope choice. Do not silently omit information to make it fit.

Illustrative examples, not facts about the user:

- Source: "I have experience in data engineering. I became interested in biology because of the project."
  Compact: "I have data engineering experience; the project sparked my interest in biology."
  Both the distinction between experience and interest and the stated origin of that interest survive.
- Source: "The job runs nightly. It retries failed requests twice. Persistent failures are logged for manual review."
  Compact: "The nightly job retries failed requests twice, logging persistent failures for manual review."
  Schedule, retry count, failure condition, and review purpose survive.

## Deliver

Return the requested text in its intended format. Add explanation, alternatives, or an edit log only when requested or needed to resolve a material ambiguity or constraint. Before delivering, check information coverage, claim strength, genre fit, and whether stylistic polish has erased the writer's compactness or reflection.
