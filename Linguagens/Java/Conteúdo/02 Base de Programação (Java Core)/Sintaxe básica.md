
## Sintaxe básica do Java — e o que realmente importa nela

### 1. O programa mínimo

```java

public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Olá, Mundo!");
    }
}
```

Esse é o ponto de partida obrigatório. Vamos desmontá-lo peça por peça, porque cada palavra tem um motivo:

**`public class HelloWorld`** — Tudo em Java vive dentro de uma classe. `public` significa que esta classe é visível para qualquer outro código. `class` define o contêiner que agrupa dados (atributos) e comportamento (métodos).

**`public static void main(String[] args)`** — O método `main` é o ponto de entrada. A JVM (Java Virtual Machine) procura exatamente essa assinatura para iniciar a execução.

`public` porque a JVM precisa chamá-lo de fora. `static` porque a JVM o chama sem criar um objeto da classe primeiro. 
`void` porque ele não retorna nada ao sistema operacional. 

`String[] args` é o array que recebe os argumentos da linha de comando — crucial quando você roda aplicações em containers e precisa passar configurações na inicialização.

**`System.out.println(...)`** — `System` é uma classe da biblioteca padrão. `out` é um objeto que representa a saída padrão (o console). `println` é o método que imprime uma linha.

**Java 21 reduz a verbosidade.** As versões recentes introduziram _Unnamed Classes_ e _Instance Main Methods_ (JEP 445/463), que permitem escrever simplesmente:

```java

void main() {
    println("Olá, Mundo!");
}

```

Para código de aprendizado ou scripts rápidos, isso remove a cerimônia sem remover o poder. Para sistemas de produção, a estrutura tradicional com `public static void main` continua sendo o padrão.

---

### 2. Classes e objetos: a base da orientação a objetos

Java é **orientado a objetos**. Isso significa que você organiza o código em **classes** (moldes) e cria **objetos** (instâncias desses moldes).

Uma classe define:

- **Atributos** — os dados que um objeto carrega.
    
- **Métodos** — o comportamento que o objeto sabe executar.
    

```java

public class Carro {
    String marca;
    int ano;
    void exibirInfo() {
        System.out.println(marca + " - " + ano);
    }
}
```

Criar um objeto é usar `new`:

``` java

Carro meuCarro = new Carro();
meuCarro.marca = "Honda";
meuCarro.ano = 2024;
meuCarro.exibirInfo();  // Honda - 2024
```

---

### 3. Encapsulamento: controle de acesso, não só getters e setters

Encapsulamento é o princípio de esconder o estado interno de um objeto e expor apenas o que é necessário. O mecanismo são os **modificadores de acesso**:

|Modificador|Visível na classe|Visível no pacote|Visível em subclasses|Visível em qualquer lugar|
|---|---|---|---|---|
|`private`|Sim|Não|Não|Não|
|(padrão)|Sim|Sim|Não|Não|
|`protected`|Sim|Sim|Sim|Não|
|`public`|Sim|Sim|Sim|Sim|

O erro comum é achar que encapsulamento se resume a declarar atributos `private` e gerar getters e setters para todos eles. Isso é um antipadrão e expõe o interior do objeto igualmente, só que com mais código. Encapsulamento de verdade significa que seus objetos expõem **comportamentos significativos**, não seus campos internos.

Em vez de:

```java

carro.setLigado(true);
carro.setVelocidade(60);
```

Prefira:

```java

carro.ligar();
carro.acelerar(60);

```

Quem usa o objeto não precisa saber como ele funciona por dentro.


**Records: imutabilidade sem boilerplate**

Introduzidos no Java 14 e estáveis desde o Java 16, os **records** são classes cujo propósito é transportar dados imutáveis. Onde você escreveria dezenas de linhas com construtor, getters, `equals`, `hashCode` e `toString`, um record resolve em uma linha:

```java

public record Carro(String marca, int ano) {}
```

Uso:

```java

Carro c = new Carro("Honda", 2024);
System.out.println(c.marca());  // Honda
System.out.println(c);          // Carro[marca=Honda, ano=2024]
```

Records são implicitamente `final` (não podem ser herdados) e seus campos são `private final`. São ideais para DTOs, respostas de API, aglomerados de dados. Não substituem classes com comportamento mutável, mas eliminam uma classe inteira de código repetitivo.

---

