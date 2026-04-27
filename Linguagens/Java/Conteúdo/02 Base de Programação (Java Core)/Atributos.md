## Atributos: os dados que dão forma aos objetos

### 1. O que são atributos

Atributos (também chamados de campos, membros de dados ou variáveis de instância) são as variáveis declaradas dentro de uma classe, fora de qualquer método. Eles armazenam o **estado** do objeto — os dados que distinguem uma instância de outra.

Enquanto variáveis locais existem apenas durante a execução de um método, atributos existem enquanto o objeto existir. Eles nascem com `new` e morrem com o Garbage Collector.

```java
public class Carro {
    // Atributos — definem o estado de cada Carro
    String marca;
    int ano;
    boolean ligado;
}
```

Cada objeto `Carro` carrega sua própria cópia dos três atributos. `carro1.marca` pode ser "Honda" enquanto `carro2.marca` é "Toyota" — os valores são independentes.

---

### 2. Atributos de instância vs. atributos estáticos

**Atributos de instância** pertencem a cada objeto. Cada instância tem sua própria cópia:

```java
public class Contador {
    private int valor = 0;  // cada Contador tem seu próprio 'valor'

    public void incrementar() {
        this.valor++;
    }
}
```

```java
Contador a = new Contador();
Contador b = new Contador();
a.incrementar();
System.out.println(a.getValor());  // 1
System.out.println(b.getValor());  // 0 — independente
```

**Atributos estáticos** (`static`) pertencem à classe, não às instâncias. Existe uma única cópia, compartilhada por todos os objetos :

```java
public class Contador {
    private int valor = 0;
    private static int totalCriado = 0;  // compartilhado

    public Contador() {
        totalCriado++;  // incrementa o contador global
    }

    public static int getTotalCriado() {
        return totalCriado;
    }
}
```

```java
new Contador();
new Contador();
System.out.println(Contador.getTotalCriado());  // 2 — compartilhado por todos
```

Atributos estáticos são essencialmente variáveis globais. Use-os com moderação: constantes (`static final`) são seguras; estáticos mutáveis complicam concorrência e testes.

---

### 3. Tipos de atributos por conteúdo

**Primitivos:** armazenam o valor diretamente no espaço do objeto na Heap:

```java
public class Produto {
    private double preco;     // 8 bytes dentro do objeto
    private int quantidade;   // 4 bytes dentro do objeto
    private boolean ativo;    // aproximadamente 1 byte
}
```

**Referências:** armazenam um ponteiro para outro objeto. O objeto referenciado está em outro lugar da Heap:

```java
public class Pedido {
    private String cliente;     // ponteiro para um objeto String
    private List<Item> itens;   // ponteiro para um ArrayList
    private LocalDate data;     // ponteiro para um objeto LocalDate
}
```

O atributo `cliente` é apenas uma referência. O objeto String ao qual ele aponta vive separadamente. Isso é crucial para entender uso de memória e comportamento do GC.

---

### 4. Modificadores de acesso

Os modificadores controlam quem pode ler e escrever o atributo :

| Modificador | Mesma classe | Mesmo pacote | Subclasse | Qualquer lugar |
|-------------|--------------|--------------|-----------|----------------|
| `private` | Sim | Não | Não | Não |
| *(padrão)* | Sim | Sim | Não | Não |
| `protected` | Sim | Sim | Sim | Não |
| `public` | Sim | Sim | Sim | Sim |

**A regra de ouro:** atributos devem ser `private`, sempre. Se precisar expor um dado, faça via métodos — assim você mantém controle sobre leitura e escrita, pode validar valores e mudar a implementação interna sem quebrar quem usa a classe .

---

### 5. Encapsulamento com getters e setters

O encapsulamento não é sobre ter getters e setters para tudo — é sobre controlar o acesso ao estado :

**Setter com validação:** o objeto nunca fica em estado inválido porque a validação acontece na entrada:

```java
public class Conta {
    private double saldo;

    public void depositar(double valor) {
        if (valor <= 0) {
            throw new IllegalArgumentException("Valor deve ser positivo");
        }
        this.saldo += valor;
    }

    public double getSaldo() {
        return saldo;
    }

    // Sem setSaldo() — o saldo só muda via depositar() e sacar()
}
```

**Getter defensivo:** para atributos mutáveis (listas, mapas), nunca devolva o objeto interno diretamente:

```java
public class Time {
    private final List<String> jogadores = new ArrayList<>();

    public List<String> getJogadores() {
        return Collections.unmodifiableList(jogadores);  // visão somente leitura
        // ou: return new ArrayList<>(jogadores);  // cópia defensiva
    }
}
```

Se você expuser a referência interna, qualquer código externo pode modificar a lista sem que o `Time` saiba — quebrando o encapsulamento.

---

### 6. Atributos `final`: imutabilidade

`final` em atributos significa: uma vez inicializado, não pode ser modificado .

```java
public class Pessoa {
    private final String cpf;       // nunca muda após construção
    private final LocalDate dataNascimento;
    private String nome;            // pode mudar (casamento, por exemplo)

    public Pessoa(String cpf, LocalDate dataNascimento, String nome) {
        this.cpf = cpf;
        this.dataNascimento = dataNascimento;
        this.nome = nome;
    }
}
```

Atributos `final` garantem que invariantes se mantenham. Eles também tornam o objeto mais seguro em ambientes concorrentes — a JVM garante que um campo `final` tem seu valor visível para todas as threads após a conclusão do construtor .

---

### 7. Inicialização de atributos

Atributos podem ser inicializados de várias formas. A ordem de execução é fixa :

**1. Valores padrão:** se nada for especificado, atributos recebem valores default:

