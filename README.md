# Embodied AI

Central hub for Embodied AI experiments within the Robotics Security Division: foundation model evaluation, adversarial robustness, and generalization research across robot policies and simulation environments.

## Experiments

| ID | Title | Status |
|----|-------|--------|
| [Experiment 1](experiment_1/README.md) | Test scene overfitting in robot foundation models | In progress |


## Structure

Each experiment lives in its own folder (`experiment_N/`) with a README describing the hypothesis, setup, and results. Sub-experiments (e.g., per-model runs) are nested as `experiment_N/N.M/`.

## Contributing

To add a new experiment:

1. Create `experiment_N/` with a `README.md` and an `index.html` (the GitHub Pages entry point).
2. Add sub-experiments as `experiment_N/N.M/` if needed.
3. Register the experiment in the table above and on the [landing page](index.html).

A starting point is available in [`_template/`](_template/) — it mirrors the structure of Experiment 1. Feel free to ignore it and build your results page however works best for your data.
