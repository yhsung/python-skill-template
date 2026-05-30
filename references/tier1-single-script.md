# Tier 1 — Single Script (PEP 723)

**Shape:** One self-contained `.py` file.  
**User runs it with:** `uv run skill.py`

---

## Directory Structure

```
my-skill/
├── skill.py        ← the entire skill (deps declared inline)
└── README.md       ← optional, describe what it does
```

No `requirements.txt`. No `pyproject.toml`. No venv to manage.

---

## Template: `skill.py`

```python
#!/usr/bin/env -S uv run --script
# /// script
# requires-python = ">=3.11"
# dependencies = [
#   "requests>=2.31",
#   "rich>=13.0",
# ]
# ///
"""
One-line description of what this skill does.

Usage:
  uv run skill.py [--arg value]
  ./skill.py [--arg value]          # after chmod +x
"""

import argparse
import sys

import requests
from rich.console import Console

console = Console()


def main() -> None:
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument("--url", required=True, help="Target URL")
    args = parser.parse_args()

    try:
        resp = requests.get(args.url, timeout=10)
        resp.raise_for_status()
        console.print(f"[green]OK[/green] {resp.status_code}")
    except requests.RequestException as e:
        console.print(f"[red]Error:[/red] {e}", file=sys.stderr)
        sys.exit(1)


if __name__ == "__main__":
    main()
```

---

## Key Rules

1. **The `# /// script` block must be at the top**, right after the shebang line (if any).  
   `uv` reads it before executing — put it before any real Python code.

2. **Pin loosely with `>=`**, not `==`.  
   `uv` creates a locked venv per-script automatically; no need to pin tightly in source.

3. **Use `argparse` (or `click`/`typer`)** for inputs — don't hardcode paths or values.

4. **Exit with `sys.exit(1)`** on failure so callers can detect errors.

---

## How Users Run It

```bash
# First time: uv downloads deps, creates isolated venv (~2s)
uv run skill.py --url https://example.com

# Make it directly executable (Unix)
chmod +x skill.py
./skill.py --url https://example.com

# Force refresh deps
uv run --refresh skill.py --url https://example.com
```

---

## Common Pitfalls

| Problem | Fix |
|---------|-----|
| Script runs but imports fail | Check the `# ///` block is properly formatted (no extra spaces) |
| Want to call another script | Use `subprocess.run(["uv", "run", "other.py"])` — or consider Tier 2 |
| Need a `.env` file | Use `python-dotenv` as a dependency, load manually in `main()` |
| Script is slow to start | Normal on first run (venv creation). Subsequent runs use cache |
