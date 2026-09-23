---
name: four-part-prompt
description: How to write every prompt in an AI-assisted Swift project - read AGENTS.md first, one task, behavior constraints, design or doc references. Use whenever writing, reviewing, or improving a prompt for a coding agent, or when a user's prompt produced wrong, half-right, or scope-creeping output. Trigger on "write a prompt", "it keeps breaking other things", "the output is close but wrong", "prompt template", or any feature request headed for an agent. Also use to fix prompts that merged several features into one.
---

# The four-part prompt

A prompt that works has a defined scope: what you are building right now, what you are not touching, and what rules the project file already holds. Four parts, always in this order.

## Part 1: point at the rules file

Open with:

> Read AGENTS.md first and follow it strictly.

Every single time. Agents start each session with no memory of previous ones, so these two lines force the project rules back into context before any code is written. "Strictly" matters: it marks the rules as binding rather than advisory. For Swift projects the file lives at the repo root; see `agents-md-swift`.

## Part 2: one task

State exactly one thing to build. One screen, one integration, one behavior.

Bad: "Set up SwiftData, build the onboarding screen, and add sign-in."
Good: "Implement the onboarding screen exactly as shown in the attached design."

The bad version felt efficient and is not. When something breaks across three merged tasks, you cannot tell which one caused it, so you pay for that efficiency in debugging. One task also means small diffs, which means reviews and fixes stay cheap.

## Part 3: constraints that protect what works

Every feature you already built is working. The agent has no way to know that unless you say so. Constraints are behavior statements, not code instructions. You never tell the agent how to write the code; you tell it what must stay true when it's done:

- "Preserve the existing UI exactly."
- "Keep the current tab bar and navigation intact."
- "Do not change any other screen's layout."
- "If anything is unclear, ask me before implementing."
- "Never store API keys or secrets in the app bundle."

To find your constraints, look at the app like a product owner: what could go wrong for the user, and what might the agent accidentally change that already works? Write those down. The "ask before implementing" line is the highest-value one for a non-coder: it converts silent wrong guesses into a question you can answer.

## Part 4: references

- **Visual work:** attach the design image, screenshot, or asset. Agents read layouts from images far better than from prose. One image saves three rounds of margin corrections.
- **Library work:** paste the current documentation after your instructions, separated by a clear divider (see `docs-freshness`).
- **Non-visual work:** there is nothing to attach, so describe the behavior end to end. Example for a persistence feature:

> Read AGENTS.md first and follow it strictly.
> Integrate the language selection state. Store the selected language with SwiftData so it persists across launches. If a signed-in user has no selected language, route them to the language selection screen; only after selecting should they reach the home screen. Preserve the existing UI exactly.
> Add a temporary button on the home screen to delete all stored data so I can test the flow.

That last line is the **verification utility** pattern: prompt for the infrastructure that lets you check the feature, not just the feature. Temporary debug buttons like this get removed later, and that removal is its own one-task prompt.

## Template

```
Read AGENTS.md first and follow it strictly.

[One task: exactly what to build.]

[Constraints: what must not change, what behavior must hold.]

[Reference: attached design image, or pasted docs after a divider.]
```

## A real example, assembled

> Read AGENTS.md first and follow it strictly.
> Implement the onboarding screen exactly as shown in the attached design, using the mascot image from Assets.xcassets. Add a navigation link from the home screen that opens it. Use the app name under the logo.
> Do not include the pagination dots shown in the design.
> [onboarding-design.png attached]

Short, scoped, one constraint, one reference. The agent knows what to build, what to leave out, and what it should look like.

## Fixing a prompt that already went wrong

When output is close but wrong, the fix is a more specific prompt, not a longer one. Re-check each part: is the task still one task, are the constraints about behavior, is there a reference for anything visual? Then send one corrective change at a time (see `fix-one-thing`).
