# Set up vicaimode

In this page you install the plugin, pick which models vicaimode uses, and run your first task. Setup is one command plus a short conversation.

## Install the plugin

vicaimode ships as a plugin inside this repo. In a Claude Code chat, add the repo as a plugin marketplace, then install:

```text
/plugin marketplace add vicai/vicai
/plugin install vicaimode@vicai
```

Claude Code confirms the plugin is installed. (Alternatively, copy or symlink `vicaimode/skills/*` into `.claude/skills/` and `vicaimode/agents/*` into `.claude/agents/` to use the skills without the marketplace.)

## Pick your models

Run:

```text
/setup-vicaimode
```

[`/setup-vicaimode`](../../skills/setup-vicaimode/SKILL.md) detects the models you have access to, asks for a reasoning budget, shows you each role (code delegates, judgment, the review panels), and asks what you want. Answer the questions. It writes `~/.claude/vicaimode-models.md`, a small config file every vicaimode skill reads.

You only override what you care about. A role with no line in the config keeps the skill's default. To restore a default later, delete that role's line, or just run `/setup-vicaimode` again.

You might be wondering what happens if you use Auto. Set a role to `inherit-parent` or `auto` and vicaimode omits the subagent `model` field, so the subagent inherits your parent chat model. Both values mean the same thing, and neither is a model slug. For a panel role the value is a list, and one subagent runs per entry, so the list length sets the panel size. Setup also configures `swarm workers`, the default model for every `/swarm` worker unless a race names a model for each arm.

## Accept the verification offer, or don't

At the end of setup, `/setup-vicaimode` looks for a way to prove app behavior in your project, either a `verify-*` skill or an existing harness. If it finds neither, it offers once to generate one with [`/create-verification-skill`](../../skills/create-verification-skill/SKILL.md).

Say yes and it writes `.claude/skills/verify-<app>/`, a project-local skill that teaches agents to drive your app the way a user does. It proves the skill works once before handing it over. Say no and setup moves on. You can run `/create-verification-skill` yourself any time. [Verify and ship](./06-verify-and-ship.md#create-a-project-verification-skill) covers when it earns its place.

After setup, vicaimode skills read the config on each run.

## Run your first task

Pick something real but small, and describe it the way you'd describe it to a colleague:

```text
/vicaimode add a --json flag to this command. text output stays byte-identical. verify both.
```

Watch the todo list. Its first items are the matched playbook's steps copied in, the Feature playbook for this prompt. If `/vicaimode` skips a step, the step stays in the list with `skip: <reason>`, so you can see what it chose not to do.

From here you can type normal follow-ups. `/vicaimode` is sticky. It stays on for the conversation until you opt out by saying so.

Next: [Route work through `/vicaimode`](./02-vicaimode.md).
