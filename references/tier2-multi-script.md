# Tier 2 — Multi-Script with Shared Library

**Shape:** A folder of scripts that share a local utility module.  
**User runs it with:** `uv run --project . script_a.py`

---

## Directory Structure

```
my-skill/
├── pyproject.toml      ← project def + shared deps
├── uv.lock             ← committed lockfile (reproducible installs)
├── lib/
│   ├── __init__.py
│   └── utils.py        ← shared code imported by all scripts
├── script_a.py         ← entry point A
├── script_b.py         ← entry point B
└── README.md
```

---

## Template: `pyproject.toml`

```toml
[project]
name = "my-skill"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "requests>=2.31",
    "rich>=13.0",
]

[tool.uv]
package = false
```

---

## Template: `lib/utils.py`

```python
"""Shared utilities for my-skill scripts."""

from rich.console import Console

console = Console()


def log_ok(msg: str) -> None:
    console.print(f"[green]✓[/green] {msg}")


def log_err(msg: str) -> None:
    console.print(f"[red]✗[/red] {msg}")
```

---

## Template: `script_a.py`

```python
#!/usr/bin/env -S uv run --project .
"""
Script A — does X.

Usage:
  uv run --project . script_a.py [--arg value]
"""

import argparse
import sys

from lib.utils import log_err, log_ok


def main() -> None:
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument("--name", required=True)
    args = parser.parse_args()

    try:
        log_ok(f"Done: {args.name}")
    except Exception as e:
        log_err(str(e))
        sys.exit(1)


if __name__ == "__main__":
    main()
```

---

## Script calling Script

### Pattern A — Function call (preferred)
```python
from script_a import main as run_a
run_a()
```

### Pattern B — Subprocess
```python
import subprocess, sys

result = subprocess.run(
    ["uv", "run", "--project", ".", "script_a.py", "--name", "foo"],
    check=False,
)
if result.returncode != 0:
    sys.exit(result.returncode)
```

---

## How to Set Up (once, by skill author)

```bash
cd my-skill/
uv sync
git add uv.lock
```

## How Users Run It

```bash
cd my-skill/
uv run --project . script_a.py --name hello
```

---

## Common Pitfalls

| Problem | Fix |
|---------|-----|
| `ModuleNotFoundError: lib` | Make sure `lib/__init__.py` exists and you're running with `--project .` |
| Scripts have conflicting deps | They share one lockfile — if conflict, use Tier 3 |
| `uv.lock` not committed | Always commit it for reproducible installs |
