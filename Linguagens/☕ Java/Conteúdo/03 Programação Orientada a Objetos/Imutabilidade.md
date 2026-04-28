
## Imutabilidade: objetos que não mudam nunca

### 1. O que é imutabilidade

Um objeto imutável é aquele cujo estado **não pode ser alterado** após sua construção. Todos os seus campos são definidos uma única vez — no construtor ou em inicializadores — e permanecem com os mesmos valores até o objeto ser coletado pelo Garbage Collector .

Parece simples, mas a definição é mais profunda do que aparenta. Não basta não ter setters. Uma classe é verdadeiramente imutável quando **nenhum código externo pode modificar seu estado interno por qualquer meio** — nem diretamente (acessando campos), nem indiretamente (modificando objetos internos expostos) .

A diferença entre imutabilidade real e wrappers "unmodifiable" é crucial: `Collections.unmodifiableList()` impede modificações através daquela referência, mas o objeto subjacente ainda pode ser alterado por outra referência. Imutabilidade verdadeira elimina essa possibilidade — não há "porta dos fundos" para modificar o estado .

---

### 2. Por que isso importa

**Thread safety gratuita.** Um objeto imutável pode ser compartilhado entre quantas threads você quiser, sem `synchronized`, sem locks, sem `volatile`. Se nada pode ser alterado, nenhuma thread pode interferir no trabalho de outra .

**Código mais simples de raciocinar.** Quando você passa um objeto imutável para um método, tem certeza absoluta de que o método não vai modificá-lo. Você não precisa inspecionar o código do método para saber o que pode ter acontecido com seus dados .

**Caching seguro.** Se um objeto não muda, qualquer propriedade computada pode ser cacheada sem medo. O `hashCode()` de uma `String` imutável é calculado uma vez e reutilizado para sempre. Chaves em `HashMap` nunca "se perdem" porque seu hash mudou inesperadamente .

**Segurança.** Strings imutáveis são a base da segurança em Java — classloaders, permissões, nomes de arquivo. Se uma String pudesse ser alterada depois da verificação de segurança, um código malicioso poderia burlar as verificações.

---

### 3. As regras para criar uma classe imutável

Sete regras, todas obrigatórias :

**1. Não forneça setters.** Nenhum método que modifique campos. Se precisar de um "novo valor", retorne um novo objeto.

**2. Marque a classe como `final`.** Impede que subclasses quebrem a imutabilidade adicionando campos mutáveis ou sobrescrevendo métodos.

**3. Marque todos os campos como `private` e `final`.** `private` impede acesso externo; `final` garante que o campo só é atribuído uma vez.

**4. Inicialize todos os campos no construtor.** Valide os argumentos e construa um estado consistente desde o primeiro momento.

**5. Faça cópias defensivas de objetos mutáveis recebidos.** Se o construtor recebe uma `List`, não guarde a referência — guarde uma cópia. Quem passou a lista pode modificá-la depois.

**6. Não exponha referências para objetos internos mutáveis.** Se um getter retorna uma coleção interna, retorne uma cópia ou uma visão imutável. Caso contrário, o chamador pode modificar seus dados internos.

**7. Métodos que "modificam" devem retornar novas instâncias.** `withAmount(BigDecimal novoValor)` não altera o objeto — retorna um novo com o valor atualizado.

```java
public final class Money {
    private final BigDecimal amount;
    private final Currency currency;

    public Money(BigDecimal amount, Currency currency) {
        if (amount == null || currency == null) {
            throw new NullPointerException("amount e currency são obrigatórios");
        }
        this.amount = amount.stripTrailingZeros();
        this.currency = currency;
    }

    public BigDecimal amount() { return amount; }
    public Currency currency() { return currency; }

    // Não modifica — retorna novo objeto
    public Money add(Money other) {
        requireSameCurrency(other);
        return new Money(this.amount.add(other.amount), this.currency);
    }

    public Money withAmount(BigDecimal newAmount) {
        return new Money(newAmount, this.currency);
    }

    private void requireSameCurrency(Money other) {
        if (!this.currency.equals(other.currency)) {
            throw new IllegalArgumentException("Moedas diferentes");
        }
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Money money)) return false;
        return amount.compareTo(money.amount) == 0 && currency.equals(money.currency);
    }

    @Override
    public int hashCode() {
        return Objects.hash(amount, currency);
    }
}
```

---

### 4. Cópias defensivas: o detalhe que faz diferença

Se sua classe imutável contém um campo de tipo mutável (como `Date`, `ArrayList`, array), você precisa de **duas** cópias:

**Na entrada (construtor):** copie o que recebeu, para que o chamador não mantenha uma referência que permita modificar seu estado interno .

