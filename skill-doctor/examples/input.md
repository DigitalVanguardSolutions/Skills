# Example input

A typical run. The user has a skill they hand-wrote and wants to know if it's publish-ready.

---

**User:**

> Audit my skill at ~/.claude/skills/meeting-summarizer before I push it to GitHub.

---

## Skill Doctor lists the directory

```
~/.claude/skills/meeting-summarizer/
├── SKILL.md          (628 lines including frontmatter)
├── README.md
├── references/
│   ├── formats.md
│   └── old-notes.md
└── scripts/          (empty)
```

Observations the audit will make:

- No `LICENSE` file present
- No `examples/` directory
- `scripts/` exists but is empty
- `SKILL.md` body looks long
- `references/old-notes.md` — need to check if SKILL.md references it
- `README.md` — need to check tone and sections

## The SKILL.md frontmatter

```yaml
---
name: meeting-summarizer
description: Use this skill whenever the user wants to summarize a meeting transcript, extract action items, or generate meeting notes. Triggers on phrases like "summarize this meeting", "pull action items", "meeting notes from".
---
```

(Frontmatter is valid; name matches the directory; description is within length limits.)

## The README.md (excerpt)

```markdown
# Meeting Summarizer

Use this skill whenever the user wants to summarize a meeting transcript.
The skill triggers on phrases like "summarize this meeting" and instructs
Claude to read the transcript and produce notes.
```

(This reads like the SKILL.md — agent-oriented, no install or usage section.)

---

Skill Doctor now runs all 16 checks and produces the report in `output.md`.
