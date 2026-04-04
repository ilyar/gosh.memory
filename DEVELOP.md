# Development

## Tooling

- Python: `3.10+`
- Package manager / runner: `uv`
- Lint: `ruff`
- Type check: `mypy`
- Security check: `bandit`
- Tests: `pytest`, `pytest-asyncio`

## Initial setup

```bash
uv sync --extra dev
```

Optional extras:

- `uv sync --extra local-embed` installs `sentence-transformers` for local embeddings
- `uv sync --extra google` installs the Google provider SDK
- `uv sync --extra sqlcipher` installs `pysqlcipher3` for encrypted SQLite support
- `uv sync --all-extras` installs all optional runtime extras

If you need both dev tooling and runtime extras:

```bash
uv sync --extra dev --all-extras
```

## Common commands

Run lint:

```bash
uv run ruff check .
```

Format check:

```bash
uv run ruff format --check .
```

Run tests:

```bash
uv run pytest -q
```

Type check:

```bash
uv run mypy src
```

Security check:

```bash
uv run bandit -r src
```

## Test notes

Some tests depend on local companion trees that are not part of this repository:

- `karnali`
- `benchmarks`
- `multibench`

Without those directories, the corresponding tests fail during collection. The core project test suite can still be run independently.

Some runtime paths also expect provider credentials or mocked embeddings. In particular, OpenAI-backed embedding paths require `OPENAI_API_KEY` unless the relevant tests stub embedding calls explicitly.

## Optional dependency policy

The repository intentionally keeps the base install smaller than the full feature set.

- Base dependencies cover the default server/runtime path.
- Optional extras enable integrations that are lazy-loaded by the code.
- Contributor tooling belongs in the `dev` extra, not in base runtime dependencies.

## `uv.lock` policy

Yes, committing `uv.lock` is reasonable for this repository.

Why:

- this repo is not just a reusable library; it is also a runnable service / CLI project
- contributors benefit from a reproducible dev environment
- CI and local debugging become easier when everyone resolves the same dependency graph

Important nuance:

- published packages still depend on `pyproject.toml`, not on `uv.lock`
- downstream users installing with `pip` do not consume `uv.lock`
- the lock file is for repository development and CI reproducibility, not for library dependency semantics

Recommended workflow:

```bash
uv lock
uv sync --extra dev
```

Regenerate `uv.lock` when dependency metadata changes in `pyproject.toml`.
