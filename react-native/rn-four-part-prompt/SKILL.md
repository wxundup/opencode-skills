---
name: rn-four-part-prompt
description: How to write every prompt in an AI-assisted React Native/Expo project - read agents.md first, one task, behavior constraints, design or doc references. Use whenever writing, reviewing, or improving a prompt for a coding agent working on Expo/React Native code, or when a user's prompt produced wrong, half-right, or scope-creeping output. Trigger on "write a prompt", "it keeps breaking other things", "the output is close but wrong", "prompt template", or any RN feature request headed for an agent. Also use to fix prompts that merged several features into one.
---

# The four-part prompt for React Native

A prompt that works has a defined scope: what you are building right now, what you are not touching, and what rules the project file already holds. Four parts, always in this order.

## Part 1: point at the rules file

Open with:

> Read agents.md first and follow it strictly.

Every single time. Agents start each session with no memory of previous ones, so these two lines force the project rules back into context before any code is written. "Strictly" matters: it marks the rules as binding rather than advisory. The file lives at the repo root (next to app/, not inside it); see `rn-agents-md`.

## Part 2: one task

State exactly one thing to build. One screen, one integration, one behavior.

Bad: "Set up NativeWind, build the onboarding screen, and add authentication."
Good: "Implement the onboarding screen exactly as shown in the attached design."

The bad version felt efficient and is not. When something breaks across three merged tasks, you cannot tell which one caused it. One task also means small diffs, which means reviews and fixes stay cheap.

## Part 3: constraints that protect what works

Every feature you already built is working. The agent has no way to know that unless you say so. Constraints are behavior statements, not code instructions. You never tell the agent how to write the code; you tell it what must stay true when it's done:

- "Preserve the existing UI exactly."
- "Keep the existing audio flow intact."
- "Do not change the tab bar or navigation."
- "If anything is unclear, ask me before implementing."
- "Do not expose any secrets in the mobile app."

To find your constraints, look at the app like a product owner: what could go wrong for the user, and what might the agent accidentally change that already works? Write those down. The "ask before implementing" line is the highest-value one for a non-coder: it converts silent wrong guesses into a question you can answer.

## Part 4: references

- **Visual work:** attach the design image, screenshot, or asset. Agents read layouts from images far better than from prose. One image saves three rounds of margin corrections.
- **Library work:** paste the current documentation after your instructions, separated by a clear divider (see `rn-docs-freshness`). Most doc sites have a "copy page as markdown" button; use it.
- **Non-visual work:** describe the behavior end to end. Example for a persistence feature:

> Read agents.md first and follow it strictly.
> Integrate the language selection state. Store the selected language using Zustand with React Native Async Storage. If an authenticated user has no selected language, route them to the language selection screen; only after selecting should they reach the home route. Preserve the existing UI exactly.
> Add a temporary button on the home screen to clear AsyncStorage so I can test the flow.

That last line is the **verification utility** pattern: prompt for the infrastructure that lets you check the feature, not just the feature. Temporary debug buttons get removed later, and that removal is its own one-task prompt.

## Template

```
Read agents.md first and follow it strictly.

[One task: exactly what to build.]

[Constraints: what must not change, what behavior must hold.]

[Reference: attached design image, or pasted docs after a divider.]
```

## A real example, assembled

> Read agents.md first and follow it strictly.
> Implement the onboarding screen exactly as shown in the attached design, using assets from the assets folder. Add a navigation link on the home route to open the onboarding screen. Use the mascot image for the top logo alongside the app name.
> Do not include the pagination dots.
> [onboarding-design.png attached]

Short, scoped, one constraint, one reference. The agent knows what to build, what to leave out, and what it should look like.

## Fixing a prompt that already went wrong

When output is close but wrong, the fix is a more specific prompt, not a longer one. Re-check each part: is the task still one task, are the constraints about behavior, is there a reference for anything visual? Then send one corrective change at a time (see `rn-fix-one-thing`).