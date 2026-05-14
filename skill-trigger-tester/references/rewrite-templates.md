# Rewrite templates

For every failing or warning static rule, pull a concrete rewrite from below. Do not invent generic advice — use the templates and adapt them to the user's specific description.

## R1 — Add explicit trigger phrasing

**Symptom:** description has no "Use this skill when..." or equivalent opener.

**Template:** Prepend the description with one of:

> `Use this skill whenever the user wants to {primary-action} {primary-artifact}, or {secondary-action}. Triggers on phrases like "{phrase-1}", "{phrase-2}", "{phrase-3}".`

**Concrete example:**

- Before: `Summarizes meetings.`
- After: `Use this skill whenever the user wants to summarize a meeting transcript, extract action items, or generate meeting notes. Triggers on phrases like "summarize this meeting", "pull action items from", "meeting notes from".`

---

## R2 — Adjust description length

**If too short (<100):** add 2–3 more trigger phrases and a second purpose clause.

**If too long (>400):**

- Cut paraphrased trigger phrases (R7 will already flag these)
- Cut hedging clauses ("when applicable", "if relevant")
- Move detailed scope to the body, keep only the trigger surface in the description

**Template for trimming:** keep `{trigger-opener}` + 3–5 phrases + 1 closing sentence on action+artifact. Drop the rest.

---

## R3 — Add or consolidate trigger phrases

**If fewer than 3 phrases:** add concrete phrases the user might say. Source them from:

- Verb variations on the same intent
- Different artifact types (file, paste, URL)
- Different framings ("summarize" vs "give me the key points")

**Template:** `Triggers on phrases like "{verb-1} {artifact}", "{verb-2} {artifact}", "{paraphrase}".`

**If 9+ phrases (R3 warn):** check for paraphrase clusters and keep the strongest one from each.

---

## R4 — Remove hedging

**Symptom:** description contains `sometimes`, `if appropriate`, `when relevant`, `maybe`, etc.

**Fix:** delete the hedging word and rewrite the surrounding clause as a declarative statement.

**Template transforms:**

- `"Sometimes triggers on X"` → `"Triggers on X"`
- `"Use this skill when relevant for Y"` → `"Use this skill whenever the user wants to Y"`
- `"If the user is asking about Z, this may help"` → `"Use this skill whenever the user asks about Z"`

The selector treats hedging as "skip when unsure." Declarative language flips that.

---

## R5 — Replace abstract triggers with concrete artifacts

**Symptom:** trigger phrases use generic nouns like `data`, `content`, `stuff`, `items`.

**Fix:** name the actual artifact.

**Template transform:** `"summarize the content"` → `"summarize the {transcript|article|email thread|PDF}"`

If the skill genuinely works on multiple artifact types, list them: `"summarize the transcript, article, or email thread"`.

---

## R6 — Diversify trigger verbs

**Symptom:** all trigger phrases use the same verb (e.g., all start with "summarize").

**Fix:** add phrases using related but distinct verbs.

**Common verb families** (pick 2–3 per skill):

| Family | Verbs |
|---|---|
| Synthesis | `summarize`, `recap`, `digest`, `condense` |
| Extraction | `extract`, `pull`, `list`, `identify` |
| Generation | `generate`, `create`, `draft`, `write` |
| Analysis | `analyze`, `evaluate`, `audit`, `review` |
| Transformation | `convert`, `transform`, `restructure`, `reformat` |
| Comparison | `compare`, `diff`, `contrast`, `match` |

Use verbs from at least 2 families if the skill genuinely covers more than one.

---

## R7 — Deduplicate paraphrase clusters

**Symptom:** "different" trigger phrases are paraphrases of the same intent (e.g., `"create a skill"`, `"make a skill"`, `"build a skill"`).

**Fix:** keep the strongest phrasing from each intent cluster, then add new phrases that cover *different* intents.

**Template:**

- Before: `"create a new skill", "make a new skill", "build a new skill"` (1 intent)
- After: `"create a new skill", "scaffold a SKILL.md", "convert a workflow into a skill"` (3 intents)

---

## R8 — Make action + artifact both concrete

**Symptom:** action is clear but artifact is vague, or vice versa.

**Fix:** ensure both halves of the description name something specific.

**Template transforms:**

- Action vague: `"handles meeting transcripts"` → `"summarizes meeting transcripts"`
- Artifact vague: `"summarizes content"` → `"summarizes meeting transcripts and call recordings"`
- Both vague: `"works on documents"` → `"redacts PII from vendor contracts"`

---

## R9 — Add domain qualifier to generic verbs

**Symptom:** description uses `analyze`, `process`, `help with`, `assist with` without a qualifier.

**Fix:** every `analyze` / `process` needs a specific object.

**Template transform:**

- `"analyze data"` → `"analyze AWS Bedrock invocation logs for cost outliers"`
- `"process documents"` → `"extract clauses from vendor security questionnaires"`
- `"help with prompts"` → `"audit Claude prompts for clarity and prompt-cache friendliness"`

`Help with` and `assist with` rarely earn their place. Replace with the actual verb whenever possible.

---

## Closing note

Show the user the rewrite *next to* their original phrasing, so they can see what changed. After rewrites are applied, suggest re-running the trigger tester to confirm the new grade.
