---
layout: page
title: How to Loop MoE
description: Foil flattens expert layers and unties attention to improve looped MoE models at fixed parameters and compute.
img: assets/img/projects/how-to-loop-moe/foil-overview.png
importance: 0
category: research
related_publications: false
github: https://github.com/SR-A-W/how-to-loop-moe
---

## How Should a Mixture-of-Experts Model Be Looped?

Looped Transformers repeatedly apply the same block, trading additional computation for greater parameter reuse. Sparse mixture-of-experts (MoE) models take a complementary approach: they store many experts but route each token to only a few. Combining the two creates a new design question: **how should experts, layers, and attention be arranged when a sparse MoE block is reused across multiple passes?**

This project answers that question with **Foil**, a flattened looped MoE architecture. Foil reorganizes the recurrent core while holding the expert parameters and expert compute per token fixed:

1. **Flatten the experts:** use fewer physical expert layers, place more experts in each layer, and make proportionally more loop passes. Every routing decision therefore chooses from a larger expert pool.
2. **Untie the attention:** give each pass its own attention parameters while keeping the experts and routers shared across passes. This restores the attention capacity that ordinary parameter sharing would discard, without adding computation.

<figure class="project-figure">
  <img src="{{ '/assets/img/projects/how-to-loop-moe/foil-overview.png' | relative_url }}" alt="Comparison of a vanilla looped MoE model with Foil, which flattens experts and uses loop-specific attention">
  <figcaption>Foil uses fewer, wider expert layers, more recurrent passes, and loop-specific attention while preserving the expert-parameter and per-token compute budgets.</figcaption>
</figure>

## Findings

The study compares looped MoE layouts at equal parameters and compute. In 20B-token pretraining, every Foil configuration achieves lower language-modeling loss than the unflattened baseline. After continued training to 100B tokens, loss improves monotonically with the degree of flattening, and the most flattened model finishes **0.012 nat below the baseline**, with downstream accuracy on par or better.

The experiments also show that flattening and looping reinforce each other: wider expert layers benefit more from additional passes, while more passes make widening more valuable. Untying attention improves loss, downstream accuracy, and routing behavior at every matched shape. The analysis further finds that load balance alone does not characterize healthy expert use; routing confidence provides a complementary diagnostic, and its per-pass peak helps identify when the gains from additional looping are close to exhausted.

The resulting design guidance is simple: under a fixed expert-parameter and compute budget, a sparse looped MoE should use appropriately more experts per layer, more passes, and pass-specific attention.

## Links

- <a href="{{ '/assets/pdf/projects/how-to-loop-moe/how-to-loop-moe-arxiv-version.pdf' | relative_url }}" download>Download the arXiv-version PDF</a>
- [Code and configurations](https://github.com/SR-A-W/how-to-loop-moe)
