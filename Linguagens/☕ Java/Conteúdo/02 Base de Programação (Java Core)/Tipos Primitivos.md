## Tipos primitivos: os tijolos da linguagem

### 1. O que são

Os oito tipos primitivos do Java são os únicos elementos da linguagem que não são objetos. Eles armazenam valores brutos — números, caracteres, booleanos — diretamente na memória, sem indireção, sem cabeçalho de objeto, sem referência.

Essa é a distinção mais fundamental do Java: tudo é objeto, exceto os primitivos. Eles existem para que operações matemáticas e lógicas não paguem o preço da orientação a objetos. São o que permite que Java seja rápido quando precisa.

---

### 2. A tabela completa

| Tipo | Armazena | Tamanho | Faixa de valores | Valor padrão |
|------|----------|---------|------------------|--------------|
| `byte` | Inteiros | 8 bits | -128 a 127 | 0 |
| `short` | Inteiros | 16 bits | -32.768 a 32.767 | 0 |
| `int` | Inteiros | 32 bits | -2³¹ a 2³¹-1 (±2,14 bilhões) | 0 |
| `long` | Inteiros | 64 bits | -2⁶³ a 2⁶³-1 (±9,22 quintilhões) | 0L |
| `float` | Decimais | 32 bits | ±1,4e-45 a ±3,4e+38 | 0.0f |
| `double` | Decimais | 64 bits | ±4,9e-324 a ±1,8e+308 | 0.0d |
| `char` | Caractere | 16 bits | 0 a 65.535 (Unicode) | '\u0000' |
| `boolean` | Booleano | (depende da JVM) | `true` / `false` | `false` |

Cada tipo tem tamanho fixo — o mesmo em qualquer plataforma, qualquer sistema operacional, qualquer hardware. Essa é uma diferença crucial de C e C++, onde `int` pode ter 16 ou 32 bits dependendo do compilador e da arquitetura.

---

### 3. Onde os primitivos vivem: Stack e Heap

A localização do primitivo depende do contexto:

**Variável local (dentro de método):** o valor vive diretamente na Stack, no frame do método. Nenhuma indireção — o espaço alocado é o próprio valor.

```java
public void metodo() {
    int x = 42;      // '42' está na Stack, ocupando 4 bytes
    double y = 3.14; // '3.14' está na Stack, ocupando 8 bytes
}
```

**Atributo de instância:** o valor vive dentro do objeto na Heap. Cada objeto carrega consigo os bytes de seus atributos primitivos.

```java
public class Carro {
    private int ano;      // quando você faz new Carro(), 4 bytes são reservados
    private boolean ligado; // mais alguns bytes
}
```

**Array de primitivos:** os valores vivem no array, que é um objeto na Heap. Mas os valores estão contíguos — lado a lado, sem ponteiros intermediários .

```java
int[] numeros = {10, 20, 30};
// Heap: [cabeçalho|10|20|30] — valores diretos, não referências
```

---

### 4. Overflow e underflow

Primitivos inteiros têm tamanho fixo. Quando uma operação ultrapassa os limites do tipo, ocorre **overflow** ou **underflow** silencioso — o valor dá a volta, sem exceção:

```java
int max = Integer.MAX_VALUE;  // 2.147.483.647
int overflow = max + 1;       // -2.147.483.648 (deu a volta)

int min = Integer.MIN_VALUE;  // -2.147.483.648
int underflow = min - 1;      // 2.147.483.647 (deu a volta novamente)
```

Não há exceção, não há aviso. Em cálculos financeiros ou científicos, isso é um desastre silencioso. Se há risco de overflow, use `long` ou `BigInteger`. Desde o Java 8, os métodos `Math.addExact()`, `Math.subtractExact()` e `Math.multiplyExact()` lançam `ArithmeticException` em caso de overflow, oferecendo uma alternativa segura.

---

### 5. Divisão inteira: a armadilha silenciosa

Dividir dois inteiros produz um inteiro — a parte decimal é truncada, não arredondada:

```java
int resultado = 5 / 2;  // 2, não 2.5
```

Para obter resultado decimal, pelo menos um dos operandos deve ser `float` ou `double`:

```java
double resultado = 5.0 / 2;    // 2.5
double resultado = (double) 5 / 2;  // 2.5
```

Este é um dos bugs mais comuns em código iniciante e um dos mais difíceis de detectar quando acontece no meio de um cálculo complexo.

---

### 6. Ponto flutuante: precisão, não exatidão

`float` e `double` seguem o padrão IEEE 754. Representam números como frações binárias, o que significa que **não conseguem representar a maioria dos números decimais com exatidão** :

```java
double resultado = 0.1 + 0.2;  // 0.30000000000000004, não 0.3
```

Isso não é bug do Java — é uma limitação inerente da representação binária de ponto flutuante, presente em todas as linguagens que usam IEEE 754.

**Consequências práticas:**

- **Nunca use `float` ou `double` para dinheiro.** Use `BigDecimal`, `long` (armazenando centavos) ou `int` (centavos).
- **Não compare igualdade com `==`.** Use uma tolerância (epsilon):

