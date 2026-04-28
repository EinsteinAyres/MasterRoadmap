## Métodos: o comportamento que dá vida aos objetos

### 1. O que são métodos

Métodos são blocos de código que definem o **comportamento** de uma classe. Eles determinam o que os objetos dessa classe sabem fazer — calcular algo, modificar seu estado, comunicar-se com outros objetos, devolver informações.

Se os atributos respondem "o que este objeto armazena?", os métodos respondem "o que este objeto faz?".

```java
public class Calculadora {
    // Método que recebe dois valores e devolve um resultado
    public int somar(int a, int b) {
        return a + b;
    }
}
```

---

### 2. A anatomia de um método

Todo método tem seis componentes. Alguns são opcionais, mas a estrutura é fixa:

```
[modificadores] [tipo de retorno] [nome]([parâmetros]) [throws exceções] {
    // corpo
}
```

**Exemplo completo:**

```java
public static final double somar(double a, double b) throws IllegalArgumentException {
    if (Double.isNaN(a) || Double.isNaN(b)) {
        throw new IllegalArgumentException("Argumentos não podem ser NaN");
    }
    return a + b;
}
```

Dissecando:

| Componente | Exemplo | Significado |
|------------|---------|-------------|
| Modificadores | `public static final` | Visibilidade, pertencimento, extensibilidade |
| Tipo de retorno | `double` | O que o método devolve; `void` se não devolve nada |
| Nome | `somar` | Identificador do método (verbo ou frase verbal por convenção) |
| Parâmetros | `(double a, double b)` | Dados de entrada — nome e tipo de cada um |
| Exceções | `throws IllegalArgumentException` | Exceções verificadas que o método pode lançar |
| Corpo | `{ ... return a + b; }` | A lógica executada quando o método é chamado |

---

### 3. Modificadores de métodos

Os modificadores determinam como o método pode ser acessado e usado .

**Modificadores de acesso** (os mesmos dos atributos):

| Modificador | Mesma classe | Mesmo pacote | Subclasse | Qualquer lugar |
|-------------|--------------|--------------|-----------|----------------|
| `private` | Sim | Não | Não | Não |
| *(padrão)* | Sim | Sim | Não | Não |
| `protected` | Sim | Sim | Sim | Não |
| `public` | Sim | Sim | Sim | Sim |

**Outros modificadores importantes:**

| Modificador | Efeito |
|-------------|--------|
| `static` | Pertence à classe, não ao objeto. Pode ser chamado sem instanciar |
| `final` | Não pode ser sobrescrito por subclasses |
| `abstract` | Não tem corpo; a subclasse é obrigada a implementar |
| `synchronized` | Apenas uma thread executa o método por vez no mesmo objeto |
| `native` | Implementado em código nativo (C/C++) via JNI |

---

### 4. Métodos de instância vs. métodos estáticos

**Métodos de instância** precisam de um objeto para serem chamados. Têm acesso ao `this` — o objeto sobre o qual operam:

```java
public class Conta {
    private double saldo;

    public void depositar(double valor) {  // método de instância
        this.saldo += valor;               // 'this' refere-se à instância
    }
}
```

```java
Conta c = new Conta();
c.depositar(100);  // chamado sobre o objeto 'c'
```

**Métodos estáticos** pertencem à classe. Não têm acesso ao `this` porque não operam sobre uma instância específica :

```java
public class Matematica {
    public static int maximo(int a, int b) {  // método estático
        return a > b ? a : b;
    }
}
```

```java
int maior = Matematica.maximo(10, 20);  // chamado diretamente na classe
```

**A regra:** se o método usa atributos da instância, ele deve ser de instância. Se a lógica é independente de estado (cálculo puro, utilidade), `static` é apropriado.

---

### 5. Parâmetros e passagem de argumentos

Java é **pass-by-value** — sempre. O que muda é o que o valor representa :

**Para primitivos:** o valor é copiado. Alterações no parâmetro não afetam a variável original:

```java
public void dobrar(int x) {
    x = x * 2;  // altera a cópia local, não o original
}

int numero = 5;
dobrar(numero);
System.out.println(numero);  // 5 — inalterado
```

**Para objetos:** o valor da **referência** é copiado. O método recebe uma cópia do ponteiro, mas ambos apontam para o mesmo objeto:

```java
public void adicionarNome(List<String> lista) {
    lista.add("Maria");  // modifica o objeto apontado
}

List<String> nomes = new ArrayList<>();
adicionarNome(nomes);
System.out.println(nomes);  // [Maria] — o objeto foi modificado
```

