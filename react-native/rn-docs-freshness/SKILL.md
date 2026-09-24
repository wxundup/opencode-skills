---
name: rn-docs-freshness
description: Keep the coding agent on the current version of fast-moving libraries and services in Expo/React Native - paste current docs into prompts, install official agent skill packs, and check installed versions instead of trusting training data. Use when integrating any SDK, library, or backend service into an RN app (auth, analytics, networking, payments, audio/video, maps), when the agent generates code with deprecated or nonexistent APIs, when builds fail on API mismatches, or when "the AI doesn't know the new version". Trigger on "integrate [service]", "use the [library] SDK", "deprecated", "old API", "the docs changed", "expo doctor", "it used the wrong version".
---

# Docs freshness for React Native

AI models learn from training data that is months old, sometimes more. RN-ecosystem libraries move fast, and Expo itself version-pins many APIs, so the gap shows up constantly: the agent configures NativeWind v4 when your project has v5, writes Clerk code from the old SDK, or skips an Expo config plugin that the current version requires. The code looks right and fails at build time or in production. Prevent it at the prompt instead.

## Fix 1: paste the current docs into the prompt

For any integration prompt, put the documentation after your instructions, separated by a divider:

```
Read agents.md first and follow it strictly.

Replace the mocked sign-in with real Clerk authentication for Expo.
Keep the existing UI and navigation intact. Implement email sign-up,
sign-in, and social auth through Clerk. After verification, navigate
to the home route. If anything is unclear, ask before implementing.

---

[Copied markdown of the Clerk Expo quickstart docs, JavaScript tab]
```

The agent reads your instructions first, understands the goal, then uses the docs to do it with the current API. Watch for version tabs on doc pages (NativeWind v5 versus v4 matters enormously) and copy the one matching your project.

## Fix 2: install the official agent skill pack

Cleaner than pasting, for services you'll touch repeatedly. Most RN-ecosystem tools publish agent skills (a reusable instruction pack that teaches the agent current, correct usage). The installer:

```
npx skills add expo          # Expo: native UI, EAS, API routes, updates, deployment
npx skills add clerk-skills  # Clerk: core features, Expo patterns, webhooks
npx skills add getstream-agent-skills   # Stream audio/video
npx skills add https://visionagents.ai  # Vision Agents
```

Pick the relevant skill features, your agent, and project scope. After installing, restart the dev server once.

## Fix 3: anchor on what's actually installed

When unsure which version the project uses, ask the agent to check rather than assume:

> Check the installed versions of [package] in package.json and implement against those versions' APIs. Do not follow setup steps from a different major version.

`package.json` is the ground truth. Expo-specific checks: `npx expo doctor` flags version mismatches, and `npx expo install [package]` installs the version compatible with your Expo SDK instead of the latest release. If the agent must upgrade a package, make that its own one-task prompt (see `rn-four-part-prompt`) so a version bump can't hide inside a feature.

## Signals you skipped this

- "Error loading the Metro config" or a missing peer package after a new integration. Paste the error (see `rn-fix-one-thing`); the missing package is usually the split-out engine of a newer library version.
- A service integration that "almost works". Usually stale training data, not your mistake.
- The agent writing config from an older version's file layout. Paste the docs and re-run the prompt; the fix is one message.