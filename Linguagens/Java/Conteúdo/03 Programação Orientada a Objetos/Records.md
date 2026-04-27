
## Records: classes de dados sem cerimônia

### 1. O problema que records resolvem

Antes do Java 16, criar uma classe simples para transportar dados exigia uma quantidade desproporcional de código. Para representar um ponto com coordenadas `x` e `y`, você precisava escrever:

```java
public class Ponto {
    private final int x;
    private final int y;

    public Ponto(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() { return x; }
    public int getY() { return y; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Ponto)) return false;
        Ponto ponto = (Ponto) o;
        return x == ponto.x && y == ponto.y;
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }

    @Override
    public String toString() {
        return "Ponto{x=" + x + ", y=" + y + "}";
    }
}
```

Isso são mais de 30 linhas para expressar "dois números que andam juntos". O código é repetitivo, propenso a erros (esquecer de atualizar `equals` quando adicionar um campo) e ocupa espaço mental que deveria estar focado na lógica de negócio.

**Records** (introduzidos oficialmente no Java 16) reduzem tudo isso a uma linha:

```java
public record Ponto(int x, int y) { }
```

O compilador gera automaticamente o construtor, os acessores, `equals()`, `hashCode()` e `toString()` — tudo consistente com os campos declarados. Você obtém uma classe imutável, thread-safe, com semântica de valor (dois pontos com as mesmas coordenadas são iguais), sem escrever nada além da declaração dos componentes .

---

### 2. O que o compilador gera

Para o record `Ponto(int x, int y)`, o compilador produz automaticamente :

| Elemento gerado | Descrição |
|-----------------|-----------|
| **Campos** | `private final int x;` e `private final int y;` |
| **Construtor canônico** | `public Ponto(int x, int y) { this.x = x; this.y = y; }` |
| **Métodos de acesso** | `public int x() { return this.x; }` e `public int y() { return this.y; }` |
| **`equals(Object)`** | Compara os campos um a um usando `==` para primitivos e `equals()` para objetos |
| **`hashCode()`** | Calcula o hash combinando todos os campos (via `Objects.hash()` ou equivalente) |
| **`toString()`** | Retorna `"Ponto[x=3, y=5]"` — nome do record seguido dos campos entre colchetes |

Características fundamentais que o compilador impõe:
- O record é implicitamente `final` — não pode ser estendido .
- Os campos são implicitamente `private final` — imutáveis por definição .
- Não é possível declarar campos de instância adicionais (apenas os componentes do record).
- O record estende implicitamente `java.lang.Record` — não pode estender outra classe, mas pode implementar interfaces .

---

### 3. Uso básico

```java
Ponto p1 = new Ponto(3, 5);
Ponto p2 = new Ponto(3, 5);
Ponto p3 = new Ponto(7, 2);

System.out.println(p1.x());          // 3 — acessores com nome do campo
System.out.println(p1.y());          // 5
System.out.println(p1);              // Ponto[x=3, y=5]
System.out.println(p1.equals(p2));   // true — mesmos valores
System.out.println(p1.equals(p3));   // false
System.out.println(p1.hashCode() == p2.hashCode());  // true
```

---

### 4. Construtor compacto: validação sem repetição

O construtor canônico (o que recebe todos os componentes) pode ser escrito de forma **compacta**: você omite os parâmetros e as atribuições, escrevendo apenas a lógica de validação. O compilador insere as atribuições automaticamente no final :

```java
public record Produto(String nome, double preco, int estoque) {
    // Construtor compacto — sem declarar parâmetros, sem atribuições manuais
    public Produto {
        if (nome == null || nome.isBlank()) {
            throw new IllegalArgumentException("Nome é obrigatório");
        }
        if (preco < 0) {
            throw new IllegalArgumentException("Preço não pode ser negativo");
        }
        if (estoque < 0) {
            throw new IllegalArgumentException("Estoque não pode ser negativo");
        }
        // Atribuições são inseridas automaticamente pelo compilador aqui
    }
}
```

Isso é equivalente a escrever o construtor canônico completo com todos os parâmetros e `this.campo = campo` — mas muito mais conciso e menos propenso a erros.

---

### 5. Construtores alternativos: delegando ao canônico

Você pode definir construtores adicionais, desde que **deleguem ao construtor canônico** via `this()` :

```java
public record Ponto(int x, int y) {
    // Construtor alternativo — origem
    public Ponto() {
        this(0, 0);  // Delega ao canônico
    }

    // Construtor alternativo — coordenadas iguais
    public Ponto(int valor) {
        this(valor, valor);  // Delega ao canônico
    }
}
```

```java
Ponto origem = new Ponto();      // Ponto[x=0, y=0]
Ponto diagonal = new Ponto(5);   // Ponto[x=5, y=5]
```

---

### 6. Records podem ter métodos

Records não são apenas cascas de dados. Eles podem ter métodos de instância, métodos estáticos e implementar interfaces :

```java
public record Circulo(double raio) implements Forma {
    // Método de instância
    @Override
    public double area() {
        return Math.PI * raio * raio;
    }

    @Override
    public double perimetro() {
        return 2 * Math.PI * raio;
    }

    // Método estático
    public static Circulo comDiametro(double diametro) {
        return new Circulo(diametro / 2);
    }
}
```

```java
Circulo c = Circulo.comDiametro(10);
System.out.println(c.raio());      // 5.0
System.out.println(c.area());      // 78.53981633974483
```

---

### 7. Records vs. classes tradicionais

