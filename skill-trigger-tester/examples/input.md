# Example input

A typical run. The user has a meeting-summarizer skill and wants to test whether its description will fire on the phrases they care about.

---

**User:**

> Test my skill at ~/.claude/skills/meeting-summarizer/SKILL.md. I want it to fire on "summarize the standup" and "what did we agree on yesterday" too. And it should NOT fire on "what's on my calendar" or "schedule a meeting".

---

## Tester reads the SKILL.md and extracts:

```yaml
---
name: meeting-summarizer
description: Sometimes summarizes meeting transcripts and processes content from calls when appropriate. Triggers on phrases like "summarize this", "summarize the meeting", "summarize the call".
---
```

## Auto-extracted phrases

The tester pulls three quoted phrases from the description:

1. `"summarize this"`
2. `"summarize the meeting"`
3. `"summarize the call"`

## User-supplied phrases

**Should fire:**
- `"summarize the standup"`
- `"what did we agree on yesterday"`

**Should NOT fire:**
- `"what's on my calendar"`
- `"schedule a meeting"`

## Confirmation

> Got it. Testing with 3 auto-extracted phrases, 2 user should-fire, and 2 should-NOT-fire. Running static rules and LLM-eval now.
