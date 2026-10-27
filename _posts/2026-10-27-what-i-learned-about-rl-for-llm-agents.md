---
layout: blog_post
title: "What I Learned About Making RL Work for LLM Agents"
date: 2026-10-27
tags:
  - Reinforcement Learning
  - Agents
  - LLMs
---

Over the past few months I have spent a lot of time on training LLM agents with reinforcement learning. The papers make RL look like an algorithm problem. In practice, most of what decides whether it works happens around the algorithm.

These are the lessons that changed how I think about it.

## 1. Make the stack boring before you judge the algorithm

When a long RL run crashes or stalls, the first suspect should be the infrastructure, not the method. A scheduler, a preemptible machine, or a weight-sync path can kill runs in ways that look like training instability. The fix is unglamorous: run a control on non-preemptible hardware, make preemption failures clearly labeled in logs, and validate the whole stack on an easy task (where RL is known to help) before trying anything ambitious.

## 2. The worst bugs are silent

Some of the most damaging RL bugs never raise an error:

- **Sampling and loss disagree.** If rollouts are sampled with top-k or top-p but the loss is computed over the full distribution, the policy can quietly collapse its entropy.
- **Tokens drift between turns.** If a multi-turn harness sends text back to the inference server and it gets re-tokenized, the tokens the model is trained on are no longer the tokens it generated, while a summary metric can still claim everything matches.
- **Regularizers that do nothing.** A KL term that is wired up but has no effect looks fine until the policy drifts.

The lesson I took: dashboards and warnings can be wrong too. Check training and inference end to end, on real trajectories.

## 3. Probe before you train

Before starting RL, measure pass@1 and pass@k on the target tasks. If pass@k is much higher than pass@1, the model already "knows" how to solve many tasks and RL has room to make that reliable. If pass@k is near zero, RL has nothing to reinforce. Probing also reveals *why* the model fails: often it found the right file and the right bug but submitted the fix in a format learned from a different harness during SFT.

## 4. Early RL gains are often habits, not abilities

The first improvements from RL frequently come from fixing behavior, such as following the output format or ending an episode instead of wandering, rather than from better reasoning. That is still useful, but it should be described honestly. It also means a quick reward jump says little about whether the model learned the skill you care about.

## 5. Too much SFT can leave nothing for RL to learn from

A heavily distilled model can be so confident that every rollout in a group looks the same. With a binary reward, identical rollouts give zero variance and therefore no gradient. What helps: finer-grained rewards (fraction of tests passed instead of all-or-nothing), tasks that are neither too easy nor too hard, filtering out groups with zero reward variance, and keeping several SFT checkpoints so you can choose a starting point that still explores.

## 6. Imitating only successes teaches the wrong confidence

Trajectories from a strong teacher rarely get stuck, so a student trained only on successful trajectories never sees what recovering from a mistake looks like. The result can be a model that is more willing to declare "done" but no better at knowing when it is actually right. One of the most promising directions I have seen is teaching agents to verify their own solution before declaring completion.

## 7. Verifiers and data deserve as much scrutiny as models

A reward is only as good as its verifier. Checks that use pattern matching instead of compiling or running code can be gamed, and RL will find the loophole. Two practices I now consider standard: run the dataset with oracle answers first to catch broken environments, and audit the *code that generates* tasks rather than inspecting tasks one by one. Data also has to match the model: tasks the model can never solve produce all-zero groups and teach nothing.

## 8. The evaluation setup can change the conclusion

This is the lesson that connects to my own research. Context-length limits and summarization can hide real RL gains; a harness the model was never trained on can make a capable model score near zero; and training data that overlaps with the benchmark inflates everything. Choosing a harness, a context budget, and a decontamination policy is part of the result, not a detail. It is the same concern behind my [proposal on evaluating vulnerability detection](/2026/10/27/how-should-we-evaluate-vulnerability-detection): before trusting a number, ask how much of it comes from the model and how much from the measurement.

## What I am taking forward

If I had to compress all of this into one rule: **in agentic RL, the model is rarely the first thing that is wrong.** Get the stack, the verifier, the data, and the evaluation right, and the algorithm usually turns out to be the easy part.
