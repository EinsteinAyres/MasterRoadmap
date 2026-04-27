## Arrays: sequências contíguas de elementos

### 1. O que é um array

Um array é uma estrutura de dados que armazena uma sequência de elementos do **mesmo tipo**, em posições contíguas de memória, com **tamanho fixo** definido no momento da criação. É a estrutura mais primitiva e mais eficiente para armazenar múltiplos valores em Java.

Diferentemente de linguagens como JavaScript ou Python, onde arrays são dinâmicos e heterogêneos, o array do Java é estático e homogêneo: uma vez criado com tamanho N, terá exatamente N posições até ser destruído, e todas armazenam o mesmo tipo.

Arrays são objetos — mesmo arrays de primitivos. A referência fica na Stack (se for variável local), mas o array propriamente dito vive na Heap.

---

### 2. Criação e inicialização

Há três formas principais de criar um array:

**Declaração com tamanho fixo — elementos inicializados com valor padrão:**

```java
int[] numeros = new int[5];           // {0, 0, 0, 0, 0}
String[] nomes = new String[3];       // {null, null, null}
boolean[] flags = new boolean[2];     // {false, false}
```

**Inicialização inline com valores explícitos:**

```java
int[] numeros = {10, 20, 30, 40, 50};
String[] nomes = {"Ana", "Carlos", "Beatriz"};
```

**Inicialização inline com `new` (sintaxe explícita):**

```java
int[] numeros = new int[] {10, 20, 30};
// Útil quando o array é criado sem declaração de variável:
somar(new int[] {1, 2, 3});
```

**Estilos de declaração equivalentes:**

```java
int[] a;   // Estilo Java — preferido, o tipo é "array de int"
int b[];   // Estilo C — funciona, mas é menos claro
```

O estilo `int[]` é considerado mais idiomático porque mantém o tipo junto: "array de inteiros".

---

### 3. Acesso e índices

Elementos são acessados por índice, começando em **zero**. O primeiro elemento está no índice 0; o último, no índice `length - 1`:

```java
int[] numeros = {10, 20, 30, 40, 50};
System.out.println(numeros[0]);   // 10 — primeiro elemento
System.out.println(numeros[2]);   // 30 — terceiro elemento
System.out.println(numeros[4]);   // 50 — último elemento

numeros[1] = 25;  // Modifica o segundo elemento: {10, 25, 30, 40, 50}
```

**`ArrayIndexOutOfBoundsException`:** tentar acessar um índice negativo ou maior ou igual a `length` lança essa exceção em tempo de execução:

```java
int[] numeros = {10, 20, 30};
System.out.println(numeros[3]);  // Exceção — índices válidos são 0, 1, 2
System.out.println(numeros[-1]); // Exceção — índice negativo
```

O compilador não verifica isso — o erro só aparece em execução.

---

### 4. A propriedade `length`

Todo array tem o atributo `length`, que retorna o número de elementos (não a capacidade, pois o array não tem capacidade extra):

```java
int[] numeros = {10, 20, 30, 40, 50};
System.out.println(numeros.length);  // 5

// Percorrer o array usando length como limite
for (int i = 0; i < numeros.length; i++) {
    System.out.println(numeros[i]);
}
```

`length` é um atributo público final, não um método — `array.length`, não `array.length()`. Para Strings, o equivalente é `str.length()` (método); para coleções, `list.size()` (método). A diferença de sintaxe é uma idiossincrasia histórica do Java.

---

### 5. Arrays e loops

**For clássico com índice:**

```java
int[] numeros = {10, 20, 30, 40, 50};
for (int i = 0; i < numeros.length; i++) {
    System.out.println("Índice " + i + ": " + numeros[i]);
}
```

**For-each (enhanced for):** mais limpo quando você não precisa do índice:

```java
for (int n : numeros) {
    System.out.println(n);
}
```

**Arrays e Streams (Java 8+):**

```java
int[] numeros = {10, 20, 30, 40, 50};

// Somar todos
int soma = Arrays.stream(numeros).sum();

// Filtrar e transformar
int[] pares = Arrays.stream(numeros)
    .filter(n -> n % 2 == 0)
    .toArray();

// Para arrays de objetos:
String[] nomes = {"Ana", "Carlos", "Beatriz"};
List<String> filtrados = Arrays.stream(nomes)
    .filter(n -> n.startsWith("A"))
    .toList();
```

---

### 6. Arrays multidimensionais

Java não tem arrays multidimensionais verdadeiros como C. Um "array 2D" em Java é um **array de arrays** — cada linha pode ter tamanho diferente (jagged array):