### 4. Construtores: garantir que um objeto nasça válido

Um construtor é um método especial chamado automaticamente quando você usa `new`. Ele tem o mesmo nome da classe e não tem tipo de retorno.

```java

public class Carro {
    String marca;
    int ano;
    public Carro(String marca, int ano) {
        this.marca = marca;
        this.ano = ano;
    }
}
```

`this.marca` se refere ao atributo do objeto; `marca` sozinho é o parâmetro. O construtor existe para garantir que nenhum objeto seja criado em estado inválido. Se a existência de um `Carro` exige marca e ano, o construtor torna impossível criar um sem eles.

Em aplicações com frameworks como [[Spring Boot]], a injeção de dependência assume parte do trabalho de construir objetos, mas o princípio é o mesmo: o construtor deve garantir a integridade do objeto. Se um construtor permite que um objeto exista incompleto, ele falhou.

---

### 5. Arrays (vetores) em Java

Arrays armazenam múltiplos valores do mesmo tipo em uma única variável, em posições contíguas de memória. Têm tamanho fixo definido na criação:

```java

int[] numeros = {10, 20, 30, 40, 50};
System.out.println(numeros[0]);  // 10
System.out.println(numeros[2]);  // 30
System.out.println(numeros.length);  // 5
```

O índice começa em 0. Tentar acessar `numeros[5]` lança `ArrayIndexOutOfBoundsException`.

Arrays de tipos primitivos (`int`, `double`, `boolean`) armazenam os valores diretamente, com excelente localidade de cache — os elementos estão lado a lado na memória.

Para tamanhos dinâmicos, use **ArrayList**:

```java

import java.util.ArrayList;
import java.util.List;
List<String> nomes = new ArrayList<>();
nomes.add("Ana");
nomes.add("Carlos");
System.out.println(nomes.get(0));  // Ana
```

**Nota sobre terminologia:** em português, "vetor" pode designar tanto arrays quanto a classe `java.util.Vector`. `Vector` é uma classe legada, sincronizada (toda operação é thread-safe), e praticamente não se usa mais. Prefira `ArrayList` para uso geral, ou `Collections.synchronizedList` se realmente precisar de sincronização. O contexto do artigo original usa "vetor" para array — só não confunda os termos em código real.

---

### 6. Estruturas de repetição

**`for` clássico** — quando você sabe quantas iterações quer:

```java

for (int i = 0; i < 5; i++) {
    System.out.println(i);  // 0, 1, 2, 3, 4
}
```

**`for-each`** — para percorrer arrays e coleções sem índice:

```java

int[] numeros = {10, 20, 30};
for (int n : numeros) {
    System.out.println(n);
}
```

**`while`** — quando a condição de parada não depende de um contador:

```java

int i = 0;
while (i < 5) {
    System.out.println(i);
    i++;
}
```

**`do-while`** — garante pelo menos uma execução:

```java

int i = 0;
do {
    System.out.println(i);
    i++;
} while (i < 5);
```

---

### 7. Indo além da sintaxe básica

A sintaxe é o começo. O que separa código funcional de código profissional é o entendimento do que acontece por baixo:

- **Stack vs. Heap:** variáveis locais de tipos primitivos vivem na [[Stack]] e são liberadas automaticamente quando o método termina. Objetos (incluindo arrays) vivem no [[Heap]] e são gerenciados pelo [[Garbage Collector]]. Usar `Integer` (objeto wrapper) onde `int` (primitivo) basta gera operações desnecessárias de autoboxing/unboxing e pressiona o GC.
    
- **ArrayList e capacidade inicial:** [[ArrayList]] internamente usa um array que é redimensionado quando esgota. Esse redimensionamento envolve alocar um novo array maior e copiar todos os elementos o que é uma operação cara sob carga. Se você sabe quantos elementos aproximadamente terá, passe a capacidade inicial no construtor: `new ArrayList<>(1000)`.
    
- **Streams e código declarativo:** para manipular coleções, a API de [[Streams]] oferece uma alternativa mais legível e fácil de paralelizar que laços manuais:
    

```java

List<String> nomes = List.of("Ana", "Carlos", "Beatriz");
nomes.stream()
     .filter(n -> n.startsWith("A"))
     .map(String::toUpperCase)
     .forEach(System.out::println);  // ANA
```

