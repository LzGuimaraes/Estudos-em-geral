# Explicação Linha-a-Linha e Significado de Cada Variável
---

## Convenções usadas aqui

* **I / v / vértice** — um vértice (mesma ideia). Em código usamos `v` em loops que representam um vértice.
* **n / nó** — quantidade de vértices (`n`) ou índice de nó quando for usado como `n` em loops (atenção: no seu código `n` é o atributo que guarda número total de vértices).
* **adj** — lista de adjacência: `std::vector<std::vector<int>> adj;` onde `adj[i]` é o vetor de vizinhos do vértice `i`.
* **u** — frequentemente representa o vértice atual sendo processado (por exemplo, ao retirar da fila). Em algoritmos de caminhos curtos, `u` é o nó cujos vizinhos serão relaxados.
* **v** — usado para representar um vizinho de `u` ou um vértice genérico; depende do contexto (em `for (int v : adj[u])` v é um vizinho de u).
* **i, j** — índices de laços (geralmente usados na geração e enumeração de pares de vértices, ou para percorrer `adj`).
* **visitado / conhecido** — `std::vector<bool>` que marca se um vértice já foi encontrado/visitado em BFS/descoberta.
* **fila / q** — `std::queue<int>` usado em BFS: enfileira vértices a serem processados.
* **dist** — `std::vector<int>` com a distância mínima conhecida do `origem` até cada vértice.
* **prev** — `std::vector<int>` com o predecessor no caminho mínimo (para reconstruir caminhos): `prev[w] = x` significa que o caminho mais curto até `w` passa por `x` imediatamente antes.
* **alt** — alternativa de distância calculada ao relaxar uma aresta (`alt = dist[u] + peso(u->v)`). Aqui `peso = 1` então `alt = dist[u] + 1`.
* **pq** — `std::priority_queue` com pares `(distância, vértice)` usado no Dijkstra (min-heap com `std::greater`).

---

# 1. Construtor

```cpp
Grafo::Grafo(int n) : n(n), adj(n) {}
```

**Linha por linha / cada símbolo:**

* `Grafo::Grafo(int n)` — função construtora, recebe `n` (número de vértices desejado).
* `: n(n)` — inicializa o atributo `this->n` com o valor do parâmetro `n`.
* `adj(n)` — cria um `std::vector<std::vector<int>> adj` com `n` elementos; cada elemento é um `std::vector<int>` vazio. Assim `adj[0]` ... `adj[n-1]` existem desde o início.

**Para onde vai cada coisa:**

* Parâmetro `n` vai para o atributo `n` do objeto e serve para dimensionar `adj`.

---

# 2. gerarTopologiaAleatoria(double densidade)

Trecho inicial:

```cpp
std::random_device rd;
std::mt19937 gen(rd());
std::uniform_real_distribution<> dis(0.0, 1.0);
```

**O que cada variável representa:**

* `rd` — fonte de entropia do sistema (pode variar por plataforma).
* `gen` — gerador pseudo-aleatório (Mersenne Twister) inicializado com `rd()`.
* `dis` — distribuição uniforme real em [0.0, 1.0).

**Para onde vão os valores:**

* Sempre que `dis(gen)` é chamado, gera um número real `r` entre 0 e 1. Esse `r` é comparado com `densidade` para decidir se cria uma aresta.

Loop principal:

```cpp
for (int i = 0; i < n; ++i) {
    for (int j = i+1; j < n; ++j) {
        if (dis(gen) < densidade) {
            adj[i].push_back(j);
            adj[j].push_back(i);
        }
    }
}
```

**Significado das variáveis neste contexto:**

* `i` e `j` são índices de vértices que representam um par (i,j).
* Condição `j = i+1` evita pares duplicados e loops (i,i).

**Fluxo de dados / execução:**

1. Gera `r = dis(gen)`.
2. Se `r < densidade`, cria aresta entre `i` e `j` adicionando `j` em `adj[i]` e `i` em `adj[j]`.
3. Ao final do laço, `adj` conterá todos os vizinhos adicionados.

**Nota importante:**

* A operação `adj[i].push_back(j)` **coloca o índice `j` no vetor de vizinhos de `i`**. Quando você mais tarde fizer `for (int v : adj[i])`, cada `v` será um desses índices adicionados.

---

# 3. garantirConexao()

Trecho completo (resumido):

