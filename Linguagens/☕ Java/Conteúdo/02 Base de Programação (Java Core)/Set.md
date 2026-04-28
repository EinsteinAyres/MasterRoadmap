## Set: a coleção que recusa duplicatas

### 1. O que é Set

`Set` é uma interface do Java Collections Framework que representa uma **coleção que não permite elementos duplicados**. Ela estende `Collection` (e portanto `Iterable`), mas redefine o contrato: se você tentar adicionar um elemento que já existe (segundo `equals()`), a operação é simplesmente ignorada.

É o tipo de coleção que você usa quando a pergunta não é "qual a posição disso?" ou "qual a chave para achar aquilo?", mas sim "este elemento pertence a este grupo?".

Diferentemente de `List`, um `Set` não tem índice posicional — você não acessa elementos por `get(0)`, porque a posição física não faz parte do contrato. A ordem dos elementos, se existir, depende da implementação específica.

---

### 2. Características essenciais

**Não permite duplicatas.** Esta é a razão de ser do `Set`. Se você adicionar um elemento que já está presente (segundo `equals()`), o método `add()` retorna `false` e o conjunto permanece inalterado:

```java
Set<String> nomes = new HashSet<>();
nomes.add("Ana");     // true
nomes.add("Carlos");  // true
nomes.add("Ana");     // false — já existe
System.out.println(nomes.size());  // 2
```

**No máximo um elemento null.** A maioria das implementações permite um elemento `null`. `HashSet` e `LinkedHashSet` permitem; `TreeSet` com ordenação natural lança `NullPointerException` se você tentar adicionar `null`.

**Não tem acesso posicional.** Não existe `get(0)`, `indexOf()`, ou qualquer método que dependa de posição. A iteração é a forma principal de percorrer os elementos.

**Igualdade entre Sets.** Dois `Set` são considerados iguais se contêm os mesmos elementos, independentemente da ordem (para implementações onde a ordem existe) e independentemente da implementação concreta. Um `HashSet` com "A" e "B" é `equals()` a um `TreeSet` com "A" e "B".

---

### 3. Métodos herdados de Collection

`Set` herda todos os métodos de `Collection`, mas com contratos ajustados para refletir a proibição de duplicatas:

| Método | Descrição |
|--------|-----------|
| `add(E e)` | Adiciona o elemento se ele não existir. Retorna `true` se foi adicionado, `false` se já existia |
| `remove(Object o)` | Remove o elemento se ele existir |
| `contains(Object o)` | Verifica se o elemento está no conjunto |
| `size()` | Retorna o número de elementos |
| `isEmpty()` | Verifica se o conjunto está vazio |
| `clear()` | Remove todos os elementos |
| `iterator()` | Retorna um iterador sobre os elementos |
| `addAll(Collection c)` | Adiciona todos os elementos da coleção, ignorando duplicatas |
| `removeAll(Collection c)` | Remove todos os elementos que estão na coleção fornecida |
| `retainAll(Collection c)` | Remove todos os elementos que NÃO estão na coleção fornecida (interseção) |
| `containsAll(Collection c)` | Verifica se todos os elementos da coleção estão no conjunto |

---

### 4. Principais implementações

A plataforma Java oferece três implementações principais, cada uma com características radicalmente diferentes de ordenação e performance:

**HashSet** — A implementação padrão. Usa uma tabela de hash internamente (na verdade, um `HashMap` onde os elementos são as chaves e os valores são um dummy). Não garante **nenhuma ordem** de iteração — a ordem pode mudar se o conjunto for redimensionado. Oferece O(1) em média para `add`, `remove` e `contains`.

```java
Set<String> hashSet = new HashSet<>();
hashSet.add("Z");
hashSet.add("A");
hashSet.add("M");
System.out.println(hashSet);  // Ordem imprevisível, ex: [A, M, Z] ou [Z, A, M]
```

**LinkedHashSet** — Herda de `HashSet`, mas usa uma lista duplamente encadeada que percorre os elementos na **ordem de inserção**. Mantém a performance O(1) do `HashSet`, com um leve custo adicional de memória pelos ponteiros da lista.

```java
Set<String> linkedSet = new LinkedHashSet<>();
linkedSet.add("Z");
linkedSet.add("A");
linkedSet.add("M");
System.out.println(linkedSet);  // [Z, A, M] — exatamente a ordem de inserção
```

**TreeSet** — Implementa `NavigableSet` e `SortedSet`. Usa uma árvore Rubro-Negra (Red-Black Tree) internamente. Mantém os elementos **ordenados** segundo sua ordem natural (`Comparable`) ou um `Comparator` fornecido. As operações básicas custam O(log n).

