# Layout and hierarchy lens

This lens judges whether a screen has one thing to look at, one thing to do, and a rhythm that makes everything else recede: hierarchy, spacing, alignment, surfaces (cards, sheets, glass, rings, radii), empty states, layout stability, and icons. The reviewer's posture is subtract first and squint last: walk every element and ask what breaks if it is gone, then blur the screen and ask whether it could belong to any of ten thousand other apps; if only the logo ties it to Flipcash, it fails, however clean each piece is.

Colour names are the house tokens (`textSecondary`, `backgroundRow`, …), with their values beside them. Each app's implementation notes map every rule to its code (the modifier, the component, the file it cites as evidence) and list the code patterns to flag on sight; on iOS that is `.claude/docs/design/implementation.md` in the app repo.

## Rules

1. **Give a screen one `filled` button per rendered state; every other action takes `subtle`, `textLink`, `filled05`, or `icon`.** Two white 54 pt capsules make neither of them the next step. `subtle` is the one secondary; on a full screen the secondary under a primary can be a `textLink`. (Revised 2026-11-19: `filled20` is deleted.)
2. **On any screen that shows an amount, set the amount in the largest font on the screen and keep every other text at 24 pt (the small display size) or below.** Money is why the screen exists, and a title at the same size competes with it. The home balance is 55 pt.
3. **Build hierarchy by demoting, not by adding: section headings take the heading text style in `textSecondary`, supporting lines are `textSecondary`, and only the hero and the primary action are full white.** Making a second thing loud makes nothing loud.
4. **Delete any element that nothing breaks without, then walk the screen again before styling what is left.** A caption that restates its heading, an icon on every label, and a divider that spacing already draws are the additive tells of generated UI, and deletion cannot introduce new ones. My Account is the exemplar: icon, title, separator; nothing else.
5. **Lay out on the rhythm: 20 for screen gutters and the primary button's inset, 12 between siblings, 8 inside a component, 4 between a label and its caption, with 16, 24, and 40 as the section steps.** The rhythm is what makes unrelated screens read as one app; 14, 28, and 30 are drift everywhere, 10 and 15 are drift outside the chat transcript (which keeps its own 5/10/15 row rhythm), and a new scale is a system decision, not a per-screen one. The rhythm binds components too, except where an app's notes document that a component mirrors the other platform's spec value.
6. **Space siblings with the container's gap, never with bottom padding on each child.** Per-child padding leaves a trailing gap after the last item and hides the rhythm from the next reader.
7. **Pin a full screen's bottom actions with flexible space above them, 20 pt at the sides and top, and 20 pt from the device's bottom edge, not from the home-indicator safe area. A screen that ends in a `textLink` takes 32 pt under the link's 44 pt box.** The last button then sits as far from the bottom as from the sides, and the keyboard still lifts it. Two buttons together sit 12 apart. (Revised 2026-11-19: replaces a bottom safe-area inset plus 20 pt bottom padding.)
8. **Clear the tab bar with a measured inset, branched on OS: 40 on iOS 26, 40 plus the legacy pill clearance on iOS 18.** The iOS 26 bar sits in the safe area and the iOS 18 pill floats over content, so one constant is wrong on one of them. (iOS rule; Android has no equivalent written here.)
9. **Align every label and icon to one leading edge per screen; center only a hero (a balance, an illustration) or a confirmation.** Two alignments on one screen read as two layouts pasted together.
10. **Reserve a fixed leading slot for row icons, sized to the neighbouring rows' leading element (45 pt in Settings, 44 pt beside avatars), so titles share one left edge whatever the glyph's width.** Glyphs vary by up to 8 pt in width, and titles that drift with them look ragged.
11. **Let wrapping labels grow vertically, and give a spacer that pushes content apart a minimum length.** Without them a long title truncates to one line or crushes its neighbor to zero width.
12. **Make depth with fills and hairline rings, not shadows: a card is `backgroundRow` (white 5%), a tappable tile is white 10%, and the ring is `rowSeparator` (white 10%) at 1 pt, stroked inside the edge.** On #19191A a drop shadow is invisible and a bright stroke glows, while a translucent ring composites on any fill.
13. **Leave shadows off every card, row, tile, and sheet; the one lifted surface is the chat bubble under its context menu, black at 0.135, radius 20, offset y 10.** One lift in the whole app means lift reads as a state, not a style.
14. **Separate rows with a 1 px hairline in `rowSeparator` (white 10%), never with a 1 pt line or a second fill.** A 1 px rule recedes on a 3x screen; a 1 pt line reads as a border and a second fill as a card.
15. **Use three radii only, plus a capsule for buttons, pills, chips and toasts (a circle for an icon-only button): 6 for fields, tiles, and small cards, never a button; 12 for boxes, sheets, and the bill card; 12 with 4 on grouped inner corners for chat bubbles.** Each extra radius is one more thing that looks undecided. (Revised 2026-11-19: buttons are capsules.)
16. **Corners are continuous.** A circular corner is a finding.
17. **Derive a nested radius as the outer radius minus the padding between the two surfaces, and make the inner surface square when that reaches 0.** A child that copies its parent's radius pinches at the corners; the tokens allow exactly one derivation, a 6 inside a 12 with 6 pt between. A capsule button inside a card is exempt and needn't be concentric with it.
18. **Put glass only over content that moves beneath it (a scrolling list, a photo, the bill), and group two glass pieces that sit together.** Glass over a flat `backgroundMain` is blur with nothing to blur and reads as a costume; grouping lets neighbors share one sample.
19. **Ship every glass surface with its iOS 18 fallback: ultra-thin material in the same shape, thin material for a clear sheet.** The fallback is the design on every iOS 18 device, not an afterthought. (iOS rule.)
20. **Present partial-height sheets on `backgroundSecondary` (#252526), one step lighter than the screen.** The sheet reads as a layer because it is lighter; a sheet painted `backgroundMain` merges into the screen behind it.
21. **Give a pressed button one feedback, the house press: a shrink to 0.95 over a 0.2 s spring, or a dim to 0.7 under Reduce Motion.** Two feedbacks on one press (scale plus opacity, or scale plus glass) stack and feel laggy. Tiles, rows and cards keep their own press, icon-only circles keep a dim, and picker segments take none; one dim value for the circles is an open decision in `design-decisions.md`. (Revised 2026-11-19: the shrink replaces the opacity drop.)
22. **Size buttons with the button-height tokens, 54 and 44 for thin, never with a literal.** A literal cannot follow the token when it changes, and 54 written by hand is indistinguishable from a guess. A title stays on one line at that height: it shrinks to 0.8, then truncates. (Revised 2026-11-19: 54, was 60.)
23. **Give every tappable glyph and text pill (a chip, a toast action) a 44 pt hit area that covers its whole shape, and never let two hit areas overlap.** Without a hit shape only the glyph's pixels take the tap; overlapping areas fire the wrong control.
24. **Size an empty state like its filled state: a headline in the large text style, one `textSecondary` line, and the action that fills it.** A branch that only adds space is a blank screen that jumps when content arrives.
25. **Hold loading layout with skeleton rows the size of real rows, and seed state synchronously so first paint shows real data.** A spinner collapses the page and the arriving rows shove everything down; a default-then-correct render flashes.
26. **Stop numbers jiggling: fixed-width digits on any figure that updates in place, a numeric text transition on any figure that animates.** Proportional digits change width per digit and the whole row shifts on every tick.
27. **Reference icons through the app's icon inventory, never by a string name, and never as an emoji in text.** The inventory is the house's icon set, a string bypasses it, and emoji take no tint and render differently per OS.
28. **Use one icon style per list, SF Symbols or the asset set, and reach for the asset set only where SF Symbols has no glyph.** Mixed stroke weights down one list read as two products.
29. **Size a symbol by its font and an asset by a 24 pt frame, and match the glyph's weight to the adjacent text's weight.** A thin glyph beside demi text looks like a mistake.
30. **Set type only with the house type tokens; a raw system font skips the house weight mapping and Dynamic Type scaling.** The tokens map bold to Semibold and scale with the body style; a raw system font does neither, and a font set from a literal size is a token nobody else can find.
31. **Finish with the squint test: blur the screen and name what ties it to Flipcash; if the answer is only the logo, it fails.** The bill-colored card, the 55 pt balance, and the 6 pt continuous tile are the house's signature; a row of three icon-title-caption cards or a centered pill badge over a headline belongs to ten thousand other apps.

## Flipcash specifics

- Theme: one dark mode. `backgroundMain` #19191A, `backgroundSecondary` #252526, `backgroundRow` white 5%, `rowSeparator` white 10%, `textMain` white, `textSecondary` (123, 123, 123), `textTertiary` for placeholder amounts. There is no light theme to design for.
- Rhythm: the steps in rule 5 are what the shipped screens use most. The stragglers (10, 15, 14, 28, 30) are drift, not steps. No spacing scale exists yet; adding one is a system decision.
- Chat transcript rhythm is its own: row gaps 5 inside a run, 10 between runs, 15 on a speaker change; bubble body padding 9/12; bubble radius 12 with 4 on grouped inner corners. Judge the transcript against the chat spec (in the iOS repo, `.claude/plans/2026-09-29-figma-component-library.md`, chat pass), not the screen rhythm.
- Radii: buttons are capsules (icon-only buttons circles) and never take the 6 radius. Chat bubbles are 12 with 4 on grouped corners. The continuous corner is the house's corner, even where older styles still draw circular ones.
- Heights: button 54, thin button 44, gap between buttons 12, button inset 20, button line width 1; 32 under a screen-ending text link.
- Button ladder, all capsules: `filled` (white fill, black text) is the one primary; `subtle` (white 10%, `actionSecondary` text) is the one secondary; `textLink` (no fill, text at 60%, 44 pt) for a help or escape link, or the secondary under a primary on a full screen; `filled05` (the row tint, for an action that reads as a row); `icon` (white 10% with an 18 pt asset); `card` for tiles; `filledCompact` and `subtleCompact` at 44. Pressed is the house press, 0.95 over a 0.2 s spring.
- Screen skeleton: the `backgroundMain` fill, a vertical scroll with no indicator, one column with no default spacing, 20 at the sides.
- Rows: 12 between a row's elements over a bottom `rowSeparator`; settings rows take 25 top and bottom, a 45 pt icon slot, and the extra-small display style for titles.
- Cards: `backgroundRow` fill, radius 6 continuous, 16 inside; the bill card is radius 12 with a white 10% ring; a bordered container (radius 12, `textSecondary` 30% stroke over `backgroundRow`) groups inputs.
- Glass: on iOS 18 a glass surface falls back to ultra-thin material, and the circle button falls back to a white 7% fill with a 10% ring.
- Sheets: a partial sheet sizes itself to content on `backgroundSecondary`; a clear sheet falls back to thin material on iOS 18.
- Hairlines: the regular separator is 1 px; the medium one is 1 pt.
- Empty, loading and toasts: use the app's catalogued empty-state, skeleton-row and toast components, never a hand-built overlay.
- Money: the home balance is set at 55 pt with a numeric text transition, and an amount can pair with its currency flag.
- Reuse before build: a component in the app's component catalog before a hand-rolled stack (iOS: `.claude/docs/design/components.md`; Android has no catalog yet).

## Flag on sight

The code forms of these live in the app's implementation notes.

- Two `filled` buttons reachable in one rendered state (separate branches of a state are fine): rule 1.
- A shadow on a card, row, tile, sheet, or anything on a `backgroundRow` fill: rule 13.
- A circular corner on a 12 or 6 surface: rule 16.
- A radius of 8, 10, 16, 20, or any value other than 6, 12, and 4: rule 15.
- Spacing of 13, 15, 10, 14, or any value outside 4, 8, 12, 16, 20, 24, 40 on a screen or in a component with no documented spec value (the chat transcript's 5/10/15 row gaps are its own rhythm): rule 5.
- Bottom padding repeated on each child of a column with no spacing: rule 6.
- A hand-mixed colour, or white at 5%, where `backgroundRow` or another token fits: rule 12.
- `backgroundMain` as the fill of a sheet, card, or tile: rule 20.
- Glass on a view whose only backdrop is `backgroundMain`: rule 18.
- Glass with no iOS 18 fallback: rule 19.
- A button sized with a literal 54, 60 or 44 instead of the height tokens: rule 22.
- An icon-only button without a 44 by 44 hit area: rule 23.
- An empty branch that is only space, nothing, or a bare "No items" line: rule 24.
- A spinner as the whole loading state of a list: rule 25.
- An icon named by a string, or an emoji standing in for an icon: rule 27.
- A raw system font, or a font set from a literal size, on a screen: rule 30.
- A titled button whose style scales or dims on press instead of taking the house press: rule 21.
- A button shaped with the 6 radius: rule 15.
- A full screen's bottom actions held 20 pt above the safe area instead of 20 pt from the device edge: rule 7.
- Three equal tiles each with icon, title, and caption; a centered pill badge above a headline; a gradient with no job: rule 31.

## Before / After / Why

| Before | After | Why |
|---|---|---|
| "Add Money" `filled` directly above "Withdraw" `filled` | "Add Money" `filled` above "Withdraw" `subtle` | Rule 1: one next step; the second action still reads as a button without competing for the slot. |
| A white 5% card, radius 12, with a black 30% drop shadow (radius 8, y 4) | A `backgroundRow` card, radius 12 continuous, with a 1 pt `rowSeparator` ring inside the edge | Rules 12, 13, 16: the shadow is invisible on #19191A, the ring is the depth cue the house uses, and the token plus continuous corner match every other card. |
| An empty activity list that renders blank space | "No activity yet" in the large text style, "Money you send or receive lands here." in `textSecondary`, and an "Add Money" `filled` button | Rule 24: the empty state teaches, offers the action that fills it, and holds the height so the first row does not jump the layout. |

The code for each row is in the iOS implementation notes.

## Needs a device

- Dynamic Type at AX3 through AX5 (every type token scales): open Wallet, My Account, and a partial sheet; the balance must not clip, a 54 pt button's label must stay on one line (it shrinks to 0.8, then truncates), and no two 44 pt hit areas may overlap.
- The longest real username and display name, and a long token name, on a settings row, the token card header, and a chat bubble: the balance stays visible and the pushing spacer's minimum length holds.
- iOS 18 on a device without glass: the ultra-thin material open-card chrome, the floating tab bar, a clear partial sheet, and the wallet's legacy pill clearance at the bottom of the scroll.
- iOS 26: every glass surface has moving content beneath it, and grouped glass sits in one group so neighbors share a sample.
- Press feedback: tap each button style and confirm one feedback (the 0.95 shrink, or the 0.7 dim with Reduce Motion on) with no glass animation stacked on top; a screenshot cannot show this.
- Scroll edges: content scrolling into the status bar fades (the wallet draws its own fade because it hides its nav bar), and the last row clears the tab bar on both OS versions.
- Hairlines: a regular separator renders as one device pixel on a 3x screen, not a blurred 2 px line.
- Landscape and iPad are out of scope on iOS: iPhone only, portrait only.
- The squint test itself: hold the device at arm's length, or blur a screenshot, and name the one thing a competitor could not paste.
