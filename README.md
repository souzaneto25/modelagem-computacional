# Instalação completa do ambiente Python no WSL com VS Code

Este guia mostra o processo completo para configurar um projeto Python no **WSL** usando **VS Code**, criando um **`.venv`** novo, instalando dependências e conectando o **Jupyter Notebook** ao ambiente correto.

## Pré-requisitos

Antes de começar, você precisa ter:

- **WSL** instalado no Windows
- Uma distribuição Linux no WSL, como **Ubuntu**
- **VS Code** instalado no Windows
- Extensão **WSL** da Microsoft instalada no VS Code
- O repositório já baixado no WSL

Neste exemplo, o projeto está em:

```bash
~/dev/modelagem-computacional
```

---

## 1. Abrir o terminal do WSL e entrar no repositório

```bash
cd ~/dev/modelagem-computacional
```

Confira se está na pasta certa:

```bash
pwd
ls -la
```

---

## 2. Remover o ambiente virtual antigo

Se existir um `.venv` antigo, apague para evitar conflitos:

```bash
rm -rf .venv
```

Também é útil remover um kernel antigo do Jupyter com o mesmo nome:

```bash
rm -rf ~/.local/share/jupyter/kernels/modelagem-computacional
```

---

## 3. Instalar os pacotes necessários no WSL

Atualize os pacotes do sistema:

```bash
sudo apt update
```

Instale Python, pip, suporte a ambiente virtual e alguns utilitários:

```bash
sudo apt install -y python3 python3-pip python3-venv python3-dev python-is-python3
```

Verifique a instalação:

```bash
python3 --version
python --version
pip --version
```

---

## 4. Criar o ambiente virtual `.venv`

Na raiz do projeto, rode:

```bash
python3 -m venv .venv
```

Isso cria a pasta `.venv` dentro do repositório.

---

## 5. Ativar o ambiente virtual

Ative o ambiente com:

```bash
source .venv/bin/activate
```

Se deu certo, o terminal deve mostrar algo como:

```bash
(.venv) username@DESKTOP-EK6ILMV:~/dev/modelagem-computacional$
```

Confira se o Python e o pip agora apontam para o `.venv`:

```bash
which python
which pip
```

O esperado é algo parecido com:

```bash
/home/username/dev/modelagem-computacional/.venv/bin/python
/home/username/dev/modelagem-computacional/.venv/bin/pip
```

---

## 6. Atualizar o pip

Com o `.venv` ativado:

```bash
python -m pip install --upgrade pip
```

---

## 7. Criar o arquivo `requirements.txt`

Se o projeto ainda não tiver um `requirements.txt`, crie um.

Exemplo de arquivo inicial para notebooks científicos:

```txt
jupyter
ipykernel
numpy
pandas
matplotlib
scipy
sympy
```

Você pode criar esse arquivo com:

```bash
cat > requirements.txt << 'EOF'
jupyter
ipykernel
numpy
pandas
matplotlib
scipy
sympy
EOF
```

Confira o conteúdo:

```bash
cat requirements.txt
```

---

## 8. Instalar as dependências do projeto

Ainda com o `.venv` ativado:

```bash
pip install -r requirements.txt
```

---

## 9. Registrar o kernel do Jupyter

Para que o VS Code consiga usar esse ambiente nos notebooks:

```bash
python -m ipykernel install --user --name modelagem-computacional --display-name "Python (.venv modelagem-computacional)"
```

Confira os kernels disponíveis:

```bash
jupyter kernelspec list
```

---

## 10. Abrir o projeto corretamente no VS Code usando WSL

Esse passo é muito importante.

Não abra a pasta pelo Explorer do Windows.  
Abra o projeto a partir do terminal do **WSL**:

```bash
code .
```

O VS Code deve abrir conectado ao WSL.  
No canto inferior esquerdo, normalmente aparece algo como:

```text
WSL: Ubuntu
```

Se isso não acontecer, instale ou habilite a extensão **WSL** no VS Code.

---

## 11. Selecionar o interpretador Python no VS Code

No VS Code aberto pelo WSL:

1. Pressione `Ctrl + Shift + P`
2. Procure por `Python: Select Interpreter`
3. Escolha o interpretador:

```text
/home/username/dev/modelagem-computacional/.venv/bin/python
```

Se ele não aparecer automaticamente:

