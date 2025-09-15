# memcpy, strcpy, sprintf — riscos de Buffer Overflow

Este documento explica o que são `memcpy`, `strcpy` e `sprintf`, por que eles podem causar **buffer overflow**, mostra exemplos vulneráveis e apresenta alternativas seguras e boas práticas em C. O objetivo é educativo: entender o problema e como evitá-lo em código real.

---

## Sumário

- O que é Buffer Overflow  
- `memcpy` — descrição, exemplo vulnerável e correção  
- `strcpy` — descrição, exemplo vulnerável e correção  
- `sprintf` — descrição, exemplo vulnerável e correção  
- Boas práticas gerais  
- Ferramentas úteis para depuração  
- Exemplos completos (vulnerável → seguro)

---

# O que é Buffer Overflow

**Buffer overflow** acontece quando você escreve mais dados em um bloco de memória (buffer) do que ele foi alocado para armazenar. Em C, funções que não verificam limites (tamanho) podem sobrescrever memória adjacente — levando a comportamentos indefinidos, crashes e possíveis vulnerabilidades de segurança (execução de código arbitrário, escalonamento de privilégios, etc).

Sempre verifique os tamanhos dos buffers, prefira APIs que aceitem tamanho máximo e considere usar ferramentas de análise (AddressSanitizer, Valgrind) durante o desenvolvimento.

---

# `memcpy`

## O que é
`memcpy(dest, src, n)` copia `n` bytes de `src` para `dest`. **`memcpy` não faz checagem de tamanhos** — cabe ao programador garantir que `dest` tem pelo menos `n` bytes disponíveis.

## Exemplo vulnerável
```c
#include <stdio.h>
#include <string.h>

int main() {
    char src[] = "Esta é uma string muito longa que pode causar overflow";
    char dest[16]; // pequeno demais

    // copia muitos bytes para dest de 16 bytes -> overflow
    memcpy(dest, src, strlen(src) + 1); // +1 para incluir o '\0'
    printf("dest: %s\n", dest);
    return 0;
}
```

**Por que é perigoso:** `dest` tem 16 bytes, `strlen(src)+1` é muito maior — `memcpy` vai sobrescrever memória além de `dest`, causando comportamento indefinido.

## Correção / Alternativa
- Verificar tamanhos antes de copiar:
```c
#include <stdio.h>
#include <string.h>

int main() {
    char src[] = "String razoável";
    char dest[32];

    size_t to_copy = strlen(src) + 1; // incluir '\0'
    if (to_copy <= sizeof(dest)) {
        memcpy(dest, src, to_copy);
    } else {
        // tratar erro ou truncar conscientemente
        memcpy(dest, src, sizeof(dest) - 1);
        dest[sizeof(dest) - 1] = '\0';
    }

    printf("dest: %s\n", dest);
    return 0;
}
```

- Usar `memmove` quando existe possibilidade de regiões sobrepostas (mas ainda verificando tamanhos).

---

# `strcpy`

## O que é
`strcpy(dest, src)` copia a string `src` (incluindo o terminador `\0`) para `dest`. **Não verifica limites** — é uma fonte clássica de buffer overflow.

## Exemplo vulnerável
```c
#include <stdio.h>
#include <string.h>

int main() {
    char src[] = "Muito longo para dest";
    char dest[8];

    strcpy(dest, src); // overflow: src é maior que dest
    printf("dest: %s\n", dest);
    return 0;
}
```

## Correção / Alternativas
- `strncpy` (com cuidado):
```c
#include <stdio.h>
#include <string.h>

int main() {
    char src[] = "Muito longo para dest";
    char dest[8];

    strncpy(dest, src, sizeof(dest) - 1); // copia no máximo 7 bytes
    dest[sizeof(dest) - 1] = '\0'; // garante terminador
    printf("dest: %s\n", dest);
    return 0;
}
```

- `strlcpy` (quando disponível): sempre NUL-termina e retorna o comprimento total que tentou criar.
```c
// strlcpy(dest, src, sizeof(dest)); // em plataformas BSD / algumas libcs
```

- Alocar dinamicamente baseado no tamanho:
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
    char src[] = "Um texto qualquer";
    char *dest = malloc(strlen(src) + 1);
    if (!dest) return 1;
    strcpy(dest, src); // agora seguro: dest é grande o suficiente
    printf("dest: %s\n", dest);
    free(dest);
    return 0;
}
```

---

# `sprintf`

## O que é
`sprintf(buf, fmt, ...)` escreve para `buf` formatando a entrada. **Não verifica** se o buffer tem espaço suficiente — pode escrever além do fim do buffer.

## Exemplo vulnerável
```c
#include <stdio.h>

