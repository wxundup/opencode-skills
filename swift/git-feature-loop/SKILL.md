---
name: git-feature-loop
description: The commit, branch, and review rhythm for AI-built Swift projects - commit per feature, develop on a branch, open a pull request, run an AI review, merge only after review. Use when committing work, when the user asks about git, branches, PRs, or backups, after finishing any feature (see practical-vibe-coding), when the user worries about losing work or breaking the app, or when setting up a new project repo. Trigger on "commit", "save my work", "push", "pull request", "merge", "backup", "branch", "review my code", "is it safe".
---

# The git feature loop

Version control is the safety net that makes AI-built code safe to accept. One commit per feature means every state of the app is recoverable, and a review before each merge is what catches the mistakes AI code makes: inconsistencies with your rules, broken edge cases, leaked secrets.

## The rhythm

After each feature passes verification (see `practical-vibe-coding`):

1. **Commit** with a message naming the feature:
   ```
   git add . && git commit -m "implement onboarding screen"
   ```
   Or just ask the agent: "Commit all changes with the message: implement onboarding screen."
2. **Push** to a development branch (make one early: `git checkout -b dev`).
3. **Open a pull request** from the dev branch to main on GitHub.
4. **Run an AI code review** on the PR (CodeRabbit is the common one; install it from the GitHub marketplace, it reviews automatically). In an editor, a review extension can also review uncommitted changes without a PR.
5. **Fix the findings.** Review comments work as prompts: copy the comment, paste it to the agent with "apply this suggestion", or use the reviewer's one-click fix.
6. **Merge** once the review is clean or findings are addressed.

For features that touch auth, data, or payments, do both review passes: quick review in the editor while working, then the PR review before merge.

## What the review catches

Real examples from AI-built projects, all found by review rather than by a crash:

- **Leaked secrets.** A `.env` file pushed to the repo. Critical, and invisible to you (see `secrets-hygiene` for the response).
- **Logic that runs before it should.** Redirect logic reading a store before its saved data has loaded, which sends every user to the wrong screen on cold start. You would only find this by testing relaunch timing; the review flags the pattern.
- **Rule drift.** A component styled in an inconsistent way after the agent "helpfully" restyled it. The reviewer reads your AGENTS.md and calls out deviations.
- **Division-by-zero and nil traps.** A progress bar that becomes NaN when a goal is zero. One-line fixes, found before users hit them.
- **Content errors.** Wrong text in sample data, which a human reviewer skims past and an AI reviewer reads.

## First-time repo setup

```
git init
git add . && git commit -m "initial project setup"
```

Then create the repo on GitHub (github.com/new) and push, or hand the whole setup to the agent with the repo URL. Commit AGENTS.md in this first push so every future session starts with the rules (see `agents-md-swift`).

## One branch is enough

A solo project needs exactly two branches: `dev` (or just work directly) for daily commits, `main` for known-good states. The habit that matters is the commit per feature, not elaborate branch topology. If the app is broken, `git log` shows the last good feature, and rewinding to it is one command.
