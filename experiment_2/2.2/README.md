# Experiment 2.2 — GR00T N1.6: Task vs Target Position Decoding on the Full 6-Layout Suite

## Hypothesis

Once the clean object-position permutation table is complete, the
System 2→System 1 boundary of `nvidia/GR00T-N1.6-fractal` supports linear
decoding of both target object and target position. The earlier apparent
failure of object decoding on partial left/right data was caused by missing
layout permutations rather than the absence of object information in the
boundary.

## Model

| Model | HuggingFace ID | Type |
|-------|----------------|------|
| GR00T N1.6 Fractal | `nvidia/GR00T-N1.6-fractal` | Fine-tuned hierarchical VLA |

## Benchmark

**SimplerEnv custom 3-object pick suite** — Google Robot (`OXE_GOOGLE`)

| Component | Value |
|-----------|-------|
| Layouts | 6 clean permutations of bottle / coke / orange |
| Targets | `pick_bottle`, `pick_coke`, `pick_orange` |
| Dataset | 18 successful sequences, 376 sampled latent points |
| Probe targets | `task` and `target_position` |
| Evaluation | leave-one-layout-out |

## How the Classifier Was Trained

- GR00T itself stays **fully frozen**. No policy fine-tuning happens during
  this experiment.
- From each sampled rollout timestep, we extract the S2→S1 boundary latent and
  pool it into one feature vector.
- We train a **single linear layer** on top of that frozen feature vector.
- Training uses standardized features, **class-balanced cross-entropy**, and
  `AdamW` with:
  - `epochs = 300`
  - `lr = 0.05`
  - `weight_decay = 1e-4`
- We train the same probe architecture three times with different feature
  slices:
  - `pooled_all`
  - `pooled_image`
  - `pooled_text`

Mathematically, if `x ∈ R^d` is the standardized pooled latent, the probe is:

```text
z = W x + b
p(y = c | x) = exp(z_c) / Σ_j exp(z_j)
```

where:

- `W ∈ R^(K×d)` and `b ∈ R^K` are the learned parameters
- `K = 3` classes for both `task` and `target_position`

The training loss for one example is the weighted cross-entropy:

```text
L(x, y) = - α_y log p(y | x)
```

where `α_y` is the class weight for label `y`.

## How It Was Tested

The main test is **leave-one-layout-out**.

That means:

1. Pick 1 of the 6 layouts and hide it completely.
2. Train the linear probe on samples from the other 5 layouts.
3. Test only on the hidden layout.
4. Repeat this once for each of the 6 layouts.
5. Report the mean accuracy across those 6 held-out tests.

Example:

- If `OCB` is held out, training uses `BCO`, `BOC`, `COB`, `OBC`, and `CBO`.
- Testing is done only on `OCB`.

This is stricter than random train/test splitting, because the classifier must
generalize to a full object-layout permutation it never saw during training.

Both classifiers use the **same exact rollout dataset** and the **same exact
held-out splits**. The only thing that changes is the label:

- `task ∈ {bottle, coke, orange}`
- `target_position ∈ {left, middle, right}`

## Held-Out Layout Coverage

Yes. Every layout that is used as a held-out test layout contains successful
sequences for all three prompts:

| Layout | `pick_bottle` | `pick_coke` | `pick_orange` |
|--------|--------------:|------------:|--------------:|
| `OCB` | 6 | 6 | 5 |
| `BCO` | 6 | 4 | 6 |
| `BOC` | 6 | 3 | 6 |
| `COB` | 6 | 6 | 6 |
| `OBC` | 6 | 5 | 3 |
| `CBO` | 6 | 5 | 3 |

So for every leave-one-layout-out fold, the test layout includes:

- `pick_bottle`
- `pick_coke`
- `pick_orange`

In other words, yes: on every scene used at test time, we test all three task
prompts, one for each target object.

## Results

Mean leave-one-layout-out accuracy:

| Target | `pooled_all` | `pooled_image` | `pooled_text` |
|--------|-------------:|---------------:|--------------:|
| `task` | `0.998` | `1.000` | `0.986` |
| `target_position` | `0.964` | `0.955` | `0.939` |

Reading the same table by columns:

