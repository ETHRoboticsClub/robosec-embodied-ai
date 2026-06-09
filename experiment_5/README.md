# Experiment 5 — Embedding-Space Attacks on GR00T-N1.6: Visual-Patch Quirks and the Limits of Pure-Embedding Hijacking

## Hypothesis

If the flow-matching DiT reads only the post_vlln token embeddings, then inverting the VLM backbone
toward a reachable target embedding `E*` (with the DiT never differentiated) should reproduce the
behavioral hijack — and ideally generalize to arbitrary, non-occluding patch placements.

## Goal

We try to make GR00T-N1.6 grab the **bottle** while it is told "pick up the orange", using an
adversarial image patch. Three robust model quirks redirect the grab; a pure-embedding (DiT-free)
version works at the bottle's edge but does NOT generalize to arbitrary placement.

## Sub-experiments

| ID | Model / Setup | Status |
|----|---------------|--------|
| [5.1](5.1/README.md) | Redirecting GR00T-N1.6 with a visual patch — what works, what doesn't, and why | Wrapped up |

## Methodology

- Target: `nvidia/GR00T-N1.6` (fractal / `OXE_GOOGLE` embodiment), in SimplerEnv's Google multi-object pick scene (orange far-left, coke can center, white bottle right).
- Prompt held fixed at "pick up the orange"; attack success = an env-confirmed grab of the **bottle**. All evals N=20 closed-loop episodes.
- Forward path: image patch → Eagle VLM backbone → vision-language LayerNorm (`vlln`) → post_vlln embeddings `[1,126,2048]` → flow-matching DiT → action decoder → 7-DoF arm. The DiT sees only post_vlln.

## Results

- **Part A — model quirks:** edge square (80×80, 3px above the bottle) 65%; full-width foreground strip (320×128) 55%; occluding the orange (48×48) 35%. Baselines: clean 5%, random patch 0%, ceiling (ask for bottle) 100%.
- **Part B — embedding attack:** honest pure-embedding patch (random init, DiT never differentiated) reaches 60% at the bottle's edge (val_collision 0.0371). The position test: moved to the empty top-left corner with a *tighter* match (val 0.0264), it collapses to 0% — both honest and DiT-gradient-probe.

## Notes

- The redirect is edge-proximity-bound, not position-agnostic. Embedding-match quality (val_collision) does not port across placements; the open-loop action gate over-predicts. Clean negative result for a position-agnostic pure-embedding hijack on this VLA.
