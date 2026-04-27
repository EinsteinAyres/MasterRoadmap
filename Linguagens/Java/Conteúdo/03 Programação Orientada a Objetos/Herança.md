
## Herança: estender para reutilizar

### 1. O que é herança

Herança é o mecanismo da orientação a objetos que permite que uma classe **herde** campos e métodos de outra classe. A classe que herda é chamada de **subclasse** (classe filha). A classe da qual se herda é a **superclasse** (classe pai).

A palavra-chave é `extends`. Quando você escreve `class Cachorro extends Animal`, está dizendo: "Cachorro é um Animal, com tudo que um Animal tem, mais algumas coisas específicas de cachorro".

A herança resolve dois problemas: **reutilização de código** (você não reescreve o que é comum a várias classes) e **polimorfismo** (você pode tratar objetos de diferentes subclasses de forma uniforme) .

---

### 2. A sintaxe básica

```java
// Superclasse
class Animal {
    private String nome;

    public Animal(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }

    public String fazerSom() {
        return "algum som";
    }
}

// Subclasse
class Cachorro extends Animal {
    public Cachorro(String nome) {
        super(nome);  // Chama o construtor da superclasse
    }

    @Override
    public String fazerSom() {
        return "latido";
    }

    public void abanarRabo() {
        System.out.println(getNome() + " está abanando o rabo");
    }
}
```

```java
Cachorro rex = new Cachorro("Rex");
System.out.println(rex.getNome());    // "Rex" — herdado de Animal
System.out.println(rex.fazerSom());   // "latido" — sobrescrito em Cachorro
rex.abanarRabo();                     // Método específico de Cachorro
```

---

### 3. A palavra-chave `super`

`super` refere-se à superclasse imediata. Tem dois usos principais:

**Chamar o construtor da superclasse** — deve ser a primeira instrução do construtor da subclasse:

```java
public Cachorro(String nome, String raca) {
    super(nome);  // Obrigatório — Animal não tem construtor padrão
    this.raca = raca;
}
```

Se a superclasse tem um construtor sem argumentos, `super()` é chamado implicitamente. Mas se a superclasse só tem construtores com argumentos, a subclasse **é obrigada** a chamar `super(argumentos)` explicitamente .

**Acessar métodos e campos da superclasse** (quando não foram sobrescritos ou quando você quer a versão original):

```java
@Override
public String fazerSom() {
    return super.fazerSom() + " alto";  // "algum som alto"
}
```

---

### 4. O que é herdado e o que não é

**É herdado:**
- Campos e métodos `public` e `protected` da superclasse.
- Campos e métodos com modificador padrão (package-private), **se a subclasse estiver no mesmo pacote**.

**Não é herdado:**
- Membros `private` — existem na subclasse, mas não são acessíveis diretamente. Só podem ser acessados via métodos `public` ou `protected` herdados .
- Construtores — não são herdados. Cada subclasse define seus próprios construtores e chama `super()` para inicializar a parte herdada .

**Regra prática:** `private` é invisível para a subclasse. `protected` é visível. `public` é visível para todos.

---

### 5. Sobrescrita de métodos: `@Override`

Sobrescrita (overriding) é quando a subclasse fornece sua própria implementação de um método herdado. A assinatura (nome + parâmetros) deve ser idêntica à da superclasse .

```java
class Animal {
    public String fazerSom() {
        return "algum som";
    }
}

class Gato extends Animal {
    @Override
    public String fazerSom() {  // Mesmo nome, mesmos parâmetros
        return "miado";
    }
}
```

**Regras da sobrescrita:**
- O método na subclasse deve ter o mesmo nome e os mesmos parâmetros.
- O tipo de retorno deve ser igual ou um subtipo (retorno covariante, desde Java 5).
- O modificador de acesso não pode ser **mais restritivo**. Se o método original é `protected`, a sobrescrita pode ser `protected` ou `public`, nunca `private` .
- O método original não pode ser `final` (métodos `final` não podem ser sobrescritos).
- O método original não pode ser `static` (métodos estáticos são ocultados, não sobrescritos).

**A anotação `@Override`** é opcional mas **altamente recomendada**. Ela instrui o compilador a verificar se você realmente está sobrescrevendo um método da superclasse. Se você errar o nome ou os parâmetros, o compilador avisa.

---

### 6. Polimorfismo: o poder da herança

Polimorfismo é a capacidade de tratar um objeto de uma subclasse como se fosse da superclasse. Uma variável do tipo `Animal` pode referenciar qualquer objeto cuja classe estenda `Animal`:

