---
name: rn-fresh-context-discipline
description: When to open a new agent session versus continue the current one in Expo/React Native projects, and how to rewind when a change made things worse. Use when starting or continuing coding agent sessions on RN code, when outputs seem confused or drift from instructions, when token usage climbs, when the user asks whether to start a new chat, or when a change needs undoing. Trigger on "should I start a new chat", "it seems confused", "it keeps forgetting", "undo that", "revert", "rewind", "context window", "this session is a mess".
---

# Fresh context discipline

Agent sessions are disposable on purpose. Treat them like paper, not like a document you keep appending to.

## The rule

**New session for each feature. Same session only for work directly tied to what you just built.**

Why: everything said in a session stays in the agent's context. Half-finished thoughts, failed attempts, old file versions. As the session ages, that stale context leaks into new output: the agent "remembers" decisions you abandoned and patterns you replaced. It also pays a token cost on every turn for all of it, so old sessions are both worse and more expensive.

Concretely:

- Finished the onboarding screen and committing it? **Close the session.** Next feature, new session.
- Fixed one detail of the screen you're still on? **Stay.** That follow-up needs the current context.
- Pasting an error about something from three features ago? **New session**, and paste what the agent needs to know (see `rn-fix-one-thing`).

If you can't tell which case you're in, open a new session and carry a two-line summary of the current state. A fresh agent that reads agents.md and one sentence of context beats a stale agent holding forty messages of noise.

## Rewind instead of iterate

When a change made things worse, do not keep prompting on top of the damage. Most agent interfaces let you rewind the conversation to an earlier message, which also restores the code to that point. Rewind to the last good state, then restate the request differently:

- The first phrasing may have been ambiguous. Say the behavior in plainer words.
- Add the missing constraint: "keep the existing layout exactly as it is."
- Attach a screenshot of the correct result if one exists.

Three failed attempts on the same problem means the description is the problem. Rewind and rewrite the description rather than burning a fourth attempt.

## Cost awareness

Sessions are also the meter. Long sessions re-send their whole history on every turn. The habits that keep quality up, new session per feature, tight prompts (see `rn-four-part-prompt`), one fix per prompt (see `rn-fix-one-thing`), are the same habits that keep the bill down. There is no trade-off between doing this well and doing it cheaply.