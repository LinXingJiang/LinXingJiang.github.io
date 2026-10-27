---
title: AI Coach for Engineering Design Coursework
short: AI Coach
period: Aug 2026 - Present
tag: AI for Education
summary: A web platform where students work through engineering-design methods with an AI coach that gives grounded feedback without handing over answers.
metrics: Used by several hundred students in a Fall 2026 UB course · 17 of 18 modules running end to end
tech: Python, React, Vite, vLLM, Hybrid RAG, Multi-Agent
capabilities: [agents, data, infra, eval]
featured: true
order: 1
---

## Highlights
- Lead developer of a research platform for a University at Buffalo engineering-design course (PI: Prof. Reza Rashidi, MAE).
- Students work through structured design-method modules such as Gantt charts, QFD, Pugh matrix concept selection, and FMEA.
- The AI coach gives bounded, evidence-grounded feedback and is designed not to produce the answer for the student.
- 17 of the 18 planned modules run end to end, from the React frontend through the API, prompt engine, and AI engine to the database.

## What I Built
The AI engine combines an agent harness, a lead agent, memory, coach agents, a skill library, and a guardrail layer. Feedback is grounded through hybrid BM25 + dense retrieval over a hand-curated course corpus, including the current semester's lecture files, and the assistant cites the material it relies on.

A recurring design choice was to keep the model out of anything that can be checked deterministically. In the concept-selection module, for example, Python verifies counts and arithmetic, points to the exact cells a student got wrong, and explains how to redo them; the model is only called to judge the quality of written reasoning. One module also runs as a real-time Socratic dialogue with turn-level quality gating.

The system runs on a shared university GPU server with a local vLLM endpoint, with a separate staging deployment, per-project coach context, instructor/TA sandboxes, rubric-based scoring, and PDF/DOCX export.

## Why It Matters
An AI tutor that simply answers questions undermines learning. This project is about the opposite constraint: giving students feedback that is specific, correct, and tied to course material, while leaving the actual design work to them.
