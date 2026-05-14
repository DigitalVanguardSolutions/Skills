# Example output

The report the tester returns after the run described in `input.md`.

---

```
Skill: meeting-summarizer
Overall grade: D (Won't fire reliably — rebuild description)
Form:            6/10
Specificity:     5/10
Coverage:        3/10
Anti-Confusion:  2/10
Total:          16/40

Static rules:
  R1 ✓  Has "Triggers on phrases like" opener
  R2 ✓  187 chars (in 100–400 range)
  R3 ✓  3 quoted trigger phrases
  R4 ✗  Hedging detected: "sometimes", "when appropriate"
  R5 ⚠  Phrase "summarize this" is artifact-vague (no concrete noun)
  R6 ✗  Only 1 distinct verb across phrases: "summarize"
  R7 ⚠  3 phrases collapse to 1 distinct intent (all "summarize the meeting" paraphrases)
  R8 ⚠  Action concrete ("summarizes"), artifact partly concrete ("transcripts" + vague "content")
  R9 ✗  "processes content from calls" — generic verb "processes" with no domain qualifier

LLM-eval coverage:

  Should-fire (auto-extracted):
    "summarize this"             → probably    — direct verb match, vague object but description covers it
    "summarize the meeting"      → definitely  — exact quoted trigger
    "summarize the call"         → definitely  — exact quoted trigger

  Should-fire (user-supplied):
    "summarize the standup"      → probably    — verb match, "standup" is meeting-adjacent but not named
    "what did we agree on yesterday" → unlikely — no verb match, "agree" not in trigger surface, "yesterday" generic

  Should-NOT-fire (user-supplied):
    "what's on my calendar"      → unlikely    — no verb match (good)
    "schedule a meeting"         → borderline  — "meeting" appears in description, "schedule" doesn't match but
                                                  description's vagueness ("processes content") creates risk
                                                  ⚠ FALSE-POSITIVE RISK

  Hit rate (should-fire): 3/5 definitely-or-probably = 60%
  Miss rate: 1/5 unlikely = 20%
  False-positive rate: 1/2 borderline+ = 50%  ⚠ HIGH

Recommended rewrites:

  R4 — Remove hedging
    Before: "Sometimes summarizes meeting transcripts and processes content from calls when appropriate."
    After:  "Use this skill whenever the user wants to summarize a meeting transcript, extract action
            items from a call, or generate meeting notes."

  R6 — Diversify verbs
    Add phrases using extraction and generation verbs alongside synthesis:
      "summarize a standup", "extract action items", "generate meeting notes", "recap yesterday's meeting"

  R7 — Deduplicate intent
    Replace 3 paraphrases of "summarize the meeting" with 3 distinct intents:
      "summarize this meeting", "extract action items", "pull decisions from a transcript"

  R9 — Remove generic verbs
    Drop "processes content from calls". If the skill works on call recordings, name them directly:
      "summarize call recordings", "extract action items from a transcript".

Coverage gaps to address:
  - "what did we agree on yesterday" missed — add a phrase for retroactive recall:
      "what did we decide in", "what did we agree", "recap the agreement from"
  - "schedule a meeting" false-positive risk — tighten description so "meeting" only co-occurs with
    summarization verbs, not standalone.

Suggested rewrite (consolidated):
  description: Use this skill whenever the user wants to summarize a meeting transcript or call recording,
    extract action items from a meeting, pull decisions or agreements from a transcript, or generate
    meeting notes. Triggers on phrases like "summarize this meeting", "extract action items", "what did
    we agree on", "pull decisions from", "recap the standup", "generate meeting notes from".

Re-run the tester on the rewritten description to verify the grade.

---
Footer:
  - This rubric does not capture cross-skill collision risk. Run skill-creator for that.
  - Body quality is not scored — only the description.
  - LLM-eval is reasoning-based, not API-based. Results approximate real selector behavior; for
    benchmarking with real model calls, use Anthropic's skill-creator.
```
