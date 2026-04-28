
## Strings: sequências imutáveis de caracteres

### 1. O que é uma String

`String` é uma classe que representa uma sequência de caracteres Unicode. Está no pacote `java.lang` — não precisa de import. É, de longe, o tipo mais usado em qualquer aplicação Java: logs, mensagens, dados de entrada e saída, nomes, caminhos de arquivo, protocolos de rede — tudo passa por String.

Três características definem String em Java:

**É uma classe, não um primitivo.** `String` é um tipo de referência. Variáveis do tipo `String` armazenam ponteiros para objetos na Heap.

**É imutável.** Uma vez criado, um objeto String nunca muda. Métodos como `toUpperCase()`, `trim()` e `concat()` não modificam o original — devolvem um novo objeto String.

**Tem tratamento especial da linguagem.** Literais entre aspas duplas (`"texto"`), concatenação com `+`, e o String Pool são suportados diretamente pelo compilador e pela JVM.

---

### 2. Criação de Strings

Há duas formas fundamentalmente diferentes de criar uma String:

**Literal — vai para o String Pool:**

```java
String a = "java";
String b = "java";
System.out.println(a == b);  // true — mesmo objeto no Pool
```

Quando o compilador encontra `"java"`, ele coloca esse literal no **String Pool** — uma área especial da Heap. Se o mesmo literal já existir no Pool, a referência é reutilizada. Isso economiza memória.

**Com `new` — cria um objeto novo, fora do Pool:**

```java
String c = new String("java");
System.out.println(a == c);  // false — objeto diferente, fora do Pool
```

`new String()` força a criação de um novo objeto, mesmo que o literal já exista no Pool. Raramente é necessário — evite.

**Outras formas comuns:**

```java
String vazia = "";                           // String vazia (não é null)
String fromChars = new String(charArray);    // A partir de char[]
String fromBytes = new String(bytes, UTF_8); // A partir de byte[] com charset
String valueOf = String.valueOf(42);         // "42"
String formatada = String.format("Valor: %.2f", 3.14159);  // "Valor: 3.14"
```

---

### 3. Imutabilidade: por que e com quais consequências

Uma String não pode ser alterada depois de criada. Isso não é acidente — é uma decisão de design com benefícios concretos:

**Segurança:** Strings são usadas para senhas, nomes de arquivo, URLs, paths de rede. Se fossem mutáveis, um trecho de código poderia alterar o conteúdo depois da validação.

**Thread-safety:** objetos imutáveis são automaticamente seguros entre threads. Nenhuma sincronização é necessária.

**Caching de hashcode:** como o conteúdo nunca muda, o `hashCode()` é calculado uma vez e cached. `HashMap<String, ...>` é eficiente por causa disso.

**String Pool:** sem imutabilidade, o Pool não funcionaria — duas referências apontando para o mesmo objeto poderiam interferir uma com a outra.

**A consequência prática:** cada "modificação" cria um novo objeto:

```java
String s = "Java";
s = s + " 21";        // Novo objeto é criado — "Java 21". O objeto "Java" permanece no Pool.
s = s.toUpperCase();  // Outro novo objeto — "JAVA 21".
```

Isso é inofensivo para uso esporádico, mas em loops pode gerar uma enxurrada de objetos temporários, pressionando o Garbage Collector.

---

### 4. Comparação: `==` vs. `equals()`

**`==` compara referências** — verifica se duas variáveis apontam para o mesmo objeto na memória.

**`equals()` compara conteúdo** — verifica se as sequências de caracteres são iguais.

```java
String a = "java";
String b = "java";               // Mesmo objeto do Pool
String c = new String("java");   // Objeto novo, mesmo conteúdo

System.out.println(a == b);      // true — mesma referência (Pool)
System.out.println(a == c);      // false — referências diferentes
System.out.println(a.equals(b)); // true — conteúdo igual
System.out.println(a.equals(c)); // true — conteúdo igual
```

**Regra de ouro:** use sempre `.equals()` para comparar Strings, a menos que você esteja deliberadamente verificando identidade de referência.

**Null-safe:** inverter a ordem evita `NullPointerException`:

