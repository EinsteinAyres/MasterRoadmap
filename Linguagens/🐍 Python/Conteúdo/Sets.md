
# O que é, quando e como usar no seu projeto


O set em Python consiste em uma estrutura de dados muito útil, porém menos compreendidas. 

Isso porque, diferentemente de listas e dicionários, os sets oferecem operações matemáticas de conjunto e garantem elementos únicos.

Se você trabalha ou pretende se especializar no desenvolvimento em Python, essa pode ser a sua grande aliada. Por isso, preparamos este artigo para te ensinar:

- O que são sets em Python e como criá-los;
- Propriedades exclusivas que os diferenciam de outras estruturas;
- Vantagens de desempenho em operações específicas.

Boa leitura!



**Você vai aprender a linguagem de Programação que mais cresce no Mundo para fazer automações incríveis,** desenvolver sites, fazer análises de dados, trabalhar com Ciência de Dados, Inteligência Artificial, mesmo que você nunca tenha tido nenhum contato com Programação na vida.

## O que é um set em Python?
## Quais são as propriedades dos sets em Python?

Os sets em Python possuem quatro propriedades fundamentais:

1. Elementos únicos: Não permitem duplicatas;
2. Não ordenados: A ordem de inserção não é preservada;
3. Mutáveis: Podem ser alterados após criação (com exceção do frozenset);
4. Otimizados para operações de pertinência: Uso de hash tables para buscas O(1).

Vamos a mais um exemplo, agora para demonstrar as propriedades:

```
# Propriedade 1: Elementos únicos

nums = {1, 2, 2, 3, 3, 3}

print(nums)  # Saída: {1, 2, 3}

# Propriedade 4: Busca eficiente

print(2 in nums)  # Retorna True instantaneamente, mesmo em sets grandes
```

## Quais são as vantagens de usar set no projeto em Python?

Os sets em Python oferecem benefícios únicos que os tornam ideais para situações específicas. As principais vantagens incluem:

- Elementos únicos automaticamente: sets não permitem valores duplicados, o que é útil para filtrar dados repetidos sem esforço.
- Operações rápidas de busca: verificar se um elemento existe em um set (x in conjunto) é extremamente eficiente, mesmo com grandes volumes de dados;
- Operações matemáticas prontas: suporta união, interseção e diferença entre conjuntos de forma nativa, sem precisar de loops manuais;
- Otimização de memória: como armazenam apenas valores únicos, sets podem ser mais econômicos que listas em certos casos.

## Quando usar set em Python no seu projeto?

Sets são especialmente úteis quando você precisa:

- Remover duplicatas de uma lista ou coleção de dados;
- Verificar rapidamente se um valor já existe em uma coleção (ex.: lista de e-mails cadastrados);
- Comparar grupos de dados (ex.: produtos vendidos em diferentes períodos);
- Realizar operações matemáticas entre conjuntos (como unir ou encontrar elementos em comum).

Por exemplo: se você está construindo um sistema de recomendação e quer evitar sugestões repetidas, um set pode ajudar a gerenciar os itens já mostrados ao usuário.

## Como criar um set em Python?

Existem duas formas principais de criar um set:

### Usando chaves {}

Funciona para sets não vazios com elementos já definidos. Exemplo:

```
cores = {"vermelho", "verde", "azul"}  
```

### Usando a função set()

Necessária para criar um set vazio ou converter outras estruturas (listas, tuplas) em sets. Um exemplo:

```
numeros = set([1, 2, 3, 3])  # Remove duplicatas automaticamente  
```

Mas vale lembrar: {} sozinho cria um dicionário vazio, não um set. Para um set vazio, use set().

## Como modificar sets em Python?

Os sets em Python são mutáveis, ou seja, você pode adicionar, remover e alterar seus elementos após a criação. 

No entanto, como os sets não são ordenados e não possuem índices, as operações de modificação funcionam de forma diferente em comparação com listas ou dicionários.

Aqui estão as principais maneiras de modificar um set.

### Adicionar

Para adicionar elementos a um set, você pode usar os dois métodos abaixo.

#### **1. add()**

Sua principal função é inserir um único elemento no set. Se o elemento já existir, o set não é alterado (não há duplicatas) e ele só aceita um item por vez (não pode adicionar uma lista diretamente).

Um exemplo para que fique mais claro:

```
frutas = {"maçã", "banana"}  

frutas.add("laranja")  # Adiciona "laranja"  

frutas.add("maçã")     # Não faz nada, pois "maçã" já existe  

print(frutas)          # Saída: {"maçã", "banana", "laranja"}
```

#### **2. update()**

Adiciona múltiplos elementos de uma vez e aceita iteráveis (listas, tuplas, outros sets etc.), além de ser capaz de remover automaticamente duplicatas.

Confira no exemplo abaixo:

