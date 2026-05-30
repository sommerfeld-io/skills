---
name: retro-consolidate
description: Review unconsolidated retrospective logs, detect recurring patterns, compare them against existing repository instructions, propose instruction updates, and archive processed retro logs.
---

# Retro Consolidate

Use this skill to periodically consolidate AI-development retrospective logs into durable repository guidance.

This skill inspects all retrospective markdown files that have **not yet been consolidated**, identifies recurring findings, compares those findings against the repository's current Copilot/agent instructions, and proposes concrete updates where recurring lessons are not yet reflected in instructions.

After consolidation, all processed retro log files must be moved into: `docs/.copilot/retro/logs/consolidated`

Create this folder if it does not exist.

## Scope

This skill applies to retrospective logs stored under: `docs/.copilot/retro/logs`

It must only process retro log markdown files that are **not already inside**: `docs/.copilot/retro/logs/consolidated`

## Goals

1. Find recurring patterns across unconsolidated retrospective logs.
2. Distinguish one-off complaints from durable process lessons.
3. Compare recurring lessons against the repository's existing instructions.
4. Propose updates only for lessons not yet captured well enough in current instructions.
5. Preserve a clear summary of findings.
6. Move processed retro logs into the consolidated folder after completion.

## Repository inputs to inspect

Inspect these instruction sources if they exist:

- `.github/copilot-instructions.md`
- `.github/instructions/**/*.instructions.md`
- any relevant `AGENTS.md` files
- any other repo-specific AI guidance files explicitly referenced by those files

Inspect these retrospective sources:

- all markdown files under `docs/.copilot/retro/logs`
- exclude files already under `docs/.copilot/retro/logs/consolidated`

## When to use

Use this skill when:

- enough retrospectives have accumulated to justify consolidation
- recurring review feedback is starting to appear
- repository instructions may be lagging behind actual workflow learnings
- you want to refine Copilot guidance based on repeated implementation experience

## When not to use

Do not use this skill when:

- there are no unconsolidated retro log files to process
- there is only one unconsolidated retro log and no cross-retro pattern analysis is possible
- the goal is to create a new retro for a single iteration (this should be done with the `retro` skill instead)
- the task is to directly edit implementation code unrelated to retro findings

## Consolidation principles

Follow these principles strictly:

1. **Prefer recurring evidence over isolated observations.**
   A lesson should usually appear multiple times, or be strongly justified by impact, before it becomes a durable instruction.

2. **Do not promote one-off frustrations into repository policy.**
   Temporary annoyance is not enough.

3. **Prefer decision rules over stylistic nitpicks.**
   Good instruction example:
   - "Prefer small, reviewable changes."
   Weak instruction example:
   - "Write nicer code."

4. **Do not duplicate existing instructions unnecessarily.**
   If a lesson already exists in repo instructions, do not propose it again unless the existing wording is clearly too vague or incomplete. Updating existing instructions is also a valid option if they are on the right track but need refinement.

5. **Choose the right destination for each lesson.**
   - repo-wide norms → `.github/copilot-instructions.md`
   - path/subsystem-specific lessons → `.github/instructions/*.instructions.md` or local `AGENTS.md`
   - weak or unproven findings → summary only, not instruction files yet

6. **Keep instruction changes minimal and durable.**
   Consolidation should improve instruction quality, not create instruction bloat.

## Required workflow

### Step 1 - Gather unconsolidated retros

- Find all markdown files under: `docs/.copilot/retro/logs`
- Exclude: `docs/.copilot/retro/logs/consolidated/**`

If there are no unconsolidated retro logs, stop and say so.

### Step 2 - Read current instruction context

Inspect current repository instruction files, including:

- `.github/copilot-instructions.md`
- `.github/instructions/**/*.instructions.md`
- relevant `AGENTS.md` files

Summarize the instruction coverage before proposing any changes.

### Step 3 - Extract findings from retros

For each unconsolidated retro log, extract:

- repeated success patterns
- repeated friction points
- repeated wrong assumptions
- repeated review comments
- candidate durable rules already suggested in retros
- subsystem-specific patterns if visible

### Step 4 - Cluster and evaluate patterns

Group findings into categories such as:

- planning and decomposition
- change size and reviewability
- testing and regression coverage
- architecture and abstraction
- dependency management
- documentation updates
- task framing and ambiguity handling
- repo/subsystem-specific conventions

For each cluster, evaluate:

- frequency
- severity
- whether it is already covered by instructions
- whether the current instruction is too weak, too broad, or missing

### Step 5 - Produce consolidation result

Produce a structured result with:

1. recurring findings
2. findings already covered by instructions
3. findings not yet covered well enough
4. proposed instruction updates
5. suggested destination for each proposed update

Communicate this result clearly and concisely to the user.

### Step 6 - Update or draft instructions

When appropriate, draft concrete markdown updates for the relevant instruction files. Keep wording:

- clear
- actionable
- durable
- non-redundant

### Step 7 - Archive processed retros

After consolidation is complete, move every processed retro log file into: `docs/.copilot/retro/logs/consolidated`

Create the folder if it does not exist.

Do not leave processed files in the unconsolidated folder.

## Output format

Return results in this structure:

### Consolidation summary
- number of retro logs processed
- number of recurring patterns identified
- number of proposed instruction changes

### Recurring findings

- list recurring themes with short evidence-based rationale

### Already covered by instructions

- list findings already reflected in repo instructions
- mention where they are covered

### Gaps in current instructions

- list recurring findings not adequately covered yet

### Proposed instruction updates

For each proposed update include:

- proposed rule text
- destination file
- rationale

## Decision rules for proposing updates

Promote a finding into instructions only if at least one of these is true:

- it appears repeatedly across multiple retros
- it caused substantial rework or repeated review churn
- it represents a clear stable preference in how development should be delegated
- it closes an obvious and durable gap in the current instructions

Do not promote a finding if:

- it is purely situational
- it depends on temporary project conditions
- it is already clearly covered by current instructions
- it reflects taste rather than a reusable engineering/process rule

## File handling rules

- Treat all files under `docs/.copilot/retro/logs/consolidated` as already processed.
- Only move files that were successfully inspected during this consolidation run.
- Preserve original filenames when moving them.
- Create `docs/.copilot/retro/logs/consolidated` if missing.
- Do not delete retro logs.
- Do not overwrite existing consolidated files without checking for conflicts first.

## Preferred instruction style

When drafting instruction updates, prefer wording like:

- Prefer small, reviewable changes over broad multi-concern patches.
- Add regression tests for bug fixes unless no meaningful automated test is possible.
- Avoid introducing new dependencies unless the benefit clearly outweighs added maintenance cost.
- Match established repository patterns before introducing new abstractions.
- Call out assumptions explicitly when requirements are ambiguous.

Avoid wording like:

- Be smarter
- Write cleaner code
- Use best practices
- Improve quality

## Success criteria

This skill is successful if it:

- inspects all unconsolidated retro logs
- compares them against current repo instructions
- identifies recurring instruction-worthy gaps
- proposes concise and durable instruction updates
- archives all processed retro logs into the consolidated folder
- avoids creating noisy or redundant instructions
