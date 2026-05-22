# PR description sections

The default structure and content guidance for each section. Adapt to the change type per Step 3 of the main SKILL.md.

---

## Summary (always include)

**Purpose:** Tell the reviewer what changed and *why* in 1–3 sentences. The diff already shows *what*.

**Rules:**
- Lead with the user-visible effect or the architectural reason, not the file list
- One paragraph maximum
- Past tense or imperative present, not "this PR will..."
- Reference the issue or ticket if one exists

**Good example:**
> Adds OAuth2 device flow as a third auth path alongside password and SSO. Used by the new CLI installer where opening a browser isn't practical. Closes #482.

**Bad example:**
> Updates a few files in the auth directory and adds some endpoints. Also some test changes.

---

## Changes (include when >3 files touched or multiple areas)

**Purpose:** Give the reviewer a fast tour of the diff grouped by intent, not by file.

**Rules:**
- Group bullets by *what they accomplish*, not which file they're in
- 3–8 bullets is the sweet spot; collapse low-signal changes into one bullet ("Test updates across affected modules")
- Use noun phrases or imperative verbs — be consistent within the list
- Don't restate the summary

**Good example:**
> - New /auth/device endpoint pair (start, poll)
> - Device-flow token issuance in src/auth/issuer.ts
> - CLI command `dvs auth login --device` wired to the new endpoints
> - Tests for polling backoff and expiry paths

**Bad example:**
> - Modified src/auth/issuer.ts
> - Modified src/auth/routes.ts
> - Modified src/cli/login.ts
> - Modified tests/auth.test.ts

---

## Test Plan (include when any logic changed)

**Purpose:** Tell the reviewer how to convince themselves the change works.

**Rules:**
- Bulleted checklist with `- [ ]` items
- Mix automated commands the reviewer can run with manual steps
- Cover the happy path AND at least one edge case
- For UI changes, include the browser steps explicitly
- Skip for doc-only / chore-only / pure refactor PRs (the test suite is the test plan)

**Good example:**
> - [ ] `pnpm test src/auth/device.test.ts` — all green
> - [ ] Manual: `dvs auth login --device` from a fresh checkout
> - [ ] Verify token TTL matches the 15-minute spec
> - [ ] Try the flow with an expired code; expect a clear error

**Bad example (boilerplate):**
> - [ ] Tests pass
> - [ ] No regressions

---

## Risks / Open Questions (include when risk-detection.md flags something)

**Purpose:** Surface things the reviewer should specifically watch for, or questions the PR author needs an answer on before merge.

**Rules:**
- Never include this section just to fill space — empty Risks signals "no concerns" implicitly
- Each item is one line, specific enough to act on
- Distinguish open questions (need an answer) from risks (need a watcher)
- Don't catastrophize — flag real concerns, not theoretical ones

**Good example:**
> - Polling backoff defaults to 5s — confirm with security team before merge
> - New /auth/device endpoints are public; rate-limit config added in nginx.conf — confirm prod nginx config picks it up
> - Open question: should device codes be one-time or reusable within TTL?

**Bad example:**
> - Could break auth (theoretical)
> - Might need more tests

---

## Change-type templates

### Feature

Sections: Summary, Changes, Test Plan, Risks (if any). Lead summary with the user-visible capability.

### Fix

Sections: Summary, Changes, Test Plan, Risks (if any). Summary names the symptom AND the root cause.

> Fixes a 500 returned when an SSO token expired mid-session. Root cause: the refresh check ran before the expiry-grace window applied.

### Refactor

Sections: Summary, Changes, Risks (if any). Skip Test Plan — the existing suite is the test plan.

Summary leads with the architectural reason. If there's no architectural reason, the refactor probably shouldn't ship.

### Performance

Sections: Summary, Changes, Test Plan, Risks (if any). Summary includes the measured improvement (`p95 query time 340ms → 90ms`).

### Docs

Sections: Summary only. Optionally Changes if >5 files updated.

### Test

Sections: Summary, Changes. Skip Test Plan (the test is the test plan).

### Chore

Sections: Summary. Optionally Changes for multi-package monorepo bumps.

### Breaking

Sections: Summary, Changes, **Migration Guide** (additional section), Test Plan, Risks. Migration Guide is mandatory — show before/after for the changed API or config.

---

## Length discipline

- 1–10 line diff → Summary only, 1 sentence
- 11–100 line diff → Summary + Changes
- 100+ line diff → full template per change type
- 500+ line diff → consider whether the PR should be split before describing it
