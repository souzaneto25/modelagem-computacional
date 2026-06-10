# AGENTS.md — Parte 2 (Inteligência Artificial e Métodos Numéricos)

Guia para agentes de IA trabalhando nos notebooks da **parte-2**. O foco principal
desta pasta é **Inteligência Artificial** — especialmente **PSO** (otimização) e
**Redes Neurais** (problemas binários / portas lógicas) — além de métodos numéricos
de EDPs (MDF, MoM, MEF).

> 📖 A teoria completa de cada método está em [`TEORIA.md`](./TEORIA.md).

## Convenções desta pasta

- Algoritmos **implementados do zero** com NumPy — **não** usar `scipy.optimize`,
  `sklearn` ou solvers de alto nível para o núcleo do método. `tensorflow`/`keras`
  são permitidos apenas na rede **convolucional** (CNN).
- Cada notebook abre com uma **célula markdown de teoria**, seguida de pares
  *título (markdown) + código*.
- Comentários, docstrings e saídas em **português**.
- Visualizações com `matplotlib`, frequentemente em tema escuro (`#0d1117`).

## Notebooks

| Notebook | Tema | Núcleo |
|----------|------|--------|
| `pso.ipynb` | Otimização por Enxame de Partículas | minimização + programação linear (penalidade) |
| `rede-neural.ipynb` | Redes neurais | perceptron, MLP (XOR), porta OR de 3 entradas |
| `mdf.ipynb` | Diferenças finitas | Laplace, Poisson (Gauss-Seidel) |
| `momentos.ipynb` | Método dos Momentos | fio carregado, fatoração LU |
| `elementos-finitos.ipynb` | Elementos finitos | Laplace 2D, malha triangular |

---

## 🎯 Foco 1 — PSO: otimizar uma função diferente

O PSO em `pso.ipynb` é **genérico**. Para adaptá-lo a uma **nova função objetivo**,
siga este checklist:

### Minimização sem restrições

1. **Troque a função objetivo** `f(x, y)`:
   ```python
   def f(x, y):
       return (x - 3)**2 + (y + 1)**2  # novo mínimo em (3, -1)
   ```
2. **Ajuste o domínio** `LIM` para conter o ótimo esperado (busca em `[-LIM, LIM]`).
3. **Ajuste os parâmetros** se necessário:
   - `n` (nº de partículas): mais partículas → busca mais robusta, mais custo.
   - `m` (iterações): aumente se a convergência não estabilizar.
   - `w_ini`/`w_fin`: inércia maior explora mais; menor refina (explota).
   - `c1`/`c2`: cognitivo vs social (padrão `1.5`/`1.5` funciona bem).
4. **Mantenha o `np.clip`** para prender as partículas no domínio.
5. O melhor global fica em `gb` (posição) e `fgb` (valor).

### Maximização

PSO minimiza por padrão. Para **maximizar**, há duas opções:
- minimizar `−f(x, y)`, **ou**
- inverter as comparações para `>` e usar `np.argmax` (como faz o 2º script).

### Otimização com restrições

Use o **método da penalidade** (2º script): some uma penalidade quadrática para
cada restrição violada antes de comparar os valores. Mantenha `PENALIDADE` grande
(`1e6`) em relação à escala da função objetivo.

### Generalizar para N dimensões

O código usa shape `(n, 2)`. Para `d` variáveis, troque `2` por `d` em `x`, `v`,
`pb`, `r1`, `r2`, e faça `f` aceitar um vetor. A lógica do loop **não muda**.

### Armadilhas comuns

- **Convergência prematura** num mínimo local → aumente `n`, `w_ini` ou a diversidade.
- **Domínio pequeno demais** corta o ótimo → o `np.clip` "gruda" partículas na borda.
- **Penalidade pequena** → o ótimo "vaza" para fora da região viável.

---

## 🧠 Foco 2 — Redes Neurais: problemas binários / portas lógicas

`rede-neural.ipynb` cobre o aprendizado de **funções booleanas**.

### Escolher o modelo certo

- **Linearmente separável** (AND, OR, NOT, NAND) → **perceptron** (função degrau).
- **Não linearmente separável** (XOR, XNOR) → **rede multicamada** (MLP com `tanh`
  + backpropagation).

> Regra prática: se conseguir separar as classes 0/1 com **uma única reta/plano**,
> o perceptron resolve. Senão, precisa de camada oculta.

### Criar uma nova porta lógica com o perceptron

1. Defina **todas** as combinações de entrada (`2ⁿ` linhas para `n` bits).
2. Defina o vetor `saidas` com a tabela-verdade desejada.
3. Use `pesos` com tamanho `n` (uma por entrada).
4. Treine com a regra `pesos[j] += taxa · entrada[j] · erro`.
5. O laço `while erroTotal != 0` **só termina se o problema for separável** — para
   XOR, ele **não convergiria** (use o MLP).

Exemplo já incluído: **porta OR de 3 entradas** ao final do notebook (converge em
~9 épocas para pesos `[1.1, 1.1, 1.1]`).

### Adaptar a rede multicamada (MLP)

- A arquitetura é `entradas → camada oculta → saída`, definida pelos shapes de
  `pesos0` e `pesos1`. Para mais neurônios ocultos, mude `(2, 3)` → `(2, k)` e
  `(3, 1)` → `(k, 1)`.
- `tanh` produz saídas em `[−1, 1]`; o limiar de decisão usado é `0.5`.
- `taxaAprendizagem` e `momento` controlam velocidade/estabilidade do treino.
- Aumente `epocas` se o erro não cair o suficiente.

### Armadilhas comuns

- **Perceptron em problema não separável** → laço infinito. Sempre confirme a
  separabilidade antes (ou limite o nº de épocas).
- **Limiar/threshold** da função degrau (`soma >= 1`) afeta a convergência das
  portas — ajuste junto com a inicialização dos pesos.
- **MLP sem normalizar** as saídas-alvo para o intervalo da `tanh` pode não treinar.

---

## Executar um notebook

```bash
source .venv/bin/activate
jupyter nbconvert --to notebook --execute parte-2/pso.ipynb

# ⚠️ rede-neural.ipynb usa input() (menu interativo na CNN, se presente) —
# rode célula a célula no Jupyter/VS Code, não com --execute.
```

> A MLP roda 100 000 épocas — a execução leva alguns segundos. O perceptron e a
> porta OR convergem quase instantaneamente.

## Referência

- Teoria detalhada: [`TEORIA.md`](./TEORIA.md)
- Setup do ambiente: `CLAUDE.md` / `README.md` na raiz.
