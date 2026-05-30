# python-skill-template

A tiered template for packaging Python skills with [`uv`](https://github.com/astral-sh/uv).

## Tiers

| Tier | Shape | When to Use |
|------|-------|-------------|
| **1** | Single `.py` file (PEP 723) | Self-contained, no shared code |
| **2** | Flat folder + shared `lib/` | Few scripts sharing utilities |
| **3** | `uv` workspace monorepo | Many skills, CLI tools, deep sharing |

## Prerequisites

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## Usage

See [`SKILL.md`](SKILL.md) for the decision guide, then the relevant file under [`references/`](references/).
