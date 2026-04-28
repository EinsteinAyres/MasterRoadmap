
## Classes: o molde de todo objeto Java

### 1. O que é uma classe

Uma classe é um molde que define a estrutura e o comportamento de um tipo de objeto. Ela descreve **o que** os objetos desse tipo armazenam (campos) e **o que** eles sabem fazer (métodos). É uma planta baixa — a casa só existe quando você usa `new` para construir uma instância .

Sem classes não há objetos, e sem objetos não há programa Java em execução. Tudo que acontece no seu código — uma requisição processada, um arquivo lido, um log emitido — envolve objetos criados a partir de classes.

---

### 2. A anatomia de uma classe

Toda classe combina três elementos: **estado**, **comportamento** e **identidade** .

**Estado** são os campos (atributos). Eles armazenam os dados que distinguem um objeto de outro. Dois objetos da mesma classe `Carro` podem ter `marca` e `ano` diferentes.

**Comportamento** são os métodos. Eles definem o que o objeto sabe fazer — acelerar, frear, calcular algo sobre seus próprios dados.

**Identidade** é o que torna cada objeto único, mesmo que dois objetos tenham os mesmos valores em todos os campos. A JVM distingue objetos pelo endereço na Heap; você pode verificar com `==` se duas referências apontam para o mesmo objeto.

```java
public class Carro {
    // Estado (campos)
    private String marca;
    private int ano;
    private boolean ligado;

    // Construtor — estabelece o estado inicial
    public Carro(String marca, int ano) {
        this.marca = marca;
        this.ano = ano;
        this.ligado = false;
    }

    // Comportamento (métodos)
    public void ligar() {
        this.ligado = true;
    }

    public String getMarca() {
        return marca;
    }
}
```

Para criar e usar objetos:

```java
Carro c1 = new Carro("Honda", 2024);
Carro c2 = new Carro("Honda", 2024);

System.out.println(c1 == c2);        // false — identidades diferentes
System.out.println(c1.equals(c2));   // depende da implementação de equals
```

`c1` e `c2` têm o mesmo estado, mas são objetos distintos — cada um com seu espaço na Heap .

---

### 3. Construtores: garantir que o objeto nasça válido

O construtor é o método especial chamado automaticamente pelo operador `new`. Ele tem o mesmo nome da classe e não tem tipo de retorno. Sua única responsabilidade é estabelecer um **estado inicial consistente** .

Se você não escrever nenhum construtor, o compilador gera um construtor padrão sem parâmetros. Mas, no momento em que você define um construtor, o padrão desaparece — você precisa escrever todos que fizerem sentido.

```java
public class Conta {
    private final String id;
    private final String titular;
    private double saldo;

    // Construtor principal — garante que id e titular nunca sejam nulos
    public Conta(String id, String titular) {
        if (id == null || titular == null) {
            throw new IllegalArgumentException("id e titular são obrigatórios");
        }
        this.id = id;
        this.titular = titular;
        this.saldo = 0.0;
    }

    // Sobrecarga com saldo inicial
    public Conta(String id, String titular, double saldoInicial) {
        this(id, titular);  // reusa o construtor principal
        this.saldo = saldoInicial;
    }
}
```

Construtores devem fazer o mínimo necessário para criar um objeto consistente. I/O pesada, consultas a banco de dados, chamadas de rede — tudo isso é má prática dentro de construtores .

---

### 4. Encapsulamento: esconda o que não precisa ser visto

Encapsulamento é o princípio de tornar os campos privados e expor apenas os métodos que fazem sentido para quem usa a classe. O objetivo é proteger **invariantes** — regras que devem permanecer verdadeiras durante toda a vida do objeto .

Os modificadores de acesso controlam a visibilidade :

| Modificador | Visível na classe | Visível no pacote | Visível em subclasses | Visível em qualquer lugar |
|-------------|-------------------|-------------------|-----------------------|---------------------------|
| `private` | Sim | Não | Não | Não |
| *(padrão)* | Sim | Sim | Não | Não |
| `protected` | Sim | Sim | Sim | Não |
| `public` | Sim | Sim | Sim | Sim |

Campos devem ser `private` — sempre. Se precisar expor um dado, faça via método, assim você mantém controle sobre como o estado é acessado ou modificado.