| Característica | Record | Classe tradicional |
|---------------|--------|-------------------|
| **Imutabilidade** | Garantida (campos `final`) | Opcional |
| **Construtor** | Canônico automático | Manual |
| **Acessores** | `campo()` (sem `get`) | `getCampo()` (por convenção) |
| **`equals`/`hashCode`/`toString`** | Gerados automaticamente | Manuais (ou via IDE/IDL) |
| **Herança** | Não pode estender nem ser estendido | Pode estender e ser estendido |
| **Campos de instância extras** | Proibidos | Permitidos |
| **Uso típico** | DTOs, value objects, tuplas | Qualquer cenário |

---

### 8. Quando usar records

**Use records para:**
- **DTOs (Data Transfer Objects):** objetos que transportam dados entre camadas (requisições, respostas de API).
- **Value Objects:** objetos cuja identidade é definida por seus valores, não por um identificador único (coordenadas, faixas de preço, intervalos de data).
- **Chaves compostas:** múltiplos campos que juntos formam uma chave única em mapas.
- **Tuplas e pares:** quando você precisa retornar dois ou três valores de um método sem criar uma classe elaborada.
- **Objetos de configuração:** agrupamentos de parâmetros imutáveis.
- **Mensagens e eventos:** payloads em sistemas de mensageria.

**Não use records quando:**
- O objeto precisa ser mutável (tem setters, muda de estado ao longo do tempo).
- Você precisa de herança (records são finais e não podem estender outras classes).
- A identidade do objeto é definida por um ID, não pelos valores dos campos (entidades JPA, por exemplo — embora alguns frameworks estejam começando a suportar records para projeções).

---

### 9. Records e serialização

Records são naturalmente compatíveis com serialização Java, JSON (Jackson, Gson) e XML (JAXB) :

```java
public record Usuario(String nome, String email) implements Serializable { }
```

Com Jackson (Spring Boot, por exemplo), records são serializados e desserializados automaticamente:

```java
// Requisição JSON: {"nome": "Ana", "email": "ana@exemplo.com"}
@PostMapping("/usuarios")
public void criar(@RequestBody Usuario usuario) {
    // usuario.nome() = "Ana", usuario.email() = "ana@exemplo.com"
}
```

---

### 10. Records e pattern matching (Java 21+)

Records combinam perfeitamente com pattern matching no `switch` e `instanceof` :

```java
sealed interface Forma permits Circulo, Retangulo { }

record Circulo(double raio) implements Forma { }
record Retangulo(double base, double altura) implements Forma { }

double area(Forma f) {
    return switch (f) {
        case Circulo c    -> Math.PI * c.raio() * c.raio();
        case Retangulo r  -> r.base() * r.altura();
        // Sem default — o compilador sabe que são todos os casos
    };
}
```

O compilador verifica exaustividade: se todas as variantes de `Forma` são records que implementam a interface selada, o `switch` cobre todos os casos sem precisar de `default`.

---

### 11. Records aninhados e locais

Records podem ser declarados dentro de classes (nested records) ou até dentro de métodos (local records), embora este último seja raro :

```java
public class Processador {
    // Record aninhado — implicitamente static
    public record Resultado(String id, boolean sucesso) { }

    public Resultado processar(String dados) {
        // ...
        return new Resultado("123", true);
    }
}
```

Records aninhados são implicitamente `static` — não têm acesso ao `this` da classe externa.

---

### 12. Limitações

- **Não podem ser abstratos.** Records são sempre `final`.
- **Não podem estender outras classes.** Todo record herda implicitamente de `java.lang.Record`.
- **Não podem declarar campos de instância além dos componentes.** Todos os campos devem estar no cabeçalho do record.
- **Os campos são sempre `private final`.** Não há como declarar um campo de instância mutável em um record.
- **Não suportam inicializadores de instância** (blocos `{ }` dentro da classe). A inicialização extra deve ser feita no construtor compacto.

---

### 13. O que levar deste guia

Records são a resposta do Java à verbosidade das classes de dados. Eles eliminam o boilerplate de construtores, getters, `equals`, `hashCode` e `toString` sem sacrificar a segurança de tipos ou a imutabilidade.

**Regras práticas:**

- **Se a classe só carrega dados, use record.** É a escolha padrão para DTOs, value objects, chaves e resposta de APIs.
- **Validação no construtor compacto.** É o lugar certo para garantir que o objeto nunca exista em estado inválido.
- **Construtores alternativos delegam ao canônico.** Use `this(...)` para oferecer conveniência sem duplicar lógica de validação.
- **Combine com sealed interfaces para hierarquias fechadas.** O compilador verifica exaustividade no pattern matching.
- **Acessores não têm `get`.** É `ponto.x()`, não `ponto.getX()`. Questão de estilo — adapte-se.

---

### Fontes

1. **Oracle — Java Language Specification (JEP 395: Records)** — Definição oficial de records, regras de compilação e semântica da classe `java.lang.Record` .  
   https://docs.oracle.com/en/java/javase/16/language/records.html

2. **Dev.java — Records** — Guia oficial com exemplos de uso, construtores compactos e implementação de interfaces .  
   https://dev.java/learn/using-records/

3. **Baeldung — Java Records** — Tutorial abrangente com construtores, métodos, serialização e limitações .  
   https://www.baeldung.com/java-records

4. **GeeksforGeeks — Java Records** — Explicação do problema que records resolvem, sintaxe, exemplos e diferenças para classes tradicionais .  
   https://www.geeksforgeeks.org/java-records/

5. **W3Schools — Java Records** — Tutorial prático com exemplos de criação, uso e métodos em records .  
   https://www.w3schools.com/java/java_records.asp

6. **Oracle — JEP 395: Records (Specification)** — Especificação técnica completa incluindo a classe `java.lang.Record`, serialização e reflexão .  
   https://openjdk.org/jeps/395