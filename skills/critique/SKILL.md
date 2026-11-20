---
name: critique
description: Review a UI, motion, or copy change against the House Standard through the design critic — a PR number, a branch, a screen name, a Figma frame, or the current diff — and report ranked findings, the taste calls that belong to Aiden, and what was checked and found sound. Use when someone says "critique", "design review", "does this look right", "review this against the standard", "check this screen", before marking a design PR ready, or when a walkthrough needs an area judged.
argument-hint: "[<PR#> | <branch> | <ScreenName> | <figma-url> | this]"
---

# Critique

The deliverable is one review from this plugin's `design-critic` agent
(`flipcash-design:design-critic`), printed in chat, plus an offer to post it where it belongs. This
skill never edits code and never posts without an explicit yes.

## 1. Resolve the target

Repo: the app repo the working directory is in, and its GitHub remote (`git remote -v`; iOS is
`code-payments/code-ios-app`). Main is that remote's `main` (`upstream/main` on a fork,
`origin/main` in the client's checkout). With no app repo in the working directory, only a Figma
frame can be critiqued here; for a PR, ask which app repo it is in and use `-R <repo>`.

| Argument | Bundle for the critic |
|---|---|
| `<PR#>` | `gh pr view <n> -R <repo> --json title,body,baseRefName,headRefName,files` and `gh pr diff <n> -R <repo>`. No checkout needed; the critic reads changed files from the diff plus the current tree for context. |
| `<branch>` | `git diff $(git merge-base <branch> <main>)...<branch>` and the changed-file list. |
| `<ScreenName>` | Run `flipcash-design:design-surveyor` first; the critic gets the surveyor's document and the file list. Needs an app repo. |
| `<figma-url>` | Load `figma:figma-design-to-code` (the prerequisite), then `get_screenshot` and `get_design_context` for the node. The critic judges the frame against the standard and the app's component catalog (with no app repo, the Figma library in `${CLAUDE_PLUGIN_ROOT}/docs/figma-screens.md`): library components used versus invented. |
| `this` or nothing | The working tree: `git diff`, `git diff --cached`, and untracked source files, against the branch's base. |

Attach any screenshots or recordings the user gave (paths), and one line stating what the change
claims to do (the PR title and body, or the user's words).

## 2. Pick the lenses

Name them in the brief. The lenses live in `${CLAUDE_PLUGIN_ROOT}/docs/lenses/`; the app's
implementation notes (on iOS, `.claude/docs/design/implementation.md`, section "Critique") list the
code forms and folders that trigger each one.

- `motion.md` when the diff touches animation or transitions.
- `layout-and-hierarchy.md` for any change under the app's screens or shared UI library.
- `states-and-copy.md` for a new screen or flow, a whole-surface loader, a button state, a toast, a
  dialog, or any string change.
- A Figma frame gets the layout and states lenses.

## 3. Run the critic

One `flipcash-design:design-critic` agent with the bundle, the lens list, and the claim. For a PR
that touches more than eight screens, split by screen folder, run the agents in parallel, then merge
their sections, keeping every finding's `path:line` and de-duplicating Checked sound.

## 4. Report

Print the critic's review as written; its headings are the contract. Then one line per action
available:

- **A PR:** "Post this as a comment on #n?" On an explicit yes, write the review to a temp file and
  run `gh pr comment <n> -R <repo> --body-file <tmp>`, with a first line
  `Design critique against the House Standard (flipcash-design: docs/design-standard.md)`. Never post
  without the yes.
- **Your own branch before "ship it":** fold Blocking and Should fix into the work; list Taste calls
  for Aiden in the PR body as "decided by Aiden: …" once he has.
- **Standard gaps:** offer to add them to `docs/design-decisions.md` as open items through a PR on
  `code-payments/flipcash-design`. The installed plugin is a cached copy; an edit there is lost on
  the next update.

## Hard rules

- No edits to source, tests, or config from this skill.
- No comment, review, or state change on GitHub without an explicit yes in chat.
- The critic's verdict is reported as written. Do not soften "Blocked".
