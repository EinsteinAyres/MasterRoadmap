
## Queue: a fila que processa um por um

### 1. O que é Queue

`Queue` é uma interface do Java Collections Framework que representa uma **fila** — uma coleção projetada para armazenar elementos antes de processá-los, tipicamente em ordem **FIFO** (First-In, First-Out: o primeiro que entra é o primeiro que sai). Ela estende `Collection` e adiciona métodos específicos para inserção, remoção e inspeção de elementos nas extremidades da fila.

É a estrutura que você usa quando a ordem de processamento importa — tarefas a executar, mensagens a entregar, requisições a atender. Enquanto `List` oferece acesso aleatório por índice e `Set` garante unicidade, `Queue` impõe uma disciplina de acesso: você interage principalmente com a "cabeça" (head) da fila.

---

### 2. Características essenciais

**FIFO por padrão, mas não obrigatoriamente.** A maioria das implementações segue a ordem First-In, First-Out — o elemento que está na fila há mais tempo é o próximo a sair. Mas `Queue` também é a interface base para `Deque` (fila de duas pontas) e `PriorityQueue` (ordenação por prioridade), que seguem disciplinas diferentes.

**Operações nas extremidades.** Diferentemente de `List`, que permite inserir e remover em qualquer posição, `Queue` concentra as operações na cabeça (remoção e inspeção) e na cauda (inserção).

**Dois "sabores" de cada operação.** Todo método essencial da `Queue` tem duas versões: uma que lança exceção quando a operação falha e outra que retorna um valor especial (`null` ou `false`). Isso permite escolher entre tratamento explícito de erros e código mais fluente.

**Capacidade pode ser limitada.** Algumas implementações (`ArrayBlockingQueue`, `LinkedBlockingQueue` com capacidade) têm capacidade máxima. Outras (`LinkedList`, `PriorityQueue`) são ilimitadas (até a memória acabar).

---

### 3. Métodos essenciais

Cada operação tem duas versões — a que lança exceção e a que retorna valor especial :

| Operação | Lança exceção | Retorna valor especial |
|----------|---------------|------------------------|
| **Inserir** | `add(E e)` — `IllegalStateException` se a fila estiver cheia | `offer(E e)` — retorna `false` se a fila estiver cheia |
| **Remover** | `remove()` — `NoSuchElementException` se a fila estiver vazia | `poll()` — retorna `null` se a fila estiver vazia |
| **Inspecionar** | `element()` — `NoSuchElementException` se a fila estiver vazia | `peek()` — retorna `null` se a fila estiver vazia |

```java
Queue<String> fila = new LinkedList<>();

// Inserir
fila.offer("Primeiro");      // Retorna true
fila.offer("Segundo");
fila.add("Terceiro");        // Equivalente a offer, mas lança exceção se falhar

// Inspecionar (sem remover)
String cabeca = fila.peek(); // "Primeiro" — não remove
String cabeca2 = fila.element(); // "Primeiro" — lança exceção se vazia

// Remover
String removido = fila.poll();    // "Primeiro" — remove e retorna
String removido2 = fila.remove(); // "Segundo" — lança exceção se vazia

System.out.println(fila);  // [Terceiro]
```

---

### 4. Principais implementações

**LinkedList** — Sim, `LinkedList` implementa tanto `List` quanto `Deque` (que estende `Queue`). Como fila FIFO pura, é uma implementação simples baseada em lista duplamente encadeada. `offer()` adiciona ao final, `poll()` remove do início. Ambas as operações custam O(1). Permite `null` (evite — `poll()` retornando `null` é ambíguo: fila vazia ou elemento `null`?).

```java
Queue<String> fila = new LinkedList<>();
fila.offer("A");
fila.offer("B");
System.out.println(fila.poll());  // "A"
```

**PriorityQueue** — Fila com prioridade. Não segue FIFO: os elementos são ordenados segundo sua ordem natural (`Comparable`) ou um `Comparator` fornecido. O elemento de **menor** valor (segundo a ordenação) é sempre a cabeça da fila. Internamente, é um **heap binário** implementado sobre um array. `offer()` e `poll()` custam O(log n). `peek()` custa O(1). **Não permite `null`.**

```java
Queue<Integer> prioritaria = new PriorityQueue<>();
prioritaria.offer(5);
prioritaria.offer(1);
prioritaria.offer(3);
System.out.println(prioritaria.poll());  // 1 (menor)
System.out.println(prioritaria.poll());  // 3
System.out.println(prioritaria.poll());  // 5
```

**ArrayBlockingQueue** — Fila **bloqueante** com capacidade fixa, baseada em array circular. Thread-safe por natureza. `put()` bloqueia a thread se a fila estiver cheia; `take()` bloqueia se estiver vazia. `offer()` e `poll()` têm versões com timeout. Ideal para cenários produtor-consumidor com limite de capacidade.

