

# Coleções no Python: [[Lista]], [[Tupla]] e [[Dicionário]]

---

As coleções permitem armazenar múltiplos itens dentro de uma única unidade, que funciona como um container. Neste artigo veremos três dentre as coleções mais utilizadas em Python, que são as listas, tuplas e dicionários.

### Dicionário

Os dicionários representam coleções de dados que contém na sua estrutura um conjunto de pares chave/valor, nos quais cada chave individual tem um valor associado. Esse objeto representa a ideia de um mapa, que entendemos como uma coleção associativa desordenada. A associação nos dicionários é feita por meio de uma chave que faz referência a um valor. No **Código 15**, vemos a estrutura de um dicionário.

```
dados_cliente = {
    'Nome': 'Renan',
    'Endereco': 'Rua Cruzeiro do Sul',
    'Telefone': '982503645'
}

print(dados_cliente['Nome']) # Renan
```

**Código 15**. Dicionário no Python

A estrutura de um dicionário é delimitada por chaves, entre as quais ficam o conteúdo desse objeto. Veja que é criada a variável dados_cliente, à qual é atribuída uma coleção de dados que, nesse caso, trata-se de um dicionário. Na linha 7 do **Código 15**, imprimimos o conteúdo que é associado ao índice “Nome”, trazendo o resultado Renan.

Nas listas e tuplas acessamos os dados por meio dos índices. Já nos dicionários, o acesso aos dados é feito por meio da chave associada a eles.

Para adicionar elementos num dicionário basta associar uma nova chave ao objeto e dar um valor a ser associado a ela. No **Código 16** vamos colocar a informação Idade em dados_cliente.

```
dados_cliente = {
    'Nome': 'Renan',
    'Endereco': 'Rua Cruzeiro do Sul',
    'Telefone': '982503645'
}

print(dados_cliente) # {'Nome': 'Renan', 'Endereco': 'Rua Cruzeiro do Sul',
   'Telefone': '982503645'}

dados_cliente['Idade'] = 40

print(dados_cliente) # {'Nome': 'Renan', 'Endereco': 'Rua Cruzeiro do Sul',
  'Telefone': '982503645', 'Idade': 40}
```

**Código 16**. Adicionando item no Dicionário

Para remover um item do dicionário, podemos usar o método pop(), como vemos no **Código 17**.

```
dados_cliente = {
    'Nome': 'Renan',
    'Endereco': 'Rua Cruzeiro do Sul',
    'Telefone': '982503645'
}

print(dados_cliente) # {'Nome': 'Renan', 'Endereco': 'Rua Cruzeiro do Sul',
  'Telefone': '982503645'}

dados_cliente.pop('Telefone', None)

print(dados_cliente) # {'Nome': 'Renan', 'Endereco': 'Rua Cruzeiro do Sul'}
```

**Código 17**. Removendo item no Dicionário

Na linha 9 do **Código 17** temos o uso do método pop(), usado para remover o item ‘Telefone’ do dicionário dados_clientes. Temos na chamada do método o parâmetro None, que é passado depois da chave a ser removida. O None serve para que a mensagem de erro KeyError não apareça devido a remoção de uma chave inexistente.

Também poderíamos usar a palavra-chave del, que remove uma chave e o valor associado a ela no dicionário. Isso se faz por meio da passagem no parâmetro, como vemos no exemplo do **Código 18**.

```
dados_cliente = {
    'Nome': 'Renan',
    'Endereco': 'Rua Cruzeiro do Sul',
    'Telefone': '982503645'
}

print(dados_cliente) # {'Nome': 'Renan', 'Endereco': 'Rua Cruzeiro do Sul',
  'Telefone': '982503645'}

del dados_cliente['Telefone']

print(dados_cliente) # {'Nome': 'Renan', 'Endereco': 'Rua Cruzeiro do Sul'}
```

**Código 18**. Removendo item no Dicionário

### Funções para coleções

O Python conta com funções úteis quando se trabalha com coleções. Vejamos algumas delas:

#### min() e max()

Veremos a seguir, as funções min() e max(). No **Código 19** temos um exemplo com elas.

```
numeros = [15, 5, 0, 20, 10]
nomes = ['Caio', 'Alex', 'Renata', 'Patrícia', 'Bruno']

print(min(numeros)) # 0
print(max(numeros)) # 20
print(min(nomes)) # Alex
print(max(nomes)) # Renata
```

**Código 19**. Funções min() e max()

Nesse código temos duas listas com os nomes numeros e nomes. A primeira lista trabalha com números, então a função min() retorna o menor valor dela, enquanto que a função max() retorna o maior valor. Já a segunda lista contém strings, o que faz com que as funções trabalhem com comparações alfabéticas. Portanto, nesse exemplo o menor valor é Alexe o maior Renata.

#### sum()

Para trabalhar com coleções na linguagem Python, temos também a função sum(), que é usada para retornar a soma de todos os elementos da coleção. No **Código 20**, temos um exemplo dela na prática:

```
numeros = [1, 3, 6]

print(sum(numeros)) # 10
```

**Código 20**. Função sum()

Como vemos no código acima, sum() retornou a soma dos itens da lista numeros. Essa função não trabalha com strings, pois não é um tipo suportado por ela. Caso fossem usadas strings, a mensagem de erro TypeError: unsupported operand type(s) for +: 'int' and 'str' seria exibida.

#### len()

A função len() é bastante usada em Python para retornar o tamanho de um objeto. Quando usada com coleções, retorna o total de itens que a coleção possui. Vemos isso no **Código 21**.

```
paises = ['Argentina', 'Brasil', 'Colômbia', 'Uruguai']

print(len(paises)) # 4
```

**Código 21**. Função len()

Essa função é de grande utilidade, pois pode ser usada em diversas situações, como nas estruturas condicionais e em laços de repetição por exemplo.

#### type()

Com a função type() podemos obter o tipo do objeto passado no parâmetro. No **Código 22** usamos essa função em algumas coleções.

```
professores = ['Carla', 'Daniel', 'Ingrid', 'Roberto']
estacoes = ('Primavera', 'Verão', 'Outono', 'Inverno')
cliente = {
    'Nome': 'Fábio Garcia',
    'email' : 'fabio_garcia_9@outlook.com'
}

print(type(professores)) # list
print(type(estacoes)) # tuple
print(type(cliente)) # dict
```

**Código 22**. Função type()

A execução do código acima nos traz uma lista, uma tupla e um dicionário, que são as coleções trabalhadas nesse artigo.

