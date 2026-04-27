
## Construtores: o ritual de nascimento dos objetos

### 1. O que são construtores

Um construtor é um bloco de código chamado automaticamente quando você usa o operador `new`. Sua única responsabilidade é **inicializar o objeto** — estabelecer um estado inicial consistente para todos os atributos, garantindo que o objeto nasça válido.

Construtores não são métodos. Eles têm sintaxe similar, mas não têm tipo de retorno (nem mesmo `void`), não são herdados e só podem ser invocados via `new` ou via `this()`/`super()` dentro de outro construtor.

```java
public class Carro {
    private String marca;
    private int ano;

    // Construtor
    public Carro(String marca, int ano) {
        this.marca = marca;
        this.ano = ano;
    }
}
```

```java
Carro c = new Carro("Honda", 2024);  // Construtor executado automaticamente
```

---

### 2. O construtor padrão

Se você não escrever **nenhum** construtor, o compilador gera um construtor padrão (default constructor) sem parâmetros e com corpo vazio. Ele inicializa todos os atributos com seus valores padrão (0, `false`, `null`):

```java
public class Exemplo {
    private int valor;       // será 0
    private String texto;    // será null
    // Construtor padrão gerado automaticamente:
    // public Exemplo() { }
}
```

**Momento crítico:** no instante em que você define **qualquer** construtor, o padrão desaparece. Se você ainda precisar dele, precisa declará-lo explicitamente:

```java
public class Exemplo {
    private int valor;

    public Exemplo(int valor) {    // Construtor com parâmetro
        this.valor = valor;
    }
    // Construtor padrão NÃO é mais gerado
}

Exemplo e = new Exemplo();  // ERRO DE COMPILAÇÃO — o construtor sem argumentos não existe
```

---

### 3. Anatomia de um construtor

```
[modificador de acesso] NomeDaClasse([parâmetros]) [throws exceções] {
    // inicialização
}
```

| Componente | Exemplo | Significado |
|------------|---------|-------------|
| Modificador | `public`, `private`, `protected` | Controla quem pode criar instâncias |
| Nome | Idêntico ao nome da classe | Obrigatório — Java identifica o construtor pelo nome |
| Parâmetros | `(String nome, int idade)` | Dados necessários para inicializar |
| Corpo | `{ this.nome = nome; ... }` | Lógica de inicialização |

```java
public class Conta {
    private final String titular;
    private double saldo;

    public Conta(String titular) {
        if (titular == null || titular.isBlank()) {
            throw new IllegalArgumentException("Titular é obrigatório");
        }
        this.titular = titular;
        this.saldo = 0.0;
    }
}
```

---

### 4. Construtores com `this()`: evitando duplicação

Um construtor pode chamar outro construtor da mesma classe usando `this()`. Isso centraliza a lógica de inicialização no construtor mais completo e evita duplicação de código :

```java
public class Produto {
    private String nome;
    private double preco;
    private int estoque;

    // Construtor mais completo — faz toda a inicialização
    public Produto(String nome, double preco, int estoque) {
        if (nome == null || nome.isBlank()) {
            throw new IllegalArgumentException("Nome é obrigatório");
        }
        if (preco < 0) {
            throw new IllegalArgumentException("Preço não pode ser negativo");
        }
        this.nome = nome;
        this.preco = preco;
        this.estoque = estoque;
    }

    // Construtor com valores padrão — delega ao completo
    public Produto(String nome, double preco) {
        this(nome, preco, 0);  // Chama o construtor de cima
    }

    // Construtor minimalista — delega ao intermediário
    public Produto(String nome) {
        this(nome, 0.0, 0);    // Chama o construtor completo
    }
}
```

**Regras do `this()`:**

- Deve ser a **primeira instrução** do construtor.
- Só pode haver **um** `this()` por construtor.
- O encadeamento eventualmente chega a um construtor que chama `super()` — explícita ou implicitamente.

---

### 5. `super()`: chamando o construtor da classe pai

Toda classe tem uma superclasse. Se não houver `extends` explícito, a superclasse é `Object`. O construtor da subclasse deve chamar o construtor da superclasse — isso acontece via `super()` :

```java
class Veiculo {
    private String fabricante;

    public Veiculo(String fabricante) {
        this.fabricante = fabricante;
    }
}

class Carro extends Veiculo {
    private int portas;

    public Carro(String fabricante, int portas) {
        super(fabricante);  // Obrigatório — Veiculo não tem construtor padrão
        this.portas = portas;
    }
}
```

**Regras do `super()`:**

