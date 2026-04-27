
## List: a sequência ordenada que mantém a ordem de inserção

### 1. O que é List

`List` é uma interface do Java Collections Framework que representa uma **coleção ordenada** de elementos. "Ordenada" aqui não significa que os elementos estão em ordem alfabética ou numérica — significa que eles mantêm a **ordem de inserção** e cada um tem uma **posição indexada** (começando em zero), como um array com esteroides .

Diferentemente de um array, uma `List` redimensiona automaticamente. Diferentemente de um `Set`, permite elementos duplicados e múltiplos valores `null` .

A interface `List` estende `Collection` (que por sua vez estende `Iterable`), então tudo que uma coleção sabe fazer — adicionar, remover, verificar se contém, esvaziar, iterar — uma lista também sabe .

---

### 2. Características essenciais

**Ordem de inserção preservada.** O primeiro elemento que você adiciona fica na posição 0, o segundo na posição 1, e assim por diante. Quando você itera, os elementos aparecem exatamente na ordem em que foram inseridos .

**Acesso por índice.** Você acessa, modifica e remove elementos pela sua posição numérica: `get(3)`, `set(2, elemento)`, `remove(1)`. Isso diferencia `List` de `Set` e `Queue`, que não oferecem acesso indexado .

**Permite duplicatas.** `list.add("A"); list.add("A");` resulta em dois elementos `"A"`. O critério é `equals()` — se dois objetos são iguais segundo `equals()`, ambos podem coexistir na lista .

**Permite múltiplos null.** Diferentemente de algumas implementações de `Set` e `Map` que restringem null, a maioria das implementações de `List` aceita múltiplos elementos `null` .

---

### 3. Métodos essenciais

A interface `List` define operações que vão além do que `Collection` oferece :

| Operação | Método | Descrição |
|----------|--------|-----------|
| **Adicionar** | `add(E e)` | Adiciona ao final da lista |
| | `add(int index, E e)` | Insere na posição especificada |
| **Acessar** | `get(int index)` | Retorna o elemento na posição |
| **Modificar** | `set(int index, E e)` | Substitui o elemento na posição |
| **Remover** | `remove(int index)` | Remove o elemento na posição |
| | `remove(Object o)` | Remove a primeira ocorrência do objeto |
| **Buscar** | `indexOf(Object o)` | Retorna o índice da primeira ocorrência |
| | `lastIndexOf(Object o)` | Retorna o índice da última ocorrência |
| | `contains(Object o)` | Verifica se o elemento existe |
| **Extrair** | `subList(int from, int to)` | Retorna uma visão de um trecho da lista |
| **Ordenar** | `sort(Comparator)` | Ordena a lista com o comparador fornecido |
| **Tamanho** | `size()` | Retorna o número de elementos |
| **Esvaziar** | `clear()` | Remove todos os elementos |

Exemplo prático:

```java
List<String> nomes = new ArrayList<>();
nomes.add("Ana");         // ["Ana"]
nomes.add("Carlos");      // ["Ana", "Carlos"]
nomes.add(0, "Beatriz");  // ["Beatriz", "Ana", "Carlos"]

String primeiro = nomes.get(0);       // "Beatriz"
nomes.set(1, "Amanda");              // ["Beatriz", "Amanda", "Carlos"]

boolean temAna = nomes.contains("Ana");  // false (foi substituída)
int pos = nomes.indexOf("Carlos");       // 2

nomes.remove(0);                       // ["Amanda", "Carlos"]
System.out.println(nomes.size());      // 2
```

---

### 4. Principais implementações

A plataforma Java oferece várias implementações da interface `List`, cada uma otimizada para um cenário diferente :

**ArrayList** — A implementação padrão. Usa um array internamente que redimensiona conforme necessário. Oferece acesso posicional em tempo constante O(1). É a escolha certa para a maioria dos casos: iteração rápida, busca por índice instantânea, excelente localidade de cache. A inserção e remoção no meio da lista custam O(n) porque exigem deslocar elementos .

