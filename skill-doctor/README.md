# Skill Doctor

> Audits a Claude Code skill directory for the structural problems that break loading or embarrass you on GitHub.

A Claude Code skill that scans an entire skill directory — files, folders, frontmatter, references — and returns a severity-ranked report with a concrete fix for every finding.

## What it does

The trigger description is one way a skill fails. The other is structure: a missing LICENSE, an empty `references/` folder, a SKILL.md that points at a file that doesn't exist, leftover `{{placeholders}}` from scaffolding, a body that blew past 500 lines.

Skill Doctor catches all of that. It runs 16 checks across four groups:

- **Structure** — required files present, no empty conditional folders
- **Frontmatter** — valid YAML, required keys, name matches directory
- **Body** — under 500 lines, no leftover scaffolding placeholders
- **Reference integrity** — every file SKILL.md points to actually exists

Each finding gets a severity (🔴 error / 🟡 warning / 🔵 info) and a fix. The whole skill rolls up into one verdict: **BROKEN**, **NEEDS WORK**, or **HEALTHY**.

## Why it exists

Skills fail to load for boring reasons — a malformed frontmatter block, a case-wrong filename, a broken reference path. Those failures are silent and frustrating. And even skills that *load* often aren't publish-ready: no license, no examples, a README that's just a copy of the SKILL.md.

Skill Doctor is the pre-flight check. Run it before you publish.

## The meta-skills trilogy

```
1. build a skill           → skill-scaffolder
2. test it triggers        → skill-trigger-tester
3. audit it's sound        → skill-doctor   ← you are here
```

Doctor deliberately does NOT judge trigger quality — that's trigger-tester's job. It does a light "is the description present and within limits" check, then hands off. The full pre-publish pass is: Doctor (structure) → Trigger Tester (description) → ship.

## Installation

```bash
git clone https://github.com/digitalvanguardsolutions/skills.git ~/dvs-skills
cp -r ~/dvs-skills/skill-doctor ~/.claude/skills/
```

## Usage

Point Claude at a skill directory:

- "Audit my skill at ~/.claude/skills/meeting-summarizer"
- "Check this skill directory for problems"
- "Lint my SKILL.md"
- "Why won't my skill load?"
- "Health-check my skill before I publish"

See `examples/input.md` and `examples/output.md` for a full run.

## What you get back

```
Skill: meeting-summarizer  (~/.claude/skills/meeting-summarizer)
Verdict: NEEDS WORK
  🔴 0 errors   🟡 3 warnings   🔵 1 info

🟡 Warnings (3)
  [D3]  No LICENSE file
        Fix: add a LICENSE (MIT text in the playbook)
  [D4]  examples/ is empty
        Fix: add input.md and output.md derived from the skill's I/O
  [D11] Body is 612 lines (over 500)
        Fix: move the rules table into references/rules.md

🔵 Info (1)
  [D14] references/old-notes.md is never referenced in SKILL.md

For deep trigger analysis, run skill-trigger-tester on this skill.
```

## Requirements

- Claude Code installed
- Read access to the skill directory you want to audit

No external dependencies, no API keys, no scripts.

## License

MIT — see `LICENSE`.

## About

Built by [Digital Vanguard Solutions](https://digitalvanguardsolutions.com) — practical AI implementation for mid-market.

Drop #4 in the [DVS Skill Drop Friday](https://github.com/digitalvanguardsolutions/skills) series.
