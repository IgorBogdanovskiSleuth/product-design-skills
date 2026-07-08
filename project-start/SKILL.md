---
name: project-start
description: Kick off a coding project safely for designers who don't live in git. Use at the START of any coding work — from a full feature to a one-line CSS tweak — in a code repo. Pulls the latest code, creates a working branch, and sets up per-session logging (Notion, Confluence, or Obsidian). Proactively SUGGEST running this whenever the user opens a code repo or says they're about to start coding, making a change, fixing a bug, or building a feature. Trigger on "start a project", "start coding", "new branch", "begin working on", or entering a repo to make changes.
---

# Project Start

A safety-first kickoff skill for designers working in code repos. It handles the git plumbing (pull, branch, commit, push, PR) in plain language and logs every session to your note-taking tool, so you can focus on the design change and not the terminal.

## When to Suggest This Skill

Be proactive. If the user opens a code repo, or says anything like "I want to change…", "let's fix…", "I'm going to build…", "start coding", offer to run this first:

> "Want me to run **project-start**? It'll grab the latest code, set you up on a fresh branch, and handle logging + the PR at the end — so you don't have to touch git directly."

Run it once at the start of each piece of work. One branch = one thing you're working on.

## First-time Setup

Check for a config file at `~/.project-start.json`.

**If it exists**: read it and use the saved settings silently.

**If it doesn't exist**, ask the user two quick questions, then save the file:

1. **"Where should I log your sessions — Notion, Confluence, or Obsidian?"**
   Store the answer as `log_destination`. Remember it so you never ask again.
   - If they already use the `design-session-logger` skill, mention that this reuses the same logging under the hood and check `~/.design-session-logger.json` for the destination and any Notion credentials.

2. **"What short name should I put in front of your branch names?"** (e.g. `igor`)
   Store as `branch_prefix`. This keeps branches tidy, like `igor/nav-drawer-redesign`.

Then write `~/.project-start.json`:

```json
{
  "log_destination": "notion",
  "branch_prefix": "igor",
  "base_branch": "auto"
}
```

- `base_branch: "auto"` means the skill detects `main` / `master` / `develop` itself. Only set an explicit value if the user asks.

Confirm the file was saved.

## Starting a Session

Run these steps **in order**. Narrate each one in plain language as you go — this is for someone learning git, so explain *why*, not just *what*. Never run a destructive command without saying what it does first.

### Step 1: Confirm you're in a code repo
Run `git rev-parse --is-inside-work-tree`.
- If it fails, tell the user plainly: "This folder isn't a code project I can track with git. Are you in the right folder?" Stop here.

### Step 2: Check for unsaved work (IMPORTANT — protects the user)
Run `git status --short`.
- **If there are uncommitted changes**, do NOT pull or switch branches yet. Explain: "You've got unsaved changes here. If I switch branches now they could get lost or throw errors." Then offer options in plain terms:
  - **Save them here first** — commit them on the current branch (go to the session-end flow).
  - **Set them aside** — `git stash push -m "project-start stash <date>"`, and remember to remind the user later that a stash is waiting.
  - **Throw them away** — only if the user explicitly confirms; warn this can't be undone.
- **If clean**, continue.

### Step 3: Find the base branch
First check whether this repo even has a remote:
```bash
git remote
```
- **If there's no remote** (empty output), there's nothing to pull from — this is a local-only repo. Tell the user plainly ("This project isn't connected to GitHub yet, so there's no latest code to pull — I'll just get you onto a working branch.") and skip the pull in Step 4.
- **If there is a remote**, detect the base branch (when `base_branch` is `"auto"`):
```bash
git remote show origin | sed -n 's/.*HEAD branch: //p'
```
This prints nothing if it can't reach the remote — don't treat empty as an answer. Fall back to checking for `main`, then `master`, then `develop` locally, and use whatever exists.

### Step 4: Get the latest code
**Only if the repo has a remote** (from Step 3). Switch to the base branch and pull:
```bash
git checkout <base_branch>
git pull --ff-only
```
Explain: "Grabbing the latest version so you're not building on top of something out of date."

**If the branch has no upstream** (git says "no tracking information"), it's never been pushed — there's nothing to pull. Say so briefly and move on; don't surface the raw error.

**If the pull reports a conflict or can't fast-forward**, do NOT dump git errors on the user. Explain in human terms: "Your local copy and the team's copy have both changed the same area — they need to be reconciled." Then offer to walk through it or pause so they can get a teammate. Never force anything.