Mas reatribuir o parâmetro não afeta a referência original:

```java
public void trocarLista(List<String> lista) {
    lista = new ArrayList<>();  // reatribui a cópia local; original inalterado
}
```

---

### 6. Retorno: `return` e `void`

**Métodos com retorno** usam `return` para devolver um valor. O tipo declarado deve ser compatível:

```java
public int somar(int a, int b) {
    return a + b;            // int compatível com int
}

public String saudacao(String nome) {
    return "Olá, " + nome;  // String compatível com String
}
```

**Métodos `void`** não devolvem nada. Podem usar `return;` (sem valor) para encerrar a execução precocemente:

```java
public void imprimirSePositivo(int valor) {
    if (valor <= 0) {
        return;  // encerra aqui
    }
    System.out.println(valor);
}
```

**Múltiplos retornos** são permitidos, mas use com moderação — métodos com muitos pontos de saída são mais difíceis de entender e testar.

---

### 7. Sobrecarga: mesmo nome, assinaturas diferentes

Sobrecarga permite múltiplos métodos com o mesmo nome, desde que tenham **parâmetros diferentes** (número, tipo ou ordem) :

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

O compilador decide qual versão chamar com base nos argumentos. Isso é resolvido em tempo de compilação — não é polimorfismo dinâmico.

**Não confunda com sobrescrita.** Sobrecarga é mesmo nome, parâmetros diferentes, mesma classe ou hierarquia. Sobrescrita é mesmo nome, mesmos parâmetros, subclasse substituindo implementação da superclasse.

---

### 8. Sobrescrita e `@Override`

Sobrescrita ocorre quando uma subclasse fornece sua própria implementação de um método herdado :

```java
class Animal {
    public String som() {
        return "algum som";
    }
}

class Cachorro extends Animal {
    @Override
    public String som() {
        return "latido";
    }
}
```

A anotação `@Override` não é obrigatória, mas é **altamente recomendada** — ela instrui o compilador a verificar se você realmente está sobrescrevendo algo. Se o método na superclasse mudar ou você errar o nome, o compilador avisa.

**Regras da sobrescrita:**

- Mesmo nome, mesmos parâmetros, mesmo tipo de retorno (ou subtipo — retorno covariante desde Java 5).
- O modificador de acesso não pode ser **mais restritivo** que o original. Se o método original é `protected`, a sobrescrita pode ser `protected` ou `public`, nunca `private`.
- O método original não pode ser `final` ou `static`.

---

### 9. Métodos abstratos

Um método abstrato declara uma assinatura sem fornecer implementação. A classe que o contém também deve ser `abstract` :

```java
abstract class Forma {
    abstract double area();   // sem corpo; subclasses devem implementar

    public void exibir() {    // método concreto — pode estar em classe abstrata
        System.out.println("Área: " + area());
    }
}
```

**Obrigação:** toda subclasse concreta é forçada a implementar os métodos abstratos. Isso garante que todas as variantes tenham o comportamento esperado.

Em interfaces, todos os métodos são implicitamente `public abstract`, exceto `default` e `static`:

```java
interface Calculavel {
    double calcular();  // implicitamente public abstract
}
```

---

### 10. Métodos `default` e `static` em interfaces

Desde o Java 8, interfaces podem ter métodos com implementação :

**`default`:** fornece uma implementação padrão que a classe implementadora pode usar ou sobrescrever:

```java
interface Notificador {
    void enviar(String mensagem);

    default void enviarComLog(String mensagem) {
        System.out.println("LOG: " + mensagem);
        enviar(mensagem);
    }
}
```

**`static` em interface:** método utilitário que pertence à interface, não às instâncias:

```java
interface Conversor {
    static double celsiusParaFahrenheit(double c) {
        return c * 9.0 / 5.0 + 32.0;
    }
}

double f = Conversor.celsiusParaFahrenheit(25);
```

---

### 11. Varargs: número variável de argumentos

O sintaxe `...` permite passar zero ou mais argumentos do mesmo tipo. Internamente, são tratados como array :

```java
public class Formatador {
    public static String concatenar(String... palavras) {
        return String.join(" ", palavras);
    }
}

Formatador.concatenar("Java", "21");               // "Java 21"
Formatador.concatenar("um", "dois", "três");       // "um dois três"
Formatador.concatenar();                           // "" (array vazio)
```

**Regras:**
- Apenas um parâmetro varargs por método.
- Deve ser o último parâmetro: `void metodo(int x, String... valores)` é válido; `void metodo(String... valores, int x)` não é.

---

### 12. Assinatura do método

