# States and copy lens

This lens judges the states a screen can be in and the words it shows in each: the four data states (empty, loading, error, populated), long content, Dynamic Type, in-flight and success controls, destructive confirmation, first-paint correctness, validation timing, shipped leftovers, and every label, title, error, toast and dialog. The reviewer's posture: assume the happy path was tested on a seeded account and the other states were not. Find the branch that does not exist first, then read the copy in the branches that do.

Each app's implementation notes give the code form of every rule, the file it cites as evidence, and the code patterns to flag on sight; on iOS that is `.claude/docs/design/implementation.md` in the app repo.

## Rules

1. **Give every data-driven view all four states: loading, failed, empty, loaded.** The developer's seeded account only ever showed the last one; a new account meets the other three first.
2. **Show the empty state only once the first load has finished, never just because there are no rows.** A list is empty before the first fetch returns, so an ungated empty state tells a user with chats that they have none.
3. **Size the empty state to the populated state's box and point at the next action.** A blank or single-line empty state is the first screen every new account sees, and nothing on it says what to do. The home chat list's empty state is the model: an icon, a headline, one sentence, filling the box.
4. **Show a search miss with a no-results state that names the query, not the first-run empty state.** A miss has to name what was searched so the user knows what to retype.
5. **Hold the page's shape while it loads: skeleton rows for content, a spinner only inside the control that is working.** A centered spinner collapses the list and the rows jump in when data lands.
6. **Delay a loading indicator 300 ms and keep populated rows on screen during a refresh.** A spinner that flashes for 80 ms on a fast response reads as a glitch, and blanking rows the user was reading is worse than the wait.
7. **Give every failed load a message and a retry that refetches.** Spinning forever or rendering nothing is what the app does on a train. A chat message that failed to send reads "Not Delivered. Tap to retry".
8. **Paint the right state in the first frame: show the stored value from the start, never a default that corrects itself after a load.** The user sees the flash on every cold launch, and a synchronous seed costs single-digit milliseconds.
9. **Check a blocking precondition before presenting a sheet, not inside it.** A sheet that opens, finds it cannot proceed and dismisses itself flashes, and once froze the iOS app.
10. **Route a dialog that fires across a sheet boundary through the app's one shared dialog slot; keep a leaf's one-off dialog local to the leaf.** On iOS a dialog is a sheet underneath, so two live bindings of one dialog fight for the single presentation slot.
11. **Truncate user strings by kind: in the middle for addresses and keys, at the tail for names; give long body text an expand control.** Dev data is "Test Project"; real data is a 90-character display name and an address with no break opportunity.
12. **Let an amount shrink to fit before it truncates.** An ellipsis inside money is a different number.
13. **Let multi-line copy wrap at accessibility sizes instead of clipping.** The house font scales with Dynamic Type, so a two-line subtitle is four lines at AX3.
14. **Reserve the width and height a label needs at its widest before it changes.** A link that swaps to a spinner or a counter that ticks moves everything beside it.
15. **Disable the submit control and lock the field while a request is in flight, and re-enable both in every failure path.** An enabled button double-fires and one tap becomes two charges; a form left disabled after a failure is the same bug from the other side.
16. **Drive a progress-reporting button through loading then success, hold success 500 ms, then navigate.** A label that swaps in its own spinner drops the checkmark every other completed action shows.
17. **Show a toast only through the app's toast controller: one at a time, 4 s default, updated in place for a message that updates, an action for anything undoable.** A screen-level overlay with its own timer stacks under sheets, outlives its screen, and nothing drops it while the bill is up.
18. **Confirm an irreversible action with a dialog whose confirming button names the outcome in the destructive style beside Cancel; give a reversible action reached by a swipe or a single tap an Undo toast instead.** "OK" or "Yes" makes the user re-read the subtitle to learn what they agreed to, and a dialog on a reversible action is friction that protects nothing.
19. **Validate free-form input through the app's shared validators on submit, raise the rejection as a dialog or an inline hint, and submit the validated value.** Rejecting at the keystroke yells at a half-typed first attempt, and an inline regex drifts from the server's.
20. **Once a field has shown an error, re-validate on change so the error clears the moment the input is fixed.** Punish late, reward early.
21. **Flag any UI that parses, formats or compares money by hand: a keypad string parses only through the app's one amount parser, a money string comes only from its one formatter, affordability goes only through its one sufficient-funds check.** The UI is where the hand-rolled compare sneaks in; two real-money bugs shipped that way on 2026-07-08.
22. **Change the words when you change the color.** The error color alone is invisible to a colorblind user.
23. **Write each piece of copy as one complete, translatable string; never assemble a sentence from fragments or show copy that skips translation.** A fragment or a `== 1 ? "Contact" : "Contacts"` branch cannot be translated. A shared component that shows copy takes a translatable key, never a plain string, so its callers' strings stay keys.
24. **Label a button with its outcome: "Add Money", "Join Chat", "Next" on a setup step, "Save" on an edit; never "Submit", and "OK" only to acknowledge.** The label is the only place the user learns what the tap does.
25. **Title an error with what failed and subtitle it with the fix; raise a rejection the user can fix by typing as a gray info dialog, and keep the red error dialog for a failure that is nobody's fault.** The red banner also fires an analytics event, so a too-short handle must not count as a failure.
26. **Ship no placeholder copy, debug logging, ungated debug-only UI, or TODO on a user path.** Lorem inside a preview is fine; anywhere else it is on screen.
27. **Never use an emoji as an icon; use an asset or an SF Symbol.** Emoji render differently per OS version and ignore the tint color; the app's copy has zero today.

