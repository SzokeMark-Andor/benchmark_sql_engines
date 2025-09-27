# Compatibility Notes & Query Rewrites

Document any semantic changes required to run the canonical queries on each engine.

## Phoenix
- **q22.sql**: not supported due to function/optimizer limitations → not executed; placeholder rows added in consolidated files.
- Date handling: cast ISO strings to `DATE` via `TO_DATE(...)` where needed.

## Drill
- Patched JDBC driver required (see MEGA).
- Certain numeric divisions coerced to `DECIMAL` to avoid integer division.

## Presto-Velox
- Use `DATE 'YYYY-MM-DD'` literals for consistent typing.
- Explicit casts on `COUNT(*)` outputs where downstream expects `BIGINT`.

## Dremio
- Prefer fully-qualified paths for Parquet datasets.
- `EXTRACT(YEAR FROM ...)` used instead of engine-specific date funcs.

## General
- Replaced non-ANSI functions with ANSI equivalents where possible.
- Ensured all `JOIN` predicates are explicit (no implicit joins).
- Avoided `SELECT *`; listed required columns for predictability.

> If you add a new engine or adjust a query, append a bullet explaining *what* changed and *why*, and link the PR/commit.
