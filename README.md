**Projeto: Análise de BFS vs DFS em Labirintos**
Este repositório contém um notebook que gera labirintos aleatórios e compara o desempenho (tempo e uso de memória) dos algoritmos de busca Breadth-First Search (BFS) e Depth-First Search (DFS).
**Arquivos principais**
- **`Homework - Answer/homework.ipynb`**: Notebook principal com a implementação, medições e gráficos.
- **`requirements.txt`**: Dependências (ex.: `matplotlib`).
**Visão geral — como funciona**
O notebook executa os seguintes passos principais:
1) Geração do labirinto
- Função: `gerar_labirinto(linhas, colunas)`.
- Abordagem: backtracking recursivo que começa em (1,1) e "cava" caminhos em passos de duas células para manter paredes estruturadas; a célula intermediária é aberta quando um movimento é feito.
- Dimensões ímpares: a função ajusta `linhas`/`colunas` para ímpar, garantindo bordas de parede.
- Convenção de símbolos: `#` (parede), `.` (caminho), `S` (início), `G` (objetivo).
2) Definição de vizinhos
- Função: `neighbors(pos, grid)` retorna vizinhos válidos (não paredes). O notebook inclui diagonais por padrão (até 8 vizinhos). Mudar para apenas movimentos ortogonais é simples: use apenas os deslocamentos `(-1,0),(1,0),(0,-1),(0,1)`.
3) Algoritmos de busca
- `bfs_grid(grid, start, goal)`: usa `deque` e `visited` para BFS; mantém `parent` para reconstrução do caminho.
- `dfs_grid(grid, start, goal)`: usa uma pilha (LIFO) para DFS; também registra `parent`.
- Reconstrução: `reconstruct_path_grid(parent, start, goal)` percorre `parent` de `goal` até `start` e inverte a lista.
4) Medições
- Tempo: `time.perf_counter()` em `get_bfs_time`/`get_dfs_time`.
- Memória: `tracemalloc` é usado para capturar o pico de memória; `gc.collect()` é chamado antes para reduzir ruído.
5) Experimentos e visualização
- O notebook cria labirintos de tamanhos crescentes, coleta tempos e picos de memória para cada execução e plota séries com `matplotlib`.
**Trechos úteis**
- Gerar labirinto e localizar `S` e `G`:
```
maze = gerar_labirinto(15, 21)
start = find_char(maze, 'S')
goal = find_char(maze, 'G')
```
- Executar buscas:
```
found_bfs, path_bfs = bfs_grid(maze, start, goal)
found_dfs, path_dfs = dfs_grid(maze, start, goal)
```
- Medir memória (exemplo interno):
```
gc.collect()
tm.start()
bfs_grid(maze, start, goal)
current, peak = tm.get_traced_memory()
tm.stop()
peak_kb = peak / 1024
```
**Complexidade (resumo)**
- BFS: tempo O(V+E), espaço O(V) (fila + visited + parent).
- DFS: tempo O(V+E), espaço O(V) no pior caso (pilha/recursão).
- Observação: ao incluir diagonais, o fator constante aumenta (até 8 vizinhos por nó).
**Como executar**
1. Criar e ativar ambiente virtual e instalar dependências:
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```
2. Abrir o notebook:
```bash
jupyter lab
```
3. Ou converter para script e executar:
```bash
jupyter nbconvert --to script "Homework - Answer/homework.ipynb"
python "Homework - Answer/homework.py"
```
**Boas práticas e sugestões**
- Use `random.seed(...)` para reproduzibilidade.
- Execute várias repetições por tamanho e reporte média + desvio.
- Para medições de memória isoladas, rodar cada execução em processo separado (ex.: `multiprocessing` ou subprocess) evita interferência do interpretador.
**Próximos passos**
- Posso extrair o código do notebook para `benchmarks.py` com argumentos CLI.
- Posso adicionar opção para desativar movimentos diagonais e gerar CSVs de resultados.
Diga qual melhoria prefere que eu implemente a seguir.
**Projeto: Análise de BFS vs DFS em Labirintos**

Este repositório contém um notebook que gera labirintos aleatórios e compara o desempenho (tempo e uso de memória) dos algoritmos de busca Breadth-First Search (BFS) e Depth-First Search (DFS).

**Arquivos principais**
- **Homework - Answer/homework.ipynb**: Notebook principal que implementa a geração de labirintos, as funções de busca (BFS e DFS), medidas de tempo/memória e os gráficos de comparação.
- **requirements.txt**: Dependências mínimas necessárias para gerar os gráficos.

**Descrição resumida**
- O notebook gera labirintos usando um algoritmo de backtracking recursivo (labirintos com paredes `#`, caminhos `.`, início `S` e objetivo `G`).
- Implementa `bfs_grid` e `dfs_grid` para buscar caminhos no grid, além de funções auxiliares para calcular vizinhos válidos e reconstruir caminhos.
- Mede tempo de execução com `time.perf_counter()` e uso de memória com `tracemalloc` (pico em KB).
- Gera gráficos de comparação de tempo e memória entre BFS e DFS para labirintos de tamanhos crescentes.

**Principais funções (no notebook)**
- **`gerar_labirinto(linhas, colunas)`**: Gera um labirinto dinâmico (matriz de caracteres).
- **`neighbors(pos, grid)`**: Retorna vizinhos válidos (inclui diagonais) que não são paredes.
- **`bfs_grid(grid, start, goal)`**: Busca em largura (BFS) retornando se encontrou e o caminho.
- **`dfs_grid(grid, start, goal)`**: Busca em profundidade (DFS) retornando se encontrou e o caminho.
- **`get_bfs_time`, `get_dfs_time`**: Medem tempos de execução.
- **`get_bfs_memory`, `get_dfs_memory`**: Medem pico de memória usando `tracemalloc`.

**Como executar**
1. Crie um ambiente Python (recomendado) e instale dependências:

```
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

2. Abra e execute o notebook com Jupyter Lab/Notebook:

```
jupyter lab
```

ou execute o notebook diretamente (ex.: conversão para script) se preferir.

**Saídas esperadas**
- Impressão do labirinto gerado e posições `S` e `G`.
- Gráficos comparativos: tempo de execução (BFS vs DFS) e uso de memória (BFS vs DFS).
- Mensagens de resumo indicando qual algoritmo teve desempenho inferior nas medições realizadas.

**Dependências**
- `matplotlib` (definida em `requirements.txt`).

**Observações**
- O notebook inclui medição de memória e coleta de lixo (`gc.collect()`) antes de rastrear memória para reduzir ruído nas medições.
- Os vizinhos consideram movimentos diagonais; se desejar comportamento somente ortogonal, ajuste a função `neighbors`.

Se quiser, eu posso:
- Extrair o código do notebook para um script `benchmarks.py` executável.
- Adicionar instruções para gerar resultados reproduzíveis (seed, parâmetros de repetição).