- Se você não escrever `super()`, o compilador insere `super()` automaticamente — uma chamada ao construtor **sem argumentos** da superclasse .
- Se a superclasse não tiver construtor sem argumentos, você é **obrigado** a chamar `super(argumentos)` explicitamente.
- Assim como `this()`, `super()` deve ser a **primeira instrução** do construtor. Portanto, um construtor não pode ter **ambos** `this()` e `super()` — um delega ao outro, e apenas o último na corrente chama `super()`.

---

### 6. Modificadores de acesso em construtores

Os modificadores controlam quem pode criar instâncias da classe :

| Modificador | Quem pode instanciar |
|-------------|---------------------|
| `public` | Qualquer classe |
| `protected` | Subclasses e classes do mesmo pacote |
| *(padrão)* | Classes do mesmo pacote |
| `private` | Apenas a própria classe |

**Construtor `private`** é usado em padrões como Singleton e em classes utilitárias que não devem ser instanciadas:

```java
public final class Matematica {
    private Matematica() {
        throw new UnsupportedOperationException("Classe utilitária — não instancie");
    }

    public static double media(double... valores) {
        return Arrays.stream(valores).average().orElse(0);
    }
}
```

---

### 7. Inicialização de atributos: ordem de execução

Quando um objeto é criado, a inicialização segue uma ordem fixa :

1. **Atributos estáticos** da superclasse (se a classe for carregada pela primeira vez).
2. **Atributos estáticos** da classe.
3. **Atributos de instância** da superclasse (valores inline e blocos de inicialização).
4. **Construtor** da superclasse.
5. **Atributos de instância** da classe (valores inline e blocos de inicialização).
6. **Construtor** da classe.

```java
class Pai {
    private int x = 10;                     // 3. Inicializado
    public Pai() {                          // 4. Executado
        System.out.println("Construtor Pai");
    }
}

class Filha extends Pai {
    private int y = 20;                     // 5. Inicializado
    public Filha() {                        // 6. Executado
        super();                            // Chama o construtor Pai (passo 4)
        System.out.println("Construtor Filha");
    }
}
```

**Blocos de inicialização de instância** executam antes do construtor, na ordem em que aparecem:

```java
public class Logger {
    private List<String> buffer;

    {  // Bloco de inicialização — executa antes de qualquer construtor
        buffer = new ArrayList<>();
        System.out.println("Bloco 1");
    }

    public Logger() {
        System.out.println("Construtor");
    }

    {  // Segundo bloco — executa depois do primeiro, antes do construtor
        System.out.println("Bloco 2");
    }
}
// Saída ao criar new Logger():
// Bloco 1
// Bloco 2
// Construtor
```

---

### 8. Construtores e exceções

Se um construtor lança uma exceção, o objeto não é criado — a referência nunca é retornada. Mas atenção: se o construtor já alocou recursos antes da exceção (arquivos abertos, conexões), esses recursos podem ficar órfãos. Use **try-with-resources** ou garanta a limpeza no bloco `catch` do código que chama `new`.

```java
public class LeitorConfig {
    private Properties props;

    public LeitorConfig(String caminho) throws IOException {
        // Se esta linha falhar, o objeto não é criado — props continua null
        try (var input = new FileInputStream(caminho)) {
            props = new Properties();
            props.load(input);
        }
    }
}
```

---

### 9. Copy constructor

Java não tem copy constructor nativo como C++, mas você pode implementar um que recebe uma instância da mesma classe e a copia :

```java
public class Pessoa {
    private String nome;
    private int idade;

    // Construtor normal
    public Pessoa(String nome, int idade) {
        this.nome = nome;
        this.idade = idade;
    }

    // Copy constructor — cria uma cópia independente
    public Pessoa(Pessoa outra) {
        this(outra.nome, outra.idade);  // ou this.nome = outra.nome; ...
    }
}
```

```java
Pessoa original = new Pessoa("Ana", 30);
Pessoa copia = new Pessoa(original);  // Cópia independente
```

Para objetos imutáveis ou records, o copy constructor é desnecessário — a imutabilidade remove a necessidade de cópia defensiva.

---

### 10. Construtores e records

Records geram automaticamente o **construtor canônico** — um construtor que recebe todos os componentes e os atribui. Você pode customizá-lo de forma compacta :

```java
public record Produto(String nome, double preco) {
    // Construtor compacto — validação sem declarar parâmetros novamente
    public Produto {
        if (nome == null || nome.isBlank()) {
            throw new IllegalArgumentException("Nome é obrigatório");
        }
        if (preco < 0) {
            throw new IllegalArgumentException("Preço não pode ser negativo");
        }
        // Atribuição automática: this.nome = nome; this.preco = preco;
    }
}
```