**Na saída (getters):** retorne uma cópia ou visão imutável, para que o chamador não possa modificar o que está dentro do seu objeto .

```java
public final class Agenda {
    private final List<String> compromissos;

    public Agenda(List<String> compromissos) {
        // Cópia defensiva na entrada — o chamador pode modificar a lista original
        this.compromissos = List.copyOf(compromissos);  // Cópia imutável
    }

    public List<String> getCompromissos() {
        // Já é imutável, então podemos retornar diretamente
        return compromissos;
    }
}
```

Se você guardar a referência direta recebida no construtor:

```java
Agenda(List<String> compromissos) {
    this.compromissos = compromissos;  // ERRO — vulnerável
}

List<String> lista = new ArrayList<>();
lista.add("Reunião");
Agenda a = new Agenda(lista);
lista.add("Festa surpresa");  // Modifica o estado interno da Agenda!
```

---

### 5. Records: imutabilidade com zero boilerplate

Records (Java 16+) são a forma mais concisa de criar classes imutáveis. O compilador gera automaticamente construtor, acessores, `equals()`, `hashCode()` e `toString()` — tudo imutável :

```java
public record Ponto(int x, int y) { }
```

Equivale a dezenas de linhas de código manual. Os campos são implicitamente `private final`, e a classe é implicitamente `final`.

**Cuidado com campos mutáveis em records.** Se um record contém uma `List`, você ainda precisa fazer cópia defensiva no construtor compacto:

```java
public record Turma(String nome, List<String> alunos) {
    public Turma {
        alunos = List.copyOf(alunos);  // Cópia defensiva
    }
}
```

O record simplifica, mas não elimina a necessidade de proteger campos mutáveis.

---

### 6. A armadilha da referência mutável em campo imutável

Um erro sutil: ter uma classe com campos `final` de tipos imutáveis **não torna automaticamente a classe que contém esses campos thread-safe** se a referência para o objeto for compartilhada sem sincronização .

O objeto `String` é imutável. Mas a **referência** para a `String` armazenada em um campo não-`final` de outra classe **é mutável**. Uma thread pode ver uma referência desatualizada (stale reference) se o campo não for `volatile` ou acessado sob `synchronized` .

```java
// O objeto Helper é imutável, MAS a referência 'helper' é mutável
class Foo {
    private Helper helper;  // Não é final, não é volatile

    public Helper getHelper() { return helper; }

    public void setHelper(int num) {
        helper = new Helper(num);  // Outra thread pode não ver esta atualização
    }
}
```

Se uma thread chama `setHelper(42)` e outra chama `getHelper()` logo depois, a segunda thread pode ver `null` ou uma referência antiga — mesmo que `Helper` seja perfeitamente imutável .

**Soluções :**

- Declare o campo como `volatile`.
- Use `synchronized` nos métodos de acesso.
- Use `AtomicReference<Helper>`.
- Declare o campo como `final` e inicialize no construtor (sem lazy initialization).

O princípio: **o objeto imutável é seguro, mas a referência para ele precisa ser publicada com segurança**.

---

### 7. Inicialização preguiçosa (lazy initialization) e imutabilidade

Criar objetos imutáveis sob demanda é comum, mas perigoso. O padrão double-checked locking **só funciona com `volatile`**, mesmo para objetos imutáveis — a não ser que o campo seja `final` .

```java
// CORRETO — volatile garante visibilidade
class Foo {
    private volatile Bar bar;

    public Bar getBar() {
        Bar value = bar;
        if (value == null) {
            synchronized (this) {
                value = bar;
                if (value == null) {
                    bar = value = computeBar();
                }
            }
        }
        return value;
    }
}
```

**Exceção:** se o objeto imutável for armazenado em um campo `final`, o JMM (Java Memory Model) garante que ele é visível para todas as threads após a construção, mesmo sem `volatile`. Mas `final` impede lazy initialization — o campo precisa ser inicializado no construtor .

**Alternativa moderna (Java 25+ Preview):** a API de Stable Values (`StableValue`) oferece inicialização preguiçosa thread-safe sem `volatile`, com otimizações da JVM típicas de campos `final` .

---

### 8. Classes imutáveis essenciais na biblioteca padrão

Java está repleto de classes imutáveis — os designers da linguagem seguiram esse princípio desde o início :

**`String`** — A classe imutável canônica. `substring()`, `concat()`, `toUpperCase()` — tudo retorna nova String.

**Wrappers de primitivos** — `Integer`, `Long`, `Double`, `Boolean`, `Character`. Cada instância armazena um valor fixo, definido na construção.

