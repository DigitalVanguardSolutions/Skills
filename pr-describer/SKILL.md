---
name: pr-describer
description: Use this skill whenever the user wants to generate a pull request description, write a PR summary, draft a PR body from a diff, or describe a branch's changes. Triggers on phrases like "write a PR description", "generate a PR body", "describe this diff", "draft a pull request", or "PR description for". Reads a diff or commit log and outputs a structured PR description.
---

# PR Describer

Generates structured pull request descriptions from a git diff and commit log. Outputs a summary, change list, test plan, and risk callouts — sized to the scope of the change, not boilerplated for every PR.

## When to use this skill

Use this skill any time the user has a branch ready to open as a PR and wants a clean description instead of "updated stuff" or a wall of bullet points. Common triggers:

- "Write a PR description for this branch"
- "Generate a PR body from this diff"
- "Describe this diff"
- "Draft a pull request"
- "What should the PR description say?"
- "PR description for #123"

It pairs naturally with `gh pr create --body-file` — generate the description, save it, attach it to the PR command.

## How it works

### Step 1 — Get the changes

Accept any of these inputs (in order of preference):

1. **Pasted `git diff` output** — the user pastes the full diff
2. **PR number via `gh`** — run `gh pr diff <number>` to fetch the diff (if `gh` is installed and authenticated)
3. **Branch reference** — run `git diff <base>...<head>` where the user names the branches
4. **A directory** — run `git diff main...HEAD` from inside it

Also capture the commit log when available: `git log <base>...<head> --oneline` (or pass it alongside the diff).

If the input is empty or trivially small (<20 lines changed), confirm with the user before generating — small diffs often don't warrant a structured description.

### Step 2 — Detect repo conventions

Read `references/repo-conventions.md` and apply the detection rules:

- Look for `.github/pull_request_template.md` — if present, use its structure as a starting point
- Check recent commit messages for style (Conventional Commits? Sentence case? Past tense?)
- Check recent PR titles for length and prefix patterns
- Note the project type (library, app, monorepo) from `package.json` / `pyproject.toml` / directory shape

If conventions are unclear, default to the templates in `references/sections.md`.

### Step 3 — Classify the change

Decide what kind of change this is. Categories (one or more may apply):

- **Feature** — new capability the user can observe
- **Fix** — corrects a bug; reference the symptom and root cause
- **Refactor** — internal restructure with no behavior change
- **Performance** — measurable speed/memory improvement
- **Docs** — documentation only
- **Test** — test-only changes
- **Chore** — dependency bumps, config tweaks, formatting
- **Breaking** — backward-incompatible

The classification controls which template sections appear (see Step 4) and the tone of the summary.

### Step 4 — Build the description

Pull the structure from `references/sections.md`. The default layout:

```markdown
## Summary
<1–3 sentences on what changed and why>

## Changes
<bulleted list grouped by area>

## Test Plan
<bulleted checklist of how to verify>

## Risks / Open Questions
<callouts where applicable — see references/risk-detection.md>
```

Section discipline:

- **Always include Summary** — even for one-line PRs
- **Include Changes** if more than 3 files touched or more than 1 area affected
- **Include Test Plan** if any logic changed (not for doc-only or chore-only)
- **Include Risks** only when `references/risk-detection.md` flags something — never empty-fill it

### Step 5 — Run risk detection

Apply `references/risk-detection.md` against the diff. Flag any of these as risks:

- Schema migrations or DB changes
- Auth, permissions, or session handling changes
- Public API or contract changes
- Removed code (especially exports)
- Configuration changes affecting prod
- New external dependencies
- Cross-cutting changes (touching 10+ files)
- Anything in `package.json` / `requirements.txt` / lockfiles

Each flagged risk gets a one-line callout in the **Risks / Open Questions** section. Do not invent risks — only surface what the diff actually contains.

### Step 6 — Title

Generate a PR title following the repo's conventions:

- If recent PRs use Conventional Commits (`feat:`, `fix:`, `refactor:`), match that pattern
- Otherwise, sentence-case imperative ("Add", "Fix", "Update")
- Keep under 72 chars
- No trailing period

Show the title separately from the body so the user can use it with `gh pr create --title`.

### Step 7 — Output

Return the description in a single fenced markdown block ready to copy-paste, plus the title as a separate line:

```
Title: feat(auth): add OAuth2 device flow

Body:
---8<---
## Summary
...
---8<---
```

Offer the user the option to:

- Tighten — make it shorter / less formal
- Expand — more detail in each section
- Adjust tone — match a specific style they prefer

## Inputs

- A `git diff` output (pasted), **or**
- A PR number (used with `gh pr diff <num>`), **or**
- A branch range (used with `git diff <base>...<head>`)
- Optional: commit log for the same range

## Output

- A PR title (1 line, under 72 chars, matching repo conventions)
- A PR description body in markdown, with sections sized to the change

## References

All structural and convention knowledge is in `references/`:

- `references/sections.md` — PR section templates by change type
- `references/risk-detection.md` — heuristics for the Risks section
- `references/repo-conventions.md` — how to detect and respect a repo's existing style

Load these at execution time. Do not paraphrase from memory.

## Quality bar

A PR description from this skill must:

- Lead with *why*, not *what* — the diff already shows *what*
- Size to the change — no boilerplate sections on a 3-line fix
- Flag real risks, never invent risks to fill space
- Match the repo's existing PR style (commit prefixes, sentence case, etc.)
- Be copy-paste ready for `gh pr create` or the GitHub UI

If a reviewer reads the description and still has to re-read the diff to understand intent, the description failed.

## How this skill was built

Drop #3 in DVS Skill Drop Friday. Scaffolded with [`skill-scaffolder`](https://github.com/digitalvanguardsolutions/skills/tree/main/skill-scaffolder), then validated with [`skill-trigger-tester`](https://github.com/digitalvanguardsolutions/skills/tree/main/skill-trigger-tester) — the two prior drops.
