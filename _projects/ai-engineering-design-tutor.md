---
title: AI Coach for Engineering Design Coursework
short: AI Coach
period: Aug 2026 - Present
tag: AI for Education
summary: A web platform where students work through engineering-design methods with an AI coach that gives grounded feedback without handing over answers.
metrics: Used by several hundred students in a Fall 2026 UB course · 17 of 18 modules running end to end
tech: Python, React, Vite, vLLM, Hybrid RAG, Multi-Agent
capabilities: [agents, rl, data, infra, eval]
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

## Next: Learning to Teach (Research Proposal)
The coach adapts to each student, but it does not yet *learn* which of its teaching moves actually worked. I am extending the project into a research proposal, **Learning to Teach with Capability-Specific Feedback**.

- **The problem: pedagogical credit assignment.** A tutoring session mixes hints, counterexamples, explanations, and practice. If the only reward is whether the final answer was right, the tutor cannot tell which intervention helped.
- **The idea: different teaching capabilities need different feedback.** Correcting a misconception, building independent reasoning, and supporting transfer to new problems are different goals, so each gets its own signal (change in the misconception, change in unscaffolded reasoning, and transfer performance), and each signal trains only the part of the tutor responsible for it. A shared skill memory lets specialized parts still learn from each other.
- **Ground truth from simulated students.** Students are simulated with seeded misconceptions (for example, adding fractions by adding tops and bottoms). Held-out probes measure each signal: an item that triggers the seeded error, a problem of the same concept with no hints, and a new surface form of the same principle. The probes are never shown during teaching, so the tutor cannot teach to the test.
- **Research questions.** Does capability-specific feedback beat a single dense reward and an outcome-only reward? Does specializing the tutor help beyond splitting the feedback? Do shared skills give positive transfer across capabilities?
- **First step.** Build the simulated students in one domain and test the first question with a single tutor and three feedback types; scale up only if it holds.

## Why It Matters
An AI tutor that simply answers questions undermines learning. This project is about the opposite constraint: giving students feedback that is specific, correct, and tied to course material, while leaving the actual design work to them.
