## Condicionais e loops: controlando o fluxo de execução

### 1. Por que existem

Um programa que executa sempre as mesmas instruções, na mesma ordem, independentemente dos dados, é uma calculadora de mesa. Tudo o que torna o software útil — decisões, repetições, respostas diferentes para entradas diferentes — depende de duas capacidades: **desviar** o fluxo com base em condições e **repetir** operações sem duplicar código.

Java oferece essas capacidades com um conjunto de estruturas de controle de fluxo que, embora tenham raízes em C, evoluíram consideravelmente nas versões recentes da linguagem.

---

### 2. `if` e `else`: a decisão binária

A estrutura condicional mais fundamental avalia uma expressão booleana e executa um bloco se o resultado for `true`:

```java
if (temperatura > 30) {
    System.out.println("Está quente");
}
```

O bloco `else` executa quando a condição é `false`:

```java
if (temperatura > 30) {
    System.out.println("Está quente");
} else {
    System.out.println("Está fresco");
}
```

**`else if`** para múltiplas condições mutuamente exclusivas:

```java
if (nota >= 9) {
    conceito = "A";
} else if (nota >= 7) {
    conceito = "B";
} else if (nota >= 5) {
    conceito = "C";
} else {
    conceito = "F";
}
```

**A condição deve ser estritamente `boolean`.** Diferentemente de C, JavaScript ou Python, Java não aceita inteiros ou strings como condição:

```java
// Erro em Java:
// if (42) { ... }          // Não compila
// if ("texto") { ... }    // Não compila

// Correto:
if (true) { ... }
if (x > 0) { ... }
if (flag) { ... }
```

**Operador ternário** para decisões simples inline:

```java
String status = (idade >= 18) ? "Maior de idade" : "Menor de idade";
```

É conciso, mas perde legibilidade quando aninhado. Use quando a intenção for clara em uma linha; prefira `if` para lógica complexa.

---

### 3. `switch`: a decisão múltipla

Quando há muitas condições baseadas no mesmo valor, `switch` é mais legível e frequentemente mais eficiente que múltiplos `if-else`:

```java
switch (diaSemana) {
    case 1:
        System.out.println("Segunda-feira");
        break;
    case 2:
        System.out.println("Terça-feira");
        break;
    case 3:
    case 4:
        System.out.println("Quarta ou Quinta");  // cases podem compartilhar código
        break;
    default:
        System.out.println("Fim de semana");
}
```

**O `break` é essencial.** Sem ele, a execução "cai" para o próximo `case` (fall-through). Isso é um comportamento herdado do C que causa bugs quando não intencional.

---

### 4. `switch` moderno: expressão e pattern matching

O `switch` tradicional é uma **instrução** (statement) — executa código, não retorna valor. O **switch como expressão**, introduzido no Java 14 e refinado no Java 17 e 21, pode retornar um valor e usar sintaxe de seta (`->`):

```java
String nomeDia = switch (dia) {
    case 1 -> "Segunda-feira";
    case 2 -> "Terça-feira";
    case 3 -> "Quarta-feira";
    default -> "Outro dia";
};
```

**Vantagens do switch moderno:**
- Sem `break` — cada case é independente.
- Múltiplos valores em um case: `case 1, 2, 3 -> ...`
- O compilador verifica exaustividade em alguns contextos.
- Blocos podem usar `yield` para devolver valor:

```java
String resultado = switch (valor) {
    case 1 -> "Um";
    case 2 -> {
        String s = "Dois";
        yield s.toUpperCase();  // yield retorna valor do bloco
    }
    default -> "Outro";
};
```

**Pattern matching no `switch` (Java 21):** o `switch` pode desestruturar objetos com base em seu tipo:

```java
Object obj = "texto";
String resultado = switch (obj) {
    case Integer i   -> "Número: " + i;
    case String s    -> "Texto: " + s;
    case null        -> "É nulo";
    default          -> "Tipo desconhecido";
};
```

Isso elimina cadeias de `if (obj instanceof ...)` e o compilador ajuda a garantir que os casos são cobertos.

---

### 5. `for`: quando você sabe quantas vezes repetir

O loop `for` é ideal quando o número de iterações é conhecido ou controlado por um contador:

```java
// Estrutura: for (inicialização; condição; incremento)
for (int i = 0; i < 10; i++) {
    System.out.println(i);  // 0, 1, 2, ..., 9
}
```

As três partes são opcionais (nenhuma é obrigatória):

```java
int i = 0;
for ( ; i < 10; ) {
    System.out.println(i);
    i++;
}
```

**For-each (enhanced for):** percorre arrays e coleções sem índice explícito:

```java
int[] numeros = {10, 20, 30, 40};
for (int n : numeros) {
    System.out.println(n);
}

List<String> nomes = List.of("Ana", "Carlos");
for (String nome : nomes) {
    System.out.println(nome);
}
```

Use for-each sempre que precisar apenas percorrer elementos, sem modificar índices. É mais legível e elimina erros de índice fora dos limites.

---

### 6. `while`: quando você não sabe quantas vezes repetir

O `while` avalia a condição **antes** de cada iteração. Se a condição for `false` já na primeira verificação, o bloco nunca executa:

```java
int tentativas = 0;
while (!conectado && tentativas < 5) {
    conectar();
    tentativas++;
}
```

**`do-while`:** executa o bloco **antes** de verificar a condição. Garante pelo menos uma iteração:

```java
String entrada;
do {
    entrada = lerTeclado();
} while (!entrada.equals("sair"));
```

`do-while` é o menos usado dos loops, mas é a escolha certa quando você precisa executar algo pelo menos uma vez antes de saber se deve continuar.

