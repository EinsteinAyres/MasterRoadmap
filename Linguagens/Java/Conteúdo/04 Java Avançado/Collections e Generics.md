
## Generics: tipos que se adaptam

### 1. O problema que generics resolvem

Antes do Java 5, tudo em coleções era `Object`. Você armazenava qualquer coisa, mas na hora de recuperar precisava de um cast manual — e torcia para o tipo estar correto:

```java
List lista = new ArrayList();
lista.add("Ana");
lista.add(42);           // Ninguém impede — a lista aceita Object

String nome = (String) lista.get(0);  // Cast obrigatório
String erro = (String) lista.get(1);  // ClassCastException em execução
```

Dois problemas: o código é verboso (cast em toda recuperação) e **inseguro** (erros de tipo só aparecem em execução, talvez em produção). O compilador não ajuda — ele vê `Object`, não sabe o que você pretendia armazenar .

**Generics** resolvem isso permitindo que classes, interfaces e métodos sejam **parametrizados por tipo**. Você diz ao compilador: "esta lista só aceita Strings", e ele garante isso em tempo de compilação — sem custo de execução (via type erasure) e sem casts manuais :

```java
List<String> nomes = new ArrayList<>();
nomes.add("Ana");
// nomes.add(42);        // ERRO DE COMPILAÇÃO — 42 não é String
String nome = nomes.get(0);  // Sem cast — o compilador sabe que é String
```

---

### 2. Anatomia de um tipo genérico

Generics usam **parâmetros de tipo** — placeholders entre `< >` que são substituídos por tipos concretos no momento do uso :

```java
// Classe genérica
public class Caixa<T> {           // T é o parâmetro de tipo
    private T conteudo;

    public void guardar(T conteudo) {
        this.conteudo = conteudo;
    }

    public T pegar() {
        return conteudo;
    }
}
```

```java
Caixa<String> caixaDeTexto = new Caixa<>();  // T = String
caixaDeTexto.guardar("Olá");

Caixa<Integer> caixaDeNumero = new Caixa<>(); // T = Integer
caixaDeNumero.guardar(42);
```

**Convenções de nomes para parâmetros de tipo:**

| Nome | Significado | Uso típico |
|------|-------------|------------|
| `T` | Type | Tipo genérico qualquer |
| `E` | Element | Elemento de coleção (`List<E>`) |
| `K` | Key | Chave de mapa (`Map<K,V>`) |
| `V` | Value | Valor de mapa (`Map<K,V>`) |
| `N` | Number | Número |
| `S`, `U`, `V` | Tipos adicionais | Segundo, terceiro tipo |

---

### 3. Type Erasure: como funciona por baixo

Generics em Java são implementados via **type erasure** (apagamento de tipo). O compilador verifica os tipos em tempo de compilação, garante a segurança, e depois **remove** toda informação de tipo genérico do bytecode. Em tempo de execução, `List<String>` e `List<Integer>` são a mesma coisa: `List` .

```java
List<String> strings = new ArrayList<>();
List<Integer> inteiros = new ArrayList<>();

System.out.println(strings.getClass() == inteiros.getClass());  // true — ambos são ArrayList
```

**Implicações práticas:**
- Você não pode instanciar um tipo genérico diretamente: `new T()` é erro.
- Você não pode criar arrays de tipos genéricos: `new List<String>[10]` é erro.
- `instanceof` com tipos genéricos não funciona: `obj instanceof List<String>` é erro de compilação.
- Métodos sobrecarregados com tipos genéricos que diferem apenas no parâmetro de tipo geram conflito porque, após o erasure, a assinatura é a mesma.

---

### 4. Métodos genéricos

Métodos podem ter seus próprios parâmetros de tipo, independentes da classe :

```java
public class Util {
    public static <T> T primeiro(List<T> lista) {
        if (lista.isEmpty()) {
            return null;
        }
        return lista.get(0);
    }

    public static <T> List<T> singletonList(T elemento) {
        List<T> lista = new ArrayList<>();
        lista.add(elemento);
        return lista;
    }
}
```

```java
String primeiroNome = Util.primeiro(List.of("Ana", "Carlos"));  // T = String
Integer primeiroNumero = Util.<Integer>primeiro(List.of(1, 2));  // T explícito (opcional)
```

O parâmetro de tipo é declarado **antes** do tipo de retorno: `public static <T> T metodo(...)`.

---

### 5. Bounded Type Parameters: limitando os tipos

Você pode restringir quais tipos são aceitos como parâmetro genérico usando `extends` (que, em generics, significa "extends ou implements") :

```java
// T deve ser Number ou subtipo de Number
public class Calculadora<T extends Number> {
    private T valor;

    public Calculadora(T valor) {
        this.valor = valor;
    }

    public double dobrar() {
        return valor.doubleValue() * 2;  // doubleValue() existe porque T extends Number
    }
}
```

