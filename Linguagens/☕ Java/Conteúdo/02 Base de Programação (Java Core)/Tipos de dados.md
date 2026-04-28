## Tipos de dados em Java: a taxonomia completa

### 1. A divisão fundamental

Os tipos de dados em Java se dividem em duas categorias mutuamente exclusivas: **tipos primitivos** e **tipos de referência**. A distinção não é acadêmica — ela determina onde o dado vive na memória, como é acessado, se está sujeito à coleta de lixo e qual é o custo de performance de cada operação.

```
Tipos de dados
├── Primitivos (armazenam o valor diretamente)
│   ├── Inteiros: byte, short, int, long
│   ├── Ponto flutuante: float, double
│   ├── Caractere: char
│   └── Booleano: boolean
│
└── Referência (armazenam um ponteiro para o objeto)
    ├── Classes (String, ArrayList, classes próprias)
    ├── Interfaces (List, Comparable)
    ├── Arrays (int[], String[], Object[])
    ├── Enums (enum Dia { SEGUNDA, TERÇA })
    └── Records (record Ponto(int x, int y) {})
```

---

### 2. Tipos primitivos

Os oito tipos primitivos são os únicos elementos da linguagem que **não são objetos**. Eles armazenam valores brutos diretamente na memória — na Stack (variáveis locais) ou inline dentro do objeto na Heap (atributos de instância).

#### Inteiros

| Tipo | Tamanho | Faixa | Exemplo |
|------|---------|-------|---------|
| `byte` | 8 bits | -128 a 127 | `byte b = 100;` |
| `short` | 16 bits | -32.768 a 32.767 | `short s = 30000;` |
| `int` | 32 bits | -2³¹ a 2³¹-1 (±2,14 bilhões) | `int i = 50000;` |
| `long` | 64 bits | -2⁶³ a 2⁶³-1 (±9,22 quintilhões) | `long l = 15000000000L;` |

`int` é o tipo padrão para literais inteiros. `long` exige o sufixo `L` (maiúsculo recomendado — `l` minúsculo parece `1`).

**Cuidados:**
- Overflow e underflow são silenciosos. Use `Math.addExact()` se houver risco.
- Para valores monetários, evite `float` e `double`. Use `BigDecimal` ou `long` com centavos.
- Desde o Java 7, literais numéricos podem usar underscore: `int milhao = 1_000_000;`

#### Ponto flutuante

| Tipo | Tamanho | Precisão | Sufixo |
|------|---------|----------|--------|
| `float` | 32 bits | ~7 dígitos decimais | `f` ou `F` |
| `double` | 64 bits | ~15 dígitos decimais | `d` ou `D` (opcional) |

`double` é o tipo padrão para literais decimais. `float` exige sufixo: `float preco = 19.99f;`

**A armadilha do IEEE 754:** nem todo decimal é representável exatamente em binário. `0.1 + 0.2` não é `0.3`, mas `0.30000000000000004`. Jamais use `float` ou `double` para dinheiro. Para comparações, use tolerância: `Math.abs(a - b) < 0.0001`.

#### Caractere

`char` é um tipo de 16 bits que armazena um único caractere Unicode (UTF-16). Pode representar de `'\u0000'` (0) a `'\uffff'` (65.535).

```java
char letra = 'A';
char digito = '7';
char simbolo = '@';
char unicode = '\u00A9';  // ©
```

**Cuidados:**
- `char` é essencialmente um inteiro sem sinal. `'A' + 1` resulta em `'B'` (ou `66` como `int`).
- Caracteres fora do Basic Multilingual Plane (BMP), como alguns emojis, ocupam **dois** `char` (surrogate pair) e exigem cuidado ao percorrer strings.

#### Booleano

`boolean` armazena apenas `true` ou `false`. O tamanho exato depende da JVM. É o único tipo cujo valor não pode ser convertido para inteiro (diferentemente de C, onde `0` é `false`).

---

### 3. Wrappers: a versão objeto dos primitivos

Cada tipo primitivo tem uma classe wrapper correspondente no pacote `java.lang`:

| Primitivo | Wrapper | Cache interno |
|-----------|---------|---------------|
| `byte` | `Byte` | -128 a 127 (todos) |
| `short` | `Short` | -128 a 127 |
| `int` | `Integer` | -128 a 127 (configurável) |
| `long` | `Long` | -128 a 127 |
| `float` | `Float` | Sem cache |
| `double` | `Double` | Sem cache |
| `char` | `Character` | 0 a 127 |
| `boolean` | `Boolean` | `TRUE` e `FALSE` |

