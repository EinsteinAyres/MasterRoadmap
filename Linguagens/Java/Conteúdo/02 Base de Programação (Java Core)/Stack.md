
## Stack: a memória que se autolimpa

### 1. O que é

A Stack é uma região de memória que a JVM atribui a cada thread no momento de sua criação. Ela é privada — cada thread tem a sua, isolada das demais. Não há compartilhamento, não há concorrência, não há necessidade de sincronização.

O nome "stack" (pilha) descreve exatamente como ela opera: uma estrutura LIFO — Last In, First Out. O último frame empilhado é o primeiro a ser desempilhado. É um mecanismo tão simples que o hardware consegue executá-lo com custo mínimo: empilhar e desempilhar são essencialmente operações de ponteiro.

---

### 2. O que a Stack armazena

Cada vez que um método é chamado, a JVM cria um **stack frame** (quadro de pilha) e o coloca no topo da Stack daquela thread. Esse frame contém:

- **Variáveis locais de tipos primitivos** (`int`, `double`, `boolean`, `char`, `byte`, `short`, `long`, `float`). O valor fica diretamente no frame, não há indireção.
- **Referências para objetos** (os ponteiros, não os objetos em si). Um objeto criado com `new` vive na Heap; na Stack fica apenas o endereço que aponta para ele.
- **Parâmetros do método** — tratados como variáveis locais inicializadas com os argumentos da chamada.
- **Endereço de retorno** — para onde a execução deve voltar quando o método terminar.

Quando o método termina (por `return` ou por exceção não capturada), o frame inteiro é removido da Stack. Todas as variáveis locais deixam de existir. Não há liberação manual, não há coleta de lixo — é uma remoção atômica do ponteiro de topo.

---

### 3. Como funciona na prática

```java
public static void main(String[] args) {
    int a = 10;
    int b = 20;
    int resultado = somar(a, b);
    System.out.println(resultado);
}

public static int somar(int x, int y) {
    int soma = x + y;
    return soma;
}
```

Passo a passo:

1. `main` começa. Um frame é criado contendo `args`, `a`, `b` e espaço para `resultado`.
2. `somar(a, b)` é chamado. Um novo frame é empilhado contendo `x=10`, `y=20` e `soma`.
3. `soma` recebe 30. O método retorna esse valor. O frame de `somar` é desempilhado e destruído.
4. De volta a `main`, `resultado` recebe 30. Continua a execução.
5. Quando `main` termina, seu frame é desempilhado. A Stack da thread principal fica vazia.

Os valores 10, 20 e 30 nunca saíram da Stack. Nenhum objeto foi criado. Nenhum garbage collector foi acionado. Tudo aconteceu com operações de ponteiro.

---

### 4. Escopo e ciclo de vida

Uma variável na Stack existe exatamente enquanto o bloco onde foi declarada estiver em execução. Isso é o **escopo léxico**:

```java
if (algumaCondicao) {
    int temp = 42;
    // 'temp' existe aqui
}
// 'temp' já não existe aqui
```

O compilador impõe essa regra. Tentar acessar `temp` fora do bloco gera erro de compilação. Na Stack, a variável ocupa espaço apenas durante a execução daquele trecho — embora o frame completo só seja liberado no fim do método.

Para variáveis de tipos primitivos, o valor morre com o escopo. Para referências, a referência morre, mas o objeto apontado (na Heap) permanece. Se nenhuma outra referência apontar para ele, o Garbage Collector eventualmente o remove.

---

### 5. Velocidade

A Stack é a região de memória mais rápida disponível para o programador, atrás apenas dos registradores da CPU.

Isso se deve a três fatores:

1. **Alocação e liberação são operações de ponteiro.** Empilhar um frame é mover o stack pointer para baixo. Desempilhar é movê-lo de volta. Não há busca por espaço livre, não há fragmentação, não há lista de blocos disponíveis — o espaço está sempre no topo.

2. **Localidade de referência total.** Os frames são contíguos. As variáveis de um método estão lado a lado na memória. Isso faz a Stack ser extremamente amigável ao cache L1/L2 do processador.

3. **Sem concorrência.** Cada thread tem sua própria Stack. Não há locks, não há contenção, não há CAS (compare-and-swap). Acesso exclusivo e garantido.

---

### 6. A limitação

A Stack é rápida, mas é pequena. O tamanho padrão por thread na maioria das JVMs é **1 megabyte**, configurável via `-Xss` (por exemplo, `-Xss512k` para 512 KB ou `-Xss2m` para 2 MB).

O tamanho reduzido é intencional: com milhares de threads, cada uma com sua Stack, o consumo total seria enorme se cada Stack fosse grande. Além disso, a Stack foi projetada para armazenar variáveis locais e encadeamento de chamadas — não grandes volumes de dados.

Quando a Stack enche, a JVM lança **`StackOverflowError`**. As causas mais comuns:

**Recursão sem caso base ou muito profunda:**

```java
public static void infinito() {
    infinito();  // Cada chamada empilha um frame. Não há retorno. Stack estoura.
}
```

**Alocações locais excessivas:**

```java
public static void metodoGigante() {
    long[] array = new long[1_000_000];  // 8 MB — a referência fica na Stack, 
                                          // mas só de variáveis locais pesadas
                                          // você pode empurrar o frame além do limite
}
```

**Encadeamento muito profundo de chamadas** (raro em código de aplicação, mais comum em algoritmos recursivos sobre árvores profundas).

---

### 7. Stack vs. registradores

Os registradores são a memória dentro da CPU — a camada mais rápida de toda a hierarquia. A Stack está um degrau abaixo. Mas os dois estão intimamente ligados.

A JVM é uma máquina baseada em Stack (diferente de arquiteturas de CPU baseadas em registradores, como x86). As instruções de bytecode Java operam sobre a **operand stack** — uma pilha interna de cada frame onde valores são empurrados, operados e consumidos. O compilador JIT, porém, traduz essas operações para usar registradores da CPU sempre que possível.

Na prática, variáveis locais que cabem em registradores (como inteiros e referências) frequentemente acabam alocadas em registradores durante a compilação JIT, nunca tocando a Stack de fato em trechos de código quente. Mas essa é uma otimização transparente — o modelo mental de que tudo vai para a Stack permanece correto para entender escopo e ciclo de vida.

---

### 8. O que levar disso

A Stack é o mecanismo mais elegante de gerenciamento de memória que existe: alocação instantânea, liberação automática, isolamento total entre threads, velocidade máxima. Seu custo é o tamanho restrito.

Para o desenvolvedor Java, as implicações práticas são:

- **Use tipos primitivos para variáveis locais sempre que possível.** `int` na Stack é mais barato que `Integer` na Heap — em memória, em tempo de acesso e em pressão sobre o GC.

- **Prefira estruturas de dados alocadas na Stack quando viável**, o que em Java significa arrays de primitivos locais e variáveis individuais. (Java não tem `struct` na Stack como C#, mas records locais são alocados na Heap — a referência fica na Stack.)

- **Evite recursão profunda** sem garantia de que a profundidade é controlada. Uma árvore balanceada com milhares de níveis pode estourar a Stack. Nesses casos, use uma abordagem iterativa com `ArrayDeque` ou estrutura semelhante.

- **Não tema a Stack.** Ela é sua aliada. Quanto mais dados você mantém nela (no escopo local, sem escapar para atributos de instância), menos pressão você exerce sobre o Heap e o GC. Código que resolve problemas com variáveis locais e primitivas é código que o hardware executa com prazer.