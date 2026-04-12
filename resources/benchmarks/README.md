# Benchmarks

Simulation benchmarks for VLA evaluation. Task, Scenes/Objects, Observation, Physics, and Description from the survey (Table 2). Embodiments validated separately.

| Benchmark | Link | Task | Scenes / Objects | Observation | Physics | Built Upon | Embodiments | Description |
|-----------|------|------|-----------------|-------------|---------|------------|-------------|-------------|
| robosuite | [github](https://github.com/ARISE-Initiative/robosuite) | Manip | — / 10 | RGB-D, S | MuJoCo | — | **Fixed:** Panda, PandaDexRH, PandaDexLH, Sawyer, IIWA, Jaco, UR5e, Baxter, Kinova3, XArm7 · **Wheeled:** PandaOmron, Tiago · **Legged:** SpotWithArm, SpotWithArmFloating, GR1, GR1FixedLowerBody, GR1ArmsOnly, GR1FloatingBody | Modular framework, 11 tasks |
| RoboCasa | [github](https://github.com/robocasa/robocasa) | Manip | 120 / 2.5K | RGB | MuJoCo | robosuite | Franka Panda (mobile) | 100 kitchen tasks, photorealistic |
| LIBERO | [github](https://github.com/Lifelong-Robot-Learning/LIBERO) | Manip | — / — | RGB | MuJoCo | robosuite | Franka Panda | 130 tasks in 4 task suites |
| Meta-World | [github](https://github.com/Farama-Foundation/Metaworld) | Manip | 1 / 80 | Pose | MuJoCo | — | Sawyer | 50 Manip tasks for Meta-RL |
| LeVERB-Bench | [arxiv](https://arxiv.org/abs/2502.01928) | Nav, WBC | 4 / — | RGB | PhysX (Isaac Sim) | Isaac Sim | Unitree H1 | Humanoid control |
| ManiSkill | [github](https://github.com/haosulab/ManiSkill) | Manip | — / 162 | RGB-D, PC, S | PhysX | SAPIEN | Franka Panda, XArm | 4 tasks, 36K demos |
| ManiSkill 2 | [github](https://github.com/haosulab/ManiSkill2) | Manip | — / 2.1K | RGB-D, PC | PhysX | ManiSkill | Franka Panda, XArm | Extended task diversity |
| ManiSkill 3 | [github](https://github.com/haosulab/ManiSkill) | Nav, Manip, WBC | — / — | RGB-D, PC, S | PhysX | ManiSkill 2 | Franka Panda, Unitree H1, Fetch | GPU-parallelized simulation |
| ManiSkill-HAB | [github](https://github.com/haosulab/ManiSkill) | Manip | 105 / 92 | RGB-D | PhysX | ManiSkill 3, Habitat 2.0 | Fetch | HAB tasks from Habitat 2.0 |
| RoboTwin | [github](https://github.com/TeleXRobots/RoboTwin) | Manip | — / 731 | RGB-D | PhysX | SAPIEN | AgileX AgiBot G1 (dual-arm) | Dual-arm tasks |
| Ravens | [github](https://github.com/google-research/ravens) | Manip | — / — | RGB-D | PyBullet | — | UR5 | 10 tabletop tasks |
| VIMA-BENCH | [github](https://github.com/vimalabs/VIMA) | Manip | — / 29 | RGB, S | PyBullet | Ravens | UR5 | 17 multimodal prompt tasks |
| LoHoRavens | [github](https://github.com/LoHoRavens/LoHoRavens) | Manip | 1 / 3 | RGB-D | PyBullet | Ravens | UR5 | Long-horizon planning |
| CALVIN | [github](https://github.com/mees/calvin) | Manip | 4 / 7 | RGB-D | PyBullet | — | Franka Panda | Long-horizon lang-cond tasks |
| Habitat | [github](https://github.com/facebookresearch/habitat-sim) | Nav | 185 / — | RGB-D, S | Bullet | — | LoCoBot | Fast, Nav only |
| Habitat 2.0 | [github](https://github.com/facebookresearch/habitat-lab) | Nav, Manip | 105 / 92 | RGB-D | Bullet | Habitat | Fetch, Spot | Mobile manipulation (HAB) |
| Habitat 3.0 | [github](https://github.com/facebookresearch/habitat-lab) | Nav, Manip | 211 / 18K | RGB-D | Bullet | Habitat 2.0 | Fetch, Spot | Human avatars support |
| RLBench | [github](https://github.com/stepjam/RLBench) | Manip | 1 / 28 | RGB-D, S | PyBullet | V-REP | Franka Panda | Tiered task difficulty |
| THE COLOSSEUM | [github](https://github.com/robot-colosseum/robot-colosseum) | Manip | 1 / 107 | RGB-D | PyBullet | RLBench | Franka Panda | 20 tasks, 14 env variations |
| AI2-THOR | [github](https://github.com/allenai/ai2thor) | Nav, Manip | — / 118 | RGB-D, S | Unity | — | LoCoBot | Object states, task planning |
| CHORES | [github](https://github.com/allenai/ai2thor) | Nav | 191K / 40K | RGB | Unity | AI2-THOR | LoCoBot | Shortest-path planning |
| SIMPLER | [github](https://github.com/simpler-env/SimplerEnv) | Manip | 4 / 17 | RGB | PhysX | SAPIEN, Isaac Sim | Google Everyday Robot, WidowX 250 | Real-to-sim evaluation |
| RoboArena | [github](https://github.com/roboarena/roboarena) | Manip | — / — | RGB | Real | — | Real-world (unspecified) | Distributed real-world evaluation |