```java
Calculadora<Integer> c1 = new Calculadora<>(42);    // OK — Integer extends Number
Calculadora<Double> c2 = new Calculadora<>(3.14);   // OK — Double extends Number
// Calculadora<String> c3 = new Calculadora<>("A"); // ERRO — String não é Number
```

**Múltiplos bounds:** `T extends Number & Comparable<T>` — T deve ser Number **e** implementar Comparable. O primeiro bound pode ser uma classe; os demais devem ser interfaces.

**Wildcards (`?`):** para quando você não sabe ou não se importa com o tipo exato .

| Wildcard | Significado | Exemplo |
|----------|-------------|---------|
| `List<?>` | Lista de tipo desconhecido | `void imprimir(List<?> lista)` |
| `List<? extends Number>` | Lista de Number ou subtipo (covariante) | `double somar(List<? extends Number> nums)` |
| `List<? super Integer>` | Lista de Integer ou supertipo (contravariante) | `void adicionar(List<? super Integer> dest)` |

**PECS:** Producer Extends, Consumer Super. Se você **lê** da coleção, use `? extends` (produz elementos). Se você **escreve** na coleção, use `? super` (consome elementos). Se ambos, use o tipo exato .

---

### 6. Generics e arrays: não se misturam bem

Arrays em Java são **covariantes**: `String[]` é um subtipo de `Object[]`. Generics são **invariantes**: `List<String>` **não** é subtipo de `List<Object>`. Essa diferença torna a combinação problemática .

```java
// Arrays covariantes — compila, mas falha em execução
Object[] array = new String[10];
array[0] = 42;  // ArrayStoreException em execução

// Generics invariantes — erro em compilação
// List<Object> lista = new ArrayList<String>();  // ERRO — tipos incompatíveis
```

**A solução com wildcards:**

```java
// Covariância controlada com extends
List<? extends Number> numeros = new ArrayList<Integer>();  // OK

// Contravariância controlada com super
List<? super Integer> inteiros = new ArrayList<Number>();   // OK
```

---

### 7. Restrições e armadilhas

**Não instancie tipos genéricos com primitivos.** `List<int>` é erro. Use o wrapper: `List<Integer>`.

**Não crie instâncias de parâmetros de tipo.** `new T()` não compila. Se precisar, passe um `Supplier<T>` ou `Class<T>` como parâmetro.

**Não crie arrays de tipos parametrizados.** `new List<String>[10]` é proibido. Use `new ArrayList<>()` ou `new List<?>[10]` com cast.

**Não use `instanceof` com tipos genéricos.** Após o erasure, `obj instanceof List<String>` não faz sentido. Use `obj instanceof List<?>` e faça casts manuais com `@SuppressWarnings`.

**Sobrecarga com genéricos.** Dois métodos com a mesma assinatura após o erasure geram conflito. `void metodo(List<String>)` e `void metodo(List<Integer>)` são o mesmo método após o erasure (`void metodo(List)`).

---

### 8. O que levar deste guia

Generics tornam Java uma linguagem mais segura e expressiva. Eles transferem erros de tipo da execução para a compilação, eliminam casts manuais e permitem que o compilador verifique a consistência do seu código.

**Regras práticas:**

- **Sempre use generics com coleções.** `List<String>` em vez de `List` cru.
- **Use `List<?>` quando o tipo não importar.** Para métodos que apenas percorrem ou inspecionam a coleção.
- **PECS: Producer Extends, Consumer Super.** Leitura → `? extends T`. Escrita → `? super T`. Ambos → `T` puro.
- **Não lute contra o type erasure.** Ele existe. `instanceof List<String>` não funciona e não há como contornar.
- **Mantenha os métodos genéricos simples.** Se você tem três bounds e quatro wildcards aninhados, algo está errado no design.

---

### Fontes

1. **Oracle — Java Tutorials: Generics** — Documentação oficial sobre tipos genéricos, type erasure, bounded type parameters e wildcards .  
   https://docs.oracle.com/javase/tutorial/java/generics/index.html

2. **Dev.java — Generics** — Guia oficial com exemplos de classes genéricas, métodos genéricos e PECS .  
   https://dev.java/learn/generics/

3. **Baeldung — Java Generics** — Tutorial abrangente sobre parâmetros de tipo, wildcards, type erasure e boas práticas .  
   https://www.baeldung.com/java-generics

4. **GeeksforGeeks — Generics in Java** — Definição, sintaxe, tipos parametrizados, wildcards e restrições .  
   https://www.geeksforgeeks.org/generics-in-java/

5. **Programiz — Java Generics** — Fundamento de generics com exemplos de classes e métodos genéricos .  
   https://www.programiz.com/java-programming/generics

6. **W3Schools — Java Generics** — Explicação prática com exemplos de classes genéricas e bounded types .  
   https://www.w3schools.com/java/java_generics.asp