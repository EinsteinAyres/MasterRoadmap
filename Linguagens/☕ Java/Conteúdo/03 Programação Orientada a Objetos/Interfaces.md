
## Interfaces: o contrato que define comportamentos

### 1. O que é uma interface

Uma interface é um **contrato** que define um conjunto de métodos que uma classe deve implementar. Ela estabelece **o que** deve ser feito, mas não **como**. A implementação concreta fica a cargo de cada classe que "assina" esse contrato.

A palavra-chave é `interface`. Uma classe "assina" o contrato com `implements` .

```java
interface Voavel {
    void voar();           // Método abstrato — sem corpo
    void pousar();         // Método abstrato — sem corpo
}

class Passaro implements Voavel {
    @Override
    public void voar() {
        System.out.println("Batendo asas");
    }

    @Override
    public void pousar() {
        System.out.println("Pousando no galho");
    }
}

class Aviao implements Voavel {
    @Override
    public void voar() {
        System.out.println("Acelerando turbinas");
    }

    @Override
    public void pousar() {
        System.out.println("Aterrissando na pista");
    }
}
```

---

### 2. Para que servem as interfaces

**Definir contratos desacoplados.** O código cliente depende da interface, não da implementação concreta. Você pode trocar `Passaro` por `Aviao` sem alterar o código que usa `Voavel` .

**Permitir herança múltipla de comportamento.** Java não permite herdar de múltiplas classes, mas permite implementar múltiplas interfaces. Uma classe pode ser `Voavel` e `Nadavel` ao mesmo tempo .

```java
class Pato implements Voavel, Nadavel {
    @Override
    public void voar() { /* ... */ }

    @Override
    public void nadar() { /* ... */ }
}
```

**Polimorfismo.** Diferentes classes que implementam a mesma interface podem ser tratadas de forma uniforme .

```java
List<Voavel> frota = List.of(new Passaro(), new Aviao(), new Pato());
for (Voavel v : frota) {
    v.voar();  // Cada um voa à sua maneira
}
```

---

### 3. Componentes de uma interface

Uma interface pode conter:

| Componente | Exemplo | Desde |
|------------|---------|-------|
| **Métodos abstratos** | `void executar();` | Sempre |
| **Métodos `default`** | `default void log() { ... }` | Java 8 |
| **Métodos `static`** | `static Metodo criar() { ... }` | Java 8 |
| **Constantes** | `int MAX = 100;` (implicitamente `public static final`) | Sempre |
| **Métodos `private`** | `private void auxiliar() { ... }` | Java 9 |

```java
interface Calculavel {
    // Constante — implicitamente public static final
    double PI = 3.14159;

    // Método abstrato — implicitamente public abstract
    double calcular(double x, double y);

    // Método default — implementação herdada (Java 8+)
    default double dobrar(double x) {
        return x * 2;
    }

    // Método static — chamado na interface (Java 8+)
    static Calculavel criarPadrao() {
        return (x, y) -> x + y;
    }

    // Método private — auxiliar interno (Java 9+)
    private void validar(double valor) {
        if (Double.isNaN(valor)) throw new IllegalArgumentException();
    }
}
```

---

### 4. Métodos `default`: evolução sem quebra

Antes do Java 8, adicionar um novo método a uma interface quebrava **todas** as classes que a implementavam — elas precisavam implementar o novo método imediatamente. Os métodos `default` resolveram isso ao fornecer uma implementação padrão .

```java
interface Logger {
    void log(String mensagem);

    // Adicionado depois, sem quebrar implementações existentes
    default void logComTimestamp(String mensagem) {
        log("[" + java.time.Instant.now() + "] " + mensagem);
    }
}
```

Classes que implementam `Logger` não são obrigadas a implementar `logComTimestamp()` — elas herdam a implementação padrão. Mas podem sobrescrevê-la se desejarem .

**Conflito de métodos `default`:** se duas interfaces fornecem métodos `default` com a mesma assinatura, a classe que implementa ambas **deve** sobrescrever o método para resolver a ambiguidade:

