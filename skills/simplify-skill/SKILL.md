---
name: simplify-skill
description: Aggressively simplify SKILL.md and other agent instruction files while preserving observable behavior.
---

# Simplify Skill

Compress agent instructions. Preserve observable behavior, not wording or explanatory prose.

## Core rule

Default to deletion and merging, not sentence-level rewriting.

Keep text only when removing it could plausibly change:
- when the skill triggers
- what action it takes
- what output it produces
- a required constraint or prohibition
- handling of a real edge case

## Remove aggressively

Delete or merge:
- duplicated or overlapping rules
- rationale and explanations of why a rule is sensible
- throat-clearing, transitions, summaries, and motivational prose
- restatements of headings or nearby rules
- examples that only repeat a rule
- obvious best practices that do not change execution
- implementation detail that does not constrain behavior
- enumerated cases covered reliably by one general rule
- defensive repetition of the same requirement in different wording

Do not preserve wording. Preserve semantics.

Prefer one strong general rule over several narrower rules when behavior remains clear.

Keep examples only when they resolve ambiguity, define an edge case, or materially constrain behavior.

Use short declarative sentences, exact common words, and consistent terminology. Do not use em dashes.

Reduce headings and bullets that add no useful structure. Optimize for fewer characters and tokens, not fewer lines.

## Verification

After the first rewrite:
1. Compare original and simplified behavior.
2. Restore anything that changes a trigger, action, output contract, constraint, prohibition, or meaningful edge case.
3. Look again for rules implied by stronger rules, duplicated semantics, unnecessary examples, and explanatory prose.
4. If character reduction is below 15%, perform a second compression pass instead of stopping after cosmetic edits.
5. If the final reduction is still below 15%, state why further deletion would change behavior or why the source was already compact.
6. Report character counts before and after and the percentage reduction.

## Corpus mode

When simplifying multiple skills, treat them as one instruction system.

Find requirements duplicated across files. If a shared parent or common instruction file exists and consolidation is in scope, move genuinely global behavior there and remove local copies. Otherwise flag repeated rules as centralization candidates rather than deleting rules required for standalone behavior.

Flag rules repeated in three or more skills.

Do not assume an external parent instruction exists unless it is present in the supplied corpus or repository.

## Aggressive mode

If the user asks for aggressive simplification, also remove prose whose behavior can be inferred reliably from adjacent rules. Favor the smallest behaviorally equivalent specification and aim for substantial compression, while keeping every observable requirement intact.