```java
BlockingQueue<String> fila = new ArrayBlockingQueue<>(3);
fila.offer("A");
fila.offer("B");
fila.offer("C");
fila.offer("D");  // false — fila cheia (sem bloquear)
```

**LinkedBlockingQueue** — Similar à `ArrayBlockingQueue`, mas baseada em nós encadeados. Pode ser limitada (capacidade definida no construtor) ou ilimitada (`Integer.MAX_VALUE`). Melhor quando o número de elementos é imprevisível.

---

### 5. PriorityQueue por dentro

`PriorityQueue` é implementada como um **min-heap binário** usando um array. O elemento de menor valor (segundo a ordenação configurada) fica na raiz do heap (índice 0 do array). Isso garante que `peek()` seja sempre O(1) — você acessa diretamente o menor elemento.

**Funcionamento do `offer(elemento)`:**
1. Insere o elemento no final do array.
2. "Sobe" o elemento (`siftUp`) comparando com o pai: se for menor, troca de posição. Repete até que a propriedade do heap seja restaurada. Custo: O(log n).

**Funcionamento do `poll()`:**
1. Remove o elemento na raiz (índice 0) — o menor.
2. Move o último elemento do array para a raiz.
3. "Desce" esse elemento (`siftDown`) comparando com os filhos: troca com o menor filho até que a propriedade do heap seja restaurada. Custo: O(log n).

**Atenção à iteração:** o iterador do `PriorityQueue` **não** garante percorrer os elementos em ordem de prioridade. Ele percorre o array interno, que é uma representação do heap, não uma sequência ordenada. Para obter os elementos em ordem, faça `poll()` repetidamente ou converta para lista e ordene.

---

### 6. BlockingQueue: filas para concorrência

`BlockingQueue` (no pacote `java.util.concurrent`) estende `Queue` com operações que bloqueiam a thread até que a operação seja possível. É a espinha dorsal do padrão **produtor-consumidor** em Java.

**Métodos adicionais:**

| Método | Descrição |
|--------|-----------|
| `put(E e)` | Insere, bloqueando se a fila estiver cheia até que haja espaço |
| `take()` | Remove, bloqueando se a fila estiver vazia até que haja elementos |
| `offer(E e, long timeout, TimeUnit unit)` | Insere, esperando até o timeout se a fila estiver cheia |
| `poll(long timeout, TimeUnit unit)` | Remove, esperando até o timeout se a fila estiver vazia |
| `drainTo(Collection c)` | Remove todos os elementos disponíveis e os adiciona à coleção fornecida |
| `remainingCapacity()` | Retorna quantos elementos ainda cabem |

```java
BlockingQueue<String> fila = new ArrayBlockingQueue<>(2);

// Producer (outra thread)
new Thread(() -> {
    try {
        fila.put("Tarefa 1");  // Bloqueia se a fila estiver cheia
        fila.put("Tarefa 2");
        fila.put("Tarefa 3");  // Esta thread bloqueia aqui até alguém consumir
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();

// Consumer (outra thread)
new Thread(() -> {
    try {
        String tarefa = fila.take();  // Bloqueia se a fila estiver vazia
        processar(tarefa);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
}).start();
```

**Principais implementações de `BlockingQueue`:**
- `ArrayBlockingQueue` — array circular com capacidade fixa. Um lock único para put e take.
- `LinkedBlockingQueue` — nós encadeados, pode ser limitada ou não. Dois locks (put e take separados), maior throughput.
- `PriorityBlockingQueue` — heap thread-safe, ilimitada. Bloqueia apenas na remoção de fila vazia.
- `SynchronousQueue` — capacidade zero: cada `put` espera um `take` e vice-versa (handoff direto entre threads).
- `DelayQueue` — elementos só podem ser removidos após um atraso configurado.

---

### 7. Queue vs. Deque

`Deque` (Double-Ended Queue, pronuncia-se "deck") estende `Queue` adicionando operações em **ambas as extremidades**. Pode funcionar como fila FIFO ou como pilha LIFO.

```java
Deque<String> deque = new ArrayDeque<>();
deque.addFirst("A");  // [A]
deque.addLast("B");   // [A, B]
deque.offerFirst("C"); // [C, A, B]
deque.offerLast("D");  // [C, A, B, D]

deque.getFirst();     // "C" — lança exceção se vazio
deque.peekFirst();    // "C" — retorna null se vazio
deque.removeLast();   // "D" — lança exceção se vazio
deque.pollLast();     // "B" — retorna null se vazio
```

**Quando usar `Deque` como pilha:** `ArrayDeque` é a implementação recomendada pela documentação oficial para substituir `Stack` (que é legada). Use `push()` e `pop()` ou os métodos `First`:

```java
Deque<String> pilha = new ArrayDeque<>();
pilha.push("A");
pilha.push("B");
System.out.println(pilha.pop());  // "B"
```