```java
Animal a1 = new Cachorro("Rex");
Animal a2 = new Gato("Mimi");
Animal a3 = new Animal("Genérico");

System.out.println(a1.fazerSom());  // "latido" — a versão de Cachorro
System.out.println(a2.fazerSom());  // "miado" — a versão de Gato
System.out.println(a3.fazerSom());  // "algum som" — a versão de Animal
```

O método chamado é o do **tipo real do objeto** (Cachorro, Gato), não o do tipo da variável (Animal). Isso é **dynamic binding** (ligação dinâmica) — a JVM decide em tempo de execução qual implementação executar .

**Onde isso brilha:**

```java
List<Animal> zoo = List.of(new Cachorro("Rex"), new Gato("Mimi"), new Cachorro("Thor"));
for (Animal a : zoo) {
    System.out.println(a.getNome() + " faz " + a.fazerSom());
}
// Rex faz latido
// Mimi faz miado
// Thor faz latido
```

Você escreve o loop uma vez. Cada objeto responde de acordo com seu tipo real. Adicionar um novo animal (ex: `Papagaio extends Animal`) não exige modificar o loop.

---

### 7. Classes e métodos `final`: bloqueando a herança

`final` em uma classe impede que qualquer classe a estenda:

```java
public final class String { ... }  // Ninguém pode herdar de String
```

`final` em um método impede que subclasses o sobrescrevam:

```java
public final String getNomeCompleto() { ... }  // Comportamento imutável
```

Use `final` quando a herança ou a sobrescrita quebrariam o contrato da classe — seja por segurança (`String` é final para proteger a imutabilidade e o String Pool), seja por design (um método cujo comportamento não deve ser alterado por subclasses).

---

### 8. Herança simples vs. herança múltipla

Java **não** permite herança múltipla de classes. Uma classe pode estender **apenas uma** superclasse:

```java
// Isto é ERRO em Java:
class Morcego extends Mamifero, Voador { ... }  // Não compila
```

O motivo: evitar o **problema do diamante** — se duas superclasses tivessem um método com o mesmo nome, qual a subclasse herdaria? Para herança de comportamento, Java resolve isso com **interfaces** (uma classe pode implementar múltiplas interfaces) .

```java
interface Voador {
    void voar();
}

class Morcego extends Mamifero implements Voador {
    @Override
    public void voar() {
        System.out.println("Morcego voando");
    }
}
```

---

### 9. `instanceof`: verificando o tipo real

O operador `instanceof` verifica se um objeto é instância de uma classe ou implementa uma interface:

```java
Animal a = new Cachorro("Rex");

if (a instanceof Cachorro) {
    Cachorro c = (Cachorro) a;  // Cast seguro
    c.abanarRabo();
}
```

**Pattern matching com `instanceof` (Java 16+):** a verificação e o cast são combinados:

```java
if (a instanceof Cachorro c) {
    c.abanarRabo();  // 'c' já está declarado e atribuído
}
```

---

### 10. `Object`: a superclasse de tudo

Toda classe em Java herda, direta ou indiretamente, de `java.lang.Object`. Se você não escrever `extends`, o compilador insere `extends Object` automaticamente .

Isso significa que toda classe herda métodos fundamentais:

| Método | Propósito |
|--------|-----------|
| `toString()` | Representação em String do objeto |
| `equals(Object o)` | Comparação de igualdade (padrão: compara referências) |
| `hashCode()` | Código hash para uso em coleções hash |
| `getClass()` | Retorna a classe do objeto |

Por isso é boa prática sobrescrever `toString()`, `equals()` e `hashCode()` nas suas classes — as implementações padrão de `Object` raramente são úteis.

---

### 11. Classes abstratas: herança parcial

Uma classe `abstract` não pode ser instanciada. Ela serve como molde para subclasses e pode conter métodos abstratos (sem corpo) que as subclasses são obrigadas a implementar :

```java
abstract class Forma {
    abstract double area();  // Subclasses devem implementar

    public void exibir() {   // Método concreto herdado por todas
        System.out.println("Área: " + area());
    }
}

class Circulo extends Forma {
    private double raio;

    @Override
    double area() {
        return Math.PI * raio * raio;
    }
}
```

`Forma f = new Forma();` é erro. `Forma f = new Circulo(5);` é válido.

---

### 12. Sealed classes: controlando a herança (Java 17+)

