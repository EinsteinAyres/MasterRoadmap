
## Polimorfismo: muitos comportamentos, uma interface

### 1. O que é polimorfismo

Polimorfismo é a capacidade de um objeto responder de formas diferentes à mesma mensagem, dependendo do seu tipo real. A palavra vem do grego: *poli* (muitos) + *morfos* (formas). Um mesmo comando — `acelerar()`, `fazerSom()`, `calcularArea()` — produz resultados diferentes conforme o objeto que o recebe .

Imagine um volante. Você gira o volante para a direita e o veículo vira para a direita. Mas *como* ele vira depende do veículo: um carro vira as rodas dianteiras; uma moto inclina o corpo; um barco move o leme. A interface é a mesma (girar o volante). O comportamento é diferente para cada tipo de veículo. Isso é polimorfismo .

Em Java, o polimorfismo se manifesta de três formas principais: **polimorfismo dinâmico** (sobrescrita), **polimorfismo estático** (sobrecarga) e **polimorfismo paramétrico** (generics). A mais importante é a primeira.

---

### 2. Polimorfismo dinâmico: sobrescrita e dynamic binding

O polimorfismo dinâmico ocorre quando uma subclasse sobrescreve um método da superclasse e a JVM decide, **em tempo de execução**, qual versão do método executar, com base no tipo real do objeto — não no tipo da variável que o referencia .

```java
class Animal {
    public String fazerSom() {
        return "algum som";
    }
}

class Cachorro extends Animal {
    @Override
    public String fazerSom() {
        return "latido";
    }
}

class Gato extends Animal {
    @Override
    public String fazerSom() {
        return "miado";
    }
}
```

```java
Animal a1 = new Cachorro();
Animal a2 = new Gato();
Animal a3 = new Animal();

System.out.println(a1.fazerSom());  // "latido"
System.out.println(a2.fazerSom());  // "miado"
System.out.println(a3.fazerSom());  // "algum som"
```

O tipo das variáveis `a1`, `a2` e `a3` é `Animal` — o mesmo para todas. Mas o método chamado é o do **tipo real do objeto** armazenado. Isso é **dynamic binding** (ligação dinâmica ou late binding): a decisão de qual implementação executar é tomada em tempo de execução, com base no objeto concreto .

---

### 3. O poder do polimorfismo: código genérico

O verdadeiro valor do polimorfismo está em escrever código que funciona com a superclasse, sem conhecer as subclasses. Você escreve o código **uma vez**, e cada objeto responde de acordo com seu tipo.

```java
public class Zoologico {
    public void fazerTodosEmitiremSom(List<Animal> animais) {
        for (Animal a : animais) {
            System.out.println(a.getNome() + " faz " + a.fazerSom());
        }
    }
}
```

```java
List<Animal> zoo = List.of(
    new Cachorro("Rex"),
    new Gato("Mimi"),
    new Cachorro("Thor"),
    new Gato("Luna")
);

Zoologico z = new Zoologico();
z.fazerTodosEmitiremSom(zoo);
```

Saída:

```
Rex faz latido
Mimi faz miado
Thor faz latido
Luna faz miado
```

Se amanhã você criar `Papagaio extends Animal` com `fazerSom()` retornando "curupaco", o método `fazerTodosEmitiremSom()` **não precisa ser modificado**. Ele já funciona com qualquer subclasse de `Animal` — passada, presente ou futura. Isso é o **princípio aberto-fechado** (Open-Closed Principle) em ação: o código está aberto para extensão (novos animais), mas fechado para modificação .

---

### 4. Polimorfismo estático: sobrecarga de métodos

O polimorfismo estático ocorre com **sobrecarga** (overloading): múltiplos métodos com o mesmo nome, mas parâmetros diferentes. A decisão de qual versão chamar é tomada em **tempo de compilação**, com base nos tipos dos argumentos .

```java
public class Calculadora {
    public int somar(int a, int b) {
        return a + b;
    }

    public double somar(double a, double b) {
        return a + b;
    }

    public String somar(String a, String b) {
        return a + b;
    }
}
```

