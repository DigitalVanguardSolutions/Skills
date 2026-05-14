# LLM-eval protocol

This protocol simulates Claude's skill-selection behavior on a phrase. Since this skill runs *inside* Claude Code, Claude is already the LLM — no external API call needed. The "simulation" is a structured reasoning step.

## Setup

Before evaluating any phrase, **isolate context**: ignore the body of the SKILL.md, the file path, the conversation history. Use only the YAML frontmatter (`name` + `description`). This mirrors what Claude's selector actually sees.

## Per-phrase procedure

For each test phrase, run this exact sequence:

1. **Read the description in isolation.** Hold it in mind as the *only* signal about what the skill does.

2. **Read the test phrase.** Take it at face value — assume the user said exactly this and nothing else.

3. **Ask the trigger question:** *"If a user said this phrase in a fresh conversation, and the only skill available was the one described above, would I — Claude — trigger this skill?"*

4. **Reason briefly.** What in the description matches the phrase? What doesn't? Is the match on a quoted trigger phrase, a verb, a domain noun, or general semantic alignment?

5. **Rate** on this 5-point scale:

   | Rating | Meaning |
   |---|---|
   | `definitely` | Phrase is a direct match for a quoted trigger or close paraphrase. Would fire every time. |
   | `probably` | Strong semantic match — same verb family and same domain. Would fire most times. |
   | `borderline` | Some match (verb or domain, not both). Would fire ~50% of the time. |
   | `unlikely` | Weak match — adjacent topic, no verb alignment. Would fire occasionally. |
   | `definitely-not` | No meaningful match. Would not fire. |

6. **One-line reason.** Cite the specific evidence: "matches quoted trigger 'X'", "verb 'summarize' aligns but artifact 'email' is outside described scope", "no overlap with stated domain (legal contracts)".

## Aggregate behavior

After scoring all phrases, build the coverage matrix:

```
Should-fire (auto-extracted from description):
  "{phrase}" → {rating} — {reason}
  ...

Should-fire (user-supplied):
  "{phrase}" → {rating} — {reason}
  ...

Should-NOT-fire (user-supplied):
  "{phrase}" → {rating} — {reason}
  ...
```

## Coverage signals

After the matrix is built, compute:

- **Hit rate** = % of should-fire phrases rated `definitely` or `probably`
- **Miss rate** = % of should-fire phrases rated `unlikely` or `definitely-not`
- **False-positive rate** = % of should-NOT-fire phrases rated `probably` or `definitely`
- **Borderline ambiguity** = % of all phrases rated `borderline`

Pass these to `references/scoring-rubric.md`.

## Anti-bias guardrails

When simulating, the model running this skill must *not*:

- Let knowledge of the skill's *body* leak into the trigger decision — only the frontmatter is in scope for selection
- Be charitable to weak descriptions because the body is good
- Be harsh on strong descriptions because of unrelated body issues
- Use the conversation history to infer intent — phrases are evaluated standalone

If a phrase rating feels like it's drawing on anything beyond the description + phrase, re-read the description and re-rate.

## What this protocol is NOT

- **Not** a real eval loop with real API calls — that's what Anthropic's `skill-creator` is for
- **Not** a benchmark across thousands of phrases — that requires automated tooling
- **Not** a guarantee of behavior at runtime — selection depends on context, other installed skills, and Claude version

It IS: a structured, reproducible heuristic that catches the obvious trigger gaps before shipping.
