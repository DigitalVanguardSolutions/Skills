# README.md template

Substitute `{{variables}}` with intake answers. The README is human-facing — write in a tone aimed at a developer browsing GitHub, not at Claude.

---

```markdown
# {{skill-display-name}}

> {{one-line-purpose}}

A Claude Code skill that {{purpose-rephrased-for-humans}}.

## What it does

{{expanded-description-of-purpose}}

## Installation

```bash
# Clone or copy this skill into your Claude Code skills folder
cp -r {{skill-name}} ~/.claude/skills/
```

Or, if you use the `dvs-skills` repository:

```bash
cd ~/.claude/skills
git clone https://github.com/digitalvanguardsolutions/skills.git dvs
ln -s dvs/{{skill-name}} {{skill-name}}
```

## Usage

Once installed, Claude Code will auto-discover this skill. Trigger it by saying things like:

- "{{example-trigger-1}}"
- "{{example-trigger-2}}"
- "{{example-trigger-3}}"

See `examples/` for sample inputs and outputs.

## Requirements

{{requirements-list-or-none}}

## License

MIT — see `LICENSE`.

## About

Built by [Digital Vanguard Solutions](https://digitalvanguardsolutions.com) — practical AI implementation for mid-market.

Part of the [DVS Skill Drop Friday](https://github.com/digitalvanguardsolutions/skills) series.
```

---

## Variable derivation rules

| Variable | How to derive |
|---|---|
| `{{skill-name}}` | Direct from intake |
| `{{skill-display-name}}` | Title Case from `{{skill-name}}` |
| `{{one-line-purpose}}` | Direct from intake |
| `{{purpose-rephrased-for-humans}}` | The purpose recast as a relative clause completing the sentence "A Claude Code skill that ...". Strip leading verbs that already imply the subject. |
| `{{expanded-description-of-purpose}}` | 2–4 sentences expanding the purpose. Mention the trigger context, the input, and the output. Aim at a developer skimming the README. |
| `{{example-trigger-1..3}}` | Same three trigger phrases used in SKILL.md (consistency matters) |
| `{{requirements-list-or-none}}` | Bulleted list, or the literal text "None — no external dependencies." |

## Tone notes

- README.md is for humans. Use second person ("you"), avoid agent-oriented phrasing like "this skill instructs Claude to...".
- Keep it scannable: short paragraphs, bullets over prose, code blocks for commands.
- Don't repeat the trigger description verbatim from SKILL.md — paraphrase it for readability.