```java
interface A {
    default void saudacao() { System.out.println("A"); }
}

interface B {
    default void saudacao() { System.out.println("B"); }
}

class C implements A, B {
    @Override
    public void saudacao() {
        A.super.saudacao();  // Ou B.super.saudacao(), ou uma implementação própria
    }
}
```

---

### 5. Métodos `static` em interfaces

Métodos `static` pertencem à interface, não às instâncias. São métodos utilitários que fazem sentido no contexto da interface e são chamados diretamente nela :

```java
interface Conversor {
    double converter(double valor);

    static Conversor criarDolarParaReal(double cotacao) {
        return valor -> valor * cotacao;
    }

    static Conversor criarRealParaDolar(double cotacao) {
        return valor -> valor / cotacao;
    }
}
```

```java
Conversor dolarParaReal = Conversor.criarDolarParaReal(5.30);
System.out.println(dolarParaReal.converter(100));  // 530.0
```

Diferentemente de métodos `default`, métodos `static` **não são herdados** pelas classes implementadoras. Eles só podem ser chamados pelo nome da interface.

---

### 6. Métodos `private` em interfaces (Java 9+)

Quando métodos `default` compartilham lógica comum, você pode extrair essa lógica para métodos `private` dentro da própria interface :

```java
interface Validador {
    default boolean validarEmail(String email) {
        return email != null && email.contains("@") && email.contains(".");
    }

    default boolean validarTelefone(String telefone) {
        return telefone != null && telefone.matches("\\d{8,11}");
    }

    // Método private — compartilhado pelos métodos default
    private boolean naoNulo(String valor) {
        return valor != null;
    }
}
```

Antes do Java 9, essa lógica teria que ser duplicada ou extraída para uma classe auxiliar separada.

---

### 7. Interfaces funcionais

Uma **interface funcional** é uma interface que possui **exatamente um** método abstrato. Ela pode ser usada como alvo de expressões lambda e method references .

A anotação `@FunctionalInterface` é opcional, mas recomendada — o compilador verifica se a interface realmente tem apenas um método abstrato:

```java
@FunctionalInterface
interface Operacao {
    int executar(int a, int b);
}
```

```java
Operacao soma = (a, b) -> a + b;
Operacao multiplicacao = (a, b) -> a * b;

System.out.println(soma.executar(3, 5));           // 8
System.out.println(multiplicacao.executar(3, 5));  // 15
```

**Interfaces funcionais padrão do Java (pacote `java.util.function`):**

| Interface | Método abstrato | Uso |
|-----------|----------------|-----|
| `Function<T, R>` | `R apply(T t)` | Transforma T em R |
| `Predicate<T>` | `boolean test(T t)` | Testa uma condição |
| `Consumer<T>` | `void accept(T t)` | Consome um valor (sem retorno) |
| `Supplier<T>` | `T get()` | Fornece um valor (sem entrada) |
| `UnaryOperator<T>` | `T apply(T t)` | Transforma T em T (mesmo tipo) |
| `BinaryOperator<T>` | `T apply(T a, T b)` | Combina dois T em um T |
| `Comparator<T>` | `int compare(T a, T b)` | Compara dois objetos |

---

### 8. Herança entre interfaces

Interfaces podem estender outras interfaces, criando hierarquias de contratos:

```java
interface Movivel {
    void mover();
}

interface Voavel extends Movivel {
    void voar();
}

// Quem implementa Voavel é obrigado a implementar mover() E voar()
class Passaro implements Voavel {
    @Override
    public void mover() {
        System.out.println("Andando");
    }

    @Override
    public void voar() {
        System.out.println("Voando");
    }
}
```

Uma interface pode estender **múltiplas** interfaces (diferentemente de classes, que só podem estender uma) .

---

### 9. Interfaces e classes abstratas: a decisão