## The states checklist

| State | What must be true | How to see it |
|---|---|---|
| Empty, first run | Headline, one line, next action; fills the populated box; gated on loaded | A preview with empty data; a fresh account on device |
| Empty, no results | The no-results state names the query | Type `zzzq` into the search field |
| Empty, filtered | One line naming the filter (`No Unread Chats`); no headline or action; the list stays mounted | Select a filter with nothing in it |
| Loading, first | Skeleton rows after 300 ms; no centered spinner; no empty flash | A throttled network at 100% loss, then 3G; slow animations |
| Loading, refresh | Rows stay; a footer spinner only | Open with a warm cache, pull to refresh |
| Failed | Message plus retry; the retry refetches | Airplane mode, open the screen, restore Wi-Fi, tap retry |
| Populated | Real data at its widest; nothing truncates without a chosen truncation | The longest real display name, group title and address |
| Long content | Truncated by kind; an expand control for body text; amounts scale | Preview with a 90-character name and a 44-character address |
| Dynamic Type | Nothing clips; multi-line text grows taller | The largest accessibility text size, in a preview and on device |
| In flight | Field locked, button loading, one request on a double tap | A very bad network; double-tap the button |
| Success | Success checkmark held 500 ms, then navigation | Watch the button after a submit with slow animations on |
| First paint | The first frame shows the stored value, never a default that corrects | Force-quit, cold launch, screen-record at 120 Hz, scrub to frame 1 |
| Covered | A toast dispatched under a sheet, dialog or the bill is dropped, not queued | Present a sheet, trigger the toast, dismiss the sheet |

The iOS notes give the preview setup, simulator menu and network tool for each row.

## Flipcash voice

Inferred from the literals in the iOS app; no strings catalog exists, so the literal is the key, and the iOS notes give each literal's path. Where the house differs from generic advice, the house wins.

- Casing: Title Case for navigation titles, dialog titles, empty-state headlines and buttons; sentence case for subtitles, hints, toasts and placeholders. `"Verify Phone Number"` (navigation title, five screens); `"No Chats Yet"`; `"Chat archived"`. Drift to flag: `"No activity yet"` and `"No archived chats"` are single-line sentence-case empties with no next action.
- Person and tone: second person, plain, no exclamation marks; contractions with straight apostrophes ("Couldn't", "Isn't", "You're"); the real "…" character (12 uses, zero "..."). The app says "we" only inside a failure subtitle: `"We couldn't create your account. Please try again."`
- Errors: the title names what failed, the subtitle names the fix as an imperative. `"Couldn't Save Your Username"` / `"Try again"`; the catch-all `"Something Went Wrong"` / `"Please try again later"` (32 sites). "Please" is the house's choice in a recovery subtitle only, as in `"Please check your connection and try again"`; never in a title or on a button.
- Rejections the user can fix are gray info dialogs with the rule in the subtitle: `"Too Short"` / `"Usernames must be a minimum of {minimum} characters"`. Money in copy is formatted, never typed: `"{amount} Minimum Balance Required"`, with the amount from the formatter and no forced decimals.
- Confirmation: the title is the action as a question (`"Change Username?"`, `"Remove Account?"`) or `"Are You Sure?"` with the consequence in the subtitle (`"Withdrawals are irreversible and cannot be undone once initiated"`); the confirming button names the outcome in the destructive style (`"Yes, Withdraw"`, `"Remove"`, `"Change Username"`); the second is Cancel or `"Not Now"`. One bare destructive `"Yes"` is the outlier.
- Acknowledgement: one `"OK"` is the house default on an error dialog with nothing to choose. Buttons otherwise name the outcome: `"Add Money"`, `"Join Chat"`, `"Connect Your Phantom Wallet"`; `"Next"` on a setup step and `"Save"` on an edit.
- Empty states and toasts: a headline plus one sentence that points at the next action, `"Start a new chat, or share your profile"`; a toast is a past-tense statement plus a one-word action, `"Chat archived"` / `"Undo"`.
- Punctuation: a one-sentence subtitle or hint carries no period, `"This is how you'll be uniquely identified on the platform"`; two sentences get periods, `"Couldn't get a fresh rate. Please try again."` (seven sites).

