# PR Describer

> Generates structured pull request descriptions from a git diff — no boilerplate, real risk callouts, sized to the change.

A Claude Code skill that reads a `git diff` (or `gh pr diff` output) and writes a PR description with a summary, change list, test plan, and risk flags. Output is copy-paste ready for `gh pr create --body-file` or the GitHub UI.

## What it does

Most PR descriptions are either two words ("updated stuff") or a wall of unstructured bullets the reviewer has to parse. This skill produces something in between — a structured description that:

- Leads with *why*, not *what* (the diff already shows what)
- Sizes the sections to the change (no Test Plan section on a doc-only PR)
- Flags real risks — schema changes, auth changes, removed exports, dependency bumps
- Matches the repo's existing PR style (Conventional Commits, sentence case, whatever)

It also generates a separate PR title following repo conventions, ready for `--title`.

## Why it exists

`gh pr create` opens an editor and asks for a description. Most developers either skip it or write the minimum to get past the prompt. The result: reviewers spend time reverse-engineering intent from the diff. This skill closes that gap without adding ceremony — descriptions are short for small changes, structured for big ones, and skip sections that don't apply.

## Installation

```bash
git clone https://github.com/digitalvanguardsolutions/skills.git ~/dvs-skills
cp -r ~/dvs-skills/pr-describer ~/.claude/skills/
```

## Usage

Once installed, point Claude at the changes:

- "Write a PR description for this branch"
- "Generate a PR body for #123"
- "Describe this diff: [paste]"
- "Draft a pull request for the auth refactor branch"

The skill handles three input modes:

1. **Pasted diff** — paste the output of `git diff main...HEAD`
2. **PR number** — uses `gh pr diff <num>` if `gh` is installed
3. **Branch reference** — runs `git diff <base>...<head>` from the repo

See `examples/input.md` and `examples/output.md` for a full run.

## What you get back

```
Title: feat(auth): add OAuth2 device flow

Body:
## Summary
Add OAuth2 device flow as a third auth path alongside password and SSO.
Used by the new CLI installer where opening a browser isn't practical.

## Changes
- New /auth/device endpoint pair (start, poll)
- Device-flow token issuance in src/auth/issuer.ts
- CLI command `dvs auth login --device` wired to the new endpoints
- Tests for the polling backoff and expiry paths

## Test Plan
- [ ] `pnpm test src/auth/device.test.ts` — all green
- [ ] Manual: `dvs auth login --device` from a fresh checkout
- [ ] Verify token TTL matches the 15-minute spec

## Risks / Open Questions
- Polling backoff defaults to 5s — confirm with security team before merge
- New /auth/device endpoints are public; rate-limit config added in nginx.conf
```

Compare to "updated auth stuff."

## Pairs with the rest of the DVS Skill Drop suite

```
1. scaffold a skill         → skill-scaffolder
2. test that it triggers    → skill-trigger-tester
3. describe its PR          → pr-describer
```

This skill itself was scaffolded with skill-scaffolder and validated with skill-trigger-tester. Dogfooding the suite.

## Requirements

- Claude Code installed
- `git` for diff access; `gh` optional but recommended for PR-number input

No external dependencies, no API keys, no scripts.

## License

MIT — see `LICENSE`.

## About

Built by [Digital Vanguard Solutions](https://digitalvanguardsolutions.com) — practical AI implementation for mid-market.

Drop #3 in the [DVS Skill Drop Friday](https://github.com/digitalvanguardsolutions/skills) series.