```java
public class Agenda {
    private final List<String> tarefas = new ArrayList<>();

    public void adicionar(String tarefa) {
        tarefas.add(tarefa);
    }

    // Devolve uma cópia não modificável — protege contra alterações externas
    public List<String> getTarefas() {
        return Collections.unmodifiableList(tarefas);
    }
}
```

---

### 5. Membros estáticos: o que pertence à classe, não ao objeto

Campos e métodos marcados como `static` pertencem à classe como um todo, não a instâncias individuais. Eles existem mesmo que nenhum objeto tenha sido criado .

Use `static` para:
- **Constantes:** `public static final double PI = 3.14159;`
- **Métodos utilitários:** `Math.sqrt()`, `Collections.sort()`
- **Métodos de fábrica:** `List.of()`, `Integer.valueOf()`

Evite campos `static` mutáveis. Eles são essencialmente variáveis globais — complicam teste, concorrência e raciocínio sobre o código.

```java
public final class Matematica {
    private Matematica() {}  // impede instanciação

    public static final double EPSILON = 1e-9;

    public static boolean quaseIgual(double a, double b) {
        return Math.abs(a - b) < EPSILON;
    }
}
```

---

### 6. Herança: quando uma classe é um tipo especializado de outra

Herança permite que uma classe (`subclasse`) estenda outra (`superclasse`), herdando seus campos e métodos. A subclasse pode adicionar novos comportamentos ou sobrescrever os existentes .

```java
abstract class Veiculo {
    abstract void acelerar();
}

class Carro extends Veiculo {
    @Override
    void acelerar() {
        System.out.println("Motor ronca, rodas giram");
    }
}

class Bicicleta extends Veiculo {
    @Override
    void acelerar() {
        System.out.println("Pedais giram, corrente traciona");
    }
}
```

**Polimorfismo** é a capacidade de tratar diferentes subclasses de forma uniforme :

```java
void iniciar(Veiculo v) {
    v.acelerar();  // comportamento depende do tipo real do objeto
}
```

**A regra de ouro:** herança expressa "é um". Se a relação não for estritamente taxonômica, prefira **composição** — um objeto que contém outro, em vez de herdar dele. Composição é mais flexível e menos acoplada .

```java
// Composição: Pedido "tem" Linhas de Pedido
class LinhaPedido {
    private String sku;
    private int quantidade;
}

class Pedido {
    private final List<LinhaPedido> linhas = new ArrayList<>();
    // Não herda de LinhaPedido — compõe com elas
}
```

---

### 7. Records: classes de dados sem cerimônia

Muitas classes existem apenas para transportar dados — DTOs, respostas de API, coordenadas. O Java tradicional exigia dezenas de linhas com construtor, getters, `equals`, `hashCode` e `toString`. Desde o Java 16, os **records** eliminam esse boilerplate :

```java
public record Ponto(int x, int y) {}
```

O compilador gera automaticamente:
- Construtor com todos os campos.
- Métodos de acesso com o nome do campo: `x()`, `y()`.
- `equals()`, `hashCode()` e `toString()` corretos.

Records são implicitamente **finais** (não podem ser herdados) e seus campos são **finais** (o objeto é imutável). Isso os torna thread-safe por natureza e ideais para programação funcional .

É possível adicionar métodos e construtores customizados:

```java
public record Ponto(int x, int y) {
    public int distanciaManhattan() {
        return Math.abs(x) + Math.abs(y);
    }
}
```

Records substituem a maior parte dos casos onde você escreveria uma classe com getters e setters. Se os dados são imutáveis e o comportamento principal é transporte, use record.

---

### 8. Sealed Classes: controlando quem pode herdar

Em herança tradicional, qualquer classe pode estender uma classe pública. Isso dificulta garantir que todas as subclasses são conhecidas em tempo de compilação .

**Sealed classes** (desde Java 17) resolvem isso declarando explicitamente quais classes podem herdar :

