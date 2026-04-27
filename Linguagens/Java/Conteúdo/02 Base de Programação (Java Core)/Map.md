
## Map: a coleção que associa chaves a valores

### 1. O que é Map

`Map` é uma interface do Java Collections Framework que representa uma **coleção de pares chave-valor**. Diferentemente de `List` (indexada por posição) e `Set` (que armazena elementos únicos sem chave explícita), um `Map` permite que você armazene um valor e o recupere usando uma chave arbitrária — não um índice numérico.

`Map` **não** estende `Collection` nem `Iterable`. É uma hierarquia separada, porque sua estrutura fundamental (pares chave-valor) é diferente da estrutura linear de uma coleção comum. Você pode, no entanto, obter visões do mapa como coleções: `keySet()` retorna as chaves como um `Set`, `values()` retorna os valores como uma `Collection`, e `entrySet()` retorna os pares como um `Set<Map.Entry<K,V>>`.

---

### 2. Características essenciais

**Chaves únicas.** Cada chave só pode aparecer uma vez no mapa. Se você chamar `put(chave, valor)` com uma chave que já existe, o valor anterior é **substituído** e o método retorna o valor antigo (ou `null` se a chave não existia ou estava mapeada para `null`).

**Valores podem repetir.** Várias chaves podem apontar para o mesmo valor. Não há restrição de unicidade nos valores.

**No máximo uma chave null.** A maioria das implementações (`HashMap`, `LinkedHashMap`) permite uma chave `null` e múltiplos valores `null`. `TreeMap` com ordenação natural não permite chave `null` (lança `NullPointerException`). `Hashtable` (legado) não permite chaves nem valores `null`.

**Ordem depende da implementação.** `HashMap` não garante ordem alguma. `LinkedHashMap` mantém ordem de inserção (ou ordem de acesso, se configurado). `TreeMap` mantém ordenação natural ou por `Comparator`.

---

### 3. Métodos essenciais

| Operação | Método | Descrição |
|----------|--------|-----------|
| **Inserir/Atualizar** | `put(K key, V value)` | Associa o valor à chave. Retorna o valor anterior ou `null` |
| | `putIfAbsent(K key, V value)` | Só insere se a chave não existir (Java 8+) |
| **Acessar** | `get(Object key)` | Retorna o valor associado ou `null` se a chave não existir |
| | `getOrDefault(Object key, V default)` | Retorna o valor ou um default se a chave não existir (Java 8+) |
| **Verificar** | `containsKey(Object key)` | Verifica se a chave existe |
| | `containsValue(Object value)` | Verifica se o valor existe (O(n)) |
| **Remover** | `remove(Object key)` | Remove a chave e retorna o valor associado |
| | `remove(Object key, Object value)` | Remove apenas se a chave estiver mapeada para o valor especificado |
| **Substituir** | `replace(K key, V value)` | Substitui o valor apenas se a chave existir |
| | `replace(K key, V old, V new)` | Substitui apenas se mapeado para `old` |
| **Tamanho** | `size()` | Número de pares chave-valor |
| | `isEmpty()` | Verifica se está vazio |
| **Visões** | `keySet()` | Retorna um `Set` com todas as chaves |
| | `values()` | Retorna uma `Collection` com todos os valores |
| | `entrySet()` | Retorna um `Set<Map.Entry<K,V>>` com todos os pares |
| **Em lote** | `putAll(Map<?,?> m)` | Copia todos os pares de outro mapa |
| | `clear()` | Remove todos os pares |

Exemplo prático:

```java
Map<String, Integer> idades = new HashMap<>();
idades.put("Ana", 30);
idades.put("Carlos", 25);
idades.put("Beatriz", 28);

idades.put("Ana", 31);                         // Substitui — retorna 30
idades.putIfAbsent("Carlos", 99);              // Não faz nada — Carlos já existe

Integer idadeAna = idades.get("Ana");           // 31
Integer idadeX = idades.getOrDefault("X", 0);  // 0 (default)

boolean temAna = idades.containsKey("Ana");     // true
boolean tem25 = idades.containsValue(25);       // true (mas é O(n) — evite em loops)

idades.remove("Beatriz");                       // Remove e retorna 28
idades.replace("Carlos", 26);                   // Substitui para 26

// Iterar sobre as chaves
for (String nome : idades.keySet()) {
    System.out.println(nome);
}

// Iterar sobre os pares
for (Map.Entry<String, Integer> entrada : idades.entrySet()) {
    System.out.println(entrada.getKey() + " tem " + entrada.getValue());
}
```

