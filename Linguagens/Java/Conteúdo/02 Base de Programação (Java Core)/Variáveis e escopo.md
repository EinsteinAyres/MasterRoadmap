## Variáveis e escopo: onde seus dados existem

### 1. O que é uma variável

Uma variável é um espaço nomeado na memória que armazena um valor — seja um primitivo ou uma referência para um objeto. Declarar uma variável significa reservar esse espaço e dar-lhe um nome e um tipo. Inicializá-la significa atribuir-lhe um valor.

```java
int idade;              // Declaração — reserva 4 bytes na Stack
idade = 30;             // Inicialização — armazena o valor 30

String nome = "Ana";    // Declaração + inicialização combinadas
```

Em Java, toda variável tem três propriedades fundamentais:
- **Nome:** o identificador pelo qual você a acessa.
- **Tipo:** o que ela pode armazenar (`int`, `String`, `List<Produto>`).
- **Escopo:** onde no código ela é visível e utilizável.

---

### 2. Tipos de variáveis por localização

Java tem quatro categorias de variáveis, classificadas por onde são declaradas e quanto tempo vivem:

| Categoria | Declarada em | Ciclo de vida | Valor padrão |
|-----------|--------------|---------------|--------------|
| **Variável de instância** (atributo) | Dentro da classe, fora de métodos | Vive enquanto o objeto existir | Sim (0, `false`, `null`) |
| **Variável de classe** (estática) | Dentro da classe, com `static` | Vive enquanto a classe estiver carregada | Sim (0, `false`, `null`) |
| **Variável local** | Dentro de método, construtor ou bloco | Vive até o bloco terminar | **Não** — deve ser inicializada antes do uso |
| **Parâmetro** | Na assinatura do método | Vive durante a execução do método | Recebe o valor do argumento da chamada |

---

### 3. Variáveis de instância (atributos)

São declaradas dentro de uma classe, sem o modificador `static`. Cada objeto tem sua própria cópia. Elas definem o **estado** do objeto.

```java
public class Conta {
    private String titular;     // Variável de instância — cada Conta tem a sua
    private double saldo;       // Variável de instância
    private boolean ativa;      // Variável de instância
}
```

```java
Conta c1 = new Conta();
Conta c2 = new Conta();
// c1 e c2 têm cópias independentes de 'titular', 'saldo' e 'ativa'
```

**Características:**
- Recebem valor padrão automaticamente se não inicializadas: `0` para números, `false` para boolean, `null` para referências.
- Vivem na Heap, dentro do objeto.
- São acessíveis por todos os métodos da classe (respeitando `private`, `protected`, etc.).

---

### 4. Variáveis de classe (estáticas)

São declaradas com o modificador `static`. Pertencem à **classe**, não às instâncias. Existe uma única cópia, compartilhada por todos os objetos:

```java
public class Conexao {
    private static int conexoesAtivas = 0;  // Compartilhada por todas as instâncias
    private String host;

    public Conexao(String host) {
        this.host = host;
        conexoesAtivas++;                   // Incrementa o contador global
    }
}
```

**Características:**
- Recebem valor padrão automaticamente.
- Vivem na área de metadados da classe (Metaspace), não na Heap de objetos.
- Podem ser acessadas diretamente pelo nome da classe: `Conexao.getConexoesAtivas()`.
- **Cuidado com estáticos mutáveis:** são variáveis globais — complicam concorrência, testes e raciocínio sobre o código. Prefira `static final` para constantes.

---

### 5. Variáveis locais

São declaradas dentro de um método, construtor, inicializador ou bloco. Existem apenas durante a execução desse bloco:

```java
public void exemplo() {
    int x = 10;                    // Variável local — vive na Stack
    String mensagem = "Valor: ";   // Referência local

    if (x > 5) {
        int y = x * 2;             // Variável local — escopo do if
        System.out.println(mensagem + y);
    }
    // y já não existe aqui
}
```

**Características:**
- **Não** recebem valor padrão. O compilador exige que sejam inicializadas antes do primeiro uso.
- Vivem na Stack, no frame do método.
- Extremamente rápidas: alocação e liberação são operações de ponteiro.
- Primitivos locais são os dados mais baratos que existem em Java.

**Erro comum:**