int main() {
    char buf[16];
    int a = 12345;
    // suponha que o formato gere uma string longa
    sprintf(buf, "valor: %d, mais texto: %s", a, "uma string longa aqui");
    printf("%s\n", buf); // overflow possível
    return 0;
}
```

## Correção / Alternativas
- Usar `snprintf` com verificação do retorno:
```c
#include <stdio.h>

int main() {
    char buf[16];
    int a = 12345;
    int written = snprintf(buf, sizeof(buf), "valor: %d, texto", a);
    if (written < 0) {
        // erro
    } else if ((size_t)written >= sizeof(buf)) {
        // truncado — tomar ação (aumentar buffer, log, etc.)
    }
    printf("%s\n", buf);
    return 0;
}
```

- Alocar dinamicamente com `vsnprintf` para descobrir o tamanho necessário:
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdarg.h>

char *format_alloc(const char *fmt, ...) {
    va_list ap;
    va_start(ap, fmt);
    int needed = vsnprintf(NULL, 0, fmt, ap);
    va_end(ap);

    if (needed < 0) return NULL;
    char *buf = malloc((size_t)needed + 1);
    if (!buf) return NULL;

    va_start(ap, fmt);
    vsnprintf(buf, (size_t)needed + 1, fmt, ap);
    va_end(ap);
    return buf;
}

int main() {
    char *s = format_alloc("Número: %d, texto: %s", 42, "exemplo longo");
    if (s) {
        puts(s);
        free(s);
    }
    return 0;
}
```

---

# Boas práticas gerais

- **Nunca** assuma que o buffer de destino é grande o suficiente — sempre verifique.  
- Use funções que recebem o tamanho do buffer: `snprintf`, `memcpy` com checagem, `memmove` quando necessário, `strncpy` com atenção, ou `strlcpy/strlcat` quando disponíveis.  
- Em situações de truncamento, garanta o terminador `'\0'` manualmente.  
- Prefira alocação dinâmica quando o tamanho da entrada é variável.  
- Compile com flags de segurança (`-fstack-protector-strong`) e use sanitizers (`-fsanitize=address`).  
- Evite funções inseguros como `gets()` (removida do padrão C).

---

# Ferramentas úteis para depuração / detecção

- **AddressSanitizer (ASan):** compile com `-fsanitize=address -g -O1` e execute; detecta muitos overflows.  
- **Valgrind:** detecta acessos inválidos de memória.  
- **Warnings do compilador:** habilite `-Wall -Wextra -Wformat-security`.  
- **strlcpy/strlcat:** quando disponíveis, são mais seguras que `strncpy`.

---

# Exemplos completos

## `exemplo_vulneravel.c`
```c
#include <stdio.h>
#include <string.h>

int main() {
    char small[8];
    char *big = "esta string é maior que oito";

    // Vulnerável: strcpy sem checagem
    strcpy(small, big); // BUFFER OVERFLOW
    printf("small: %s\n", small);
    return 0;
}
```

## `exemplo_seguro.c`
```c
#include <stdio.h>
#include <string.h>

int main() {
    char small[8];
    char *big = "esta string é maior que oito";

    // Seguro: copiar no máximo sizeof(small)-1 e garantir terminador
    strncpy(small, big, sizeof(small) - 1);
    small[sizeof(small) - 1] = '\0';
    printf("small (truncado): %s\n", small);

    // Exemplo com snprintf
    char buf[32];
    int n = snprintf(buf, sizeof(buf), "format: %s", big);
    if (n >= (int)sizeof(buf)) {
        // truncamento
    }
    printf("buf: %s\n", buf);

    return 0;
}
```

---

# Como compilar e testar

Exemplos de compilação:
```bash
# compilar exemplo seguro
gcc -Wall -Wextra -o exemplo_seguro exemplo_seguro.c

# compilar com AddressSanitizer (detecta overflow em tempo de execução)
gcc -fsanitize=address -g -O1 -o exemplo_vuln exemplo_vulneravel.c
./exemplo_vuln
```

---

# Referências rápidas

- `memcpy(dest, src, n)` — copia `n` bytes; **verifique** que `dest` tem `n` bytes.  
- `strcpy(dest, src)` — copia a string inteira; **pode overflow**; use `strncpy`/`strlcpy`/alocação dinâmica.  
- `sprintf(buf, ...)` — formata sem limites; **pode overflow**; use `snprintf` e verifique retorno.

---

> **Aviso:** os exemplos vulneráveis têm fins educativos. Não use código vulnerável em produção e não explore vulnerabilidades em sistemas sem autorização.
