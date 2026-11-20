# flipcash-design

The Flipcash House Standard as a Claude Code plugin: the design standard, the decision log, the Figma
Screen Kit and three review lenses, plus the skills and agents that use them. Designers use it to
build Figma screens from the published library. iOS and Android engineers use it in their app repos
to review UI changes against the same rules.

Aiden owns the standard. Taste calls are his.

## Install

```
/plugin marketplace add code-payments/flipcash-design
/plugin install flipcash-design@flipcash-design
```

Adding the marketplace clones this repo, so you need read access to it on GitHub. To pick up changes,
run `/plugin marketplace update flipcash-design`, then update the plugin from the **Installed** tab in
`/plugin` or with `claude plugin update flipcash-design@flipcash-design`.

### Designers: Figma

The Figma work goes through the Figma MCP server and Figma's skills (`figma:figma-use`,
`figma:figma-design-to-code`). Both come with Figma's plugin:

```
/plugin install figma@claude-plugins-official
```

Then run `/mcp` and authenticate the `figma` server. Your Figma account needs access to the
**Components – Flipcash** library; `docs/figma-screens.md` gives its file and page ids.

## What's in it

| Path | What it is |
|---|---|
| `docs/design-standard.md` | What Flipcash is, the principles, the patterns, the motion vocabulary |
| `docs/design-decisions.md` | Settled calls, dated, each with its reason |
| `docs/figma-screens.md` | The Screen Kit: how every Figma screen is built |
| `docs/lenses/` | Layout and hierarchy, motion, states and copy: numbered rules a review cites |
| `skills/design-spec` | `/flipcash-design:design-spec`: a product idea to a Figma spec in the house's materials |
| `skills/critique` | `/flipcash-design:critique`: review a PR, branch, screen or Figma frame against the standard |
| `skills/walkthrough` | `/flipcash-design:walkthrough`: a punch list of what drifted since the last walkthrough |
| `agents/` | `design-critic` judges, `design-surveyor` maps code, `design-carpenter` makes one scoped change |

## App repos

The standard says what to build. Each app keeps its own record of how:

- `.claude/docs/design/components.md`: the component catalog, with each component's code name.
- `.claude/docs/design/implementation.md`: the code forms for each lens rule, and the folders,
  commands and off-limits paths the agents need.

When the plugin runs inside an app repo, the skills and agents read both files. Without them,
`design-spec` and `critique` work from the Figma library alone, and the surveyor, carpenter and
walkthrough stop. The iOS repo has both files. Android has neither yet (its
`docs/architecture/07-design-system.md` describes the UI modules, not each component) and needs
its own catalog and notes before the code-facing agents can work there.

## Changing the standard

Open a PR here. CODEOWNERS sends it to Aiden. A rule's code form changes in the app repo's
`implementation.md`, in a PR there.
