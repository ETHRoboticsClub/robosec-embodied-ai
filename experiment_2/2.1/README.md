# Experiment 2.1 — GR00T N1.6: Task vs Scene Geometry at the System 2→System 1 Interface

## Hypothesis

The S2→S1 conditioning bottleneck of `nvidia/GR00T-N1.6-fractal` exhibits task-clustered
geometry: embeddings from the same task, collected across different scene variants, are closer
to each other than embeddings from different tasks in the same scene. The interface encodes
*what to do*, not *where you are*.

## Model

| Model | HuggingFace ID | Type |
|-------|---------------|------|
| Fine-tuned | `nvidia/GR00T-N1.6-fractal` | Fine-tuned on Google Robot Fractal |

## Benchmark

**SimplerEnv** (MuJoCo-based, headless via EGL) — Google Robot (`OXE_GOOGLE` embodiment)

| Task (`--env_name`) |
|---------------------|
| `google_robot_pick_coke_can` |
| `google_robot_close_drawer` |
| `google_robot_open_drawer` |
| `google_robot_move_near` |

## Hook Point

The S2→S1 conditioning tokens are extracted via `register_forward_hook` on the final
layer of the System 2 (Eagle2 VLM) transformer, before its output is consumed by the
System 1 (flow-matching diffusion) cross-attention. One embedding vector is captured
per rollout timestep.

## Analysis

| Method | Purpose |
|--------|---------|
| UMAP (2D) | Visualise embedding geometry — colour by task vs scene variant |
| Pairwise cosine similarity | Quantify within-task vs cross-task representational distance |

## Setup

Same hardware and environment setup as Experiment 1.1 (see [`../1.1/README.md`](../../experiment_1/1.1/README.md)).

### Additional dependencies

```bash
pip install umap-learn matplotlib seaborn
```

### Inference script

Runs 20 independent episodes, one at a time, saving videos and latents under `/tmp/latents/`:

```bash
for RUN in $(seq 1 20); do
    MUJOCO_GL=egl PYOPENGL_PLATFORM=egl VK_ICD_FILENAMES=/etc/vulkan/icd.d/nvidia_icd.json \
    gr00t/eval/sim/SimplerEnv/simpler_uv/.venv/bin/python \
      gr00t/eval/rollout_policy.py \
      --model_path nvidia/GR00T-N1.6-fractal \
      --env_name simpler_env_google/google_robot_pick_coke_can \
      --n_episodes 1 \
      --n_action_steps 1 \
      --n_envs 1 \
      --max_episode_steps 300 \
      --env_seed 1 \
      --run_tag run$RUN \
      --latent_log_dir /tmp/latents
done
```

Key flags:
- `--n_action_steps 1` — execute only the first predicted step of each 8-step action chunk (ac1)
- `--env_seed 1` — controls scene randomisation; `seed1` in data directory names
- `--run_tag run$RUN` — injected into output directory names for traceability
- `--latent_log_dir` — destination for per-timestep latent npz + json files

### Analysis script (TBD)

```bash
python analysis/umap_plot.py --embeddings-dir embeddings/ --output-dir static/
```

## Notes

- TBD