```java
Set<String> treeSet = new TreeSet<>();
treeSet.add("Z");
treeSet.add("A");
treeSet.add("M");
System.out.println(treeSet);  // [A, M, Z] — ordem alfabética
```

---

### 5. HashSet por dentro

`HashSet` delega a um `HashMap<E, Object>` interno. Cada elemento do Set é uma chave do Map; o valor associado é um objeto dummy compartilhado (`PRESENT`).

**Contrato com `hashCode()` e `equals()`:** o funcionamento correto do `HashSet` depende de você implementar corretamente `hashCode()` e `equals()` nos objetos que armazena. Se dois objetos são iguais por `equals()`, eles **devem** ter o mesmo `hashCode()`. Se tiverem `hashCode()` diferentes, o `HashSet` os tratará como elementos distintos mesmo que `equals()` retorne `true`.

```
Funcionamento do add():
1. Calcula o hash do elemento
2. Vai para o bucket correspondente
3. Se o bucket está vazio → insere
4. Se há elementos no bucket, compara com equals()
5. Se não encontrou igual → insere
6. Se encontrou igual → não faz nada, retorna false
```

**Performance:**
- `add`, `remove`, `contains`: O(1) em média, O(n) no pior caso (muitas colisões de hash).
- O fator de carga padrão é 0.75. Quando o Set atinge 75% da capacidade, ele redimensiona (o que custa O(n) e pode causar pausas).

---

### 6. LinkedHashSet: ordem de inserção garantida

`LinkedHashSet` estende `HashSet` adicionando uma lista duplamente encadeada que conecta os elementos na ordem em que foram inseridos.

**Iteração:** percorrer um `LinkedHashSet` é mais rápido que percorrer um `HashSet` comum — a lista encadeada permite percorrer apenas os elementos efetivamente presentes, sem visitar buckets vazios.

**Custo de memória:** maior que `HashSet` — cada elemento ganha dois ponteiros adicionais (`before` e `after` na lista encadeada).

**Uso típico:** quando você precisa da garantia de unicidade do `Set` mas também precisa preservar a ordem de inserção (como uma lista sem duplicatas). Comum em caches LRU, histórico de navegação, registro de eventos únicos.

---

### 7. TreeSet: ordenação garantida

`TreeSet` implementa `NavigableSet` e mantém os elementos **ordenados**, não por inserção, mas por valor. Internamente, é uma árvore Rubro-Negra — uma árvore binária de busca balanceada.

**Requisito:** os elementos devem ser comparáveis — ou implementam `Comparable<E>` (ordenação natural), ou você fornece um `Comparator<E>` ao construtor. Se nenhum dos dois existir, `add()` lança `ClassCastException`.

```java
// Ordenação natural — String implementa Comparable
Set<String> natural = new TreeSet<>();

// Ordenação customizada — Comparator explícito
Set<String> porTamanho = new TreeSet<>(
    Comparator.comparing(String::length).thenComparing(String::compareTo)
);
```

**Métodos exclusivos de `TreeSet` (via `NavigableSet`):**

| Método | Descrição |
|--------|-----------|
| `first()` | Menor elemento |
| `last()` | Maior elemento |
| `lower(E e)` | Maior elemento estritamente menor que `e` |
| `floor(E e)` | Maior elemento menor ou igual a `e` |
| `ceiling(E e)` | Menor elemento maior ou igual a `e` |
| `higher(E e)` | Menor elemento estritamente maior que `e` |
| `pollFirst()` | Remove e retorna o menor elemento |
| `pollLast()` | Remove e retorna o maior elemento |
| `subSet(from, to)` | Visão do intervalo `[from, to)` |
| `headSet(to)` | Visão dos elementos menores que `to` |
| `tailSet(from)` | Visão dos elementos maiores ou iguais a `from` |
| `descendingSet()` | Visão em ordem reversa |

**Performance:** `add`, `remove`, `contains` custam O(log n). É a escolha quando você precisa de elementos únicos e ordenados, e está disposto a pagar o custo logarítmico por isso.

---

### 8. HashSet vs. LinkedHashSet vs. TreeSet

