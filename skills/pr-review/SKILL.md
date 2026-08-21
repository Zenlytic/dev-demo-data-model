---
name: pr-review
description: Use when the user asks to review a pull request, review a branch's changes, review a diff, do a code review, sanity-check data model changes before merging/publishing, or asks "what changed" between a branch and master/production before it goes live.
---

# PR Review

Use this skill to review a set of pending changes to the Git-backed workspace data model (a branch,
a diff, or "what changed since master") before they are merged or published. This is a review
workflow, not an editing workflow — do not modify files unless the user explicitly asks you to fix
what you found.

## Workflow

1. Identify what to review: a branch name, a commit range, or "current uncommitted changes". If the
   user names a branch, use local Git to inspect it (e.g. `git -C /data_model log`,
   `git -C /data_model diff master..<branch>`). Never run network Git commands (`fetch`/`pull`/`push`
   outside the provided tools) — use `pull_context` first if the branch may not be hydrated locally.
2. Get the full diff before commenting on anything:
   ```bash
   git -C /data_model diff master...<branch> -- .
   ```
   Read every changed file in full context, not just the diff hunks, when the change touches YAML
   structure (joins, fields, relationships) — a diff alone can hide surrounding logic that a change
   breaks.
3. Run `validate_context` against the branch to catch YAML/data-model errors before doing a manual
   read. Treat any validation failure as a blocking issue.
4. Review the change against this checklist:
   - **Correctness**: Do new/changed measures use valid patterns (e.g. `type: sum` with a raw column,
     or `type: number` with an aggregate `sql` expression)? Are dimension groups used for time fields
     instead of plain dimensions?
   - **Joins**: Are new `relationships` correct, and do they specify the right cardinality
     (`many_to_one`, `one_to_many`, `many_to_many`)? Could a join fan out results? Is aggregation done
     in a CTE before a one-to-many/many-to-many join where needed?
   - **Naming & style**: Do new fields follow existing naming conventions in the view? Are reserved
     words like `day`, `month`, `time`, `order` avoided as field names?
   - **Documentation**: Do new fields have a `description` (user-facing) and, where the logic is
     non-obvious, a `zoe_description` (agent-only)? Are low-cardinality categorical fields marked
     `searchable: true`, and high-cardinality/ID/timestamp fields explicitly not?
   - **Blast radius**: Does the change modify or remove an existing field, join, or default date that
     other fields, views, or skills might depend on? Search for other references to the same field or
     view before approving a rename or removal.
   - **Secrets & scope**: No credentials, connection secrets, or customer-private data introduced.
     Change is scoped to what the PR claims to do — flag unrelated or unexplained edits.
   - **Skills & system prompt** (if touched): Is the change in the right surface per the context
     surface rules (system prompt = universal always-on rules; skill = on-demand recurring workflow;
     field `zoe_description` = field-specific instructions)? Is a skill's YAML frontmatter valid with a
     `description` that starts with clear trigger conditions?
5. Summarize findings as a short structured review, not a wall of prose:
   - **Summary**: one or two sentences on what the branch does.
   - **Blocking issues**: validation failures, broken joins, invalid measures, missing required fields
     — must be fixed before merge.
   - **Suggestions**: style, documentation, discoverability improvements — non-blocking.
   - **Looks good**: call out anything done well, briefly.
6. If the user asks you to fix the issues you found, treat that as a normal context-editing task: read
   `/skills/context-editing/SKILL.md`, make the minimal fix, `validate_context`, and persist with the
   Git workflow available in the conversation (commit locally, then `push_context` to the same
   branch).

## What NOT to Do

- Do not approve a change purely because `validate_context` passes — validation only catches YAML
  syntax/schema errors, not join fan-out risk, bad naming, or missing documentation.
- Do not silently rewrite the branch while "reviewing" it. Report findings first; only edit if asked.
- Do not review topics as if they were a preferred pattern — if a PR adds a new topic, flag that
  `relationships` in model YAML is the current preferred approach instead.
- Do not merge or push to `master`/production branches as part of a review; reviews conclude with a
  report, and any fix commits go back to the branch under review unless told otherwise.

## Output Format

Keep the review concise and scannable. Prefer this structure:

```
### PR Review: <branch/commit range>

**Summary:** <what this change does>

**Blocking issues**
- <issue> (`<file path>`)

**Suggestions**
- <suggestion> (`<file path>`)

**Looks good**
- <positive note>
```

If there are no blocking issues, say so explicitly rather than leaving the section blank.
