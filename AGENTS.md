# AGENTS.md

Repo: educational Jupyter notebooks for a computational modeling course (UFERSA).

## Structure

- **`parte-1/`** — numerical methods (linear systems, interpolation, MMQ/integration, root-finding, Runge-Kutta).
- **`parte-2/`** — AI & PDE methods (PSO, neural networks, MDF, Method of Moments, FEM).
  See [`parte-2/AGENTS.md`](parte-2/AGENTS.md) for parte-2 specifics and [`parte-2/TEORIA.md`](parte-2/TEORIA.md) for theory.
- **`prova/`** — exam notebooks (see Workflow below).

## Rules

- All algorithms implemented **from scratch with NumPy** — do NOT use `scipy.linalg`, `scipy.optimize`, `sklearn`, or similar high-level solvers.
- Exception: the optional CNN in parte-2 may use `tensorflow`/`keras` (not currently present in codebase).
- `.venv` and `.ipynb_checkpoints` are gitignored.

## Running notebooks

```bash
source .venv/bin/activate
jupyter nbconvert --to notebook --execute parte-1/scripts/lista_sistemas_lineares.ipynb   # headless
```

## Setup

```bash
python3 -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt
python -m ipykernel install --user --name modelagem-computacional --display-name "Python (.venv modelagem-computacional)"
```

## Workflow for exam scripts (`prova/`)

When asked to create exam scripts, follow these rules:

### Output format
- Jupyter notebooks (`.ipynb`) placed in `prova/`
- **No markdown cells** — only code cells with minimal inline comments in Portuguese
- Comments are short directives, e.g. `# aplica gauss-seidel` or `# funcao objetivo`
- Minimal Portuguese variable/function naming

### Source material
- Base new scripts on `parte-2/pso.ipynb`, `parte-2/rede-neural.ipynb`, and `parte-2/mdf.ipynb`
- Theoretical questions draw from `parte-2/elementos-finitos.ipynb`, `parte-2/momentos.ipynb`, `parte-2/mdf.ipynb`
