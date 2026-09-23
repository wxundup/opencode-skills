---
name: practical-vibe-coding
description: The build method for AI-assisted Swift app development - one feature per session, verify each on a real running app, never batch. Use when the user starts building an app, plans features, asks what to build next, or when any multi-feature Swift/iOS build is underway. Trigger on "build my app", "add this feature", "what's next", "practical vibe coding", or any project that will grow past two screens. Apply from the very first feature, not after things break.
---

# Practical vibe coding

Two ways of building with AI both end in nothing. Pure vibe coding (type a wish, accept the output, never look) is fast for three features and collapses around feature five: one fix breaks something else, and soon the project is code nobody can explain. The opposite extreme, days of planning folders and architecture docs before a single screen exists, never ships. The middle path keeps AI speed and adds just enough structure to stay in control:

1. Build **one feature at a time**.
2. **Verify each one** against the real running app before moving on.
3. Fix what breaks with one targeted prompt.
4. Commit, then start the next feature.

The product changes per project. This loop never does.

## The loop

For every feature, in order:

1. **Name the feature.** One sentence: what the user can do after it exists. "The user can pick a language and the app remembers it." If it takes more than one sentence, split it.
2. **Write the prompt** using the four-part formula (see `four-part-prompt`): read AGENTS.md, one task, behavior constraints, references.
3. **Verify on the real artifact.** Build must succeed, then run the app in the simulator and do the thing yourself, or ask the agent to drive the simulator and screenshot the result. Reading the agent's summary is not verification; tapping the button is.
4. **Fix with one prompt** if something is wrong (see `fix-one-thing`).
5. **Commit** and open a review before merging (see `git-feature-loop`).
6. **New session** for the next feature (see `fresh-context-discipline`).

Completion criterion for the whole loop: the feature works when you try it, the build is clean, and the commit exists. "The agent says it's done" is none of those.

## Build order that keeps the AI reliable

Build in this order regardless of app type:

1. **UI shell.** Screens with layout and static text, nothing wired up.
2. **Typed sample data.** Plain Swift structs with hardcoded content (see `hardcoded-data-first`).
3. **State that persists.** What the app remembers between launches.
4. **Navigation structure.** Tab bar or flow skeleton before filling screens in.
5. **Real services last.** Auth, network, purchases. These break in ways the earlier layers don't, and each needs its own session.

Isolate navigation as its own feature: placeholder screens for every tab first, real screens later. A prompt that bundles navigation plus screen content plus animations produces output that is hard to debug and harder to keep.

## Verify against the right surface

- **Every feature:** build succeeds and the screen works in the simulator.
- **Mic, audio, camera, speech features:** a physical device. The simulator's microphone input is famously unreliable; test these on real hardware before believing them.
- **Anything with an account, payment, or push:** real device as well.

Ask the agent to build and run, or do it yourself, but the screenshot of the working feature is the bar. If the feature was about layout, look at the layout. If it was about persistence, relaunch the app and check the value survived.

## Explain what you built

You are not writing code, but you should leave each feature able to answer one question: what did this change and why does it work that way? After each feature, prompt:

> Explain each file you created or changed for this feature in simple language, including the reasoning behind the choices.

This is the learning loop. It costs one message, and it is what turns a pile of generated code into a project you can steer. When an explanation confuses you, ask again narrower: "what does this one function do, in plain words?"

## Know when to stop following recipes

Once the moving parts are familiar (a screen, a store, a service), stop copying example prompts. Try writing the next one yourself first, then compare against a reference. The real skill this method trains is guiding the agent with the context it already has, and that skill only develops when you attempt the prompt.

## Conversational features (voice, chat, tutoring)

When a feature talks back, structure the conversation or it fights itself:

- The agent and the user need **turns**. Let the agent finish, then listen. Mute the mic while the agent speaks or the device hears its own voice and responds to itself.
- A **press-and-hold to talk** button gives the user a clean turn: press stops the agent and opens the mic, release closes it. This kills echo without any clever audio processing.
- Never have the agent **praise before hearing**. Congratulation lines must fire after the user's input arrives, or the lesson feels like a recording.
- Show **status**: connecting, listening, speaking, error. The user needs to know whose turn it is.

## Where the rest of the method lives

- `four-part-prompt`, how to write each prompt.
- `agents-md-swift`, the project rules file every prompt leans on.
- `fix-one-thing`, what to do when a feature breaks.
- `fresh-context-discipline`, when to open a new session.
- `git-feature-loop`, commits, branches, review before merge.
- `design-from-image`, getting screens to look like the design.
- `hardcoded-data-first`, sample data before a backend.
- `docs-freshness`, keeping the agent on current library versions.
- `secrets-hygiene`, keys and tokens.
- `analytics-events`, seeing what users actually do.
