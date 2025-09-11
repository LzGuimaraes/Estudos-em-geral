# Tutorial: Programação Orientada a Objetos (POO) em C++ — Guia passo a passo

## Introdução

A **Programação Orientada a Objetos (POO)** é um paradigma que organiza o código em *objetos* — estruturas que combinam **dados** (atributos) e **comportamentos** (métodos). Em C++, POO é central para escrever código modular, reutilizável e fácil de manter.

**Quatro pilares da POO**

- **Encapsulamento** — esconder detalhes internos e oferecer uma interface clara.
- **Abstração** — representar entidades do domínio com classes que modelam o comportamento essencial.
- **Herança** — criar novas classes a partir de outras, reutilizando código.
- **Polimorfismo** — permitir que objetos de tipos diferentes sejam usados através de uma interface comum.

---

## Pré‑requisitos

Antes de começar: conhecimento básico de C++ (variáveis, funções, tipos, referências e fluxo de controle). Não é necessário saber ponteiros avançados aqui — usaremos alocação automática (stack) e referências sempre que possível.

---

# 1. Classes e objetos

**Classe** é o molde;&#x20;

**objeto** é a instância.

```cpp
#include <iostream>
#include <string>

class Pessoa {
public:
    std::string nome;
    int idade;

    void apresentar() const {
        std::cout << "Olá, eu sou " << nome << ", " << idade << " anos\n";
    }
};

int main() {
    Pessoa p;       // objeto criado na stack
    p.nome = "Luiz";
    p.idade = 25;
    p.apresentar();
    return 0;
}
```

**Pontos importantes**

- `public` / `private` / `protected` controlam visibilidade.

---

# 2. Construtores e destrutores

Construtores inicializam objetos; destrutores liberam recursos ao final da vida do objeto.

```cpp
#include <iostream>
#include <string>

class Pessoa {
public:
    std::string nome;
    int idade;

    Pessoa(const std::string& n, int i) : nome(n), idade(i) {
        std::cout << "Construtor: " << nome << "\n";
    }
    ~Pessoa() {
        std::cout << "Destrutor: " << nome << "\n";
    }

    void apresentar() const {
        std::cout << nome << " — " << idade << " anos\n";
    }
};

int main() {
    Pessoa a("Ana", 30);
    a.apresentar();
}
```

**Dicas**

- Use *member initializer lists* (`: nome(n), idade(i)`) para inicializar membros.
- O destrutor `~Classe()` é chamado automaticamente quando o objeto sai de escopo.

---

# 3. Encapsulamento: getters e setters

Coloque atributos sensíveis em `private` e controle acesso com métodos públicos.

```cpp
class ContaBancaria {
private:
    double saldo;
public:
    ContaBancaria(double s) : saldo(s) {}
    double getSaldo() const { return saldo; }
    void depositar(double v) { if (v > 0) saldo += v; }
    bool sacar(double v) { if (v > 0 && v <= saldo) { saldo -= v; return true; } return false; }
};
```

**Motivo**: validação centralizada e proteção do estado interno.

---

# 4. Métodos `const`, `static` e `inline`

- `void foo() const` — garante que o método não altera o estado do objeto.
- `static` — membro ligado à classe, não à instância.
- `inline` — dica ao compilador para expandir (normalmente em headers).

---

# 5. Herança

Criauma relação `é-um` entre classes.

```cpp
#include <iostream>

class Animal {
public:
    virtual void falar() const { std::cout << "Som de animal\n"; }
};

class Cachorro : public Animal {
public:
    void falar() const override { std::cout << "Au au!\n"; }
};

int main() {
    Cachorro c;
    c.falar(); // "Au au!"
}
```

**Notas**

- `public` herança é a mais comum: `class Derived : public Base`.
- Para polimorfismo dinâmico, use `virtual` no método base.

---

# 6. Polimorfismo (estático vs dinâmico)

- **Estático (compile-time)**: overloading, templates.
- **Dinâmico (run-time)**: `virtual` + chamadas via referência ou ponteiro. Exemplo usando referência (sem precisar de ponteiro):

```cpp
void fazFalar(const Animal& a) {
    a.falar(); // chama o método da classe derivada se for virtual
}

int main() {
    Cachorro dog;
    fazFalar(dog); // "Au au!" — polimorfismo via referência
}
```

---

# 7. Classes abstratas e interfaces

Classe abstrata: contém pelo menos um método puro virtual (`= 0`). Serve como interface.

```cpp
class Forma {
public:
    virtual double area() const = 0; // método puro virtual
    virtual ~Forma() = default;
};

class Retangulo : public Forma {
    double w, h;
public:
    Retangulo(double ww, double hh) : w(ww), h(hh) {}
    double area() const override { return w * h; }
};
```

---

# 8. Composição vs Herança

- **Herança**: use quando há uma relação forte `é-um`.
- **Composição**: use quando uma classe *tem* outra (recomendado quando unsure).

Exemplo de composição (Turma tem Alunos):

```cpp
#include <vector>
#include <string>

class Aluno {
public:
    std::string nome;
    Aluno(const std::string& n) : nome(n) {}
};

class Turma {
    std::vector<Aluno> alunos; // composição — turma contém alunos por valor
public:
    void adicionar(const Aluno& a) { alunos.push_back(a); }
};
```

---

# 9. Regra dos 3 / Regra dos 5 (visão geral)

