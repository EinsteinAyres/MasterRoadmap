
## Classes e Objetos: o molde e a instância

### 1. A metáfora que explica tudo

Uma **classe** é uma planta baixa. Um **objeto** é a casa construída a partir dela.

A planta descreve como a casa deve ser — quantos quartos, onde ficam as janelas, qual a altura do teto. Mas a planta em si não é uma casa. Você não pode morar nela. Você precisa construí-la — e pode construir quantas casas quiser a partir da mesma planta, cada uma com móveis diferentes, cores diferentes, ocupantes diferentes.

Em Java, a classe é a planta. O objeto é a casa. A classe define a estrutura e o comportamento; o objeto é a manifestação concreta dessa definição na memória, com valores específicos para cada atributo .

---

### 2. O que é uma classe

Uma classe é um **tipo definido pelo programador** que agrupa **estado** (campos) e **comportamento** (métodos) em uma única unidade. É o mecanismo fundamental da orientação a objetos em Java — tudo em Java vive dentro de uma classe .

**Anatomia de uma classe:**

```java
public class Carro {
    // Estado (campos / atributos)
    private String marca;
    private String modelo;
    private int ano;
    private int velocidade;

    // Construtor — inicializa o estado
    public Carro(String marca, String modelo, int ano) {
        this.marca = marca;
        this.modelo = modelo;
        this.ano = ano;
        this.velocidade = 0;
    }

    // Comportamento (métodos)
    public void acelerar(int incremento) {
        this.velocidade += incremento;
    }

    public int getVelocidade() {
        return velocidade;
    }
}
```

**Componentes de uma classe** (em ordem) :

1. **Modificadores** — `public`, `private`, `protected` (opcionais, mas essenciais para encapsulamento).
2. **Palavra-chave `class`** — obrigatória.
3. **Nome da classe** — por convenção, começa com maiúscula (`Carro`, `ContaBancaria`).
4. **Superclasse** (se houver) — precedida por `extends`.
5. **Interfaces implementadas** (se houver) — precedidas por `implements`.
6. **Corpo** — delimitado por `{}`, contendo campos, métodos, construtores e blocos de inicialização.

**Características importantes:**

- Uma classe **não ocupa memória** em tempo de execução. Ela é um molde, não um objeto .
- O nome do arquivo `.java` deve coincidir com o nome da classe pública que ele contém .
- Classes podem ter campos de tipos primitivos, referências para outros objetos, métodos e construtores .

---

### 3. O que é um objeto

Um **objeto** é uma instância concreta de uma classe. Enquanto a classe é a ideia abstrata de "um carro", o objeto é "aquele Honda Civic azul, ano 2024, estacionado na vaga 7" .

Objetos têm três propriedades fundamentais:

- **Estado:** os valores armazenados nos campos naquele momento. Um `Carro` tem `marca = "Honda"`, `velocidade = 60`.
- **Comportamento:** o que o objeto sabe fazer — métodos como `acelerar()`, `frear()`.
- **Identidade:** cada objeto é único na memória, mesmo que tenha os mesmos valores que outro. `carro1 == carro2` só é verdadeiro se ambos apontarem para o mesmo objeto .

**Criando objetos — o operador `new`:**

```java
Carro meuCarro = new Carro("Honda", "Civic", 2024);
Carro seuCarro = new Carro("Toyota", "Corolla", 2023);
Carro outroCarro = new Carro("Honda", "Civic", 2024);
```

O operador `new` faz três coisas:
1. **Aloca** memória na Heap para o novo objeto.
2. **Inicializa** os campos com valores padrão (0, `false`, `null`) ou com os valores passados ao construtor.
3. **Retorna** uma referência para o objeto recém-criado .

`meuCarro` e `outroCarro` são objetos **diferentes** — ambos são Honda Civic 2024, mas ocupam espaços distintos na memória. Mudar a velocidade de um não afeta o outro .

**Usando objetos:**

```java
// Acessar campos (não recomendado se private — use getters)
System.out.println(meuCarro.marca);  // Só funciona se 'marca' for public

// Chamar métodos
meuCarro.acelerar(50);
System.out.println(meuCarro.getVelocidade());  // 50
```

