# Repo convention detection

Before generating, scan the repo for existing conventions. Respect them. A description that mismatches the team's house style draws more attention than the content itself.

---

## Step 1 — Look for a PR template

Check these locations:

- `.github/pull_request_template.md`
- `.github/PULL_REQUEST_TEMPLATE.md`
- `.github/PULL_REQUEST_TEMPLATE/` (directory of multiple templates)
- `docs/pull_request_template.md`

If a template exists, **use its section headers as the structure**. Fill them with the content from `references/sections.md`. Do not invent additional sections beyond what the template includes — that's a signal the team explicitly chose a leaner format.

If the template has form fields (checkboxes, "Type of change:" multi-select), pick the matching option and remove the others.

---

## Step 2 — Detect commit message style

Sample the last 10–20 commits on the default branch:

```bash
git log --pretty=format:"%s" -20 origin/main
```

Pattern match:

| Pattern | Style | Title format |
|---|---|---|
| `feat:`, `fix:`, `refactor:` prefixes (50%+ of recent commits) | Conventional Commits | `type(scope): description` |
| `feat`, `fix`, `chore` without colon | Loose conventional | `type: description` |
| Sentence-case starts with imperative verb | GitHub default | `Add X to Y` |
| All-caps prefix like `[FRONTEND]`, `[BUG]` | Bracketed area | `[AREA] Description` |
| Jira keys like `PROJ-123:` | Ticket-prefixed | `PROJ-123: Description` |

Pick the matching style for the PR title. If the sample is mixed (no clear majority), default to Conventional Commits for code repos and sentence-case imperative for docs/content repos.

---

## Step 3 — Detect PR title length norms

Sample recent merged PR titles:

```bash
gh pr list --state merged --limit 20 --json title -q '.[].title'
```

If recent titles average:

- **< 50 chars** → keep yours tight, under 50
- **50–72 chars** → standard, aim for 60–72
- **> 72 chars** → team tolerates long titles, but still cap at 72 for GitHub UI

Never exceed 72 chars (GitHub truncates).

---

## Step 4 — Detect project type

Look at the repo root:

| Marker | Project type | Description tone |
|---|---|---|
| `package.json` with `bin` field | CLI tool | Concrete: "Adds --json flag for machine-readable output" |
| `package.json` with frontend deps (React/Vue/etc) | Web app | User-facing: "New settings page allows ..." |
| `package.json` library-only | JS library | API-focused: "Adds the `parseConfig()` export" |
| `pyproject.toml` with `[project.scripts]` | Python CLI | Same as JS CLI |
| `Dockerfile` + `docker-compose.yml` at root | Service | Ops-aware: mention deploy / migration concerns |
| `terraform/` or `cloudformation/` | Infrastructure | Risk-heavy: prod impact, rollback plan |
| `migrations/` directory | DB-backed app | Always flag schema changes prominently |

The project type informs default section emphasis. For infra/service repos, the Risks section is more often non-empty. For libraries, the API contract is the lead.

---

## Step 5 — Detect tone from existing PR descriptions

Sample 3–5 recent merged PR bodies:

```bash
gh pr list --state merged --limit 5 --json body -q '.[].body'
```

Look for:

- **Formality** — does the team write in first person? Past tense? Bulleted or prose?
- **Length** — are typical descriptions 5 lines or 50?
- **Emoji usage** — :sparkles: in summaries, or never
- **Linking** — do they reference issues, design docs, Slack threads?

Match the dominant pattern. If the team writes 5-line descriptions, don't ship a 30-line one.

---

## Step 6 — When in doubt

Defaults that work for most repos:

- Conventional Commits title (`feat(auth): add device flow`)
- Past-tense or imperative summary
- Bulleted Changes section
- Checkbox Test Plan
- Risks section only if `risk-detection.md` flags something

These match the GitHub default template and won't look out of place anywhere.

---

## What to NOT do

- Don't copy the team's mistakes — if every PR description says "updated stuff", produce a better one
- Don't add emojis if the team doesn't use them
- Don't reference internal tools / processes you can't verify exist
- Don't fabricate ticket references — if you can't find an issue number, omit it
