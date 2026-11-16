# DS Agent Harness — making AI agents' data analysis verifiable

*Data science · experiment design · AI evaluation* · Oct–Nov 2026 · Python · [code](https://github.com/gary1381/ds-agent-harness)

## Problem

AI agents can now answer data questions, but their worst errors look right: a wrong join, a pooled effect, a fished subgroup, a forecast with leaked future data. The question was whether a thin verification layer can cut these silent errors without blocking too many correct answers, and how to show that with evidence rather than a demo.

## Approach

A layer between the agent and the data:
- **Context:** the agent works against registered datasets and definitions.
- **Verification:** the agent's numbers are recomputed by programs.
- **Delivery gates:** an answer is allowed, held or rejected before it reaches the user.
- **Registry:** every claim goes into an append-only record.

## Evaluation method

About 4,100 real-agent attempts (Claude Code and Codex in a sandbox) across seven experiments. Every experiment was pre-registered. Questions were written blind by models that had not seen the gate code, outcomes were labeled blind by two models, and the analysis code was frozen before the runs.

## Results

| Experiment | Data | Result |
|---|---|---|
| E1 | synthetic world, 540 runs | v0.1 did not help: 24.8% vs 21.5% silent errors. Most errors came from the harness itself. |
| E1b | synthetic world, 846 runs | v0.2: **3.9% vs 20.2%** silent errors, but 28.5% of correct answers were held |
| E3a | real retail data (UCI), 888 runs | **0.0% vs 7.9%** silent errors; 26.5% false holds |
| E4 | same data, new questions, 816 runs | Not met: agents alone were already 98.3% right |
| E3d | pipeline repair (dbt + Great Expectations, NYC taxi), 144 runs | Rule met (2.8% vs 25.0%), but mostly for a reason unrelated to the gate |
| E3c | e-mail A/B test read-out (Hillstrom), 552 runs | Inconclusive (−5.6 points [−12.8, +1.7]) |
| E3b | quarterly forecast, 336 runs | Inconclusive (−3.1 points [−12.5, +6.9]) |

## What I learned

- **The gate works where it has a program:** wrong numbers, pooled effects, fished subgroups, leaked future data and unreproducible files were caught in every experiment that had a check for them.
- **The largest remaining error is "a part of the question left out".** It sits outside the programs, and a model judge for it failed its pre-registered test.
- **A gate can cause errors.** Its hold reasons pushed agents to wrong answers in some cases.
- **Benefit shows on hard questions.** On routine questions agents are already strong, so the value of a gate depends on question difficulty.

---
*Built with AI coding agents; I designed the system and experiments, set the evaluation rules and reviewed every result.*
