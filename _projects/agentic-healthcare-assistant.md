---
title: Agentic Healthcare Assistant
short: Healthcare Agent
period: Jan 2026 - Apr 2026
tag: Multi-Agent System
summary: A memory-augmented healthcare assistant unifying diagnosis, retrieval, and follow-up in one test-backed system.
metrics: 7 skills · 3 agents · 26/26 tests passing
tech: Python, FastAPI, ReAct, Mem0, Milvus
capabilities: [agents, memory, data]
featured: false
order: 7
---

## Highlights
- Designed a Skills-Agent architecture with `7` reusable skills and `3` specialized agents.
- Combined short-term memory, long-term memory, shared context, and bounded agent execution.
- Integrated diagnosis, guideline retrieval, lifestyle guidance, and research flows into one backend.
- Reached `26/26` passing end-to-end tests.

## What I Built
I built a memory-augmented multi-agent backend for healthcare assistance. The system supports personalized symptom analysis, disease-risk reasoning, retrieval over medical knowledge, and follow-up support through a modular architecture that separates skills, agents, memory, and coordination logic.

## Why It Matters
Instead of treating healthcare Q&A as a single chatbot problem, this project decomposed the workflow into constrained, reusable components. That made the system easier to test, extend, and reason about, while improving reliability for multi-step medical interactions.