### Step 5: Learn what they're working on
Ask: **"In a few words, what are you working on?"**
- Save their answer verbatim as the **session goal** — you'll need it for the log entry at the end.
- Turn it into a clean branch name: lowercase, hyphens, no spaces, prefixed. Example: goal "redesign the nav drawer" → `igor/redesign-nav-drawer`.

### Step 6: Create the working branch
```bash
git checkout -b <branch_prefix>/<slug>
```
Confirm: "You're now on your own branch, `igor/redesign-nav-drawer`. Anything you change here is safely separate from the main code until you're ready to share it."

### Step 7: Ready to work
Tell the user they're set up and can start making changes. Remind them a "session" ends when they push — and that you'll handle the commit, push, log, and PR when they're ready. Just say "**I'm done**" or "**push this**".

## Resuming an Existing Branch

If the user runs the skill in a repo where they're already on a working branch (not the base branch) with a matching prefix, don't start over. Say: "Looks like you're already working on `igor/redesign-nav-drawer`. Want to keep going on this, or start something new?" If they keep going, skip to Step 7.

## Ending a Session (push → log → maybe PR)

A **session** lasts until the code is pushed. A single branch may have **many** sessions and many commits — both before and after a PR is opened. Handle each part independently.

Trigger this flow when the user says "I'm done", "push this", "commit", "log", or "exit".

### Step A: Commit the work
1. Show what changed: `git status --short` and a short summary of the diff in plain language.
2. Help write a clear commit message describing the change (the "why", not a file list).
3. Stage the relevant files by name (avoid blanket `git add -A`; never commit secrets, `.env`, tokens).
4. Commit.

### Step B: Push
Only if the repo has a remote (see Step 3). If it doesn't, tell the user the work is committed locally but can't be uploaded until the project is connected to GitHub, then skip to logging.
```bash
git push -u origin <current-branch>
```
Explain: "Uploading your work so it's backed up and shareable."

### Step C: Log the session
Write a log entry to the user's chosen `log_destination`. Reuse the **design-session-logger** skill's mechanics if available (same config, Notion sync, file structure). The entry should capture, for THIS session:
- **Session goal** (from Step 5)
- **What changed** (plain-language summary of the commit(s) in this session)
- **Branch** name and whether a PR exists yet
- **Decisions / notes** worth remembering
- **Next steps**

Append to the existing log if it's the same project/day; create a new entry otherwise. Confirm where it was saved.

### Step D: Offer a PR (only if none exists)
Check whether the current branch already has an open PR:
```bash
gh pr view <current-branch> 2>/dev/null
```
- **If a PR already exists**: don't open another. Just tell the user "Your new commits are now part of the existing PR: <link>."
- **If no PR exists**: ask "Want me to open a pull request so the team can review this?"
  - If yes: `gh pr create` with a clear title and body. **Never auto-merge.** Hand back the PR link.
  - If not yet: fine — the branch is pushed and can get a PR later. Remind them they can keep pushing more sessions to the same branch.

### Step E: Wrap up
- If the user said "**I'm done**" / "**exit**": confirm everything saved, give the PR link if there is one, and say goodbye.
- Otherwise: confirm and stay ready for the next session on the same branch.

## Guardrails (always)

- **Never** run `git reset --hard`, `git push --force`, delete branches, or discard changes without the user explicitly confirming, in plain language, what will be lost.
- **Never** auto-merge a PR.
- **Never** commit secrets or credential files.
- **Never** work directly on the base branch — always create/use a working branch.
- If anything git-related fails, translate the error into plain English and offer safe next steps rather than surfacing raw git output.

## Config Reference

`~/.project-start.json`:

| Key | Meaning |
| --- | --- |
| `log_destination` | Where sessions are logged: `notion`, `confluence`, or `obsidian`. Asked once, then remembered. |
| `branch_prefix` | Short name prefixed to every branch, e.g. `igor`. |
| `base_branch` | `"auto"` to detect `main`/`master`/`develop`, or an explicit branch name. |

To change settings later, the user can edit the file or say "update my project-start config".

## Troubleshooting

- **Not a git repo**: user is likely in the wrong folder, or the project was never set up with git. Ask them to confirm the folder.
- **`gh` not installed**: PR creation needs the GitHub CLI. Explain they can install it (`brew install gh`) and authenticate (`gh auth login`), or open the PR manually on GitHub. Push still works without it.
- **Pull conflict**: reconcile with the user's help — never force. Offer to pause so a teammate can assist.
- **Uncommitted changes at start**: always resolve via Step 2 before switching branches.
- **Config missing/corrupted**: re-run first-time setup.
