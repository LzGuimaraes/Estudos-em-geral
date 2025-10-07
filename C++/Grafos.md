# 🧠 O que é a Teoria dos Grafos?

A **Teoria dos Grafos** é um ramo da matemática e da ciência da computação que estuda **como objetos se conectam entre si**.

Um grafo é formado por:
- **Vértices (ou nós):** representam os objetos.
- **Arestas:** representam as ligações entre esses objetos.

Por exemplo, imagine um mapa de cidades:
- Cada **cidade** é um **vértice**.
- Cada **estrada** entre duas cidades é uma **aresta**.

Os grafos podem ser:
- **Não direcionados**: a ligação vale para os dois lados (ex: estradas de mão dupla).
- **Direcionados**: a ligação tem direção (ex: ruas de mão única, links na web).
- **Ponderados**: as arestas têm um peso (ex: distância, tempo ou custo).
  
# 🧩 Representação de Grafos em C++

Existem duas formas comuns de representar um grafo em código:
1. **Matriz de adjacência**
2. **Lista de adjacência**

A mais usada por ser mais eficiente é a lista de adjacência, utilizando `vector`.

#### Exemplo básico (grafo não direcionado)

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    int vertices = 5;
    vector<vector<int>> grafo(vertices);

    // Adicionando conexões (arestas)
    grafo[0].push_back(1);
    grafo[1].push_back(0);

    grafo[0].push_back(4);
    grafo[4].push_back(0);

    grafo[1].push_back(2);
    grafo[2].push_back(1);

    grafo[1].push_back(3);
    grafo[3].push_back(1);

    // Exibindo as conexões
    for (int i = 0; i < vertices; i++) {
        cout << "Vértice " << i << " está conectado a: ";
        for (int vizinho : grafo[i]) {
            cout << vizinho << " ";
        }
        cout << endl;
    }

    return 0;
}
```

### 🔹 Explicando:
Cada posição do vetor principal representa um vértice.

Dentro de cada posição, há um vetor com os vértices conectados a ele.

## 🔍 Percorrendo o grafo – BFS e DFS
Para explorar ou buscar elementos em um grafo, usamos algoritmos clássicos como **BFS (Busca em Largura) e DFS (Busca em Profundidade)**.

#### Busca em Largura (BFS)

A BFS visita os vértices em camadas, útil para encontrar o menor caminho em grafos não ponderados.

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

void bfs(int start, const vector<vector<int>>& grafo) {
    vector<bool> visitado(grafo.size(), false);
    queue<int> fila;

    visitado[start] = true;
    fila.push(start);

    while (!fila.empty()) {
        int atual = fila.front();
        fila.pop();
        cout << "Visitando: " << atual << endl;

        for (int vizinho : grafo[atual]) {
            if (!visitado[vizinho]) {
                visitado[vizinho] = true;
                fila.push(vizinho);
            }
        }
    }
}
```
#### Como usar:
```cpp
int main() {
    vector<vector<int>> grafo = {
        {1, 4}, {0, 2, 3}, {1}, {1}, {0}
    };
    bfs(0, grafo);
    return 0;
}
```

## Busca em Profundidade (DFS)
A DFS vai o mais fundo possível antes de voltar — é útil para verificar **componentes conectados** ou detectar ciclos.

```cpp
#include <iostream>
#include <vector>
using namespace std;

void dfs(int atual, const vector<vector<int>>& grafo, vector<bool>& visitado) {
    visitado[atual] = true;
    cout << "Visitando: " << atual << endl;

    for (int vizinho : grafo[atual]) {
        if (!visitado[vizinho]) {
            dfs(vizinho, grafo, visitado);
        }
    }
}

int main() {
    vector<vector<int>> grafo = {
        {1, 4}, {0, 2, 3}, {1}, {1}, {0}
    };

    vector<bool> visitado(grafo.size(), false);
    dfs(0, grafo, visitado);

    return 0;
}
```

## ⚙️ Aplicações reais

A Teoria dos Grafos é aplicada em diversas áreas:

| Área                  | Aplicação                                          |
| --------------------- | -------------------------------------------------- |
| Redes de computadores | Modelar conexões entre dispositivos                |
| GPS e mapas           | Calcular o menor caminho entre cidades             |
| Redes sociais         | Identificar conexões entre usuários                |
| Ciência de dados      | Analisar relações entre entidades                  |
| Jogos e IA            | Encontrar caminhos em mapas e decisões automáticas |


