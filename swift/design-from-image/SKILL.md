---
name: design-from-image
description: Getting SwiftUI screens to match a design by attaching images instead of describing them, and iterating with screenshots of the real app. Use when building or correcting any visual screen, when the user has a design, mockup, screenshot, or reference image, when output "doesn't look like the design", when setting up a design system (colors, fonts, spacing) for a Swift app, or when generating app assets. Trigger on "make it look like this", "match the design", "it doesn't look right", "the layout is off", "design system", "colors and fonts", or any UI work with a visual reference.
---

# Design from image

Agents read layouts from images better than they read descriptions of layouts. One attached image saves three rounds of you explaining margins. This skill covers the whole visual loop: get a design, hand it over, and correct against the real rendered app.

## Step 1: have a design to attach

If there's no design yet, make one first. Options, in rough order of quality:

- Any design tool output, Figma screenshot, or a napkin photo. Imperfect beats absent.
- An image model can generate a full design system sheet from a description of how the app should feel: a few primary colors, semantic colors (success, warning), neutrals, and text sizes. That single sheet is enough for an agent to build the entire token layer from.
- App icons and illustration assets can be generated the same way. Generate them before the screens that use them, then commit them to the asset catalog so prompts can point at them by name.

## Step 2: attach, don't describe

Every visual prompt carries the image:

> Read AGENTS.md first and follow it strictly.
> Implement the onboarding screen exactly as shown in the attached design, using assets from Assets.xcassets. Add a navigation link from the home screen to open it.
> Do not include the pagination dots shown in the design.
> [design image attached]

Two details that matter:

- **"Exactly as shown"** plus a **leave-out constraint** for anything you don't want. Agents otherwise add plausible extras (dots, cards, badges) that aren't in the design.
- **Name the assets** to use, so the agent doesn't substitute placeholders.

## Step 3: set up the token layer once

Before building screens, spend one prompt on the design system itself:

> Read AGENTS.md first and follow it strictly.
> Implement the design system from the attached design theme. Create design tokens (colors, text styles, spacing) in a single theme file, set up reusable text styles for each size in the design, and load the app font at launch. Ensure the app's look matches the attached theme.
> [design system image attached]

After that, screens refer to tokens instead of raw values, which keeps every screen consistent and makes restyling a one-file change. If the generated names feel clumsy, ask for simpler ones in the same session: "simplify the naming, I want to type 'h1', not 'typography__heading1'."

## Step 4: the corrective loop

The built screen will not match on the first try. The loop:

1. **Screenshot the actual app.** The real rendered screen, not your memory of it.
2. **Attach it next to the design** and name the one difference.
3. **One corrective change per prompt.** "The mascot image is cut off at the top, show it in full aspect ratio." Not four differences at once; you can't tell which prompt fixed what.
4. **Re-verify against the design**, not against the previous screenshot.

For pixel-level mismatches, crop the exact region from both screenshots and attach both crops. The narrower the evidence, the faster the fix.

## Two habits this skill prevents

- Describing layouts in words ("centered, but kind of high, with some space below") when you could attach the image. Words lose; images don't.
- Correcting from memory. Always screenshot the running app before describing what's wrong.