```
frutas = {"maçã", "banana"}  

frutas.update(["laranja", "uva", "banana"])  # Adiciona "laranja" e "uva" (ignora "banana")  

print(frutas)  # Saída: {"maçã", "banana", "laranja", "uva"}  

Atenção: se você tentar usar add() com uma lista, ocorrerá um erro. Use update() para múltiplos itens.
```

### Remover

Para remover elementos, existem quatro métodos principais, cada um com um comportamento diferente:

#### **1. remove()**

Remove um elemento específico e, se o elemento não existir, gera um erro (KeyError). É um método ideal para usar quando você tem certeza de que o elemento está no set.

Por exemplo:

```
frutas = {"maçã", "banana", "laranja"}  

frutas.remove("banana")  # Remove "banana"  

print(frutas)            # Saída: {"maçã", "laranja"}  

# frutas.remove("uva")   # Causaria KeyError, pois "uva" não existe
```

#### **2. discard()**

Remove um elemento se ele existir e não gera erro se o elemento não estiver no set. Além disso, é mais seguro de usar do que remove() quando você não tem certeza da existência do item.

Aqui vai um exemplo:

```
frutas = {"maçã", "banana", "laranja"}  

frutas.discard("banana")  # Remove "banana"  

frutas.discard("uva")     # Não faz nada (sem erro)  

print(frutas)             # Saída: {"maçã", "laranja"}
```

#### **3. pop()**

Remove e retorna um elemento aleatório do set. Só que, como sets não são ordenados, você não controla qual item será removido. Além disso, se o set estiver vazio, gera KeyError.

Confira no exemplo abaixo:

```
frutas = {"maçã", "banana", "laranja"}  

item_removido = frutas.pop()  

print(f"Item removido: {item_removido}")  # Pode ser qualquer um ("maçã", "banana" ou "laranja")  

print(frutas)  # Set sem o item removido
```

#### **4. clear()**

Remove todos os elementos do set, deixando-o vazio. Muito útil para reutilizar a variável sem criar um novo set. Cheque no exemplo a seguir:

```
frutas = {"maçã", "banana", "laranja"}  

frutas.clear()  

print(frutas)  # Saída: set()
```

Aproveite, também, e confira a tabela abaixo com o resumo das operações de modificação que falamos anteriormente:

|**Operação**|**Método**|**Comportamento**|
|---|---|---|
|Adicionar 1 item|add()|Ignora se já existir|
|Adicionar vários itens|update()|Aceita listas, tuplas, etc.|
|Remover item específico|remove()|Erro se não existir|
|Remover item (seguro)|discard()|Não gera erro|
|Remover item aleatório|pop()|Retorna o elemento removido|
|Limpar todo o set|clear()|Deixa o set vazio|

## Como iterar os sets em Python?

Iterar sobre um set em Python significa percorrer cada um de seus elementos para leitura ou processamento. 

Apesar de os sets não serem ordenados (ou seja, não garantirem uma sequência fixa), a iteração é simples e eficiente.

Entenda melhor a partir das principais características da iteração em sets:

- Ordem imprevisível: como os sets são baseados em hash tables, a ordem dos elementos durante a iteração não segue a inserção e pode variar entre execuções;
- Eficiência: a iteração é tão rápida quanto em listas, mas sem a sobrecarga de índices;
- Uso em loops: pode-se usar loops for diretamente, assim como em outras estruturas iteráveis.

E quando iterar em sets? Basicamente, quando você precisa processar todos os elementos de uma coleção sem duplicatas ou para aplicar operações ou filtros a cada item individualmente. 

Ou, até mesmo, ao trabalhar com dados onde a ordem não é relevante, como conjuntos matemáticos ou IDs únicos.

## Como fazer operações com sets em Python?

Os sets em Python suportam operações matemáticas de conjuntos de forma nativa, o que os torna ideais para comparações e combinações entre coleções de dados. 

Essas operações são otimizadas e evitam a necessidade de loops manuais.

### União

A união combina os elementos de dois ou mais sets, resultando em um novo conjunto com todos os itens presentes em qualquer um dos sets originais, sem repetições.

- Símbolo: |
- Método equivalente: union()

Tipicamente, você pode usar para combinar listas de usuários de diferentes fontes ou também para agregar resultados de múltiplas consultas sem repetições.

### Interseção

A interseção retorna apenas os elementos que estão presentes em todos os sets envolvidos na operação.

- Símbolo: &
- Método equivalente: intersection()

Aplicações típicas incluem a identificação de itens comuns entre diferentes grupos (ex.: produtos comprados por dois clientes) ou também para filtrar dados que atendem a múltiplas condições simultaneamente.

### Diferença

A diferença entre sets em Python retorna os elementos que estão no primeiro set mas não estão nos demais.

- Símbolo: –
- Método equivalente: difference()

Que tal fazer uso disso para encontrar itens exclusivos de uma coleção em relação a outra ou para detectar mudanças entre dois estados de dados (ex.: novos registros em um banco de dados)?