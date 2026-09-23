---
name: hardcoded-data-first
description: Build Swift app features against typed, hardcoded sample data before any backend or database, then swap the source later. Use when starting a new app, planning data models, before integrating a backend, when the user says "I'll add a database later", "use fake data", or "hardcode it for now", or when screens need content the backend can't provide yet. Trigger on "set up the data", "content", "sample data", "dummy data", "backend", "database", "SwiftData", "should the data come from a server".
---

# Hardcoded data first

For an MVP, skip the database. Write the app's content as typed Swift data files and build every feature against them. The screen becomes a rendering layer on top of predictable structures, which makes the AI's work dramatically more consistent: it knows exactly what shape everything has, so it stops inventing shapes.

This is not a shortcut you'll regret. Building UI and features with hardcoded data first, then wiring a real backend once the shapes are settled, is how experienced developers build for speed and iteration. You also get to change the data model ten times in an afternoon, because nothing but files needs to move.

## The pattern

One file for types, one file per content area:

```swift
// Types.swift
enum LanguageCode: String, Codable, CaseIterable {
    case spanish, french, japanese, german
}

struct Lesson: Codable, Identifiable {
    let id: String
    let title: String
    let goal: String
    let vocabulary: [VocabularyItem]
    let xpReward: Int
}

struct VocabularyItem: Codable, Identifiable {
    let id: String
    let word: String
    let translation: String
    let pronunciation: String
}
```

```swift
// LessonsData.swift
let spanishLessons: [Lesson] = [
    Lesson(id: "es-1", title: "Greetings",
           goal: "Say hello and goodbye",
           vocabulary: [
               VocabularyItem(id: "es-1-1", word: "hola",
                              translation: "hello",
                              pronunciation: "OH-lah")
           ],
           xpReward: 10),
    // ...
]
```

The prompt that generates this should be explicit about structure, because you, not the agent, know what the app is about:

> Read AGENTS.md first and follow it strictly.
> Create the content system as typed Swift data: types file plus data files for languages, units, and lessons. Define supported languages, units, lessons with goals, vocabulary items with translations and pronunciations, and XP rewards. Include a small beginner-friendly sample set for two or three languages. Keep it simple, typed, and easy to extend.

Notice this prompt is about **what the app contains**, not what code to write. That is the point of this stage: you are deciding the product's shape, and typed structures are how that decision becomes real.

## Rules that keep it extensible

- **Type everything.** Enums for closed sets (language codes, lesson activity types). Structs with `Codable` for everything else. When you add a language later, you add a case and a data file, not a refactor.
- **Images by URL when possible.** A CDN URL (like flag images keyed by country code) extends to any content without shipping assets.
- **Sample content, small and real.** Two or three entries per collection, each fully filled in. Fake filler ("Lorem ipsum") hides bugs; real-shaped samples surface them.
- **One prompt to extend.** "Add three more lessons for French in the same structure and style" works precisely because the structure exists.

## Swapping in the real thing later

When the shapes are settled and features work, the swap is a bounded job, not a rewrite:

- **Local persistence:** SwiftData models mirroring the structs, or `@AppStorage` for single values.
- **Backend:** a fetch layer that returns the same types, so screens change nothing.

Prompt it as its own feature: "Replace the hardcoded lesson data with SwiftData, keeping the same types and all existing screens working." Preserve-the-UI constraints apply as always (see `four-part-prompt`).

## What this pairs with

- `practical-vibe-coding` puts this at build-order step 2, before state and services.
- `analytics-events` tracks what users do with the content once it's real.
