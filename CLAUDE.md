# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Environment setup

```bash
# Create and activate the virtual environment
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# Register the Jupyter kernel
python -m ipykernel install --user --name modelagem-computacional --display-name "Python (.venv modelagem-computacional)"
```

## Running notebooks

```bash
source .venv/bin/activate
jupyter lab          # full JupyterLab UI
jupyter notebook     # classic notebook UI

# Run a single notebook non-interactively
jupyter nbconvert --to notebook --execute scripts/lista_sistemas_lineares.ipynb
```

## Project structure

This is a **Python/Jupyter repository for a computational modeling course** (UFERSA — Universidade Federal Rural do Semi-Árido).

- `scripts/` — four problem-set notebooks (listas), each covering a numerical methods topic:
  - `lista_mmq_integracao.ipynb` — Least squares (MMQ) and numerical integration
  - `lista_interpolacao.ipynb` — Polynomial interpolation
  - `lista_sistemas_lineares.ipynb` — Linear systems (direct and iterative methods)
  - `lista_zeros_funcoes.ipynb` — Root-finding for scalar and system equations
- `scripts-daniel/` — eight individual activity notebooks (`atividade-1.ipynb` through `atividade-8.ipynb`) following the same course

## Coding conventions

All numerical algorithms are **implemented from scratch** using NumPy, not delegated to `scipy.linalg` or similar high-level solvers. Each notebook opens with a cell that defines all generic helper functions (e.g. `gaussian_elimination_pp`, `jacobi`, `newton`, `lagrange_interpolation`) before the problem-by-problem cells that call them.

Notebook structure per question:
1. Markdown cell with rewritten problem statement
2. Code cell(s) with computation and printed output

Dependencies: `numpy`, `scipy`, `sympy`, `matplotlib`, `pandas`, `ipykernel`.