**LinkedList** — Implementada como uma lista duplamente encadeada. Inserir e remover no início ou no fim custa O(1). Mas acessar por índice custa O(n) — percorrer a lista do início até a posição. Também implementa a interface `Deque`, podendo funcionar como fila ou pilha. Raramente é a melhor escolha na prática, porque o custo de percorrer os nós domina a maioria dos cenários. Só use se tiver medições que justifiquem .

**Vector** — Essencialmente um `ArrayList` sincronizado. Todos os métodos têm `synchronized`, tornando-o thread-safe. O preço é performance: mesmo em cenários single-thread, paga-se o overhead da sincronização. Praticamente obsoleto — prefira `Collections.synchronizedList(new ArrayList<>())` ou `CopyOnWriteArrayList` se precisar de thread safety .

**Stack** — Subclasse de `Vector` que adiciona operações LIFO (Last-In, First-Out): `push()`, `pop()`, `peek()`. Também obsoleta — a documentação oficial recomenda usar `Deque` (especificamente `ArrayDeque`) em seu lugar .

---

### 5. ArrayList vs. LinkedList: o trade-off

A decisão entre as duas implementações principais se resume ao padrão de acesso :

| Operação | ArrayList | LinkedList |
|----------|-----------|------------|
| `get(index)` | **O(1)** — acesso direto ao array | O(n) — percorre a lista |
| `add(E e)` no final | O(1) amortizado | O(1) |
| `add(0, E e)` no início | O(n) — desloca todos | O(1) |
| `remove(index)` no meio | O(n) — desloca todos | O(n) — percorre até a posição, depois O(1) |
| Iteração completa | **Rápida** (cache-friendly) | Mais lenta (cache-unfriendly) |
| Uso de memória | Só os elementos + overhead do array | Elementos + 2 ponteiros por nó |

**Regra prática:** comece sempre com `ArrayList`. Se você achar que precisa de `LinkedList`, meça com dados reais — na maioria esmagadora dos casos, `ArrayList` vence até em cenários de inserção no meio para listas de tamanho moderado, porque o `System.arraycopy` nativo é extremamente rápido e a localidade de cache do array é imbatível.

---

### 6. ListIterator: mais poder que o Iterator comum

Além do `Iterator` herdado de `Collection`, `List` oferece um **`ListIterator`** — um iterador bidirecional que permite percorrer a lista nos dois sentidos e modificar elementos durante a iteração :

```java
List<String> nomes = new ArrayList<>(List.of("Ana", "Carlos", "Beatriz"));
ListIterator<String> it = nomes.listIterator();

// Percorrer para frente
while (it.hasNext()) {
    String nome = it.next();
    if (nome.equals("Carlos")) {
        it.set("Carlos Silva");  // Substitui o último elemento retornado
    }
}

// Percorrer para trás
while (it.hasPrevious()) {
    System.out.println(it.previous());  // Beatriz, Carlos Silva, Ana
}
```

**Métodos exclusivos do `ListIterator`:**

| Método | Descrição |
|--------|-----------|
| `hasPrevious()` | Existe elemento antes do cursor? |
| `previous()` | Retorna o elemento anterior e retrocede o cursor |
| `nextIndex()` | Índice do próximo elemento |
| `previousIndex()` | Índice do elemento anterior |
| `set(E e)` | Substitui o último elemento retornado por `next()` ou `previous()` |
| `add(E e)` | Insere um elemento na posição atual do cursor |

---

### 7. Listas imutáveis: `List.of()` e `List.copyOf()`

Desde o Java 9, os métodos de fábrica estáticos `List.of()` criam listas imutáveis — não podem ser modificadas depois de criadas :

