---
name: rn-secrets-hygiene
description: Keep API keys, tokens, and secrets out of an Expo/React Native app bundle and out of git, and respond correctly when one leaks. Use when integrating any service with an API key into an RN app (auth, AI, analytics, payments, maps, audio), when setting up .env config for Expo, when a review flags exposed secrets, or when the user pastes a key anywhere in chat. Trigger on "API key", "secret", "where do I put the key", ".env", "EXPO_PUBLIC", "environment variables", "it got leaked", "exposed", "the key is in the repo".
---

# Secrets hygiene for Expo / React Native

AI-generated code will happily embed an API key wherever it compiles. In a mobile app that key ships to every user's device, and in a pushed `.env` it sits in git history forever. Reviews catch this (see `rn-git-feature-loop`), but the habit prevents it.

## The rule

**Anything that must stay secret never goes in the app bundle or the repo.** Secrets include API keys and signing secrets for third-party services (Stream secrets, OpenAI keys, Clerk secrets). What's fine in the app: public client keys, public config values, user-facing strings. What never is: any key a dashboard labels "secret".

Two tiers in practice:

1. **Client-safe values** live in `.env` at the project root with the `EXPO_PUBLIC_` prefix and are readable in the app through Expo Constants. They are baked into the bundle and visible to anyone who unpacks the app, so only put values there that are safe to be public.
2. **True secrets** (service keys and secrets) live only on a server you control. For Expo apps, that means an **Expo API route**: the app requests a token from your own endpoint, which holds the secret, validates the authenticated caller, and returns a short-lived token. This is the standard pattern for real-time services. The app never mints tokens itself. If an API route needs the caller's identity, it must verify their authenticated session (their token against the auth provider), never trust a user ID sent in the request. An unauthenticated token endpoint lets anyone impersonate any user.

## Setup

1. Put values in `.env` at the project root. Client-safe values get `EXPO_PUBLIC_` prefixes; true secrets get their own `.env` inside the server folder (for example, the Vision Agent service directory), never the app's `.env`.
2. Confirm `.env` is in `.gitignore` before the first commit.
3. Commit a `.env.example` with the key names and empty values, so future sessions know what config exists without seeing values.

Prompt the agent the same way every time (see `rn-four-part-prompt`, constraints part): "Do not expose any secrets in the mobile app. Keep the existing flow intact."

## When one leaks

If `.env` ends up in a commit (reviews catch this often), deleting the file is not enough; it stays in git history. The response, in order:

1. **Rotate the keys first.** Once a key has been in a pushed repo, treat it as compromised. Generate new ones in each service's dashboard, update your `.env`, retire the old ones.
2. **Remove it from tracking** so it doesn't return:
   ```
   git rm --cached .env
   git commit -m "remove .env from tracking"
   ```
   Or click the reviewer's fix button, which hands these commands to the agent.
3. **Check exposure.** If the repo was public or shared for any real time, rotation is mandatory, not optional. For private solo repos with a brief exposure, rotation is still the safe default.

Reviews will also flag "rotate your environment variables" as a follow-up; do it even though the demo still works.

## Bonus checks the review pass covers

- **Error handling on social auth.** Google and Apple sign-in that fails silently. Also the classic: the first SSO attempt erroring because expo-auth-session and expo-web-browser are missing. Paste the error (see `rn-fix-one-thing`) and install what it names.
- **Fail fast on missing env vars.** Server scripts should check required keys at startup and exit with a clear message, not crash mid-request.
- **Mock removal.** When real auth replaces a mocked flow, the mock code and its test utilities (like a temporary clear-storage button) should go, not linger behind a dead branch.