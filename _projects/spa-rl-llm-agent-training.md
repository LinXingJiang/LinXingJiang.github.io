---
title: SPA-RL LLM Agent Training
short: SPA-RL
period: 2025 - 2026
tag: Reinforcement Learning
summary: Turned sparse end-task rewards into stepwise training signals for multi-step LLM agents.
metrics: 3 environments · stepwise rewards · PPO pipeline
tech: Python, PPO, PRM, WebShop, ALFWorld, VirtualHome
capabilities: [agents, rl]
featured: false
order: 8
---

## Highlights
- Reproduced a full SPA-RL training workflow from supervised fine-tuning to PPO.
- Organized data collection, progress reward modeling, RL data prep, and evaluation.
- Worked across `3` benchmark environments: `WebShop`, `ALFWorld`, and `VirtualHome`.
- Framed agent training around stepwise progress attribution rather than only final success.

## What I Built
I organized and reproduced a full LLM-agent reinforcement learning pipeline centered on SPA-RL. The workflow included expert trajectory fine-tuning, exploration data handling, progress reward model training, PPO-based RL, and downstream evaluation across multi-step interactive environments.

## Why It Matters
This project was valuable because it turned a hard-to-train sparse-reward setup into something more analyzable and learnable. By attributing progress step by step, the training process becomes more informative and better aligned with long-horizon agent behavior.