```java
boolean iguais = Math.abs(a - b) < 0.0001;
```

- `BigDecimal` resolve a exatidão decimal ao custo de performance e verbosidade. Use quando a exatidão importar mais que a velocidade.

---

### 7. Wrappers: quando o primitivo vira objeto

Cada tipo primitivo tem uma classe wrapper correspondente:

| Primitivo | Wrapper |
|-----------|---------|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

Os wrappers existem porque genéricos (`List<int>` é inválido — precisa ser `List<Integer>`) e coleções não aceitam primitivos. Eles também oferecem métodos utilitários (`Integer.parseInt()`, `Double.isNaN()`) e a capacidade de representar `null` .

**O custo dos wrappers:**

Um `int` ocupa 4 bytes. Um `Integer` ocupa 16-24 bytes (cabeçalho do objeto + campo + alinhamento), mais o custo de indireção e de coleta de lixo .

---

### 8. Autoboxing e unboxing: a conversão automática

Desde o Java 5, a conversão entre primitivo e wrapper é automática :

```java
Integer obj = 42;       // autoboxing: int → Integer
int primitivo = obj;    // unboxing: Integer → int

List<Integer> lista = new ArrayList<>();
lista.add(10);          // autoboxing na inserção
int valor = lista.get(0); // unboxing na recuperação
```

**O perigo:** autoboxing em loops pode criar milhares de objetos desnecessários e estrangular o Garbage Collector:

```java
// Péssimo: cada iteração cria um Integer e soma com autoboxing
Integer soma = 0;
for (int i = 0; i < 1_000_000; i++) {
    soma += i;  // unbox soma, soma com i, autobox resultado → novo Integer
}

// Bom: zero objetos criados
int soma = 0;
for (int i = 0; i < 1_000_000; i++) {
    soma += i;
}
```

**NullPointerException com unboxing:**

```java
Integer valor = null;
int x = valor;  // NullPointerException — unboxing de null
```

Quando um wrapper é `null` e você tenta atribuí-lo a um primitivo ou usá-lo em operação aritmética, o unboxing falha. É um erro comum com dados vindos de banco de dados ou APIs.

---

### 9. Cache de wrappers: a surpresa do `==`

`Integer`, `Short`, `Byte`, `Character` e `Long` mantêm um cache interno para valores entre -128 e 127 :

```java
Integer a = 100;
Integer b = 100;
System.out.println(a == b);   // true — mesmo objeto do cache

Integer c = 200;
Integer d = 200;
System.out.println(c == d);   // false — fora do cache, objetos diferentes
```

Para wrappers, **sempre compare com `.equals()`** , nunca com `==`, a menos que você esteja deliberadamente verificando identidade de referência. O cache é um detalhe de implementação que pode mudar.

---

### 10. Escolhendo o tipo certo

| Cenário | Tipo recomendado | Motivo |
|---------|------------------|--------|
| Contar coisas, índices, loops | `int` | Suficiente para a maioria dos casos, performance máxima |
| IDs, timestamps, valores grandes | `long` | Maior alcance, ainda performático |
| Dinheiro, valores financeiros | `BigDecimal` ou `long` (centavos) | Exatidão decimal obrigatória |
| Notas, porcentagens, medidas científicas | `double` | Precisão suficiente, performance |
| Economia extrema de memória | `byte`, `short` | Arrays de bytes para dados binários, buffers |
| Verdadeiro/falso | `boolean` | Semântica clara |
| Dados em APIs e coleções | Wrappers | Genéricos exigem objetos |
| Cálculos massivos em loops | Primitivos | Zera a pressão sobre o GC |

**A regra de ouro:** na dúvida, use `int` para inteiros e `double` para decimais. Só escolha outro tipo quando tiver uma razão específica — economia de memória, alcance maior, exatidão decimal.

---

### 11. Resumo em uma frase

Os oito tipos primitivos são o chão de fábrica da JVM — o único lugar onde o Java toca os dados sem a cerimônia dos objetos. Usá-los bem significa código que roda mais rápido, ocupa menos memória e não abusa do Garbage Collector; ignorá-los em favor de wrappers por conveniência é como pagar todas as contas com cartão de crédito sem olhar os juros.

---

### Fontes

1. Oracle, "Java Language Specification – Chapter 4: Types, Values, and Variables" — definição canônica dos tipos primitivos, tamanhos e faixas 
2. Oracle, "Java Tutorials – Primitive Data Types" — documentação oficial com exemplos de uso e valores padrão 
3. Baeldung, "Java Primitives versus Objects" — comparação de performance, consumo de memória e impacto do autoboxing 
4. GeeksforGeeks, "Primitive Data Types in Java" — explicação de cada tipo com código de exemplo 
5. Medium/JavaJams, "Java Primitives vs. Wrappers" — análise de cenários de uso e armadilhas com `==` em wrappers 
6. Software Testing Help, "Java Primitive Data Types" — referência com valores literais, wrappers e considerações de performance