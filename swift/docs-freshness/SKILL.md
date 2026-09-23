---
name: docs-freshness
description: Keep the coding agent on the current version of fast-moving libraries and services - paste current docs into prompts, install official agent skill packs, and check installed versions instead of trusting training data. Use when integrating any SDK, library, or backend service (auth, analytics, networking, payments, speech), when the agent generates code with deprecated or nonexistent APIs, when builds fail on API mismatches, or when the user mentions a library updating or "the AI doesn't know the new version". Trigger on "integrate [service]", "use the [library] SDK", "deprecated", "old API", "the docs changed", "it used the wrong version".
---

# Docs freshness

AI models learn from training data that is months old, sometimes more. Libraries that move fast ship breaking changes in that gap. The result is code that looks right and fails at build time or in production: a deprecated method, an old initializer, a config pattern from the previous major version. You will not notice until something breaks, so prevent it at the prompt instead.

## Fix 1: paste the current docs into the prompt

For any integration prompt, put the documentation after your instructions, separated by a divider:

```
Read AGENTS.md first and follow it strictly.

Replace the mocked sign-in with real [service] authentication.
Keep the existing UI and navigation intact. After verification,
navigate to the home screen. If anything is unclear, ask before
implementing.

---

[Copied markdown of the service's current quickstart/setup docs]
```

The agent reads your instructions first, understands the goal, then uses the docs to do it with the current API. This works for any library where you have any doubt about the agent's knowledge. Most doc sites now have a "copy page as markdown" button; use it.

## Fix 2: install the official agent skill pack

Cleaner than pasting, for services you'll touch repeatedly. Most developer tools publish agent skills today (a reusable instruction pack that teaches the agent the current, correct usage). Installing one means the agent keeps using the right version without you pasting docs every time. The common installer:

```
npx skills add <package-or-url>
```

Then pick the relevant skill features, your agent, and project scope. Services like Expo, Clerk, and Stream all publish packs; check the service's docs for "agent skills" or "AI docs".

A third path for broad coverage is a docs-fetching service (such as Context7) that pulls current documentation for any library on demand. Any of the three fixes works; pasting is the fallback when nothing else is set up.

## Fix 3: anchor on what's actually installed

When unsure which version the project uses, ask the agent to check rather than assume:

> Check which version of [package] is installed in this project (Package.resolved or Package.swift for Swift packages) and implement against that version's API.

For Swift projects, `Package.resolved` and the project's package list are the ground truth. If the agent must upgrade a package, make that its own one-task prompt (see `four-part-prompt`) so a version bump can't hide inside a feature.

## Signals you skipped this

A build that fails on "no such method", a service integration that "almost works", docs examples the agent ignores. Each of these is usually stale training data, not your mistake. Paste the docs and re-run the prompt; the fix is one message.