```cpp
std::vector<bool> visitado(n, false);

auto bfs = [&](int start){
    std::queue<int> q;
    q.push(start);
    visitado[start] = true;
    while(!q.empty()) {
        int u = q.front(); q.pop();
        for (int v : adj[u]) {
            if (!visitado[v]) {
                visitado[v] = true;
                q.push(v);
            }
        }
    }
};

bfs(0);
for (int i = 0; i < n; ++i) {
    if (!visitado[i]) {
        for (int j = 0; j < n; ++j) {
            if (visitado[j]) {
                adj[i].push_back(j);
                adj[j].push_back(i);
                break;
            }
        }
        bfs(i);
    }
}
```

### Linha-a-linha / significado das variáveis

* `visitado(n,false)` — cria um vetor booleano que acompanha se um nó foi alcançado pela BFS. `visitado[x] == true` significa: o vértice `x` é parte do componente já conectado ao vértice inicial (ou já unificado ao conjunto principal).

* `auto bfs = [&](int start){ ... }` — define um lambda que executa BFS iniciando do nó `start`. Ele usa fechamento (`[&]`) para acessar `visitado` e `adj` externos.

Dentro do BFS:

* `std::queue<int> q;` — cria fila `q` de nós a processar.
* `q.push(start); visitado[start] = true;` — marca `start` como visitado e o coloca na fila.
* `while (!q.empty()) { int u = q.front(); q.pop(); ... }` — loop clássico de BFS: retira o próximo nó `u` a processar.
* `for (int v : adj[u])` — percorre todos os vizinhos `v` de `u` (cada `v` foi colocado em `adj[u]` durante a geração de arestas).
* `if (!visitado[v]) { visitado[v] = true; q.push(v); }` — se vizinho ainda não foi visto, marca e enfileira.

Após `bfs(0)`:

* Todos os vértices do **mesmo componente conectado ao vértice 0** ficam com `visitado[...] = true`.

Laço seguinte:

* `for (int i = 0; i < n; ++i) { if (!visitado[i]) { ... } }` — procura vértices ainda não visitados (componentes desconexos).
* Dentro: `for (int j = 0; j < n; ++j) { if (visitado[j]) { adj[i].push_back(j); adj[j].push_back(i); break; } }` — encontra o primeiro `j` que está no componente principal (`visitado[j] == true`) e conecta `i` a `j`. Isso junta o componente de `i` ao principal.
* Depois `bfs(i)` é chamado para marcar todos os vértices que agora podem ser alcançados através de `i` (o antigo componente de `i`).

**Por que chamar `bfs(0)` no início?**

* Para definir um "componente principal" (o que contém o nó 0). A estratégia é: unir todos os componentes ao componente do nó 0. Pior caso: se o grafo tiver componentes A, B, C, chamando `bfs(0)` marcamos A; quando encontramos um nó em B, conectamos B a A, chamamos `bfs(i)` que marca B (e C pode também ser marcado depois) — no fim tudo vai estar unido.

**Fluxo de dados:**

* `visitado` é atualizado progressivamente pela BFS.
* `adj` recebe novas arestas quando encontramos nós não-visitados.

---

# 4. imprimirMatriz() — explicação completa do `std::find` e fluxo

Trecho:

```cpp
for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
        int peso = std::find(adj[i].begin(), adj[i].end(), j) != adj[i].end() ? 1 : 0;
        std::cout << " " << peso << " ";
    }
    std::cout << "
";
}
```

### Passo a passo

* `i` representa a linha da matriz (origem hipotética do arco).
* `j` representa a coluna (destino hipotético do arco).
* `adj[i]` é o vetor contendo os vizinhos reais do `i` (por exemplo, `adj[2] = {5, 7}` significa que existe aresta 2--5 e 2--7).

A expressão `std::find(adj[i].begin(), adj[i].end(), j)` percorre internamente o vetor `adj[i]` comparando cada elemento com `j`:

* Se encontra `j` naquela lista, retorna um iterador apontando para o elemento (não é `end()`).
* Se não encontra, retorna `adj[i].end()`.

Comparação `!= adj[i].end()` transforma isso em booleano: verdadeiro se a aresta `i--j` existe, falso caso contrário.

O operador ternário `? :` produz `1` quando existe aresta, `0` quando não existe. Isso é impresso como entrada na matriz.

**Exemplo concreto:**

* `adj[1] = {0, 3}` e `n = 4`.

  * Para `i = 1`, `j = 0` → `find` encontra 0 → imprime `1`.
  * Para `i = 1`, `j = 1` → `find` não encontra → imprime `0`.
  * Para `i = 1`, `j = 2` → `find` não encontra → imprime `0`.
  * Para `i = 1`, `j = 3` → `find` encontra → imprime `1`.

**Onde cada valor “vai”:**

* `peso` é uma variável temporária para impressão; não altera `adj`.

**Complexidade e por que é caro:**

