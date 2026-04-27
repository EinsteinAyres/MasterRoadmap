
## Exceções: quando o código segue pelo caminho inesperado

### 1. O problema que exceções resolvem

Sem exceções, o tratamento de erros se mistura com a lógica de negócio. Cada operação que pode falhar exige verificação imediata do código de retorno, e o fluxo normal do programa desaparece sob camadas de `if`:

```java
// Sem exceções — o "caminho feliz" se perde na verificação de erros
int resultado = conectar();
if (resultado == OK) {
    resultado = autenticar();
    if (resultado == OK) {
        resultado = buscarDados();
        if (resultado == OK) {
            processar(dados);
        } else {
            tratarErroBusca();
        }
    } else {
        tratarErroAutenticacao();
    }
} else {
    tratarErroConexao();
}
```

**Exceções** separam o código que lida com problemas do código que implementa a lógica principal. O "caminho feliz" fica limpo; os tratamentos de erro, organizados em blocos próprios .

```java
try {
    conectar();
    autenticar();
    buscarDados();
    processar(dados);
} catch (ErroConexao e) {
    tratarErroConexao();
} catch (ErroAutenticacao e) {
    tratarErroAutenticacao();
} catch (ErroBusca e) {
    tratarErroBusca();
}
```

---

### 2. A hierarquia das exceções

Toda exceção em Java é uma instância de `java.lang.Throwable` ou de uma de suas subclasses. A árvore se divide em três ramos principais :

```
Throwable
├── Error                    — problemas graves, não se espera que o programa recupere
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── ...
│
└── Exception                — condições que o programa pode querer capturar
    ├── RuntimeException     — exceções não verificadas (unchecked)
    │   ├── NullPointerException
    │   ├── IllegalArgumentException
    │   ├── IndexOutOfBoundsException
    │   └── ...
    │
    └── (demais Exception)   — exceções verificadas (checked)
        ├── IOException
        ├── SQLException
        └── ...
```

---

### 3. Checked vs. Unchecked: a decisão de design mais debatida do Java

**Checked exceptions** (exceções verificadas) são as que estendem `Exception` mas **não** `RuntimeException`. O compilador **obriga** quem chama um método que lança uma checked exception a: capturá-la com `try-catch` **ou** declará-la com `throws` na assinatura .

```java
// Método que declara uma checked exception
public String lerArquivo(String caminho) throws IOException {
    return Files.readString(Path.of(caminho));
}

// Quem chama é obrigado a tratar:
try {
    String conteudo = lerArquivo("dados.txt");
} catch (IOException e) {
    System.err.println("Arquivo não encontrado: " + e.getMessage());
}
```

**Unchecked exceptions** (exceções não verificadas) são as que estendem `RuntimeException` (ou `Error`). O compilador **não obriga** ninguém a capturá-las ou declará-las. Elas representam **bugs no código**: passar `null` onde não deveria, acessar índice inválido, violar invariantes .

```java
// Método que lança unchecked exception — ninguém é obrigado a tratar
public void setNome(String nome) {
    if (nome == null) {
        throw new IllegalArgumentException("Nome não pode ser null");
    }
    this.nome = nome;
}
```

**A regra prática:**
- Use **checked exceptions** quando o chamador **pode razoavelmente se recuperar** do erro (arquivo não encontrado → pedir outro caminho; rede indisponível → tentar novamente).
- Use **unchecked exceptions** para **bugs de programação** (argumentos inválidos, estado corrompido, violação de contrato). Se o chamador não tem como se recuperar, não o force a capturar.

---

### 4. A anatomia do tratamento de exceções

**Blocos `try-catch-finally`:**

```java
try {
    // Código que pode lançar exceção
    Arquivo arquivo = abrirArquivo(caminho);
    processar(arquivo);
} catch (ArquivoNaoEncontradoException e) {
    // Tratamento específico
    System.err.println("Arquivo não encontrado: " + caminho);
} catch (IOException e) {
    // Tratamento genérico
    System.err.println("Erro de I/O: " + e.getMessage());
} finally {
    // Executado SEMPRE — havendo exceção ou não
    fecharRecursos();
}
```

