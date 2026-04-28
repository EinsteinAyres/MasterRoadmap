
## Stack vs Heap: onde seus dados realmente vivem

### 1. A distinção que importa

Quando seu programa Java roda, a JVM reserva um bloco de memória do sistema operacional e o divide em regiões com propósitos específicos. As duas mais importantes são Stack e Heap. Não são abstrações teóricas — são regiões físicas da RAM, com regras, velocidades e ciclos de vida diferentes.

A Stack é onde métodos executam. A Heap é onde objetos residem. Confundir as duas não gera erro de compilação, mas gera sistemas que consomem memória sem necessidade, falham sob carga ou têm performance degradada por razões que o desenvolvedor não entende.

---

### 2. Stack: o bloco de rascunho da JVM

A Stack é uma região de memória com duas características definidoras: é **privada por thread** e opera em ordem **LIFO** (Last In, First Out — o último que entra é o primeiro que sai) .

**O que ela armazena:**

- Variáveis locais de tipos primitivos (`int`, `double`, `boolean` e os demais) — o valor fica diretamente na Stack .
- Referências para objetos (o ponteiro, não o objeto em si) .
- Parâmetros de métodos e endereços de retorno.

**Como funciona na prática:**

Cada vez que um método é chamado, a JVM cria um **stack frame** (quadro de pilha) e o empilha. Esse frame contém todas as variáveis locais daquele método, os parâmetros recebidos e a informação de para onde retornar quando o método terminar .

Quando o método termina, o frame é desempilhado e simplesmente descartado. Não há coleta de lixo, não há alocação dinâmica — é uma operação de ponteiro. Por isso a Stack é extremamente rápida, perdendo apenas para os registradores da CPU .

**Exemplo:**

```java
public static void main(String[] args) {
    int a = 10;                // 'a' vive na Stack, no frame de main
    int resultado = soma(a, 5);
}

public static int soma(int x, int y) {
    int total = x + y;         // 'x', 'y' e 'total' vivem na Stack, no frame de soma
    return total;
}
```

Quando `soma` é chamada, um novo frame é empilhado contendo `x`, `y` e `total`. Quando `soma` retorna, esse frame inteiro desaparece. `resultado` recebe o valor, mas as variáveis de `soma` já não existem mais .

**Limitação crítica:**

A Stack tem tamanho fixo por thread, definido pelo parâmetro `-Xss` da JVM (tipicamente 1 MB por thread). Se você encher esse espaço — por exemplo, com recursão infinita ou profundidade excessiva de chamadas — o resultado é um `StackOverflowError` .

```java
public static void recursaoInfinita() {
    recursaoInfinita();  // Cada chamada empilha um frame novo, até estourar
}
```

---

### 3. Heap: o depósito de objetos

A Heap é uma região de memória **compartilhada por todas as threads** da JVM. É aqui que todo objeto criado com `new` e todo array são alocados .

**O que ela armazena:**

- Instâncias de classes (os objetos propriamente ditos, com todos os seus atributos).
- Arrays de qualquer tipo.
- Literais de String (no String Pool, uma subárea da Heap desde Java 7).

**Como funciona:**

Quando você escreve `new Carro()`, a JVM aloca espaço na Heap para armazenar os atributos daquele objeto. Na Stack, apenas uma referência (um endereço de memória) é armazenada, apontando para o objeto na Heap .

```java
public static void main(String[] args) {
    Carro c = new Carro("Honda", 2024);  
    // 'c' é uma referência (Stack) → o objeto Carro está na Heap
}
```

A referência `c` vive na Stack, no frame de `main`. Quando `main` terminar, `c` desaparece. Mas o objeto `Carro` na Heap não desaparece automaticamente — ele permanece até que o Garbage Collector (GC) identifique que não há mais referências apontando para ele .

**Garbage Collection: a limpeza automática**

Diferente de C/C++, onde você precisa liberar memória manualmente com `free()` ou `delete`, em Java o GC cuida disso. Periodicamente, a JVM escaneia a Heap, identifica objetos que não têm mais referências alcançáveis na Stack e os remove .

Isso é conveniente, mas tem custo: o GC consome ciclos de CPU e pode causar pausas. Em sistemas de alta performance ou baixa latência, alocar objetos excessivamente na Heap força o GC a trabalhar mais, degradando a performance.

Se a Heap encher completamente e o GC não conseguir liberar espaço, a JVM lança `OutOfMemoryError` .

---

