---
title: "Multimodal Customer-Service Agent: From SFT to Agentic RL"
period: 2026
tag: Agentic RL · Multimodal
summary: A vision-language customer-service agent for e-commerce, moved from imitating tool chains with SFT to choosing its own tool chains with agentic RL.
metrics: Accuracy 64.6 → 71.8 on a 1,000-question eval · human handoff 21.1% → 15.6% · 99% tool-call success
tech: Python, LLaMA-Factory, rLLM, Megatron-LM, GRPO, Vision-Language Models, LLM-as-Judge
featured: false
order: 7
---

## The Problem
E-commerce support questions often mix images and text: comparing prices across platforms, judging whether a second-hand item is authentic, or following up on a refund. Answering them takes several tools in sequence. An SFT agent learns to replay the tool chains in its demonstrations, but when an image is low quality, a tool fails, or evidence conflicts, it has no strategy for recovering.

## What I Built
- **Tools.** 23 composable tools in three groups: vision (cropping, enhancement, OCR), retrieval (image-to-image and keyword search), and business operations (cross-platform price comparison, authenticity checks, refund tracking).
- **SFT iteration.** Expanded multi-turn tool-use data with complex multi-hop trajectories, low-quality-image trajectories, and tool-failure recovery trajectories.
- **Agentic RL.** RL on 8k hard multi-hop questions with online rollouts against the real tools, combining format, final-answer, and process-quality rewards, so the model learns to choose tool chains instead of copying them.
- **Improved GRPO for multi-turn credit assignment.** When a step fails, gradients for the tokens after it are truncated, and negative advantages are zeroed one-sidedly so that valid reasoning early in a long trajectory is not punished for a later failure.
- **Training stability.** Diagnosed and fixed entropy collapse that made rollouts nearly identical, reward hacking, and concurrent tool timeouts.

## Results
- On a 1,000-question evaluation set, accuracy rose from `64.6` (SFT) to `71.8` after agentic RL, a `+7.2` point gain from the RL stage.
- Human-handoff rate dropped from `21.1%` to `15.6%`.
- Each question triggers 2–3 tool calls on average, with P50 latency around `6s`, P95 at most `12s`, and `99%` tool-call success.

## What I Took From It
Most of the difficulty in agentic RL is not the RL algorithm itself but everything around it: tool reliability, rewards that cannot be gamed, and keeping training stable over long multi-turn rollouts. I wrote up the broader lessons in [this blog post](/2026/10/27/what-i-learned-about-rl-for-llm-agents).
