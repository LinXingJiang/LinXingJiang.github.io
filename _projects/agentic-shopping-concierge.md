---
title: Agentic Shopping Concierge
period: May 2026 - Present
tag: Reliable Shopping Agent
summary: A multi-agent shopping assistant that grew out of my award-winning ByteDance challenge entry, now focused on keeping prices, products, and constraints grounded instead of generated.
metrics: Excellence Award, ByteDance challenge · price leakage 100% → 10% · brand consistency 30% → 60%
tech: Python, FastAPI, Swift, Qdrant, bge-m3, Mem0, Qwen2.5-VL, Thompson Sampling, SSE
github: https://github.com/LinXingJiang/Shopping_Agent
featured: true
order: 3
---

## Origin: ByteDance Challenge
The project started as my entry for a ByteDance challenge in May–June 2026, where it **won an Excellence Award**. That version was a complete AI shopping assistant with a Python (FastAPI) backend and a SwiftUI iOS client:

- **Layered request pipeline.** A regex intent classifier routes cart views and small talk to fast paths with zero or one LLM call; only real search requests go through the full agent.
- **Two execution modes.** A lead agent decides between a single ReAct agent for deep, multi-turn search and a parallel swarm of category workers for multi-item requests.
- **Grounded retrieval.** Products are split into marketing, feature, review, and FAQ chunks, embedded with bge-m3, and searched in Qdrant with category, price, and brand filters applied during the search, followed by deduplication and reranking.
- **Personalization.** A three-tier memory (conversation history, session profile, cross-session Mem0) and Thompson Sampling re-ranking driven by likes, add-to-carts, and dislikes.
- **Multimodal and streaming.** Image search through a local Qwen2.5-VL model, and token-by-token streaming to the iOS app over SSE.

## Evolution: From Working Agent to Reliable Agent
After the challenge, I kept working on the part that matters most when an agent helps someone spend money: **reliability**. I targeted three failure modes common in recommendation agents:

- **Hallucinated product data.** Prices and product facts are rendered from structured product cards rather than written by the model. In evaluation, price leakage dropped from `100%` to `10%`.
- **Constraint drift across turns.** Budget, excluded brands, and excluded ingredients live in structured search state instead of only in the conversation.
- **Inconsistent multi-item recommendations.** Workers coordinate through a shared blackboard. When one worker settles on a brand, it posts a brand signal that the others use. Multi-item brand consistency improved from `30%` to `60%`.

## Why It Matters
Wrong prices or mismatched recommendations are not harmless chat mistakes; they directly affect purchase decisions. The main lesson from this project is that the most important constraints should be enforced by the system around the model, not left to its free-form generation.
