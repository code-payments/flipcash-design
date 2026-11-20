# Motion lens

Judges whether a change's motion is feedback the user needed, at the house's pace, in the house's forms, and whether it survives Reduce Motion, interruption, and a 120 Hz phone. The reviewer reads the diff for the patterns below, names the rule each finding breaks and the path:line that already does it right, and files anything about feel under "Needs a device" instead of guessing from code.

Each app's implementation notes give the code form of every rule, the file it cites as evidence, and the code patterns to flag on sight; on iOS that is `.claude/docs/design/implementation.md` in the app repo.

## Rules

1. **Animate only what answers a question: where did this come from, did my tap register, what just changed.** Motion that answers nothing is a tax paid on every repetition, so cut it before tuning it.
2. **Let frequency set the elaboration: a 100-times-a-day action gets press feedback and nothing more, an occasional one gets one spring under 0.3 s, a first-time moment may run long.** A spring that delights once annoys on the hundredth repeat. The notification-permission screen runs 0.8 s at bounce 0.4 after a 0.3 s delay because it plays once per install; the house press is the whole budget for a button.
3. **Never animate a change driven by typing: a filtered list, a caret, a character count, a mention candidate swap.** The user is already ahead of any curve, so animation reads as the UI lagging their input.
4. **Write every spring as a duration plus a bounce, duration 0.17 to 0.45 s, bounce 0 to 0.35.** Every spring form the apps use shares one physics (`dampingRatio = 1 - bounce`), so a number tuned in one form ports to the others untouched; response-and-damping-fraction springs and the legacy presets take no new entries.
5. **Choose the curve by the job: a spring for anything that settles into place or that a finger can interrupt, ease-out or a bounce-0 smooth spring for a fade, ease-in-out only for a view already on screen moving between two resting states, linear only for elapsed time, ease-in never on an entrance.** A spring retargets from live velocity when interrupted and a fixed curve restarts; an ease-in holds the movement back at the moment the user is watching for a response.
6. **Give bounce 0 to any value another view tracks every frame: an inset following a bar, a height a sibling measures, a scroll offset following chrome.** Overshoot in the leader drags the follower past its resting place and back. The presets `keyboardScroll` and `replySurface` are bounce 0 for this reason.
7. **Move with the keyboard on the keyboard's own duration and curve, never a house spring.** Any other curve fights the system's and the bar visibly detaches from the keyboard's edge.
8. **Run exits 20 to 30 percent quicker than their entrance, with bounce 0 and less travel.** The user has already decided to leave, and an overshoot on the way out reads as the thing coming back. `reaction` runs 0.32 s in, `reactionExit` 0.2 s out, no bounce.
9. **Enter from scale 0.9 to 0.95 plus opacity, anchored at the edge the view comes from; a glyph, pill or toast no taller than 44 pt may start at 0.4 to 0.6; a bar or toast that belongs to a screen edge may slide in from that edge instead; nothing starts at 0.** A real object has a shape before it arrives, and a scale from 0 hides the opacity ramp behind a speck. A chat row grows from the sender's bottom corner; the send arrow starts at 0.6.
10. **Give every conditionally mounted view a transition that mirrors its entrance, driven by one animation on the view that owns the condition, and never add a second animation at the dismiss site on top of it.** A view with no transition blinks out and severs the context its entrance built; a second animation gives the exit a curve the entry never had.
11. **Views that move as one share one spring: a surface and its scrim, a card and the page that follows it, a row and the pills inside it.** Two timings on one movement read as two unrelated events. On the wallet the page follows on the card's own curve, delayed, committed in the same frame; `reactionReflow` is `reflow`.
12. **Scope an animation to the view that changes and the value that changes it; never a default animation on a container, never an animation that is not tied to a value.** An unscoped animation animates every layout that happens to change in the same frame, including text wrapping and rows the user never touched.
13. **Sequence on completion, not on a timer; a delay is only for a designed hold, and a chain is at most three steps.** A spring is still settling when its nominal duration is up, so a timer hands off early and the two halves judder.
14. **A view the user may tap mid-flight still takes the tap.** On iOS, UIKit drops touches on animating views until they settle unless the animation allows interaction; the app's notes give the option.
15. **Under Reduce Motion keep the opacity and color change and drop the movement and scale; never return early with nothing.** The setting asks for less motion, not less feedback, and a hard cut is the jolt it exists to prevent. A reaction pill keeps scale 1 and still fades; a push becomes a fade.
16. **Press feedback is one press everywhere: a shrink to 0.95 over a 0.2 s spring with bounce 0, a dim to 0.7 instead under Reduce Motion.** Every titled house button does this, so a button with its own press reads as a different control. Exceptions: tiles, rows and cards keep their own press; icon-only circles keep a dim; picker segments take none, since the sliding selection is the feedback; the public group keeps its touch-down haptic. (Revised 2026-11-19: replaces "an opacity drop, never a scale".)
17. **One haptic per event, fired on the event, never from an animation's completion.** A haptic that lands when the spring settles arrives late and lies about when the thing happened. Swipe-to-reply fires at the threshold crossing, not the settle; the home tab fires after the gate so a hold that opens nothing stays silent.
18. **Roll numbers with a numeric text transition, swap symbols with a replace effect, and replace a string inside a sized capsule by giving the new string its own identity.** Interpolating a label's width draws the new glyphs against the old frame, so the text spills or slides into place.
19. **Order a matched-geometry morph's modifiers so the match comes before the frame, and never give a matched view's parent an identity transition.** Wrong order fails silently as two views fading at their own positions; an identity transition removes the parent before the morph can interpolate. (SwiftUI rule; the code form is in the iOS notes.)
20. **Nothing animates on first appearance: a first load, a cell scrolling in with pills it already had, an indicator already at rest.** Nothing the user did not cause should move when a screen appears.
21. **Scroll-driven motion tracks the finger: a fade or offset tied to the scroll position has no spring of its own, a programmatic scroll lands on the system curve or one house spring, and nothing animates a value the scroll view already drives.** A second animation on a scrolling value fights the gesture and reads as lag. The tips chip row's pull is the one scroll-driven surface today; judge it on the device.

