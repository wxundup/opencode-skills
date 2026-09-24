---
name: rn-hardcoded-data-first
description: Build Expo/React Native app features against typed, hardcoded TypeScript data before any backend or database, then swap the source later. Use when starting a new RN app, planning data models, before integrating a backend, when the user says "I'll add a database later", "use fake data", or "hardcode it for now", or when screens need content the backend can't provide yet. Trigger on "set up the data", "content", "sample data", "dummy data", "backend", "database", "AsyncStorage", "should the data come from a server".
---

# Hardcoded data first

For an MVP, skip the backend. Write the app's content as typed TypeScript data files and build every feature against them. The screen becomes a rendering layer on top of predictable structures, which makes the AI's work dramatically more consistent: it knows exactly what shape everything has, so it stops inventing shapes. Local-only state (selected language, lesson progress, XP) lives on the device with Zustand plus AsyncStorage until a real backend is warranted.

This is not a shortcut you'll regret. Building UI and features with hardcoded data first, then wiring a backend once the shapes are settled, is how experienced developers build for speed and iteration. You also get to change the data model ten times in an afternoon, because nothing but files needs to move.

## The pattern

One file for types, one file per content area:

```typescript
// data/types.ts
export type LanguageCode = "es" | "fr" | "ja" | "de";

export interface Lesson {
  id: string;
  title: string;
  goal: string;
  vocabulary: VocabularyItem[];
  xpReward: number;
}

export interface VocabularyItem {
  id: string;
  word: string;
  translation: string;
  pronunciation: string;
  emoji: string;
}
```

```typescript
// data/lessons.ts
import { Lesson } from "./types";

export const spanishLessons: Lesson[] = [
  {
    id: "es-1",
    title: "Learning greetings",
    goal: "Say hello and goodbye",
    vocabulary: [
      { id: "es-1-1", word: "hola", translation: "hello",
        pronunciation: "OH-lah", emoji: "👋" },
    ],
    xpReward: 10,
  },
];
```

The prompt that generates this should be explicit about structure, because you, not the agent, know what the app is about:

> Read agents.md first and follow it strictly.
> Create the learning content system using hardcoded TypeScript data: a types file plus data files for languages, units, and lessons. Define supported languages, units, lessons with activities, vocabulary with translations and pronunciations, lesson goals, and XP rewards. Include a small beginner-friendly sample set for a few languages. Keep it simple, typed, and easy to extend.

Notice this prompt is about **what the app contains**, not what code to write. That is the point of this stage: you are deciding the product's shape, and typed structures are how that decision becomes real.

## Rules that keep it extensible

- **Type everything.** Union types for closed sets (language codes, activity types). Interfaces for entities. When you add a language later, you extend the union and add a data file, not a refactor.
- **Images by URL when possible.** A CDN URL (like flag images keyed by country code at flagcdn.com) extends to any content without shipping assets.
- **Sample content, small and real.** A few entries per collection, each fully filled in. Fake filler hides bugs; real-shaped samples surface them.
- **One prompt to extend.** "Add five more lessons for French in the same structure and style" works precisely because the structure exists.

## Swapping in the real thing later

When the shapes are settled and features work, the swap is a bounded job, not a rewrite:

- **Local persistence:** Zustand with AsyncStorage persist middleware holding the same shapes.
- **Backend:** a fetch layer that returns the same types, so screens change nothing.

Prompt it as its own feature: "Replace the hardcoded lesson data with data from [backend], keeping the same types and all existing screens working." Preserve-the-UI constraints apply as always (see `rn-four-part-prompt`).

## What this pairs with

- `rn-practical-vibe-coding` puts this at build-order step 5, before state and services.
- `rn-analytics-events` tracks what users do with the content once it's real.