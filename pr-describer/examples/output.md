# Example output

What pr-describer returns for the diff in `input.md`.

---

```
Title: feat(auth): add OAuth2 device flow

Body:
---8<---
## Summary

Adds OAuth2 device flow as a third auth path alongside password and SSO. Used by the new CLI installer where opening a browser isn't practical — the user gets a short code, visits the verification URL on any device, and the CLI polls until authorization completes.

## Changes

- New `startDeviceFlow` / `pollDeviceFlow` in `src/auth/device.ts` — issues device + user codes, polls until the user authorizes
- Device routes wired into the main auth router (`src/auth/routes.ts`)
- New `dvs login --device` CLI option (`src/cli/login.ts`)
- Prisma migration `20260522_add_device_codes` adds the `DeviceCode` table
- Test coverage in `tests/auth/device.test.ts` — happy path, expiry, polling backoff

## Test Plan

- [ ] `pnpm test tests/auth/device.test.ts` — all green
- [ ] Manual: `dvs login --device` from a fresh checkout, complete flow via the verification URL
- [ ] Verify device-code TTL is 15 minutes (DB record expires; poll returns `expired`)
- [ ] Verify polling returns `authorization_pending` before user completes, `token` after

## Risks / Open Questions

- **Schema change** in `prisma/migrations/20260522_add_device_codes/migration.sql` — new `DeviceCode` table. Verify migration is reversible (a `down` script or `prisma migrate reset` plan) before merge.
- **Auth surface change** — new public endpoints under `/auth/device`. Confirm with security review; rate-limit config will be needed in nginx.conf (not in this PR — open follow-up?).
- Open question: should device codes be one-time (deleted after token issuance) or reusable within TTL? Current behavior is one-time-implicit (the record stays but `userId` is set; next poll returns `token`).
---8<---
```

## What the skill flagged and why

| Flag | Source | Rule |
|---|---|---|
| Schema change | `prisma/migrations/.../migration.sql` | risk-detection.md → high-severity → DB migrations |
| Auth surface change | files under `src/auth/`, new routes | risk-detection.md → high-severity → auth |
| Open question (one-time vs reusable codes) | inferred from `device.ts` logic | judgment call surfaced for reviewer |

## What the skill did NOT flag

- No removed exports (diff is net-positive)
- No new dependencies (no `package.json` change)
- No config or env changes
- No cross-cutting changes (4 files, 1 area)

This is why the Risks section is short and specific, not boilerplate. The skill flagged only what the diff actually contains.

## What changed in the title

The commit log used `feat(auth): add device flow startup` and 4 other narrow commits. The skill detected Conventional Commits style and consolidated to `feat(auth): add OAuth2 device flow` — one title for the whole branch, not a copy of the latest commit.

---

## Comparing to "no skill"

Without this skill, the same branch typically gets a PR description like:

> Added device flow support. Also some routing changes and tests. Should be ready to merge.

That's 18 words and tells the reviewer almost nothing. The output above is ~280 words and tells the reviewer exactly where to look.