- image-token slices are slightly stronger than text-token slices for both
  labels
- text-token slices are still very strong
- this is a post-fusion token-position comparison, not a pre-fusion modality
  separation

2D geometry views:

- [`task_observer_pca_triptych.png`](static/img/task_observer_pca_triptych.png):
  PCA is a true 2D projection of the pooled latent vectors.
- [`task_observer_classifier_coordinate_triptych.png`](static/img/task_observer_classifier_coordinate_triptych.png):
  the x-axis is defined by the target-position classifier and the y-axis by the
  target-object classifier.

Figures: [`static/img/`](static/img/)

## Hardware

| | |
|---|---|
| Instance | `g5.2xlarge` |
| GPU | NVIDIA A10G (23 GB) |
| RAM | 30 GB |
| Storage | local SSD |
| OS | Ubuntu 24.04.4 LTS |

## Setup

```bash
cd /home/ubuntu/gr00t_embedding_steer

# add the missing third target to the clean 6-layout suite
/home/ubuntu/Isaac-GR00T/.venv/bin/python scripts/collect_task_observer_sequences.py \
  --stages stage1,stage2,stage3 \
  --tasks pick_coke \
  --label task_observer_coke_full6_collect \
  --n-episodes 6 \
  --gpu 0 \
  --port-base 7200

# merge the new coke rollouts with the existing bottle/orange suite
/home/ubuntu/Isaac-GR00T/.venv/bin/python scripts/combine_task_observer_manifests.py \
  --manifest runs/0068_task_observer_position_full6/combined_manifest.json \
  --manifest runs/0079_task_observer_coke_full6_collect/manifest.json \
  --label task_observer_full6_three_object
```

## Running

```bash
cd /home/ubuntu/gr00t_embedding_steer

# capture frozen boundary latents
/home/ubuntu/Isaac-GR00T/.venv/bin/python scripts/capture_task_observer_dataset.py \
  --collector-manifest runs/0080_task_observer_full6_three_object/combined_manifest.json \
  --device cuda:0

# train both classifiers on the same dataset
for TARGET in task target_position; do
  for FEATURE in pooled_all pooled_image pooled_text; do
    /home/ubuntu/Isaac-GR00T/.venv/bin/python scripts/train_task_observer.py \
      --dataset-dir runs/0080_task_observer_full6_three_object/dataset \
      --target "$TARGET" \
      --feature-key "$FEATURE" \
      --device cpu
  done
done

# generate comparison plots
/home/ubuntu/Isaac-GR00T/.venv/bin/python scripts/plot_task_observer_results.py \
  --results-dir runs/0080_task_observer_full6_three_object/dataset/observer_results \
  --label-kind task \
  --evaluation-prefix leave_one_scene_out_

/home/ubuntu/Isaac-GR00T/.venv/bin/python scripts/plot_task_observer_results.py \
  --results-dir runs/0080_task_observer_full6_three_object/dataset/observer_results \
  --label-kind target_position \
  --evaluation-prefix leave_one_scene_out_

/home/ubuntu/Isaac-GR00T/.venv/bin/python scripts/plot_task_observer_results.py \
  --results-dir runs/0080_task_observer_full6_three_object/dataset/observer_results \
  --label-kind task \
  --evaluation-prefix leave_one_scene_out_ \
  --features pooled_image,pooled_text

/home/ubuntu/Isaac-GR00T/.venv/bin/python scripts/plot_task_observer_results.py \
  --results-dir runs/0080_task_observer_full6_three_object/dataset/observer_results \
  --label-kind target_position \
  --evaluation-prefix leave_one_scene_out_ \
  --features pooled_image,pooled_text
```

## Notes

- With full permutation coverage, both `task/object` and `target_position` are
  strongly linearly decodable.
- `task/object` is slightly easier to decode than `target_position` in this
  setup.

## Next Steps

- Run prompt-counterfactual captures on the same rollout states to measure how
  much of the classifier signal comes from prompt-conditioned fusion versus
  scene or behavior state.
- Extend the same probes beyond the clean 6-layout suite into cluttered and
  out-of-distribution scenes.
- Move beyond coarse slice ablations and test token importance directly with
  leave-one-text-token-out and grouped image-token masking.
