# expensr-backend

Backend API service for Expensr.

## Requirements

- Python 3.12 (matches the ECS runtime; see `.python-version`)
- pip

The project targets **Python 3.12** even if your local interpreter is newer, so
that linting, type-checking, and runtime behaviour match the deployed container.

## Local setup

```powershell
# Create and activate a virtual environment
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1

# Install the project with dev tooling (ruff + mypy)
pip install -e ".[dev]"
```

On macOS / Linux:

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

## Tooling

All tooling is configured in `pyproject.toml`.

| Task            | Command                     |
| --------------- | --------------------------- |
| Lint            | `ruff check .`              |
| Lint + autofix  | `ruff check --fix .`        |
| Format          | `ruff format .`             |
| Format check    | `ruff format --check .`     |
| Type check      | `mypy`                      |

Ruff is pinned to `~=0.16.0` and mypy to `~=1.18.0`. The ruff lint rule set is
pycodestyle (`E`/`W`), pyflakes (`F`), isort (`I`), pyupgrade (`UP`), and
flake8-bugbear (`B`). mypy runs in `strict` mode.

## Conventions / notes for later phases

These are decisions recorded now so the application code added in Phase 3 stays
consistent:

- **Money uses `Decimal`, never `float`.** All monetary amounts must be
  represented with `decimal.Decimal` to avoid binary floating-point rounding
  errors. Database columns should be `NUMERIC`/`DECIMAL`, and (de)serialization
  must preserve exact precision.
- **PgBouncer / connection pooling: disable prepared statement caching.** When
  running behind RDS Proxy or PgBouncer in transaction-pooling mode, prepared
  statements are not safe to reuse across pooled connections. Set the driver's
  `prepare_threshold` to `None` (asyncpg/psycopg) so statements are not cached
  server-side.

## Status

Phase 1 (Task 1): tooling only. No FastAPI application yet — that lands in
Phase 3. The `app/` package currently contains only a version placeholder.