* Para cada par `(i,j)` você faz `find` que é O(deg(i)). Logo, imprimir a matriz custa O(Σ_i (n * deg(i))) = O(n * E) no total — ineficiente para grafos grandes.

**Melhoria (onde "iria" o cálculo):**

* Construir uma matriz booleana primeiro: para cada `i` percorrer `adj[i]` e marcar `mat[i][v] = 1`. Isso percorre todas as arestas apenas uma vez (O(E)). Depois imprimir, custo O(n²) só para a impressão sem buscas repetidas.

---

# 5. salvarDot(filename)

Trecho:

```cpp
std::ofstream out(filename);
if (!out.is_open()) { std::cerr << "Erro ao criar arquivo DOT!
"; return; }
out << "graph G {
";
for (int i = 0; i < n; ++i) {
    for (int j : adj[i]) {
        if (i < j)
            out << "  " << i << " -- " << j << ";
";
    }
}
out << "}
";
out.close();
```

**Entendimento linha-a-linha:**

* `std::ofstream out(filename);` — abre/ cria arquivo para escrita. `out` é o objeto que receberá texto.
* `if (!out.is_open())` — verifica se o arquivo foi aberto com sucesso. Se não, imprime erro e retorna.
* `out << "graph G {
  ";` — escreve o cabeçalho do DOT para grafos não-direcionados.
* `for (int i = 0; i < n; ++i) { for (int j : adj[i]) { if (i < j) ... }}`

  * Para cada `i` pega cada vizinho `j` em `adj[i]`.
  * `if (i < j)` garante que cada aresta seja escrita **uma única vez** (evita imprimir `0 -- 1;` e `1 -- 0;`).
* `out << "}
  ";` — fecha o bloco DOT.
* `out.close();` — fecha o arquivo (sinaliza ao sistema que todos os dados foram escritos).

**Para onde cada valor "vai":**

* `i` e `j` são convertidos em texto e enviados para o stream `out`, que escreve no disco.

---

# 6. dijkstra(origem, dist, prev)

Trecho inicial:

```cpp
dist.assign(n, 999);
prev.assign(n, -1);
dist[origem] = 0;
using pii = std::pair<int,int>;
std::priority_queue<pii, std::vector<pii>, std::greater<pii>> pq;
pq.push({0, origem});
```

### O que `dist` e `prev` armazenam:

* `dist[x]` → distância mínima conhecida de `origem` até `x` (inicialmente 999 = infinito).
* `prev[x]` → predecessor de `x` no caminho mínimo (inicialmente -1 = sem predecessor).
* `dist[origem] = 0` → a distância da origem para si mesma é zero.

### `pii` e `pq` explained:

* `pii` = par (distância, vértice).
* `std::priority_queue<pii, vector<pii>, greater<pii>>` — cria uma fila de prioridade onde o menor par (pela comparação lexicográfica) sai primeiro.

  * Por padrão `priority_queue` é max-heap (maior elemento sai primeiro).
  * `greater<pii>` inverte o comportamento e torna um min-heap, útil para Dijkstra porque sempre queremos processar o vértice com a menor distância conhecida.

**Observação de comparação de `pair<int,int>`:**

* A comparação é lexicográfica: `(d1, v1) < (d2, v2)` se `d1 < d2` ou `d1 == d2 && v1 < v2`.

Loop principal (resumido):

```cpp
while(!pq.empty()) {
    auto [d,u] = pq.top(); pq.pop();
    if (d > dist[u]) continue;
    for (int v : adj[u]) {
        int alt = dist[u] + 1;
        if (alt < dist[v]) {
            dist[v] = alt;
            prev[v] = u;
            pq.push({alt,v});
        }
    }
}
```

### Linha-a-linha e significado das variáveis no loop:

* `auto [d,u] = pq.top(); pq.pop();` — *desestrutura* o par retornado da fila: `d` é a distância armazenada no par (estimativa da menor distância para `u`), `u` é o nó a processar.

* `if (d > dist[u]) continue;` — essa condição descarta entradas antigas na fila: quando se atualiza `dist[u]` várias vezes, várias entradas para `u` podem existir em `pq`. A primeira que for retirada com `d == dist[u]` é a válida; menores atualizações posteriores produzirão `d < dist[u]` e entradas antigas terão `d > dist[u]` e serão ignoradas.

* `for (int v : adj[u])` — para cada vizinho `v` de `u` (cada `v` foi previamente adicionado em `adj[u]`).

* `int alt = dist[u] + 1;` — calcula uma possível nova distância até `v` passando por `u`. `alt` é a alternativa de distância.

* `if (alt < dist[v]) { ... }` — se essa alternativa for melhor que a conhecida, atualiza `dist[v]`, `prev[v]`, e empurra `{alt, v}` para a fila.

