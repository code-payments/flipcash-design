# flipcash-design

This repo holds the Flipcash House Standard and ships it as a Claude Code plugin. Aiden owns it, and
taste calls are his.

## Designing a screen in Figma

Read these before building anything:

1. `docs/figma-screens.md`: the Screen Kit. Every frame is an instance of a Screen component, filled
   through its slots from the Components – Flipcash library.
2. `docs/design-standard.md`: what Flipcash is, the principles, the patterns.
3. `docs/lenses/layout-and-hierarchy.md` and `docs/lenses/states-and-copy.md`.
4. `docs/design-decisions.md` for anything that looks settled.

The skills in `skills/` run only once the plugin is installed (see `README.md`):
`/flipcash-design:design-spec` turns an idea into a Figma spec, and `/flipcash-design:critique`
reviews a frame against the standard. Figma work needs the Figma plugin and its MCP server, set up as
the README describes.

## Changing the standard

- Keep lens rule numbers stable. Decisions and reviews cite them ("layout lens 17").
- `docs/design-decisions.md` is dated and newest first. A decision that becomes a rule also lands in
  `docs/design-standard.md`.
- No app code here. A rule's code form lives in the app repo's `.claude/docs/design/implementation.md`,
  its components in that repo's `.claude/docs/design/components.md`.
- Skills and agents reference this repo's files as `${CLAUDE_PLUGIN_ROOT}/docs/...`, which Claude
  Code fills in when the plugin runs. Plain `docs/...` paths only work when Claude is opened in this
  repo.
