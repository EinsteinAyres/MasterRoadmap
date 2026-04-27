
## Métodos em Java: o comportamento que dá vida aos objetos

### 1. O que é um método

Um método é um bloco de código nomeado que executa uma tarefa específica. Ele pode receber dados de entrada (parâmetros), processá-los e devolver um resultado (valor de retorno). Métodos são a unidade fundamental de comportamento em Java — tudo o que um programa faz, ele faz através de métodos.

Em Java, não existem funções soltas como em C, Python ou JavaScript. Todo método pertence a uma classe ou interface. Mesmo os métodos `static` — que não precisam de instância — estão vinculados a uma classe. É por isso que o termo "função", embora usado informalmente, não é tecnicamente preciso no ecossistema Java: o termo correto é **método**.

---

### 2. A anatomia de um método

Todo método é composto por seis elementos. A estrutura canônica é:

```
[modificadores] [tipo de retorno] [nome] ([parâmetros]) [throws exceções] {
    // corpo
}
```

**Exemplo completo:**

```java
/**
 * Calcula a média aritmética de dois valores.
 *
 * @param a primeiro valor
 * @param b segundo valor
 * @return a média de a e b
 * @throws IllegalArgumentException se algum valor for negativo
 */
public static double media(double a, double b) throws IllegalArgumentException {
    if (a < 0 || b < 0) {
        throw new IllegalArgumentException("Valores não podem ser negativos");
    }
    return (a + b) / 2;
}
```

Dissecando componente por componente:

| Componente | Exemplo | Significado | Obrigatório? |
|------------|---------|-------------|--------------|
| Modificador de acesso | `public` | Quem pode chamar o método | Não (default package-private) |
| Outros modificadores | `static` | Pertence à classe, não à instância | Não |
| Tipo de retorno | `double` | O tipo do valor devolvido | Sim (use `void` se não retorna nada) |
| Nome | `media` | Identificador do método | Sim |
| Parâmetros | `(double a, double b)` | Dados de entrada: tipo e nome | Não (pode ser vazio) |
| Exceções | `throws IllegalArgumentException` | Exceções verificadas que pode lançar | Não |
| Corpo | `{ ... }` | A lógica executada | Sim (exceto em métodos `abstract`) |

---

### 3. Modificadores de acesso

Controlam a visibilidade do método — de onde ele pode ser chamado:

| Modificador | Mesma classe | Mesmo pacote | Subclasse | Qualquer lugar |
|-------------|--------------|--------------|-----------|----------------|
| `private` | Sim | Não | Não | Não |
| *(padrão / package-private)* | Sim | Sim | Não | Não |
| `protected` | Sim | Sim | Sim | Não |
| `public` | Sim | Sim | Sim | Sim |

**A regra de ouro:** comece com o modificador mais restritivo possível. Se um método só é usado dentro da própria classe, declare-o `private`. Só amplie o acesso quando realmente necessário. Isso reduz o acoplamento e torna o código mais fácil de modificar.

---

### 4. Outros modificadores importantes

| Modificador | Efeito |
|-------------|--------|
| `static` | Pertence à classe, não ao objeto. Pode ser chamado sem instanciar |
| `final` | Não pode ser sobrescrito por subclasses |
| `abstract` | Não tem corpo; a subclasse é obrigada a implementar |
| `synchronized` | Apenas uma thread executa o método por vez no mesmo objeto |
| `native` | Implementado em código nativo (C/C++) via JNI |

Exemplos:

```java
public static int somar(int a, int b) { ... }     // Método estático
public final String getNome() { ... }              // Não pode ser sobrescrito
protected abstract double area();                  // Deve ser implementado pela subclasse
public synchronized void incrementar() { ... }     // Thread-safe
```

---

### 5. Métodos de instância vs. métodos estáticos

**Métodos de instância** operam sobre um objeto. Têm acesso ao `this` e aos atributos da instância:

```java
public class Conta {
    private double saldo;

    public void depositar(double valor) {  // Método de instância
        if (valor <= 0) {
            throw new IllegalArgumentException("Valor deve ser positivo");
        }
        this.saldo += valor;
    }

    public double getSaldo() {             // Método de instância
        return this.saldo;
    }
}

// Uso: precisa de uma instância
Conta c = new Conta();
c.depositar(100);
```

**Métodos estáticos** pertencem à classe. Não têm acesso ao `this` e não podem acessar atributos de instância diretamente:

```java
public class Calculadora {
    // Método estático — não precisa de instância
    public static int maximo(int a, int b) {
        return a > b ? a : b;
    }
}

// Uso: chamado diretamente na classe
int maior = Calculadora.maximo(10, 20);
```

**A decisão:**

- O método usa atributos do objeto? → **Instância.**
- O método é uma utilidade pura (só depende dos parâmetros)? → **Estático.**
- O método precisa ser acessado sem criar objetos? → **Estático.**

---