```java
public void exemplo() {
    int x;
    System.out.println(x);  // ERRO DE COMPILAÇÃO — variável não inicializada
}
```

---

### 6. Parâmetros

São as variáveis declaradas na assinatura do método. Recebem seus valores dos argumentos passados na chamada:

```java
public void cadastrar(String nome, int idade) {
    // 'nome' e 'idade' são parâmetros — agem como variáveis locais
    System.out.println(nome + " tem " + idade + " anos");
}
```

**Características:**
- Inicializados automaticamente com os valores da chamada.
- Comportam-se como variáveis locais dentro do método — escopo limitado ao corpo do método.
- Para objetos, o parâmetro é uma **cópia da referência**, não uma cópia do objeto.

---

### 7. Escopo: a regra das chaves

Escopo é a região do código onde uma variável pode ser acessada. Em Java, o escopo é delimitado por **chaves** `{}`:

```java
public class Escopos {
    private int atributo = 1;           // Escopo: toda a classe

    public void metodo(int parametro) { // Escopo do parâmetro: todo o método
        int local = 2;                  // Escopo: do ponto da declaração até o fim do método

        if (parametro > 0) {
            int condicional = 3;        // Escopo: apenas dentro destas chaves
            System.out.println(condicional);  // OK
        }
        // System.out.println(condicional);  // ERRO — fora do escopo

        for (int i = 0; i < 10; i++) {  // 'i' declarada no for
            System.out.println(i);      // OK — dentro do escopo
        }
        // System.out.println(i);       // ERRO — 'i' morreu com o for
    }
}
```

**Regras fundamentais:**

- Uma variável é visível a partir do ponto em que foi declarada até o fim do bloco que a contém.
- Blocos aninhados podem acessar variáveis dos blocos externos.
- Blocos externos **não** podem acessar variáveis de blocos internos.
- Variáveis locais são destruídas quando o bloco termina — o espaço na Stack é liberado imediatamente.

---

### 8. Sombreamento (Shadowing)

Quando uma variável local tem o mesmo nome de um atributo, a variável local **sombreia** o atributo dentro do seu escopo:

```java
public class Exemplo {
    private int valor = 10;  // Atributo da classe

    public void metodo(int valor) {  // Parâmetro 'valor' sombreia o atributo
        System.out.println(valor);   // Refere-se ao PARÂMETRO, não ao atributo
        System.out.println(this.valor);  // 'this' acessa o atributo explicitamente
    }
}
```

Dentro de `metodo`, `valor` (sem `this`) refere-se ao parâmetro. Para acessar o atributo, é obrigatório usar `this.valor`.

**Evite sombreamento sempre que possível.** Dê nomes distintos a parâmetros e atributos, ou use `this.` de forma consistente. Sombreamento não intencional é fonte de bugs sutis, especialmente em construtores.

---

### 9. Variáveis `final`: uma vez atribuída, para sempre

O modificador `final` transforma uma variável em uma constante — ela só pode receber valor **uma vez**:

```java
// Variável local final — comum em lambdas e classes anônimas
final int maximo = 100;
// maximo = 200;  // ERRO — não pode ser reatribuída

// Atributo final — deve ser inicializado até o fim do construtor
public class Pessoa {
    private final String cpf;  // Imutável após a construção

    public Pessoa(String cpf) {
        this.cpf = cpf;  // Única chance de atribuir
    }
}
```

**Onde `final` importa de verdade:**
- **Atributos `final`:** garantem que o estado não será alterado após a construção — thread-safe por natureza.
- **Variáveis locais `final` (ou efetivamente final):** exigidas para serem capturadas por lambdas e classes anônimas. Uma variável é "efetivamente final" se nunca é reatribuída, mesmo sem o modificador `final` explícito.

```java
int x = 10;
// x = 20;  // Se descomentar, 'x' deixa de ser efetivamente final
Runnable r = () -> System.out.println(x);  // OK — x é efetivamente final
```

---

### 10. Escopo de variáveis em lambdas

Lambdas podem capturar variáveis do escopo externo, mas apenas se forem **final** ou **efetivamente final**:

