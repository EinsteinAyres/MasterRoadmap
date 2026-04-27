## Streams: processando dados sem loops

### 1. O problema que Streams resolve

Antes do Java 8, processar uma lista de objetos exigia loops explícitos, variáveis temporárias e código imperativo:

```java
List<String> nomes = List.of("Ana", "Carlos", "Beatriz", "Alice");
List<String> resultado = new ArrayList<>();
for (String nome : nomes) {
    if (nome.startsWith("A")) {
        resultado.add(nome.toUpperCase());
    }
}
```

Isso funciona. Mas o código mistura **o que** você quer (filtrar, transformar) com **como** fazer (criar lista vazia, iterar, adicionar). Para operações mais complexas — agrupar, somar, particionar — a quantidade de código boilerplate explode.

**Streams** inverte essa lógica. Você declara o resultado desejado, e a API cuida de como executá-lo :

```java
List<String> resultado = nomes.stream()
    .filter(n -> n.startsWith("A"))
    .map(String::toUpperCase)
    .toList();
```

Nenhuma variável intermediária. Nenhum `new ArrayList()`. Nenhum loop visível. É o estilo **declarativo**: você diz o que quer, não como fazer .

---

### 2. A anatomia de uma Stream

Toda operação com Stream segue uma estrutura de três partes :

**Fonte → Operações intermediárias → Operação terminal**

```
list.stream()          // fonte
    .filter(...)       // intermediária
    .map(...)          // intermediária
    .collect(...)      // terminal
```

**Fonte:** de onde os dados vêm. Pode ser uma Collection (`list.stream()`), um array (`Arrays.stream()`), um gerador (`Stream.iterate()`), ou até um arquivo.

**Operações intermediárias:** transformam a Stream em outra Stream. São **lazy** — não executam nada sozinhas, apenas montam um plano de processamento que fica registrado :
- `filter` — remove elementos que não atendem a uma condição.
- `map` — transforma cada elemento em outra coisa.
- `sorted` — ordena.
- `distinct` — remove duplicados.
- `limit` — trunca nos primeiros N elementos.
- `skip` — pula os primeiros N elementos.
- `flatMap` — achata estruturas aninhadas.

**Operação terminal:** dispara a execução de todo o pipeline. Sem ela, nada acontece :
- `collect` — agrupa resultados em lista, mapa, conjunto.
- `forEach` — executa uma ação para cada elemento.
- `count` — conta elementos.
- `reduce` — combina todos os elementos em um único valor.
- `findFirst`, `findAny` — retornam um elemento.
- `anyMatch`, `allMatch`, `noneMatch` — verificam condições.

---

### 3. Lazy evaluation: nada acontece até o terminal

Este é o ponto mais mal compreendido sobre Streams. As operações intermediárias **não processam dados**. Elas constroem uma descrição do que deve ser feito. Somente quando uma operação terminal é invocada, todo o pipeline executa .

```java
var stream = lista.stream()
    .filter(x -> {
        System.out.println("Filtrando: " + x);
        return x > 5;
    })
    .map(x -> {
        System.out.println("Mapeando: " + x);
        return x * 2;
    });

// Nada foi impresso ainda. Nenhuma operação executada.

var resultado = stream.toList();  // Agora tudo executa.
```

Isso permite duas otimizações importantes:

**Short-circuiting:** se a operação terminal não precisa de todos os elementos, o processamento para antes . `findFirst()` consome apenas o primeiro elemento que passa pelos filtros, ignorando o resto. `anyMatch()` para assim que encontra uma correspondência. `limit(5)` trunca após 5 elementos processados.

**Fusão de operações:** a JVM pode combinar `filter` e `map` para percorrer a lista uma única vez, aplicando ambas as operações em cada elemento, em vez de gerar coleções intermediárias.

---

### 4. Operações comuns descomplicadas

**Filtrar e coletar:**

```java
List<Produto> caros = produtos.stream()
    .filter(p -> p.getPreco() > 100)
    .toList();
```

**Transformar (map):**

```java
List<String> nomes = produtos.stream()
    .map(Produto::getNome)
    .toList();
```

**Achatar (flatMap):** quando cada elemento gera múltiplos resultados, e você quer uma lista plana:

```java
// List<List<String>> → List<String>
List<String> todasPalavras = frases.stream()
    .flatMap(frase -> Arrays.stream(frase.split(" ")))
    .toList();
```

**Agrupar:**

```java
Map<Categoria, List<Produto>> agrupado = produtos.stream()
    .collect(Collectors.groupingBy(Produto::getCategoria));
```

**Reduzir a um único valor:**

```java
int soma = numeros.stream()
    .reduce(0, Integer::sum);  // 0 é o valor inicial, Integer::sum combina

// Ou usando IntStream para melhor performance:
int soma = numeros.stream()
    .mapToInt(Integer::intValue)
    .sum();
```

**Verificar condições:**

```java
boolean existeCaro = produtos.stream().anyMatch(p -> p.getPreco() > 1000);
boolean todosBaratos = produtos.stream().allMatch(p -> p.getPreco() < 50);
boolean nenhumNegativo = produtos.stream().noneMatch(p -> p.getPreco() < 0);
```

---

### 5. Parallel Streams: cuidado com o entusiasmo

Streams podem ser executadas em paralelo com `.parallelStream()` ou `.parallel()`. A JVM usa o **ForkJoinPool** comum para distribuir o trabalho entre múltiplos threads .