---

### 8. Queue vs. List: a decisão

| Característica | Queue | List |
|---------------|-------|------|
| **Acesso** | Apenas cabeça e cauda | Aleatório por índice |
| **Ordem** | FIFO (padrão) ou prioridade | Ordem de inserção |
| **Inserção** | Na cauda (`offer`) | Em qualquer posição (`add(index, e)`) |
| **Remoção** | Da cabeça (`poll`) | De qualquer posição (`remove(index)`) |
| **Inspeção** | `peek()` sem remover | `get(index)` sem remover |
| **Bloqueio** | `BlockingQueue` para concorrência | Não (use `CopyOnWriteArrayList` ou sincronização manual) |
| **Uso típico** | Processamento sequencial, tarefas, mensagens | Armazenamento geral, acesso por posição |

**Use `Queue` quando:** você precisa processar elementos em uma ordem específica (FIFO, prioridade) e não precisa acessar posições arbitrárias.

**Use `List` quando:** você precisa de acesso aleatório, modificações em qualquer posição ou simplesmente armazenar elementos sem um padrão de consumo definido.

---

### 9. Quando usar cada implementação

**Use `LinkedList` como Queue quando:**
- Você precisa de uma fila FIFO simples e ilimitada.
- Não há concorrência.
- Você aceita o overhead de memória dos nós encadeados.

**Use `PriorityQueue` quando:**
- Os elementos têm prioridade e devem ser processados do menor para o maior.
- Você precisa de `peek()` O(1) para o menor elemento.
- Exemplos: fila de tarefas com prioridade, algoritmo de Dijkstra, heap de máximos/mínimos.

**Use `ArrayDeque` quando:**
- Você precisa de uma fila FIFO **ou** de uma pilha LIFO eficiente.
- Você quer performance melhor que `LinkedList` (array contíguo, cache-friendly).
- É a implementação de fila mais rápida para uso single-thread.

**Use `ArrayBlockingQueue` quando:**
- Você tem produtores e consumidores concorrentes.
- A capacidade máxima é conhecida e deve ser imposta.
- Você quer backpressure natural (produtor bloqueia quando a fila enche).

**Use `LinkedBlockingQueue` quando:**
- Cenário produtor-consumidor com tamanho imprevisível.
- Você quer throughput maior que `ArrayBlockingQueue` (locks separados para put e take).

---

### 10. Cuidados e armadilhas

**`null` em filas.** A maioria das implementações de `Queue` (exceto `LinkedList`) não permite elementos `null`. O motivo é que `poll()` retorna `null` para indicar "fila vazia" — se você pudesse inserir `null`, seria impossível distinguir entre "fila vazia" e "o próximo elemento é null". Evite `null` em filas, mesmo nas que permitem.

**Iteração no `PriorityQueue` não é ordenada.** Como mencionado, o iterador percorre o array interno do heap, não a ordem de prioridade. Se você precisa dos elementos em ordem, faça `poll()` até esvaziar ou copie para uma lista e ordene.

**`PriorityQueue` não é thread-safe.** Use `PriorityBlockingQueue` se houver concorrência.

**Fila sem consumo.** Se você adiciona elementos a uma fila ilimitada e nunca consome, eventualmente terá `OutOfMemoryError`. Em cenários produtor-consumidor, sempre considere um limite de capacidade e uma política de descarte para quando a fila encher.

**`element()` e `remove()` em filas vazias.** Se você não tem certeza de que a fila não está vazia, use `peek()` e `poll()` — eles retornam `null` em vez de lançar exceção. Reserve `element()` e `remove()` para casos onde fila vazia é uma condição excepcional de fato.

---

### 11. Resumo em uma frase

`Queue` é a coleção que impõe disciplina de acesso — use `LinkedList` ou `ArrayDeque` para FIFO simples, `PriorityQueue` para ordem de prioridade, e `BlockingQueue` quando produtores e consumidores concorrentes precisam de backpressure ou bloqueio.

---

### Fontes

1. Oracle, "The Queue Interface" — documentação oficial com definição, métodos (offer, poll, peek) e implementações 
2. Dev.java, "Queue Interface" — guia oficial sobre a interface Queue e suas implementações 
3. Baeldung, "Guide to the Java Queue Interface" — tutorial abrangente com exemplos, PriorityQueue, BlockingQueue e Deque 
4. GeeksforGeeks, "Queue Interface in Java" — definição, métodos, implementações e exemplos práticos 
5. CodeGym, "Java Queue Interface" — material didático com PriorityQueue, ArrayBlockingQueue e operações blocking 
6. 廖雪峰的官方网站, "Java Queue" — explicação das implementações com exemplos de PriorityQueue e Deque 
7. Oracle, "BlockingQueue (Java Platform SE 8)" — documentação canônica da interface BlockingQueue com put, take, offer e poll com timeout