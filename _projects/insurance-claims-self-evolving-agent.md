---
title: Insurance-Claims Agent with Self-Evolving Skills
short: Insurance Agent
period: Sep 2026
tag: Agentic RL · Self-Evolution
summary: A sandboxed insurance-claims service agent trained to act through a controlled skill-and-action protocol, with on-policy distillation that turns hindsight lessons into better intermediate steps.
metrics: Accuracy 82.8% → 91.9% · required-action completion 78.6% → 90.7% · hallucination 9.4% → 2.3%
tech: Python, verl, SEED, GRPO, On-Policy Distillation, LLM Agents
capabilities: [agents, rl, memory, infra, data]
featured: true
order: 3
---

## The Setting
Insurance-claims support is a hard place for an agent: workflows depend on each other (status checks, missing documents, payout explanations, disputes, escalation), and some actions write to real cases. A wrong step is worse than a wrong sentence.

## What I Built
- **A controlled step protocol.** Every turn follows `load_skills` → `load_action` → `respond`. Only one skill can be loaded per turn, and an action is allowed only if the loaded skill permits it, so the agent's action space stays inside clear boundaries.
- **Skills and tools kept separate.** Business rules live in 20 insurance skills covering claim progress, missing documents, payout explanations, disputes and escalation, and risk handling; tools are split into read tools (case status, document lists, payout details) and write tools (requesting documents, callbacks, escalation, appeals).
- **A claims sandbox.** Simulated claim cases, a verifier, and a regression harness let the same protocol be used for training, evaluation, and replay.
- **Synthesized training data.** Conversations are exported both as step-level SFT samples and as full-session RL rollouts.
- **Reward design.** Rewards combine the final outcome, process compliance, factual grounding, efficiency, response quality, and risk penalties; the per-item scores also decide which samples are kept, rewritten, reviewed, or dropped.
- **Learning from hindsight.** Lessons extracted from complete trajectories are distilled back into the intermediate steps with SEED, self-evolving on-policy distillation ([Wu et al., 2026](https://arxiv.org/abs/2607.14777)), alongside GRPO on whole trajectories.

## Results
On a fixed evaluation set of 500 cases:

- Claim-handling accuracy improved from `82.8%` to `91.9%`.
- Completion of required actions improved from `78.6%` to `90.7%`.
- Hallucination rate dropped from `9.4%` to `2.3%`, with hard-violation rate held at `1.6%`.
- Protocol validity stays above `98.7%`.

## Why It Matters
It connects two threads in my work: constraining what an agent is *allowed* to do at each step, and letting the agent improve from its own experience without losing those constraints.
