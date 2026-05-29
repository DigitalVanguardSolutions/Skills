# Example output

The report Skill Doctor returns for the directory in `input.md`.

---

```
Skill: meeting-summarizer  (~/.claude/skills/meeting-summarizer)
Verdict: NEEDS WORK
  🔴 0 errors   🟡 5 warnings   🔵 2 info

🟡 Warnings (5)

  [D3]  No LICENSE file
        Fix: add a LICENSE file. For MIT, copy the standard text with your
             name and year. Unpublishable without one.

  [D4]  examples/ directory missing
        Fix: create examples/ with input.md and output.md derived from the
             skill's stated input (a transcript) and output (notes + action items).

  [D5]  scripts/ exists but is empty
        Fix: either populate scripts/ with a README stub explaining what goes
             there, or delete the folder. Empty conditional folders are confusing.

  [D11] Body is 612 lines (over the 500-line guideline)
        Fix: move the long format tables into references/formats.md (which
             already exists) and reference them from the body.

  [D15] README reads like SKILL.md (agent-oriented)
        Fix: rewrite README for humans — second person, why-it-exists, install
             and usage sections. Trigger phrasing belongs in SKILL.md, not README.

🔵 Info (2)

  [D14] references/old-notes.md is never referenced in SKILL.md
        Fix: wire it in or delete it — currently dead weight.

  [D16] README missing an Installation section
        Fix: add how to copy the skill into ~/.claude/skills/.

Checks passed (9): D1, D2, D6, D7, D8, D9, D10, D12, D13

Directory tree:
  meeting-summarizer/
  ├── SKILL.md          (628 lines)
  ├── README.md
  ├── references/
  │   ├── formats.md
  │   └── old-notes.md  ← orphaned (D14)
  └── scripts/          ← empty (D5)

---
Footer:
  - Verdict reflects STRUCTURE only. It does not judge whether the description
    will actually trigger — run skill-trigger-tester for that.
  - It does not verify the skill's logic is correct, or that the examples are
    accurate. Only the author can confirm those.
```

## Why NEEDS WORK and not BROKEN

No 🔴 errors fired: SKILL.md exists (D1), frontmatter is valid (D6), required keys present (D7), name matches the directory (D8), and no references are broken (D13). The skill *loads* — it's just not publish-ready. Fix the 5 warnings and re-run; the verdict should flip to HEALTHY (the 🔵 info items don't block HEALTHY).

## What a BROKEN verdict would look like

If the frontmatter were missing its closing `---`, or `name:` were absent, or SKILL.md pointed at `references/missing.md` that didn't exist, those would be 🔴 errors and the verdict would be BROKEN — fix those first, before touching any warnings.