```java
Integer obj = 42;           // Autoboxing — int → Integer
int primitivo = obj;        // Unboxing — Integer → int
```

**Quando usar wrappers:**
- Genéricos exigem objetos: `List<Integer>`, não `List<int>`.
- APIs que precisam de `null` para representar "ausência de valor".
- Métodos utilitários como `Integer.parseInt()`, `Double.isNaN()`.

**Custo:** um `Integer` ocupa 16-24 bytes (objeto + campo + alinhamento), contra 4 bytes do `int`. Em loops com milhões de iterações, autoboxing pode degradar performance significativamente e sobrecarregar o GC.

---

### 4. Tipos de referência

Toda variável que não é de tipo primitivo armazena uma **referência** — um ponteiro para um objeto na Heap. O valor padrão para referências não inicializadas é `null`.

#### Classes

O tipo mais comum. Definido com a palavra-chave `class`, pode conter atributos, métodos, construtores e inicializadores:

```java
public class Carro {
    private String marca;
    private int ano;
    // ...
}

Carro c = new Carro();  // 'c' é uma referência; o objeto está na Heap
```

#### Interfaces

Definem contratos que classes podem implementar. Não armazenam estado e, desde o Java 8, podem conter métodos `default` e `static`:

```java
interface Voavel {
    void voar();
}

class Passaro implements Voavel {
    public void voar() { /* ... */ }
}

Voavel v = new Passaro();  // A referência é do tipo da interface
```

#### Arrays

Estruturas que armazenam múltiplos elementos do mesmo tipo, em posições contíguas de memória. São objetos — mesmo arrays de primitivos vivem na Heap:

```java
int[] numeros = {1, 2, 3};           // Array de primitivos
String[] nomes = {"Ana", "Carlos"};  // Array de referências
Pessoa[] pessoas = new Pessoa[10];   // Cada posição é null inicialmente
```

**Arrays vs. ArrayList:**
- Arrays: tamanho fixo, podem armazenar primitivos, acesso O(1), sem overhead.
- ArrayList: tamanho redimensionável, armazena apenas objetos (usa wrappers para primitivos), métodos convenientes.

#### Enums

Um tipo especial de classe que define um conjunto fixo de constantes. São implicitamente `final` e estendem `java.lang.Enum`:

```java
public enum DiaSemana {
    SEGUNDA, TERCA, QUARTA, QUINTA, SEXTA, SABADO, DOMINGO
}

DiaSemana hoje = DiaSemana.QUARTA;
```

Enums podem ter atributos, métodos e construtores (privados):

```java
public enum Moeda {
    DOLAR(4.90, "USD"),
    EURO(5.30, "EUR"),
    REAL(1.00, "BRL");

    private final double cotacao;
    private final String codigo;

    Moeda(double cotacao, String codigo) {
        this.cotacao = cotacao;
        this.codigo = codigo;
    }

    public double converterParaReais(double valor) {
        return valor * cotacao;
    }
}
```

#### Records (Java 16+)

Tipo especial de classe imutável cujo propósito é transportar dados. O compilador gera automaticamente construtor, acessores, `equals()`, `hashCode()` e `toString()`:

```java
public record Ponto(int x, int y) {}
Ponto p = new Ponto(10, 20);
System.out.println(p.x());  // 10 — acessor, não getter
```

Características:
- Imutáveis (campos `private final`).
- Implicitamente `final` (não podem ser herdados).
- Construtor canônico com todos os campos.
- Não suportam setters (por definição).

---

### 5. A classe String

`String` merece menção separada. É um tipo de referência — uma classe — mas a linguagem lhe confere tratamento especial:

**Criação:**

```java
String a = "java";                  // Literal — vai para o String Pool
String b = new String("java");      // Novo objeto — fora do Pool (não recomendado)
```

**Imutabilidade:** uma vez criada, uma String não pode ser alterada. Métodos como `toUpperCase()`, `trim()`, `concat()` devolvem **uma nova String**, não modificam a original:

```java
String texto = "  Java  ";
String limpo = texto.trim();  // Novo objeto "Java" — 'texto' permanece inalterado
```

**Comparação:** use `.equals()` para comparar conteúdo, `==` compara identidade de referência:

