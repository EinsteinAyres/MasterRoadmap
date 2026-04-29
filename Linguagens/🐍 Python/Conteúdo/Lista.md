

Lista é uma coleção de valores indexada, em que cada valor é identificado por um índice. O primeiro item na lista está no índice 0, o segundo no índice 1 e assim por diante.

Para criar uma lista com elementos deve-se usar colchetes e adicionar os itens entre eles separados por vírgula, como mostra o **Código 1**.

```
programadores = ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']
print(type(programadores)) # type ‘list’
print(len(programadores)) # 5
print(programadores[4]) # Luana
```

**Código 1**. Lista no Python

Na linha 1 criamos uma variável do tipo lista chamada programadores contendo cinco nomes como os seus itens. Como já visto antes a função type() (Linha 2) traz o tipo de variável e len() "Tipo de dados em Python") (Linha 3) o tamanho do objeto. Observe que na linha 4 imprimimos um item da lista acessando o índice 4.

Outra característica das listas no Python é que elas são mutáveis, podendo ser alteradas depois de terem sido criadas. Em outras palavras, podemos adicionar, remover e até mesmo alterar os itens de uma lista.

No **Código 2** vemos um exemplo de como alterar uma lista.

```
programadores = ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']
print(programadores) # ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']

programadores[1] = 'Carolina'
print(programadores) # ['Victor', 'Carolina', 'Samuel', 'Caio', 'Luana']
```

**Código 2**. Alteração em lista

Primeiro, criamos uma lista contendo algumas strings e depois imprimimos o seu valor na linha 2. Após isso, acessamos um dos elementos dela e alteramos o valor dele para “Carolina” na linha 4.

Além de alterar elementos em listas, também é possível adicionar itens nelas, pois já vêm com uma coleção de métodos predefinidos que podem ser usados para manipular os objetos que ela contém. No caso de adicionar elementos, podemos usar o método append(), como veremos no **Código 3**.


```
programadores = ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']
print(programadores) # ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana

programadores.append('Renato')
print(programadores) # ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana', 'Renato']
```

**Código 3**. Adicionando item na lista

Na linha 1 criamos a lista e na linha 2 a imprimimos. Na linha 3 usamos o método append(), que adiciona elementos no final de uma lista. Quando imprimimos a lista na linha 4 vemos que ela exibirá o item adicionado na última posição.

Há outra forma de adicionarmos itens na lista, que é através do método insert(). Ele usa dois parâmetros: o primeiro para indicar a posição da lista em que o elemento será inserido e o segundo para informar o item a ser adicionado na lista. O **Código 4** mostra como isso ocorre na prática.

```
programadores = ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']
programadores.insert(1, 'Rafael')

print(programadores) # ['Victor', 'Rafael', 'Juliana', 'Samuel', 'Caio', 'Luana']
```

**Código 4**. Adicionando item na lista

No código acima adicionamos na posição 1 da lista a string ‘Rafael’. O resultado que será gerado pela linha 4 será o seguinte:

```
['Victor', 'Rafael', 'Juliana', 'Samuel', 'Caio', 'Luana']
```

Assim como podemos adicionar itens em nossa lista, também podemos retirá-los. E para isso temos dois métodos: remove() para a remoção pelo valor informado no parâmetro, e pop() para remoção pelo índice do elemento na lista. Vejamos como isso funciona na lista de programadores que estamos usando como exemplo:

```
programadores = ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']
print(programadores) # ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']

programadores.remove('Victor')
print(programadores) # ['Juliana', 'Samuel', 'Caio', 'Luana']
```

**Código 5**. Removendo item da lista

A remoção é feita na linha 4 do **Código 5**, onde removemos um elemento pelo seu **valor**. Nesse caso, retiramos da lista programadores o item que tem a string ‘Victor’. Isso fará com que a linha 5 imprima os seguintes valores:

```
['Juliana', 'Samuel', 'Caio', 'Luana']
```

Também é possível remover um item pelo seu índice, como podemos ver no **Código 6**.

```
programadores = ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']
print(programadores) # ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']

programadores.pop(0)
print(programadores) # ['Juliana', 'Samuel', 'Caio', 'Luana']
```

**Código 6**. Removendo item da lista

Usamos o método pop() para remover o primeiro item da lista programadores, gerando o seguinte resultado:

```
['Juliana', 'Samuel', 'Caio', 'Luana']
```

Note que nos códigos houve uma mudança na lista programadores.

No caso de tentarmos remover um item de uma posição inexistente, obtemos o erro IndexError: pop index out of range como veremos a seguir:

```
programadores = ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']

programadores.pop(5)
```

**Código 7**. Erro ao remover item da lista

Na linha 3 do **Código 7**, o método pop() foi usado para remover um item da lista pelo seu índice. Entretanto, a posição usada como parâmetro não existe, visto que a última posição da lista é 4, e foi usado o índice 5 para a remoção.

Também podemos obter um erro ao tentar remover um item com valor inexistente de uma lista, como é mostrado no **Código 8**.

```
programadores = ['Victor', 'Juliana', 'Samuel', 'Caio', 'Luana']

programadores.remove('Igor')
```

**Código 8**. Erro ao remover item da lista

Obtemos o erro ValueError: list.remove(x): x not in list, pois não existe na lista programadores um item com a string “Igor” que queremos excluir.

Outra característica das listas é que elas podem possuir diferentes tipos de elementos na sua composição. Isso quer dizer que podemos ter strings, booleanos, inteiros e outros tipos diferentes de objetos na mesma lista. O **Código 9** mostra como isso acontece na prática.

```
aluno = ['Murilo', 19, 1.79] # Nome, idade e altura

print(type(aluno)) # type 'list'
print(aluno) # ['Murilo', 19, 1.79]
```

**Código 9**. Diferentes tipos de objetos na lista

Essa característica heterogênea das listas é mostrada no exemplo acima, no qual criamos uma lista com variáveis dos tipos string, inteiro e float, que representam o nome, idade e altura, respectivamente.