## Flag on sight

The code forms of these, and the places they are live today, are in the app's implementation notes.

- A filled button that swaps its label for a spinner by hand (R16).
- A toast drawn by the screen itself on its own timer (R17).
- A spinner as a list's whole loading state; a blank as its whole empty state (R3, R5).
- An empty state shown on "no rows" alone, with no loaded flag (R2).
- A screen that starts empty and fills from a load when the store already has the rows (R8).
- A sheet presented straight after a call that can block, with no check in front of it (R9).
- One dialog bound from both a sheet and its parent (R10).
- A button that waits on a network call and is not disabled while it does (R15).
- Money parsed, formatted or compared by hand (R21).
- A one-line limit on a user string with no truncation chosen; a fixed width on text (R11, R13).
- A "Submit" button, "OK" offered as a choice, a destructive "Yes" with nothing after it, a destructive alert whose only button is "OK" (R18, R24).
- `"Oops"`, `"Error"`, `"Invalid input"`, or a new `"Something Went Wrong"` where the failure has a name (R25).
- A navigation or dialog title in sentence case; a subtitle or toast in Title Case (voice).
- A plural picked by a count check, copy that skips translation, or a sentence assembled from pieces (R23).
- `Lorem`, `"John Doe"`, `"test@"`, `"TODO"` outside a preview; debug logging in a view; debug-only UI with no launch-argument gate (R26).
- An emoji in copy (R27).
- The error color applied with the same words as the normal state (R22).

## Before / After / Why

| Before | After | Why |
|---|---|---|
| A Save button that swaps its label for a spinner while saving | The house button-state label going loading, then success held 500 ms, then dismiss, disabled while not normal | The checkmark is how the app confirms a completed action; a hand-rolled spinner skips it and leaves the button tappable mid-request (R15, R16). |
| An empty chat list that shows a blank as soon as there are no rows | An empty state shown only after the first load: an icon, `"No Chats Yet"`, `"Start a new chat, or share your profile"`, filling the box | A new account's first screen teaches the next action and never flashes "empty" before the fetch lands (R2, R3). |
| A `"Delete?"` alert with one "OK" button | `"Remove Account?"` / `"Make sure you have a backup of your Access Key before removing this account."` with a destructive `"Remove"` and Cancel | The confirming button names the outcome and the subtitle names the cost; "OK" names neither (R18, voice). |

The code for each row is in the iOS implementation notes.

## Needs a device

- Airplane mode after a list has loaded, then open each data-driven screen: a message and a working retry, never an endless spinner or a blank.
- A very bad network on a submit: field locked, spinner, checkmark, one request on a double tap, button re-enabled after a failure.
- The largest accessibility text size: nothing clips or overlaps. On iOS the chat transcript uses a fixed-size font and will not scale; note it as a finding, do not fix it in a design PR.
- The longest real display name, group title, currency name and wallet address in the row, the title bar and the dialog title.
- VoiceOver: an inline error is read right after its field; a toast is announced; a dialog reads its title before its buttons.
- Cold launch after a force quit with a warm cache, screen-recorded: the first frame shows the selected currency and the stored rows, not USD and an empty list.
- A toast fired while a sheet, a dialog or the bill is up is dropped: not shown under it, not replayed after.
- The 500 ms success checkmark on a real round trip: visible, or lost behind the navigation push.
