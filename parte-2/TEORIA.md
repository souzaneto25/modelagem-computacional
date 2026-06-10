# Parte 2 — Fundamentação Teórica dos Métodos

Este documento explica, de forma teórica, cada um dos métodos implementados nos
notebooks da **parte-2** do curso de Modelagem Computacional (UFERSA).

> Cada notebook também contém uma célula de teoria no topo; este arquivo reúne
> tudo num único lugar para estudo e revisão.

## Índice

1. [Otimização por Enxame de Partículas (PSO)](#1-otimização-por-enxame-de-partículas-pso) — `pso.ipynb`
2. [Método das Diferenças Finitas (MDF)](#2-método-das-diferenças-finitas-mdf) — `mdf.ipynb`
3. [Método dos Momentos (MoM)](#3-método-dos-momentos-mom) — `momentos.ipynb`
4. [Método dos Elementos Finitos (MEF)](#4-método-dos-elementos-finitos-mef) — `elementos-finitos.ipynb`
5. [Redes Neurais Artificiais](#5-redes-neurais-artificiais) — `rede-neural.ipynb`

---

## 1. Otimização por Enxame de Partículas (PSO)

O **PSO** (*Particle Swarm Optimization*) é uma **metaheurística bioinspirada**
proposta por Kennedy e Eberhart (1995), que imita o comportamento social de
**bandos de pássaros** e **cardumes de peixes** na busca por alimento.

### Ideia geral

Um conjunto de **partículas** (soluções candidatas) "voa" pelo espaço de busca.
Cada partícula `i` possui:

- uma **posição** `xᵢ` — uma solução candidata;
- uma **velocidade** `vᵢ` — direção e passo do próximo movimento;
- a sua **melhor posição individual** `pᵢ` (*pbest*);
- o conhecimento da **melhor posição global** `g` (*gbest*) de todo o enxame.

### Equações de atualização

A cada iteração:

```
vᵢ ← w·vᵢ + c₁·r₁·(pᵢ − xᵢ) + c₂·r₂·(g − xᵢ)
xᵢ ← xᵢ + vᵢ
```

| Símbolo | Significado |
|---------|-------------|
| `w` | **inércia** — controla a exploração; decai linearmente de `w_ini` a `w_fin` |
| `c₁` | coeficiente **cognitivo** — atração à melhor posição individual |
| `c₂` | coeficiente **social** — atração à melhor posição global |
| `r₁, r₂` | números aleatórios em `[0,1]` que dão estocasticidade à busca |

O termo **cognitivo** puxa a partícula para a sua própria experiência; o termo
**social** puxa para a melhor solução do grupo. O equilíbrio entre **exploração**
(inércia alta) e **explotação** (inércia baixa) é a chave do método.

### Restrições e o método da penalidade

O PSO é, por natureza, um otimizador **sem restrições**. Para resolver um
**Problema de Programação Linear** usamos o **método da penalidade**: toda
violação de uma restrição `g(x) ≤ 0` subtrai um valor grande da função objetivo,
empurrando as partículas de volta para a **região viável**:

```
f_penalizada(x) = f(x) − λ · Σ max(0, g(x))²
```

### Nos scripts

- **Script 1** — minimiza `f(x,y) = x² + y² + 2` (ótimo em `(0,0)`, com `f = 2`).
- **Script 2** — maximiza o lucro `Z = 5x + 4y` sujeito a `6x + 4y ≤ 24` e
  `x + 2y ≤ 6`, `x,y ≥ 0` (ótimo analítico: `x=3, y=1.5, Z=21`).

> 💡 **Para otimizar outra função:** basta trocar a função objetivo `f(x,y)` e o
> domínio `LIM`. Veja `AGENTS.md` desta pasta para um passo a passo.

---

## 2. Método das Diferenças Finitas (MDF)

O **MDF** resolve **equações diferenciais parciais (EDPs)** substituindo as
**derivadas** por **aproximações discretas** calculadas sobre uma **malha** de pontos.

### Discretização

A segunda derivada é aproximada por diferenças centradas. Numa malha 2D de
espaçamento `h`, o **laplaciano** `∇²φ = φ_xx + φ_yy` vira:

```
∇²φ ≈ (φ[i+1,j] + φ[i−1,j] + φ[i,j+1] + φ[i,j−1] − 4·φ[i,j]) / h²
```

Essa é a **estrela de 5 pontos** (*five-point stencil*).

### Equação de Laplace (∇²T = 0)

Modela um campo em **regime permanente sem fontes** (ex.: temperatura numa placa).
Igualando o laplaciano a zero, cada ponto interno é a **média dos 4 vizinhos**:

```
T[i,j] = ¼·(T[i+1,j] + T[i−1,j] + T[i,j+1] + T[i,j−1])
```

### Equação de Poisson (∇²φ = −ρ/ε₀)

Inclui um **termo de fonte** `ρ` (ex.: densidade de carga elétrica):

```
φ[i,j] = ¼·(φ[i+1,j] + φ[i−1,j] + φ[i,j+1] + φ[i,j−1] + h²·ρ[i,j]/ε₀)
```

### Solução iterativa (Gauss-Seidel)

O sistema linear resultante é resolvido **iterativamente**: varre-se a malha
atualizando cada ponto, repetindo até que a maior variação `delta` entre duas
varreduras fique abaixo da tolerância `erro`. Como cada ponto usa os valores
**já atualizados** dos vizinhos na mesma varredura, trata-se do método de
**Gauss-Seidel**, que converge mais rápido que Jacobi.

### Nos scripts

- **Laplace** — distribuição de temperatura numa placa com bordas a temperaturas fixas.
- **Poisson** — potencial elétrico de duas cargas puntiformes (uma `+q`, uma `−q`).

---

## 3. Método dos Momentos (MoM)

O **Método dos Momentos** é uma técnica numérica para resolver **equações
integrais**, muito usada em **eletromagnetismo computacional**. A ideia central
é transformar uma equação integral contínua num **sistema linear** `[Z]·[q] = [b]`.

### O problema físico

Considere um **fio condutor fino** mantido a um potencial constante `V₀`. A
relação entre o potencial e a densidade de carga `q(x)` é uma **equação integral**:

```
V₀ = (1 / 4πε₀) · ∫_L  q(x') / |x − x'|  dx'
```

A incógnita não é um número, mas uma **função** `q(x)` desconhecida.

### Discretização

1. **Segmentação:** o fio de comprimento `L` é dividido em `N` segmentos de
   tamanho `Δx = L/N`.
2. **Funções de base:** assume-se densidade de carga **constante** em cada
   segmento (funções de base tipo *pulso*).
3. **Casamento (*matching*):** impõe-se a condição de contorno (potencial `V₀`)
   no centro de cada segmento.

Isso gera o sistema linear:

```
Σ_n  Z[m,n]·q[n] = b[m],     com  b[m] = 4πε₀·V₀
```

onde `Z[m,n]` é a **matriz de interação** — quantifica a contribuição da carga do
segmento `n` para o potencial no segmento `m`, calculada por uma expressão
logarítmica analítica.

### Resolução

O sistema `[Z][q] = [b]` é resolvido por **fatoração LU** (`Z = LU`) seguida de
substituições direta (em `L`) e reversa (em `U`) — **implementadas do zero**.

### No script

Calcula-se a distribuição de carga ao longo do fio, a carga total, e estuda-se
como variam com o número de subdivisões `N` e com o raio `a` do fio.

---

## 4. Método dos Elementos Finitos (MEF)

O **MEF** (*FEM*) resolve EDPs **dividindo o domínio em subdomínios pequenos e
simples** — os **elementos finitos** — sobre os quais a solução é aproximada por
**funções de forma** (polinômios). Diferente do MDF (que discretiza a *equação*),
o MEF discretiza o *domínio* e é naturalmente adequado a **geometrias irregulares**.

### Etapas do método

1. **Discretização (malha):** o domínio 2D é dividido em **elementos triangulares**,
   cada um com 3 **nós** de coordenadas `(x, y)`.

2. **Funções de forma:** dentro de cada elemento, o potencial é aproximado por uma
   interpolação **linear** entre os valores nodais: `φ(x,y) ≈ Σ αᵢ·φᵢ`.

3. **Matriz de rigidez do elemento:** minimizando o **funcional de energia** da
   equação de Laplace, obtém-se a matriz local:

   ```
   CE[i,j] = (P[i]·P[j] + Q[i]·Q[j]) / (4·A)
   ```

   onde `A` é a **área** do triângulo e `P[i], Q[i]` dependem das coordenadas dos
   nós (derivadas das funções de forma).

4. **Montagem global (*assembly*):** as matrizes de todos os elementos são somadas
   nas posições corretas, formando a **matriz de rigidez global** `[C]`.

5. **Condições de contorno:** nós com **potencial preestabelecido** (Dirichlet)
   têm suas linhas fixadas e suas contribuições passadas para o vetor `[B]`.

6. **Resolução:** resolve-se `[C]·[V] = [B]`. O script verifica o **condicionamento**
   da matriz e, se necessário, usa **SVD** ou **pseudo-inversa** para robustez.

### No script

Resolve-se a equação de Laplace 2D numa malha triangular, exibindo a distribuição
de potencial (mapa de contorno), a malha de elementos e uma superfície 3D.

---

## 5. Redes Neurais Artificiais

Uma **Rede Neural Artificial (RNA)** é um modelo computacional inspirado nos
**neurônios biológicos**. A unidade básica — o **neurônio artificial** — calcula
uma **soma ponderada** das entradas e aplica uma **função de ativação**:

```
y = f( Σ wᵢ·xᵢ + b )
```

onde `xᵢ` são as entradas, `wᵢ` os **pesos** (o que a rede aprende), `b` o viés e
`f` a função de ativação.

### 5.1 Perceptron (neurônio único)

O **perceptron** é a rede mais simples. Usa a **função degrau** (*step*):

```
f(s) = 1, se s ≥ θ ;   0, caso contrário
```

Aprendizado pela **regra do perceptron**:

```
wᵢ ← wᵢ + η·xᵢ·(d − y)
```

onde `η` é a **taxa de aprendizagem**, `d` a saída desejada e `y` a calculada. O
peso só muda quando há **erro** (`d ≠ y`).

> ⚠️ **Limitação:** o perceptron só resolve problemas **linearmente separáveis**
> (portas **AND**, **OR**, **NOT**), mas **não** resolve o **XOR**.

### 5.2 Rede Multicamada (MLP)

Para problemas **não linearmente separáveis** (como o **XOR**) usam-se **camadas
ocultas** e ativações **não lineares** (aqui, a `tanh`). O treinamento usa
**retropropagação do erro** (*backpropagation*):

1. **Forward:** propaga as entradas, calculando as saídas de cada camada.
2. **Erro:** compara a saída final com a desejada.
3. **Backward:** propaga o gradiente do erro de trás para frente (regra da cadeia),
   usando a **derivada da ativação** (`tanh'(t) = 1 − t²`).
4. **Atualização:** ajusta os pesos com a taxa de aprendizagem e um termo de
   **momento** para acelerar a convergência.

### 5.3 Separabilidade linear e portas lógicas

| Porta | Linearmente separável? | Resolve com |
|-------|:----------------------:|-------------|
| AND   | ✅ | perceptron |
| OR (2 ou 3 entradas) | ✅ | perceptron |
| NOT   | ✅ | perceptron |
| XOR   | ❌ | rede multicamada (MLP) |

### Nos scripts

- **Rede multicamada** — aprende o **XOR** com `tanh` + backpropagation.
- **Primeiro neurônio** — perceptron que aprende portas linearmente separáveis.
- **Porta OR de 3 entradas** — exemplo final: perceptron com 3 entradas que
  aprende a tabela-verdade de 8 linhas (saída 0 só para `[0,0,0]`).

---

## Dependências

Todos os notebooks usam: `numpy`, `matplotlib` (e `scipy`, `sympy`).
A rede convolucional (quando presente) requer `tensorflow` e `pillow`.

```bash
source .venv/bin/activate
pip install -r requirements.txt
```
