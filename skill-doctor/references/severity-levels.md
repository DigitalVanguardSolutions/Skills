# Severity levels and verdict

Maps each checklist finding to a severity, then rolls all findings into one overall verdict.

---

## Severity mapping

| Check | Severity if failed | Rationale |
|---|---|---|
| D1 — SKILL.md present | 🔴 Error | No SKILL.md = nothing loads |
| D2 — README present | 🟡 Warning | Skill loads, but no human docs |
| D3 — LICENSE present | 🟡 Warning | Loads fine, but unpublishable without a license |
| D4 — examples populated | 🟡 Warning | Loads fine, but examples are part of the contract |
| D5 — no empty folders | 🟡 Warning | Confusing, violates Lack of Surprise |
| D6 — frontmatter well-formed | 🔴 Error | Malformed frontmatter = skill won't load |
| D7 — name + description present | 🔴 Error | Missing required keys = won't load |
| D8 — name matches directory | 🔴 Error | Mismatch breaks resolution |
| D9 — name kebab-case | 🟡 Warning | May load, but violates convention and risks resolution issues |
| D10 — description length | 🔴 Error if >1024; 🟡 Warning if <20 | Over hard limit = rejected; too short = effectively useless |
| D11 — body ≤ 500 lines | 🟡 Warning | Loads, but bloats context; move detail to references/ |
| D12 — no leftover placeholders | 🟡 Warning | Loads, but ships obviously-unfinished content |
| D13 — referenced files exist | 🔴 Error | Broken pointer; skill will fail when it tries to load the file |
| D14 — no orphaned references | 🔵 Info | Dead weight, not breaking |
| D15 — README human-oriented | 🔵 Info | Quality polish |
| D16 — README has install + usage | 🔵 Info | Quality polish |

---

## Overall verdict

Apply in order:

1. **BROKEN** — at least one 🔴 Error. The skill will not load correctly or fails Anthropic's hard requirements. Must fix before anything else.
2. **NEEDS WORK** — zero errors, but at least one 🟡 Warning. The skill loads but isn't publish-ready.
3. **HEALTHY** — zero errors, zero warnings. 🔵 Info findings are allowed — they don't downgrade a HEALTHY verdict.

Display the verdict prominently at the top of the report with the count of each severity:

```
Verdict: NEEDS WORK
  🔴 0 errors   🟡 3 warnings   🔵 1 info
```

---

## Ordering within the report

Within each severity group, order findings by check ID ascending (D1 → D16). This keeps structure issues (low IDs) above polish issues (high IDs) and makes reports comparable across runs.

---

## What the verdict does NOT mean

- **HEALTHY** means structurally sound — NOT that the description will trigger well (run `skill-trigger-tester`), NOT that the skill's logic is correct (only the author can verify that), NOT that the examples are accurate.
- **BROKEN** means a hard failure exists — it does not rank *how* broken. Fix all 🔴 errors, then re-run.

State this limitation in the report footer so the user calibrates.
