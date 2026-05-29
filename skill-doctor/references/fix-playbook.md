# Fix playbook

The concrete remediation for each check. When a finding fails, pull its fix from here — do not invent generic advice.

---

## D1 — SKILL.md missing

Create a `SKILL.md` at the skill root with valid frontmatter:

```yaml
---
name: your-skill-name
description: Use this skill whenever ...
---
```

If the file exists but is misnamed (`skill.md`, `Skill.md`), rename it to exactly `SKILL.md` — the loader is case-sensitive.

If you're starting from scratch, run `skill-scaffolder` instead of hand-writing it.

---

## D2 — README missing or empty

Add a `README.md` aimed at a human browsing GitHub. Minimum sections: a one-line summary, What it does, Installation, Usage, License. See `skill-scaffolder`'s readme-template for the shape. Do NOT copy the SKILL.md content into it (see D15).

---

## D3 — LICENSE missing

Add a `LICENSE` file. For MIT:

```
MIT License

Copyright (c) {year} {holder}

Permission is hereby granted, free of charge, ...
```

For a non-MIT license, fetch the canonical text from <https://spdx.org/licenses/>. Don't hand-write license text.

---

## D4 — examples/ missing or empty

Create `examples/` with at least `input.md` (a sample input the skill accepts) and `output.md` (the expected output). Derive them from the skill's stated input/output. Empty example files don't count — populate them.

---

## D5 — empty conditional folder

Either populate the folder with a `README.md` stub explaining what it's for, or delete it. An empty `scripts/` / `references/` / `assets/` is worse than no folder.

---

## D6 — malformed frontmatter

Frontmatter must open with `---` on line 1 and close with `---`, with valid YAML between:

```yaml
---
name: my-skill
description: ...
---
```

Common breakers: missing closing `---`, a `:` inside an unquoted value, tabs instead of spaces, smart quotes pasted from a doc.

---

## D7 — missing name or description

Add the missing key. Both are required:

```yaml
name: my-skill
description: Use this skill whenever the user wants to ...
```

---

## D8 — name does not match directory

Rename one to match the other. Easiest: set `name:` to the directory name. If you rename the directory instead, keep it kebab-case.

---

## D9 — name not kebab-case

Convert to lowercase, replace spaces/underscores with hyphens, strip invalid characters: `My_Skill` → `my-skill`. Then re-check D8 (the directory must match).

---

## D10 — description length

- **Over 1024 chars:** cut paraphrased trigger phrases and any body-level detail that crept into the description. The description is a trigger surface, not documentation.
- **Under 20 chars:** it's effectively empty. Write a real description — and to make it *pushy*, run the result through `skill-trigger-tester`.

---

## D11 — body over 500 lines

Move procedural detail, rule lists, long tables, and templates out of SKILL.md into `references/{topic}.md`, then point at them from the body. SKILL.md should orchestrate; references should hold the depth. Re-count after moving.

---

## D12 — leftover placeholders

Search the body for `{{`, `<!-- IF:`, `TODO`, `FIXME`, `PLACEHOLDER`, `Lorem ipsum` and resolve each:

- `{{variable}}` → substitute the real value
- `<!-- IF: ... -->` → either keep the block (remove the markers) or delete it
- `TODO` / `FIXME` → finish the section or remove it

A shipped skill should have zero scaffolding residue.

---

## D13 — broken file reference

SKILL.md points at a file that doesn't exist. Either:

- Create the missing file (if SKILL.md should reference it), or
- Fix the path in SKILL.md (typo, wrong folder), or
- Remove the reference if it's no longer needed

Re-run after fixing to confirm no other references break.

---

## D14 — orphaned reference file

A file in `references/` is never mentioned in SKILL.md. Either:

- Wire it in (add a reference from the body), or
- Delete it if it's dead weight

Informational — won't break the skill, but clutters the package.

---

## D15 — README reads like SKILL.md

Rewrite the README for a human audience: second person ("you"), why-it-exists framing, install + usage, an example. Trigger phrasing and step-by-step agent instructions belong in SKILL.md, not the README.

---

## D16 — README missing install or usage

Add the missing section:

- **Installation:** how to copy the skill into `~/.claude/skills/` (or clone the repo)
- **Usage:** 3–5 example phrases that trigger the skill, plus a pointer to `examples/`

---

## After fixing

Re-run Skill Doctor to confirm the verdict improved. If the description was a concern, also run `skill-trigger-tester`. For a full pre-publish pass: Doctor (structure) → Trigger Tester (description) → ship.