---

### 4. Principais implementações

**HashMap** — A implementação padrão. Usa uma tabela de hash internamente. Não garante **nenhuma ordem** de iteração. Oferece O(1) em média para `put`, `get`, `remove`, `containsKey`. Permite uma chave `null` e múltiplos valores `null`.

```java
Map<String, Integer> hashMap = new HashMap<>();
hashMap.put("Z", 1);
hashMap.put("A", 2);
System.out.println(hashMap);  // Ordem imprevisível
```

**LinkedHashMap** — Herda de `HashMap`, mas mantém uma lista duplamente encadeada com a **ordem de inserção** (ou ordem de acesso, se configurado com `accessOrder=true`, útil para caches LRU). Performance O(1) como `HashMap`, com leve custo extra de memória.

```java
Map<String, Integer> linkedMap = new LinkedHashMap<>();
linkedMap.put("Z", 1);
linkedMap.put("A", 2);
System.out.println(linkedMap);  // {A=1, B=2} — ordem de inserção
```

**TreeMap** — Implementa `NavigableMap` e `SortedMap`. Usa uma árvore Rubro-Negra. Mantém as chaves **ordenadas** (ordem natural ou `Comparator`). `put`, `get`, `remove` custam O(log n). Não permite chave `null` (com ordenação natural). Permite valores `null`.

```java
Map<String, Integer> treeMap = new TreeMap<>();
treeMap.put("Z", 1);
treeMap.put("A", 2);
treeMap.put("M", 3);
System.out.println(treeMap);  // {A=2, M=3, Z=1} — ordem alfabética pelas chaves
```

**Hashtable** — Implementação legada (Java 1.0), sincronizada (todos os métodos têm `synchronized`). Não permite chaves nem valores `null`. Foi substituída por `HashMap` e, para uso thread-safe, por `ConcurrentHashMap`. Não use em código novo.

---

### 5. HashMap por dentro

`HashMap` é estruturado como um array de "buckets". Cada bucket pode conter uma lista encadeada ou uma árvore (desde Java 8, quando há muitas colisões).

**Funcionamento do `put(key, value)`:**
1. Calcula o `hashCode()` da chave.
2. Aplica uma função de espalhamento para determinar o bucket.
3. Se o bucket está vazio → insere o par.
4. Se há elementos no bucket, percorre comparando as chaves com `equals()`.
5. Se encontrar chave igual → substitui o valor e retorna o antigo.
6. Se não encontrar → insere no final (lista) ou na árvore.
7. Se o fator de carga for excedido → redimensiona o array (dobra a capacidade) e re-hash todos os elementos.

**Contrato `hashCode()` e `equals()`:**
- Se `a.equals(b)`, então `a.hashCode() == b.hashCode()` (obrigatório).
- O contrário não é obrigatório: hash codes iguais não implicam objetos iguais (colisão).

Se você usar objetos mutáveis como chave e modificá-los depois da inserção, o mapa se corrompe — a chave pode ficar no bucket errado e nunca mais ser encontrada. **Use apenas objetos imutáveis como chaves** (Strings, números, records, enums).

**Desde Java 8:** quando um bucket tem muitos elementos (8+), a lista encadeada é convertida em uma árvore balanceada, reduzindo o pior caso de O(n) para O(log n). Isso mitiga ataques de colisão de hash.

---

### 6. LinkedHashMap: ordem de inserção e caches LRU

`LinkedHashMap` estende `HashMap` com uma lista duplamente encadeada que conecta todas as entradas. Isso oferece:

**Ordem de inserção (padrão):** a iteração segue a ordem em que as chaves foram adicionadas.