```java
public void exemplo() {
    String prefixo = "Valor: ";  // Efetivamente final

    List<Integer> numeros = List.of(1, 2, 3);
    numeros.forEach(n -> {
        System.out.println(prefixo + n);  // OK — prefixo é efetivamente final
    });

    // prefixo = "Outro: ";  // Se descomentar, a lambda acima não compila
}
```

A restrição existe porque lambdas podem ser executadas em outro momento e em outra thread. Se a variável externa pudesse mudar, o comportamento seria imprevisível.

---

### 11. Escopo de variáveis em blocos `try`

Variáveis declaradas dentro do bloco `try` não são visíveis no `catch` ou `finally`:

```java
try {
    int resultado = 10 / 0;       // Escopo: apenas bloco try
} catch (ArithmeticException e) {
    // System.out.println(resultado);  // ERRO — 'resultado' não existe aqui
}
```

Se você precisa da variável após o `try`, declare-a antes:

```java
int resultado;
try {
    resultado = calcular();
} catch (Exception e) {
    resultado = valorPadrao;
}
System.out.println(resultado);  // OK — visível aqui
```

---

### 12. Ciclo de vida e memória: quem vive onde?

| Categoria | Onde vive | Quando nasce | Quando morre |
|-----------|-----------|--------------|--------------|
| **Variável de instância** | Heap (dentro do objeto) | Com `new` | Com o GC (quando o objeto não tem mais referências) |
| **Variável de classe** | Metaspace (área da classe) | Quando a classe é carregada | Quando a classe é descarregada (raro) |
| **Variável local** | Stack (frame do método) | Na declaração | Quando o bloco termina |
| **Parâmetro** | Stack (frame do método) | Na chamada do método | Quando o método retorna |

Esta distinção tem implicações de performance: variáveis locais e parâmetros são incrivelmente rápidos — alocados e liberados com ajustes de ponteiro. Atributos de instância são mais caros (alocação na Heap + GC). Atributos estáticos são efetivamente eternos — nunca são coletados, o que pode causar vazamentos de memória se você acumular referências em coleções estáticas.

---

### 13. Boas práticas de escopo

**Menor escopo possível.** Declare a variável o mais próximo possível de onde será usada. Isso reduz a carga mental de rastrear onde a variável é modificada e minimiza o risco de uso acidental após o ponto correto:

```java
// Ruim — 'resultado' fica disponível por todo o método
int resultado;
if (condicao) {
    resultado = processarA();
} else {
    resultado = processarB();
}

// Melhor — 'resultado' existe apenas onde tem significado
int resultado = condicao ? processarA() : processarB();
```

**Inicialize na declaração sempre que possível.** Evita o estado "declarado mas não inicializado" e torna o código mais legível.

**Evite reutilizar variáveis locais.** Uma variável deve ter um propósito único. Reutilizar a mesma variável para coisas diferentes no mesmo método confunde o leitor e dificulta a depuração.

**Prefira `final` para variáveis que não devem mudar.** Comunica intenção e previne bugs por reatribuição acidental.

**Não abuse de estáticos mutáveis.** Se precisar de estado compartilhado, encapsule-o em um objeto com controle de acesso e sincronização apropriados.

---

### 14. Resumo em uma frase

Variáveis são os post-its da memória: algumas duram a execução inteira (estáticas), outras duram a vida do objeto (atributos), e as melhores de todas duram apenas o necessário (variáveis locais) — e saber escolher o escopo certo é o que impede que seu código vire um quadro de cortiça onde ninguém mais encontra nada.

---

### Fontes

1. Oracle, "Java Language Specification — Chapter 6: Names, Section 6.3: Scope of a Declaration" — definição canônica de escopo, sombreamento e regras de visibilidade 
2. Oracle, "Java Tutorials – Variables" — documentação oficial sobre tipos de variáveis (instância, classe, local, parâmetro), escopo e inicialização 
3. Dev.java, "Declaring Member Variables" — variáveis de instância, estáticas, modificadores de acesso e valores padrão 
4. 廖雪峰的官方网站, "Java变量作用域" — explicação prática de escopo com exemplos, sombreamento e `final` 
5. JavaRush, "Переменные в Java: типы, область видимости" — tipos de variáveis, escopo, ciclo de vida e melhores práticas 
6. Baeldung, "Java Scope" — guia detalhado sobre escopo de variáveis locais, de instância, estáticas, lambdas e blocos `try`