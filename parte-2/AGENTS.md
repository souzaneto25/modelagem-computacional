# AGENTS.md — parte-2

[TEORIA.md](./TEORIA.md) covers the theory of all methods. This file covers repo-specific rules.

## Framework rules

- All algorithms implemented **from scratch with NumPy** — no `scipy.optimize`, `scipy.linalg`, `sklearn`.
- `tensorflow`/`keras` allowed only for a CNN (not currently in codebase; the warning about `input()` in `rede-neural.ipynb` refers to a possible CNN cell that does not exist yet).
- Notebooks open with a markdown theory cell, followed by `## Section` markdown + code pairs.
- Comments, docstrings, and printed output are in **Portuguese**.
- Visualizations use `matplotlib`, often dark theme (`#0d1117`).

## Notebooks

| Notebook | Topic | Core algorithm |
|----------|-------|----------------|
| `pso.ipynb` | Particle Swarm Optimization | minimization + constrained LP via penalty |
| `rede-neural.ipynb` | Neural networks | perceptron, MLP (XOR), 3-input OR |
| `mdf.ipynb` | Finite Differences | Laplace & Poisson (Gauss-Seidel) |
| `momentos.ipynb` | Method of Moments | charged wire, LU from scratch |
| `elementos-finitos.ipynb` | Finite Elements | 2D Laplace, triangular mesh, checks conditioning → may use SVD/pseudo-inverse |

## Execution

```bash
source .venv/bin/activate
jupyter nbconvert --to notebook --execute parte-2/pso.ipynb   # headless, works for all except rede-neural.ipynb
```

- The only `input()` call in parte-2 is in **`elementos-finitos.ipynb`** (optional data file prompt). Still, to avoid issues with headless execution, run cell-by-cell in VS Code / Jupyter UI rather than `nbconvert --execute`.
- MLP trains for 100 k epochs (~seconds). Perceptron/OR converge nearly instantly.

## Common pitfalls

- **Perceptron on non-separable problem** (e.g. XOR) → infinite loop. Always check separability first or cap epochs.
- **PSO**: `np.clip` boundaries are hard — if the optimum lies outside `[-LIM, LIM]`, particles get stuck at the edge.
- **PSO penalty**: keep `PENALIDADE` large (≥1e6) relative to objective scale, else the optimum drifts outside the feasible region.
- **MoM / FEM**: `momentos.ipynb` and `elementos-finitos.ipynb` implement LU decomposition from scratch. Do not replace with `scipy.linalg.lu`.
