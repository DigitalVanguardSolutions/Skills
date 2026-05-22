# Risk detection heuristics

Scan the diff for the patterns below. Each detected pattern produces a one-line callout in the **Risks / Open Questions** section. Do not include risks the diff doesn't actually contain.

The goal is to save the reviewer time — point them at the lines that need a careful read.

---

## High-severity (always flag)

### Schema / DB migrations

**Detect:**
- New files under `migrations/`, `db/migrate/`, `alembic/`, `prisma/migrations/`
- SQL files outside fixtures
- ORM model files with structural changes (added/removed/renamed columns)
- `ALTER TABLE`, `DROP COLUMN`, `CREATE INDEX`

**Callout template:**
> Schema change in `{file}` — verify migration is reversible and runs in expected time on prod data volume.

### Auth / permissions / session handling

**Detect:**
- Files matching `*auth*`, `*session*`, `*permission*`, `*token*`, `*credentials*`
- Changes to middleware lists, route guards, or RBAC config
- Changes to JWT signing/verification, cookie attributes, CORS

**Callout template:**
> Auth surface change in `{file}` — confirm with security review before merge.

### Public API / contract changes

**Detect:**
- Changes to OpenAPI/Swagger specs
- Changes to GraphQL schemas
- Changes to public function signatures in library code
- Changes to CLI argument structure
- Changes to environment variable names or required vars

**Callout template:**
> Breaking change to `{contract}` — downstream callers may need updates. Confirm version bump matches the change.

### Removed code (especially exports)

**Detect:**
- Net-negative diff in `index.ts` / `__init__.py` / barrel files
- Removed `export` / `module.exports` statements
- Removed CLI commands, API routes, or DB columns

**Callout template:**
> Removed `{export}` from `{file}` — confirm no consumers still import it (search the codebase + dependent repos).

### Production configuration changes

**Detect:**
- Changes to `Dockerfile`, `docker-compose.yml`, `kubernetes/`, `terraform/`, `cloudformation/`
- Changes to `nginx.conf`, `.env.production`, secrets manager templates
- Changes to GitHub Actions workflows that deploy

**Callout template:**
> Prod config change in `{file}` — coordinate with deploy / on-call. Note rollback plan.

---

## Medium-severity (flag with brief callout)

### New external dependencies

**Detect:**
- New entries in `package.json`, `requirements.txt`, `pyproject.toml`, `go.mod`, `Cargo.toml`
- New lockfile entries that aren't transitive bumps

**Callout template:**
> Adds dependency `{name}` — confirm license is compatible (and the package is healthy / maintained).

### Cross-cutting changes (touching many files)

**Detect:**
- Diff touches 10+ files in unrelated directories
- Renames or signature changes that propagate

**Callout template:**
> Cross-cutting change touching {N} files across {M} areas — review for missed call sites.

### New environment variables

**Detect:**
- Diff adds references to `process.env.X`, `os.environ['X']`, `std::env::var("X")` not previously present
- `.env.example` or `config/` files add new entries

**Callout template:**
> New env var `{NAME}` required — confirm it's documented in `.env.example` and added to deploy environments.

### Disabled tests

**Detect:**
- `.skip(`, `xit(`, `@pytest.mark.skip`, `#[ignore]`, `it.todo(`
- Removed test files without obvious replacement

**Callout template:**
> Test `{name}` skipped or removed in `{file}` — confirm the reason and tracking issue.

---

## Low-severity (flag only if no other risks present, to avoid noise)

### TODO / FIXME added

**Detect:** New `TODO`, `FIXME`, `XXX`, `HACK` markers in the diff (not in unchanged context lines).

**Callout template:**
> New `TODO` in `{file}:{line}` — confirm there's a tracking issue.

### Generated code or formatting-only changes mixed with logic

**Detect:** Diff includes both `{file}.generated.ts` (or similar) and same-PR logic changes.

**Callout template:**
> Mixes generated and hand-written changes in the same PR — consider splitting for reviewability.

---

## Anti-patterns (DON'T flag these)

- "Could potentially affect performance" — only flag if you have evidence (benchmarks, complexity change)
- "Might need more tests" — only flag specific gaps (which path is untested)
- "May have edge cases" — only flag specific edge cases
- "Theoretical security concern" — only flag if the diff actually exposes one

If you can't name the specific line or pattern, don't include the callout.

---

## When the diff has zero risks

Omit the entire **Risks / Open Questions** section. An empty Risks section is more confusing than no section — it implies "I looked and there's nothing" but a missing section conveys the same.
