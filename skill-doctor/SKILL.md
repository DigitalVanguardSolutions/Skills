---
name: skill-doctor
description: Use this skill whenever the user wants to audit an existing Claude Code skill, check a skill directory for structural problems, lint a SKILL.md, or diagnose why a skill won't load. Triggers on phrases like "audit my skill", "check this skill directory", "lint my SKILL.md", "is my skill valid", or "why won't my skill load". Scans for structural, frontmatter, and reference-integrity issues.
---

# Skill Doctor

Audits an existing Claude Code skill *directory* — not just the description — for structural problems that break loading, fail Anthropic's conventions, or embarrass you on GitHub. Returns a severity-ranked report with a fix for every finding.

## When to use this skill

Use this skill any time the user has a skill directory and wants to know if it's healthy before shipping. Common triggers:

- "Audit my skill at ~/.claude/skills/foo"
- "Check this skill directory"
- "Lint my SKILL.md"
- "Is my skill valid?"
- "Why won't my skill load?"
- "Health-check my skill before I publish"

It completes the meta-skills trilogy:

- `skill-scaffolder` builds a skill
- `skill-trigger-tester` checks the description fires
- `skill-doctor` audits the whole directory is sound

## How it works

### Step 1 — Locate the skill

Accept a directory path or a single SKILL.md path. If given a directory, find `SKILL.md` inside it. If given a SKILL.md path, treat its parent as the skill root.

List the full directory tree first so the user sees what's being audited. If no SKILL.md is found, stop and report that — there's nothing to audit.

### Step 2 — Run the checklist

Apply every check in `references/checklist.md`. The checks fall in four groups:

1. **Structure** — required files and folders present, no empty conditional folders
2. **Frontmatter** — valid YAML, required keys, name matches directory
3. **Body** — under 500 lines, no leftover scaffolding placeholders
4. **Reference integrity** — every file SKILL.md points to exists; flag orphaned reference files

Run all checks. Do not stop at the first failure. Collect every finding.

### Step 3 — Assign severity

Grade each finding per `references/severity-levels.md`:

- 🔴 **Error** — the skill is broken or won't load (missing SKILL.md, malformed frontmatter, missing `name`/`description`, broken file reference)
- 🟡 **Warning** — quality/convention issue that should block publishing (no LICENSE, no examples, body over 500 lines, leftover placeholders)
- 🔵 **Info** — polish (orphaned reference file, README missing a usage section)

### Step 4 — Compute the verdict

Roll findings into one verdict per `references/severity-levels.md`:

- **BROKEN** — one or more 🔴 errors
- **NEEDS WORK** — no errors but one or more 🟡 warnings
- **HEALTHY** — no errors, no warnings (info-only is still HEALTHY)

### Step 5 — Report

Produce a structured report:

```
Skill: {name}  ({path})
Verdict: {BROKEN | NEEDS WORK | HEALTHY}

🔴 Errors ({n})
  [D{n}] {finding} — {file}
         Fix: {one-line fix}

🟡 Warnings ({n})
  [D{n}] {finding} — {file}
         Fix: {one-line fix}

🔵 Info ({n})
  [D{n}] {finding}

Directory tree:
  {tree}
```

For each finding, pull the concrete remediation from `references/fix-playbook.md`. Cite the check ID (e.g., D11) so the user can ask follow-ups.

### Step 6 — Hand off the description check

Skill Doctor does a *light* description check (present, length within Anthropic's 1024 hard limit, has `name`). It does **not** judge trigger pushiness — that's a different job. End the report with:

> For a deep analysis of whether the description will actually trigger, run `skill-trigger-tester` on this skill.

## Inputs

- A skill directory path, **or**
- A path to a single SKILL.md (parent treated as the skill root)

## Output

A severity-ranked audit report (🔴 / 🟡 / 🔵), an overall verdict, the directory tree, and a concrete fix for every finding.

## References

All audit logic lives in `references/`:

- `references/checklist.md` — the full structural / frontmatter / body / reference-integrity checks
- `references/severity-levels.md` — how findings map to severity and the overall verdict
- `references/fix-playbook.md` — the concrete remediation for each check

Load these at execution time. Do not paraphrase from memory.

## Quality bar

An audit from this skill must:

- Cite specific check IDs, not vague impressions
- Give a concrete fix for every finding, pulled from the playbook
- Separate "won't load" errors from "won't impress" warnings
- Never invent findings — only report what the directory actually contains
- Defer trigger-quality judgment to `skill-trigger-tester` rather than duplicating it

## Iteration

Skill Doctor catches structural problems. For trigger-accuracy tuning, use `skill-trigger-tester`. For automated eval loops with real model calls, use Anthropic's [skill-creator](https://github.com/anthropics/claude-skills).