---

### 4. Classes vs. Objetos: a tabela

| Característica | Classe | Objeto |
|---------------|-------|--------|
| **O que é** | Molde, blueprint, definição de tipo  | Instância concreta da classe |
| **Existe em** | Tempo de compilação (código-fonte) | Tempo de execução (Heap) |
| **Ocupa memória** | Não (a classe em si não consome Heap)  | Sim — cada objeto ocupa espaço na Heap |
| **Quantas vezes** | Definida uma vez | Pode ser instanciada inúmeras vezes |
| **Estado** | Define quais campos podem existir | Contém valores específicos para esses campos |
| **Exemplo** | `class Carro { ... }` | `new Carro("Honda", "Civic", 2024)` |
| **Palavra-chave** | `class` | `new` |

---

### 5. O construtor padrão

Se você não escrever **nenhum** construtor, o compilador gera um automaticamente: o **construtor padrão** (default constructor). Ele não tem parâmetros e inicializa todos os campos com valores padrão (0 para números, `false` para boolean, `null` para referências) :

```java
public class Exemplo {
    private int valor;    // Será 0
    private String texto; // Será null
    // Construtor padrão gerado:
    // public Exemplo() { }
}
```

No momento em que você define **qualquer** construtor, o padrão desaparece. Se ainda precisar dele, declare-o explicitamente.

---

### 6. Múltiplas classes e organização

Um programa Java típico usa múltiplas classes. É comum separar a classe que contém o `main()` das classes de domínio :

**Carro.java** — define o molde:

```java
public class Carro {
    private String marca;
    private int ano;

    public Carro(String marca, int ano) {
        this.marca = marca;
        this.ano = ano;
    }

    public String getMarca() {
        return marca;
    }
}
```

**Programa.java** — usa o molde:

```java
public class Programa {
    public static void main(String[] args) {
        Carro c = new Carro("Honda", 2024);
        System.out.println(c.getMarca());
    }
}
```

Cada classe pública deve estar em seu próprio arquivo com o nome correspondente: `Carro.java`, `Programa.java` .

---

### 7. O que levar deste guia

Classes e objetos são os dois pilares sobre os quais tudo em Java é construído. A classe é a abstração — define o que algo **é** (estado) e o que **faz** (comportamento). O objeto é a concretização — uma entidade viva na memória com valores específicos, interagindo com outros objetos.

**Regras práticas:**

- **Uma classe, uma responsabilidade.** Se a classe `Carro` começa a gerenciar pagamentos, algo está errado.
- **Campos `private` por padrão.** Exponha apenas o necessário via métodos .
- **Construtores devem deixar o objeto em estado válido.** Se um `Carro` sem `marca` não faz sentido, o construtor deve exigir uma.
- **Objetos que não são mais referenciados são coletados pelo GC.** Você não precisa (nem deve) destruí-los manualmente.

---

### Fontes

1. **Baeldung — Java Classes and Objects** — Definição canônica de classes como blueprints e objetos como instâncias, com exemplos de campos, construtores e métodos .  
   https://www.baeldung.com/java-classes-objects

2. **Dev.java — Creating Classes** — Documentação oficial sobre declaração de classes, componentes, modificadores de acesso e encapsulamento .  
   https://dev.java/learn/classes-objects/creating-classes/

3. **Dev.java — Classes and Objects** — Portal de tutoriais oficiais da Oracle cobrindo criação de classes, objetos, construtores e métodos .  
   https://dev.java/learn/classes-objects/

4. **W3Schools — Java Classes and Objects** — Tutorial prático com exemplos de criação de classes, instanciação e múltiplos objetos .  
   https://www.w3schools.com/java/java_classes.asp

5. **Oracle — The Java Tutorials** — Documentação oficial abrangente sobre classes, objetos, herança, interfaces e encapsulamento .  
   https://docs.oracle.com/javase/tutorial/reallybigindex.html

6. **Studocu — Java Objects and Classes** — Material didático explicando classe como blueprint, diferença classe vs. objeto e componentes .  
   https://www.studocu.com/in/document/andhra-university/computer-science-and-engineering/java-2nd-lesson-perfect/79937684