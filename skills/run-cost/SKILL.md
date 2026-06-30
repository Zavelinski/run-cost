---
name: run-cost
description: Estimate the token/cost of a whole TASK or WORKFLOW before running it, and propose a cheaper shape. Use before a big multi-step task, before launching a Workflow that fans out agents, or when the user is cost-sensitive. Works at task altitude (agent count x model tier x context), complementing prompt-level tools. Trigger with /run-cost or "what will this cost", "estimate tokens for this", "is this run expensive".
version: "0.1.0"
user-invocable: true
metadata:
  emoji: "🧾"
---

# run-cost

A pre-flight cost estimate for a task or workflow, BEFORE you spend the tokens. It works at the altitude of the whole run (how many agent calls, on which model tier, over how much context), not a single prompt.

## Why this exists

- Power users report apprehension about token cost with big agent harnesses and MCP integrations, and that large harnesses often "cost more and take longer" than expected. A cheap up-front estimate lets you choose the shape before paying for it.
- It complements, does not replace: `token-budget-check` (one prompt/message), `token-audit` (one SKILL.md). run-cost is the run-level view that ties those together with model choice.

## What it estimates

Decompose the planned run and sum it:

```
RUN COST ESTIMATE
- Stages / agent calls: <N>  (list them)
- Per call: input ~= context loaded + prompt ; output ~= expected tokens
- Model per call: <Opus | Sonnet | Haiku>  (from model-router if used)
- Tool/MCP overhead: <reads, searches, retries>
TOTAL (rough): ~<input>k in / ~<output>k out
DRIVERS: <the 2-3 biggest contributors>
CHEAPER SHAPE: <what to change to cut it: route cheap stages down, mask context, fewer agents>
```

## Method (rough, honest)

1. List the steps the run will actually take (stages, fan-out width, expected retries).
2. For each step estimate input tokens (context + prompt) and output tokens (what it must produce). Use file sizes and prior similar tasks as anchors; do not invent precise numbers.
3. Multiply by the model tier per step (cheap stages cost less per token AND should be smaller).
4. Sum, name the top drivers, and propose one cheaper shape.
5. Present the estimate as a RANGE with the assumptions stated. Never present a guess as a measured bill.

## How to apply

- Before a Workflow: estimate `agent calls x avg tokens x tier`. If it blows the budget, cut fan-out, route stages down (model-router), or mask context (context-warden) and re-estimate.
- For a directive like "+500k": check the projected total against the target before launching; scale depth to fit.
- After the run: compare actual output tokens to the estimate to calibrate next time.

## Composes with

- `model-router`: the per-stage tier that drives cost.
- `context-warden`: shrinks the input side of every call.
- `token-budget-check` / `token-audit`: prompt- and skill-level detail under this run-level view.

## Honest limits

- This is an ESTIMATE, not billing. It cannot read your account's live token meter; it projects from steps, sizes, and tiers.
- Accuracy depends on how well the run is specified. Vague plan in, vague estimate out. State assumptions and give a range.
