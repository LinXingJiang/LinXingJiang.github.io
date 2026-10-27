---
title: Agentic Shopping Concierge
short: Shopping Agent
period: May 2026 - Jun 2026
tag: Agentic RL · Multimodal
summary: A multimodal e-commerce agent built for a ByteDance challenge, where it won an Excellence Award, trained with agentic RL to choose its own tool chains and kept reliable by grounding prices and constraints in the system.
metrics: Excellence Award, ByteDance challenge · accuracy 64.6 → 71.8 with agentic RL · price leakage 100% → 10%
tech: Python, FastAPI, Swift, Qdrant, bge-m3, Mem0, Qwen2.5-VL, GRPO, rLLM, Megatron-LM, SSE
github: https://github.com/LinXingJiang/Shopping_Agent
capabilities: [agents, rl, memory, infra, data, eval]
featured: true
order: 6
---

## Overview
I built this project for a ByteDance challenge in May–June 2026, where it **won an Excellence Award**. It is a complete AI shopping and customer-service agent: a Python (FastAPI) backend, a SwiftUI iOS client, and a vision-language model trained with agentic RL to handle mixed image-and-text requests such as cross-platform price comparison, second-hand authenticity checks, and refund follow-ups.

## The System
- **Layered request pipeline.** A regex intent classifier routes cart views and small talk to fast paths with zero or one LLM call; only real search requests go through the full agent.
- **Two execution modes.** A lead agent chooses between a single ReAct agent for deep, multi-turn search and a parallel swarm of category workers for multi-item requests.
- **Grounded retrieval.** Products are split into marketing, feature, review, and FAQ chunks, embedded with bge-m3, and searched in Qdrant with category, price, and brand filters applied during the search, followed by deduplication and reranking.
- **Tools.** 23 composable tools in three groups: vision (cropping, enhancement, OCR), retrieval (image-to-image and keyword search), and business operations (price comparison, authenticity checks, refund tracking).
- **Personalization.** A three-tier memory (conversation history, session profile, cross-session Mem0) and Thompson Sampling re-ranking driven by likes, add-to-carts, and dislikes.
- **Streaming.** Token-by-token responses to the iOS app over SSE.

## Agentic RL: From Imitating Tool Chains to Choosing Them
An SFT agent learns to replay the tool chains in its demonstrations. When an image is low quality, a tool fails, or evidence conflicts, it has no strategy for recovering.

- **SFT iteration.** Expanded multi-turn tool-use data with complex multi-hop trajectories, low-quality-image trajectories, and tool-failure recovery trajectories.
- **Agentic RL.** RL on 8k hard multi-hop questions with online rollouts against the real tools, combining format, final-answer, and process-quality rewards.
- **Improved GRPO for multi-turn credit assignment.** When a step fails, gradients for the tokens after it are truncated, and negative advantages are zeroed one-sidedly so that valid reasoning early in a long trajectory is not punished for a later failure.
- **Training stability.** Diagnosed and fixed entropy collapse that made rollouts nearly identical, reward hacking, and concurrent tool timeouts.

## Reliability: Keeping Critical Facts Out of Free-Form Generation
- **No hallucinated product data.** Prices and product facts are rendered from structured product cards rather than written by the model.
- **No constraint drift across turns.** Budget, excluded brands, and excluded ingredients live in structured search state instead of only in the conversation.
- **Consistent multi-item recommendations.** Workers coordinate through a shared blackboard: when one worker settles on a brand, it posts a brand signal that the others use.

## Results
- **Excellence Award** in the ByteDance challenge.
- On a 1,000-question evaluation set, accuracy rose from `64.6` (SFT) to `71.8` after agentic RL, a `+7.2` point gain; human-handoff rate dropped from `21.1%` to `15.6%`.
- Price leakage dropped from `100%` to `10%`, and multi-item brand consistency improved from `30%` to `60%`.
- Each request triggers 2–3 tool calls on average, with P50 latency around `6s`, P95 at most `12s`, and `99%` tool-call success.

## Why It Matters
Wrong prices or mismatched recommendations are not harmless chat mistakes; they directly affect purchase decisions. The project combines two lessons: RL is what lets an agent choose tool chains it was never shown, and the most important constraints should be enforced by the system around the model, not left to its free-form generation.
