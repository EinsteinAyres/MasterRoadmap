
## Abstração: esconder a complexidade, expor a essência

### 1. O que é abstração

Abstração é o princípio da orientação a objetos que consiste em **isolar os aspectos essenciais** de algo, ignorando os detalhes acidentais. Em vez de se preocupar com *como* algo funciona internamente, você se concentra em *o que* ele faz — sua interface, seu contrato, seu comportamento visível.

A metáfora mais direta: o pedal do acelerador de um carro. Você pisa para ir mais rápido, tira o pé para reduzir. Você não precisa saber como o motor injeta combustível, como as válvulas abrem e fecham, como a transmissão engata as marchas. Toda essa complexidade está abstraída atrás de um pedal. Se você trocar o motor a gasolina por um elétrico, o pedal continua funcionando igual .

Em Java, a abstração é implementada com **classes abstratas**, **interfaces** e **modificadores de acesso** que escondem detalhes internos. A abstração responde à pergunta "o que este objeto faz?", não "como ele faz?".

---

### 2. Abstração vs. Encapsulamento

São conceitos complementares, mas distintos. A confusão é comum porque ambos envolvem "esconder coisas" .

| Característica | Abstração | Encapsulamento |
|---------------|-----------|----------------|
| **Foco** | O **que** o objeto faz (comportamento externo) | **Como** o objeto protege seus dados (implementação interna) |
| **Pergunta** | "Qual é a interface deste objeto?" | "Quem pode acessar estes campos?" |
| **Mecanismo** | Classes abstratas, interfaces | Modificadores de acesso (`private`, `protected`) |
| **Objetivo** | Reduzir complexidade para o usuário da classe | Proteger a integridade dos dados |

**Exemplo concreto:** uma classe `ContaBancaria`:

- **Abstração:** você sabe que pode `depositar(valor)`, `sacar(valor)` e `getSaldo()`. Não precisa saber se o saldo é armazenado como `double`, `BigDecimal` ou `long` com centavos .
- **Encapsulamento:** o campo `saldo` é `private`. Ninguém de fora pode fazer `conta.saldo = -1000`. O acesso é controlado por métodos que validam as operações .

Ambos trabalham juntos: a abstração define a interface limpa que o mundo externo vê; o encapsulamento garante que o mundo externo não viole o estado interno.

---

### 3. Classes abstratas

Uma classe abstrata é declarada com a palavra-chave `abstract`. Ela **não pode ser instanciada** — serve apenas como molde para subclasses. Pode conter métodos abstratos (sem corpo, que as subclasses devem implementar) e métodos concretos (com corpo, herdados como estão) .

```java
abstract class Forma {
    protected String cor;

    public Forma(String cor) {
        this.cor = cor;
    }

    // Método abstrato — sem corpo. Subclasses são obrigadas a implementar
    public abstract double calcularArea();

    // Método concreto — herdado por todas as subclasses
    public void exibir() {
        System.out.println("Forma " + cor + " com área " + calcularArea());
    }
}
```

```java
class Circulo extends Forma {
    private double raio;

    public Circulo(String cor, double raio) {
        super(cor);
        this.raio = raio;
    }

    @Override
    public double calcularArea() {
        return Math.PI * raio * raio;
    }
}

class Retangulo extends Forma {
    private double base, altura;

    public Retangulo(String cor, double base, double altura) {
        super(cor);
        this.base = base;
        this.altura = altura;
    }

    @Override
    public double calcularArea() {
        return base * altura;
    }
}
```

```java
Forma f1 = new Circulo("vermelho", 5);
Forma f2 = new Retangulo("azul", 4, 6);

f1.exibir();  // Forma vermelha com área 78.53981633974483
f2.exibir();  // Forma azul com área 24.0

// Forma f3 = new Forma("verde");  // ERRO — classe abstrata não pode ser instanciada
```

**Características das classes abstratas:**
- Podem ter construtores (chamados via `super()` pelas subclasses).
- Podem ter campos, métodos concretos, métodos abstratos.
- Uma classe com **pelo menos um** método abstrato deve ser declarada `abstract`.
- Uma classe abstrata pode não ter nenhum método abstrato (útil para impedir instanciação direta).
- Fornecem uma base comum para todas as subclasses, eliminando duplicação de código .

---

### 4. Interfaces: abstração pura (e além)

Uma interface é um **contrato** que define um conjunto de métodos que uma classe deve implementar. Antes do Java 8, interfaces só podiam ter métodos abstratos. Hoje, podem ter também métodos `default` (com implementação) e `static` .