**`BigInteger` e `BigDecimal`** — Aritmética imutável. Cada operação (`add`, `multiply`) retorna um novo objeto. Essencial para cálculos financeiros seguros.

**API `java.time`** — `LocalDate`, `LocalTime`, `LocalDateTime`, `Instant`, `Duration`. Todas as operações retornam novas instâncias. A imutabilidade eliminou os pesadelos de `Date` e `Calendar`.

**`UUID`** — Identificadores universais imutáveis.

**`Optional`** — Container imutável para um valor presente ou ausente.

**Coleções de `List.of()`, `Set.of()`, `Map.of()`** (Java 9+) — Coleções verdadeiramente imutáveis (não apenas wrappers unmodifiable).

---

### 9. Imutabilidade vs. performance

A objeção mais comum: "criar novos objetos o tempo todo é ineficiente". Na prática, a imutabilidade frequentemente **melhora** a performance :

**Caching sem medo.** Valores computados podem ser cacheados agressivamente — o `hashCode()` de `String` é calculado uma única vez.

**Eliminação de locks.** Sem necessidade de sincronização, elimina-se o overhead de contenção de locks em ambientes concorrentes.

**JVM otimiza melhor.** Campos `final` permitem otimizações agressivas do compilador JIT — constant folding, eliminação de verificações redundantes.

**Menos cópias defensivas.** Se tudo é imutável, você não precisa copiar dados ao passá-los entre métodos ou threads.

**O trade-off real:** algumas operações (como adicionar um elemento a uma "lista imutável") exigem criar uma cópia inteira. Para estruturas com muitas modificações, coleções mutáveis podem ser mais adequadas. Mas para a maioria dos cenários de negócio, a simplicidade e segurança da imutabilidade compensam o custo.

---

### 10. O que levar deste guia

Imutabilidade é o padrão seguro. Se você não tem um bom motivo para um objeto ser mutável, faça-o imutável.

**Regras práticas:**

- **Comece imutável.** Só torne mutável se houver um requisito concreto de performance que justifique a complexidade adicional.
- **Use records para dados.** `record Ponto(int x, int y)` substitui dezenas de linhas de código manual.
- **Faça cópia defensiva de entradas e saídas mutáveis.** Listas, arrays, Dates — copie na entrada (construtor) e proteja na saída (getters).
- **Publique referências imutáveis com segurança.** Se a referência pode mudar (reassign), use `volatile`, `synchronized` ou `AtomicReference`.
- **Prefira `List.of()` a `new ArrayList<>()` para coleções constantes.** Imutabilidade e thread safety gratuitas.
- **Cuidado com lazy initialization.** Se não for `final`, precisa de `volatile` ou sincronização — mesmo para objetos imutáveis.

---

### Fontes

1. **Carnegie Mellon University SEI — VNA01-J** — Thread-safety de referências para objetos imutáveis e risco de stale references .  
   https://wiki.sei.cmu.edu/confluence/pages/diffpagesbyversion.action?pageId=88487906&originalVersion=82&revisedVersion=111

2. **Cleverence — Immutable Objects in Java** — Definição, regras de design, classes imutáveis essenciais e armadilhas comuns .  
   https://www.cleverence.com/articles/oracle-documentation/immutable-objects-essential-java-classes-4827/

3. **InfoQ — Java 25 Stable Values API** — Deferred immutability, inicialização preguiçosa thread-safe e integração com otimizações da JVM .  
   https://www.infoq.com/news/2025/06/java25-stable-values-api-startup/

4. **Carnegie Mellon University SEI — CON28-J** — Publicação segura de referências para objetos imutáveis .  
   https://wiki.sei.cmu.edu/confluence/plugins/viewsource/viewpagesrc.action?pageId=88873525

5. **GUVI — Thread-Safe Java Classes** — Imutabilidade como estratégia primária para thread safety .  
   https://www.guvi.in/blog/essential-tips-thread-safe-java-classes/

6. **Google Open Source (Error Prone) — DoubleCheckedLocking** — Regras para double-checked locking com objetos imutáveis e exigência de `volatile` .  
   https://chromium.journaldev.googlesource.com/external/github.com/google/error-prone/+/ffe52a88571183d232f4407c744ea4e4cd2e3502/docs/bugpattern/DoubleCheckedLocking.md

7. **亿速云 — Java Immutable最佳实践** — Regras práticas para criação de classes imutáveis .  
   https://m.yisu.com/zixun/984207.html

8. **亿速云 — Java Immutable线程安全** — Exemplo de classe imutável com cópias defensivas .  
   https://m.yisu.com/zixun/1074141.html