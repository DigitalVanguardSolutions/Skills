# Example output

Continuing from `input.md`. The Scaffolder generates the directory and reports back.

---

## Generated tree

```
~/.claude/skills/meeting-summarizer/
├── SKILL.md
├── README.md
├── LICENSE
└── examples/
    ├── input.md
    └── output.md
```

No `scripts/`, `references/`, or `assets/` folders — the user said no, so they aren't created (Principle of Lack of Surprise).

## SKILL.md (first 20 lines)

```markdown
---
name: meeting-summarizer
description: Use this skill whenever the user wants to summarize a meeting transcript, extract action items from a meeting, generate meeting notes, or pull decisions from a recording transcript. Triggers on phrases like "summarize this meeting", "pull action items from", "meeting notes from", "what did we decide in", "recap the meeting".
---

# Meeting Summarizer

Summarizes a meeting transcript and extracts action items, decisions, and open questions.

## When to use this skill

Use this skill whenever the user has a meeting transcript and wants a structured recap — action items, decisions, and open questions. It also activates when the user pastes raw transcript text into a session and asks for a summary or notes.

Common triggers:

- "summarize this meeting"
- "pull action items from"
- "what did we decide in"

## How it works
```

## Test triggers

Try these phrases in a fresh Claude Code session to confirm the skill fires:

1. "Summarize this meeting transcript: [paste]"
2. "Pull action items from yesterday's standup notes"
3. "What did we decide in the Q3 planning meeting? Here's the transcript: [paste]"

If any of these don't fire, run the skill back through the scaffolder with a tightened description.

## Next steps

- **Install:** Already at `~/.claude/skills/meeting-summarizer/`. Restart Claude Code if it was running.
- **Test:** Open a new session, paste a transcript, try one of the trigger phrases above.
- **Iterate:** If trigger accuracy is off (firing too often or not enough), run the skill through Anthropic's `skill-creator` for the eval loop.
- **Commit:** If you're tracking your skills in a repo, `cd ~/.claude/skills/meeting-summarizer && git init && git add . && git commit -m 'feat: initial scaffold'`.

## Validation summary

All 12 validation rules passed. Skill is ready to ship.