```java
interface Voavel {
    // Método abstrato — quem implementa é obrigado a definir
    void voar();

    // Método default — fornece implementação padrão (Java 8+)
    default void planar() {
        System.out.println("Planando...");
    }

    // Método estático — utilitário vinculado à interface (Java 8+)
    static double velocidadeMaximaPadrao() {
        return 100.0;
    }
}
```

```java
class Passaro implements Voavel {
    @Override
    public void voar() {
        System.out.println("Batendo asas");
    }
}

class Aviao implements Voavel {
    @Override
    public void voar() {
        System.out.println("Usando turbinas");
    }

    @Override
    public void planar() {
        System.out.println("Planando com asas fixas");
    }
}
```

```java
Voavel v1 = new Passaro();
v1.voar();    // "Batendo asas"
v1.planar();  // "Planando..." — implementação default

Voavel v2 = new Aviao();
v2.voar();    // "Usando turbinas"
v2.planar();  // "Planando com asas fixas" — sobrescrito

double max = Voavel.velocidadeMaximaPadrao();  // 100.0 — chamado na interface
```

---

### 5. Classes abstratas vs. Interfaces

| Característica | Classe Abstrata | Interface |
|---------------|-----------------|-----------|
| **Palavra-chave** | `abstract class` | `interface` |
| **Instanciação** | Não pode | Não pode |
| **Construtores** | Sim | Não (campos não são permitidos, exceto constantes) |
| **Campos** | Sim (qualquer tipo) | Apenas constantes (`public static final`) |
| **Métodos concretos** | Sim | Sim, via `default` e `static` (Java 8+) |
| **Herança múltipla** | Não (herda de uma só) | Sim (implementa várias) |
| **Modificadores de acesso nos métodos** | Qualquer um (`private`, `protected`, `public`) | Implicitamente `public` |
| **Quando usar** | Subclasses compartilham estado e comportamento | Classes não relacionadas compartilham contrato |

**A regra de ouro:** use **classe abstrata** quando as subclasses compartilham estado (campos) ou comportamento parcial comum. Use **interface** quando você quer definir um contrato que classes não relacionadas podem implementar .

---

### 6. Abstração em camadas

A abstração não é binária — é uma escada. Cada camada esconde detalhes da camada inferior e oferece uma visão simplificada para a camada superior.

**Exemplo: sistema de pagamentos.**

```java
// Camada 1: Abstração de mais alto nível
interface MetodoPagamento {
    boolean processar(double valor);
}
```

```java
// Camada 2: Implementações concretas
class CartaoCredito implements MetodoPagamento {
    private String numero;
    private String codigoSeguranca;

    @Override
    public boolean processar(double valor) {
        return gatewayExterno.autorizar(numero, codigoSeguranca, valor);
    }

    private boolean gatewayExterno(String num, String cvv, double valor) {
        // Detalhes de comunicação HTTP, criptografia, etc.
        // Tudo escondido de quem usa a interface
    }
}

class Pix implements MetodoPagamento {
    private String chavePix;

    @Override
    public boolean processar(double valor) {
        return bancoCentral.transferir(chavePix, valor);
    }
}
```

```java
// Camada 3: Código cliente — vê apenas a abstração
class Caixa {
    public void cobrar(MetodoPagamento pagamento, double valor) {
        if (pagamento.processar(valor)) {
            System.out.println("Pagamento de " + valor + " efetuado");
        } else {
            System.out.println("Pagamento recusado");
        }
    }
}
```

O `Caixa` não sabe se está lidando com cartão de crédito, PIX, boleto ou criptomoeda. Ele só conhece `MetodoPagamento.processar()`. Você pode adicionar novos métodos de pagamento sem alterar uma linha do `Caixa` .

---

### 7. Métodos `default` e `static` em interfaces (Java 8+)

Antes do Java 8, adicionar um novo método a uma interface quebrava todas as classes que a implementavam — elas precisavam implementar o novo método. Os métodos `default` resolveram isso:

```java
interface Logger {
    void log(String mensagem);

    // Método default adicionado depois, sem quebrar implementações existentes
    default void logComTimestamp(String mensagem) {
        log("[" + LocalDateTime.now() + "] " + mensagem);
    }

    // Método estático — utilitário vinculado à interface
    static Logger paraConsole() {
        return System.out::println;
    }
}
```