### 6. Passagem de parâmetros: Java é sempre pass-by-value

Esta é uma das questões mais mal compreendidas da linguagem. Java **sempre** passa argumentos por valor — cópia. O que muda é o que está sendo copiado.

**Para primitivos:** o valor é copiado. Alterações no parâmetro não afetam a variável original:

```java
public static void dobrar(int x) {
    x = x * 2;  // Altera a cópia local
}

int numero = 5;
dobrar(numero);
System.out.println(numero);  // 5 — inalterado
```

**Para objetos:** a **referência** é copiada. O método recebe uma cópia do ponteiro, mas ambos apontam para o mesmo objeto:

```java
public static void adicionarNome(List<String> lista) {
    lista.add("Maria");       // Modifica o objeto apontado
    lista = new ArrayList<>(); // Reatribui a cópia local — original inalterado
}

List<String> nomes = new ArrayList<>();
adicionarNome(nomes);
System.out.println(nomes);  // [Maria] — o objeto foi modificado
                            // A reatribuição dentro do método não surtiu efeito
```

Se Java fosse pass-by-reference, a reatribuição `lista = new ArrayList<>()` dentro do método alteraria a variável externa. Ela não altera — porque a referência externa é uma variável diferente da interna.

---

### 7. Retorno: `return` e `void`

**Métodos com retorno** usam `return` seguido de um valor compatível com o tipo declarado:

```java
public int somar(int a, int b) {
    return a + b;
}

public String saudacao(String nome) {
    if (nome == null) {
        return "Olá, anônimo";
    }
    return "Olá, " + nome;
}
```

O `return` encerra o método imediatamente. Qualquer código depois dele é inacessível.

**Métodos `void`** não devolvem valor. Podem usar `return;` (sem valor) para encerrar precocemente:

```java
public void imprimirSePositivo(int valor) {
    if (valor <= 0) {
        return;  // Encerra aqui
    }
    System.out.println(valor);
}
```

---

### 8. Sobrecarga: mesmo nome, parâmetros diferentes

Sobrecarga (overloading) permite múltiplos métodos com o mesmo nome na mesma classe, desde que tenham **assinaturas diferentes** — número, tipo ou ordem dos parâmetros:

```java
public class Formatador {
    public String formatar(int valor) {
        return String.format("R$ %d,00", valor);
    }

    public String formatar(double valor) {
        return String.format("R$ %.2f", valor);
    }

    public String formatar(double valor, String simbolo) {
        return String.format("%s %.2f", simbolo, valor);
    }
}
```

O compilador decide qual versão chamar com base nos argumentos em tempo de compilação. **O tipo de retorno não diferencia sobrecarga** — dois métodos com mesmo nome e mesmos parâmetros mas tipos de retorno diferentes geram erro de compilação.

---

### 9. Sobrescrita: substituindo a implementação herdada

Sobrescrita (overriding) ocorre quando uma subclasse fornece sua própria implementação de um método definido na superclasse:

```java
class Animal {
    public String som() {
        return "algum som";
    }
}

class Cachorro extends Animal {
    @Override
    public String som() {          // Sobrescreve o método da superclasse
        return "latido";
    }
}

class Gato extends Animal {
    @Override
    public String som() {
        return "miado";
    }
}
```

**A anotação `@Override`** instrui o compilador a verificar se você realmente está sobrescrevendo algo. Se o método da superclasse mudar ou você errar o nome, o compilador avisa. Use sempre.

**Regras da sobrescrita:**

- Mesmo nome, mesmos parâmetros, tipo de retorno compatível (igual ou subtipo — retorno covariante).
- O modificador de acesso não pode ser **mais restritivo** que o original. Se o original é `protected`, a sobrescrita pode ser `protected` ou `public`, nunca `private`.
- O método original não pode ser `final`.
- O método original não pode ser `static` (métodos estáticos não são sobrescritos — são ocultados, um conceito diferente).

---

### 10. Métodos abstratos

Um método `abstract` declara uma assinatura sem fornecer corpo. A classe que o contém também deve ser `abstract`:

```java
abstract class Forma {
    abstract double area();  // Sem implementação — as subclasses devem fornecer

    public void exibir() {   // Método concreto em classe abstrata — OK
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

**Em interfaces** (pré-Java 8), todos os métodos são implicitamente `public abstract`. Desde o Java 8, interfaces também podem ter métodos `default` e `static` com implementação.

---

### 11. Varargs: número variável de argumentos

O sintaxe `...` permite passar zero ou mais argumentos do mesmo tipo:

```java
public static String concatenar(String... palavras) {
    return String.join(" ", palavras);
}

