---
name: agents-md-swift
description: How to create and maintain an AGENTS.md file at the root of a Swift/Xcode project so every AI agent works from the same rules. Use when setting up a new Swift project with AI, when the agent keeps picking different patterns or libraries between sessions, when the user asks about AGENTS.md or CLAUDE.md or project rules files, or when a recurring mistake needs to be made permanent. Trigger on "set up my project", "the agent keeps forgetting", "different style each time", "AGENTS.md", "project rules".
---

# AGENTS.md for Swift projects

AGENTS.md is a plain markdown file at the **root** of the repo (next to the .xcodeproj, not inside a subfolder). Coding agents read it before anything else in every session. It exists because agents start each session with amnesia: without this file you retype the same context into every prompt, and the one time you forget, the agent picks its own state library, its own folder convention, its own styling approach. Twenty prompts later the codebase looks like five people built it without speaking to each other.

## Write it, or have it written

Either write it yourself or ask an agent:

> Create an AGENTS.md for this Swift project. Read the project files first, then write a role, an app overview, the stack with one line per tool, the folder structure, and working rules. Keep it short and factual.

Expect to edit it by hand afterward. It is a reflection of your decisions, not a template to copy. A simple notes app might need only a role, an overview, and a few rules.

## The sections

```markdown
# Role

You are an expert SwiftUI engineer. You write clean, simple code.
Clarity over abstraction. Think like a senior mobile developer,
implement like someone building a focused product.

# Overview

[App name] is a [what it does] for [who uses it]. One paragraph.

# Stack

- SwiftUI, iOS 17+, all UI
- SwiftData, local persistence
- [Auth service], sign in and accounts
- [Networking lib], API calls

# Architecture

- App/, entry point, root views
- Features/<Name>/, one folder per feature: views, models, logic
- Shared/, components and helpers used by multiple features
- Data/, sample data and content

# Patterns

- One screen per view file. Subviews stay private inside it.
- State lives at the lowest view that needs it; pass down as bindings.
- Navigation through [NavigationStack / your choice].
- No third-party dependencies without asking first.

# Rules

- If anything is unclear, ask before implementing.
- After changes, build with xcodebuild and fix every error before reporting done.
- Use the exact package versions already in the project.
- Never add secrets or API keys to the app bundle.
```

**Role first.** A general-purpose agent gives general-purpose answers. Naming the role filters every suggestion that follows. Ask yourself: who would you want building this, what do they care about, what do they avoid? Write that down.

**Stack, one line each.** This is what stops the agent from suggesting a different persistence layer in session nine.

**When the agent may propose libraries.** The strongest version of this rule lets the agent think but not act: "If a new dependency would clearly simplify something, recommend it with the reason, and wait for my approval." Agents thrive on examples, so include one: "e.g. 'this could be done manually, but <library> would make the animation smoother. Add it?'"

## The living-file rule

You do not write this perfectly up front. You write what you know, then you update it when something keeps coming up. The moment the agent makes the same mistake twice, or you find yourself repeating the same instruction across prompts, add it to AGENTS.md once:

> Add to AGENTS.md under Patterns: [the rule, stated once, plainly].

Solve it once, document it, never deal with it again. Real examples worth their slot: "StatusBar/safe-area insets need explicit padding in this layout", "this design system's colors live in Theme.swift, use them instead of raw Color values", "the app is portrait only".

## Keep it committed

Commit AGENTS.md to the repo. It is not private scratch; pushing it means any agent, any tool, any teammate (or future you) starts every session with the same context instead of re-deriving it.