- **Switch com pattern matching (Java 21):** o `switch` moderno vai muito além de inteiros e enums. Ele desestrutura objetos e tipos com elegância, eliminando cadeias de `if-else`:
    

```java

Object obj = "texto";
String resultado = switch (obj) {
    case Integer i -> "Número: " + i;
    case String s  -> "Texto de tamanho: " + s.length();
    default        -> "Tipo desconhecido";
};
```

Essas ferramentas não substituem a base — elas a pressupõem. Mas são elas que fazem a diferença entre código que apenas funciona e código que escala, é legível e usa a linguagem em sua capacidade plena.


---
---
---

## Palavras reservadas do Java: guia rápido

### 1. O que são

Palavras reservadas são termos que a linguagem reserva para si. Você não pode usá-las como nome de variável, método, classe ou qualquer outro identificador. Tentar fazer isso gera erro de compilação e o compilador simplesmente se recusa a aceitar.

São 51 palavras reservadas (48 em uso, 2 obsoletas, 1 sem função). A este grupo somam-se três literais que também são proibidos como identificadores: `true`, `false` e `null`.

Este guia as organiza por função, não por ordem alfabética. Assim você entende o propósito, não apenas decora a lista.

---

### 2. Tipos primitivos (8 palavras)

Java tem oito [[Tipos Primitivos]]. Eles armazenam valores diretamente na [[Stack]] — não são objetos, não vivem no [[Heap]].

|Palavra|Tipo|Tamanho|Faixa de valores|
|---|---|---|---|
|`byte`|Inteiro|8 bits|-128 a 127|
|`short`|Inteiro|16 bits|-32.768 a 32.767|
|`int`|Inteiro|32 bits|-2,14 bilhões a 2,14 bilhões|
|`long`|Inteiro|64 bits|±9,22 quintilhões|
|`float`|Ponto flutuante|32 bits|±3,4e-38 a ±3,4e+38|
|`double`|Ponto flutuante|64 bits|±1,7e-308 a ±1,7e+308|
|`char`|Caractere|16 bits (Unicode)|0 a 65.535|
|`boolean`|Booleano|(depende da JVM)|`true` ou `false`|

Regra prática: inteiros use `int` a menos que precise de alcance maior; decimais use `double` a menos que esteja em cenário com restrição de memória. `float` quase sempre exige o sufixo `f`: `float x = 3.14f;`.

---

### 3. Modificadores de acesso (3 palavras)

Controlam quem pode ver e usar [[Classes]], [[Atributos]], [[Métodos]] e [[Construtores]].

|Palavra|Visibilidade|
|---|---|
|`private`|Somente dentro da própria classe|
|_(padrão)_|Somente dentro do mesmo pacote|
|`protected`|Mesmo pacote + subclasses|
|`public`|Qualquer lugar|

O padrão (ausência de modificador) não tem palavra-chave — é a omissão. É chamado _package-private_.

---

### 4. Modificadores de não-acesso (6 palavras + 1 obsoleta)

Complementam os modificadores de acesso com restrições ou características adicionais.

| Palavra        | Atua sobre                  | Efeito                                                                                                                               |
| -------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `static`       | Atributos, métodos          | Pertencem à classe, não ao objeto. Podem ser acessados sem instanciar                                                                |
| `final`        | Classes, métodos, atributos | **Classe:** não pode ser herdada.<br>**Método:** não pode ser sobrescrito.<br>**Atributo:** não pode ser alterado após inicialização |
| `abstract`     | Classes, métodos            | **Classe:** não pode ser instanciada, serve como molde. <br>**Método:** não tem corpo, deve ser implementado pela subclasse          |
| `synchronized` | Métodos, blocos             | Apenas uma thread por vez pode executar o trecho marcado                                                                             |
| `volatile`     | Atributos                   | O valor é sempre lido da memória principal, não do cache local da thread                                                             |
| `transient`    | Atributos                   | O atributo é ignorado durante a serialização                                                                                         |
| `strictfp`     | Classes, métodos            | **Obsoleto** desde Java 17. Forçava precisão de ponto flutuante conforme IEEE 754                                                    |

---

### 5. Estrutura de classes e herança (7 palavras + 1 reservada)

