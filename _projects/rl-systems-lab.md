---
title: "RL Systems Lab: Fingerprinting Silent Bugs in RL Training"
short: RL Systems Lab
period: Oct 2026 - Present
tag: RL Infrastructure
summary: Hands-on RL training experiments in verl where I deliberately reproduce the silent bugs that break real runs, to learn what each one looks like in the training curves.
metrics: 5 controlled bug experiments · top-p truncation found to cause entropy explosion, not collapse
tech: Python, verl, vLLM, GRPO, Weights & Biases
capabilities: [rl, infra, eval]
featured: false
order: 5
---

## The Idea
The most dangerous bugs in RL training do not crash. They quietly change what the model learns, and the run still looks plausible. Large open efforts have lost whole experiments to bugs like these. I wanted to be able to recognize them **from the curves alone, without reading the config**, so I started reproducing them on purpose.

## The Method
- **Baseline first.** GRPO on GSM8K, run twice, so the gap between two identical runs measures noise and tells me which differences are real.
- **One change per experiment**, plotted against the baseline, with a written prediction before each run and a log entry after.
- **Five silent bugs**, each modeled on a failure seen in real large-scale RL:
  1. **Sampling truncation.** Rollouts sampled with top-p = 0.9 while the loss uses the full distribution, so training and sampling disagree.
  2. **KL outside the gradient.** Comparing a KL coefficient of 0.001 against 10: if both runs look the same, the KL term never reached the gradient.
  3. **Infrastructure failures scored as wrong answers.** A reward function that randomly returns 0 for 20% of samples, mimicking verifier timeouts.
  4. **The fix for (3).** Masking infrastructure errors with a sentinel value instead of training on them, with and without std normalization.
  5. **Filtering uninformative groups.** Dropping all-correct and all-wrong groups (dynamic sampling) and measuring the cost per step.

## Findings So Far
- **Truncated sampling made entropy explode, not collapse.** I predicted the usual collapse story; the run went the other way. The mechanism is the same mismatch, but the direction depends on the setup, which is exactly why reading the curves matters more than memorizing the story.
- Along the way I fixed the kind of engineering problems that block real runs: an incompatible vLLM version, a missing package in the installer, a synchronous-mode hang on the final step, entropy logging that is off by default, and an out-of-memory error once it was turned on.

## Why It Matters
RL results are only as trustworthy as the stack that produced them. This lab is my way of learning that stack from the inside: what a correct run looks like, and what each kind of wrong run looks like.
