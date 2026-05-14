# Scoring rubric

Combines static-rule results and LLM-eval coverage into four per-axis scores plus an overall grade.

## Per-axis scores

Each axis is scored 0–10. Round to integer.

### Form (max 10)

Drawn from: R1, R2.

| Static signal | Points |
|---|---|
| R1 pass (explicit trigger phrasing present) | +5 |
| R2 pass (length 100–400) | +5 |
| R2 warn (length 401–600) | +2 |
| R2 fail (length <100 or >600) | +0 |

### Specificity (max 10)

Drawn from: R5, R8.

| Static signal | Points |
|---|---|
| R5 pass (all phrases concrete) | +5 |
| R5 warn (one abstract-only phrase) | +3 |
| R5 fail (majority abstract) | +0 |
| R8 pass (action + artifact both concrete) | +5 |
| R8 warn (one half vague) | +3 |
| R8 fail (both halves vague) | +0 |

### Coverage (max 10)

Drawn from: R3, R6, R7, and LLM-eval hit rate.

| Signal | Points |
|---|---|
| R3 pass (3+ phrases) | +2 |
| R3 warn (9+ phrases, paraphrase-heavy) | +1 |
| R3 fail (0–2 phrases) | +0 |
| R6 pass (3+ verbs) | +2 |
| R6 warn (2 verbs) | +1 |
| R7 pass (intents distinct) | +1 |
| Hit rate ≥ 80% | +5 |
| Hit rate 60–79% | +3 |
| Hit rate 40–59% | +1 |
| Hit rate < 40% | +0 |

### Anti-Confusion (max 10)

Drawn from: R4, R9, and LLM-eval false-positive rate.

| Signal | Points |
|---|---|
| R4 pass (no hedging) | +3 |
| R9 pass (no generic conflict phrases) | +3 |
| R9 warn | +1 |
| False-positive rate 0% | +4 |
| False-positive rate 1–10% | +3 |
| False-positive rate 11–25% | +1 |
| False-positive rate > 25% | +0 |

If no should-NOT-fire phrases were provided, mark Anti-Confusion as `(no negative-space data — supply should-NOT-fire phrases for full score)` and award partial credit based on R4 + R9 only (max 6/10).

## Overall grade

Sum the four axes (max 40). Then:

| Total | Grade | Label |
|---|---|---|
| 34–40 | A | Pushy — ships clean |
| 27–33 | B | OK — minor rewrites recommended |
| 20–26 | C | Weak — significant rewrites needed |
| 13–19 | D | Won't fire reliably — rebuild description |
| 0–12 | F | Won't fire — start from scratch with skill-scaffolder |

## Report formatting

In the final report:

```
Overall grade: {letter} ({label})
Form:           {score}/10
Specificity:    {score}/10
Coverage:       {score}/10
Anti-Confusion: {score}/10
```

Always cite the rule IDs that contributed to each axis when explaining a score. Never just say "your specificity is low" — say "R5 fail (2 of 4 phrases use abstract nouns only) dropped Specificity by 5 points."

## What this rubric does NOT capture

- **Real-world contention** — if the user has 50 skills installed and this one's trigger phrases collide with another, this rubric won't catch it. Use Anthropic's `skill-creator` for cross-skill eval.
- **Domain truth** — the rubric trusts the user's should-fire / should-NOT-fire labels. If the user mislabels phrases, the score is wrong.
- **Body quality** — only the description is scored. A 5000-line bloated body still gets a perfect Form score if the description is good.

Surface these limitations in the report's footer so the user calibrates.
