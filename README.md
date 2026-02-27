ou execute o notebook diretamente (ex.: conversão para script) se preferir.
**Projeto: Análise de BFS vs DFS em Labirintos**

Este repositório contém notebooks que geram labirintos aleatórios e comparam o desempenho (tempo e uso de memória) dos algoritmos de busca Breadth-First Search (BFS) e Depth-First Search (DFS).

**Alterações recentes**
- O notebook de visualização foi atualizado para tornar os plots mais robustos:
	- aceita tanto `df_resultados` (pandas DataFrame) quanto `resultados` (lista de dicionários);
	- usa a coluna `Tamanho` como eixo-x quando disponível;
	- normaliza séries de forma segura (proteção contra divisão por zero e valores constantes);
	- converte valores para `float` antes de normalizar para evitar erros com strings formatadas.

**Arquivos principais**
- `Homework - Answer/Atividade_IA_Labirinto.ipynb`: Notebook principal com geração de labirintos, medições e gráficos (contém as melhorias de plot descritas acima).
- `requirements.txt`: Dependências mínimas para executar os notebooks.

**Visão geral — fluxo do notebook**
1) Geração do labirinto
- Função: `gerar_labirinto(linhas, colunas)` (backtracking recursivo). Ajusta dimensões para ímpar e usa `#`/`.`/`S`/`G`.
2) Vizinhos
- Função: `neighbors(pos, grid)` retorna vizinhos válidos. O notebook atual inclui diagonais por padrão; é simples desativar diagonais removendo deslocamentos diagonais.
3) Algoritmos de busca
- `bfs_grid(grid, start, goal)` e `dfs_grid(grid, start, goal)` com `parent` para reconstrução de caminho.
4) Medições
- Tempo com `time.perf_counter()` (convertido para ms).
- Memória com `tracemalloc` (pico em KB). `gc.collect()` é chamado antes das medições de memória.
5) Experimentos e visualização
- Gera labirintos de tamanhos crescentes, coleta tempos e picos de memória, monta `df_resultados` e plota comparações normalizadas com curvas teóricas (O(n), O(n log n), O(n²)).

**Como executar**
1. Criar e ativar ambiente virtual e instalar dependências:
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pip install pandas numpy
```
Observação: `pandas` e `numpy` são usados no notebook e podem não estar listados no `requirements.txt`; instale-os se necessário.

2. Abrir o notebook:
```bash
jupyter lab
```

3. Alternativa — converter para script e executar:
```bash
jupyter nbconvert --to script "Homework - Answer/Atividade_IA_Labirinto.ipynb"
python "Homework - Answer/Atividade_IA_Labirinto.py"
```

**Notas sobre plots e dados**
- Se os valores em `df_resultados` estiverem formatados como strings (ex.: "12.34 ms"), converta para `float` antes de construir os gráficos.
- A normalização usada nos gráficos divide pela primeira entrada de cada série; o código atual protege contra zeros e séries constantes.

**Como os gráficos de comparação (Big O) são gerados**

- Eixo X (tamanhos): o eixo X representa o tamanho característico do problema. No notebook esse valor vem da coluna `Tamanho` (ex.: 25, 35, 45...). Se essa coluna não existir, os valores são gerados como uma sequência esperada (`[25 + i*15 for i in range(...) ]`).

- Curvas teóricas: o notebook constrói três curvas teóricas (O(n), O(n log n), O(n²)) usando os mesmos valores de `Tamanho`. Por exemplo:
	- O(n): vetor `n`
	- O(n log n): vetor `n * log2(n)`
	- O(n²): vetor `n**2`

- Normalização: as curvas teóricas e os dados reais (tempo/memória) são normalizados para permitir comparação visual independentemente das unidades.
	- Método aplicado: dividir cada série pelo seu primeiro valor (ou por um valor alternativo seguro quando o primeiro é zero), transformando a primeira entrada em 1.0 e escalando as demais relativamente.
	- Por que: dessa forma vemos apenas o crescimento relativo da série com o aumento de `Tamanho`, o que facilita comparar o formato (crescimento linear, n log n, quadrático) sem que a escala absoluta (ms ou KB) atrapalhe.

- Pré-processamento dos dados reais:
	- Converter colunas como `BFS_Tempo_ms`, `DFS_Tempo_ms`, `BFS_Memoria_KB`, `DFS_Memoria_KB` para `float` antes da normalização.
	- Remover sufixos formatados (ex.: " ms", " KB") caso existam — o notebook já inclui conversões quando necessário.

- Proteção contra casos degenerados:
	- Séries constantes (todos valores iguais) ou primeiro valor igual a zero: o código de normalização usa um denominador alternativo (máximo da série) ou 1.0 como fallback, evitando divisão por zero e resultados inválidos.

- Plotagem final:
	- Os dados normalizados (`bfs_norm`, `dfs_norm`) são plotados com marcadores (`o`, `s`) e cores distintas.
	- As curvas teóricas (`o_n`, `o_n_log_n`, `o_n2`) são plotadas como linhas tracejadas para facilitar a comparação visual do formato (inclinação/curvatura).
	- Legendas, rótulos e grade são adicionados para leitura clara.

- Interpretação visual:
	- Se a série real segue de perto a curva O(n), o crescimento relativo será semelhante à linha O(n).
	- Se a série cresce mais rápido e curvar para cima, pode se aproximar de O(n²).
	- Importante: esse é um diagnóstico visual para entender tendência (ordem de crescimento) — não substitui um ajuste numérico/regressão para estimar parâmetros assintóticos.

- Sugestão para análise quantitativa opcional:
	- Aplicar regressão em escala log-log (fit de forma log(y) ~ a + b * log(n)) para estimar o expoente b; b≈1 indica linear, b≈2 quadrático, b≈1·log→ ajuste para n log n requer transformação especial.


**Boas práticas**
- Use `random.seed(...)` para reproduzibilidade.
- Rode múltiplas repetições por tamanho e reporte média + desvio padrão.
- Para medições de memória isoladas, executar cada teste em processo separado reduz interferência do interpretador.

**Próximos passos sugeridos**
- Extrair o código para um script `benchmarks.py` com argumentos CLI (tamanhos, repetições, desativar diagonais).
- Gerar e salvar CSV com `df_resultados` para análises posteriores.

Se quiser, aplico uma dessas melhorias — qual prefere que eu adicione primeiro?
