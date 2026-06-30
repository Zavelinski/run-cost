# run-cost

A pre-flight token/cost estimate for a whole **task or workflow** before you run it. Works at run altitude (agent count x model tier x context size), and proposes a cheaper shape, complementing prompt-level tools.

## Why

Power users are wary of token cost with big agent harnesses and MCP integrations, and large harnesses often cost more than expected. A cheap up-front estimate lets you pick the shape before paying for it. run-cost complements `token-budget-check` (one prompt) and `token-audit` (one skill) with the run-level view that ties them to model choice.

## What you get

`/run-cost` (skill): decompose a planned run into stages, estimate input/output tokens per stage and model tier, sum it, name the top drivers, and propose a cheaper shape. Presented as a range with stated assumptions, never a guess dressed as a bill.

Skill-only, no per-session cost.

## Install (Claude Code, local directory marketplace)

```jsonc
"extraKnownMarketplaces": {
  "run-cost": { "source": { "source": "directory", "path": "C:\\Users\\<you>\\run-cost" } }
},
"enabledPlugins": { "run-cost@run-cost": true }
```

Or after publishing: `"source": { "source": "github", "repo": "<you>/run-cost" }`.

## Usage

Run `/run-cost` (or "what will this cost") before a big multi-step task or a fan-out Workflow. Pair with `model-router` (tiers) and `context-warden` (shrink input).

## Limits

An estimate, not billing. It projects from steps, sizes, and tiers; it cannot read a live token meter. Vague plan in, vague estimate out.

## License

MIT
