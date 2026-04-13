# Experiment 1.1 — GR00T N1.6: Zero-Shot vs Fine-Tuned on SimplerEnv

**Project page:** https://noemacee.github.io/gr00t-n16-eval/

## Hypothesis

GR00T N1.6 fine-tuned on the Fractal dataset overfits to the specific tabletop scenes in SimplerEnv, and its reported benchmark numbers do not reflect genuine generalization beyond those scenes.

## Models

| Model | HuggingFace ID | Type |
|-------|---------------|------|
| Base | `nvidia/GR00T-N1.6-3B` | Zero-shot |
| Fine-tuned | `nvidia/GR00T-N1.6-fractal` | Fine-tuned on Google Robot Fractal |

## Benchmark

**SimplerEnv** (MuJoCo-based, headless via EGL) — Google Robot (`OXE_GOOGLE` embodiment)

| Task (`--env_name`) | Fine-tuned reference |
|---------------------|----------------------|
| `google_robot_pick_coke_can` | 97.5% |
| `google_robot_close_drawer` | 87.5% |
| `google_robot_open_drawer` | 44.0% |
| `google_robot_move_near` | 75.5% |

Zero-shot baseline numbers are not published — produced by our runs.

## Results

| Task | Zero-shot | Fine-tuned |
|------|-----------|------------|
| pick_coke_can | TBD | 97.5% (ref) |
| close_drawer | TBD | 87.5% (ref) |
| open_drawer | TBD | 44.0% (ref) |
| move_near | TBD | 75.5% (ref) |

Rollout videos: [`static/videos/`](static/videos/)

## Hardware

| | |
|---|---|
| Instance | AWS g5.2xlarge |
| GPU | NVIDIA A10G — 24 GB VRAM |
| RAM | 32 GB |
| Storage | 150 GB SSD |
| OS | Ubuntu 22.04 (Deep Learning AMI) |

## Setup

```bash
# 1. Expand EBS to 150 GB in AWS console before this step

# 2. System packages
sudo apt-get update -qq
sudo apt-get install -y git curl wget tmux ffmpeg \
    libegl1-mesa-dev libglu1-mesa libgl1-mesa-glx libosmesa6-dev

# 3. Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh
export PATH="$HOME/.local/bin:$PATH"
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc

# 4. Clone Isaac-GR00T
git clone --recurse-submodules https://github.com/NVIDIA/Isaac-GR00T ~/Isaac-GR00T
cd ~/Isaac-GR00T

# 5. Install Python dependencies
bash scripts/deployment/dgpu/install_deps.sh
source .venv/bin/activate

# 6. Install SimplerEnv
bash gr00t/eval/sim/SimplerEnv/setup_SimplerEnv.sh

# 7. Download model weights
huggingface-cli download nvidia/GR00T-N1.6-3B
huggingface-cli download nvidia/GR00T-N1.6-fractal
```

## Running

Each model needs two terminals. Run from `~/Isaac-GR00T`.

```bash
export MUJOCO_GL=egl
  export PYOPENGL_PLATFORM=egl
  export VK_ICD_FILENAMES=/etc/vulkan/icd.d/nvidia_icd.json
```

**Terminal 1 — Policy server**

```bash
# Zero-shot
uv run python gr00t/eval/run_gr00t_server.py \
    --model-path nvidia/GR00T-N1.6-3B \
    --embodiment-tag OXE_GOOGLE \
    --use-sim-policy-wrapper \
    --port 5555

# Fine-tuned
uv run python gr00t/eval/run_gr00t_server.py \
    --model-path nvidia/GR00T-N1.6-fractal \
    --embodiment-tag OXE_GOOGLE \
    --use-sim-policy-wrapper \
    --port 5555
```

**Terminal 2 — Rollout client**

```bash
gr00t/eval/sim/SimplerEnv/simpler_uv/.venv/bin/python \
    gr00t/eval/rollout_policy.py \
    --policy_client_host 127.0.0.1 \
    --policy_client_port 5555 \
    --env_name <TASK> \
    --n_episodes 20 \
    --n_action_steps 1 \
    --n_envs 5 \
    --max_episode_steps 300
```

Run all 4 tasks × 2 models = 8 runs total.

## Notes

- TBD
