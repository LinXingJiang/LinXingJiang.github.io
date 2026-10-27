---
title: Agentic Shopping Concierge
period: 2026 - Present
tag: Reliable Shopping Agent
summary: A shopping agent that grounds prices and product data in system logic instead of free-form generation.
metrics: Price leakage 100% → 10% · brand consistency 30% → 60%
tech: Python, FastAPI, Qdrant, RAG, Thompson Sampling, SSE
featured: true
order: 3
---

## Highlights
- Reduced price leakage from `100%` to `10%` in evaluation.
- Improved multi-item brand consistency from `30%` to `60%`.
- Added structured search context, shared blackboard coordination, and product-card grounding.
- Designed for reliability in real shopping decisions rather than generic chatbot fluency.

## What I Built
I designed an agentic shopping system that focuses on three core failure modes in recommendation agents: hallucinated product data, multi-turn constraint drift, and weak coordination across multi-item recommendations. The system uses structured search state, grounded product cards, and blackboard-style coordination to make outputs more reliable.

## Why It Matters
In shopping, incorrect prices or inconsistent recommendations are not harmless chat mistakes; they directly affect purchase decisions. This project moved critical constraints out of the model’s free-form generation path and into system-level guarantees.
