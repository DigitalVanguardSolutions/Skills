# Audit checklist

Run every check. Each produces a finding with a check ID, the affected file, and a pass/fail result. Severity is assigned separately (see `severity-levels.md`). Remediation comes from `fix-playbook.md`.

Do not stop at the first failure. Collect all findings.

---

## Group 1 — Structure

### D1 — SKILL.md present

**Check:** A file named exactly `SKILL.md` (case-sensitive) exists at the skill root.

**Fail if:** missing, or named `skill.md` / `Skill.md` / `SKILLS.md`.

---

### D2 — README.md present and non-empty

**Check:** `README.md` exists at the skill root and has more than 10 non-whitespace characters.

**Fail if:** missing or effectively empty.

---

### D3 — LICENSE present and non-empty

**Check:** A `LICENSE` (or `LICENSE.md` / `LICENSE.txt`) file exists and is non-empty.

**Fail if:** missing or empty.

---

### D4 — examples/ present and populated

**Check:** An `examples/` directory exists with at least one non-empty file.

**Fail if:** missing, or present but empty / contains only empty files.

---

### D5 — No empty conditional folders

**Check:** If `scripts/`, `references/`, or `assets/` exist, each contains at least one non-empty file.

**Fail if:** any of these folders exists but is empty (Principle of Lack of Surprise).

---

## Group 2 — Frontmatter

### D6 — Frontmatter well-formed

**Check:**
- File starts with `---` on line 1
- A closing `---` appears within the first 40 lines
- The content between is parseable as YAML key/value pairs

**Fail if:** no opening `---`, no closing `---`, or unparseable YAML.

---

### D7 — Required keys present

**Check:** Frontmatter contains both `name:` and `description:` keys with non-empty values.

**Fail if:** either key missing or empty.

---

### D8 — name matches directory

**Check:** The `name` value equals the skill's directory name.

**Fail if:** mismatch (e.g., directory `foo-bar/` but `name: foobar`). Anthropic resolves skills by directory; a mismatch causes confusion.

---

### D9 — name is kebab-case

**Check:** `name` matches `^[a-z][a-z0-9]*(-[a-z0-9]+)*$`.

**Fail if:** contains uppercase, spaces, underscores, or leading/trailing hyphens.

---

### D10 — description within length limits

**Check:** `description` is between 20 and 1024 characters (1024 is Anthropic's hard limit).

**Fail if:** under 20 (uselessly short) or over 1024 (will be rejected/truncated).

**Note:** This is a *length-only* check. Whether the description is pushy enough to trigger is out of scope — defer to `skill-trigger-tester`.

---

## Group 3 — Body

### D11 — Body under 500 lines

**Check:** Count lines from the line after the closing frontmatter `---` to EOF. Must be ≤ 500.

**Fail if:** over 500 (Anthropic's guideline; long bodies should move detail into `references/`).

---

### D12 — No leftover scaffolding placeholders

**Check:** The SKILL.md body contains no *scaffolding residue*. Match these as literal syntax or all-caps standalone tokens, NOT as words in prose:

- `{{...}}` — template variable syntax (literal double braces)
- `<!-- IF:` / `<!-- ENDIF -->` — conditional block markers
- `TODO`, `FIXME`, `XXX`, `HACK`, `PLACEHOLDER` — only when they appear as all-caps tokens on a word boundary (e.g., `TODO:`, `PLACEHOLDER`), which is how scaffolders and editors emit them
- `Lorem ipsum`

**Do NOT fail on:** lowercase prose that happens to use these words (e.g., a sentence describing "leftover placeholders" or "things still to do"). A skill that documents these concepts — including this one — must not trip the check. The signal is residue syntax, not vocabulary.

**Fail if:** any residue token or marker is present — a sign the skill was scaffolded but not finished.

---

## Group 4 — Reference integrity

### D13 — All referenced files exist

**Check:** For every relative path mentioned in SKILL.md that points into `references/`, `scripts/`, `assets/`, or `examples/`, the target file must exist.

Method:
1. Extract path-like tokens from SKILL.md (e.g., `references/checklist.md`, `scripts/run.py`, `examples/input.md`)
2. For each, verify the file exists on disk

**Fail if:** SKILL.md references a file that doesn't exist (broken pointer).

---

### D14 — No orphaned reference files

**Check:** Every file inside `references/` is mentioned at least once in SKILL.md.

**Fail if:** a file in `references/` is never referenced — it's dead weight, or SKILL.md forgot to wire it in. (This is informational, not a hard failure — see severity-levels.md.)

---

## Group 5 — README quality (light)

### D15 — README is human-oriented

**Check (heuristic):** README.md does NOT read like SKILL.md. Flag if it contains agent-directed phrasing such as:

- "this skill instructs Claude to"
- "the skill triggers on"
- "Claude reads the SKILL.md"
- "Use this skill whenever" (that belongs in the description, not the README)

**Fail if:** README appears to be a copy of SKILL.md content rather than human-facing docs.

---

### D16 — README has install + usage

**Check:** README.md contains a section or heading for installation AND one for usage (case-insensitive match on "install" and "usage" / "using").

**Fail if:** either missing — a human browsing GitHub can't tell how to install or invoke it.

---

## Output of the checklist

For each check, record:

```
[D{n}] {pass | fail} — {affected file or 'n/a'} — {one-line detail}
```

Pass the full list to `severity-levels.md` for grading and the verdict.
