# Alocação Dinâmica de Memória

Como exemplificado no capítulo sobre layout de memória, um programa pode alocar memória dinâmicamente, e geralmente chamamos toda região de memória alocada dessa forma de "heap".

A linguagem C fornece, através da sua biblioteca padrão e expõe pelo header stdlib.h funções para realizar o gerencimento manual dessa memória, fornecendo funções que alocam, realocam e liberam memória dinamicamente.

Quando alocamos memória, estamos efetivamente reservando uma quantidade específica de bytes para algum uso específico.

## Funções para alocação

Para realizar a alocação de memória, existem 3 funções da biblioteca padrão: `malloc`, `calloc` e `aligned_alloc`.

### malloc
malloc é a principal função para alocação de memória, e tem a seguinte sintaxe :
```cpp
void *malloc(size_t tamanho);
```

Onde `tamanho` é a quantidade de bytes que devem ser alocados e o retorno é um `void*` indicando o endereço da memória alocado, ou **NULL** caso a alocação falhe.

A memória alocada com `malloc` não tem nenhuma garantia quanto ao seu conteúdo inicial, é comum que seu conteúdo seja similar ao de uma variável não inicializada da stack.

O alinhamento da memória alocada é o maior alinhamento que qualquer tipo possa ter, equivalente ao alinhamento do tipo `max_align_t` do C11.

Essa memória posteriormente pode ser liberada utilizando free ou alguma das outras funções de liberar memória da `stdlib.h` que serão mencionadas mais a frente.

Utilizar **0** como tamanho pode, dependendo da implementação, retornar **NULL** ou um ponteiro válido que não pode ser acessado mas ainda deve ser **liberado**.

Acessar memória além do que foi alocado gera um comportamento indefinido, que pode sobrescrever/ler outras variáveis ou causar uma finalização forçada do programa pelo sistema operacional.

Exemplo de uso da função malloc:
```cpp
int main()
{
    //Aloca memória para 10 inteiros
    int *quadradosPerfeitos = malloc(sizeof(int) * 10);

    for(int i = 0; i < 10; i++)
        quadradosPerfeitos[i] = (i+1) * (i+1);
    
    //Libera a memória utilizada
    free(quadradosPerfeitos);
}

```

É comum que ao alocar memória, o sistema operacional simplesmente "marque" que uma região do endereço virtual de um processo é válido, sem efetivamente reservar memória física para ele.

Quando o usuário acessa (tenta ler/escrever no endereço alocado) ocorre um erro interno no sistema denominado de "falha de página", isso faz com que uma função do kernel seja chamada para lidar com o erro,
essa função efetivamente prepara essa página de memória, e retorna o fluxo de execução ao programa do usuário.

Esse mecanismo faz com que a memória só seja efetivamente consumida, após ser utilizada pela primeira vez.

### calloc

Assim como a função `malloc` existe a função calloc que pode ser utilizada para alocar memória onde todos os bytes são zero.

A sintaxe para uso de `calloc` é :
```cpp
void *calloc(size_t num, size_t tamanho);
```

Apesar de ter 2 parâmetros, o tamanho final da alocação é efetivamente a multiplicação `num * tamanho` e o comportamento é exatamente o mesmo de `malloc`, exceto pela memória alocada ser inicializada com 0.

Lembrando que ponteiros nulos não necessariamente são compostos por bytes zerados, apesar desse ser o comportamento padrão em boa parte se não de todos sistemas modernos.

A motivação pelo uso de dois parâmetros é histórica e não tem mais um propósito efetivo nos dias de hoje.

Alguns guias e autores erroneamente falam que utilizar calloc é efetivamente igual a chamar `malloc + memset` para zerar a memória, mas essa constatação está **errada**.

Devido ao mecanismo de muitos sistemas operacionais de esperar o primeiro acesso para realmente alocar, 
uma alocação realizada com calloc pode tirar proveito disso e ser muito mais rápida sem consumir memória até seu primeiro uso.

### aligned_alloc

A função `aligned_alloc` foi introduzida no C11, seu propósito é realizar uma alocação similar a malloc porém controlando o alinhamento de memória.
```cpp
void *aligned_alloc(size_t alinhamento, size_t tamanho);
```

A função tem comportamento extremamente similar a malloc, porém utilizando o alinhamento definido pela função.

Podemos dizer que malloc é efetivamente equivalente a uma chamada de aligned_alloc da seguinte forma

```cpp
void *ptr = aligned_alloc(_Alignof(max_align_t), tamanho);
```

### Realocação de memória

Quando há necessidade de expandir ou diminuir o uso de memória de uma alocação feita anteriormente, utilizamos a função `realloc`, que tem a seguinte sintaxe:

```cpp
void *realloc(void *ptr, size_t tamanho);
```

`ptr` é o ponteiro para a memória previamente alocada com `malloc`, `calloc` ou `aligned_alloc` ou `NULL`.

`tamanho` é o novo tamanho da memória realocada, não recomenda-se passar 0 como tamanho pois o comportamento para tal é definido pela implementação e desde o C23 se tornou indefinido 
(permitindo que implementações tratem esse caso como bug e façam qualquer coisa).

A função retorna o novo bloco realocado, ou NULL caso haja uma falha em realocar, nesse caso o bloco ptr ainda é válido e não é liberado por realloc.

A ideia por trás do `realloc` é que existem dois comportamentos possíveis ao realizar uma realocação :

- A região já alocada por ptr é expandida ou contraida, se possível. os conteúdos da região antiga que estará contida na região nova são mantidos.
- Uma nova região é alocada, o conteúdo da região antiga é copiado para ela e o bloco antigo é liberado.

Lembrando que caso o segundo comportamento seja escolhido devido a uma falha em expandir/contrair a região atual, será necessário ter,
mesmo que temporariamente, memória o suficiente para a existências das duas regiões de memória (a antiga e a nova) para que seja possível copiar os conteúdos da região antiga para a nova.

Também é comum que algumas implementações utilizem o seguinte código ao realizar uma realocação :

```cpp
ptr = realloc(ptr, tamanho);
```

Isso não é exatamente ideal, pois no caso da alocação falhar, teremos efetivamente perdido o valor do endereço da região original, chamamos essa "perda de referência" de vazamento de memória.

Uma forma ideal de utilizar `realloc` lidando com erros corretamente seria :

```cpp
void *novoPtr = realloc(ptr, novoTamanho);
if(novoPtr != NULL) {
    ptr = novoPtr;
    tamanho = novoTamanho;
} else {
    fputs(stderr, "Erro ao realocar memória no arquivo %s e próximo a linha %d\n",
            __FILE__, __LINE__);
}
```
### Funções para liberar memória

Para liberar o uso de memória e permitir que novas alocações ou outros programas no sistema utilizem a memória, existem 3 funções diferentes: `free`, `free_sized` e `free_aligned_sized`.

#### free
free é a principal função para liberar o uso de memória pelas funções de alocação e realocação, até o C23 era a única função para tal.

A sintaxe para uso da função é extremamente simples :

```cpp
void free(void *ptr);
```