Classes seladas permitem declarar explicitamente **quais** classes podem estendê-las. Isso é útil quando todas as variantes de um tipo são conhecidas em tempo de compilação :

```java
public sealed class Resultado
    permits Sucesso, Falha { }

public final class Sucesso extends Resultado { }
public final class Falha extends Resultado { }
// Nenhuma outra classe pode estender Resultado
```

As subclasses permitidas devem declarar como `final`, `sealed` ou `non-sealed`. Combinado com pattern matching no `switch`, o compilador verifica que todos os casos são cobertos — sem necessidade de `default`.

---

### 13. Herança vs. composição

Herança expressa "**é um**". Composição expressa "**tem um**". A diferença é sutil na teoria, mas gigantesca na prática.

**Herança:**

```java
class Carro extends Veiculo { }  // Carro É UM Veiculo
```

**Composição:**

```java
class Carro {
    private Motor motor;   // Carro TEM UM Motor
    private List<Roda> rodas;  // Carro TEM Rodas
}
```

**A regra de ouro:** prefira composição à herança. Herança cria acoplamento forte entre subclasse e superclasse — mudanças na superclasse podem quebrar subclasses. Composição é mais flexível: você pode trocar o `Motor` sem mudar a interface do `Carro` .

Use herança quando a relação "é um" for verdadeira e estável, e quando o polimorfismo for realmente necessário.

---

### 14. Armadilhas comuns

**Chamar métodos sobrescritíveis no construtor.** Se o construtor da superclasse chama um método que a subclasse sobrescreve, a versão da subclasse será executada — mas a subclasse ainda não terminou de inicializar seus campos:

```java
class Pai {
    public Pai() {
        inicializar();  // Perigoso — chama método sobrescritível
    }
    protected void inicializar() { }
}

class Filha extends Pai {
    private String dado;

    @Override
    protected void inicializar() {
        System.out.println(dado.length());  // NullPointerException — dado ainda é null
    }
}
```

**Confundir sobrecarga com sobrescrita.** Sobrecarga é mesmo nome, parâmetros diferentes (resolvida em compilação). Sobrescrita é mesmo nome, mesmos parâmetros (resolvida em execução). A anotação `@Override` previne esse erro — se você tentar sobrescrever mas errar os parâmetros, o compilador reclama.

**Herança profunda.** Hierarquias com muitos níveis (`A extends B extends C extends D`) são difíceis de entender, testar e modificar. Mantenha a hierarquia rasa — no máximo 2 ou 3 níveis.

---

### 15. O que levar deste guia

Herança é um mecanismo poderoso, mas deve ser usado com moderação. Ela resolve reutilização de código e polimorfismo, mas ao custo de acoplamento forte entre subclasse e superclasse.

**Regras práticas:**

- **Use herança apenas quando a relação "é um" for verdadeira.** "Cachorro é um Animal" — herança. "Carro tem um Motor" — composição.
- **Sobrescreva com `@Override` sempre.** O compilador verifica se você realmente está sobrescrevendo.
- **Não chame métodos sobrescritíveis em construtores.** A subclasse ainda não está pronta.
- **Prefira hierarquias rasas.** Se a árvore de herança tem mais de 3 níveis, reavalie o design.
- **Considere interfaces e composição antes de herança.** São mais flexíveis e geram menos acoplamento.
- **Use `sealed classes` quando todas as variantes forem conhecidas.** O compilador ajuda a garantir exaustividade.

---

### Fontes

1. **Oracle — Java Tutorials: Inheritance** — Documentação oficial sobre herança, `extends`, `super`, sobrescrita e polimorfismo .  
   https://docs.oracle.com/javase/tutorial/java/IandI/subclasses.html

2. **Dev.java — Inheritance** — Guia oficial sobre herança, reutilização de código e hierarquias de classes .  
   https://dev.java/learn/oop/inheritance/

3. **Baeldung — Java Inheritance** — Tutorial abrangente com exemplos, `super`, `@Override`, herança vs. composição .  
   https://www.baeldung.com/java-inheritance

4. **GeeksforGeeks — Inheritance in Java** — Definição, tipos de herança, métodos `final` e boas práticas .  
   https://www.geeksforgeeks.org/inheritance-in-java/

5. **W3Schools — Java Inheritance** — Tutorial prático com exemplos de subclasse, `super` e `final` .  
   https://www.w3schools.com/java/java_inheritance.asp

6. **Programiz — Java Inheritance** — Fundamento da herança com exemplos, `super`, `protected` e polimorfismo .  
   https://www.programiz.com/java-programming/inheritance