Se sua classe gerencia recursos (memória dinâmica, handles), você precisa controlar cópia e destruição:

- **Regra dos 3**: se implementar/destrutor, copie-construtor ou operador=, provavelmente precisará dos três.
- **Regra dos 5**: com C++11, considere também move-construtor e move-assign.

> **Observação:** essa seção é *avançada* e exige conhecimento sobre alocação dinâmica (ponteiros). Se preferir, estudamos isso após dominar POO.

---

# 10. Boas práticas em POO C++

- Prefira inicializar membros com *member initializer lists*.
- Mantenha membros `private` por padrão.
- Favor composição sobre herança quando possível.
- Use `const` nos métodos que não modificam o objeto.
- Separe declarações (`.h`) e implementações (`.cpp`).
- Documente a responsabilidade de cada classe (SRP — Single Responsibility Principle).

---

# 11. Exercícios (etapa a etapa)

Os exercícios abaixo são progressivos: comece pelo 1 e avance.

## Exercício 1 — Retângulo (nível iniciante)

**Objetivo:** criar uma classe `Retangulo` com atributos privados `largura` e `altura`.

**Etapas**:

1. Declare a classe com atributos privados e um construtor público que inicializa largura e altura.
2. Implemente métodos `double area() const` e `double perimetro() const`.
3. No `main()`, crie um `Retangulo r(3.0, 4.0);` e imprima área e perímetro.
4. Adicione validação no construtor (largura/altura devem ser > 0). Se inválido, defina como 0.
5. Bônus: sobrecarregue `operator<<` para imprimir o retângulo.

**Critério de aceitação:** `Retangulo r(3,4)` deve imprimir `Área: 12` e `Perímetro: 14`.

---

## Exercício 2 — ContaBancaria (iniciante → intermediário)

**Objetivo:** encapsular um saldo e operações básicas.

**Etapas**:

1. Crie `class ContaBancaria` com `private: double saldo;` e um construtor.
2. Implemente `depositar(double)`, `bool sacar(double)` (retorna `true` se OK) e `double getSaldo() const`.
3. No `main()`, simule depósitos e saques e verifique o saldo final.
4. Bônus: implemente método `transferir(ContaBancaria& destino, double valor)`.

**Critério de aceitação:** transferir 50 de uma conta com saldo 100 para outra deve resultar em 50 e 150.

---

## Exercício 3 — Polimorfismo com Formas (intermediário)

**Objetivo:** criar hierarquia de `Forma` e usar polimorfismo via referências.

**Etapas**:

1. Implemente `class Forma` com `virtual double area() const = 0;`.
2. Crie `Retangulo` e `Circulo` que implementem `area()`.
3. Implemente função `void imprimeArea(const Forma& f)` que mostra a área.
4. No `main()`, crie `Retangulo r(3,4); Circulo c(2);` e chame `imprimeArea(r); imprimeArea(c);`.

**Critério de aceitação:** as áreas devem corresponder às fórmulas conhecidas.

---

## Exercício 4 — Funcionários (abstração & herança)

**Objetivo:** modelar funcionários com diferentes tipos de pagamento.

**Etapas**:

1. Crie `class Funcionario` abstrata com `virtual double calcularSalario() const = 0;`.
2. Implemente `FuncionarioCLT` (salário fixo + benefícios) e `FuncionarioPJ` (horas \* valorHora).
3. Faça um `vector` de objetos (por valor ou por referência) e imprima salário total da empresa.

**Observação:** para coleções heterogêneas, você pode armazenar ponteiros ou referências. Se quiser evitar ponteiros, calcule salários diretamente em variáveis locais.

---

## Exercício 5 — Composição (intermediário)

**Objetivo:** criar `Turma` que contém `Aluno`s por composição.

**Etapas**:

1. Implemente `Aluno` com `nome` e `nota`.
2. Implemente `Turma` com `std::vector<Aluno>` e métodos para adicionar aluno e calcular média.
3. No `main()`, preencha a turma e calcule a média.

---

## Exercício 6 — *Opcional avançado*: Regra dos 3 (requer ponteiros)

**Objetivo:** entender por que a cópia automática pode quebrar quando há recursos alocados.

**Etapas**:

1. Crie `class Buffer` que aloca um `int* dados` no construtor e libera no destrutor.
2. Compile e observe comportamento ao copiar objetos (cópia rasa — *shallow copy*).
3. Implemente copy-constructor e operador de atribuição para fazer *deep copy*.

> Faça este exercício somente depois de estudar ponteiros e `new`/`delete` — eu te ajudo quando chegarmos nessa etapa.

---

# 12. Próximos passos recomendados

1. Pratique os exercícios 1–3 até ficar confortável com classes, construtores e polimorfismo.
2. Depois reveja `new`/`delete` e a Regra dos 3/5 para entender gerenciamento de recursos.
3. Em seguida estudamos **ponteiros inteligentes** (unique\_ptr, shared\_ptr, weak\_ptr) para substituir `new`/`delete` com segurança.

---

Se quiser, eu posso:

- Gerar um **PDF** deste tutorial;
- Incluir **diagramas visuais** (stack x heap, vida do objeto, polimorfismo) para facilitar;
- Gerar as **soluções dos exercícios** (se preferir depois de tentar).

Diz o que prefere que eu faça a seguir e já partimos pra próxima etapa! 🎯

