# O que é, como criar e manipular e suas diferenças com as Listas


Afinal, qual a **diferença entre as tuplas e as listas** na linguagem [**Python**](https://www.alura.com.br/artigos/python)? Veja quando utilizar uma e outra e perca essa grande dúvida!

## Exemplo prático

Estou trabalhando em uma aplicação de mapeamento que se baseia em [**coordenadas geográficas**](https://pt.wikipedia.org/wiki/Coordenadas_geogr%C3%A1ficas) para localizar endereços. Mas como posso armazenar estas coordenadas?

Uma ideia é termos uma variável para latitude e uma para longitude, mais ou menos dessa forma:

```

latitude = -23.588254
longitude = -46.632477
```

Por enquanto tudo bem. Mas quero trabalhar com rotas e distâncias, então preciso de mais de uma coordenada. E aí, como fazemos? Temos que criar outras variáveis de latitude e longitude?

```

latitude = -23.588254
longitude = -46.632477
outra_latitude = 48.8583698
outra_longitude = 2.2944833
```

Bem… isso já está ficando estranho, não é? À medida que a quantidade de coordenadas vai crescendo, nosso código está ficando mais confuso e nos dando mais propensão a erros.

O ideal seria não deixar **latitude** e **longitude** separados, mas juntos em uma variável só, realmente organizados como uma coordenada. Quando queremos **guardar juntos mais de um valor no Python**, a primeira coisa que vem à mente são… as [**listas**](https://www.alura.com.br/artigos/operacoes-basicas-com-listas-no-python)! Vamos usá-las, então:

```

caelum_coordenadas = [-23.588254, -46.632477]
torre_eiffel_coordenadas = [48.8583698, 2.2944833]
```

Agora já melhorou bastante, temos as nossas duas coordenadas separadas e organizadas! Mas **será que uma lista é o melhor tipo para o que queremos fazer** no Python?

### Os problemas do uso da lista para esses casos

Listas no [Python](https://www.alura.com.br/artigos/python) são incrivelmente poderosas e úteis, mas será que são o ideal no nosso caso? Em primeiro lugar, podemos pensar no que pode ser problemático na prática, como a **mutabilidade** das listas.

Já sabemos que [**listas são mutáveis**](https://www.alura.com.br/artigos/como-fazer-copia-de-lista-python), ou seja, que os valores de um mesmo objeto lista podem ser alterados. O que aconteceria, então, se alguém adicionasse um valor em `caelum_coordenadas` com o método **append()** ou ainda removesse um com o **remove()**. Essa variável não teria mais sentido nenhum!

Pior seria se alguém simplesmente mudasse um dos valores, fazendo por exemplo isso:

```

caelum_coordenadas = [-23.588254, -46.632477]
caelum_coordenadas[1] = -5.0
```

`caelum_coordenadas` agora apontaria para uma coordenada sem relação nenhuma com o que era pra ter o endereço da Caelum São Paulo. Agora, na verdade, [aponta para o meio do Oceano Atlântico](https://www.google.com.br/maps/place/23%C2%B035'17.7%22S+5%C2%B000'00.0%22W/@-23.975626,-19.814652,3z/data=!4m5!3m4!1s0x0:0x0!8m2!3d-23.58825!4d-5)! Isso não faz sentido nenhum pra nós…

Além dessa questão prática, temos um grave problema de **semântica**. Usamos uma lista para representar a coordenada, certo? Mas se é uma lista, **é uma lista de quê?** Afinal,são dois valores diferente - **latitude** e **longitude**.

A [**própria documentação do Python**](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences) indica que os elementos de uma lista são, geralmente, **homogêneos**. Ou seja, têm o mesmo tipo e significado. Com as nossas coordenadas, temos valores **heterogêneos**, pois representam duas coisas diferentes (para então formar uma só).

Além disso, direto de uma definição de lista no dicionário: uma relação **ordenada** de coisas **relacionadas**. Repare que o que queremos não é **ordem**, mas **estrutura** a relação não é de que latitude vem primeiro que longitude, mas que latitude está na primeira posição, enquanto longitude na segunda.

O que **além da lista** poderia ser usado para **satisfazer melhor nossa necessidade**? A **Tupla**!

## O que é Tupla? Como manipular?

O Python nos disponibiliza um tipo que preenche muito melhor as características que procurávamos — as [**tuplas**](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences). Tuplas podem ser consideradas similares às listas, mas suas diferenças são cruciais.

Quanto à **sintaxe**, a tupla se diferencia por substituir os colchetes (`[]`) das listas por parênteses (`()`):

```

estudos_coordenadas = (-23.588254, -46.632477)
print(type(estudos_coordenadas))
```

Só para checar o tipo:

```

<class 'tuple'>
```

Em primeiro lugar, a diferença mais explícita pela própria comunidade Python é que **tuplas são imutáveis**. Isso significa que não podemos alterar um mesmo objeto tupla, ou seja, mudar uma de suas referências internas (seus valores), nem adicionar ou remover elemento algum:

![Testes com a tupla](https://cdn-wcsm.alura.com.br/2025/04/tupla-testes.png)

Ainda assim, podemos criar um novo objeto tupla com o operador de soma (`+`):

![Soma com a tupla](https://cdn-wcsm.alura.com.br/2025/04/tuplaplus.png)

> Para uma tupla de valor único, devemos sempre colocar uma vírgula (`,`) no final do valor, mesmo com os parênteses, ou o Python não interpretará como tupla.

Outro ponto importante e que pode gerar confusão a respeito dessa imutabilidade é que **tuplas podem conter objetos mutáveis**, como listas:

![Tuplas podem conter objetos mutáveis](https://cdn-wcsm.alura.com.br/2025/04/tupla-lista.png)

> "Ué, mas nós alteramos um valor de nossa tupla sem mudar de objeto! Isso significa que ela não é imutável?"

Não, porque na verdade não alteramos nada na tupla, já que as referências que ela guarda são as mesmas! Modificamos a lista dentro dela, de novo, **sem mudar o identificador**, e assim isso foi permitido.

Além disso, qual a diferença entre as tuplas e as listas? Para muitos desenvolvedores, aparentemente acaba aí, como se tuplas fossem **apenas** listas constantes, imutáveis. Isso, entretanto, não é bem verdade.

## Qual a diferença entre listas e tuplas?

De fato, tecnicamente as diferenças mais claras são essas - tuplas se comportam como **listas estáticas**. Assim, ainda podemos deduzir (e confirmar) que, por conta disso, tuplas ocupam menos espaço na memória comparadas a listas:

![Tupla e lista na memória do computador](https://cdn-wcsm.alura.com.br/2025/04/tupla-lista-sizeof.png)

Mas e além disso?

Quando tocamos nos problemas das listas, falamos sobre a **semântica**, isto é, o que dá sentido ao nosso código. Verificamos que uma lista realmente não era ideal para nosso caso, como a própria documentação do Python especificava.

Da mesma forma, a documentação ainda nos apresenta um sentido para as tuplas, indicando que elas geralmente contêm uma sequência **heterogênea** de elementos, ou seja, elementos de diversos tipos e significados.

Como já vimos, listas, por outro lado, são mais próprias para armazenamento de valores de mesmo significado, isto é, **homogêneos**. Isso não significa que uma lista não possa armazenar valores de diferentes tipos, mas que esse é comumente o sentido dado a ela.

Enfim, listas geralmente trabalham com **ordem**, enquanto tuplas geralmente trabalham com **estrutura**. O primeiro elemento de nossa tupla `caelum_coordenadas` é a latitude, e o segundo é a longitude - a posição não pode ser invertida, porque elas dão sentido para os valores.

Pensando que as tuplas trabalham com estrutura, e se nós pudéssemos especificar o que cada valor significa? Isto é, dar um nome para cada posição. Será que podemos fazer isso?

## Criando tuplas nomeadas com a função `namedtuple()`

Já que as tuplas se baseiam na estrutura dos nosso dados, seria interessante poder nomeá-los. Pensando nisso, no Python, já existe uma maneira para implementarmos nossas tuplas com campos nomeados - com **a função [[namedtuple]]()**, localizada no módulo **collections** da biblioteca padrão da linguagem..

A função `namedtuple()` é, na verdade, uma fábrica de tuplas nomeadas. Isto é, ela nos retorna um objeto de uma subclasse de tupla que, além de darem acesso aos elementos pelo índice, nos permitem dar significado nomeado para cada um deles e acessá-los dessa forma:

![Tupla nomeada](https://cdn-wcsm.alura.com.br/2025/04/namedtuple.png)

Assim temos a tupla com mais alguns benefícios de legibilidade!

Uma alternativa às tuplas nomeadas poderia ser usar [**dicionários**](https://docs.python.org/3/tutorial/datastructures.html#dictionaries), que são outro tipo nativo do Python que nos permite mapeamento de objetos, como você pode ver na documentação da linguagem ;).

## Conclusão

Listas e tuplas são algumas estruturas de dados muito utilizadas quando estamos programando. Essas duas estruturas nos permitem guardar nossos dados agrupados, contudo suas semelhanças se encerram por aí, como pudemos ver nesse post.

Conseguimos desmistificar a ideia de que a tupla é **apenas** uma lista imutável, e entendemos como as listas têm uma função semântica diferente das tuplas.

**Listas** ([saiba mais sobre listas no Python](https://www.alura.com.br/artigos/listas-no-python)) são usadas para guardar dados de mesmo tipo e significado, de modo que a ordem pode fazer diferença.

Já as tuplas, além de serem imutáveis, têm uma semântica muito mais estrutural, armazenando dados heterogêneos. Isto é, a posição do dado pode alterar o seu significado.

Também conhecemos um outro tipo que pode nos auxiliar com o bom uso da tupla - a tupla nomeada, gerada pela função `namedtuple()`. Com ela, podemos dar um nome para cada elemento da nossa tupla.