## Flipcash specifics

- **Named springs.** The house springs are named presets, each with a stated intent; a spring changes in its preset, never at a call site, and its intent stays true. The iOS preset file and its sixteen springs are in the app's notes.
- **The house range is the presets' range:** 0.17 s (`sendButton`) to 0.45 s (`corner`, the slowest on purpose so a bubble run regrouping reads as settling), bounce 0 to 0.35 (`reaction`). `replyMerge` at 0.5 s sits outside it for the reason in its own comment and is not a precedent.
- **Physics.** `dampingRatio = 1 - bounce`, `stiffness = (2π / duration)²`, `damping = 4π × dampingRatio / duration`.
- **Legacy forms.** Convert a response-and-damping-fraction spring only when its file is already open for motion, as `duration = response`, `bounce = 1 - dampingFraction`, same feel, and say so in the PR.
- **Toasts are not a precedent yet.** The toast runs the entrance and the exit on one 0.4 s, bounce 0.2 spring with a y-scale swell matched to Android; rule 2 budgets an occasional action under 0.3 s and rule 8 a quicker exit, so the toast's timing is an open decision in `design-decisions.md`.
- **Haptics.** A light impact for an arrival, a selection tick for a highlight moving, a heavy impact for a commit. A gesture prepares its haptic ahead of the threshold.
- **Press feedback is the 0.95 shrink** (rule 16). The keypad's key pop (scale 1.5, no animation) is a keyboard key, not a button, and stays.
- **Judged on a 120 Hz iPhone.** The simulator is for quick looks and Slow Animations; a verdict on feel from a simulator recording is not a verdict.

## Flag on sight

The code forms of these live in the app's implementation notes.

- A spring written as response and damping fraction in new work (4).
- A new entry in the legacy presets (4).
- An animation with no curve (5); any animation on a text field's binding, search results or mention candidates (3).
- Ease-in on an entrance (5).
- Bounce above 0.35 (4); any bounce above 0 on a value whose change resizes a bar, an inset or a measured height (6).
- Duration above 0.45 outside onboarding (2, 4).
- A scale entrance with no start scale, or one that starts at 0 (9).
- A conditionally shown view with no transition (10); an identity transition near a matched-geometry morph (19).
- A default animation on a container or screen root; any animation not tied to a value (12).
- A delay chain longer than three steps; a sleep sized to a spring's duration standing in for its completion (13).
- A fixed-duration animation on a gesture-driven or interruptible view (4, 5).
- A tappable view moved by an animation that drops touches (14).
- A Reduce Motion branch that returns with nothing (15).
- A titled button with its own scale or dim on press, or a titled button with no press at all (16).
- A haptic fired from an animation's completion (17).
- An animation on appear outside onboarding (20).

## Before / After / Why

| Before | After | Why |
|---|---|---|
| A response 0.35, damping fraction 0.8 spring | A duration 0.35, bounce 0.2 spring | Same physics, house form (4): the number now reads against the named presets and ports to every other form unchanged. |
| A panel entering at scale from 0 on a 0.5 s ease-in | A panel entering from scale 0.95 with opacity on a 0.3 s, bounce 0.15 spring | A shape before it arrives (9), a spring that reverses cleanly when closed mid-open (5, 10), 0.3 s instead of 0.5 (2). |
| Under Reduce Motion, the view appears with no animation at all | Under Reduce Motion, the 12 pt rise is gone and the fade stays | Gentler, not zero (15): the fade still tells the user the view arrived, only the travel is gone. |

The code for each row is in the iOS implementation notes.

## Needs a device

- **Feel of a bounce or a settle.** Only a 120 Hz iPhone shows it; the simulator runs on the Mac's clock and GPU, so a bounce that reads right there is unproven. Judge the branch installed on the phone, not a simulator recording.
- **First-frame jumps, snapped exits, pairs drifting apart.** Slow animations first, then the same surface at 1x on the phone; a jump at the start or end of any enter or exit is a finding (8, 10, 11).
- **Chat motion before/after.** Record both sides of the change from the same scripted motion sandbox (the app's notes say how to launch it); what differs is the change.
- **Reduce Motion.** Turn Reduce Motion on; walk every changed surface; a hard cut or a lost fade is a finding (15).
- **Interruption.** Open and close each surface as fast as possible and reverse a drag mid-flight; a jump to the end state or a restart from the beginning is a finding (5, 10).
- **Taps mid-flight.** Tap a pill, row or button while it is still moving; a dropped touch is a finding (14).
- **Keyboard.** Raise and dismiss it with the transcript at and away from the bottom; the bar rides the keyboard's edge with no gap and no overshoot (6, 7).
- **Scroll-driven surfaces.** A slow drag, a short pull and a fling on each: the fade tracks the finger, and a programmatic scroll lands once without a second animation (21).
