# Skill Scaffolder

> A Claude Code skill that builds Claude Code skills.

Generates properly-structured skills from a short interactive intake — with quality validation that catches the mistakes most quick-built skills ship with.

## What it does

The Skill Scaffolder takes you from "I have an idea for a skill" to "I have a working skill directory" in under five minutes. It:

- Walks you through a structured intake (purpose, name, triggers, I/O, structure)
- Generates a complete skill directory with proper YAML frontmatter, separate SKILL.md and README.md, examples folder, and optional `scripts/` / `references/` / `assets/` folders
- Validates every input and rejects common mistakes — vague descriptions, kebab-case violations, missing trigger phrases, body length over Anthropic's 500-line guideline
- Outputs next steps for testing and iterating

## Why it exists

Vibe-coding a skill from a one-shot prompt produces inconsistent results. Anthropic's own guidance is that Claude *undertriggers* skills — meaning descriptions need to be deliberately "pushy" with explicit trigger phrases, not just job descriptions. Most hand-written SKILL.md files miss this and the resulting skills don't fire when they should.

This scaffolder bakes those best practices in by default.

## Installation

```bash
git clone https://github.com/digitalvanguardsolutions/skills.git ~/dvs-skills
cp -r ~/dvs-skills/skill-scaffolder ~/.claude/skills/
```

## Usage

Once installed, just tell Claude what you want to build:

- "Create a new skill that reviews security questionnaires"
- "Scaffold a skill for chunking documents for RAG"
- "I need a skill that estimates Bedrock costs"

The scaffolder will walk you through the intake and generate the skill directory.

## Example

```
You: I want a skill that summarizes meeting transcripts

Skill Scaffolder: Got it. Let me ask a few questions to scaffold this properly.

  Suggested name: meeting-transcript-summarizer
  Use this name? (y / suggest a different one)

You: y

Skill Scaffolder: When should this skill trigger? Be specific — Claude tends
  to undertrigger skills, so we want explicit trigger phrases. Examples:

  Bad:  "Summarizes meeting transcripts" (too vague)
  Good: "Use this skill whenever the user wants to summarize a meeting
        transcript, extract action items from a meeting, or generate
        meeting notes. Triggers on phrases like 'summarize this meeting',
        'pull action items from', 'meeting notes from'."

  Paste your description, or hit enter for me to draft one based on the name.
```

See `examples/input.md` and `examples/output.md` for a full sample run.

## What it generates

```
your-new-skill/
├── SKILL.md          # The instructions Claude reads
├── README.md         # The thing humans read on GitHub
├── LICENSE           # MIT by default
└── examples/
    ├── input.md
    └── output.md
```

Plus optional `scripts/`, `references/`, or `assets/` folders if your skill needs them.

## Iterating further

Once you have a draft, Anthropic's skill-creator skill will help you build evals, test trigger accuracy, and optimize the description through automated iteration. Use this scaffolder for the first pass; use skill-creator for the optimization loop.

## Requirements

- Claude Code installed
- Write access to your target install path (default `~/.claude/skills/`)

No external dependencies, no scripts, no API keys.

## License

MIT — see `LICENSE`.

## About

Built by [Digital Vanguard Solutions](https://digitalvanguardsolutions.com) — practical AI implementation for mid-market.

Part of the [DVS Skill Drop Friday](https://github.com/digitalvanguardsolutions/skills) series — a weekly drop of free, production-grade Claude Code skills.