**Ordem de acesso (`accessOrder=true`):** cada `get` ou `put` move a entrada para o final da lista. Isso implementa naturalmente um cache LRU (Least Recently Used):

```java
// Cache LRU: remove a entrada mais antiga quando o tamanho excede 3
Map<String, String> cache = new LinkedHashMap<>(3, 0.75f, true) {
    @Override
    protected boolean removeEldestEntry(Map.Entry<String, String> eldest) {
        return size() > 3;
    }
};

cache.put("a", "1");
cache.put("b", "2");
cache.put("c", "3");
cache.get("a");        // Move "a" para o final (mais recente)
cache.put("d", "4");    // Remove "b" (menos recente), insere "d"
// cache = {c=3, a=1, d=4}
```

---

### 7. TreeMap: chaves ordenadas e navegação

`TreeMap` implementa `NavigableMap`, adicionando métodos de navegação inexistentes em `HashMap`:

| Método | Descrição |
|--------|-----------|
| `firstKey()` | Menor chave |
| `lastKey()` | Maior chave |
| `lowerKey(K key)` | Maior chave estritamente menor que `key` |
| `floorKey(K key)` | Maior chave menor ou igual a `key` |
| `ceilingKey(K key)` | Menor chave maior ou igual a `key` |
| `higherKey(K key)` | Menor chave estritamente maior que `key` |
| `firstEntry()` | Menor par chave-valor |
| `lastEntry()` | Maior par chave-valor |
| `pollFirstEntry()` | Remove e retorna o menor par |
| `pollLastEntry()` | Remove e retorna o maior par |
| `subMap(from, to)` | Visão do intervalo `[from, to)` |
| `headMap(to)` | Visão das chaves menores que `to` |
| `tailMap(from)` | Visão das chaves maiores ou iguais a `from` |
| `descendingMap()` | Visão em ordem reversa |

```java
TreeMap<Integer, String> ranking = new TreeMap<>();
ranking.put(3, "Bronze");
ranking.put(1, "Ouro");
ranking.put(2, "Prata");

System.out.println(ranking.firstKey());      // 1
System.out.println(ranking.lastEntry());     // 3=Bronze
System.out.println(ranking.ceilingKey(1));   // 1
System.out.println(ranking.higherKey(1));    // 2
System.out.println(ranking.subMap(1, 3));    // {1=Ouro, 2=Prata}
```

---

### 8. HashMap vs. LinkedHashMap vs. TreeMap

| Característica | HashMap | LinkedHashMap | TreeMap |
|---------------|---------|---------------|---------|
| **Estrutura** | Tabela de hash | Tabela de hash + lista encadeada | Árvore Rubro-Negra |
| **Ordem** | Nenhuma garantia | Ordem de inserção (ou acesso) | Ordenado pelas chaves |
| **Chave null** | Sim (uma) | Sim (uma) | Não (com ordenação natural) |
| **Valor null** | Sim | Sim | Sim |
| **put / get / remove** | O(1) médio | O(1) médio | O(log n) |
| **Iteração** | Ordem imprevisível | Ordem de inserção | Ordem das chaves |
| **Uso de memória** | Menor | Maior (ponteiros da lista) | Médio |
| **Requisitos** | `hashCode()` e `equals()` na chave | `hashCode()` e `equals()` na chave | `Comparable` ou `Comparator` na chave |

---

### 9. Métodos modernos (Java 8+)

A API de `Map` foi enriquecida com métodos que simplificam operações comuns :

```java
Map<String, Integer> mapa = new HashMap<>();

// getOrDefault — evita null
int idade = mapa.getOrDefault("Ana", 0);

// putIfAbsent — só insere se a chave não existe
mapa.putIfAbsent("Carlos", 25);

// compute — calcula novo valor a partir do existente
mapa.compute("Ana", (k, v) -> v == null ? 1 : v + 1);

// computeIfAbsent — calcula e insere apenas se a chave não existe
mapa.computeIfAbsent("Beatriz", k -> k.length());  // 7

// computeIfPresent — calcula apenas se a chave existe
mapa.computeIfPresent("Ana", (k, v) -> v + 10);

// merge — combina valores
mapa.merge("Ana", 1, Integer::sum);

// forEach — iteração concisa
mapa.forEach((chave, valor) -> System.out.println(chave + ": " + valor));

// replaceAll — transforma todos os valores
mapa.replaceAll((chave, valor) -> valor * 2);
```