```java
List<String> frutas = List.of("Maçã", "Banana", "Laranja");
// frutas.add("Uva");   // UnsupportedOperationException
// frutas.set(0, "Pera"); // UnsupportedOperationException
// frutas.remove(0);      // UnsupportedOperationException
```

**Características das listas imutáveis:**
- Não aceitam `null` — `List.of("A", null)` lança `NullPointerException`.
- Ocupam menos memória (implementação interna otimizada).
- São thread-safe por natureza (ninguém pode modificá-las).
- Ideais para constantes, configurações e retorno de métodos que não devem ter o resultado alterado.

`List.copyOf(collection)` cria uma cópia imutável de uma coleção existente .

---

### 8. List vs. Set vs. Map: a tabela da decisão

| Característica | List | Set | Map |
|---------------|------|-----|-----|
| **Duplicatas** | Permite | Não permite | Chaves únicas, valores podem repetir |
| **Null** | Múltiplos (na maioria) | No máximo um | Até uma chave null, múltiplos valores null |
| **Ordem** | Ordem de inserção | Depende (HashSet não garante, TreeSet ordena) | Depende (HashMap não garante, TreeMap ordena) |
| **Acesso** | Por índice (posição) | Sem índice, acesso por iteração ou `contains` | Por chave |
| **Uso típico** | Lista de itens em ordem, logs, filas | Valores únicos, filtro de duplicatas | Dicionário, cache, consulta por chave |

---

### 9. Boas práticas

**Declare como `List`, instancie como `ArrayList`:**

```java
List<String> nomes = new ArrayList<>();  // Programe para a interface
```

Isso permite trocar a implementação depois (ex: para `LinkedList` ou `CopyOnWriteArrayList`) sem alterar o resto do código.

**Dê capacidade inicial ao `ArrayList` quando souber o tamanho aproximado.** Evita múltiplos redimensionamentos:

```java
List<String> linhas = new ArrayList<>(10000);
```

**Use `List.of()` para listas pequenas e imutáveis.** Mais limpo e seguro que criar um `ArrayList` para três constantes.

**Prefira for-each ou Stream a `for` com índice**, a menos que o índice seja realmente necessário:

```java
for (String nome : nomes) { ... }
nomes.forEach(System.out::println);
```

**Não modifique a lista dentro de um for-each.** Lança `ConcurrentModificationException`. Use `Iterator.remove()` ou `removeIf`:

```java
nomes.removeIf(n -> n.startsWith("A"));
```

---

### 10. Resumo em uma frase

`List` é a coleção indexada que preserva a ordem de inserção, aceita duplicatas e oferece acesso posicional imediato com `ArrayList` como implementação padrão — use-a sempre que a ordem dos elementos importar e você precisar alcançar qualquer posição em tempo constante.

---

### Fontes

1. Oracle Help Center, "Using JavaFX Collections" — documentação oficial com definição de List, exemplos de uso e métodos como `add`, `set`, `contains`, `subList` e `clear` 
2. Android Developers, "List API Reference" — definição da interface `List`, subtipos conhecidos (`ArrayList`, `LinkedList`, `Vector`, `Stack`) e características de coleção ordenada 
3. Oracle, "Java Platform SE 8 – List Interface" — documentação canônica da interface `List`, comportamento de duplicatas, acesso posicional e `ListIterator` 
4. Oracle, "List Implementations" — documentação oficial sobre `ArrayList`, `LinkedList`, `Vector` e `CopyOnWriteArrayList`, com trade-offs e cenários de uso 
5. Old Dominion University, "java.util.List" — material didático sobre a interface `List`, `ListIterator`, métodos de fábrica `of()` e `copyOf()` 
6. Oracle, "Java Platform SE 11 – List Interface" — documentação atualizada com `List.of()` e `List.copyOf()` para listas imutáveis 
7. phoenixNAP, "Java List Interface: Definition, Usage, Examples" — tutorial com operações básicas e tabela comparativa entre `ArrayList`, `LinkedList` e `Vector` 