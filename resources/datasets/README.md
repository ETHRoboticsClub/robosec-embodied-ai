# Datasets

Recent real-world robot datasets used in VLA research.
Adapted from the survey (Table 1). Skill = atomic action primitives (pick, place, reach). Task = instruction-level goals.

| Dataset | Episodes | Skills | Tasks | Modality | Embodiment | Collection |
|---------|----------|--------|-------|----------|------------|------------|
| QT-Opt | 580K | 1 (Pick) | — | RGB | KUKA LBR iiwa | Learned |
| MT-Opt | 800K | 2 | 12 | RGB, L | 7 robots | Scripted, Learned |
| RoboNet | 162K | — | — | RGB | 7 robots | Scripted |
| BridgeData | 7.2K | 4 | 71 | RGB, L | WidowX 250 | Teleop |
| BridgeData V2 | 60.1K | 13 | — | RGB-D, L | WidowX 250 | Teleop |
| BC-Z | 26.0K | 3 | 100 | RGB, L | Google EDR | Teleop |
| Language Table | 413K | 1 (Push) | — | RGB, L | xArm | Teleop |
| RH20T | 110K | 42 | 147 | RGB-D, L, F, A | 4 robots | Teleop |
| RT-1 | 130K | 12 | 700+ | RGB, L | Google EDR | Teleop |
| OXE | 1.4M | 527 | 160,266 | RGB-D, L | 22 robots | Mixed |
| DROID | 76K | 86 | — | RGB-D, L | Franka Panda | Teleop |
| FuSe | 27K | 2 | 3 | RGB, L, T, A | WidowX 250 | Teleop |
| RoboMIND | 107K | 38 | 479 | RGB-D, L | 4 robots | Teleop |
| AgiBot World | 1M | 87 | 217 | RGB-D, L | AgiBot G1 | Teleop |

**Modality key:** RGB = color camera · D = depth · L = language · F = force/torque · A = audio · T = touch/tactile
