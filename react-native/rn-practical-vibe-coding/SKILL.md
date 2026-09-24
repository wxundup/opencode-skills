---
name: rn-practical-vibe-coding
description: The build method for AI-assisted React Native/Expo app development - one feature per session, verify each on a real running app, never batch. Use when the user starts building an Expo/React Native app, plans features, asks what to build next, or when any multi-feature mobile RN build is underway. Trigger on "build my app", "add this feature", "what's next", "practical vibe coding", "Expo", "React Native", or any project that will grow past two screens. Apply from the very first feature, not after things break.
---

# Practical vibe coding for React Native

Two ways of building with AI both end in nothing. Pure vibe coding (type a wish, accept the output, never look) is fast for three features and collapses around feature five: one fix breaks something else, and soon the project is code nobody can explain. The opposite extreme, days of planning folder structures and architecture docs before a single screen exists, never ships. The middle path keeps AI speed and adds just enough structure to stay in control:

1. Build **one feature at a time**.
2. **Verify each one** against the real running app before moving on.
3. Fix what breaks with one targeted prompt.
4. Commit, then start the next feature.

The product changes per project. This loop never does.

## The loop

For every feature, in order:

1. **Name the feature.** One sentence: what the user can do after it exists. "The user can pick a language and the app remembers it." If it takes more than one sentence, split it.
2. **Write the prompt** using the four-part formula (see `rn-four-part-prompt`): read agents.md, one task, behavior constraints, references.
3. **Verify on the real artifact.** The Expo dev server reloads live, so check the feature in Expo Go on your phone (scan the QR code from `npx expo start`) or in the simulator (`i` for iOS, `a` for Android). Reading the agent's summary is not verification; tapping the button is.
4. **Fix with one prompt** if something is wrong (see `rn-fix-one-thing`).
5. **Commit** and open a review before merging (see `rn-git-feature-loop`).
6. **New session** for the next feature (see `rn-fresh-context-discipline`).

Completion criterion for the whole loop: the feature works when you try it, the dev server runs clean, and the commit exists. "The agent says it's done" is none of those.

## Expo Go versus development builds

The testing surface changes when native code enters the project:

- **Expo Go:** enough for pure JavaScript screens, styles, navigation, state. Fastest loop; scan the QR code and get live reload on a real device.
- **Development build (`npx expo run:ios` / `npx expo run:android`):** required once any SDK ships native modules (audio, video, camera). Expo Go cannot load arbitrary native code at runtime. Same hot reload, same Expo Router, but compiled with the native modules included.

Switch to a dev build the moment you integrate something like Stream, background audio, or any module the error "requires a development build" mentions. After `expo prebuild --clean`, iOS needs signing (add your Apple account in Xcode under signing and capabilities) and Android needs the package name set in app config.

## Build order that keeps the AI reliable

Build in this order regardless of app type:

1. **Project bootstrap.** `npx create-expo-app@latest`, `npx expo start`, Expo Go on your phone, `npm run reset-project` to strip the boilerplate.
2. **agents.md** at the repo root (see `rn-agents-md`), plus agent skills for the tools you'll use (`npx skills add expo` and the packs for auth, analytics, and any real-time service).
3. **Styling setup** (NativeWind) and the design system, before any screen.
4. **UI shell.** Screens with layout and static text, nothing wired up.
5. **Typed sample data.** TypeScript data files with hardcoded content (see `rn-hardcoded-data-first`).
6. **State that persists** (Zustand plus AsyncStorage).
7. **Navigation structure.** Bottom tabs with placeholder screens before filling any of them in.
8. **Real services last.** Auth, network, real-time AI. Each gets its own session.

Isolate navigation as its own feature. A prompt that bundles navigation plus screen content plus animations produces output that is hard to debug and harder to keep.

## Verify against the right surface

- **Every feature:** reload in Expo Go or the simulator and use the screen.
- **Mic, audio, camera, speech features:** a physical device, and a dev build. The iOS simulator's WebRTC microphone connection is famously unreliable; test audio features on real hardware with `npx expo run:ios --device` before believing them.
- **Auth with social providers:** real device too. Google and Apple sign-in open browser sessions the simulator handles poorly.

## Explain what you built

After each feature, prompt:

> Explain each file you created or changed for this feature in simple language, including the reasoning behind the choices.

This is the learning loop for a non-coder. It costs one message and turns generated code into a project you can steer. When an explanation confuses you, ask again narrower: "what does this one function do, in plain words?"

## Know when to stop following recipes

Once the moving parts are familiar (a screen, a store, an API route), stop copying example prompts. Try writing the next one yourself first, then compare against a reference. The real skill this method trains is guiding the agent with the context it already has.

## Conversational features (voice, chat, tutoring)

When a feature talks back, structure the conversation or it fights itself:

- The agent and the user need **turns**. Mute the mic while the AI teacher speaks, or the device hears its own voice through the speaker and responds to itself in a loop.
- A **press-and-hold to talk** button gives the user a clean turn: press stops the agent and opens the mic, release closes it. This kills echo without clever audio processing.
- Never have the agent **praise before hearing**. Congratulation lines must fire after the user's input arrives, or the lesson feels like a recording.
- Show **status** everywhere: connecting, listening, speaking, error. The user needs to know whose turn it is.

## Where the rest of the method lives

- `rn-four-part-prompt`, how to write each prompt.
- `rn-agents-md`, the project rules file every prompt leans on.
- `rn-fix-one-thing`, what to do when a feature breaks.
- `rn-fresh-context-discipline`, when to open a new session.
- `rn-git-feature-loop`, commits, branches, review before merge.
- `rn-design-from-image`, getting screens to look like the design.
- `rn-hardcoded-data-first`, sample data before a backend.
- `rn-docs-freshness`, keeping the agent on current library versions.
- `rn-secrets-hygiene`, keys and tokens.
- `rn-analytics-events`, seeing what users actually do.