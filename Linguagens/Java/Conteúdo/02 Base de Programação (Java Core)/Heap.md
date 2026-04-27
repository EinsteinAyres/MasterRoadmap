## Heap: o depósito de objetos

### 1. O que é

A Heap é uma região de memória criada pela JVM no momento da inicialização. Diferente da Stack, que é privada por thread, a Heap é **compartilhada por todas as threads** do processo. É aqui que todo objeto criado com `new`, todo array e toda String literal (no String Pool) são alocados.

Enquanto a Stack é uma estrutura LIFO de vida curta, a Heap é um grande espaço desorganizado onde objetos nascem, vivem e morrem em momentos diferentes, governados por um único critério: se ainda há referências alcançáveis apontando para eles.

---

### 2. O que a Heap armazena

- **Instâncias de classes** — qualquer objeto criado com `new`. Todos os atributos de instância (incluindo primitivos que pertencem ao objeto) residem aqui.
- **Arrays** de qualquer tipo, seja de primitivos (`int[]`) ou de objetos (`String[]`).
- **Literais de String** — desde o Java 7, o String Pool foi movido da PermGen para a Heap.
- **Constantes de classe** — os valores `static final` de tipos primitivos e Strings que o compilador resolve em tempo de compilação também podem residir em áreas relacionadas.

Quando você declara `int[] numeros = {1, 2, 3};`, a referência `numeros` fica na Stack (se for variável local), mas o array propriamente dito — os três inteiros — fica na Heap.

---

### 3. Como funciona na prática

```java
public class Exemplo {
    public static void main(String[] args) {
        Carro c = new Carro("Honda", 2024);
        // 'c' é uma referência na Stack
        // O objeto Carro com atributos "Honda" e 2024 está na Heap
    }
}
```

A referência `c` ocupa 4 ou 8 bytes na Stack (dependendo da arquitetura). Ela armazena um endereço de memória. O objeto `Carro`, por sua vez, ocupa muito mais — cabeçalho do objeto (12-16 bytes), mais cada atributo, mais possíveis bytes de alinhamento.

Quando `main` termina, `c` desaparece da Stack. Mas o objeto `Carro` continua na Heap. Ele só será removido quando o Garbage Collector passar e concluir que não há mais nenhuma referência alcançável para ele.

---

### 4. A alocação não é caótica — é otimizada

Dizer que a Heap é "desorganizada" é enganoso. A JVM aplica estratégias sofisticadas para tornar a alocação extremamente rápida.

**Alocação com pointer bumping:**

Em condições normais, alocar um objeto na Heap é tão simples quanto mover um ponteiro. A JVM mantém um ponteiro para a próxima posição livre. Quando você faz `new`, o espaço necessário é reservado avançando esse ponteiro. É uma única instrução de adição — comparável em velocidade a uma alocação na Stack.

Isso funciona enquanto há espaço contíguo. Quando a Heap fica fragmentada ou lotada, estratégias mais complexas entram em ação.

**TLABs (Thread-Local Allocation Buffers):**

Para evitar contenção entre threads (já que a Heap é compartilhada), a JVM entrega a cada thread um pequeno pedaço privado da Heap chamado TLAB. Quando uma thread aloca um objeto pequeno, ela o faz dentro de seu TLAB, sem precisar sincronizar com as outras threads. Só quando o TLAB esgota a thread pede um novo.

Isso faz a alocação de objetos de curta duração ser essencialmente tão rápida quanto alocação na Stack, competindo apenas na liberação — que na Stack é instantânea e na Heap depende do GC.

---

### 5. Garbage Collection: a limpeza que você não controla

A Stack se autolimpa: método termina, frame desaparece. A Heap não tem esse luxo. Objetos podem ter ciclos de vida completamente imprevisíveis — um objeto criado no primeiro segundo de execução pode ser necessário até o último.

O **Garbage Collector** (GC) é o componente da JVM que resolve isso. Ele periodicamente vasculha a Heap, identifica objetos que não têm mais referências alcançáveis a partir das raízes (GC roots: variáveis na Stack de qualquer thread, membros estáticos, referências JNI) e libera o espaço ocupado por eles.

**O processo é complexo e tem várias estratégias:**

