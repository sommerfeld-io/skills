---
name: retro
description: Run a lightweight retrospective after an AI-assisted implementation or iteration, refine the result with the user, and optionally persist approved learnings into repository markdown files.
---

# Retro

Use this skill to run a **micro-retrospective after the latest AI-assisted development iteration**.

The iteration can be:

- a full implementation,
- a partial implementation,
- a smaller reviewable step,
- or any other meaningful AI-assisted development increment.

The size of the changeset may differ. This skill must still keep the retrospective lightweight, practical, and focused on improving future AI-assisted development.

The goal is to:

1. summarize the task,
2. summarize the outcome,
3. identify what worked,
4. identify friction and rework,
5. identify wrong assumptions,
6. detect feedback likely to repeat,
7. decide whether any rule, guideline, or agreement should be persisted, updated, or removed in markdown files for future agent work.

It is completely acceptable for a retro to end with **no new rule** if the iteration went smoothly and there is no durable lesson to persist. It is also acceptable for a retro to update existing rules or remove existing rules if the iteration revealed that current instructions need revision.

## When to use

Use this skill:

- after an implementation,
- after an iteration,
- after a reviewable AI-assisted change,
- when the user wants to reflect on the most recent work,
- when the user wants to improve future alignment with the agent.

## When not to use

Do not use this skill:

- for broad historical pattern analysis across many retro logs,
- for consolidating many retros into repo-wide instruction updates (this should be done with the `retro-consolidate` skill instead),
- for unrelated implementation tasks,
- for direct instruction-file refactoring without first running a retrospective.

## Core behavior

This skill must:

- run a lightweight retro on the most recent AI-assisted iteration,
- avoid unnecessary process overhead,
- avoid inventing issues that did not happen,
- avoid promoting one-off frustrations into durable rules too quickly,
- revise the retro if the user wants changes,
- ask permission before persisting rule changes into markdown files,
- persist the final agreed retro result into the retro log folder.

## Retrospective format

Run a micro-retrospective for the last AI-assisted development iteration.

First summarize:

- **Task:** summarize the task
- **Outcome:** summarize the short result

Then answer in **5 bullets**:

1. What worked?
2. What caused rework?
3. What assumption was wrong?
4. What feedback will likely repeat?
5. What rules / guidelines / agreements should we persist in markdown files or remove from markdown files or update existing in markdown files for the agent?

Important:

- It is okay if the answer to item 5 is: no durable rule needed.
- It is okay if the answer to all items is: nothing to report (but only if really everything went smoothly).
- Keep the retro short and practical.
- Prefer evidence-based observations over abstract opinions.

## Clarifying questions

If you are unclear on something or need more information, ask the user.

But:

- do **not** spam the user with questions,
- only ask when necessary to produce a useful retro,
- prefer proceeding with reasonable context when enough information is already available.

## Review and revision loop

After producing the retrospective:

1. present the retro result to the user,
2. ask whether the user agrees or wants changes,
3. if the user wants changes, incorporate the feedback,
4. present the updated retro again,
5. ask again whether the user agrees,
6. repeat until the user agrees with the retro result.

Do not skip this confirmation loop.

## Rule classification

For each proposed rule, guideline, or agreement, classify it as one of:

- `.github/copilot-instructions.md` in this repository
- `AGENTS.md` / path-specific instruction
- retro log only

Use this classification logic:

### `.github/copilot-instructions.md`

Use for:

- repo-wide development norms
- broad working agreements
- durable cross-cutting expectations for AI-assisted development

Examples:

- prefer small, reviewable changes
- add regression tests for bug fixes
- avoid unnecessary new dependencies

### `AGENTS.md` / path-specific instruction

Use for:

- subsystem-specific expectations
- folder-specific patterns
- local conventions that should not apply to the whole repo

Examples:

- testing expectations for a parser directory
- documentation rules for a docs subtree
- shell scripting conventions for a scripts folder

### retro log only

Use for:

- one-off observations
- weakly supported lessons
- temporary or situational feedback
- items not yet strong enough to become durable instructions

## Persistence workflow

Once the user agrees with the retro:

1. suggest which rule / guideline / agreement should be persisted and where,
2. ask for the user's permission to persist the files,
3. if necessary, ask the user to provide missing information about where a rule should be persisted,
4. only persist after the user agrees.

Do **not** update markdown instruction files without user approval.

## Retro log persistence

After:

- the user agrees with the retro result, and
- any approved rule / guideline / agreement persistence is handled,

always write and persist the agreed retrospective result as a markdown file.

Persist retro log files in: `docs/.copilot/retro/logs`

Create the folder if it does not exist.

### Retro log filename format

Use this format: `<counter>-<very-short-info>.md`

Where:

- `<counter>` is an incrementing integer
- `<very-short-info>` is a short, human-readable summary of the task or iteration

Examples:

- `1-parser-fix.md`
- `2-readme-update.md`
- `3-import-cleanup.md`

### Counter rules

When creating a new retro log file:

- inspect existing files in `docs/.copilot/retro/logs`
- if no files exist in `docs/.copilot/retro/logs`, then inspect `docs/.copilot/retro/logs/consolidated`
- find the highest existing counter value across both folders or start at 1 if no files exist,
- determine the next available incrementing integer
- preserve existing numbering
- do not overwrite an existing retro log file
- use the next free counter value

## Retro log content

The persisted retro markdown file should contain:

- task summary
- if the implementation is based on a GitHub issue or pull request, include a link to it
- outcome summary
- the 5 retro answers
- agreed changes after user feedback
- proposed rule classifications
- final persistence decision

Keep it concise but complete enough that future review can understand what was learned.

## Instruction update behavior

If the user agrees to persist rules/guidelines/agreements:

- update the relevant markdown files,
- keep changes concise,
- avoid duplicating existing instructions unnecessarily,
- prefer durable wording over one-off wording,
- do not add noisy or overly specific rules.

If the user does not want to persist any rule:

- still persist the retro log file.

## Writing principles

When drafting rules, prefer:

- clear
- actionable
- durable
- non-redundant wording

Prefer examples like:

- Prefer small, reviewable changes over broad multi-concern patches.
- Add regression tests for bug fixes unless no meaningful automated test is possible.
- Match established repository patterns before introducing new abstractions.

Avoid examples like:
- Be smarter
- Write cleaner code
- Use best practices
- Improve quality

## Success criteria

This skill is successful if it:

- produces a useful lightweight retro,
- avoids unnecessary questions,
- allows user feedback and revision,
- classifies candidate rules correctly,
- asks permission before persisting instruction changes,
- persists the final agreed retro log into `docs/.copilot/retro/logs`,
- and keeps the process simple enough to run after each iteration.
