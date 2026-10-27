---
title: Airline Customer-Service Agent
period: 2024 - 2025
tag: Production LLM Backend
summary: Replaced a keyword FAQ workflow with a production LLM service handling real customer traffic.
metrics: 2k-6k daily inquiries · 0.93 F1 · 5.2s → 2.0s latency
tech: Python, LangGraph, FastAPI, Neo4j, Redis
featured: true
order: 4
---

## Highlights
- Served `2k-6k` daily inquiries in production.
- Reached `0.93` F1 for intent classification.
- Improved average latency from `5.2s` to `2.0s`.
- Achieved `88%` Cypher execution accuracy with Text2Cypher retrieval.

## What I Built
I built and owned a production airline customer-service backend with intent routing, tool-calling workflows, graph-based retrieval, response validation, and service-level error handling. The system replaced a rule-heavy FAQ pattern with a more flexible LLM-native architecture.

## Why It Matters
This project directly affected real user traffic. The main challenge was not just building an agent that could answer questions, but building one that was fast, reliable, and structured enough to operate in production at scale.