concatenar("Java", "21");          // "Java 21"
concatenar("um", "dois", "três");  // "um dois três"
concatenar();                      // "" (array vazio)
```

**Regras:**
- Apenas um parâmetro varargs por método.
- Deve ser o último parâmetro: `void metodo(int x, String... valores)` é válido; `void metodo(String... valores, int x)` não é.
- Internamente, é tratado como array: `palavras` é um `String[]`.

---

### 12. Retornando múltiplos valores

Java não suporta retorno de múltiplos valores diretamente. As alternativas:

**Criar uma classe ou record:**

```java
public record ResultadoDivisao(int quociente, int resto) {}

public ResultadoDivisao dividir(int a, int b) {
    return new ResultadoDivisao(a / b, a % b);
}
```

**Modificar um objeto passado como parâmetro** (não recomendado para retorno):

```java
public void dividir(int a, int b, int[] resultado) {
    resultado[0] = a / b;
    resultado[1] = a % b;
}
```

**Usar `Map` ou `Pair`** (genérico demais, perde semântica):

```java
public Map<String, Integer> dividir(int a, int b) {
    return Map.of("quociente", a / b, "resto", a % b);
}
```

Records são a melhor opção para a maioria dos casos — tipados, imutáveis, semânticos.

---

### 13. Boas práticas

**Nomes expressivos:** métodos fazem algo — use verbos. `calcularTotal()`, `enviarEmail()`, `validarEntrada()`. Para métodos que retornam boolean, prefixos `is`, `has`, `can`: `isAtivo()`, `hasPermissao()`, `canExecute()`.

**Um método, uma responsabilidade:** se um método faz duas coisas, divida em dois. Se ele passa de 20-30 linhas, provavelmente está fazendo demais.

**Poucos parâmetros:** zero parâmetros é ideal. Um é bom. Dois é aceitável. Três é suspeito. Quatro ou mais — agrupe em um record ou classe.

```java
// Ruim — muitos parâmetros, ordem fácil de errar
public void cadastrar(String nome, String email, String telefone,
                       String endereco, String cidade, String cep) { }

// Melhor — parâmetros agrupados semanticamente
public record DadosPessoais(String nome, String email, String telefone) {}
public record Endereco(String logradouro, String cidade, String cep) {}
public void cadastrar(DadosPessoais dados, Endereco endereco) { }
```

**Documente métodos públicos com JavaDoc:**

```java
/**
 * Calcula o imposto sobre o valor fornecido.
 *
 * @param valor base de cálculo, deve ser positivo
 * @return valor do imposto calculado (alíquota de 15%)
 * @throws IllegalArgumentException se {@code valor} for negativo
 */
public double calcularImposto(double valor) {
    if (valor < 0) {
        throw new IllegalArgumentException("Valor não pode ser negativo");
    }
    return valor * 0.15;
}
```

**Evite efeitos colaterais inesperados:** um método chamado `getSaldo()` não deve modificar nada. Um método chamado `calcular()` não deve enviar e-mails. O nome deve refletir tudo que o método faz.

**Prefira imutabilidade:** métodos que devolvem novos objetos em vez de modificar o existente são mais seguros em ambientes concorrentes e mais fáceis de raciocinar.

---

### 14. Métodos como cidadãos de segunda classe

Diferentemente de linguagens funcionais como JavaScript ou Kotlin, em Java métodos não são objetos de primeira classe — você não pode passar um método diretamente como argumento. As soluções são:

**Interfaces funcionais e lambdas (Java 8+):**

```java
List<String> nomes = List.of("Ana", "Carlos");
nomes.forEach(nome -> System.out.println(nome));       // Lambda
nomes.forEach(System.out::println);                    // Method reference
```

**Method references** (`Classe::metodo`) são a ponte entre métodos e programação funcional:

```java
// Method reference para método estático
Function<String, Integer> parser = Integer::parseInt;

// Method reference para método de instância
Function<String, Integer> medidor = String::length;

// Method reference para método de instância de um objeto específico
var lista = new ArrayList<String>();
Consumer<String> adicionador = lista::add;
```

---

### 15. Resumo em uma frase

Métodos são o verbo na gramática do Java — uma classe descreve o que algo é, e seus métodos descrevem o que ela faz. Um método bem escrito tem nome expressivo, faz uma coisa só, recebe poucos parâmetros, protege suas invariantes e ou devolve um resultado previsível ou deixa claro que modificou o estado.

---

### Fontes

1. Oracle, "Java Language Specification — Chapter 8: Classes" — especificação oficial de declaração de métodos, assinaturas e modificadores 
2. Oracle, "Java Tutorials – Defining Methods" — documentação oficial sobre definição, parâmetros e retorno 
3. Dev.java, "Methods in Java" — guia sobre declaração, passagem de argumentos e sobrecarga 
4. GeeksforGeeks, "Java Methods" — estrutura, tipos, sobrecarga e melhores práticas 
5. Baeldung, "Java Methods" — guia abrangente com assinaturas, pass-by-value e varargs 
6. 廖雪峰的官方网站, "Java方法" — métodos de instância vs. estáticos, sobrescrita e polimorfismo