---
title: "Multimodal Search Agent: From SFT to Agentic RL"
period: 2026
tag: Agentic RL · Multimodal
summary: An end-to-end pipeline that takes a vision-language model from supervised tool-use imitation to reinforcement learning on real search tools.
tech: Python, LLaMA-Factory, rLLM, Megatron-LM, Vision-Language Models, LLM-as-Judge
featured: false
order: 7
---

## The Problem
Supervised fine-tuning teaches an agent to copy the tool chains in its demonstrations. When a tool returns a poor result, the image is low quality, or the question combines steps in a new way, an imitation-only agent has no strategy for recovering. Reinforcement learning on live tools is meant to close that gap: the agent decides which tool to call, when to switch, and when to stop.

## What I Built
- **Tools.** Text search, web search with page reading, image search, and OCR/layout parsing for documents.
- **SFT stage.** Multi-turn tool-use trajectories trained with LLaMA-Factory as a cold start.
- **RL stage.** Online rollouts against the real tools with rLLM on a Megatron backend, scoring trajectories with format, answer, and process rewards.
- **Evaluation.** An LLM judge scores final answers on held-out questions.

## What I Took From It
Most of the difficulty in agentic RL is not the RL algorithm itself but everything around it: tool reliability, reward design that cannot be gamed, and keeping training stable over long multi-turn rollouts. I wrote up the broader lessons in [this blog post](/2026/10/27/what-i-learned-about-rl-for-llm-agents).