O `finally` é o lugar certo para liberar recursos (fechar conexões, arquivos, streams), porque ele executa mesmo que o `try` lance exceção ou que o `catch` faça `return`.

**Multi-catch (Java 7+):** um único `catch` pode capturar vários tipos de exceção, separados por `|`:

```java
try {
    processar();
} catch (IOException | SQLException e) {
    // Tratamento comum para ambas
}
```

**try-with-resources (Java 7+):** para recursos que implementam `AutoCloseable`, o `try` gerencia o fechamento automaticamente. O `finally` torna-se desnecessário :

```java
// Antes — finally manual e verboso
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("dados.txt"));
    processar(reader);
} catch (IOException e) {
    tratarErro(e);
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            // O que fazer com uma exceção no close?
        }
    }
}

// Depois — try-with-resources
try (BufferedReader reader = new BufferedReader(new FileReader("dados.txt"))) {
    processar(reader);
} catch (IOException e) {
    tratarErro(e);
}
// reader.close() é chamado automaticamente
```

---

### 5. Criando suas próprias exceções

**Checked exception personalizada:**

```java
public class SaldoInsuficienteException extends Exception {
    private final double saldoAtual;
    private final double valorSolicitado;

    public SaldoInsuficienteException(double saldoAtual, double valorSolicitado) {
        super(String.format("Saldo insuficiente: R$ %.2f para sacar R$ %.2f",
                saldoAtual, valorSolicitado));
        this.saldoAtual = saldoAtual;
        this.valorSolicitado = valorSolicitado;
    }

    public double getSaldoAtual() { return saldoAtual; }
    public double getValorSolicitado() { return valorSolicitado; }
}
```

**Unchecked exception personalizada:**

```java
public class ProdutoInvalidoException extends RuntimeException {
    private final String codigo;

    public ProdutoInvalidoException(String codigo, String motivo) {
        super("Produto " + codigo + ": " + motivo);
        this.codigo = codigo;
    }

    public String getCodigo() { return codigo; }
}
```

**Regras para exceções personalizadas:**
- Inclua uma mensagem clara (passada ao construtor da superclasse).
- Armazene informações relevantes para diagnóstico (saldo, código do produto, timestamp).
- Documente com `@throws` na Javadoc dos métodos que as lançam.

---

### 6. `try-catch` vs. `throws`: quem resolve o problema?

**`throws`** transfere a responsabilidade para o chamador:

```java
public void transferir(Conta origem, Conta destino, double valor)
        throws SaldoInsuficienteException, ContaInativaException {
    if (!origem.isAtiva()) throw new ContaInativaException(origem);
    if (origem.getSaldo() < valor) throw new SaldoInsuficienteException(origem, valor);
    origem.debitar(valor);
    destino.creditar(valor);
}
```

**`try-catch`** resolve o problema no local:

```java
try {
    transferir(contaA, contaB, 500);
} catch (SaldoInsuficienteException e) {
    sugerirEmprestimo(e.getSaldoAtual(), e.getValorSolicitado());
} catch (ContaInativaException e) {
    notificarUsario("Conta inativa: " + e.getConta());
}
```

**A regra: lance cedo, capture tarde.** Lance a exceção assim que o erro for detectado (na camada que tem informação sobre o que deu errado). Capture apenas no ponto onde você pode fazer algo útil — loggar, notificar o usuário, tentar uma alternativa.

---

### 7. Más práticas que você deve evitar

**Não capture `Exception` genérico sem motivo.** Isso engole exceções que você não esperava, escondendo bugs:

```java
// Péssimo — esconde NullPointerException e outros bugs
try {
    processar();
} catch (Exception e) {
    // Silencia tudo — nunca faça isso
}
```

**Não faça catch vazio.** Um `catch` sem nenhuma ação (nem log) é um dos piores pecados em Java. Se você acha que uma exceção "nunca vai acontecer", pelo menos registre um log com o stack trace:

