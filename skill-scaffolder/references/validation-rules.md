# Validation rules

Apply every rule below before generating files. Each rule has a check, a failure message, and a remediation hint. Surface failures with the rule ID so the user can ask follow-up questions.

When a rule fails, **do not silently autocorrect**. Explain the rule, show what failed, and offer one or more concrete fixes. The user picks one (or edits) and you re-validate.

---

## R1 — Skill name

**Check:**
- Matches regex `^[a-z][a-z0-9]*(-[a-z0-9]+)*$`
- Length 2–40 characters
- Not a reserved word (`test`, `tmp`, `example`, `default`, `skill`, `claude`)

**Failure message:** "Skill name `{name}` failed: must be lowercase kebab-case (alphanumeric + hyphens, starting with a letter), 2–40 characters, and not a reserved word."

**Remediation:** Suggest a transformed version (lowercase, replace spaces/underscores with hyphens, strip invalid chars). Show 2–3 alternatives derived from the purpose if the original is unsalvageable.

---

## R2 — One-line purpose

**Check:**
- Length 10–120 characters
- Does not match the vague pattern: lowercase verb + ("stuff" | "things" | "data" | "documents" | "files") with no qualifier
- Mentions the *what* (artifact type or domain) — heuristic: contains at least one noun more specific than "thing"

**Failure message:** "Purpose `{purpose}` is too vague. Claude needs to know *what* this skill operates on and *why* it's distinct. Examples of vague: 'reviews stuff', 'processes documents'. Examples of specific: 'reviews vendor security questionnaires for SOC2 gaps', 'chunks PDFs into RAG-friendly markdown'."

**Remediation:** Ask: "What artifact does it consume? What artifact does it produce? What domain or context distinguishes it?" Re-form the purpose as `{verb} {specific-input} {into|for|against} {specific-output|criterion}`.

---

## R3 — Trigger description

**Check:**
- Length 100–400 characters
- Contains at least one of: `Use this skill when`, `Use this skill whenever`, `Triggers on`, `Trigger this skill when`, `Activate when`
- Contains at least 3 concrete trigger phrases (heuristic: quoted phrases, comma-separated short fragments after "phrases like", or imperative sentence fragments separated by `,` or `or`)
- Does not contain hedging words: `maybe`, `sometimes`, `if appropriate`, `when relevant`

**Failure message:** "Trigger description failed `{rule}`. Anthropic's guidance is that Claude *undertriggers* skills — descriptions must be 'pushy', with explicit trigger phrasing and at least 3 concrete trigger phrases."

**Remediation:** Show a template fragment:

> "Use this skill whenever the user wants to {primary-action} or {secondary-action}. Triggers on phrases like '{phrase-1}', '{phrase-2}', '{phrase-3}'."

Have the user fill the slots. Re-validate.

---

## R4 — Expected input

**Check:**
- Non-empty after trimming
- Mentions a concrete artifact type (file, directory, URL, freeform text, structured data)

**Failure message (empty):** "Expected input is empty. Even 'freeform user description' counts — but say so explicitly."

**Failure message (too vague):** "Expected input doesn't say *what kind* of input. Is it a file? A directory? A description? Be concrete."

**Remediation:** Suggest concrete patterns: "a single markdown file", "a directory of source code", "a freeform description from the user", "a URL".

---

## R5 — Expected output

**Check:**
- Non-empty after trimming
- Names a concrete output artifact (file, report, structured response, list)

**Failure message:** Same shape as R4.

**Remediation:** Suggest concrete patterns: "a markdown report written to disk", "a JSON object returned to the caller", "a generated directory of files", "a structured analysis printed to chat".

---

## R6 — Conditional folder defaults

Suggest defaults based on purpose-keyword heuristics. The user can override.

| Folder | Suggest YES if purpose mentions | Suggest NO otherwise |
|---|---|---|
| `scripts/` | "automate", "generate files", "run code", "execute", "compute", "transform" | default no |
| `references/` | "domain-specific", "framework", "vertical", "compliance", "industry-specific", or the skill is large enough that splitting context helps | default no |
| `assets/` | "template", "letterhead", "branding", "boilerplate", "logo", "watermark" | default no |

If the user says yes to a folder, generate it with a `README.md` stub. If no, do not create the folder. **Never create empty conditional folders** (Principle of Lack of Surprise).

---

## R7 — Install path

**Check:**
- Path's parent directory exists
- Parent directory is writeable
- Target path does not already exist (or, if it does, the user has explicitly confirmed overwrite)

**Failure message (parent missing):** "Install parent `{parent}` doesn't exist. Create it first or pick a different path."

**Failure message (not writeable):** "Install path `{path}` is not writeable. Check permissions or pick a different path."

**Failure message (already exists):** "`{path}` already exists. Overwrite, pick a different name, or pick a different parent?"

**Remediation:** Offer to default to `~/.claude/skills/{name}` (creating `~/.claude/skills/` if needed).

---

## R8 — Generated SKILL.md body length

**Check:** After generation, count lines in `SKILL.md` from the line *after* the closing `---` of the YAML frontmatter to the end of file. Must be ≤ 500 lines.

**Failure message:** "Generated SKILL.md body is `{n}` lines. Anthropic's guideline is ≤ 500 lines so the body fits cleanly in context. The usual cause is: too much detail in 'How it works' that should live in `references/`."

**Remediation:** Move long procedural detail, validation rules, or templates into `references/{topic}.md` and reference them from SKILL.md. Re-generate and re-validate.

---

## R9 — No empty conditional folders

**Check:** After generation, walk the output directory. Any folder must contain at least one file.

**Failure message:** "Folder `{path}` is empty. Either populate it or remove it — empty conditional folders violate the Principle of Lack of Surprise."

**Remediation:** Populate with a `README.md` stub describing what the folder is for, or remove the folder.

---

## R10 — Frontmatter syntax

**Check:**
- File starts with `---\n`
- Closing `---` exists on its own line within the first 30 lines
- Between the markers: valid YAML, with `name:` and `description:` keys present
- `name` value matches the directory name and R1
- `description` value matches R3

**Failure message:** "Frontmatter check failed: `{specific-failure}`."

**Remediation:** Show the expected frontmatter shape:

```yaml
---
name: skill-name
description: Use this skill whenever ...
---
```

---

## R11 — Examples folder populated

**Check:** `examples/input.md` and `examples/output.md` exist and are non-empty.

**Failure message:** "examples/input.md or examples/output.md is missing or empty. Examples are part of the contract — without them the skill is not finished."

**Remediation:** Generate skeleton examples derived from the user's stated input/output during intake. Have the user review and edit before finalizing.

---

## R12 — README is human-oriented

**Check (heuristic):** Generated README.md does not contain agent-oriented phrasing like "this skill instructs Claude to", "the skill triggers on", "Claude reads the SKILL.md and...". README addresses the human reader directly.

**Failure message:** "README.md reads like SKILL.md. README is for humans browsing GitHub — second person, less procedural detail, more why-it-exists and how-to-install."

**Remediation:** Rewrite using the README template's tone notes. The trigger description belongs in SKILL.md; the README paraphrases it for human readability.

---

## Validation order

Run rules in this order. Stop at the first batch of failures and report them all together rather than failing one at a time:

1. R1, R2, R3 (intake basics)
2. R4, R5 (I/O)
3. R6 (folder choices — these are warnings/suggestions, not blockers)
4. R7 (install path)
5. **Generate files**
6. R8, R9, R10, R11, R12 (post-generation checks)

If post-generation checks fail, regenerate with corrections and re-validate. Do not ship a skill that fails any post-generation check.
