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
jupyter nbconvert --to notebook --execute parte-1/scripts/lista_sistemas_lineares.ipynb
```

## Project structure

This is a **Python/Jupyter repository for a computational modeling course** (UFERSA — Universidade Federal Rural do Semi-Árido). The repo is split into two parts.

### `parte-1/` — Numerical methods (1st part of the course)

- `parte-1/scripts/` — problem-set notebooks (listas), each covering a numerical methods topic:
  - `lista_mmq_integracao.ipynb` — Least squares (MMQ) and numerical integration
  - `lista_interpolacao.ipynb` — Polynomial interpolation
  - `lista_sistemas_lineares.ipynb` — Linear systems (direct and iterative methods)
  - `lista_zeros_funcoes.ipynb` — Root-finding for scalar and system equations
  - `lista_runge_kutta.ipynb` — Runge-Kutta methods for ODEs
- `parte-1/scripts-daniel/` — eight individual activity notebooks (`atividade-1.ipynb` through `atividade-8.ipynb`)
- `parte-1/scripts-listas/`, `parte-1/scripts-slides/`, `parte-1/lista-gpt/`, `parte-1/listas/` — additional notebooks and slide-exercise solutions
- `parte-1/slides/` — course slide PDFs

### `parte-2/` — AI & PDE methods (2nd part of the course)

- `pso.ipynb` — Particle Swarm Optimization (minimization + linear programming via penalty)
- `rede-neural.ipynb` — Neural networks: single perceptron, multilayer (XOR), 3-input OR gate
- `mdf.ipynb` — Finite Difference Method (Laplace & Poisson via Gauss-Seidel)
- `momentos.ipynb` — Method of Moments (charged wire, LU factorization)
- `elementos-finitos.ipynb` — Finite Element Method (2D Laplace, triangular mesh)
- `TEORIA.md` — theoretical explanation of every parte-2 method
- `AGENTS.md` — AI-focused guidance (PSO for new objective functions, binary neural nets)

## Coding conventions

All numerical algorithms are **implemented from scratch** using NumPy, not delegated to `scipy.linalg`, `scipy.optimize`, `sklearn`, or similar high-level solvers. (Exception: the optional convolutional network in parte-2 may use `tensorflow`/`keras`.) Each notebook opens with a cell that defines all generic helper functions (e.g. `gaussian_elimination_pp`, `jacobi`, `newton`, `lagrange_interpolation`) before the problem-by-problem cells that call them.

Notebook structure per question:
1. Markdown cell with theory and/or rewritten problem statement
2. Code cell(s) with computation and printed output

Dependencies: `numpy`, `scipy`, `sympy`, `matplotlib`, `pandas`, `ipykernel` (plus `tensorflow`, `pillow` for the parte-2 convolutional network).
