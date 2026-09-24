---
name: rn-git-feature-loop
description: The commit, branch, and review rhythm for AI-built Expo/React Native projects - commit per feature, develop on a branch, open a pull request, run an AI review, merge only after review. Use when committing RN work, when the user asks about git, branches, PRs, or backups, after finishing any feature (see rn-practical-vibe-coding), when the user worries about losing work or breaking the app, or when setting up a new project repo. Trigger on "commit", "save my work", "push", "pull request", "merge", "backup", "branch", "review my code", "is it safe".
---

# The git feature loop for React Native

Version control is the safety net that makes AI-built code safe to accept. One commit per feature means every state of the app is recoverable, and a review before each merge is what catches the mistakes AI code makes: inconsistencies with your rules, broken edge cases, leaked secrets.

## The rhythm

After each feature passes verification (see `rn-practical-vibe-coding`):

1. **Commit** with a message naming the feature:
   ```
   git add . && git commit -m "implement onboarding screen"
   git push
   ```
   Or just ask the agent: "Commit all changes with the message: implement onboarding screen."
2. **Work on a dev branch.** Make one early: `git checkout -b dev`. All features land there.
3. **Open a pull request** from dev to main on GitHub (compare the branches, open a PR).
4. **Run an AI code review** on the PR. CodeRabbit is the strong default: install it from the GitHub marketplace, give it access to the repo, and it reviews every PR automatically with a summary, a walkthrough of what changed across files, and actionable comments ranked by severity. The CodeRabbit VS Code extension also reviews uncommitted changes without a PR.
5. **Fix the findings.** Review comments work as prompts: copy the comment, paste it to the agent with "apply this suggestion", or use the one-click "fix with AI" button.
6. **Merge** once the review is clean or findings are addressed.

For features that touch auth, tokens, or real-time data, do both review passes: quick review in the editor while working, then the PR review before merge.

## What the review catches

Real examples from AI-built RN projects, all found by review rather than by a crash:

- **Leaked secrets.** A `.env` file pushed to the repo. Critical, and invisible to you (see `rn-secrets-hygiene` for the response).
- **Hydration races.** Redirect logic reading a Zustand store before AsyncStorage has rehydrated, which sends every user to the wrong screen on cold start. The fix: wait for the persist hydration lifecycle before checking. You would only find this by testing relaunch timing; the review flags the pattern.
- **Security holes in API routes.** An Expo API route that mints tokens from an untrusted query parameter without checking who is calling. This is a real impersonation vulnerability class AI code produces, and the reviewer catches it.
- **Rule drift.** StyleSheet styles where Tailwind classes should go, or an import the agent guessed wrong. The reviewer reads your agents.md and calls out deviations.
- **Division-by-zero and nil traps.** A progress bar that becomes NaN when a daily goal is zero. One-line fixes, found before users hit them.
- **Content errors.** Wrong phrases or typos in sample data, which a human skims past and an AI reviewer reads closely.

## First-time repo setup

```
git init && git add . && git commit -m "initial project setup"
```

Then create the repo on GitHub (github.com/new) and push, or hand the whole setup to the agent with the repo URL. Commit agents.md in this first push so every future session starts with the rules (see `rn-agents-md`).

## One branch is enough

A solo project needs exactly two branches: `dev` for daily commits, `main` for known-good states. The habit that matters is the commit per feature, not elaborate branch topology. If the app is broken, `git log` shows the last good feature, and rewinding to it is one command.