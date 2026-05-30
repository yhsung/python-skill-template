# Quick Reference: uv Cheatsheet

## Install uv (once per machine)
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## Tier 1 — Single Script
```bash
uv run skill.py
uv run --refresh skill.py      # force dep refresh
```

## Tier 2 — Multi-Script Project
```bash
uv sync                         # setup (author)
uv run --project . script.py   # run
```

## Tier 3 — Workspace
```bash
uv sync                                   # setup (author)
uv run --package skill-a skill-a         # run a package
uv run pytest                             # run all tests
uv add requests --package skill-a        # add dep to one package
uv add --dev pytest                       # add workspace dev dep
```

## Dep Format
```
"requests"             # any version
"requests>=2.31"       # minimum
"requests>=2.31,<3"    # range
"pkg @ git+https://github.com/org/repo.git"  # git dep
```
