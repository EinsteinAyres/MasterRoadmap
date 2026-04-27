## ArrayList: o array que cresce sozinho

### 1. O problema que o ArrayList resolve

Arrays em Java têm tamanho fixo. Uma vez criado um `int[10]`, ele terá exatamente 10 posições até o fim de sua existência. Se você precisar de uma décima primeira posição, não há o que fazer — precisa criar um array novo, maior, e copiar tudo manualmente.

`ArrayList` elimina esse problema. É uma implementação da interface `List` que usa um array internamente, mas cuida sozinha do redimensionamento. Quando o array interno enche, o `ArrayList` cria um maior, copia os elementos e segue em frente — tudo transparente para quem usa .

---

### 2. Estrutura interna

Por dentro, um `ArrayList` é surpreendentemente simples. São apenas dois campos :

```java
transient Object[] elementData;  // o array que armazena os elementos
private int size;                // quantos elementos efetivamente existem
```

`elementData` é o array bruto onde os objetos ficam. `size` é o contador de quantas posições estão ocupadas — que pode ser menor (e geralmente é) que `elementData.length`.

Quando você escreve:

```java
List<String> nomes = new ArrayList<>();
nomes.add("Ana");
nomes.add("Carlos");
```

Acontece internamente:

1. `elementData` é um `Object[]` de algum tamanho.
2. `nomes.add("Ana")` coloca `"Ana"` em `elementData[0]` e incrementa `size` para 1.
3. `nomes.add("Carlos")` coloca `"Carlos"` em `elementData[1]` e incrementa `size` para 2.
4. `nomes.get(0)` simplesmente retorna `elementData[0]` — acesso direto por índice.

---

### 3. Complexidade das operações

A simplicidade do array interno dita a performance de cada operação :

| Operação | Complexidade | Por quê |
|----------|--------------|--------|
| `get(index)` | **O(1)** | Acesso direto ao índice do array |
| `set(index, elem)` | **O(1)** | Substituição direta no índice |
| `add(e)` (no final) | **O(1) amortizado** | Normalmente só insere; raramente redimensiona |
| `add(index, elem)` | **O(n)** | Precisa deslocar todos os elementos à direita |
| `remove(index)` | **O(n)** | Precisa deslocar todos os elementos à direita |
| `contains(obj)` | **O(n)** | Precisa percorrer o array comparando |
| `indexOf(obj)` | **O(n)** | Precisa percorrer o array comparando |

O `add` no final é **amortizado** O(1) — termo que merece explicação. "Amortizado" significa que, na média ao longo de muitas operações, o custo é constante . Quando o array interno está cheio, a operação de redimensionamento custa O(n). Mas isso acontece com pouca frequência. Se você adiciona 1 milhão de elementos, o redimensionamento ocorre talvez 20 vezes. O custo total ainda é O(n), diluído entre as operações baratas.

---

### 4. O mecanismo de crescimento

Quando `add()` é chamado e `size` já é igual a `elementData.length`, o array está lotado. O `ArrayList` então :

1. Calcula a **nova capacidade**: `novaCapacidade = capacidadeAntiga + (capacidadeAntiga >> 1)`. Isso equivale a multiplicar por 1,5 — um array de 10 posições vira 15; de 100 vira 150.
2. Cria um novo array com essa capacidade.
3. Copia todos os elementos do array antigo para o novo (`Arrays.copyOf`).
4. Descarta o array antigo.

**Exemplo concreto** do código-fonte da OpenJDK :