- **Minor GC (coleta da Young Generation):** a maioria dos objetos morre jovem. A JVM divide a Heap em Young Generation (onde objetos nascem) e Old Generation (para onde são promovidos se sobrevivem a várias coletas). A coleta da Young Generation é rápida — varre apenas objetos novos e copia os sobreviventes.
- **Major GC / Full GC (coleta da Old Generation):** mais lenta e potencialmente pausante. Percorre toda a Heap. É a principal causa de pausas perceptíveis em aplicações.

**O custo real:** o GC consome ciclos de CPU. Pior, algumas fases do GC (dependendo do coletor) exigem pausar todas as threads da aplicação — os chamados *stop-the-world pauses*. Em sistemas de baixa latência (finanças, jogos, sistemas em tempo real), essas pausas são inaceitáveis e ditam escolhas de arquitetura e configuração.

---

### 6. Configuração e limites

A Heap tem tamanho configurável. Os parâmetros principais são:

- **`-Xms`**: tamanho inicial da Heap.
- **`-Xmx`**: tamanho máximo da Heap. Quando a Heap atinge esse limite e o GC não consegue liberar espaço suficiente, a JVM lança `OutOfMemoryError`.
- **`-XX:NewRatio`**: proporção entre Young e Old Generation.
- **`-XX:SurvivorRatio`**: proporção dentro da Young Generation entre Eden e Survivor spaces.

Uma configuração inadequada da Heap é uma das causas mais comuns de problemas em produção. Heap pequena demais força coletas frequentes e pode causar `OutOfMemoryError`. Heap grande demais aumenta a duração das pausas de GC e o footprint de memória do processo.

---

### 7. O que realmente importa: consequências práticas

**Objetos que escapam do escopo local permanecem na Heap.**

Uma variável local que é retornada por um método ou armazenada em um campo de instância de longa duração continua viva na Heap. Se você constantemente adiciona objetos a uma coleção estática ou a um cache sem política de evicção, está criando um vazamento de memória — objetos que nunca serão coletados porque ainda são alcançáveis.

```java
public class CacheVazado {
    private static List<Dado> cache = new ArrayList<>();
    
    public void adicionar(Dado d) {
        cache.add(d);  // Este objeto nunca será removido. 
                        // A lista cresce indefinidamente.
    }
}
```

**O custo oculto dos wrappers e do autoboxing.**

`int` vive na Stack (se local) ou inline dentro do objeto na Heap (se atributo). `Integer` é um objeto separado na Heap, com cabeçalho, referência e coleta de lixo. Em um loop com um milhão de iterações, a diferença entre somar `int` primitivos e somar `Integer` com autoboxing pode ser ordens de magnitude:

```java
// Rápido — opera na Stack, zero GC
int soma = 0;
for (int i = 0; i < 1_000_000; i++) {
    soma += i;
}

// Lento — cada iteração cria um Integer na Heap, depois descarta
Integer soma = 0;
for (Integer i = 0; i < 1_000_000; i++) {
    soma += i;  // Unboxing, soma, autoboxing — tudo alocando na Heap
}
```

**Strings e o String Pool.**

Para Strings, a distinção importa:

```java
String a = "java";            // Literal — vai para o String Pool na Heap.
                              // Se "java" já existir no pool, reutiliza.
String b = "java";            // Mesma referência do pool.
System.out.println(a == b);   // true

String c = new String("java"); // Novo objeto, fora do pool.
System.out.println(a == c);    // false — objetos diferentes.
```

Usar literais quando possível economiza memória ao evitar duplicação. Usar `new String()` explicitamente quase nunca é necessário e só cria trabalho para o GC.

---

### 8. Heap vs. Stack: a regra de ouro

| Situação | Onde vive |
|----------|-----------|
| `int x = 5;` dentro de um método | Stack (o valor 5 diretamente) |
| `int[] arr = {1, 2, 3};` dentro de um método | Referência `arr` na Stack; o array `{1,2,3}` na Heap |
| `Carro c = new Carro();` dentro de um método | Referência `c` na Stack; o objeto `Carro` na Heap |
| Atributo `private int ano;` em uma classe | Dentro do objeto, na Heap |
| `static int contador;` | Na área de metadados da classe (Metaspace), não na Heap |

---

### 9. Resumo em uma frase

A Stack é o rascunho veloz que se apaga sozinho quando o método termina. A Heap é o arquivo permanente que cresce enquanto houver referências e só é limpo pelo Garbage Collector — um faxineiro automático que você não controla, mas cujo custo você sente se abusar da bagunça.