```java
public sealed interface Forma
    permits Circulo, Retangulo, Triangulo {
    double area();
}

public record Circulo(double raio) implements Forma {
    @Override
    public double area() {
        return Math.PI * raio * raio;
    }
}

public record Retangulo(double largura, double altura) implements Forma {
    @Override
    public double area() {
        return largura * altura;
    }
}

public record Triangulo(double base, double altura) implements Forma {
    @Override
    public double area() {
        return (base * altura) / 2;
    }
}
```

As subclasses permitidas devem declarar se são `final`, `sealed` ou `non-sealed` .

O verdadeiro poder das sealed classes aparece combinado com **switch com pattern matching** (Java 21) :

```java
double area = switch (forma) {
    case Circulo c    -> Math.PI * c.raio() * c.raio();
    case Retangulo r  -> r.largura() * r.altura();
    case Triangulo t  -> t.base() * t.altura() / 2;
    // Sem default necessário: o compilador sabe que são todos os casos
};
```

O compilador verifica que todos os casos foram cobertos — sem `default`, sem surpresas em tempo de execução .

---

### 9. Classes aninhadas: lógica auxiliar que não merece arquivo próprio

Java permite declarar classes dentro de outras classes. Há quatro variantes :

**Classe aninhada estática:** não tem acesso ao `this` da classe externa. Use para agrupar lógica auxiliar que não depende do estado da instância.

```java
class Relatorio {
    static class Formatador {
        static String negrito(String texto) {
            return "**" + texto + "**";
        }
    }
}
// Uso: Relatorio.Formatador.negrito("atenção")
```

**Classe interna (não estática):** tem acesso ao `this` da instância que a contém.

**Classe local:** declarada dentro de um método. Útil para implementações pontuais.

**Classe anônima:** declarada e instanciada no mesmo ponto. Desde o Java 8, lambdas substituem a maioria dos casos.

---

### 10. O ciclo de vida de um objeto

Um objeto nasce com `new`, vive na Heap e morre quando o Garbage Collector determina que não há mais referências alcançáveis .

```java
public void metodo() {
    Carro c = new Carro("Honda", 2024);
    // 'c' é uma referência na Stack
    // O objeto Carro está na Heap
} // 'c' sai do escopo. O objeto fica elegível para coleta.
```

A referência `c` é uma variável local na Stack . Quando o método termina, `c` desaparece. Se nenhuma outra referência apontar para o objeto `Carro`, o GC eventualmente o remove.

Evite manter referências desnecessárias — especialmente em coleções estáticas ou caches sem política de evicção. Isso causa vazamentos de memória: objetos tecnicamente alcançáveis, mas logicamente inúteis.

---

### 11. O que levar deste guia

Classes são o vocabulário com que você descreve o domínio do seu sistema. Cada classe deve ter uma responsabilidade clara, um construtor que garanta estado válido e o menor escopo de visibilidade possível para seus membros.

Para classes que só transportam dados, prefira **records** — menos código, imutabilidade, thread-safety. Para hierarquias fechadas onde todas as variantes são conhecidas, use **sealed classes** — o compilador verifica exaustividade e o pattern matching remove a necessidade de `default`.

A diferença entre um amontoado de classes e uma arquitetura bem modelada está na disciplina: campos privados, construtores que impõem invariantes, composição sobre herança quando não há relação "é um", e código que expressa a intenção do domínio, não os detalhes de implementação.

---

### Fontes

1. Cleverence, "Java Classes and Objects: Complete Tutorial with Examples and Best Practices" (2026) — anatomia de classes, construtores, encapsulamento, herança, composição, membros estáticos e ciclo de vida de objetos 

2. 阿里云开发者社区, "拥抱现代Java：记录与密封类的实用指南" (2025) — explicação e exemplos práticos de Records e Sealed Classes com switch pattern matching 

3. Stack Overflow, "Revision 814211ec" — implementação do tipo Either com sealed interface, records e pattern matching em Java 21 

4. GitHub/shukla, "modern-java-springboot-deep-dive" — projeto demonstrando uso de records e sealed classes com Java 21 e Spring Boot 3.2 

5. JetBrains Blog, "Java 21: Focus on Virtual Threads and Pattern Matching" (2023) — records e sealed classes como base para data-oriented programming 

6. Emory Libraries, "Effective Java (3rd Edition) — Joshua Bloch" — referência canônica sobre design de classes, construtores, encapsulamento e herança 