|Palavra|Função|
|---|---|
|`class`|Define uma classe|
|`interface`|Define uma interface (até Java 7, só métodos abstratos; hoje permite métodos `default` e `static`)|
|`enum`|Define um tipo enumerado (conjunto fixo de constantes)|
|`extends`|Herda de uma classe (herança simples: uma classe só estende uma)|
|`implements`|Implementa uma ou mais interfaces|
|`super`|Referencia a classe pai — chama construtores ou métodos da superclasse|
|`this`|Referencia o próprio objeto — distingue atributos de parâmetros, chama construtores|
|`const`|**Reservada mas não usada.** Java nunca a implementou. Use `final`|

---

### 6. Estruturas de controle de fluxo (9 palavras + 1 inativa)

|Palavra|Função|
|---|---|
|`if` / `else`|Desvio condicional|
|`switch` / `case` / `default`|Seleção múltipla. Desde Java 14, `switch` é também uma expressão que retorna valor. Desde Java 21, suporta pattern matching|
|`for`|Laço com inicialização, condição e incremento|
|`while`|Laço enquanto condição for verdadeira|
|`do`|Executa o bloco uma vez antes de verificar a condição com `while`|
|`break`|Sai do laço ou switch imediatamente|
|`continue`|Pula para a próxima iteração do laço|
|`goto`|**Reservada mas sem função.** Java não tem `goto`. O salto incondicional existe nos bytecodes, não no código-fonte|

---

### 7. Tratamento de exceções (5 palavras)

|Palavra|Função|
|---|---|
|`try`|Delimita o bloco de código que pode lançar exceção|
|`catch`|Captura e trata um tipo específico de exceção|
|`finally`|Bloco executado sempre, havendo exceção ou não — ideal para liberar recursos|
|`throw`|Lança uma exceção explicitamente|
|`throws`|Declara que um método pode lançar uma exceção — transfere a responsabilidade de tratamento para quem chama|

---

### 8. Criação e organização de código (5 palavras + módulos)

|Palavra|Função|
|---|---|
|`new`|Instancia um objeto (aloca no Heap e chama o construtor)|
|`package`|Declara o pacote ao qual o arquivo pertence|
|`import`|Importa classes, interfaces ou membros estáticos de outro pacote|
|`return`|Encerra o método e, opcionalmente, devolve um valor|
|`void`|Indica que o método não retorna valor|
|`var`|**Desde Java 10.** Inferência de tipo local — o compilador deduz o tipo pela inicialização. Só para variáveis locais: `var lista = new ArrayList<String>();`|
|`module` / `requires` / `exports`|**Desde Java 9 (Project Jigsaw).** Definem módulos — agrupamento de pacotes com controle explícito do que se expõe e do que se consome|

---

### 9. Outras palavras (3)

|Palavra|Função|
|---|---|
|`instanceof`|Verifica se um objeto é instância de uma classe ou implementa uma interface. Desde Java 16, pode fazer pattern matching: `if (obj instanceof String s)` já declara e atribui a variável|
|`native`|Declara que o método é implementado em código nativo (C/C++) via JNI|
|`assert`|Habilita verificações de diagnóstico durante o desenvolvimento. Desabilitadas em produção por padrão|

---

### 10. Os três que não são palavras-chave, mas são proibidos

|Literal|Significado|
|---|---|
|`true`|Valor booleano verdadeiro|
|`false`|Valor booleano falso|
|`null`|Referência nula — não aponta para objeto nenhum|

Não são palavras reservadas tecnicamente, mas você não pode usá-los como identificadores. O compilador trata como erro do mesmo jeito.

---

### 11. As que deixaram de importar (ou nunca importaram)

- `const` — reservada, nunca implementada. Substituída por `final`.
    
- `goto` — reservada, sem função. Existe por compatibilidade com a tradição do C/C++.
    
- `strictfp` — obsoleta desde Java 17. Todo cálculo de ponto flutuante agora segue o padrão IEEE 754 por padrão.
    

De 51, apenas 48 têm uso real hoje.

---

### 12. O que levar deste guia

As palavras reservadas são o vocabulário mínimo da linguagem. Saber agrupá-las por função transforma uma lista decorada em ferramenta mental: você reconhece o que está vendo em qualquer código Java e entende por que aquela palavra está ali. Tipos definem o que se armazena. Modificadores definem quem acessa. Controle de fluxo define quando executa. Exceções definem como falha. O resto é sintaxe a favor da semântica.










---
[Sintaxe](https://www.w3schools.com/java/java_syntax.asp)