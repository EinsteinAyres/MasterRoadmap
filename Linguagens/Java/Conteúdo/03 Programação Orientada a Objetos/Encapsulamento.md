
## Encapsulamento: esconder para proteger

### 1. O que é encapsulamento

Encapsulamento é o princípio da orientação a objetos que consiste em **esconder os detalhes internos** de uma classe e expor apenas o que é necessário para quem a usa. Não se trata apenas de tornar campos `private` — trata-se de proteger o estado do objeto contra modificações indevidas e garantir que ele nunca fique inválido.

A metáfora clássica é o controle remoto da televisão. Você aperta o botão "aumentar volume" sem precisar saber como o circuito interno processa esse comando, quais tensões são alteradas, como o amplificador responde. A complexidade está encapsulada atrás de uma interface simples. Se o fabricante mudar o circuito interno, o botão continua funcionando igual — seu uso não muda .

Em Java, o encapsulamento é implementado com **modificadores de acesso** e métodos públicos seletivos que controlam como o estado é lido e modificado.

---

### 2. Os quatro níveis de acesso

Java oferece quatro níveis de visibilidade, controlados por modificadores que se aplicam a classes, campos, métodos e construtores :

| Modificador | Mesma classe | Mesmo pacote | Subclasse | Qualquer lugar |
|-------------|--------------|--------------|-----------|----------------|
| `private` | Sim | Não | Não | Não |
| *(padrão / package-private)* | Sim | Sim | Não | Não |
| `protected` | Sim | Sim | Sim | Não |
| `public` | Sim | Sim | Sim | Sim |

**`private`** — O mais restritivo. O membro só é visível dentro da própria classe. É o pilar do encapsulamento: todos os campos devem ser `private`, a menos que haja uma razão muito forte para não serem .

**`default` (package-private)** — Sem modificador explícito. Visível apenas para classes do mesmo pacote. Útil para classes e métodos auxiliares que não devem fazer parte da API pública, mas precisam ser acessados por outras classes próximas.

**`protected`** — Visível no mesmo pacote e em subclasses (mesmo que em pacotes diferentes). Permite que uma classe filha acesse membros da classe pai — essencial para herança bem projetada.

**`public`** — Visível para todos. Use para os métodos que compõem a **interface pública** da classe — aquilo que os usuários da classe devem ver e usar.

---

### 3. Campos privados, métodos públicos

O padrão mais comum de encapsulamento é declarar todos os campos como `private` e fornecer métodos `public` apenas para as operações que fazem sentido para o domínio:

```java
public class ContaBancaria {
    private String titular;
    private double saldo;
    private boolean ativa;

    public ContaBancaria(String titular) {
        if (titular == null || titular.isBlank()) {
            throw new IllegalArgumentException("Titular é obrigatório");
        }
        this.titular = titular;
        this.saldo = 0.0;
        this.ativa = true;
    }

    public void depositar(double valor) {
        if (valor <= 0) {
            throw new IllegalArgumentException("Valor deve ser positivo");
        }
        this.saldo += valor;
    }

    public boolean sacar(double valor) {
        if (valor <= 0 || valor > this.saldo) {
            return false;
        }
        this.saldo -= valor;
        return true;
    }

    public double getSaldo() {
        return saldo;
    }

    public String getTitular() {
        return titular;
    }
}
```

O campo `saldo` é `private`. Ninguém de fora pode fazer `conta.saldo = -1000`. A única forma de alterar o saldo é através de `depositar()` (que valida que o valor é positivo) e `sacar()` (que valida que há saldo suficiente). O campo `ativa` nem sequer tem getter — é um detalhe interno.

---

### 4. Getters e setters: o certo e o errado

O erro mais comum ao aprender encapsulamento é achar que ele se resume a declarar campos `private` e gerar getters e setters para todos eles automaticamente. Isso **não** é encapsulamento — é apenas tornar os campos privados com acesso público disfarçado .

**Errado — "encapsulamento" que não encapsula nada:**

```java
public class Pessoa {
    private String nome;
    private int idade;

    public String getNome() { return nome; }
    public void setNome(String nome) { this.nome = nome; }
    public int getIdade() { return idade; }
    public void setIdade(int idade) { this.idade = idade; }
}
```

Isso é equivalente a ter campos públicos. Qualquer código externo pode ler e modificar tudo, sem validação, sem restrições. O campo `idade` pode receber -500 e ninguém impedirá.

**Certo — encapsulamento de verdade:**

```java
public class Pessoa {
    private String nome;
    private int idade;

    public Pessoa(String nome, int idade) {
        if (nome == null || nome.isBlank()) {
            throw new IllegalArgumentException("Nome é obrigatório");
        }
        if (idade < 0 || idade > 150) {
            throw new IllegalArgumentException("Idade inválida");
        }
        this.nome = nome;
        this.idade = idade;
    }

    public String getNome() { return nome; }

    public int getIdade() { return idade; }

    public void fazerAniversario() {
        this.idade++;
    }

    // Sem setNome — nome não muda depois de criado
    // Sem setIdade — idade só muda via fazerAniversario()
}
```

Aqui, o encapsulamento impõe **invariantes**: um nome nunca é nulo ou vazio; uma idade nunca é negativa ou maior que 150; a idade só aumenta através de um método com semântica clara (`fazerAniversario()`), não por atribuição arbitrária .

---

### 5. Métodos de acesso defensivos

