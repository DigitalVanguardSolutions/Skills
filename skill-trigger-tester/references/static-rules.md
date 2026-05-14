# Static rules

Nine deterministic checks against a SKILL.md's `description` field. Apply every rule. Each returns `pass`, `warn`, or `fail` with a one-line explanation citing the specific evidence from the description.

Surface failures with the rule ID. Do not silently skip rules.

---

## R1 — Explicit trigger phrasing

**Check:** Description contains at least one of:

- `Use this skill when`
- `Use this skill whenever`
- `Triggers on`
- `Trigger this skill when`
- `Activate when`
- `Activate whenever`

**Severity:** `fail` if none present. This is the single highest-leverage signal for Claude's skill selector.

**Evidence to cite:** the matched phrase, or "no explicit trigger phrasing found".

---

## R2 — Description length

**Check:** 100–400 characters (Anthropic's hard limit is 1024; this is the quality bar).

- `< 100` → `fail` — too short to communicate trigger space
- `100–400` → `pass`
- `401–600` → `warn` — over the recommended limit but workable
- `> 600` → `fail` — Claude truncates context; key triggers may be missed

**Evidence to cite:** exact char count.

---

## R3 — Trigger phrase count

**Check:** Count distinct trigger phrases in the description. Heuristics:

- Quoted strings (`"..."` or `'...'`)
- Comma-separated fragments after `phrases like` or `phrases such as`
- Sentence-fragment imperatives separated by `or` / `,`

**Severity:**

- `0–2 phrases` → `fail`
- `3–4 phrases` → `pass`
- `5–8 phrases` → `pass`
- `9+ phrases` → `warn` — descriptions this dense often paraphrase the same intent

**Evidence to cite:** count and list the phrases.

---

## R4 — Hedging language

**Check:** Description does NOT contain hedging words:

- `sometimes`
- `maybe`
- `perhaps`
- `occasionally`
- `if appropriate`
- `when relevant`
- `if needed`
- `as needed`
- `if applicable`

**Severity:** `fail` if any hedging word present. Hedging tells the selector "skip me when unsure" — exactly what causes undertriggering.

**Evidence to cite:** the hedging word and its surrounding clause.

---

## R5 — Trigger phrase specificity

**Check:** Each trigger phrase from R3 must contain at least one concrete noun (a domain artifact, file type, or named entity), not only abstract nouns.

Abstract noun list (flag if a phrase contains only these): `thing`, `things`, `stuff`, `data`, `content`, `text`, `info`, `information`, `item`, `items`.

Concrete examples that pass: `meeting transcript`, `PDF`, `security questionnaire`, `Bedrock cost`, `SKILL.md`, `vendor contract`.

**Severity:**

- All phrases concrete → `pass`
- 1+ abstract-only phrase → `warn`
- Majority abstract-only → `fail`

**Evidence to cite:** the abstract phrase(s).

---

## R6 — Verb diversity

**Check:** Across all trigger phrases, count distinct *primary verbs* (the action verb at the start of each phrase). Treat synonyms as the same verb for this check — `create / make / build / scaffold` all count once.

**Severity:**

- 1 distinct verb → `fail` — Claude only fires on the one phrasing the author thought of
- 2 distinct verbs → `warn`
- 3+ distinct verbs → `pass`

**Evidence to cite:** list of verbs (e.g., `summarize, extract, generate`).

---

## R7 — Trigger phrase distinctness

**Check:** Quoted trigger phrases should differ in meaning, not just wording.

Method:

1. For each pair of phrases, ask: would Claude treat these as the same intent?
2. Phrases that share the same verb AND same object are duplicates (e.g., `"create a skill"` and `"make a skill"`).
3. Count distinct intents, not raw phrase count.

**Severity:**

- `distinct intents >= ceil(raw count / 2)` → `pass`
- `distinct intents < ceil(raw count / 2)` → `warn` — looks varied but isn't

**Evidence to cite:** group the phrases by intent and show the groups.

---

## R8 — Self-reference clarity

**Check:** Description says what the skill *does* (action) AND what it operates on (artifact). Two halves must be present.

Failure patterns:

- Action only, no artifact: "summarizes things"
- Artifact only, no action: "for meeting transcripts and notes"
- Vague action + vague artifact: "handles documents"

**Severity:**

- Both halves clear and concrete → `pass`
- One half vague → `warn`
- Both halves vague → `fail`

**Evidence to cite:** quote the verb and the object noun.

---

## R9 — Anti-conflict phrasing

**Check:** Description does NOT use generic phrases that would collide with other common skills:

- `analyze` without a domain qualifier
- `process` without a domain qualifier
- `help with` (always vague)
- `assist with` (always vague)
- `work on` (always vague)

**Severity:**

- No generic conflict-prone phrasing → `pass`
- 1 occurrence with no qualifier → `warn`
- 2+ occurrences → `fail`

**Evidence to cite:** the conflict-prone phrase and absence of qualifier.

---

## Evaluation order

Run R1 → R9 in order. Do not stop on the first failure — collect all results and surface them together.

For each rule, output one line:

```
R{n} {✓|⚠|✗} {one-line explanation citing evidence}
```

Pass these results to `references/scoring-rubric.md` for the final grade.