1. `Ctrl + Shift + P`
2. `Python: Select Interpreter`
3. `Enter interpreter path`
4. Cole:

```text
/home/username/dev/modelagem-computacional/.venv/bin/python
```

---

## 12. Selecionar o kernel no notebook

Abra o arquivo `.ipynb` no VS Code.

No canto superior direito do notebook:

1. Clique em **Select Kernel**
2. Escolha:

```text
Python (.venv modelagem-computacional)
```

ou o kernel vinculado a:

```text
/home/username/dev/modelagem-computacional/.venv/bin/python
```

---

## 13. Testar se está funcionando

Crie uma célula no notebook com:

```python
import sys
print(sys.executable)
```

O caminho exibido deve ser algo como:

```text
/home/username/dev/modelagem-computacional/.venv/bin/python
```

Também pode testar:

```python
import numpy as np
import pandas as pd
print("Tudo certo")
```

---

## 14. Fluxo normal de uso no dia a dia

Sempre que voltar ao projeto no terminal do WSL:

```bash
cd ~/dev/modelagem-computacional
source .venv/bin/activate
```

Se precisar instalar uma biblioteca nova:

```bash
pip install nome-da-lib
```

Depois, se quiser salvar no `requirements.txt`:

```bash
pip freeze > requirements.txt
```

> Observação: esse comando grava **todas** as dependências instaladas, inclusive versões exatas.

---

## 15. Comando completo para fazer tudo de uma vez

Se quiser executar o processo inteiro do zero:

```bash
cd ~/dev/modelagem-computacional
rm -rf .venv
rm -rf ~/.local/share/jupyter/kernels/modelagem-computacional
sudo apt update
sudo apt install -y python3 python3-pip python3-venv python3-dev python-is-python3
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
cat > requirements.txt << 'EOF'
jupyter
ipykernel
numpy
pandas
matplotlib
scipy
sympy
EOF
pip install -r requirements.txt
python -m ipykernel install --user --name modelagem-computacional --display-name "Python (.venv modelagem-computacional)"
which python
python --version
jupyter kernelspec list
```

---

## 16. Problemas comuns

### Erro: `No such file or directory` ao ativar `.venv`

Causa provável:
- o `.venv` não existe
- o ambiente foi criado fora do WSL
- a pasta está corrompida

Solução:

```bash
rm -rf .venv
python3 -m venv .venv
source .venv/bin/activate
```

---

### Erro: `ensurepip is not available`

Causa provável:
- falta o pacote `python3-venv`

Solução:

```bash
sudo apt update
sudo apt install -y python3-venv
```

Depois recrie o `.venv`.

---

### VS Code mostra um Python do Windows como `~\.local\bin\python3.14.exe`

Causa provável:
- o projeto foi aberto fora do WSL

Solução:
- fechar essa janela do VS Code
- abrir o terminal do WSL
- entrar no projeto
- rodar:

```bash
code .
```

---

### O notebook diz que o kernel não está mais disponível

Causa provável:
- o VS Code está tentando usar um kernel antigo
- o `.venv` foi apagado e recriado

Solução:
1. recriar o kernel com `ipykernel`
2. recarregar a janela do VS Code
3. selecionar o kernel novo no notebook

Comandos:

```bash
source .venv/bin/activate
pip install jupyter ipykernel
python -m ipykernel install --user --name modelagem-computacional --display-name "Python (.venv modelagem-computacional)"
```

---

## 17. Resumo rápido

Ordem recomendada:

1. entrar na pasta do projeto
2. apagar `.venv` antigo
3. instalar `python3-venv`
4. criar `.venv`
5. ativar `.venv`
6. instalar dependências
7. registrar kernel do Jupyter
8. abrir o projeto com `code .` no WSL
9. selecionar o interpretador `.venv/bin/python`
10. selecionar o kernel do notebook

---

## 18. Comandos essenciais

### Criar ambiente virtual

```bash
python3 -m venv .venv
```

### Ativar ambiente virtual

```bash
source .venv/bin/activate
```

### Instalar dependências

```bash
pip install -r requirements.txt
```

### Registrar kernel Jupyter

```bash
python -m ipykernel install --user --name modelagem-computacional --display-name "Python (.venv modelagem-computacional)"
```

### Abrir VS Code no WSL

```bash
code .
```