```java
// Ruim
try { ... } catch (ExcecaoRara e) { }

// Aceitável (se realmente acredita que não vai acontecer)
try { ... } catch (ExcecaoRara e) {
    log.error("Ocorreu o impossível", e);
}
```

**Não use exceções para controle de fluxo.** Exceções são caras (capturar uma exceção é ordens de magnitude mais lento que um `if`). Não use `try-catch` como substituto de validação:

```java
// Péssimo — usar exceção como validação
try {
    int numero = Integer.parseInt(texto);
} catch (NumberFormatException e) {
    // Tratar como não-número
}

// Bom — validar antes
if (texto.matches("\\d+")) {
    int numero = Integer.parseInt(texto);
}
```

**Nunca lance `Exception` ou `RuntimeException` genéricas.** Crie exceções com nomes significativos que descrevem o problema: `SaldoInsuficienteException`, não `RuntimeException`.

**Não ignore o `finally` para liberar recursos.** Use try-with-resources. Se não puder, libere no `finally` com `try-catch` aninhado.

---

### 8. Exceções em streams e lambdas

Streams e lambdas não combinam bem com checked exceptions. O compilador reclama se uma lambda lança checked exception dentro de um `Stream.map()`:

```java
// Não compila — IOException é checked
List<String> conteudos = arquivos.stream()
    .map(arquivo -> Files.readString(Path.of(arquivo)))
    .toList();
```

**Soluções:**
- Capture a checked exception dentro da lambda e a converta em unchecked:

```java
List<String> conteudos = arquivos.stream()
    .map(arquivo -> {
        try {
            return Files.readString(Path.of(arquivo));
        } catch (IOException e) {
            throw new UncheckedIOException(e);
        }
    })
    .toList();
```

- Extraia para um método helper que faz essa conversão.

---

### 9. O que levar deste guia

Exceções são o mecanismo do Java para separar o tratamento de erros da lógica principal. Bem usadas, tornam o código mais limpo e robusto. Mal usadas, criam bugs silenciosos e código impossível de depurar.

**Regras práticas:**

- **Checked para erros recuperáveis, unchecked para bugs.** Se o chamador pode fazer algo a respeito, use checked. Se é violação de contrato ou estado inválido, use unchecked.
- **Lance cedo, capture tarde.** Lance no ponto onde o erro é detectado; capture apenas onde há uma ação útil a tomar.
- **Nunca engula exceções sem registro.** Um `catch` vazio é um bug esperando para acontecer em produção.
- **Use try-with-resources para tudo que é `AutoCloseable`.** Zero `finally` manual.
- **Crie exceções com nomes significativos e mensagens claras.** `SaldoInsuficienteException` com o saldo e o valor solicitado; nunca `RuntimeException("erro")`.
- **Não use exceções para controle de fluxo normal.** São caras e obscurecem a intenção do código.

---

### Fontes

1. **Oracle — Java Tutorials: Exceptions** — Documentação oficial sobre checked vs. unchecked, try-catch-finally e try-with-resources .  
   https://docs.oracle.com/javase/tutorial/essential/exceptions/index.html

2. **Dev.java — Exceptions** — Guia oficial sobre captura, lançamento e melhores práticas com exceções .  
   https://dev.java/learn/exceptions/

3. **Baeldung — Exception Handling in Java** — Tutorial abrangente com checked, unchecked, custom exceptions e anti-padrões .  
   https://www.baeldung.com/java-exceptions

4. **GeeksforGeeks — Exceptions in Java** — Hierarquia, tipos de exceção, try-catch, throw/throws e exceções personalizadas .  
   https://www.geeksforgeeks.org/exceptions-in-java/

5. **W3Schools — Java Exceptions** — Explicação prática com exemplos de try-catch, finally e throw .  
   https://www.w3schools.com/java/java_try_catch.asp

6. **Programiz — Java Exception Handling** — Fundamento de exceções com exemplos de checked, unchecked e try-with-resources .  
   https://www.programiz.com/java-programming/exception-handling