### Fluxo de dados:

* `dist` é lido (dist[u]) e escrito (dist[v]).
* `prev[v] = u` grava o antecessor para reconstrução do caminho.
* `pq` recebe entradas que guiam a ordem futura de processamento.

### Observação sobre o custo do algoritmo aqui:

* Como todas arestas tem peso 1, **um BFS** poderia substituir Dijkstra com ganho de eficiência (BFS também constrói `prev` e `dist` corretos em grafos não ponderados).

---

# 7. simularMensagens(origem)

Trecho:

```cpp
std::vector<int> dist, prev;
dijkstra(origem, dist, prev);
for (int destino = 0; destino < n; ++destino) {
    if (destino == origem) continue;
    if (prev[destino] == -1) {
        std::cout << "Rota " << origem << " -> " << destino << ": inacessível
";
        continue;
    }
    std::vector<int> caminho;
    for (int v = destino; v != -1; v = prev[v]) caminho.push_back(v);
    std::reverse(caminho.begin(), caminho.end());
    // imprime caminho
}
```

### O que faz cada etapa:

* `dijkstra(origem, dist, prev);` — enche `dist` e `prev` com as informações da árvore de caminhos mínimos a partir da origem.

* `for (int destino = 0; destino < n; ++destino)` — itera sobre todos os nós que podem ser destinatários.

* `if (destino == origem) continue;` — não faz rota de um nó para ele mesmo.

* `if (prev[destino] == -1)` — se o predecessor é -1 (e destino != origem), significa que **não existe caminho** do `origem` ao `destino` (atingível). Então imprime "inacessível".

* Reconstrução do caminho:

  * `std::vector<int> caminho;`
  * `for (int v = destino; v != -1; v = prev[v]) caminho.push_back(v);`

    * Começa em `v = destino`.
    * Adiciona `v` em `caminho`.
    * Atualiza `v = prev[v]` (ou seja, percorre o caminho de volta para a origem seguindo predecessores).
    * Repete até `v == -1` (quando `v` chega a `origem`, `prev[origem]` é `-1` e o laço termina depois de empilhar a origem).
  * `std::reverse(caminho.begin(), caminho.end());` — inverte o vetor para que fique `origem -> ... -> destino`.

### Por que `prev` funciona para reconstrução?

* Ao final do Dijkstra (ou BFS), para cada vértice `x` alcançado, `prev[x]` aponta para o nó imediatamente anterior no caminho mínimo de `origem` até `x`. Assim, seguir `prev` é atravessar a árvore reversa até a origem.

### Exemplo numérico curto:

* Suponha `origem = 0` e temos caminhos: `0 -> 2 -> 5`.

  * `prev[5] = 2`, `prev[2] = 0`, `prev[0] = -1`.
  * Para `destino = 5`, o laço empilha: 5, 2, 0. Depois `reverse` → 0, 2, 5. Imprime `0->2->5`.

---

# 8. Variáveis e onde "vão" durante a execução — resumo final e mapeamento

* `n` (atributo do objeto) — número total de vértices; usado para dimensionar `adj`, laços, e bounds.
* `adj` — estrutura que *contém* as arestas. Cada `adj[i]` é um vetor listando os índices dos vizinhos.
* `i, j` — índices de laços, utilizados ao gerar arestas e ao escrever DOT / imprimir matriz.
* `u` — vértice atual sendo processado em BFS/Dijkstra. Quando retirado da fila, seus vizinhos serão checados.
* `v` — vizinho de `u` dentro do loop `for (int v : adj[u])`. Cada `v` é um inteiro que representa o índice de outro vértice.
* `visitado` — vetor booleano; indica se um vértice foi alcançado em BFS/descoberta. Ele evolui de `false` → `true` quando o vértice é enfileirado (descoberto).
* `fila q` — mantém a ordem FIFO de processamento em BFS; cada `push` adiciona para processar mais tarde, `pop` remove para processar agora.
* `dist` — atualizada por Dijkstra (ou BFS), contém as distâncias mínimas finais ao término do algoritmo.
* `prev` — construída junto com `dist`; permite reconstruir caminhos. `prev[origem] == -1` sempre e `prev[x] == -1` também indica inatingibilidade.
* `alt` — valor temporário enquanto relaxa arestas; se menor que `dist[v]` atualiza e é empurrado para a fila.
* `pq` — organiza ordem de processamento por distância crescente; recebe pares `(dist, vértice)`. Entradas antigas não são removidas, mas descartadas ao serem extraídas se estiverem obsoletas (condição `if (d > dist[u]) continue;`).

---