---

### 10. Mapas imutáveis: `Map.of()` e `Map.copyOf()`

Desde o Java 9, `Map.of()` cria mapas imutáveis:

```java
Map<String, Integer> constantes = Map.of(
    "MAX", 100,
    "MIN", 0,
    "DEFAULT", 50
);
// constantes.put("X", 1);  // UnsupportedOperationException
```

**Limitações:**
- Não aceita chaves nem valores `null`.
- Não aceita chaves duplicadas (lança `IllegalArgumentException`).
- `Map.of()` sobrecarregado suporta até 10 pares chave-valor. Para mais, use `Map.ofEntries()` (menos conveniente, sintaxe verbosa).

`Map.copyOf(map)` cria uma cópia imutável de um mapa existente.

---

### 11. Quando usar cada implementação

**Use `HashMap` quando:**
- Você só precisa associar chaves a valores.
- A ordem não importa.
- Performance de inserção e busca é a prioridade.
- É o caso mais comum — a implementação padrão.

**Use `LinkedHashMap` quando:**
- Você precisa preservar a ordem de inserção.
- Você está implementando um cache LRU simples (`accessOrder=true`).
- A iteração precisa ser previsível.

**Use `TreeMap` quando:**
- Você precisa de chaves ordenadas.
- Você precisa de operações de navegação (`firstKey`, `lastKey`, intervalos).
- Você está disposto a pagar O(log n).

**Use `Map.of()` / `Map.copyOf()` quando:**
- O mapa é constante ou não deve ser modificado após a criação.
- Thread-safety é necessária (imutabilidade garante segurança).

---

### 12. Cuidados e armadilhas

**Objetos mutáveis como chaves.** Se você usar um `ArrayList` ou qualquer objeto mutável como chave e depois o modificar, o `hashCode()` muda e a entrada fica perdida. Use apenas objetos imutáveis: `String`, `Integer`, `LocalDate`, records, enums.

**`null` como chave em TreeMap.** `TreeMap` com ordenação natural lança `NullPointerException` ao inserir `null`. Com `Comparator` customizado, depende da implementação do comparator.

**`containsValue()` é O(n).** Verificar se um valor existe percorre todos os elementos. Não use em loops críticos — se precisar de busca por valor, considere manter um mapa reverso ou usar uma estrutura como `BiMap` (Google Guava).

**Concorrência.** `HashMap` não é thread-safe. Se múltiplas threads acessam e modificam o mapa simultaneamente, use `ConcurrentHashMap`, `Collections.synchronizedMap()`, ou `Hashtable` (apenas se estiver mantendo código legado).

---

### 13. Resumo em uma frase

`Map` é a estrutura que responde "para esta chave, qual é o valor?" — use `HashMap` para performance máxima e ordem irrelevante, `LinkedHashMap` para preservar a ordem de inserção ou implementar caches LRU, e `TreeMap` quando precisar que as chaves estejam permanentemente ordenadas e navegáveis.

---

### Fontes

1. Oracle, "The Map Interface" — documentação oficial com definição, implementações e operações básicas 
2. Dev.java, "Map Interface" — guia oficial sobre a interface Map, métodos e implementações 
3. Baeldung, "Java Map Guide" — tutorial abrangente com HashMap, LinkedHashMap, TreeMap, Hashtable e métodos Java 8 
4. GeeksforGeeks, "Map Interface in Java" — definição, hierarquia, métodos e exemplos práticos 
5. CodeGym, "Java Map Interface" — material didático com HashMap, LinkedHashMap, TreeMap e comparações 
6. 廖雪峰的官方网站, "Java Map" — explicação das implementações com exemplos de ordenação e uso de equals/hashCode 
7. Oracle, "Map (Java Platform SE 17)" — documentação canônica da interface Map com todos os métodos e contratos