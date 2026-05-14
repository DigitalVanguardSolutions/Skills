---
name: skill-trigger-tester
description: Use this skill whenever the user wants to test a Claude Code skill's description, check if a SKILL.md will fire on intended phrases, grade trigger accuracy, or audit pushiness. Triggers on phrases like "test my skill", "will this skill fire", "grade my SKILL.md", "audit a skill description", or "is this skill pushy enough". Applies static rules plus trigger-simulation.
---

# Skill Trigger Tester

Tests whether a Claude Code skill's description is "pushy" enough to actually fire when it should — and *not* fire when it shouldn't. Returns a graded report with specific rewrites for any failing rule.

## When to use this skill

Use this skill any time the user wants to verify a SKILL.md will trigger correctly. Common triggers:

- "Test my skill description"
- "Will this skill fire on X?"
- "Grade my SKILL.md"
- "Audit this skill's triggers"
- "Is my description pushy enough?"
- "Why isn't my skill firing?"

It pairs naturally with `skill-scaffolder`: scaffold a skill, then test it here before shipping.

## How it works

### Step 1 — Accept input

The user can provide the skill in two ways. Accept either:

1. **A file path** — read `SKILL.md` from disk. The path may be relative or absolute. If the path is a directory, look for `SKILL.md` inside it.
2. **Pasted YAML frontmatter** — the `---\nname: ...\ndescription: ...\n---` block, or the full file contents.

Extract the `name` and `description` fields. If either is missing or malformed, stop and report a frontmatter error before proceeding.

### Step 2 — Auto-extract trigger phrases

Pull candidate trigger phrases out of the `description` field. Heuristics:

- Quoted strings (single or double quotes) — most common form
- Comma-separated fragments after `phrases like` or `phrases such as`
- Verb-first imperatives separated by `or` / `,`

List the auto-extracted phrases for the user.

### Step 3 — Ask for optional user phrases

Ask the user (one question at a time):

1. **Should-fire phrases** — "What additional phrases *should* trigger this skill? (5–10 ideal, can be 0)"
2. **Should-NOT-fire phrases** — "What phrases *should NOT* trigger this skill but might confusingly look like they should? (3–5 ideal, can be 0)"

If the user provides none, run with auto-extracted phrases only and note the limitation in the report.

### Step 4 — Run static rules

Apply every rule in `references/static-rules.md` to the description. Each rule returns pass / warn / fail with a one-line explanation. Do not silently skip rules.

### Step 5 — Run LLM-eval

For each test phrase (auto-extracted + user should-fire + user should-NOT-fire), follow the protocol in `references/llm-eval-protocol.md`:

1. Read only the frontmatter (simulating Claude's skill-selection context)
2. Reason about whether you would trigger the skill on this phrase
3. Rate: `definitely`, `probably`, `borderline`, `unlikely`, `definitely-not`
4. Give a one-line reason

Build a coverage matrix: rows are phrases, columns are the rating + reason.

### Step 6 — Score

Combine static-rule results and LLM-eval coverage per `references/scoring-rubric.md`. Output an overall grade (Pushy / OK / Weak / Won't Fire) plus per-axis scores (Form, Specificity, Coverage, Anti-Confusion).

### Step 7 — Report

Generate a structured report:

```
Skill: {name}
Overall grade: {grade}
Form: {score}
Specificity: {score}
Coverage: {score}
Anti-Confusion: {score}

Static rules:
  R1 ✓ ...
  R2 ✗ ...

LLM-eval coverage:
  Should-fire (extracted):
    "phrase" → rating — reason
  Should-fire (user):
    ...
  Should-NOT-fire:
    ...

Recommended rewrites:
  R{n}: {rewrite suggestion} — see references/rewrite-templates.md
```

For every failing or warning rule, pull a concrete rewrite from `references/rewrite-templates.md`. Do not invent generic advice — use the templates.

### Step 8 — Offer next steps

Conclude with:

- "Apply the rewrites and re-run me to compare"
- "If grade is C or worse, consider running this through the `skill-scaffolder` from scratch"
- "For automated eval loops with real Claude API calls, see Anthropic's `skill-creator`"

## Inputs

- A SKILL.md file path, **or**
- Pasted YAML frontmatter / full file contents
- Optional: user-supplied should-fire phrases (list)
- Optional: user-supplied should-NOT-fire phrases (list)

## Output

A graded markdown report with per-rule findings, a phrase-by-phrase coverage matrix, and specific rewrites for every failing rule.

## References

All analysis logic is in `references/`:

- `references/static-rules.md` — 9 deterministic checks
- `references/llm-eval-protocol.md` — how to reason about would-trigger
- `references/scoring-rubric.md` — grade calculation
- `references/rewrite-templates.md` — fix patterns per failure mode

Load these at execution time. Do not paraphrase from memory.

## Quality bar

A report from this skill must:

- Cite specific rule IDs, not vague impressions
- Give concrete rewrites pulled from templates, not generic advice
- Distinguish form failures (length, missing phrasing) from substance failures (vague triggers, hedging, low verb diversity)
- Flag negative-space coverage gaps (when "should-NOT-fire" phrases actually would fire)

## Iteration

This skill catches the structural and trigger-coverage issues. For automated eval loops with real model calls and large-scale phrase sampling, hand the skill off to Anthropic's [skill-creator](https://github.com/anthropics/claude-skills).
