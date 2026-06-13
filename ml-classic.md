# ML Classic Project Guide for AI Assistants

This file describes how this repository is structured and how any
coding assistant (Claude, Antigravity, GiyHub Copilot etc.) should work inside it.

The goal: **small, boring, reproducible ML projects** that cover 80% of
classic tabular ML work (pandas + scikit-learn, baseline models).

---

## 1. Project Skeleton (must be respected)

The intended structure is:

```text
my-ml-project/
  pyproject.toml      # dependencies & metadata (managed by uv)
  uv.lock             # exact locked versions
  data/
    raw/              # original data (read-only)
    processed/        # cleaned / feature-ready data
  notebooks/
    01_exploration.ipynb
    02_first_model.ipynb
  src/
    __init__.py
    features.py       # feature engineering helpers
    models.py         # train/evaluate code
  reports/
    figures/
  README.md
  ml-classic.md       # this file
```

Assistant rules:

- Do **not** invent new top-level folders.
- Prefer adding new Python modules under `src/` if needed.
- Treat `data/raw/` as read-only; write derived data to `data/processed/`.
- Put ad-hoc experimentation in `notebooks/`, not in `src/`.

---

## 2. Environment and Dependencies (uv only)

The environment is managed solely by **uv**.

- All dependencies must be added via:

  ```bash
  uv add <package>
  ```

- Do **not** edit `pyproject.toml` or `uv.lock` by hand.
- Do **not** suggest `pip install`, `conda install`, or global installs.
- Assume the environment is created / reproduced with:

  ```bash
  uv sync
  ```

Expected core libraries:

- `numpy`, `pandas`
- `scikit-learn`
- `matplotlib`, `seaborn`
- `jupyterlab` or `notebook`

Optional (but only when needed and explicitly requested):

- `xgboost`, `lightgbm`
- `polars`

Assistants should **resist adding extra libraries** unless there is a
clear benefit and the change is small and justified.

---

## 3. Code Organisation Guidelines

When generating or modifying code:

- Put reusable logic in `src/`, not inside notebooks.
  - Feature engineering → `src/features.py`
  - Model training / evaluation → `src/models.py`
- Keep functions **small and composable**:
  - Prefer pure functions over large, stateful classes.
- Respect existing naming and style; follow PEP 8 when in doubt.

Examples:

- New feature transformation?  
  → add a function in `src/features.py`, then call it from a notebook.

- New model training routine?  
  → add it in `src/models.py`, return the fitted estimator and metrics.

---

## 4. Notebooks vs Scripts

Notebooks are for:

- Exploration
- Visualisation
- Running small experiments

Scripts / modules in `src/` are for:

- Code that will be re-used
- Code that must be testable and importable

Assistant behaviour:

- If asked to “clean this up” or “productionise” a notebook cell,
  move logic into a function in `src/` and call it from the notebook.
- Avoid creating large monolithic notebooks with complex logic duplicated
  in many places.

---

## 5. Jupyter / VS Code Workflow Assumptions

Assume the user:

- Uses **VS Code** with the Jupyter extension.
- Uses the **uv-managed environment** as the Python interpreter / kernel.

Do **not** assume:

- Custom Conda environments
- Docker as the default
- Manually launched Jupyter servers, unless explicitly requested

If you need to mention commands, use:

```bash
uv sync
# then open folder in VS Code and select the uv interpreter as kernel
```

---

## 6. Data Handling

- Do not commit large raw datasets to the repo unless explicitly allowed.
- When suggesting file paths:
  - Input data → `data/raw/...`
  - Clean / feature-engineered data → `data/processed/...`
  - Generated figures → `reports/figures/...`
- When writing examples, use small, synthetic data if real data is absent.

---

## 7. Scope of This Repo

This repo is for **classic ML**:

- Tabular data
- scikit-learn models
- Baseline evaluation (accuracy, precision/recall, RMSE, etc.)

Assistants should **not**:

- Introduce heavy deep learning stacks (TensorFlow, PyTorch) by default.
- Add complex MLOps frameworks.
- Over-engineer the project structure.

Only propose heavier tooling if explicitly requested and justify why.

---

If you are a coding assistant reading this, follow these rules
by default when proposing changes, creating files, or suggesting commands.