Classes que implementam `Logger` não são obrigadas a implementar `logComTimestamp()` — elas herdam a implementação padrão, mas podem sobrescrevê-la se desejarem .

---

### 8. Abstração com classes seladas (Java 17+)

Classes seladas (`sealed`) permitem definir explicitamente quais subclasses são permitidas. Isso cria uma abstração **fechada**, onde todas as variantes são conhecidas em tempo de compilação:

```java
public sealed interface Resultado
    permits Sucesso, Erro { }

public record Sucesso(String mensagem) implements Resultado { }
public record Erro(int codigo, String descricao) implements Resultado { }
```

Combinado com pattern matching no `switch` (Java 21+), o compilador verifica que todos os casos foram tratados:

```java
String tratar(Resultado r) {
    return switch (r) {
        case Sucesso s -> "OK: " + s.mensagem();
        case Erro e    -> "Erro " + e.codigo() + ": " + e.descricao();
        // Sem default — o compilador SABE que são todos os casos
    };
}
```

Se alguém adicionar um novo `record Alerta implements Resultado`, o `switch` para de compilar — forçando o desenvolvedor a tratar o novo caso. Isso elimina bugs por omissão .

---

### 9. Benefícios da abstração

**Redução da complexidade.** O usuário de uma classe vê apenas os métodos relevantes. Os detalhes internos — estruturas de dados, algoritmos, conexões — ficam escondidos. Isso reduz a carga cognitiva e a chance de uso incorreto .

**Manutenibilidade.** Você pode reescrever completamente a implementação interna de um método sem afetar quem o usa. Troque o algoritmo de ordenação, mude o banco de dados, otimize o cache — desde que a interface pública permaneça a mesma, o resto do sistema não precisa saber .

**Extensibilidade.** Com interfaces, você pode adicionar novos comportamentos ao sistema criando novas classes que implementam a interface, sem modificar o código existente .

**Desacoplamento.** O código cliente depende de abstrações (`MetodoPagamento`, `List`, `Forma`), não de implementações concretas (`CartaoCredito`, `ArrayList`, `Circulo`). Isso torna o sistema mais modular e testável .

**Trabalho em equipe.** Diferentes desenvolvedores podem trabalhar em diferentes implementações da mesma interface simultaneamente, sem conflitos — cada um sabe o contrato que deve cumprir .

---

### 10. O que levar deste guia

Abstração é esconder o "como" atrás do "o quê". É o princípio que permite construir sistemas complexos sem que a complexidade de cada parte contamine as outras.

**Regras práticas:**

- **Programe para interfaces, não para implementações.** Declare variáveis com o tipo mais abstrato possível: `List<String>` em vez de `ArrayList<String>`, `MetodoPagamento` em vez de `CartaoCredito`.
- **Use classes abstratas quando houver estado ou comportamento comum.** Se várias classes compartilham campos e métodos, extraia uma superclasse abstrata.
- **Use interfaces para contratos.** Se classes não relacionadas precisam oferecer o mesmo comportamento, defina uma interface.
- **Mantenha as interfaces enxutas.** Muitos métodos em uma interface a tornam difícil de implementar. Prefira interfaces focadas (Interface Segregation Principle).
- **Use `sealed` quando as variantes forem conhecidas.** Para hierarquias fechadas (Resultado: Sucesso ou Erro), classes seladas dão segurança ao compilador.

---

### Fontes

1. **Oracle — Java Tutorials: Abstract Methods and Classes** — Documentação oficial sobre classes abstratas, métodos abstratos e suas regras .  
   https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html

2. **Dev.java — Abstract Classes and Interfaces** — Guia oficial sobre classes abstratas, interfaces e quando usar cada uma .  
   https://dev.java/learn/oop/abstraction/

3. **Baeldung — Abstract Classes in Java** — Tutorial abrangente com exemplos, construtores em classes abstratas e diferenças para interfaces .  
   https://www.baeldung.com/java-abstract-class

4. **GeeksforGeeks — Abstraction in Java** — Definição, classes abstratas, interfaces e diferenças entre abstração e encapsulamento .  
   https://www.geeksforgeeks.org/abstraction-in-java-2/

5. **Programiz — Java Abstraction** — Fundamento da abstração com exemplos de classes abstratas, interfaces e aplicação prática .  
   https://www.programiz.com/java-programming/abstraction

6. **W3Schools — Java Abstraction** — Explicação prática com exemplos de classes abstratas e interfaces .  
   https://www.w3schools.com/java/java_abstract.asp