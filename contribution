# Contributing

Thanks for your interest in improving this benchmark suite! Please follow these guidelines to keep results reproducible.

## How to contribute
1. **Open an issue** describing the change (new engine, new dataset, fixes, etc.).
2. **Fork** the repo and create a feature branch.
3. Make changes and include/update tests or validation where applicable.
4. **Update docs** (README, DATA_MANIFEST.md, RESULTS.md, compat/rewrites.md) if behavior or files change.
5. **Open a PR** that:
   - Explains *what* changed and *why*.
   - Links the tracking issue.
   - Attaches or links results (CSV/XLS) and logs. For large artifacts, upload to the MEGA folder and reference the link.

## Adding a new engine
- Put any engine-specific SQL in `queries/per_engine/<engine>/`.
- If you require query rewrites, document them in `compat/rewrites.md`.
- Ensure your runner emits rows with this schema:

