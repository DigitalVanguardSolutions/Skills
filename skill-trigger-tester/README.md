# Skill Trigger Tester

> Tests whether your Claude Code skill description is "pushy" enough to actually fire.

A Claude Code skill that grades an existing SKILL.md against the trigger-quality rules most hand-written skills fail.

## What it does

Anthropic's guidance is that Claude tends to *undertrigger* skills. Most skills fail not because the body is bad, but because the description is too tame — missing explicit trigger phrasing, hedging language, vague verbs, no negative-space coverage.

The Skill Trigger Tester:

- Reads your SKILL.md (file path or pasted frontmatter)
- Auto-extracts the trigger phrases you've already declared
- Lets you add "should fire" and "should NOT fire" test phrases
- Runs 9 static rules (pushiness, hedging, verb diversity, specificity, distinctness, etc.)
- Simulates trigger behavior on each phrase (no API key needed — Claude is already the LLM)
- Returns a graded report with concrete rewrites for every failing rule

## Why it exists

Building a skill is one thing; verifying it actually fires when you want it to is another. The `skill-scaffolder` (also in this repo) prevents the obvious mistakes at write-time. This tester catches the deeper issues — does your description cover the trigger space, or only the obvious phrases?

Common findings it surfaces:

- Hedging words ("sometimes", "if appropriate") that soften triggering
- Three "different" trigger phrases that are all paraphrases of the same intent
- Abstract nouns ("data", "content", "stuff") where concrete ones would help Claude select correctly
- Negative-space gaps — phrases that *would* fire your skill but shouldn't

## Installation

```bash
git clone https://github.com/digitalvanguardsolutions/skills.git ~/dvs-skills
cp -r ~/dvs-skills/skill-trigger-tester ~/.claude/skills/
```

## Usage

Once installed, just point Claude at the skill you want to test:

- "Test my skill at ~/.claude/skills/meeting-summarizer/SKILL.md"
- "Will this SKILL.md fire on 'summarize the standup'? [pastes frontmatter]"
- "Grade my contract-review skill"
- "Why isn't my skill firing on these phrases?"

See `examples/` for a full run with input and output.

## What you get back

```
Skill: meeting-summarizer
Overall grade: B (OK)
Form: 9/10
Specificity: 7/10
Coverage: 6/10
Anti-Confusion: 8/10

Static rules:
  R1 ✓ Has "Use this skill when..." opener
  R2 ✓ 287 chars (100–400 range)
  R3 ✓ 5 distinct trigger phrases
  R4 ✗ Hedging detected: "sometimes", "when relevant"
  ...

LLM-eval coverage:
  Should-fire (auto-extracted):
    "summarize this meeting" → definitely
    "pull action items" → probably
  Should-NOT-fire:
    "what's on my calendar" → unlikely (good)

Recommended rewrites:
  R4 — Remove "sometimes" and "when relevant" — replace with declarative phrasing
```

## Pairs with skill-scaffolder

```
1. scaffold a skill →  skill-scaffolder
2. test that it actually fires →  skill-trigger-tester
3. ship to users
```

If the tester returns C or worse, run the skill back through the scaffolder with a tightened description.

## Requirements

- Claude Code installed
- Read access to the SKILL.md you want to test

No external dependencies, no API keys, no scripts.

## Iterating further

For automated eval loops, large-scale phrase sampling, and trigger-accuracy benchmarks across thousands of test cases, use Anthropic's [skill-creator](https://github.com/anthropics/claude-skills). This tester is the fast manual check; skill-creator is the rigorous automated one.

## License

MIT — see `LICENSE`.

## About

Built by [Digital Vanguard Solutions](https://digitalvanguardsolutions.com) — practical AI implementation for mid-market.

Drop #2 in the [DVS Skill Drop Friday](https://github.com/digitalvanguardsolutions/skills) series — a weekly drop of free, production-grade Claude Code skills.
