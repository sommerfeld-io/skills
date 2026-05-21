---
name: triage-issues
description: 'Triage all open GitHub issues labelled "needs-triage" in the current repository. Turns skeleton issues into fully described issues (user stories, feature requests, or bug reports) and swaps the triage label for "refinement-done". No arguments required.'
---

# Triage Issues

Automatically refine all open issues labelled `needs-triage` in the current GitHub repository. Each skeleton issue is enriched into a well-structured description that matches the issue type, after which the triage label is replaced with `refinement-done`.

**No input parameters or additional context from the user are required or accepted.**

## Workflow

### 1. Fetch issues to triage

Use the GitHub CLI to list all open issues (not pull requests) that carry the label `needs-triage`:

```bash
gh issue list --label "needs-triage" --state open --json number,title,body,labels --limit 200
```

- Filter the JSON output to include only items that are issues (not PRs). `gh issue list` already excludes PRs, so no additional filtering is needed.
- If the result is empty, print a short message ("No issues require triage.") and stop.

### 2. Iterate through the issues

For each issue in the list, execute steps 3–5 below before moving to the next issue.

### 3. Determine issue type and write the refined description

Inspect the labels on the issue to identify its type. Use the **first matching rule** below.

#### Issue type: `story`

Write a **user story** following standard agile conventions:

- **Title**: keep unchanged.
- **Description structure**:
  - Opening user-story sentence: `As a <role>, I want <goal>, so that <benefit>.`
  - **Background / Context** section: brief explanation of why this story exists, derived from the existing issue body.
  - **Acceptance Criteria** section: a numbered list of criteria that must be satisfied for the story to be considered done.
  - **Given-When-Then Scenarios** section: at least two scenarios formatted as:
    ```
    Scenario: <short title>
    Given <precondition>
    When  <action>
    Then  <expected outcome>
    ```
  - **Notes** section (optional): any additional details, edge cases, or open questions found in the original issue body.

#### Issue type: `task`

Write a **feature request / requirements description**:

- **Title**: keep unchanged.
- **Description structure**:
  - **Summary** section: one or two sentences describing what needs to be implemented, derived from the existing issue body.
  - **Motivation** section: why this work is needed.
  - **Requirements** section: a numbered list of concrete, testable requirements.
  - **Out of Scope** section: explicitly list anything that is intentionally excluded, if inferable from the existing body.
  - **Notes** section (optional): any additional details or open questions.

#### Issue type: `bug`

Write a **bug report**:

- **Title**: keep unchanged.
- **Description structure**:
  - **Summary** section: one sentence describing the defect.
  - **Steps to Reproduce** section: numbered list of steps.
  - **Expected Behaviour** section.
  - **Actual Behaviour** section.
  - **Impact / Severity** section: assess impact based on available context.
  - **Possible Cause** section (optional): hypothesis derived from the existing body.
  - **Notes** section (optional): any additional details or open questions.

#### Fallback (no matching type label)

If the issue carries none of the labels above, write a general **description**:

- **Summary** section: expand the existing body into a clear, complete description.
- **Background** section: context and motivation.
- **Definition of Done** section: a checklist of what "done" looks like.

### 4. Update the issue body

Use the GitHub CLI to replace the issue description with the refined text. Keep the title exactly as it is:

```bash
gh issue edit <number> --body "<refined description>"
```

Ensure the body is valid Markdown with real line breaks, not escaped `\n` sequences.

### 5. Swap the triage label

Remove `needs-triage` and add `refinement-done`:

```bash
gh issue edit <number> --remove-label "needs-triage" --add-label "refinement-done"
```

If the label `refinement-done` does not yet exist in the repository, create it first:

```bash
gh label create "refinement-done" --color "#0075ca" --description "Issue has been refined and is ready for planning"
```

### 6. Confirm

After all issues have been processed, print a summary table:

| Issue # | Title | Type | Status |
|---------|-------|------|--------|
| #42 | ... | story | refined |

## Additional Requirements

- Never modify the issue title.
- Base all refinement solely on the data already present in the issue (title, body, labels, comments). Do not invent requirements or assumptions that cannot be derived from the existing content.
- The refined body must be well-formatted Markdown with appropriate headings, lists, and spacing.
- All headings within the refined issue body must use level 3 (`###`) or lower — never `#` or `##`.
- Use ordered or unordered lists for non-actionable content. Use checkbox lists (`- [ ] item`) for actual todos or actionable items.
- If `gh` is not authenticated, inform the user and ask them to run `gh auth login` before proceeding.
- Only process **open** issues. Skip any issue that is closed.

## Quality Criteria

- Every issue that carried `needs-triage` at the start of the run now carries `refinement-done` and no longer carries `needs-triage`.
- Each refined description follows the structure appropriate to its type label.
- No issue title has been modified.
- The refined body reads as a complete, self-contained document — a reader unfamiliar with the project should understand what is needed without consulting the original skeleton.

## Example Prompts

- "Triage the issues."
- "Run the issue triage skill."
