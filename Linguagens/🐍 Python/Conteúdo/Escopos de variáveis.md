# Escopo das variáveis em Python e a regra `LEGB` explicada

É frustrante ver “nome não definido” ou “variável local referenciada antes da atribuição” logo depois de escrever algumas linhas. O código parece estar certo, mas o Python não concorda. A causa principal é quase sempre o escopo: onde um nome é visível e a que objeto ele se refere naquele momento.

Depois que você entender como funciona a pesquisa de nomes no Python — geralmente resumida como a regra LEGB —, esses erros se tornam previsíveis e fáceis de corrigir. Neste tutorial, vou explicar variáveis e escopos em Python passo a passo.

## O que é uma variável?

Uma variável é um nome ligado a um objeto. Em Python, você cria uma ligação com o operador de atribuição `=`. Os nomes não são digitados; eles podem apontar para qualquer objeto (string, int, lista, função e assim por diante).

`customer_name = "Ava Torres" order_count = 1 total_cents = 1 + 2 * 300  # evaluated first, then bound`

Siga essas regras e convenções ao nomear variáveis para evitar erros de sintaxe e bugs sutis.

- Use só letras, números e sublinhados; os nomes não podem começar com um número.

- Não use palavras-chave como `True`, `for` ou `class` como nomes.

- Evite ocultar funções internas como `list`, `dict`, `sum` ou `max`.

- É melhor usar `snake_case` pra facilitar a leitura (PEP 8).

Esses exemplos mostram o que não fazer e os erros que o Python apresenta:

`first string value = "First string"  # spaces not allowed # SyntaxError: invalid syntax  1st_value = 10  # cannot start with a digit # SyntaxError: invalid decimal literal  True = "yes"  # cannot assign to a keyword # SyntaxError: cannot assign to True`
## Como funciona o escopo do Python (`LEGB`)

O escopo é a parte do código onde um nome aparece. Quando você usa um nome, o Python procura por ele nessa ordem (LEGB):

- **Local**: o escopo da função atual.

- **Incluir**: qualquer escopo de função externo (para funções aninhadas).

- **Global**: o nível superior do módulo (espaço de nomes do módulo).

- **Incorporados**: nomes definidos pelo Python em `builtins` (por exemplo, `len`, `print`).

Os nomes ficam em namespaces (tipo dicionários que juntam nomes com objetos). O escopo é sobre quais namespaces o Python consulta para resolver um nome em um determinado ponto do código.

### Âmbito local

Os nomes atribuídos dentro de uma função são locais para essa função, a menos que seja declarado o contrário. Elas não são visíveis fora da função.

`def show_order_id():     order_id = 42     print("inside function:", order_id)  show_order_id() print("outside function:", order_id)  # NameError`

O Python decide o escopo na hora da compilação. Se uma função atribuir um nome em qualquer lugar do seu corpo, todos os usos desse nome na função serão tratados como locais — levando a um erro comum de “ `UnboundLocalError` ” (nome não definido) quando você ler antes de atribuir:

`discount_rate = 0.10  # module-level (global)  def price_with_discount(amount_cents):     print("configured discount:", discount_rate)  # looks local because of the assignment below     discount_rate = 0.20  # assignment makes 'discount_rate' local in this function     return int(amount_cents * (1 - discount_rate))  # UnboundLocalError: cannot access local variable 'discount_rate' where it is not associated with a value`

Para usar o nome no nível do módulo na função, evite atribuí-lo ou marque-o como " `global` " (abordado abaixo).

### Escopo de inclusão (fechamentos)

As funções aninhadas podem ver os nomes da função que as envolve diretamente. Para reassociar esse nome (não apenas lê-lo), declare-o como `nonlocal`.

`def make_step_counter():     count = 0  # enclosing scope for 'increment'      def increment():         nonlocal count  # rebind the 'count' in the nearest enclosing function         count += 1         return count      return increment  step = make_step_counter() print(step())  # 1 print(step())  # 2`

Sem `nonlocal`, atribuir a `count` dentro de `increment()` criaria um novo nome local e deixaria o `count` externo inalterado.

### Alcance global

Os nomes atribuídos no nível superior de um módulo ficam no namespace global do módulo. Qualquer função pode ler esses dados. Para atribuir um nome no nível do módulo de dentro de uma função, declare-o como ` `global``.

`greeting = "Hello"  def greet_city(city_name):     print(greeting, city_name)  # reads global  def set_greeting(new_greeting):     global greeting     greeting = new_greeting     # rebinds global  greet_city("Nairobi")  # Hello Nairobi set_greeting("Hi") greet_city("Nairobi")  # Hi Nairobi`

Use `global` com moderação. É melhor passar valores como parâmetros e devolver resultados para manter o código testável e previsível.

### Escopo integrado (e uma observação sobre palavras-chave)

O escopo integrado tem nomes como `len`, `print` e `Exception`. Evite sombreá-los, ou você perderá o acesso ao built-in para esse escopo.

`list = [1, 2, 3]     # shadows the built-in 'list' constructor list("abc")          # TypeError: 'list' object is not callable del list             # fix by deleting the shadowing name`

Palavras-chave (como `if`, `for`, `def`) fazem parte da sintaxe do Python, não do namespace embutido, e nunca podem ser usadas como identificadores.

