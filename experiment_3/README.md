# Experiment 3 — Adversarial Visual Patches

## Hypothesis

A small, fixed RGB patch placed in the policy's input image can override the language conditioning of a generalist vision-language-action policy, redirecting its behaviour cross-prompt or cross-task.

## Goal

Test whether the language signal of a robot foundation model can be cleanly hijacked through the vision channel alone, without modifying weights, prompt, or environment dynamics — using only the model's own action loss as supervision.

## Sub-experiments

| ID | Target | Attack | Status |
|----|--------|--------|--------|
| [3.1](3.1/README.md) | GR00T N1.6 (fractal) on SimplerEnv multi-object scene | Cross-prompt redirection: orange-prompt → bottle pickup | First result (50%) |

## Methodology

- **Action-sequence distillation.** Freeze the full policy. Treat the patch pixels as the only trainable parameters. Optimise the patch so that `policy(image+patch, state, attack_prompt)` matches `policy(image, state, teacher_prompt)` under the model's native flow-matching action-head loss.
- **Closed-loop evaluation.** Cheap action-space MSE filters candidate patches; the simulator metric is the headline.
- **Single fixed scene seed** for the headline number — transferability is out of scope at this stage.

## Notes

- Open question: does a patch trained at one scene seed transfer to other seeds, embodiments, or prompts?
- Open question: does the same recipe hijack tasks beyond pick-and-place (e.g. drawer-open, place-on-shelf)?