O construtor compacto não declara parâmetros nem atribuições — ele apenas faz validações e ajustes. A atribuição é implícita no final.

Também é possível declarar construtores alternativos que delegam ao canônico:

```java
public record Ponto(int x, int y) {
    public Ponto() {
        this(0, 0);  // Delega ao canônico
    }

    public Ponto(int valor) {
        this(valor, valor);  // x e y iguais
    }
}
```

---

### 11. O que **não** fazer em construtores

**Não chame métodos sobrescritíveis.** Se o construtor chama um método que a subclasse sobrescreve, a versão da subclasse será executada — mas a subclasse ainda não terminou de inicializar. Resultado: comportamento imprevisível e bugs sutis :

```java
class Pai {
    public Pai() {
        inicializar();  // Perigoso — chama método sobrescritível
    }

    protected void inicializar() { }
}

class Filha extends Pai {
    private String dado;

    public Filha() {
        super();               // Chama Pai() → que chama inicializar()
        this.dado = "valor";   // ainda não executou
    }

    @Override
    protected void inicializar() {
        System.out.println(dado.length());  // NullPointerException! dado ainda é null
    }
}
```

**Não faça I/O pesada.** Construtores devem ser rápidos e previsíveis. Conexões de rede, leitura de arquivos, consultas a banco de dados — tudo isso pertence a métodos de fábrica ou a um processo de inicialização separado.

**Não lance `this` antes da inicialização completa.** Passar `this` para outro objeto dentro do construtor expõe um objeto parcialmente inicializado.

---

### 12. Construtores vs. fábricas estáticas

Métodos de fábrica estáticos são uma alternativa a construtores públicos. Oferecem vantagens que construtores não têm :

```java
public class Usuario {
    private String nome;
    private String email;
    private boolean ativo;

    // Construtor privado — ninguém de fora instancia diretamente
    private Usuario(String nome, String email, boolean ativo) {
        this.nome = nome;
        this.email = email;
        this.ativo = ativo;
    }

    // Fábrica estática com nome expressivo
    public static Usuario criarAtivo(String nome, String email) {
        return new Usuario(nome, email, true);
    }

    public static Usuario criarInativo(String nome, String email) {
        return new Usuario(nome, email, false);
    }
}
```

**Vantagens das fábricas estáticas sobre construtores:**

- **Nomes expressivos:** `Usuario.criarAtivo()` é mais claro que `new Usuario(nome, email, true)`.
- **Não precisam criar um objeto novo:** podem retornar instâncias cacheadas (ex: `Integer.valueOf()`).
- **Podem retornar subtipos:** a fábrica pode decidir qual implementação retornar.

---

### 13. O que levar deste guia

O construtor é o guardião do estado do objeto. Sua missão é simples e inegociável: **o objeto deve nascer válido**. Se um construtor permite que um objeto exista em estado inconsistente, ele falhou.

**Regras práticas:**

- **Valide no construtor.** Nulos, valores negativos, strings vazias — rejeite no ponto de entrada, não depois.
- **Centralize a inicialização.** Use `this()` para evitar duplicação entre construtores sobrecarregados.
- **Prefira construtores enxutos.** Se a inicialização é complexa (I/O, validações extensas), considere um método de fábrica estático.
- **Documente pré-condições.** Se o construtor exige argumentos não-nulos ou positivos, deixe isso claro.
- **Não abuse de parâmetros.** Se o construtor recebe 8 parâmetros, considere um builder ou um record como parâmetro.

---

### Fontes

1. Cleverence, "Java Classes and Objects: Complete Tutorial with Examples and Best Practices" (2026) — inicialização de objetos, sobrecarga de construtores, `this()`, `super()` e copy constructors 
2. Baeldung, "A Guide to Java Constructors" — anatomia do construtor, construtor padrão, `this()` e `super()`, cópia e boas práticas 
3. GeeksforGeeks, "Java Constructors" — tipos de construtores, sobrecarga, `this()` e `super()`, construtores privados e copy constructors 
4. Dev.java, "Providing Constructors for Your Classes" (Oracle) — documentação oficial sobre inicialização de objetos, `super()` implícito e ordem de execução 
5. Stack Overflow via Kiwix, "What is a constructor in Java?" — definição canônica, diferenças entre construtor e método, e regras de `this()` e `super()` 
6. Oracle, "Java Language Specification — Chapter 8: Classes, Section 8.8: Constructor Declarations" — especificação oficial da linguagem sobre declaração e comportamento de construtores