## 🧭 Conclusão

Grafos são estruturas fundamentais em ciência da computação.
Com eles, é possível resolver problemas complexos como rotas, dependências, conexões e recomendações.

Em C++, a **lista de adjacência** é a forma mais prática de trabalhar com grafos, e algoritmos como **BFS** e **DFS** são as ferramentas básicas para explorá-los

## Algoritmo de Dijkstra

O Algoritmo de Dijkstra serve para encontrar o menor caminho entre um vértice inicial e todos os outros vértices de um grafo ponderado (onde cada aresta tem um peso, como distância, tempo ou custo).

👉 É muito usado em sistemas como GPS, roteamento de redes e análise de caminhos em jogos.

## ⚙️ Como funciona (em resumo)

1. Começa de um vértice inicial.
2. Define a distância até ele mesmo como 0, e até todos os outros como “infinito”.
3. Escolhe sempre o vértice com menor distância atual que ainda não foi visitado.
4. Atualiza as distâncias dos vizinhos, se encontrar um caminho mais curto.
5. Repete até visitar todos os vértices.

## 💻 Exemplo completo em C++

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <limits>
using namespace std;

const int INF = numeric_limits<int>::max();

// Função de Dijkstra
void dijkstra(int start, const vector<vector<pair<int, int>>>& grafo) {
    int n = grafo.size();
    vector<int> distancia(n, INF); // Distâncias mínimas
    distancia[start] = 0;

    // priority_queue: (distância, vértice)
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
    pq.push({0, start});

    while (!pq.empty()) {
        int distAtual = pq.top().first;
        int vertice = pq.top().second;
        pq.pop();

        // Ignora se já achamos um caminho melhor antes
        if (distAtual > distancia[vertice]) continue;

        // Percorre os vizinhos do vértice atual
        for (auto [vizinho, peso] : grafo[vertice]) {
            int novaDist = distAtual + peso;
            if (novaDist < distancia[vizinho]) {
                distancia[vizinho] = novaDist;
                pq.push({novaDist, vizinho});
            }
        }
    }

    // Exibindo o resultado
    cout << "Distâncias mínimas a partir do vértice " << start << ":\n";
    for (int i = 0; i < n; i++) {
        if (distancia[i] == INF)
            cout << "Vértice " << i << ": inatingível\n";
        else
            cout << "Vértice " << i << ": " << distancia[i] << endl;
    }
}

int main() {
    int vertices = 5;
    vector<vector<pair<int, int>>> grafo(vertices);

    // Adicionando arestas (vértice destino, peso)
    grafo[0].push_back({1, 10});
    grafo[0].push_back({4, 5});
    grafo[1].push_back({2, 1});
    grafo[1].push_back({4, 2});
    grafo[2].push_back({3, 4});
    grafo[3].push_back({0, 7});
    grafo[4].push_back({1, 3});
    grafo[4].push_back({2, 9});
    grafo[4].push_back({3, 2});

    dijkstra(0, grafo);

    return 0;
}
```
--- 
## 🧩 Explicando o código

- `vector<vector<pair<int, int>>> grafo` → cada vértice contém uma lista de pares {vizinho, peso}.
- `distancia[]` → armazena a menor distância conhecida até cada vértice.
- `priority_queue` → escolhe o vértice com a menor distância atual para processar primeiro.
- O algoritmo percorre todos os vizinhos e atualiza suas distâncias se encontrar um caminho mais curto.

## 🧮 Exemplo visual

Com o grafo acima:

```lua
0 --10--> 1
0 --5--> 4
1 --1--> 2
1 --2--> 4
2 --4--> 3
4 --2--> 3
```
O resultado da execução será:
```yml
Distâncias mínimas a partir do vértice 0:
Vértice 0: 0
Vértice 1: 8
Vértice 2: 9
Vértice 3: 7
Vértice 4: 5
```

## 🔚 Conclusão

O **Algoritmo de Dijkstra** é a base de muitos sistemas modernos.

Ele mostra como a **Teoria dos Grafos** se transforma em **ferramentas práticas** dentro da programação.

Com isso, você já viu:

- Representação de grafos em C++;
- Percursos BFS e DFS;
- Cálculo de menor caminho com Dijkstra.

Esses três pilares são o essencial para dominar a teoria e aplicá-la em projetos reais de software.
