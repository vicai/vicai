# Porting notes

vicaimode is a Claude Code adaptation of [pstack](https://github.com/cursor/plugins/tree/main/pstack)
by Lauren Tan ([@poteto](https://x.com/poteto)), MIT licensed. The engineering
principles, playbooks, and skill logic are preserved verbatim; only branding and
platform plumbing were changed. This file records what changed and what still
assumes tooling that Claude Code does not ship.

## What was rebranded

| pstack | vicaimode |
|---|---|
| `pstack` (plugin) | `vicaimode` |
| `/poteto-mode` (skill + command) | `/vicaimode` |
| `/setup-pstack` | `/setup-vicaimode` |
| `poteto-agent` | `vicai-agent` |
| the poteto / Lauren Tan persona and voice | a neutral `vicai` identity |

The `LICENSE` file is unchanged and retains the upstream MIT copyright, as MIT
requires for a derivative. Attribution to the original also appears in `README.md`.

## What was ported to Claude Code

- **Plugin manifest.** `.cursor-plugin/plugin.json` → `.claude-plugin/plugin.json`
  (Claude Code plugin format). A `.claude-plugin/marketplace.json` at the repo
  root registers the plugin so it installs with
  `/plugin marketplace add vicai/vicai` then `/plugin install vicaimode@vicai`.
- **Install command.** `/add-plugin pstack` → the `/plugin` marketplace flow above.
- **Model config.** Cursor's always-applied rule at
  `~/.cursor/rules/pstack-models.mdc` → a plain config file at
  `~/.claude/vicaimode-models.md`. Every vicaimode skill reads it on each run when
  present and falls back to its built-in defaults otherwise (the `alwaysApply`
  frontmatter was dropped, since Claude Code has no equivalent auto-apply rule).
- **Subagent tool.** References to Cursor's `Task` tool → Claude Code's `Agent`
  tool / subagents in `agents/`.
- **Question tool.** `AskQuestion` → `AskUserQuestion`.
- **Skill authoring.** Cursor's built-in `create-skill` → `skill-creator`
  (available in Claude Code, e.g. `anthropic-skills:skill-creator`).
- **Product name.** "Cursor" → "Claude Code" where it referred to the agent/IDE.
- **Paths.** `.cursor/…` → `.claude/…` throughout.
- `/loop` references are unchanged: Claude Code also ships a `/loop` command.

## What still assumes external / non-bundled tooling

These were left as-is because they degrade gracefully in the original text (it
tells you to install the dependency or ask for the same outcome in plain words).
Adapt or ignore them as needed:

- **`cursor-team-kit` plugin.** Several skills mention `/deslop`, `control-cli`,
  and `control-ui`, which live in the external `cursor-team-kit` plugin, not here.
  vicaimode has its own `unslop` and `no-comments` skills covering similar ground.
- **A "built-in babysit skill."** `skills/vicaimode/SKILL.md` and the babysit
  playbook warn against routing to a built-in babysit skill "whose description
  matches the same words." Claude Code may not ship such a competing skill; the
  guidance is harmless either way.
- **`automations/benny/`.** The Benny automation pack targets a Cursor-style
  Automations runner (Cursor Automations, Slack actions, the Automations editor
  handoff). It was rebranded but not functionally ported; it needs adaptation to
  a Claude Code automation surface before it will run.
- **Model slugs** in `/setup-vicaimode` (grok, gpt-sol, fable, opus panels) are
  illustrative defaults. `/setup-vicaimode` detects the models you actually have
  and writes those; the model panel itself was not changed in this port.

## Verification

The `skills/vicaimode/scripts/` TypeScript tools (`orch`, `watch-pr`,
`check-plan`) were left functionally intact. After the reskin they still pass:

```
cd vicaimode/skills/vicaimode/scripts
bun install
bun run typecheck   # tsc --strict, clean
bun test orch watch-pr   # 52 pass, 0 fail
```
