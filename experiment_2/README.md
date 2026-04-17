# Experiment 2 — Latent Representation Geometry at the S2→S1 Interface

## Hypothesis

The S2→S1 conditioning bottleneck of GR00T N1.6 (fractal fine-tune) encodes task-clustered representations — backbone features group by task identity, not by scene or episode randomness.

## Goal

Characterize the geometry of System 2 conditioning tokens before they reach System 1 cross-attention. Determine whether the latent space clusters along task boundaries (H1) or scene/visual boundaries (H2).

## Sub-experiments

| ID | Model | Tasks | Status |
|----|-------|-------|--------|
| [2.1](2.1/README.md) | GR00T-N1.6-fractal, SimplerEnv Pick Coke Can | seed1 20-run baseline | In progress |

## Methodology

- Hook `register_forward_hook` on the final System 2 transformer layer to capture conditioning tokens (shape `(1, 107, 2048)`) at each inference call
- Compute all-pairs cosine similarity across runs (latent and action)
- Planned: multi-task UMAP colored by task vs scene variant

## Results

- TBD

## Notes

- Only the fractal fine-tuned model is tested (no base model comparison)
- npz latent files are excluded from git (too large) — kept locally only
