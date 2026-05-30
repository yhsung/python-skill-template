# Tier 3 — uv Workspace (Monorepo)

**Shape:** Multiple installable packages in one repo, sharing a common lockfile.  
**User runs it with:** `uv run --package skill-a skill-a`

---

## Directory Structure

```
skills-repo/
├── pyproject.toml          ← workspace root (no code here)
├── uv.lock                 ← single lockfile for all packages
├── shared/                 ← internal library
│   ├── pyproject.toml
│   └── src/shared/
│       ├── __init__.py
│       └── utils.py
├── skill-a/
│   ├── pyproject.toml
│   └── src/skill_a/
│       ├── __init__.py
│       └── main.py
└── skill-b/
    ├── pyproject.toml
    └── src/skill_b/
        ├── __init__.py
        └── main.py
```

---

## Template: Workspace Root `pyproject.toml`

```toml
[tool.uv.workspace]
members = ["shared", "skill-a", "skill-b"]

[tool.uv]
dev-dependencies = [
    "pytest>=8.0",
    "ruff>=0.4",
]
```

---

## Template: `shared/pyproject.toml`

```toml
[project]
name = "shared"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = ["rich>=13.0"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

---

## Template: `skill-a/pyproject.toml`

```toml
[project]
name = "skill-a"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "shared",
    "requests>=2.31",
]

[project.scripts]
skill-a = "skill_a.main:run"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.uv.sources]
shared = { workspace = true }
```

---

## Template: `skill-a/src/skill_a/main.py`

```python
"""Skill A — does X."""

import argparse
import sys

from shared.utils import log_err, log_ok


def run() -> None:
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument("--name", required=True)
    args = parser.parse_args()

    try:
        log_ok(f"Done: {args.name}")
    except Exception as e:
        log_err(str(e))
        sys.exit(1)


if __name__ == "__main__":
    run()
```

---

## How to Set Up (once, by skill author)

```bash
cd skills-repo/
uv sync
git add uv.lock
```

## How Users Run It

```bash
uv run --package skill-a skill-a --name hello

# Or install CLI permanently
uv tool install --editable ./skill-a
skill-a --name hello

# Run all tests
uv run pytest
```

---

## Adding a New Skill

1. Copy `skill-a/` → `skill-c/`, rename accordingly
2. Add `"skill-c"` to `members` in root `pyproject.toml`
3. Run `uv sync`

---

## Common Pitfalls

| Problem | Fix |
|---------|-----|
| `ModuleNotFoundError: shared` | Add `[tool.uv.sources] shared = { workspace = true }` |
| New package not picked up | Add to `members` in root `pyproject.toml` |
| Circular deps | Extract common code into `shared` |