---

### 7. `break` e `continue`: controle fino do loop

**`break`** encerra o loop imediatamente:

```java
for (int i = 0; i < 100; i++) {
    if (i == 5) {
        break;  // Sai do loop quando i == 5
    }
    System.out.println(i);
}
// Imprime 0, 1, 2, 3, 4
```

**`continue`** pula para a próxima iteração:

```java
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) {
        continue;  // Pula os números pares
    }
    System.out.println(i);  // Imprime 1, 3, 5, 7, 9
}
```

**Labels** permitem quebrar ou continuar loops externos a partir de loops internos:

```java
externo:
for (int i = 0; i < 10; i++) {
    for (int j = 0; j < 10; j++) {
        if (i * j > 50) {
            break externo;  // Sai de ambos os loops
        }
    }
}
```

Use labels com moderação — se a lógica exige labels, considere extrair o código para um método e usar `return`.

---

### 8. Loops vs. Streams: quando preferir cada um

O Java 8 introduziu a API de Streams como alternativa aos loops tradicionais:

```java
// Loop tradicional
List<String> resultado = new ArrayList<>();
for (String nome : nomes) {
    if (nome.startsWith("A")) {
        resultado.add(nome.toUpperCase());
    }
}

// Stream equivalente
List<String> resultado = nomes.stream()
    .filter(n -> n.startsWith("A"))
    .map(String::toUpperCase)
    .toList();
```

**Prefira Streams quando:**
- Há múltiplas transformações encadeadas (filtrar, mapear, reduzir).
- O código com loop fica cheio de variáveis temporárias e `if` aninhados.
- Você quer deixar explícito que não há efeitos colaterais.

**Prefira loops quando:**
- A lógica envolve índices ou precisa modificar a posição.
- É necessário `break`, `continue` ou exceções controladas.
- A operação é simples e a versão com Stream não acrescenta clareza.

Não há dogma. Use o que comunica a intenção com mais clareza para o próximo desenvolvedor que ler seu código.

---

### 9. O que não fazer

**Loops infinitos acidentais:**

```java
// Esqueceu o incremento
for (int i = 0; i < 10; ) {
    System.out.println(i);  // Loop infinito — i nunca muda
}

// Condição que nunca se torna false
while (true) {
    if (condicao) {
        // Esqueceu o break
    }
}
```

**Modificar a coleção durante a iteração com for-each:**

```java
List<String> nomes = new ArrayList<>(List.of("Ana", "Carlos"));
for (String nome : nomes) {
    if (nome.startsWith("A")) {
        nomes.remove(nome);  // ConcurrentModificationException!
    }
}
```

Para remover durante a iteração, use o iterador explicitamente ou `removeIf`:

```java
// Correto com removeIf
nomes.removeIf(n -> n.startsWith("A"));

// Correto com Iterator
Iterator<String> it = nomes.iterator();
while (it.hasNext()) {
    if (it.next().startsWith("A")) {
        it.remove();  // Método do Iterator, seguro
    }
}
```

**Usar `==` em vez de `.equals()` em condições:**

```java
String nome = lerEntrada();
if (nome == "admin") {  // Errado — compara referências, não conteúdo
    // ...
}

if ("admin".equals(nome)) {  // Correto — compara conteúdo e é null-safe
    // ...
}
```

---

### 10. Performance e armadilhas

**`switch` é mais rápido que longas cadeias de `if-else`?** Para muitos casos, sim. O compilador pode otimizar `switch` para uma tabela de salto (tableswitch) ou busca binária (lookupswitch) em vez de avaliação sequencial. Na prática, a diferença é relevante apenas em loops muito intensivos — mas usar `switch` para valores discretos é também mais idiomático e legível.

**Loops aninhados e complexidade algorítmica:** três loops aninhados que percorrem coleções de tamanho N resultam em O(n³). Antes de otimizar o loop em si, verifique se a estrutura algorítmica é a correta.

**Variáveis declaradas dentro vs. fora do loop:**

```java
// Dentro do loop — novo objeto a cada iteração
for (int i = 0; i < 1000; i++) {
    String temp = "Valor " + i;  // Criada e descartada a cada iteração
}

// Fora do loop — reutiliza a variável
String temp;
for (int i = 0; i < 1000; i++) {
    temp = "Valor " + i;
}
```

A diferença de performance é desprezível na maioria dos casos (o compilador e a JVM otimizam), mas a legibilidade favorece declarar a variável dentro do loop quando ela só é usada lá.

---

### 11. Resumo em uma frase

Condicionais decidem o caminho, loops percorrem o caminho — e a diferença entre um código que funciona e um código que funciona bem está em usar a ferramenta certa para cada bifurcação e cada repetição, do clássico `if` ao `switch` com pattern matching, do `for-each` idiomático à Stream declarativa.

---

### Fontes

1. Oracle, "Java Language Specification — Chapter 14: Blocks and Statements" — especificação oficial de `if`, `switch`, `for`, `while`, `do` e regras de controle de fluxo 
2. Oracle, "Java Tutorials – Control Flow Statements" — documentação oficial com exemplos de condicionais e loops 
3. Dev.java, "Switch Expressions" — documentação oficial sobre switch como expressão e pattern matching 
4. Baeldung, "Java Control Flow" — guia abrangente de condicionais, loops, `break`/`continue`, labels e boas práticas 
5. GeeksforGeeks, "Decision Making in Java" — estruturas de decisão com exemplos, ternário e armadilhas comuns 
6. GeeksforGeeks, "Loops in Java" — tipos de loops, for-each, loops aninhados e armadilhas