```java
String entrada = null;
// entrada.equals("admin");     // NullPointerException!
"admin".equals(entrada);        // false — seguro
```

---

### 5. O String Pool e `intern()`

O String Pool armazena literais para reuso. O método `intern()` adiciona manualmente uma String ao Pool (ou retorna a cópia existente):

```java
String a = new String("java");  // Objeto novo, fora do Pool
String b = a.intern();          // Busca/adiciona no Pool
String c = "java";              // Referência do Pool

System.out.println(a == b);     // false — a está fora, b está no Pool
System.out.println(b == c);     // true — ambos no Pool
```

`intern()` raramente é necessário em código de aplicação. É útil quando você tem muitas Strings duplicadas e quer economizar memória — mas o custo de internar é alto e o Pool tem tamanho limitado.

---

### 6. Métodos essenciais

**Informação e busca:**

```java
String s = "  Java 21  ";

s.length();                    // 11
s.isEmpty();                   // false
s.isBlank();                   // false (Java 11+: true se só tem whitespace)
s.charAt(0);                   // ' '
s.indexOf('a');                // 3 (primeira ocorrência)
s.lastIndexOf('a');            // 4
s.contains("21");              // true
s.startsWith("Ja");            // false (tem espaços antes)
s.endsWith("21  ");            // true
```

**Transformação:**

```java
s.trim();                      // "Java 21" — remove espaços das pontas
s.strip();                     // "Java 21" — versão moderna, lida com Unicode
s.toUpperCase();               // "  JAVA 21  "
s.toLowerCase();               // "  java 21  "
s.substring(2, 6);             // "Java" — índices 2 a 5
s.replace('a', 'x');           // "  Jxvx 21  "
s.replace("21", "LTS");        // "  Java LTS  "
s.replaceAll("\\s+", "");      // "Java21" — regex
s.split(" ");                  // ["", "", "Java", "21", "", ""]
```

**Comparação:**

```java
"abc".equals("ABC");                    // false
"abc".equalsIgnoreCase("ABC");          // true
"abc".compareTo("abd");                 // -1 (lexicográfica)
```

**Concatenação e construção:**

```java
"Java" + " " + 21;                     // "Java 21"
"Java".concat(" 21");                   // "Java 21"
String.join(", ", "A", "B", "C");       // "A, B, C"
```

---

### 7. StringBuilder e StringBuffer

Concatenar Strings com `+` dentro de loops é ineficiente — cada `+` cria um novo objeto. Para concatenação em loops ou construção incremental, use `StringBuilder`:

```java
// Péssimo — cria um novo objeto String a cada iteração
String resultado = "";
for (int i = 0; i < 1000; i++) {
    resultado += i;  // Cria String "0", depois "01", depois "012"...
}

// Excelente — modifica o mesmo buffer
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
String resultado = sb.toString();
```

**StringBuilder vs. StringBuffer:**

| Característica | StringBuilder | StringBuffer |
|---------------|---------------|--------------|
| **Sincronização** | Não | Sim (thread-safe) |
| **Performance** | Mais rápido | Mais lento (overhead de locks) |
| **Uso** | Código single-thread (a maioria) | Código multi-thread legado |
| **Desde** | Java 5 | Java 1.0 |

**Regra:** use `StringBuilder`, a menos que tenha um motivo específico para `StringBuffer`. O compilador Java inclusive traduz concatenações com `+` para `StringBuilder` internamente — mas apenas para expressões simples, não para loops.

---

### 8. Strings e a memória

**Quanto uma String ocupa?**

Uma String é um objeto com:
- Cabeçalho do objeto: 12-16 bytes.
- Referência para o array de caracteres: 4-8 bytes.
- Campo `hash` (int): 4 bytes.
- O array `byte[]` ou `char[]` interno com os caracteres: depende do tamanho.

Uma String de 10 caracteres pode ocupar 60-80 bytes no total. Strings são objetos caros — trate-as com respeito em estruturas de dados massivas.

**Compact Strings (Java 9+):**

Desde o Java 9, a JVM armazena Strings como `byte[]` em vez de `char[]` quando possível. Se todos os caracteres são Latin-1 (0-255), cada caractere ocupa 1 byte em vez de 2. Isso reduz o consumo de memória pela metade para a maioria das Strings em aplicações ocidentais. O processo é transparente — você não precisa fazer nada.