```java
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);  // 1,5x
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

A cópia do array é uma operação cara — `System.arraycopy` é nativa e otimizada, mas ainda percorre todos os elementos. É por isso que, se você sabe quantos elementos aproximadamente vai armazenar, **informar a capacidade inicial no construtor** evita múltiplos redimensionamentos :

```java
List<String> nomes = new ArrayList<>(1000);  // já reserva espaço para 1000 elementos
```

---

### 5. Capacidade inicial padrão

O construtor sem argumentos `new ArrayList()` não cria um array de 10 posições imediatamente. Desde o JDK 8, ele usa um array vazio compartilhado (`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`). Somente quando o primeiro elemento é adicionado, o array é criado — com capacidade 10 .

```java
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;  // array vazio, não ocupa memória
}
```

Na primeira chamada a `add()`, o método `ensureCapacityInternal` detecta que o array é o vazio compartilhado e expande para `DEFAULT_CAPACITY` (10) .

---

### 6. Comparação com LinkedList

`ArrayList` e `LinkedList` implementam a mesma interface `List`, mas têm estruturas internas radicalmente diferentes :

| Característica | ArrayList | LinkedList |
|----------------|-----------|------------|
| **Estrutura** | Array contíguo | Lista duplamente encadeada |
| **Acesso por índice** | O(1) — salto direto | O(n) — percorre nós |
| **Inserção no final** | O(1) amortizado | O(1) |
| **Inserção no meio** | O(n) — desloca elementos | O(n) — percorre até a posição, depois O(1) |
| **Remoção no meio** | O(n) — desloca elementos | O(n) — percorre até a posição, depois O(1) |
| **Uso de memória** | Só armazena os elementos | Cada nó tem referências `prev` e `next` extras |

**Na prática, ArrayList é a escolha padrão.** LinkedList raramente vence em cenários reais, porque:

- O custo de percorrer a lista encadeada (O(n) para acessar o meio) anula o ganho O(1) na inserção/remoção.
- A dispersão dos nós na memória destrói a localidade de cache. O array contíguo do ArrayList é extremamente amigável ao cache da CPU .

---

### 7. CPU Cache: por que ArrayList é tão rápido na prática

Esse é um ponto que a maioria dos materiais ignora, mas é crucial para entender a performance real.

A CPU não acessa a RAM diretamente para cada operação. Ela tem caches (L1, L2, L3) muito mais rápidos que a memória principal. Quando a CPU lê um dado, ela traz uma **linha de cache inteira** (tipicamente 64 bytes) — apostando que os próximos acessos serão em posições vizinhas .

**ArrayList:** os elementos estão lado a lado no array contíguo. Quando você percorre a lista, a CPU carrega blocos inteiros de elementos no cache de uma vez. Acesso sequencial é praticamente na velocidade do cache L1/L2 .

**LinkedList:** cada nó está em um lugar aleatório da Heap. A cada salto para o próximo nó, a CPU provavelmente precisa buscar uma linha de cache nova — o cache anterior é inútil. Isso se chama **cache miss**, e cada um custa dezenas ou centenas de ciclos de clock .

Um benchmark citado em materiais técnicos aponta que o acesso sequencial em ArrayList pode ser até **33 vezes mais rápido** que em LinkedList devido ao efeito do cache — mesmo que ambos sejam tecnicamente O(n) para percorrer .

---

### 8. Thread safety: ArrayList não é sincronizado

`ArrayList` não tem nenhum mecanismo interno de sincronização. Se duas threads modificam a mesma lista simultaneamente, o resultado é imprevisível — `ConcurrentModificationException`, índices corrompidos, ou perda silenciosa de dados .

Se você precisa de uma lista thread-safe, as opções são:

- **`Collections.synchronizedList(new ArrayList<>())`** — envolve a lista em um wrapper que sincroniza todos os métodos. Simples, mas a sincronização é grossa (um lock por operação).
- **`CopyOnWriteArrayList`** — cada modificação cria uma cópia do array. Ideal quando leituras são muito mais frequentes que escritas.
- **`Vector`** — a classe legada que é essencialmente um `ArrayList` sincronizado. Praticamente não se usa mais; `Collections.synchronizedList` é a alternativa moderna.

O iterador de `ArrayList` é **fail-fast**: se a lista for modificada estruturalmente por qualquer meio que não seja o próprio iterador, ele lança `ConcurrentModificationException` imediatamente, em vez de continuar com comportamento imprevisível .

---

### 9. Dicas práticas

**Defina capacidade inicial quando souber o tamanho aproximado.**

```java
// Ruim: múltiplos redimensionamentos silenciosos
List<String> lista = new ArrayList<>();
for (int i = 0; i < 100_000; i++) {
    lista.add("item " + i);
}

// Bom: um único array, zero redimensionamentos
List<String> lista = new ArrayList<>(100_000);
for (int i = 0; i < 100_000; i++) {
    lista.add("item " + i);
}
```

A diferença de performance é mensurável — o segundo caso elimina cerca de 20 operações de `Arrays.copyOf` em um array grande.

**Use `addAll` em vez de `add` em loop para coleções.**

```java
// Ruim: cada add pode disparar ensureCapacity
for (String s : outraLista) {
    lista.add(s);
}

// Bom: um único ensureCapacity + uma única cópia de array
lista.addAll(outraLista);
```

**Prefira ArrayList a LinkedList como padrão.**

A menos que você tenha medições mostrando que LinkedList vence em seu cenário específico (o que é raro), ArrayList é a escolha correta. A simplicidade do array contíguo, a localidade de cache e o acesso O(1) por índice o tornam superior para a maioria esmagadora dos casos de uso.

**Use `trimToSize()` para liberar espaço não utilizado.**

Se uma lista cresceu temporariamente e depois encolheu, `trimToSize()` reduz o array interno ao tamanho exato de `size`, liberando a memória excedente .

---

### 10. Resumo em uma frase

`ArrayList` é um array que finge ser flexível: acesso instantâneo por índice, crescimento preguiçoso em lotes de 1,5x, e uma simpatia pelo cache da CPU que o torna, na prática, a implementação de lista mais eficiente para quase todos os cenários — desde que você dimensione a capacidade inicial com juízo e mantenha distância de inserções no meio em laços quentes.

---

### Fontes

1. OpenJDK, "ArrayList.java source code" — implementação de referência com campos, construtores e mecanismo de crescimento .
2. Oracle, "Java Platform SE 8 – ArrayList documentation" — especificação oficial com complexidade de operações e política de crescimento .
3. 阿里云开发者社区, "从底层数据结构和CPU缓存两方面剖析LinkedList的查询效率为什么比ArrayList低" — análise do impacto do CPU cache na diferença de performance entre ArrayList e LinkedList .
4. GitHub/zhpanvip, "ArrayList与LinkedList" — comparação de complexidade, mecanismo de扩容 (crescimento) e cenários de uso .
5. StackOverflow via Kiwix, "ArrayList add() and remove() performance" — explicação do conceito de custo amortizado constante .
6. Oracle, "Java Platform SE 8 – ArrayList (Japanese)" — documentação adicional com detalhes de construtores e métodos .