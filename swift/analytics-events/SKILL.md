---
name: analytics-events
description: Instrument a Swift app with event tracking so you can see what users actually do, then read the data with funnels and natural-language questions. Use when the user wants to know how users use the app, before or after launch, when adding analytics (PostHog and similar), when naming events, when setting up a dashboard, or when deciding what to build next based on usage. Trigger on "analytics", "tracking", "how do I know if people use it", "retention", "funnel", "dashboard", "PostHog", "what should I build next".
---

# Analytics events

Without analytics you're guessing what users do: which screens they visit, where they give up, which feature is the one they wanted. A handful of events answers those questions, and modern analytics tools let you ask for the answers in plain language.

## The setup shortcut

Services like PostHog offer a setup wizard that reads your project and does the wiring itself (SDK install, provider setup, screen tracking, and first events). It's a single terminal command copied from their docs, then the wizard proposes an event plan and implements it. Accept the screen tracking and auto-capture defaults; add custom events for the moments that matter, below.

## The three events worth wiring first

The video's pattern: name **what fires, when in the user flow it fires, and what properties go with it**, then let the agent find the right places in the code. You never specify files.

For any app with an account and a core action, instrument:

1. **The onboarding funnel.** `app_opened`, `onboarding_completed`, `core_action_started`. For a language app: opened, language selected, lesson started. This funnel's drop-offs tell you what to fix first.
2. **The key action with properties.** `lesson_started` with `lesson_id` and `language`; `language_selected` with `language_name`. Properties are how you slice later ("do Spanish learners finish more lessons than French learners?").
3. **Abandonment.** Record when the user enters the core screen and how long they stay; capture `lesson_abandoned` with `duration_seconds` if they leave early. Nine seconds of attendance versus three is the difference between a screen that works and one that doesn't.

The prompt for each:

> Read AGENTS.md first and follow it strictly.
> Using the initialized PostHog instance (don't re-initialize anything), capture these events: [event name] when [trigger moment], with properties [list]. Also identify signed-in users with their account ID after sign-in and sign-up. Preserve existing behavior exactly.

User identification matters: it connects sessions into a person, which turns "57 events happened" into "3 people tried the app and only 1 got to the lesson".

## Reading the data

You don't build dashboards by hand. In PostHog's chat (or any AI analytics tool), ask in plain language:

> Build me a dashboard called "Core Engagement". Add: an onboarding funnel from app opened to language selected to lesson started, with conversion between steps. A breakdown of language_selected events grouped by language. Average time before lesson abandonment. Most started lessons, top ten. Daily active users and weekly retention.

It figures out the event names from context and builds the charts. Typical first findings, straight from this workflow: the funnel loses most people before the core action (fix onboarding first), one lesson is far more popular than the rest (make more like it), and abandonment times reveal which screens feel broken.

## What to do with the answer

The point of analytics is the next build decision, which loops back into `practical-vibe-coding`: the funnel says onboarding loses 60% of users, so the next feature is fixing onboarding, not the feature you were most excited about. Let the data pick.