```java
Calculadora calc = new Calculadora();
System.out.println(calc.somar(2, 3));        // 5 — somar(int, int)
System.out.println(calc.somar(2.5, 3.7));    // 6.2 — somar(double, double)
System.out.println(calc.somar("A", "B"));    // "AB" — somar(String, String)
```

A decisão é puramente do compilador, baseada nos tipos dos argumentos. Isso é resolvido estaticamente, daí o nome.

---

### 5. Polimorfismo paramétrico: Generics

Generics permitem escrever classes e métodos que funcionam com **qualquer tipo**, mantendo a segurança de tipo em tempo de compilação. Você escreve o código uma vez e ele opera sobre diferentes tipos concretos :

```java
public class Caixa<T> {
    private T conteudo;

    public void guardar(T conteudo) {
        this.conteudo = conteudo;
    }

    public T pegar() {
        return conteudo;
    }
}
```

```java
Caixa<String> caixaDeTexto = new Caixa<>();
caixaDeTexto.guardar("Olá");
String texto = caixaDeTexto.pegar();  // Não precisa de cast

Caixa<Integer> caixaDeNumero = new Caixa<>();
caixaDeNumero.guardar(42);
Integer numero = caixaDeNumero.pegar();  // Não precisa de cast
```

---

### 6. Polimorfismo com interfaces

Interfaces são a forma mais pura de polimorfismo em Java. Uma interface define **o que** deve ser feito (contrato), mas não **como**. Diferentes classes implementam o mesmo contrato de formas radicalmente diferentes, e o código cliente não precisa saber qual implementação está usando .

```java
interface Pagavel {
    double calcularValor();
}

class Produto implements Pagavel {
    private double preco;

    @Override
    public double calcularValor() {
        return preco * 1.1;  // Com imposto
    }
}

class Servico implements Pagavel {
    private double valorHora;
    private int horas;

    @Override
    public double calcularValor() {
        return valorHora * horas;  // Sem imposto adicional
    }
}
```

```java
List<Pagavel> itens = List.of(
    new Produto(100),
    new Servico(50, 3)
);

double total = 0;
for (Pagavel p : itens) {
    total += p.calcularValor();  // Polimorfismo — cada um calcula à sua maneira
}
System.out.println("Total: " + total);
```

`Produto` e `Servico` não herdam de uma classe comum — implementam a mesma interface. O loop percorre uma lista de `Pagavel` e chama `calcularValor()` sem saber se está lidando com um produto ou um serviço .

---

### 7. Upcasting e downcasting

**Upcasting** é converter uma referência de subclasse para superclasse. É automático e sempre seguro — todo `Cachorro` é um `Animal`:

```java
Cachorro rex = new Cachorro("Rex");
Animal a = rex;  // Upcasting implícito — sempre funciona
```

**Downcasting** é converter uma referência de superclasse para subclasse. Não é automático e pode falhar:

```java
Animal a = new Cachorro("Rex");
Cachorro c = (Cachorro) a;  // Downcasting — ok, o objeto real é Cachorro
c.abanarRabo();              // Funciona

Animal b = new Gato("Mimi");
Cachorro d = (Cachorro) b;  // ClassCastException em execução!
```

**Pattern matching com `instanceof` (Java 16+)** torna o downcasting seguro e conciso:

```java
if (a instanceof Cachorro c) {
    c.abanarRabo();  // 'c' já está declarado e atribuído
} else if (a instanceof Gato g) {
    g.ronronar();
}
```

---

### 8. Polimorfismo no switch (Java 21+)

O pattern matching no `switch` leva o polimorfismo a outro nível, permitindo que você desestruture objetos com base em seu tipo de forma exaustiva:

```java
sealed interface Forma permits Circulo, Retangulo, Triangulo { }

record Circulo(double raio) implements Forma { }
record Retangulo(double base, double altura) implements Forma { }
record Triangulo(double base, double altura) implements Forma { }

double area(Forma f) {
    return switch (f) {
        case Circulo c    -> Math.PI * c.raio() * c.raio();
        case Retangulo r  -> r.base() * r.altura();
        case Triangulo t  -> (t.base() * t.altura()) / 2;
        // Sem default — o compilador sabe que são todos os casos
    };
}
```

