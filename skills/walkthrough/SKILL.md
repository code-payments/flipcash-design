---
name: walkthrough
description: Walk the house — inspect everything that merged to main since the last walkthrough, judge each touched screen against the House Standard, and write a ranked punch list of cohesion fixes with evidence. Use when the user says "walkthrough", "walk the house", "what shipped this week", "punch list", "inspect what merged", on a weekly cadence, or after a version bump.
argument-hint: "[--since <sha|date>] [--area <ScreenFolder>] [--no-build]"
model: opus
effort: high
---

# Walkthrough

You are inspecting the house after a week of other people's work. The deliverable is **one punch
list** in the app repo: what drifted from the standard, ranked by what to fix first, each item with
evidence. No claim without a citation, and that file is the only thing you create (plus screenshots
in the session scratchpad).

This needs an app repo in the working directory. Read its implementation notes first (on iOS,
`.claude/docs/design/implementation.md`, section "Walkthrough"): they give the punch list's path,
the folders to scan, the version-bump search, and how to build, run and screenshot the app. With no
app repo or no notes, say so and stop.

## 1. The range

- Repo remote: the app repo's GitHub remote; `git fetch` it. Main is its `main`.
- `--since` given: use it. Otherwise read the newest walkthrough file at the notes' path and take
  the end SHA of its `Range:` line. None at all: the last version bump (the notes' search).
- Commits: `git log <from>..<main> --format='%h %s' -- <the notes' screen and UI folders>`. Where the
  repo squash-merges (the notes say), one commit is one PR and the `(#N)` is its number. Drop
  `test:`, `ci:` and `chore(deps)` subjects. Zero commits: say so and stop.

## 2. Survey

One `flipcash-design:design-surveyor` per screen folder touched, in parallel, each with the commit
list for that folder. Each returns the files, values, components, states and motion for what
changed, grouped by screen. With `--area`, survey only that folder.

## 3. See it (skipped with `--no-build`)

Build and run the app as the notes describe, so the critic judges screens, not hunks.

- Reach each touched screen and save a screenshot to `<scratchpad>/walkthrough/<Screen>.png`.
  Capture empty and error states where a launch argument or a mock reaches them.
- A screen you could not reach is listed under Needs a device, never skipped silently.

## 4. Judge

One `flipcash-design:design-critic` per screen folder, in parallel, with: the surveyor's document,
the screenshots, all three lenses, and the commit subjects as the claims. Ask it to judge each
screen as it now stands, not only the diff: an addition that is fine alone and wrong beside its
neighbours is a cohesion finding.

## 5. Write the punch list

```markdown
# Walkthrough — <YYYY-MM-DD>

Range: <from>..<to> (<N> commits, <M> screens) · Seen on a device or simulator: <list> · Read only: <list>

## Punch list

| # | Screen | Finding (what the user sees) | Rule | Evidence | Fix scope | Owner |
|---|---|---|---|---|---|---|

## Taste calls for Aiden
## Checked sound
## Needs a device
## Standard gaps
```

Rank: first-session-visible and cross-screen inconsistencies first, then single-screen defects, then
polish. Fix scope is one of `value change`, `screen PR`, `system PR (lists screens)`, `decision`.
Owner is `Aiden` (renovation), the app's engineer named in the notes (defect), or `Decision`
(both). Cap the file at 900 words. Every row cites `path:line` or a screenshot file.

## 6. Chat summary

Five lines: the range, screens seen versus read, the top three items, the path to the file, and the
next step as a phrase the user can say (`start the <Screen>`).

## Hard rules

- Only the punch list and scratchpad screenshots are written. No source, test, or config changes.
- No GitHub comments or issues from this skill.
- Nothing is skipped silently: an area not surveyed, built, or seen is listed as such.