```java
// Matriz 3x3
int[][] matriz = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

System.out.println(matriz[0][0]);  // 1 — primeira linha, primeira coluna
System.out.println(matriz[1][2]);  // 6 — segunda linha, terceira coluna

// Percorrer
for (int i = 0; i < matriz.length; i++) {
    for (int j = 0; j < matriz[i].length; j++) {
        System.out.print(matriz[i][j] + " ");
    }
    System.out.println();
}
```

**Jagged array:** cada linha pode ter tamanho diferente:

```java
int[][] jagged = new int[3][];
jagged[0] = new int[] {1, 2};
jagged[1] = new int[] {3, 4, 5, 6};
jagged[2] = new int[] {7};

// jagged:
// [0]: {1, 2}
// [1]: {3, 4, 5, 6}
// [2]: {7}
```

**Criação com `new`:**

```java
int[][] matriz = new int[3][4];  // 3 linhas, 4 colunas (todos 0)
int[][] jagged = new int[3][];   // 3 linhas, cada uma null até ser inicializada
jagged[0] = new int[2];
jagged[1] = new int[5];
jagged[2] = new int[1];
```

---

### 7. Arrays e a classe utilitária `java.util.Arrays`

A classe `Arrays` oferece operações essenciais:

**`toString`** — representação legível:

```java
int[] numeros = {1, 2, 3, 4, 5};
System.out.println(Arrays.toString(numeros));  // [1, 2, 3, 4, 5]
System.out.println(numeros);  // [I@1b6d3586 — inútil
```

**`sort`** — ordenação in-place (modifica o array original):

```java
int[] numeros = {5, 2, 8, 1, 9};
Arrays.sort(numeros);  // {1, 2, 5, 8, 9}

// Ordenar parte do array:
Arrays.sort(numeros, 1, 4);  // Ordena índices 1 a 3
```

**`binarySearch`** — busca binária (array deve estar ordenado):

```java
int[] numeros = {1, 2, 5, 8, 9};
int pos = Arrays.binarySearch(numeros, 5);  // 2
int naoEncontrado = Arrays.binarySearch(numeros, 7);  // Valor negativo
```

**`equals`** — compara arrays elemento por elemento:

```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};
System.out.println(a == b);             // false — referências diferentes
System.out.println(a.equals(b));        // false — equals de Object
System.out.println(Arrays.equals(a, b)); // true — compara conteúdo!
```

**`copyOf` e `copyOfRange`** — cópia com redimensionamento:

```java
int[] original = {1, 2, 3};
int[] copia = Arrays.copyOf(original, 5);      // {1, 2, 3, 0, 0}
int[] trecho = Arrays.copyOfRange(original, 0, 2);  // {1, 2}
```

**`fill`** — preenche com um valor:

```java
int[] zeros = new int[10];
Arrays.fill(zeros, -1);  // Todos os elementos agora são -1
Arrays.fill(zeros, 3, 7, 99);  // Preenche índices 3 a 6 com 99
```

---

### 8. Arrays vs. ArrayList

| Característica | Array | ArrayList |
|---------------|-------|-----------|
| **Tamanho** | Fixo após criação | Redimensionável dinamicamente |
| **Primitivos** | Sim (`int[]`, `double[]`) | Não (usa wrappers: `List<Integer>`) |
| **Acesso por índice** | O(1) | O(1) |
| **Redimensionamento** | Manual (criar novo + copiar) | Automático |
| **Uso de memória** | Mínimo (só os dados, sem overhead) | Maior (objeto wrapper + overhead da estrutura) |
| **Métodos** | Apenas `length` e `[]`. Utilitários via `Arrays` | `add`, `remove`, `contains`, `indexOf`, etc. |
| **Performance** | Máxima (localidade de cache, sem GC por elemento) | Boa, mas com overhead de objetos e possível redimensionamento |
| **Genéricos** | Não (arrays são covariantes) | Sim (`List<String>`) |

**Regra prática:** use arrays quando o tamanho for fixo e conhecido, performance for crítica, ou você estiver lidando com muitos primitivos (economia de memória e zero GC). Use `ArrayList` na maioria dos outros casos — a flexibilidade vale o overhead mínimo.

---

### 9. Passagem de arrays para métodos

Arrays são objetos. Quando passados como argumento, a **referência** é copiada. O método pode modificar o conteúdo do array original:

