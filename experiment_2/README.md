# Experiment 2 — Latent Representation Geometry at the S2→S1 Interface

## Next Steps

- Run prompt-counterfactual re-encodes on the exact rollout states used in
  [2.2](2.2/README.md) to separate prompt-conditioned signal from scene or
  behavior state.
- Extend the same linear probes beyond the clean 6-layout suite into cluttered
  and out-of-distribution scenes.
- Finish the broader geometry analysis in [2.1](2.1/README.md) so the global
  latent structure can be compared directly against the classifier result.

## Goal

Characterize what information survives at the frozen System 2→System 1 boundary
of `nvidia/GR00T-N1.6-fractal`: task identity, target position, scene identity,
or some mixture of these factors.

## Current Result

The first concrete result comes from [2.2](2.2/README.md). On a clean
6-layout, 3-object tabletop pick suite, a small linear probe trained on frozen
boundary latents decodes both:

- `task` with mean leave-one-layout-out accuracy
  `0.998 / 1.000 / 0.986` for pooled all / image / text token slices
- `target_position` with mean leave-one-layout-out accuracy
  `0.964 / 0.955 / 0.939`

This is a stronger and cleaner result than the earlier partial left/right-only
study: once the full permutation table is covered, both object and position are
strongly linearly separable.

## Sub-experiments

| ID | Model / Focus | Status |
|----|---------------|--------|
| [2.2](2.2/README.md) | GR00T N1.6, full 6-layout linear decoding of `task` vs `target_position` | First result |
| [2.1](2.1/README.md) | GR00T N1.6, task-vs-scene geometry analysis with UMAP / cosine similarity | In progress |

## Methodology

- Hook `register_forward_hook` on the final System 2 transformer layer to
  capture conditioning tokens at each inference call.
- For [2.2](2.2/README.md), train linear classifiers on frozen latents and
  evaluate with leave-one-layout-out splits.
- For [2.1](2.1/README.md), compute pairwise cosine similarity and UMAP
  projections to study broader geometry beyond simple linear decodability.

## Notes

- The current image-vs-text comparison is a post-fusion token-position slice,
  not a pure pre-fusion modality separation.
- Only the fractal fine-tuned model is tested so far; no base-model comparison
  has been added yet.
- Large latent dumps are kept locally and excluded from git.
