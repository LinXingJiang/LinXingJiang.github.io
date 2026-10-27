---
title: Self-Evolving Agent Skills
short: Skill Evolution
period: Aug 2026 - Present
tag: Research · Agent Self-Improvement
summary: Research extensions to SkillOpt, which trains an agent's skill document instead of its weights, focused on making learned rules actually get followed.
metrics: Rule gate + failure-pattern lineage · experiments on ALFWorld, LiveMath, SealQA, DocVQA
tech: Python, Qwen3, LLM-as-Judge, ALFWorld, SkillOpt
capabilities: [agents, memory, eval]
featured: true
order: 2
---

## Background
[SkillOpt](https://github.com/microsoft/SkillOpt) (Microsoft Research) treats an agent's skill document as trainable state: an optimizer model turns scored rollouts into small edits to a Markdown skill file, and an edit is kept only if it improves a held-out validation score. The model weights never change. My work builds on this open-source framework.

## What I Built
- **Rule gate at rollout time.** A separate judge model checks each proposed action against the skill's "Non-Negotiable Rules" section before it runs. It only approves or rejects, never rewrites the action, and fails open on errors. Because it reads rule text directly, any rule the optimizer writes can be enforced in any environment without hand-written Python checks.
- **Failure-pattern lineage.** Extended the optimizer so recurring failure patterns across batches, along with partially correct patterns, are aggregated and carried forward as evidence into skill updates, instead of each batch being reflected on in isolation.
- **Controlled experiments.** Ran ablations across ALFWorld, LiveMathematicianBench, SealQA, DocVQA, and SpreadsheetBench with Qwen models, comparing advisory vs. strict rule framing, gated vs. ungated execution, and different edit policies.

## What I Learned
The early results are small and I report them as such: in an ALFWorld pilot, strict rule framing repaired one of six failing tasks, while the gate itself recorded no rejections, so it could not be credited yet. The main lesson so far is methodological: a gate can only be evaluated on task slices that actually provoke rule violations, and skill-training claims need paired comparisons, not single runs.

## Why It Matters
Self-improving agents are only useful if what they learn changes their behavior reliably. This project looks at the gap between an agent writing down a good rule and the agent actually following it.
