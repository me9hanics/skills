---
name: sentences-rephrase
description: Rephrase short text sentence by sentence - preserving composition, voice, level of detail. Only user-prompted. Used for intra-sentence rewriting rather than full rewrite.
---

# Sentences Rephrase

Perform a **sentence-local edit**. Treat source as accepted composition: each sentence is fixed, has a job and a position. Improve how it says that thing without changing what the text is doing.

## Rephrase

1. Infer the source's language, plus tone/register/characteristic phrasing.
2. Work through it sentence by sentence. Improve grammar, naturalness, syntax, word choice, flow, and concision only within the current sentence. Reorder clauses when useful, but do not move information between sentences.
  - Do not add fluff.
3. Compare the result with the source's structural skeleton. Restore any unnecessary structural or substantive change.

## Preserve

Unless the user explicitly requests otherwise, preserve:

- paragraphs, headings, list items, line groupings, and their order;
- sentence count, sentence boundaries, and sentence order;
- each sentence's claim, purpose, emphasis, and degree of certainty;
- tone, formality, perspective, and the author's recognizable voice;
- established terminology, vocabulary, and characteristic phrases when they are already natural;
- factual content and approximate level of detail.

This is not a summary or rewrite from scratch. Add no new claims, explanations, examples, transitions, headings, or context. Do not merge or split sentences, reorganize the argument, standardize the voice into generic polished prose, or remove intentional informality merely because another style might read more smoothly.

## Output

Return only the rephrased text, with no preface, commentary, change log, or alternative version unless the user asks for one.

## Optional compaction (if prompted)

If user requests, you are ought to reduce text length, even grammatically less-preferred changes, while keeping the meaning. E.g. removing words that are only structural such as "that", "which", adverbs, etc. Typically every ~5th word can be removed and the meaning is still preserved.

## Optional extra edits (if prompted)

If the user requests, you may also do some more text improvements. In this case, you can remove (especially if text doesn't belong there) and merge sentences/parts, but you shall not add new parts (such as "Summary" or so).