```java
public static void dobrar(int[] numeros) {
    for (int i = 0; i < numeros.length; i++) {
        numeros[i] *= 2;  // Modifica o array original
    }
}

int[] valores = {1, 2, 3};
dobrar(valores);
System.out.println(Arrays.toString(valores));  // {2, 4, 6}
```

Para evitar modificações, passe uma cópia ou use `Collections.unmodifiableList` para coleções (não há equivalente nativo para arrays).

---

### 10. Arrays e memória

**Arrays de primitivos:** os valores são armazenados diretamente no array, em posições contíguas. É a estrutura com melhor localidade de cache possível em Java — percorrer um `int[]` é extremamente rápido porque a CPU carrega blocos inteiros no cache L1/L2.

**Arrays de objetos:** o array armazena **referências** (ponteiros), não os objetos em si. Os objetos referenciados podem estar espalhados pela Heap. Percorrer um array de objetos é menos eficiente que percorrer um array de primitivos porque cada acesso pode causar um cache miss — a referência está no array, mas o objeto está em outro lugar.

```java
Ponto[] pontos = new Ponto[1000];  // Array de referências — cada posição é 4 ou 8 bytes
for (int i = 0; i < 1000; i++) {
    pontos[i] = new Ponto(i, i);   // Cada Ponto é um objeto separado na Heap
}
// Percorrer 'pontos' envolve seguir cada referência para um local diferente da Heap
```

---

### 11. Limitações

**Tamanho fixo.** Uma vez criado, o array não cresce nem encolhe. Para "redimensionar", você precisa criar um novo array e copiar os elementos:

```java
int[] original = {1, 2, 3};
int[] expandido = Arrays.copyOf(original, 6);  // {1, 2, 3, 0, 0, 0}
```

`ArrayList` faz isso internamente, com uma estratégia de crescimento de 1,5x.

**Não oferece métodos de coleção.** Não há `add`, `remove`, `contains`, `indexOf` nativos. A classe `Arrays` supre parte disso, mas não resolve redimensionamento.

**Tipagem menos segura em arrays de objetos.** Arrays são covariantes: `String[]` é um subtipo de `Object[]`. Isso pode causar `ArrayStoreException` em tempo de execução:

```java
String[] strings = {"A", "B"};
Object[] objects = strings;      // Covariância — aceito em compilação
objects[0] = 42;                 // ArrayStoreException em tempo de execução
```

Coleções genéricas como `List<String>` não têm esse problema — são invariantes.

---

### 12. Boas práticas

**Use for-each para percorrer.** A menos que precise do índice, `for (int n : numeros)` é mais limpo e elimina erros de índice fora dos limites.

**Use `Arrays.toString()` para depuração.** Imprimir um array diretamente (`System.out.println(array)`) mostra um hash interno inútil. Use `Arrays.toString()` ou, para arrays multidimensionais, `Arrays.deepToString()`.

**Prefira `Arrays.equals()` para comparar conteúdo.** `a.equals(b)` em arrays não faz comparação de conteúdo. `Arrays.equals(a, b)` faz.

**Use `Arrays.copyOf()` para redimensionar.** É mais expressivo e menos propenso a erros que criar array novo e usar `System.arraycopy` manualmente, embora `System.arraycopy` seja ligeiramente mais rápido em cenários críticos.

**Declare arrays com o tipo mais abstrato possível quando conveniente.** Se o método aceita qualquer sequência, considere usar `Iterable`, `Collection` ou `Stream` em vez de `int[]`, para maior flexibilidade.

---

### 13. Resumo em uma frase

Arrays são a estrutura de dados mais primitiva e mais rápida de Java — tamanho fixo, acesso O(1), localidade de cache imbatível para primitivos — e servem de fundação para praticamente todas as coleções da linguagem, do `ArrayList` ao `HashMap`.

---

### Fontes

1. Oracle, "Java Tutorials – Arrays" — documentação oficial sobre criação, inicialização, acesso e arrays multidimensionais 
2. Oracle, "Java Language Specification — Chapter 10: Arrays" — especificação canônica de arrays, índices, covariância e exceções 
3. Baeldung, "Java Arrays Guide" — guia abrangente de criação, acesso, `Arrays` utilitária, ordenação e cópia 
4. GeeksforGeeks, "Arrays in Java" — fundamentos, inicialização, for-each, multidimensionais e passagem para métodos 
5. Dev.java, "Using Arrays" — documentação oficial com exemplos de `Arrays` utilitária, Streams e ordenação 
6. JavaRush, "Массивы в Java" — explicação prática, criação, acesso, loops e utilitários `Arrays`