```java
public class Exemplo {
    int inteiro;       // 0
    double decimal;    // 0.0
    boolean flag;      // false
    String texto;      // null (referências não inicializadas são null)
}
```

**2. Inicializador inline:** valor atribuído na declaração:

```java
public class Configuracao {
    private int timeout = 30;
    private String endpoint = "https://api.exemplo.com";
    private List<String> permitidos = new ArrayList<>();
}
```

**3. Bloco de inicialização:** executado antes do construtor:

```java
public class Logger {
    private List<String> buffer;
    {
        buffer = new ArrayList<>();
        System.out.println("Bloco de inicialização executado");
    }
}
```

**4. Construtor:** última palavra na inicialização. Sobrescreve valores inline se atribuir novamente:

```java
public class Usuario {
    private String role = "USER";  // valor inicial padrão

    public Usuario(String role) {
        this.role = role;  // sobrescreve se um valor for passado
    }
}
```

---

### 8. Atributos em Records

Records (Java 16+) geram automaticamente atributos `private final` para cada componente :

```java
public record Produto(String nome, double preco, int estoque) {}
```

O compilador gera:
- Atributos `private final String nome;`, `private final double preco;`, `private final int estoque;`
- Construtor que inicializa todos.
- Métodos de acesso com o nome do componente: `nome()`, `preco()`, `estoque()`.
- `equals()`, `hashCode()` e `toString()`.

Records são imutáveis por definição — não há setters, e os atributos são `final`. Isso os torna thread-safe e ideais para DTOs e objetos de valor .

---

### 9. Sombreamento e `this`

Quando um parâmetro ou variável local tem o mesmo nome de um atributo, ocorre **sombreamento**: o identificador refere-se à variável local, não ao atributo. A palavra-chave `this` resolve a ambiguidade:

```java
public class Retangulo {
    private int largura;
    private int altura;

    public Retangulo(int largura, int altura) {
        this.largura = largura;  // this.largura é o atributo; largura é o parâmetro
        this.altura = altura;
    }
}
```

Sem `this`, a atribuição seria `largura = largura` — o parâmetro atribuindo a si mesmo, e o atributo permaneceria com o valor padrão (zero). Um bug sutil e frustrante.

---

### 10. Atributos transient: fora da serialização

O modificador `transient` instrui o mecanismo de serialização a ignorar o atributo. Útil para dados que podem ser reconstruídos ou que não devem ser persistidos:

```java
public class Sessao implements Serializable {
    private String usuario;
    private transient String senha;        // não será serializada
    private transient Connection db;       // conexões não são serializáveis
}
```

---

### 11. Atributos volatile: visibilidade entre threads

`volatile` garante que toda leitura do atributo vá à memória principal e toda escrita seja imediatamente visível para outras threads . O atributo nunca é cached localmente:

```java
public class FlagControle {
    private volatile boolean ativo = true;

    public void desligar() {
        this.ativo = false;  // thread B vê imediatamente
    }

    public void executar() {
        while (ativo) {  // thread A sempre lê o valor mais recente
            // processa
        }
    }
}
```

Sem `volatile`, a thread que executa o loop poderia nunca enxergar a mudança feita por outra thread, mantendo-se em loop infinito.

---

### 12. Consumo de memória

O consumo de memória dos atributos varia:

| Tipo de atributo | Espaço aproximado |
|------------------|-------------------|
| `byte`, `boolean` | ~1 byte (mas alinhamento pode expandir) |
| `short`, `char` | 2 bytes |
| `int`, `float` | 4 bytes |
| `long`, `double` | 8 bytes |
| Referência (`String`, `List`, etc.) | 4 bytes (32-bit) ou 8 bytes (64-bit com compressed OOPs) |
| Objeto inteiro (cabeçalho + dados) | 12-16 bytes de overhead + atributos |

O alinhamento de objetos na JVM HotSpot arredonda o tamanho para múltiplos de 8 bytes. Três campos `byte` podem ocupar 24 bytes devido ao overhead do objeto e alinhamento — uma consideração importante em estruturas de dados massivas.

---

### 13. O que levar deste guia

Atributos são o alicerce do estado em Java. Trate-os com disciplina:

- **`private` sempre.** Acesso público só com justificativa muito forte.
- **Imutabilidade sempre que possível.** Atributos `final`, objetos imutáveis via records, cópias defensivas para coleções.
- **Validação na entrada.** Construtores e setters são as fronteiras por onde o estado entra — é ali que você deve garantir que o objeto nunca fique inválido.
- **Cuidado com estáticos mutáveis.** São globais, são perigosos, complicam tudo.
- **Entenda onde os atributos vivem.** Primitivos são inline no objeto; referências apontam para outros objetos na Heap. Isso afeta consumo de memória, comportamento do GC e performance.

---

### Fontes

1. Baeldung, "Java Classes and Objects" — definição de atributos de instância, estáticos, modificadores de acesso, encapsulamento e inicialização 
2. GeeksforGeeks, "Attributes in Java" — explicação de atributos, `static`, `final`, `transient`, `volatile` e padrões de encapsulamento 
3. 三W点biancheng点net, "Java类的属性（成员变量）" — guia detalhado sobre tipos de atributos, inicialização padrão, blocos de inicialização e `this` 
4. Aboullaite, "Java Memory Consumption: Primitives vs Objects" — análise de consumo de memória incluindo overhead de objetos na JVM HotSpot 
5. Cleverence, "Java Classes and Objects" (2026) — encapsulamento, getters/setters, atributos `final`, records e boas práticas 
6. Oracle, "Java Language Specification — Chapter 8: Classes" — especificação oficial de classes, campos, modificadores e inicialização