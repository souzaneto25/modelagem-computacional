# AGENTS.md

This repo is split into **two parts**:

- **`parte-1/`** — numerical methods (linear systems, interpolation, MMQ/integration,
  root-finding, Runge-Kutta).
- **`parte-2/`** — AI & PDE methods (PSO, neural networks, MDF, Method of Moments, FEM).
  See [`parte-2/AGENTS.md`](parte-2/AGENTS.md) for AI-specific guidance and
  [`parte-2/TEORIA.md`](parte-2/TEORIA.md) for the theory of each method.

## Running notebooks

```bash
# Activate venv first
source .venv/bin/activate

# Run a single notebook non-interactively
jupyter nbconvert --to notebook --execute parte-1/scripts/lista_sistemas_lineares.ipynb
```

## Key facts

- This repo contains **educational Jupyter notebooks** — no CI/CD, tests, or build system
- Each notebook defines its own numerical algorithms from scratch using NumPy
- Do NOT use `scipy.linalg`, `scipy.optimize`, `sklearn` or similar high-level solvers —
  implement algorithms manually (exception: the optional CNN in parte-2 uses `tensorflow`/`keras`)
- `.venv` and `.ipynb_checkpoints` are gitignored

## Kernel setup

After creating `.venv`:
```bash
python -m ipykernel install --user --name modelagem-computacional --display-name "Python (.venv modelagem-computacional)"
```

Select the kernel in VS Code: `Ctrl+Shift+P` → "Python: Select Interpreter" → choose the `.venv` kernel.

## Reference

- Full setup guide: see `CLAUDE.md` or `README.md`