---

### 9. Text Blocks (Java 15+)

Para Strings multilinha (HTML, SQL, JSON, XML), os **text blocks** eliminam a verbosidade de concatenações com `\n` e aspas escapadas:

```java
// Antes (Java 14-)
String json = "{\n" +
              "  \"nome\": \"Ana\",\n" +
              "  \"idade\": 30\n" +
              "}";

// Java 15+
String json = """
    {
      "nome": "Ana",
      "idade": 30
    }
    """;
```

**Características:**
- Delimitado por `"""` (três aspas duplas).
- A indentação comum é removida automaticamente (o compilador usa a linha de menor indentação como referência).
- Aspas dentro do bloco não precisam ser escapadas.
- `\` no final da linha suprime a quebra de linha no resultado.
- `\s` insere um espaço explícito que não é removido pela formatação.

---

### 10. Conversão entre tipos

**String ↔ números:**

```java
int i = Integer.parseInt("42");
double d = Double.parseDouble("3.14");
boolean b = Boolean.parseBoolean("true");

String s1 = String.valueOf(42);     // "42"
String s2 = Integer.toString(42);   // "42"
String s3 = "" + 42;                // "42" — funciona, mas não use em loops
```

**String ↔ arrays:**

```java
char[] chars = "Java".toCharArray();           // ['J', 'a', 'v', 'a']
String s = new String(chars);                  // "Java"

String[] partes = "A,B,C".split(",");          // ["A", "B", "C"]
String unido = String.join("-", partes);       // "A-B-C"
```

**String ↔ datas (java.time, Java 8+):**

```java
LocalDate data = LocalDate.parse("2024-01-15");
String texto = data.format(DateTimeFormatter.ofPattern("dd/MM/yyyy"));  // "15/01/2024"
```

---

### 11. Boas práticas

**Nunca use `==` para comparar conteúdo.** É o erro mais comum com Strings. Use sempre `.equals()`.

**Evite `new String()`.** Literais vão para o Pool e são reutilizados. `new String()` cria objetos desnecessários. A única exceção é quando você recebe uma substring de uma String gigante e quer liberar o array maior — mas isso praticamente não se aplica desde Java 7.

**Use `StringBuilder` em loops.** Cada `+` em loop é um novo objeto no Heap. `StringBuilder` modifica o mesmo buffer.

**Prefira `String.isEmpty()` e `String.isBlank()` a comparar com `""`.** Mais expressivo e menos propenso a erro.

**Use `text blocks` para strings multilinha.** Mais legível e menos propenso a erros de formatação.

**Cuidado com `null`.** Prefira métodos que retornam String vazia (`""`) em vez de `null`. Se `null` for inevitável, use `"constante".equals(variavel)` para comparação null-safe.

**Considere `String.format()` ou `String.formatted()` para formatação complexa.** Mais legível que concatenação manual:

```java
// Menos legível
String msg = "Usuário " + nome + " tem " + idade + " anos";

// Mais legível
String msg = "Usuário %s tem %d anos".formatted(nome, idade);
```

---

### 12. Resumo em uma frase

String é imutável, onipresente e tratada com carinho pela JVM — use literais e o String Pool a seu favor, fuja de `new String()` como de praga, e quando o assunto for concatenação em escala, troque o `+` pelo `StringBuilder` sem pensar duas vezes.

---

### Fontes

1. Oracle, "String (Java Platform SE 17)" — documentação oficial da classe String 
2. Oracle, "Java Tutorials – Strings" — guia oficial sobre criação, imutabilidade e métodos 
3. Baeldung, "Java String Guide" — guia abrangente com todos os métodos, comparações e melhores práticas 
4. Dev.java, "String and StringBuilder" — documentação oficial sobre imutabilidade e concatenação eficiente 
5. GeeksforGeeks, "Strings in Java" — fundamentos, Pool, métodos e text blocks 
6. OpenJDK, "JEP 254: Compact Strings" — especificação oficial da otimização introduzida no Java 9 
7. OpenJDK, "JEP 378: Text Blocks" — especificação oficial dos text blocks do Java 15