| Característica | Interface | Classe Abstrata |
|---------------|-----------|-----------------|
| **Campos de instância** | Não (só constantes `static final`) | Sim |
| **Construtores** | Não | Sim |
| **Métodos concretos** | `default` e `static` (Java 8+) | Sim |
| **Métodos abstratos** | Sim (implicitamente `public`) | Sim (qualquer modificador) |
| **Herança múltipla** | Sim (implementa várias) | Não (estende uma) |
| **Modificadores nos métodos** | Implicitamente `public` | `private`, `protected`, `public` |
| **Para que serve** | Definir contrato sem estado | Compartilhar estado e comportamento comum |

**A regra de ouro:**
- Use **interface** para definir **capacidades** que classes não relacionadas podem compartilhar (`Voavel`, `Serializavel`, `Comparable`).
- Use **classe abstrata** quando houver **estado** (campos) ou **comportamento parcial** comum que as subclasses devem herdar.

---

### 10. Interfaces de marcador (Marker Interfaces)

Uma interface de marcador **não tem métodos**. Ela apenas "marca" uma classe como possuidora de uma propriedade especial. O exemplo mais famoso é `java.io.Serializable`:

```java
public interface Serializable {
    // Vazia — apenas marca a classe como serializável
}
```

```java
class Pessoa implements Serializable {
    private String nome;
    // Agora Pessoa pode ser serializada
}
```

Outros exemplos na biblioteca padrão: `Cloneable`, `RandomAccess`. São cada vez menos comuns — anotações (`@Entity`, `@Serializable`) frequentemente as substituem.

---

### 11. Interfaces no mundo real

**API de Collections:** `List`, `Set`, `Map`, `Queue` — todas são interfaces. Você usa `List<String>` em vez de `ArrayList<String>` para não se prender à implementação .

**Comparators e ordenação:** a interface `Comparable<T>` define ordem natural; `Comparator<T>` define ordens customizadas — ambas permitem que algoritmos de ordenação funcionem com qualquer tipo.

**Streams e programação funcional:** `Function`, `Predicate`, `Consumer`, `Supplier` permitem passar comportamento como parâmetro, viabilizando a API de Streams.

**Injeção de dependência (Spring Boot):** você declara a dependência como interface (`UserRepository`), e o framework injeta a implementação concreta em tempo de execução.

---

### 12. O que levar deste guia

Interfaces são o coração do design orientado a objetos em Java. Elas permitem acoplamento fraco, código flexível e sistemas extensíveis.

**Regras práticas:**

- **Programe para interfaces, não para implementações.** Declare `List<String>` em vez de `ArrayList<String>`; `MetodoPagamento` em vez de `CartaoCredito`.
- **Mantenha interfaces pequenas e focadas.** Muitos métodos tornam a interface difícil de implementar (Interface Segregation Principle).
- **Use `default` para evoluir interfaces sem quebrar código existente.** Mas não abuse — métodos `default` devem ser exceção, não regra.
- **Marque interfaces funcionais com `@FunctionalInterface`.** O compilador verifica que há apenas um método abstrato.
- **Use interfaces para definir capacidades transversais** (`Comparable`, `Serializable`, `Voavel`), não para compartilhar estado — para isso existem classes abstratas.

---

### Fontes

1. **Oracle — Java Tutorials: Interfaces** — Documentação oficial sobre declaração, implementação, métodos `default` e `static` .  
   https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html

2. **Dev.java — Interfaces and Inheritance** — Guia oficial sobre interfaces, herança múltipla, métodos `default` e evolução de APIs .  
   https://dev.java/learn/oop/interfaces/

3. **Baeldung — Java Interfaces** — Tutorial abrangente com métodos `default`, `static`, `private`, interfaces funcionais e herança .  
   https://www.baeldung.com/java-interfaces

4. **GeeksforGeeks — Interfaces in Java** — Definição, implementação, herança entre interfaces, interfaces de marcador e novidades do Java 8+ .  
   https://www.geeksforgeeks.org/interfaces-in-java/

5. **W3Schools — Java Interfaces** — Explicação prática com exemplos de implementação, múltiplas interfaces e métodos `default` .  
   https://www.w3schools.com/java/java_interface.asp

6. **Programiz — Java Interfaces** — Fundamento das interfaces com exemplos de implementação, herança entre interfaces e `default` .  
   https://www.programiz.com/java-programming/interfaces