```java
int soma = lista.parallelStream()
    .mapToInt(Integer::intValue)
    .sum();
```

**O gancho:** para datasets grandes com operações **CPU-intensivas**, o ganho pode ser de 5 a 9 vezes . Para datasets pequenos ou operações triviais, o overhead de dividir o trabalho e juntar os resultados torna a versão paralela **mais lenta** que a sequencial .

**O que pode dar errado :**

- **Thread safety:** se sua operação usa estruturas não thread-safe (como adicionar a um `ArrayList` comum dentro de `forEach`), o resultado é imprevisível — dados corrompidos, elementos duplicados ou perdidos.

- **Operações stateful:** `sorted` e `distinct` em streams paralelas são caras porque exigem barreiras de sincronização. Sempre que possível, faça operações stateful na stream sequencial ou após coletar os resultados.

- **ForkJoinPool comum:** o pool padrão é compartilhado por toda a JVM. Se múltiplas streams paralelas disputam o mesmo pool, o desempenho degrada.

**Regra prática:** use parallel streams apenas quando :

- O dataset for grande (centenas de milhares de elementos ou mais).

- A operação por elemento for cara (cálculos, parsing, compressão).

- A operação for stateless e independente (cada elemento processado sem depender de outros).

- Você **medir** e confirmar que há ganho.

Para I/O (consultas a banco de dados, chamadas de rede), parallel streams não ajudam; virtual threads (Java 21+) são a ferramenta correta .

---

### 6. Streams vs. loops tradicionais

| Característica | Stream | Loop tradicional |
|---------------|--------|------------------|
| **Estilo** | Declarativo (o quê) | Imperativo (como) |
| **Código** | Conciso, encadeado | Explícito, mais linhas |
| **Paralelismo** | `.parallelStream()` (trivial) | Manual (threads, executors) |
| **Reuso** | Consumida uma vez; reusar lança exceção | Pode reexecutar o loop |
| **Depuração** | Breakpoints difíceis em lambda | Breakpoints triviais |

**Performance:** para a maioria dos casos, a diferença é desprezível. Streams podem ter um leve overhead em operações muito simples devido à infraestrutura de pipeline. Em contrapartida, para operações complexas com muitos filtros e transformações, o processamento lazy e as otimizações da JVM frequentemente tornam Streams mais rápidas que loops manuais ingênuos.

---

### 7. O que evitar

**Não reuse uma Stream.**

Uma Stream só pode ser consumida uma vez. Após uma operação terminal, ela é considerada "fechada" e qualquer tentativa de reutilizá-la lança `IllegalStateException` .

```java
var stream = lista.stream().filter(x -> x > 5);
var resultado1 = stream.toList();  // OK
var resultado2 = stream.toList();  // IllegalStateException!
```

**Não modifique a fonte durante o processamento.**

Se a coleção que originou a Stream for modificada enquanto a Stream está sendo processada, o comportamento é imprevisível .

**Não use `forEach` para acumular em variáveis externas.**

Streams foram projetadas para operações stateless e sem efeitos colaterais. Acumular resultados em uma variável externa dentro de `forEach` é um antipadrão — use `collect`:

```java
// Ruim — efeito colateral
List<String> resultado = new ArrayList<>();
stream.forEach(s -> resultado.add(s));

// Bom — coletor imutável
List<String> resultado = stream.toList();
```

**Não transforme tudo em Stream por dogma.**

Se um loop simples resolve em duas linhas e todos na equipe o entendem imediatamente, usar Stream não é obrigatório. Streams brilham em pipelines com múltiplas operações encadeadas: filtrar, transformar, agrupar, reduzir.

---

### 8. O que realmente importa

Streams não são apenas uma sintaxe alternativa para loops. São uma mudança de paradigma: de código imperativo (passo a passo) para código declarativo (intenção). Isso traz três vantagens concretas:

- **Legibilidade:** o código expressa a intenção diretamente — "filtre os que começam com A, transforme em maiúsculas, junte em lista" — sem ruído de variáveis temporárias e loops.

- **Componibilidade:** operações podem ser encadeadas em qualquer ordem, e adicionar um novo passo (mais um filtro, mais uma transformação) é trivial.

- **Paralelismo acessível:** migrar de sequencial para paralelo é tão simples quanto trocar `stream()` por `parallelStream()`, desde que você respeite as regras de operações stateless.

A contrapartida é que Streams exigem disciplina: sem reuso, sem efeitos colaterais em operações intermediárias, sem modificação da fonte. Mas, respeitadas essas regras, são a ferramenta mais expressiva do Java moderno para processamento de dados.

---

### Fontes

1. InfoWorld, "High-performance programming with Java streams" (2025) — técnicas avançadas incluindo short-circuiting, parallel streams, virtual threads e stream gatherers 

2. Oracle, "Stream (Java Platform SE 8)" — documentação oficial da interface Stream com especificação de pipeline, lazy evaluation e operações 

3. 百度开发者中心, "ParallelStream并行流性能优化与避坑指南" (2024) — guia de otimização e armadilhas comuns de parallel streams 

4. Android Developers, "Stream API reference" — definições e métodos da interface Stream 

5. Pluralsight, "Guided: Java SE Streams Methods" (2026) — estrutura de pipeline (fonte → intermediárias → terminal) e exercícios práticos 

6. CSDN/Tencent Cloud, "Java循环操作哪个快？" (2024) — benchmarks comparando performance de streams e loops tradicionais 