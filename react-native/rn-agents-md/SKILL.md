---
name: rn-agents-md
description: How to create and maintain an agents.md file at the root of an Expo/React Native project so every AI agent works from the same rules. Use when setting up a new React Native project with AI, when the agent keeps picking different patterns or libraries between sessions, when the user asks about agents.md or project rules files, or when a recurring mistake needs to be made permanent. Trigger on "set up my project", "the agent keeps forgetting", "different style each time", "agents.md", "project rules", "Expo project setup".
---

# agents.md for Expo / React Native projects

agents.md is a plain markdown file at the **root** of the repo (next to app/ and package.json). Coding agents read it before anything else in every session. It exists because agents start each session with amnesia: without this file you retype the same context into every prompt, and the one time you forget, the agent picks its own state library, its own folder convention, its own styling approach. Twenty prompts later the codebase looks like five people built it without speaking to each other.

## Write it, or have it written

Either write it yourself or ask an agent:

> Create an agents.md for this Expo project. Read the project files first, then write a role, an app overview, the stack with one line per tool, the folder structure, and working rules. Keep it short and factual.

Expect to edit it by hand afterward. It is a reflection of your decisions, not a template to copy. A simple notes app might need only a role, an overview, and a few rules.

## The sections

```markdown
# Role

You are an expert React Native + Expo engineer. You write clean,
simple code. Clarity over abstraction. Think like a senior mobile
developer, implement like someone building a focused product.

# Overview

[App name] is a [what it does] for [who uses it]. One paragraph.

# Stack

- Expo + React Native with TypeScript
- Expo Router: navigation (file-based routes in app/)
- NativeWind (Tailwind CSS): styling via className
- Zustand: state management
- AsyncStorage: local persistence
- [Auth service]: sign in and user management

# Development philosophy

- Build feature by feature. Understand the request before coding.
- Build the smallest useful version first.
- Only refactor when repetition or complexity appears.

# Architecture

- app/: routes and layouts (screens live here)
- components/: reusable UI
- store/: Zustand stores
- data/: types and hardcoded content
- constants/: theme tokens, image exports

# Patterns

- Styling: Tailwind className wherever NativeWind supports it.
  StyleSheet only for components where className does not work
  (SafeAreaView, Modal, TextInput).
- Check the installed NativeWind version in package.json before
  configuring anything.
- After changes, check for type errors before reporting done.
- No new libraries without asking first.

# Rules

- If anything is unclear, ask before implementing.
- If a new library would clearly simplify something, recommend it
  with the reason and wait for approval. e.g. "this could be done
  manually, but Reanimated would make the animation smoother.
  Add it?"
- Check for type errors after changes.
- Never add secrets or API keys to the mobile app.
```

**Role first.** A general-purpose agent gives general-purpose answers. Naming the role filters every suggestion that follows.

**Stack, one line each.** This is what stops the agent from reaching for a different state library in session nine.

**When the agent may propose libraries.** Let the agent think but not act: recommend with a reason, wait for approval. Agents thrive on examples, so include one.

## The living-file rule

You do not write this perfectly up front. Write what you know, then update it when something keeps coming up. The moment the agent makes the same mistake twice, or you find yourself repeating the same instruction across prompts, add it once:

> Add to agents.md under Patterns: [the rule, stated once, plainly].

Solve it once, document it, never deal with it again. Real examples worth their slot: "NativeWind's className prop does not work on SafeAreaView, use StyleSheet constants for it", "use the installed NativeWind version from package.json, not the docs' latest", "images come from constants/images via the centralized exports".

## Keep it committed

Commit agents.md to the repo. It is not private scratch; pushing it means any agent, any tool, any teammate (or future you) starts every session with the same context instead of re-deriving it.