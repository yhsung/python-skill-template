---
name: python-skill-template
description: >
  Guide for packaging Python-based skills with proper dependency management using uv.
  Use this skill whenever someone is creating or structuring a Python skill, script, or tool
  that needs dependencies — especially in multi-skill or shared-code scenarios.
  Covers single-file scripts (PEP 723) all the way to multi-package workspace projects.
---

# Python Skill Packaging Guide

This skill helps you structure Python skills correctly based on complexity.
Three tiers are supported — pick the right one and follow its reference file.

## Tiers at a Glance

| Tier | Shape | When to Use |
|------|-------|-------------|
| **1 — Single Script** | One `.py` file | Self-contained tool, no shared code |
| **2 — Multi-Script** | Flat folder of scripts + shared lib | A few scripts that share utilities |
| **3 — Workspace** | `uv` monorepo with packages | Many skills, deep shared code, CLI tools |

## Step 1: Classify the Skill

Ask:
1. Is it one script with no imports from other local files? → **Tier 1**
2. Does it call other scripts, or share code with 2–5 other scripts? → **Tier 2**
3. Is it part of a larger repo with many skills, needs installable CLIs, or has complex shared code? → **Tier 3**

## Step 2: Read the Reference File

- Tier 1 → `references/tier1-single-script.md`
- Tier 2 → `references/tier2-multi-script.md`
- Tier 3 → `references/tier3-workspace.md`

## Step 3: Apply the Template

Each reference file contains:
- Canonical directory structure
- Annotated template files (copy-paste ready)
- Common pitfalls
- How users run the skill

## Prerequisites (for all tiers)

Users need `uv` installed once:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

That's the only global install needed. Everything else is handled automatically.