A **assinatura** de um método é composta por seu nome e a lista de tipos dos parâmetros. O tipo de retorno, os nomes dos parâmetros e as exceções **não fazem parte** da assinatura para fins de sobrecarga.

```java
// Mesma assinatura — conflito:
public int calcular(int x) { ... }
public double calcular(int x) { ... }  // ERRO — tipo de retorno diferente não basta

// Assinaturas diferentes — sobrecarga válida:
public int calcular(int x) { ... }
public int calcular(double x) { ... }
```

---

### 13. Boas práticas

**Nomes expressivos:** métodos fazem algo — use verbos. `calcularTotal()`, `enviarEmail()`, `validarEntrada()`. Para métodos que retornam boolean, prefixos como `is`, `has`, `can`: `isAtivo()`, `hasPermissao()`, `canExecute()`.

**Métodos curtos:** um método deve fazer uma coisa e fazê-la bem. Se passa de 20-30 linhas, provavelmente está fazendo demais. Extraia métodos auxiliares privados.

**Menos parâmetros:** zero parâmetros é ideal. Um é bom. Dois é aceitável. Três é suspeito. Quatro ou mais — considere agrupar em um objeto (record ou classe).

```java
// Ruim — muitos parâmetros, ordem fácil de errar
public void cadastrar(String nome, String email, String telefone, 
                       String endereco, String cidade, String cep) { }

// Melhor — parâmetros agrupados semanticamente
public record DadosPessoais(String nome, String email, String telefone) {}
public record Endereco(String logradouro, String cidade, String cep) {}

public void cadastrar(DadosPessoais dados, Endereco endereco) { }
```

**Evite efeitos colaterais inesperados:** um método chamado `getSaldo()` não deve modificar nada. Um método chamado `calcular()` não deve enviar e-mails. O nome deve refletir tudo que o método faz.

**Documente com JavaDoc:** para métodos públicos, explique o que o método faz, o que cada parâmetro significa, o que retorna e quais exceções pode lançar :

```java
/**
 * Calcula o imposto sobre o valor fornecido.
 *
 * @param valor base de cálculo, deve ser positivo
 * @return valor do imposto (alíquota de 15%)
 * @throws IllegalArgumentException se o valor for negativo
 */
public double calcularImposto(double valor) {
    if (valor < 0) {
        throw new IllegalArgumentException("Valor não pode ser negativo: " + valor);
    }
    return valor * 0.15;
}
```

---

### 14. O que levar deste guia

Métodos são o verbo da linguagem. Uma classe bem projetada é aquela cujos métodos expressam claramente as ações do domínio: `conta.depositar()`, `pedido.calcularTotal()`, `usuario.temPermissao()`.

**Privado por padrão.** Só exponha como `public` o que realmente faz parte da interface da classe. Métodos auxiliares, detalhes de implementação — `private`.

**Imutabilidade quando possível.** Métodos que devolvem novos objetos em vez de modificar o existente (`return new Conta(...)`) são mais seguros em ambientes concorrentes e mais fáceis de raciocinar.

**Sobrescrita com disciplina.** Use `@Override` sempre. Respeite o contrato da superclasse — o Princípio da Substituição de Liskov: uma subclasse deve poder substituir a superclasse sem quebrar o programa.

**Testabilidade.** Métodos puros (sem efeitos colaterais, mesmo resultado para os mesmos argumentos) são trivialmente testáveis. Métodos que dependem de estado global, data/hora atual ou I/O exigem técnicas como injeção de dependência para serem testados adequadamente.

---

### Fontes

1. Cleverence, "Java Classes and Objects: Complete Tutorial with Examples and Best Practices" (2026) — anatomia de métodos, modificadores, sobrecarga, sobrescrita, varargs e assinatura de métodos 
2. Dev.java, "Methods in Java" — guia oficial da Oracle sobre declaração, parâmetros, passagem de argumentos, sobrecarga e varargs 
3. GeeksforGeeks, "Java Methods" — estrutura completa de métodos, tipos, sobrecarga, boas práticas de nomenclatura e documentação 
4. 廖雪峰的官方网站, "Java方法" — métodos de instância vs. estáticos, sobrescrita, `@Override`, polimorfismo e métodos `final` 
5. JavaRush, "Методы в Java" — explicação detalhada de parâmetros, retorno, pass-by-value e sobrecarga 
6. Oracle, "Java Language Specification — Chapter 8: Classes, Section 8.4: Method Declarations" — especificação oficial da linguagem sobre declaração de métodos, assinaturas, modificadores e regras de sobrescrita