### 4. Comparação direta

| Característica | Stack | Heap |
|----------------|-------|------|
| **Propriedade** | Privada por thread  | Compartilhada por todas as threads  |
| **Armazena** | Primitivos locais e referências  | Objetos, arrays, atributos de instância  |
| **Velocidade** | Extremamente rápida (LIFO)  | Mais lenta (alocação dinâmica + GC) |
| **Gerenciamento** | Automático — frames são empilhados/desempilhados  | Automático — Garbage Collector  |
| **Tamanho** | Pequeno e fixo por thread (`-Xss`)  | Grande e configurável (`-Xmx`)  |
| **Ciclo de vida** | Variáveis vivem até o método terminar  | Objetos vivem enquanto houver referências  |
| **Erro típico** | `StackOverflowError`  | `OutOfMemoryError`  |
| **Thread safety** | Inerentemente seguro  | Requer sincronização explícita  |

---

### 5. O que realmente importa: consequências práticas

**Use tipos primitivos, não wrappers, quando possível.**

`int` ocupa 4 bytes na Stack e é acessado diretamente. `Integer` é um objeto na Heap, com overhead de cabeçalho do objeto (12-16 bytes) mais o custo de indireção e de coleta de lixo. Em loops com milhões de iterações, a diferença é medida em segundos de CPU e megabytes de memória .

**Entenda o ciclo de vida dos seus objetos.**

Um objeto criado dentro de um método e nunca retornado morre junto com o frame da Stack correspondente. Mas um objeto armazenado em uma coleção estática ou em um atributo de instância de longa duração vive indefinidamente — potencialmente causando memory leaks se você esquecer de removê-lo .

**Cuidado com recursão profunda.**

Cada chamada recursiva consome um frame da Stack. Processar uma árvore com profundidade de 10.000 nós recursivamente pode estourar a Stack. Nesses casos, uma abordagem iterativa com uma pilha explícita na Heap (`ArrayDeque`, por exemplo) é mais segura .

**A String Pool é uma otimização real.**

Quando você escreve `String s = "abc";` (sem `new`), a JVM armazena o literal no String Pool — uma área especial da Heap. Se outra variável usar o mesmo literal, ambas apontam para o mesmo objeto. Isso economiza memória. Já `new String("abc")` cria um objeto novo a cada chamada, ignorando o pool .

```java
String a = "java";
String b = "java";
System.out.println(a == b);  // true (mesmo objeto no pool)

String c = new String("java");
System.out.println(a == c);  // false (objeto diferente na Heap)
```

---

### 6. Resumo em uma frase

A Stack é rápida, pequena e descartável — ideal para variáveis que morrem com o método. A Heap é ampla, compartilhada e persistente — necessária para qualquer dado que sobreviva ao escopo local. Saber onde cada dado reside não é curiosidade acadêmica; é a base para escrever código que não estrangule o Garbage Collector nem desperdice memória.

---

### Fontes

1. CharonChui, "Java内存模型 (Java Memory Model)" — explica a divisão da memória JVM em Stack, Heap, Method Area e o funcionamento de stack frames .

2. upGrad, "Stack and Heap Memory in Java – Differences, Usage & Examples" (2025) — tabela comparativa detalhada e exemplos de alocação em Stack e Heap .

3. JavaRush, "Memory layout in the JVM: stack, heap, PermGen/Metaspace" — visão geral da arquitetura de memória da JVM com diagramas .

4. TechTarget, "深入Java虚拟机：JVM中的Stack和Heap" — análise da Stack como área de instruções e da Heap como área de dados, com explicação do GC .

5. 华为云社区, "简述堆和栈的区别" (2023) — distinção prática entre Stack e Heap, incluindo o mecanismo de compartilhamento de dados na Stack .

6. Team IT Security, "How Java Handles Memory — What Are Stack, Heap, and Garbage Collection?" (2025) — resumo didático com analogias e melhores práticas .

7. CSDN/Tencent Cloud, "java栈stack和堆heap的工作原理,用途及区别？举例说明" — exemplos com Strings e o comportamento do pool de literais .

8. GitHub/CorsoJava, "La gestione della memoria" — papel do Garbage Collector, Stack e Heap, e o papel do Metaspace para classes e membros estáticos .

9. Heroku Dev Center, "Java アプリケーションでのメモリ問題のトラブルシューティング" (2024) — configuração de tamanho de Stack (`-Xss`) e Heap (`-Xmx`) e comportamento em containers .