```java
String a = "java";
String b = "java";
System.out.println(a == b);       // true — mesmo objeto do Pool
System.out.println(a.equals(b));  // true — mesmo conteúdo

String c = new String("java");
System.out.println(a == c);       // false — objetos diferentes
System.out.println(a.equals(c));  // true — mesmo conteúdo
```

**Concatenação em loops:** `+` dentro de loops cria múltiplos objetos intermediários. Use `StringBuilder` (não sincronizado, rápido) ou `StringBuffer` (sincronizado, legado):

```java
// Ruim — cria um novo objeto String a cada iteração
String resultado = "";
for (int i = 0; i < 1000; i++) {
    resultado += i;
}

// Bom — modifica o mesmo buffer
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
String resultado = sb.toString();
```

---

### 6. Inferência de tipo: `var` (Java 10+)

`var` permite omitir o tipo explícito quando o compilador pode inferi-lo do contexto. É **açúcar sintático** — o tipo ainda é determinado em tempo de compilação; `var` não torna Java dinamicamente tipado:

```java
var nome = "Java";                 // String
var numeros = new ArrayList<>();   // ArrayList<Object>
var mapa = Map.of("A", 1, "B", 2); // Map<String, Integer>

// Uso em loops:
for (var item : lista) {
    System.out.println(item);
}
```

**Limitações:**
- Apenas para variáveis **locais** com inicialização explícita.
- Não permitido em atributos de classe, parâmetros de método ou tipos de retorno.
- `var x = null;` é inválido — o compilador não tem informação para inferir o tipo.

---

### 7. Tipos primitivos vs. referência: a tabela da decisão

| Característica | Primitivo | Referência |
|---------------|-----------|------------|
| **Armazena** | O valor diretamente | Um ponteiro para o objeto |
| **Valor padrão** | 0, `false`, `\u0000` | `null` |
| **Onde vive** | Stack (local) ou inline na Heap (atributo) | Stack armazena a referência; objeto na Heap |
| **GC** | Não afetado | Sujeito à coleta de lixo |
| **Genéricos** | Incompatível (`List<int>` é erro) | Compatível (`List<Integer>`) |
| **Null** | Impossível | Possível (`null`) |
| **Performance** | Máxima | Indireção + GC overhead |
| **Métodos** | Não possui (uso via wrappers) | Possui |

---

### 8. Conversão entre tipos

**Widening (automático):** de tipo menor para maior — sempre seguro, o compilador faz sozinho:

```
byte → short → int → long → float → double
```

```java
int i = 100;
long l = i;      // automático — int cabe em long
double d = l;    // automático — long cabe em double (pode perder precisão)
```

**Narrowing (explícito):** de tipo maior para menor — requer cast. Pode perder dados:

```java
double d = 9.99;
int i = (int) d;  // 9 — truncado, não arredondado

long l = 300L;
byte b = (byte) l;  // 44 — overflow silencioso
```

**Boxing (automático):** primitivo → wrapper:

```java
Integer obj = 42;     // autoboxing
```

**Unboxing (automático):** wrapper → primitivo:

```java
int valor = obj;      // unboxing — NullPointerException se obj for null
```

---

### 9. Resumo em uma frase

Java divide o mundo dos dados em duas categorias: primitivos — rápidos, diretos, sem frescura — e referências — flexíveis, orientadas a objeto, com todos os benefícios e custos do GC. Saber escolher entre um `int` e um `Integer`, entre um array e um `ArrayList`, entre um `double` e um `BigDecimal` não é micro-otimização: é o que separa código que funciona no laptop de código que funciona em produção.

---

### Fontes

1. Oracle, "Java Language Specification — Chapter 4: Types, Values, and Variables" — especificação canônica dos tipos de dados, primitivos e de referência 
2. Oracle, "Java Tutorials – Primitive Data Types" — documentação oficial com faixas de valores, literais e sufixos 
3. Baeldung, "Java Data Types" — visão geral de primitivos, wrappers, strings, arrays, enums e records 
4. GeeksforGeeks, "Data Types in Java" — taxonomia completa com exemplos, tamanhos e valores padrão 
5. Dev.java, "Primitive Data Types" — documentação oficial sobre tipos primitivos, literais e conversões 
6. Dev.java, "Using Records" — documentação oficial sobre records, construtores e imutabilidade