## Blocos, loops e compreensões

As instruções de bloco e compreensões do Python têm comportamentos de escopo específicos que muitas vezes surpreendem os desenvolvedores que vêm de outras linguagens.

### Não tem escopo de bloco para `if`/`for`/`while`/`with`

As atribuições dentro desses blocos afetam o escopo que as contém (função ou módulo). As variáveis do loop também continuam definidas depois que o loop termina.

`if True:     status = "ready" print(status)  # "ready"  for i in range(3):     pass print(i)  # 2 (the last value from the loop)`

### As compreensões isolam sua variável de iteração

Compreensões de lista, dicionário e conjunto têm seu próprio escopo local para variáveis de loop. A variável de iteração não vaza para o escopo circundante.

`numbers = [1, 2, 3] [x for x in numbers] print("x" in globals() or "x" in locals())  # False`

O Python 3.12 (PEP 709) incorporou compreensões para aumentar a velocidade, mantendo esse isolamento; você ainda tem variáveis de loop claras e sem vazamentos, com execução mais rápida.

## Usando o `global` Palavra-chave

Declare um nome como ` `global` ` dentro de uma função quando precisar reassociar uma variável no nível do módulo. Coloque a declaração perto do topo da função para que fique claro qual variável você está modificando.

`tax_rate = 0.08  def configure_tax(rate):     global tax_rate     tax_rate = float(rate)  def total_with_tax(cents):     return int(cents * (1 + tax_rate))  configure_tax(0.10) print(total_with_tax(1000))  # 1100`

## Usando o `nonlocal` Palavra-chave

Use `nonlocal` para reassociar um nome do escopo da função mais próxima. Isso é comum em closures que mantêm o estado.

`def make_accumulator(start=0):     total = start     def add(amount):         nonlocal total         total += amount         return total     return add  acc = make_accumulator() print(acc(5))   # 5 print(acc(10))  # 15`

Usar `nonlocal` para um nome que não está definido em uma função envolvente é um erro de " `SyntaxError`". Se o nome for global, use `global` em vez disso.

## `locals()` e `globals()` em 2025

As funçõessão úteis para inspeção e depuração, não para atualizar variáveis. A partir do Python 3.13 (PEP 667), cada chamada para locals() em uma função retorna um instantâneo independente. Editar esse instantâneo não altera os locais reais. Confirmei esse comportamento executando o seguinte trecho no Python 3.13:

`def probe():     project = "alpha"     snap1 = locals()     snap1["project"] = "beta"  # edits the snapshot only     observed = project         # still "alpha"     snap2 = locals()           # new snapshot     return snap1["project"], observed, snap2["project"]  print(probe())  # ('beta', 'alpha', 'alpha')`

Use `globals()` da mesma forma para o namespace do módulo. Prefira parâmetros explícitos e valores de retorno em vez de pesquisas dinâmicas ao escrever código de aplicativo.

## Erros comuns e como resolvê-los

Esses erros são a maioria dos problemas relacionados ao escopo que vejo na prática.

- `UnboundLocalError` de atribuir em uma função: O Python trata um nome como local se ele for atribuído em qualquer lugar da funçã. Mova a leitura após a atribuição, renomeie ou adicione global/não local, conforme apropriado.

- Esperando escopo de bloco:  Os nomes atribuídos em if/for/while/with persistem no escopo que os contém. Use funções auxiliares mais restritas para limitar o escopo.

- Sombreamento de elementos embutidos: Evite nomes de identificação como “list”, “dict”, “sum”, “id” ou “min”. Escolha nomes descritivos como “ `customer_list` ” ou “ `min_allowed` ”.

- Esquecendo o ` `nonlocal` ` nos closures: Se você quiser atualizar uma variável de função externa, declare-a como não local. Caso contrário, você cria um novo local e o valor externo não muda.

- Confundindo palavras-chave com built-ins: Palavras-chave (sintaxe) nunca podem ser usadas como nomes. Os elementos embutidos podem ser sombreados, mas não devem ser.

## Melhores práticas que mantêm o escopo simples

Esses hábitos tornam o código mais fácil de ler, testar e depurar.

- Dá preferência a funções pequenas e específicas. Defina as variáveis no escopo mais restrito possível.

- Passe dados para dentro e para fora por meio de parâmetros e valores de retorno. Minimize o estado no nível do módulo.

- Use os closures de propósito. Documente qual nome externouma função aninhada captura e use nonlocal quando você realmente precisar refazer a ligação.

- Escolhanomes descritivose que não causem confusão. Um sublinhado inicial (por exemplo, `_cache`) indica uso interno.

- Tratelocals()/globals() como diagnósticos somente leitura, não como um mecanismo de configuração.

## Conclusão

O Python resolve nomes procurando nos escopos Local → Envolvente → Global → Incorporado. Entender essa ordem — e quando usar global ou não local — elimina erros comuns como `NameError` e `UnboundLocalError`. Mantenha as variáveis no menor escopo possível, evite ocultar funções internas e use closures de forma consciente. Com esse modelo mental, suas funções se comportam de maneira previsível e o escopo deixa de ser uma fonte de surpresas.