---
name: skill-scaffolder
description: Use this skill whenever the user wants to create a new Claude Code skill, scaffold a SKILL.md, or convert a workflow into a skill. Triggers on phrases like "create a new skill", "scaffold a skill", "build a Claude skill", "make a SKILL.md", or "turn this into a skill". Runs an interactive intake and generates a validated skill directory following Anthropic's conventions.
---

# Skill Scaffolder

Generates properly-structured Claude Code skills from a short interactive intake. Enforces Anthropic's skill conventions and rejects common quality issues before they ship.

## When to use this skill

Use this skill any time the user expresses intent to create, scaffold, or start a new Claude Code skill. Common triggers include:

- "I want to make a skill that..."
- "Create a new skill..."
- "Scaffold a skill for..."
- "Turn this into a skill"
- "I need a skill"
- "Build me a SKILL.md"

This skill also activates when a user has just walked through a workflow conversationally and wants to capture it as a reusable skill.

## How it works

### Step 1 — Capture intent

Confirm the user's intent in a single sentence. If the conversation already contains a workflow they want to capture, extract details from history first: candidate names, the problem being solved, observed inputs/outputs.

### Step 2 — Run the intake

Ask these questions one at a time. Don't dump them all at once.

1. **What should this skill do?** (one-line purpose, 10–120 chars)
2. **What should we call it?** (kebab-case; suggest one based on the purpose)
3. **When should it trigger?** (the trigger description; provide examples of good vs vague — see `references/validation-rules.md`)
4. **What's the expected input?** (e.g., a contract file, a directory of docs, a freeform description)
5. **What's the expected output?** (e.g., a markdown report, a generated file, a structured table)
6. **Does it need scripts, references, or assets?** (suggest defaults based on purpose; see the conditional-folder defaults in `references/validation-rules.md`)
7. **Where should it install?** (default `~/.claude/skills/{name}`)

### Step 3 — Validate

Before generating files, apply every rule in `references/validation-rules.md`. Surface failures, explain *why* they matter, and offer concrete fixes. **Never silently autocorrect** — that defeats the educational value.

If a validation failure is borderline (e.g., description is 95 chars when minimum is 100), still flag it — let the user choose to bypass.

### Step 4 — Generate

Create the directory structure:

```
{skill-name}/
├── SKILL.md
├── README.md
├── LICENSE
└── examples/
    ├── input.md
    └── output.md
```

Add `scripts/`, `references/`, or `assets/` only if the user said yes. Each conditional folder gets a `README.md` stub explaining what it is for. Do not create empty conditional folders.

Use the templates in `references/`:

- `references/skill-template.md` — parameterized SKILL.md
- `references/readme-template.md` — parameterized README.md
- `references/license-mit.md` — MIT license text

Substitute `{{variables}}` from intake answers. Strip any conditional block whose flag is false (see template comments for block markers).

### Step 5 — Report

After generating, show the user:

- The created file tree
- The first 20 lines of the generated SKILL.md
- Three example trigger phrases they can test with
- Next steps:
  - How to install (`cp -r {skill-name} ~/.claude/skills/`)
  - How to test (start a new Claude Code session and try one of the trigger phrases)
  - When to reach for Anthropic's `skill-creator` to run the eval loop for further refinement

## Validation rules

The complete validation logic lives in `references/validation-rules.md`. Read that file before running step 3. The summary:

- **Skill name** — kebab-case, lowercase, alphanumeric + hyphens, 2–40 chars
- **One-line purpose** — 10–120 chars; not vague
- **Trigger description** — 100–400 chars; explicit "Use this skill when..." or "Triggers on..." phrasing; at least 3 specific trigger phrases
- **Install path** — writeable
- **Generated SKILL.md body** — under 500 lines

## Templates

All output templates are reference files, not embedded in this SKILL.md:

- `references/skill-template.md`
- `references/readme-template.md`
- `references/license-mit.md`

Load them at generation time. Do not paraphrase from memory.

## Quality bar

A skill produced by this scaffolder must:

- Pass Anthropic's skill conventions (proper frontmatter, body under 500 lines)
- Have a description "pushy" enough to actually trigger when relevant
- Have a separate README.md aimed at humans, not agents
- Have working examples in `examples/`
- Be publishable to GitHub as-is, with no embarrassments

If the generated skill doesn't meet that bar, the scaffolder failed.

## Iteration

For first-pass scaffolding, this skill is sufficient. For trigger-accuracy tuning and description optimization, hand the generated skill off to Anthropic's [skill-creator](https://github.com/anthropics/claude-skills) — it provides an eval loop this scaffolder intentionally doesn't replicate.
