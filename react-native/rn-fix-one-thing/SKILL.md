---
name: rn-fix-one-thing
description: How to fix a broken feature in an AI-built Expo/React Native project with one targeted prompt instead of spiraling. Use when anything breaks, when dev-server or build errors appear, when the agent's last change broke something that worked, when pasting Metro/Expo/error output, or when the user reports "it was working and now it isn't". Trigger on "it's broken", "fix this error", "this broke", "something's wrong", "it crashes", "the build fails", "expo error", or any bug report about an RN app under AI development.
---

# Fix one thing

Things break even with a good method. That is not the method failing; that is software. What separates a controlled project from a spiraling one is the shape of the fix:

**One problem, one fix, one verification.**

## The shape of a fix prompt

> [The problem, observed on the real app.] It should [the correct behavior]. Don't change [what must stay untouched].

Example:

> The verification modal appears behind the keyboard on iOS. It should sit above the keyboard. Don't change any other modal behavior or layout.

Three sentences. You do not re-explain the feature. You do not paste the codebase. You state the problem, state the correct behavior, and add a constraint to keep everything else untouched.

## Paste the exact error

When there is an error message, copy all of it verbatim into the prompt:

> The app throws this error: [full error text]. Analyze what's causing it and fix only that.

Exact text carries the file, the line, and the reason. Summarizing it ("something about Zustand") throws away the information the agent needs and invites it to guess. This is doubly valuable in React Native, where runtime errors are loud and specific: a red screen in Expo Go, a terminal stack trace from the Metro bundler, or an Xcode build error each name their culprit. Real examples from AI-built RN apps, all fixed in under a minute by pasting the error:

- "Clerk: AuthSession and expo-web-browser are required for SSO. Install them." Paste it; the agent installs the two packages.
- "Syntax error in Zustand middleware, import.meta not present in babel config." Paste it; the agent creates the babel config with the polyfill.
- "Cannot leave the call that has already been left." Paste it; the fix was two lines guarding a cleanup that ran twice.

## Where to send it

If the break is directly tied to the feature you were just building, stay in the **same session** so the agent still holds that context (see `rn-fresh-context-discipline`). If the break is unrelated to what you were doing, open a **new session** so old context can't leak in.

## After the fix

Verify the same way the feature was verified before: reload the app (`r` in the Expo terminal) and use the screen. For anything native-module related, restart the dev server entirely, sometimes with `npx expo start --clear` to wipe the cache. Then confirm the fix didn't disturb its neighbors:

1. The fixed behavior works.
2. The thing next to it still works.
3. Commit with a message naming the fix: "fix: sheet hidden behind keyboard".

If a fix made things worse instead of better, stop iterating forward. Rewind to the checkpoint before the fix (see `rn-fresh-context-discipline`) and re-describe the problem differently. Three failed attempts in a row means the description of the problem is wrong, not the agent's effort.

## Two mistakes this skill prevents

- **The batch fix.** "Fix the login error, the layout overflow, and the typo" produces three changes where any one can be the next break, and you can't tell which. Send three prompts.
- **The explain-everything fix.** Retelling the whole feature history wastes context and dilutes the actual problem. The agent can read the code; you only need to point at the difference between observed and expected behavior.