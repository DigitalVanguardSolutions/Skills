# Example input

A typical interactive run. The user opens Claude Code and says:

> I want to create a new skill that summarizes meeting transcripts.

The Skill Scaffolder activates and walks through the intake. Below is the conversation that follows.

---

## Intake transcript

**Scaffolder:** Got it — a skill that turns meeting transcripts into summaries. A few questions to scaffold this properly. I'll go one at a time.

**Q1.** What should this skill do, in one line? (10–120 chars)

**User:** Summarizes a meeting transcript and extracts action items, decisions, and open questions.

---

**Scaffolder:** Good — concrete artifact (transcript), concrete output (summary + action items + decisions + open questions).

**Q2.** What should we call it? Suggested name based on the purpose: `meeting-summarizer`. Use that, or pick your own (kebab-case, 2–40 chars)?

**User:** meeting-summarizer

---

**Scaffolder:**

**Q3.** When should it trigger? Be specific — Claude tends to undertrigger skills, so the description needs explicit trigger phrases (3+) and an explicit "Use this skill when..." opener. Examples:

- Bad: "Summarizes meetings" (too vague, Claude won't fire on relevant requests)
- Good: "Use this skill whenever the user wants to summarize a meeting transcript, extract action items from a meeting, generate meeting notes, or pull decisions from a recording transcript. Triggers on phrases like 'summarize this meeting', 'pull action items from', 'meeting notes from', 'what did we decide in', 'recap the meeting'."

Paste your description, or hit enter for me to draft one.

**User:** Use this skill whenever the user wants to summarize a meeting transcript, extract action items from a meeting, generate meeting notes, or pull decisions from a recording transcript. Triggers on phrases like "summarize this meeting", "pull action items from", "meeting notes from", "what did we decide in", "recap the meeting".

---

**Scaffolder:**

**Q4.** What's the expected input?

**User:** A meeting transcript file (markdown or plain text), or pasted transcript text.

---

**Scaffolder:**

**Q5.** What's the expected output?

**User:** A markdown report with three sections: Action Items (with owners if mentioned), Decisions, Open Questions. Plus a one-paragraph summary at the top.

---

**Scaffolder:**

**Q6.** Does it need any of these folders?

- `scripts/` — for helper code. Suggested: NO (no automation keywords in the purpose).
- `references/` — for domain-specific knowledge. Suggested: NO (no vertical/framework keywords).
- `assets/` — for templates/branding. Suggested: NO.

Keep all defaults?

**User:** Yes, keep defaults.

---

**Scaffolder:**

**Q7.** Where should it install? Default: `~/.claude/skills/meeting-summarizer`.

**User:** Default is fine.

---

**Scaffolder:** Running validation against `references/validation-rules.md`...

- R1 (name): pass
- R2 (purpose): pass — concrete artifact and output
- R3 (description): pass — 432 chars, has "Use this skill whenever", 5 trigger phrases
- R4 (input): pass
- R5 (output): pass
- R6 (folders): all defaults to NO
- R7 (install path): writeable, doesn't exist yet

All checks passed. Generating now.
