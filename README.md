# roblox-studio-dev-agents

OpenCode / OpenChamber agents for building a Roblox game with Claude and DeepSeek. Switch to the `roblox-studio-dev`
agent and prompt as usual: it splits each request by kind of work and hands every part to a subagent whose model and
effort are fixed in its file, so the right model does each job whatever model you would otherwise have picked.

| Work | Agent | Model, effort |
|------|-------|---------------|
| Dispatch, game design, planning, architecture | `roblox-studio-dev` (primary) | Opus 5.5, high |
| Routine coding: tool and Studio scripts, kit fixes, features with a decided design, refactors | `dev-coder` | Sonnet 5.5, high |
| Hard coding: new systems, debugging an unknown cause, networking, saves, security, performance, code review | `dev-coder-deep` | Opus 5.5, high |
| Research: questions and option comparisons from the code, docs and web | `dev-research` | Opus 5.5, medium |
| Modelling and animation: plans, briefs, checks renders and asserts | `dev-model-lead` | Opus 5.5, high |
| Modelling and animation: builds in Blender or Blockbench | `dev-modeller` | DeepSeek V4.1 Flash Free (OpenCode), high |
| The same, when the free model is rate-limited | `dev-modeller-go` | DeepSeek V4.1 Flash (OpenCode Go), high |
| Map assembly: layout, placing models, terrain, dressing | `dev-map` | Opus 5.5, high |
| Mechanical: decided edits, re-runs, exports, docs write-back, commits, status | `dev-mechanic` | Haiku 5.5, high |

## Install

Copy the files in `agents/` to one of:

- `<your project>/.opencode/agents/`: this project only, and everyone who clones it gets them.
- `~/.config/opencode/agents/`: every project on your machine.

Reload the project in OpenChamber (or restart OpenCode), then pick `roblox-studio-dev` in the agent list.
`opencode debug agents` shows whether they loaded and which model each one resolved to.

## Models

The ids use the Claude Agent provider (`claude-agent/opus`, `claude-agent/sonnet`, `claude-agent/haiku`) and OpenCode for DeepSeek. To save usage, `dev-model-lead`
sends every brief to the free `dev-modeller` first and moves it to the paid `dev-modeller-go` only when the free model
hits its rate or usage limit; the next brief tries the free one again. On another provider, change the
`model:` line in each file to that provider's id (`opencode models` lists them); the part after `#` is the effort.

## How it stays on the right models

- Each subagent's model is set in its file, not chosen at run time.
- The dispatcher may only spawn the six role agents. Each subagent may spawn only what its role needs (mostly
  `dev-mechanic`), so no agent re-dispatches its own work.
- Briefs are self-contained, results are checked before they are reported, and a failed check goes back to the same
  subagent.
- A model that can't be reached is reported to you; no agent silently does another role's work.
- Your project's `AGENTS.md` wins where it sets its own roles, models or rules. An explicit instruction for one task
  ("do it yourself", another model) wins for that task.

## Optional skills

The agents use these skills when they are installed and skip them otherwise: `roblox-game`, `roblox-studio-mcp`,
`blender-model-brief`, `hub-map-design`. The Roblox Studio, Blender and Blockbench MCP servers give the agents their
hands in those apps.