| Característica | HashSet | LinkedHashSet | TreeSet |
|---------------|---------|---------------|---------|
| **Estrutura interna** | Tabela de hash (HashMap) | Tabela de hash + lista encadeada | Árvore Rubro-Negra (TreeMap) |
| **Ordem** | Nenhuma garantia | Ordem de inserção | Ordenado (natural ou Comparator) |
| **Permite null** | Sim (um) | Sim (um) | Não (com ordenação natural) |
| **add / remove / contains** | O(1) médio | O(1) médio | O(log n) |
| **Iteração** | O(capacidade) — visita buckets vazios | O(n) — só elementos reais | O(n) |
| **Uso de memória** | Menor | Maior (ponteiros extras) | Médio |
| **Requisitos** | `hashCode()` e `equals()` corretos | `hashCode()` e `equals()` corretos | `Comparable` ou `Comparator` |

---

### 9. Set.of() e Set.copyOf(): conjuntos imutáveis

Desde o Java 9 (aprimorado no Java 10+), `Set.of()` cria conjuntos imutáveis:

```java
Set<String> vogais = Set.of("A", "E", "I", "O", "U");
// vogais.add("Y");  // UnsupportedOperationException
// vogais.remove("A"); // UnsupportedOperationException
```

**Regras:**
- Não aceita elementos `null` — lança `NullPointerException`.
- Não aceita duplicatas — `Set.of("A", "A")` lança `IllegalArgumentException` já na criação.
- A ordem de iteração não é garantida (é deliberadamente imprevisível para desencorajar dependência de ordem).

`Set.copyOf(collection)` cria uma cópia imutável de uma coleção existente:

```java
Set<String> mutavel = new HashSet<>();
mutavel.add("X");
Set<String> imutavel = Set.copyOf(mutavel);
```

---

### 10. Quando usar cada implementação

**Use `HashSet` quando:**
- Você só precisa de unicidade.
- A ordem dos elementos não importa.
- Performance de `add`, `remove` e `contains` é a prioridade.
- É o caso mais comum — a implementação padrão.

**Use `LinkedHashSet` quando:**
- Você precisa de unicidade **e** ordem de inserção.
- A iteração precisa ser rápida e previsível.
- Exemplo: remover duplicatas de uma lista preservando a ordem original.

**Use `TreeSet` quando:**
- Você precisa de elementos únicos **e** ordenados.
- Você precisa de operações de navegação (`first`, `last`, `ceiling`, `floor`, intervalos).
- Você está disposto a pagar O(log n) em vez de O(1).

---

### 11. Cuidados e armadilhas

**Objetos mutáveis como elementos.** Se você adiciona um objeto a um `Set` e depois modifica atributos que afetam `equals()` ou `hashCode()`, o comportamento do conjunto se torna imprevisível — o objeto pode ficar "perdido" em um bucket errado e não ser encontrado por `contains()` nem removido por `remove()`.

```java
Set<List<String>> set = new HashSet<>();
List<String> lista = new ArrayList<>(List.of("A"));
set.add(lista);
lista.add("B");  // Modifica o elemento depois de inserido
System.out.println(set.contains(lista));  // Provavelmente false — o hash mudou
```

**Regra:** só use objetos imutáveis (ou que você garante que não serão modificados) como elementos de `Set`. Strings, números, records e `List.of()` são seguros.

**`null` em TreeSet.** Se você tentar adicionar `null` a um `TreeSet` que usa ordenação natural, receberá `NullPointerException` em tempo de execução.

**Performance com `hashCode()` ruim.** Se todos os elementos retornam o mesmo `hashCode()`, o `HashSet` degenera para uma lista encadeada. As operações O(1) viram O(n). Implemente `hashCode()` corretamente.

---

### 12. Resumo em uma frase

`Set` é a coleção que responde "pertence ou não pertence?" — use `HashSet` para unicidade pura e rápida, `LinkedHashSet` para unicidade com ordem de inserção, e `TreeSet` quando precisar que os elementos estejam sempre ordenados e navegáveis por intervalo.

---

### Fontes

1. Oracle, "The Set Interface" — documentação oficial com definição, implementações (`HashSet`, `TreeSet`, `LinkedHashSet`) e operações básicas 
2. Dev.java, "Set Interface" — guia oficial sobre a interface Set, métodos herdados e características de cada implementação 
3. Baeldung, "Guide to Java Set" — tutorial abrangente com exemplos de uso, implementações, Set.of() e armadilhas comuns 
4. GeeksforGeeks, "Set in Java" — definição da interface, hierarquia, métodos e exemplos práticos 
5. CodeGym, "Conjunto em Java" — material didático sobre HashSet, LinkedHashSet, TreeSet e comparações de performance 
6. 廖雪峰的官方网站, "Java Set" — explicação das implementações com exemplos de ordenação e uso de equals/hashCode 
7. Android Developers, "Set API Reference" — definição da interface Set e subtipos conhecidos na plataforma Java