O compilador verifica que todos os subtipos de `Forma` foram cobertos. Se você adicionar um novo `record Quadrado implements Forma`, o `switch` não compilará até que você trate o novo caso. Isso elimina uma classe inteira de bugs por omissão .

---

### 9. O princípio da substituição de Liskov

O polimorfismo só funciona corretamente se as subclasses respeitarem o **Princípio da Substituição de Liskov (LSP)**: uma subclasse deve poder substituir a superclasse sem quebrar o programa .

Em termos práticos:
- Uma subclasse não deve fortalecer pré-condições (exigir mais do que a superclasse exige).
- Uma subclasse não deve enfraquecer pós-condições (garantir menos do que a superclasse garante).
- Os métodos sobrescritos devem honrar o contrato do método original.

**Violação clássica:**

```java
class Retangulo {
    protected int largura, altura;

    public void setLargura(int largura) { this.largura = largura; }
    public void setAltura(int altura) { this.altura = altura; }
    public int getArea() { return largura * altura; }
}

class Quadrado extends Retangulo {
    @Override
    public void setLargura(int largura) {
        super.setLargura(largura);
        super.setAltura(largura);  // Força altura = largura — quebra o contrato
    }

    @Override
    public void setAltura(int altura) {
        super.setAltura(altura);
        super.setLargura(altura);  // Força largura = altura — quebra o contrato
    }
}
```

```java
void redimensionar(Retangulo r) {
    r.setLargura(5);
    r.setAltura(10);
    // Espera: área = 50
    // Se r for Quadrado: área = 100 — surpresa!
}
```

Quadrado **não pode** substituir Retangulo sem surpresas. A herança aqui é conceitualmente errada, embora matematicamente um quadrado seja um retângulo. A solução é não herdar — use composição ou uma interface comum .

---

### 10. Benefícios concretos do polimorfismo

**Código mais genérico e reutilizável.** Você escreve métodos que operam sobre a superclasse ou interface e eles funcionam automaticamente com qualquer subclasse futura .

**Menos acoplamento.** O código cliente depende da abstração (`Animal`, `Pagavel`, `List`), não das implementações concretas (`Cachorro`, `Produto`, `ArrayList`). Mudanças nas implementações não afetam o código cliente .

**Extensibilidade.** Adicionar um novo comportamento (uma nova subclasse ou implementação de interface) não exige modificar o código existente. Basta criar a nova classe e passá-la para os mesmos métodos .

**Testabilidade.** Com polimorfismo, você pode substituir implementações reais por mocks e stubs em testes, injetando dependências via interface sem alterar o código testado .

---

### 11. Resumo em uma frase

Polimorfismo é a capacidade de tratar objetos diferentes de forma uniforme — o cacheiro late, o gato mia, mas ambos respondem a `fazerSom()` à sua maneira, e o código que os invoca não precisa saber qual é qual.

---

### Fontes

1. **Oracle — Java Tutorials: Polymorphism** — Documentação oficial sobre polimorfismo dinâmico, sobrescrita e dynamic binding .  
   https://docs.oracle.com/javase/tutorial/java/IandI/polymorphism.html

2. **Dev.java — Polymorphism** — Guia oficial sobre polimorfismo, upcasting, downcasting e o princípio da substituição de Liskov .  
   https://dev.java/learn/oop/polymorphism/

3. **Baeldung — Polymorphism in Java** — Tutorial abrangente com polimorfismo dinâmico, estático, generics e melhores práticas .  
   https://www.baeldung.com/java-polymorphism

4. **GeeksforGeeks — Polymorphism in Java** — Definição, tipos de polimorfismo, sobrecarga vs. sobrescrita e exemplos práticos .  
   https://www.geeksforgeeks.org/polymorphism-in-java/

5. **W3Schools — Java Polymorphism** — Explicação prática com exemplos de herança, sobrescrita e polimorfismo .  
   https://www.w3schools.com/java/java_polymorphism.asp

6. **Programiz — Java Polymorphism** — Fundamento do polimorfismo com exemplos de sobrecarga, sobrescrita e polimorfismo em tempo de execução .  
   https://www.programiz.com/java-programming/polymorphism