Para campos mutáveis que armazenam coleções ou objetos mutáveis, mesmo com getters você pode expor o estado interno. Um getter que retorna a referência direta para uma lista interna permite que código externo a modifique sem passar pelos seus métodos de controle:

```java
// Frágil — expõe a lista interna
public class Agenda {
    private List<String> compromissos = new ArrayList<>();

    public List<String> getCompromissos() {
        return compromissos;  // Quem chama pode fazer getCompromissos().clear()
    }
}
```

**Soluções:**

- **Visão não modificável:** retorna um wrapper que bloqueia modificações.

```java
public List<String> getCompromissos() {
    return Collections.unmodifiableList(compromissos);
}
```

- **Cópia defensiva:** retorna uma cópia. Modificações na cópia não afetam o original.

```java
public List<String> getCompromissos() {
    return new ArrayList<>(compromissos);
}
```

- **Não exponha a coleção.** Ofereça métodos específicos para as operações permitidas.

```java
public void adicionarCompromisso(String compromisso) { ... }
public void removerCompromisso(String compromisso) { ... }
public int quantidadeCompromissos() { ... }
```

---

### 6. Encapsulamento em Records

Records (Java 16+) são classes imutáveis cujo propósito é transportar dados. O encapsulamento funciona de forma diferente: os campos são implicitamente `private final`, e os métodos de acesso têm o nome do campo (sem `get`). Como são imutáveis, não há risco de modificação externa .

```java
public record Ponto(int x, int y) {
    // Construtor compacto — validação no ponto de criação
    public Ponto {
        if (x < 0 || y < 0) {
            throw new IllegalArgumentException("Coordenadas não podem ser negativas");
        }
    }
}
```

`ponto.x()` retorna o valor, mas ninguém pode alterar `x` porque não há setter e o campo é `final`. O encapsulamento é garantido pela imutabilidade.

---

### 7. Encapsulamento de classes inteiras

O encapsulamento também se aplica a classes. Se uma classe é um detalhe de implementação interno, torne-a `package-private` (sem modificador) em vez de `public`:

```java
// Classe pública — parte da API
public class Fatura {
    private List<ItemFatura> itens = new ArrayList<>();
    // ...
}

// Classe package-private — detalhe interno, invisível fora do pacote
class ItemFatura {
    private String descricao;
    private double valor;
    // ...
}
```

Quem usa `Fatura` não precisa conhecer `ItemFatura`. Se um dia você decidir armazenar os itens de outra forma, a mudança não afeta ninguém fora do pacote.

---

### 8. Benefícios do encapsulamento

**Proteção da integridade dos dados.** O estado só é alterado através de métodos controlados, que podem validar entradas e manter invariantes. Um objeto nunca fica em estado inválido porque o acesso direto aos campos é impossível .

**Flexibilidade para mudanças internas.** Você pode reescrever completamente a implementação interna sem afetar quem usa a classe. Pode trocar um `ArrayList` por um `Set`, mudar o algoritmo de cálculo de saldo, adicionar cache — desde que a interface pública permaneça a mesma, ninguém precisa saber .

**Ocultação de complexidade.** Quem usa a classe vê apenas os métodos relevantes. Os detalhes internos — estruturas de dados auxiliares, cálculos intermediários, conexões — ficam escondidos, reduzindo a carga cognitiva e a chance de uso incorreto .

**Testabilidade.** Classes encapsuladas têm fronteiras claras. Você testa a interface pública; a implementação interna pode ser modificada com segurança .

---

### 9. O que levar deste guia

Encapsulamento não é sobre getters e setters. É sobre **controlar o acesso ao estado** para que o objeto seja o único responsável por sua própria integridade.

**Regras práticas:**

- **Campos são `private`, sempre.** A única exceção são constantes (`public static final`).
- **Métodos públicos só os necessários.** Comece com o mínimo de API pública e expanda conforme necessário.
- **Validação nos pontos de entrada.** Construtores e métodos que alteram estado devem validar argumentos e impedir estados inválidos.
- **Cópia defensiva para coleções.** Nunca retorne a referência direta para uma coleção interna mutável.
- **Prefira imutabilidade quando possível.** Objetos imutáveis são encapsulados por natureza — ninguém pode alterá-los depois de criados.

---

### Fontes

1. **Oracle — Controlling Access to Members of a Class** — Documentação oficial sobre modificadores de acesso (`public`, `private`, `protected`, package-private) e encapsulamento .  
   https://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html

2. **Dev.java — Encapsulation** — Guia oficial sobre encapsulamento, proteção de estado e exposição de comportamento .  
   https://dev.java/learn/oop/encapsulation/

3. **GeeksforGeeks — Encapsulation in Java** — Definição, implementação com exemplos, benefícios e comparação com abstração .  
   https://www.geeksforgeeks.org/encapsulation-in-java/

4. **Baeldung — Java Encapsulation** — Tutorial com exemplos de getters/setters, encapsulamento com registros e melhores práticas .  
   https://www.baeldung.com/java-encapsulation

5. **W3Schools — Java Encapsulation** — Explicação prática do conceito com exemplos de getters, setters e proteção de dados .  
   https://www.w3schools.com/java/java_encapsulation.asp

6. **Programiz — Java Encapsulation** — Fundamento do encapsulamento, ocultação de dados e exemplos com validação .  
   https://www.programiz.com/java-programming/encapsulation