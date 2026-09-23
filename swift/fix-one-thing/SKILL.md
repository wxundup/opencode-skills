---
name: fix-one-thing
description: How to fix a broken feature in an AI-built Swift project with one targeted prompt instead of spiraling. Use when anything breaks, when build errors appear, when the agent's last change broke something that worked, when pasting crash logs or error output, or when the user reports "it was working and now it isn't". Trigger on "it's broken", "fix this error", "this broke", "something's wrong", "it crashes", "the build fails", or any bug report about an app under AI development.
---

# Fix one thing

Things break even with a good method. That is not the method failing; that is software. What separates a controlled project from a spiraling one is the shape of the fix:

**One problem, one fix, one verification.**

## The shape of a fix prompt

> [The problem, observed on the real app.] It should [the correct behavior]. Don't change [what must stay untouched].

Example:

> The verification sheet appears behind the keyboard on iPhone. It should sit above the keyboard. Don't change any other sheet behavior or layout.

Three sentences. You do not re-explain the feature. You do not paste the codebase. You state the problem, state the correct behavior, and add a constraint to keep everything else untouched.

## Paste the exact error

When there is an error message, copy all of it verbatim into the prompt:

> The build fails with this error: [full error text]. Analyze what's causing it and fix only that.

Exact text carries the function name, the line, and the reason. Summarizing it ("something about SwiftData") throws away the information the agent needs and invites it to guess.

## Where to send it

If the break is directly tied to the feature you were just building, stay in the **same session** so the agent still holds that context (see `fresh-context-discipline` for the full rule). If the break is unrelated to what you were doing, open a **new session** so old context can't leak in.

## After the fix

Verify the same way the feature was verified before: build succeeds, then use the app. Then confirm the fix didn't disturb its neighbors:

1. The fixed behavior works.
2. The thing next to it still works.
3. Commit with a message naming the fix: "fix: sheet hidden behind keyboard".

If a fix made things worse instead of better, stop iterating forward. Rewind to the checkpoint before the fix (see `fresh-context-discipline`) and re-describe the problem differently. Three failed attempts in a row means the description of the problem is wrong, not the agent's effort.

## Two mistakes this skill prevents

- **The batch fix.** "Fix the login error, the layout overflow, and the typo" produces three changes where any one can be the next break, and you can't tell which. Send three prompts.
- **The explain-everything fix.** Retelling the whole feature history wastes context and dilutes the actual problem. The agent can read the code; you only need to point at the difference between observed and expected behavior.
