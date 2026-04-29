# AGENTS.md

## Running notebooks

```bash
# Activate venv first
source .venv/bin/activate

# Run a single notebook non-interactively
jupyter nbconvert --to notebook --execute scripts/lista_sistemas_lineares.ipynb
```

## Key facts

- This repo contains **educational Jupyter notebooks** — no CI/CD, tests, or build system
- Each notebook defines its own numerical algorithms from scratch using NumPy
- Do NOT use `scipy.linalg` or similar high-level solvers — implement algorithms manually
- `.venv` and `.ipynb_checkpoints` are gitignored

## Kernel setup

After creating `.venv`:
```bash
python -m ipykernel install --user --name modelagem-computacional --display-name "Python (.venv modelagem-computacional)"
```

Select the kernel in VS Code: `Ctrl+Shift+P` → "Python: Select Interpreter" → choose the `.venv` kernel.

## Reference

- Full setup guide: see `CLAUDE.md` or `README.md`