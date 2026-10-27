---
layout: blog_post
title: "Pretraining Draws the Map, Prompts and RL Search It"
date: 2026-10-27
tags:
  - Reinforcement Learning
  - LLMs
  - Agents
  - Research Ideas
---

Does reinforcement learning teach a language model anything new? I keep coming back to a simple way of thinking about it:

> **Pretraining builds a huge search space. Prompting and RL are search strategies over that space.**

This post is about how far that picture goes, where it breaks, and what it suggests about how agents should improve. My short answer: it is a good first approximation, but it needs three corrections, and those corrections are where the interesting questions are.

## The basic picture

**Pretraining decides which paths exist.** Everything a model can say is a path through token space, and pretraining decides how likely each path is. If the knowledge needed for an answer was never learned, no prompt and no amount of RL will find it.

**Prompting picks a direction for one search.** A prompt reshapes the distribution for a single query. It is external, temporary, and reversible: remove the prompt and the model is exactly what it was.

**RL makes some paths more likely.** The strongest evidence for the "search" view is how RL learns. In GRPO-style training, the model samples a group of answers per problem, and correct answers are reinforced relative to wrong ones. If every answer in the group is wrong, there is no learning signal at all. **RL can only reinforce paths the model has already sampled.**

This matches what published results show. [Yue et al. (2025)](https://arxiv.org/abs/2504.13837) found that RL-trained models beat their base models at pass@1, but at very large k (say pass@256), the base model often solves *more* problems. RL makes the model hit the target more often, but it can also narrow what the model covers.

So far, the picture holds. Here is where it needs correcting.

## Correction 1: RL does not just search the space, it rewrites it

A prompt is a strategy you hold in your hand. RL writes the strategy into the weights, and that has two consequences a prompt does not have:

- **It narrows.** Probabilities must sum to one, so raising some paths lowers others.
- **It is not reversible.** You can take a prompt away; you cannot take RL away from a checkpoint.

A better metaphor:

> A prompt points the way for one trip. RL paves one road into a highway, and lets the side roads grow over.

### Which paths get pushed down?

Suppose a problem has four solution paths:

| Path | Before RL | After RL (no KL) |
|---|---:|---:|
| A: correct, sampled often, rewarded | 30% | **80%** |
| B: correct, rarely sampled | 10% | **3%** |
| C: wrong | 40% | 12% |
| D: wrong | 20% | 5% |

Pushing down C and D is the point of RL. Pushing down **B** is the problem: B is also correct, but it was rarely sampled, so it rarely got rewarded, and A crowded it out. This is how diversity collapses. It is also why pass@1 can rise while pass@k at large k falls: sample 128 times and you get the same solution 128 times.

### Can a KL penalty prevent this?

In theory, more than you might expect. With a KL penalty of strength β against the original model π₀, the optimal policy has a closed form:

> **π\*(path) ∝ π₀(path) · exp(reward(path) / β)**

With a binary reward, every correct path is multiplied by the *same* factor. With a factor of 4 in the example, A goes to about 55% and **B rises from 10% to about 18%**. The ratio between A and B stays exactly 3:1, and only the wrong paths lose ground. So collapse is not something the RL objective itself requires. It comes from the training dynamics: A gets sampled first, gets rewarded, becomes more likely, gets sampled even more.

In practice, KL only slows collapse down. It is estimated from samples, so it cannot protect paths that are never sampled. Training stops long before the optimum. And β is hard to tune: too large and nothing is learned, too small and nothing is protected. An entropy bonus is weaker still: it flattens the distribution for wrong paths as much as for right ones.

The direct way to protect B is to **make sure B gets sampled**: larger groups, enough exploration temperature, or objectives that reward pass@k instead of pass@1.

## Correction 2: whether a path is "reachable" depends on the budget

Take an agent task that requires 10 correct steps in a row, where the base model gets each step right half the time:

- The whole chain succeeds with probability 0.5¹⁰ ≈ **0.1%**, so you need about 1,000 samples on average.
- After RL raises each step to 90%, the chain succeeds 0.9¹⁰ ≈ **35%** of the time, so about 3 samples are enough.

Did RL teach the model something new? In terms of knowledge, no: it could already do every step. In terms of capability, yes: under any realistic budget, the task went from impossible to routine. At 30 steps the gap is even starker: 0.5³⁰ is about one in a billion.

This is why I think the most useful one-line description of RL is:

> **RL is amortized search. It pays the cost of search once, at training time, and stores the result in the weights so every later use is cheap.**

AlphaGo is the classic example: expensive tree search finds good moves, and a policy network is trained to imitate the search, until the network plays well without searching at all. Seen this way, "RL learned something new" and "RL reduced the budget needed" are usually two descriptions of the same thing. The better question is not *whether* RL expands capability, but **how much budget it saves, and on which tasks**. This also matters most for agents, whose tasks are long chains: search, read code, edit, test, edit again. Reports like NVIDIA's [ProRL](https://arxiv.org/abs/2505.24864) suggest that prolonged RL can make progress on tasks where the base model essentially never succeeds, which is consistent with this view, although the question is still debated.

## Correction 3: unlimited search only helps if you can recognize the answer

If RL is amortized search, couldn't a base model plus a good prompt and enough samples do everything RL does? Only under conditions that real agent tasks rarely meet:

1. **You need a reliable verifier.** pass@k quietly assumes someone tells you which of the k answers is correct. Math problems have answer keys. A real bug fix does not: generate 1,000 patches, and which one do you submit? Without a verifier, more samples do not help, because you cannot choose.
2. **The path must have nonzero probability.** In theory softmax gives every token some probability. In practice, top-p and top-k sampling set many paths to exactly zero, and context limits cut off long ones.
3. **The cost must not explode.** As the 30-step example shows, "possible in principle" can mean "never in practice".

| | Limited budget | Unlimited budget |
|---|---|---|
| **Reliable verifier** | RL lowers the budget needed and makes new tasks reachable | Search eventually finds it; RL just saves cost |
| **No verifier** | The model has to get it right on its own | No amount of sampling helps, because you cannot choose |

This makes verifiers, not just policies, central to agent capability. It also points to something RL can teach that search alone cannot: **knowing when an answer is right and it is time to submit.**

## What this means for agents that improve themselves

This framework shapes how I think about my own research on self-improving agents. If RL implicitly does two things, raising good paths and suppressing bad ones, then an agent can also do both **explicitly**, in context, without touching its weights:

- **Skills act as search strategies.** They describe how to construct a solution, raising the probability of good paths.
- **Rules act as search priors.** They encode recurrent failure patterns, suppressing classes of actions that keep going wrong.

The same risks follow. A rule that is too broad is the explicit version of crowding out path B: it can block correct solutions along with wrong ones. Scoping each rule to the setting where its evidence was collected plays the role that KL plays in RL. And a rule learned from a buggy grader is worse than no rule at all, so the verifier needs checking before the rules it produces can be trusted.

## How I would test it

The cleanest test is cheap: measure **pass@1 and pass@k (say k = 64) before and after** RL or rule learning.

- **pass@1 up, pass@k flat:** the method mostly improved selection, as the search picture predicts.
- **pass@1 up, pass@k down:** the method narrowed the model and crowded out other correct paths.
- **Both up:** the method expanded what is reachable, and the simple picture needs revising.

Whichever comes out, it is an answer from data rather than from an argument, which is the kind of answer I trust most.

## In one sentence

> **Pretraining draws the map. A prompt points the way for one trip. RL paves one road and lets others grow over, and on long journeys like agent tasks, paving the road is what makes the destination reachable at all.**
