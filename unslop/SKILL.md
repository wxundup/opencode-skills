---
name: unslop
description: Cut AI tells from any output. Must always apply.
---

# Unslop

Edit text to remove AI patterns.

The rule catalog lives in `~/.config/opencode/AGENTS.md` under "Unslop rules" and is loaded into every prompt. The rules are already in context, so do not load anything to read them. This skill is the named hook other skills mean when they say "apply unslop", and it holds the process.

## Process

1. Scan the target text for the catalog patterns.
2. Rewrite. Preserve meaning, match intended tone.
3. Self-audit: "What makes this obviously AI generated?" Fix remaining tells.
