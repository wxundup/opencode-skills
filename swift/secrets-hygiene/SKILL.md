---
name: secrets-hygiene
description: Keep API keys, tokens, and secrets out of a Swift app's bundle and out of git, and respond correctly when one leaks. Use when integrating any service with an API key (auth, AI, analytics, payments, maps), when setting up environment config for a Swift project, when a review flags exposed secrets, or when the user pastes a key anywhere in chat. Trigger on "API key", "secret", "where do I put the key", "environment variables", ".env", "xcconfig", "it got leaked", "exposed", "the key is in the repo".
---

# Secrets hygiene

AI-generated code will happily embed an API key wherever it compiles. That key then ships to every user's device and sits in your git history forever. Reviews catch this (see `git-feature-loop`), but the habit prevents it.

## The rule

**Anything that must stay secret never goes in the app bundle or the repo.** Secrets include API keys and signing secrets for third-party services. What's fine in the app: public configuration values, and user-facing strings. What never is: any key a dashboard labels "secret".

Two tiers in practice:

1. **Client-safe keys** (some services issue publishable keys) can live in the app, kept out of git via a config file.
2. **True secrets** (service secrets, signing keys) live only on a server you control. If a feature needs one, the app calls your own endpoint, which holds the secret and returns what the app needs (like a short-lived token). This is the standard pattern for auth tokens: the app never mints tokens itself, it asks a server to.

## Setup for a Swift project

Keep keys in a git-ignored file and exclude it from version control:

1. Put values in a local file (`.env` for script tooling, or an `.xcconfig` for build settings).
2. Add the filename to `.gitignore` before the first commit.
3. Commit a `.env.example` with the key names and empty values, so future sessions know what config exists without seeing values.

Prompt the agent the same way every time (see `four-part-prompt`, constraints part): "Do not include any secrets or API keys in the app bundle. Keep the existing UI and flow intact."

## When one leaks

If a key ends up in a commit (reviews catch this often), deleting the file is not enough; it stays in git history. The response, in order:

1. **Rotate the key first.** Once a key has been in a public or shared repo, treat it as compromised. Generate a new one in the service's dashboard, update your config, retire the old one.
2. **Remove it from tracking** so it doesn't return:
   ```
   git rm --cached .env
   echo ".env" >> .gitignore
   git commit -m "remove .env from tracking"
   ```
   Or hand those commands to the agent.
3. **Check exposure.** If the repo was public or shared for any real time, rotation is mandatory, not optional. For private solo repos with a brief exposure, rotation is still the safe default.

Reviews will also flag "rotate the key" as a follow-up; do it even though the demo still works. Keys are free to rotate now and expensive to rotate after someone finds them.

## Bonus checks the review pass covers

- **Identity binding.** If an endpoint generates tokens or data for a user, it must verify who is asking (from their authenticated session), not trust a user ID sent in the request. An unauthenticated token endpoint lets anyone impersonate any user; this is a real vulnerability class that AI code produces and reviews catch.
- **Error handling on auth flows.** Social sign-in that fails silently. Reviews flag missing error paths on exactly these flows.
- **Missing dependencies surfaced by errors.** "AuthSession is required for SSO" style errors: paste the error to the agent (see `fix-one-thing`), install what it names, rebuild.
