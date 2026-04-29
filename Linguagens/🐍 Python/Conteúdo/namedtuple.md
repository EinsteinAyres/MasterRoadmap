## Função de fábrica para tuplas com campos nomeados `namedtuple()`

Tuplas nomeadas determinam o significado de cada posição numa tupla e permitem um código mais legível e autodocumentado. Podem ser usadas sempre que tuplas regulares forem utilizadas, e adicionam a possibilidade de acessar campos pelo nome ao invés da posição do índice.

collections.namedtuple(_typename_, _field_names_, _*_, _rename=False_, _defaults=None_, _module=None_)[](https://docs.python.org/pt-br/3.12/library/collections.html#collections.namedtuple "Link para esta definição")

Retorna uma nova subclasse de tupla chamada _typename_. A nova subclasse é usada para criar objetos tupla ou similares que possuem campos acessíveis por pesquisa de atributos, além de serem indexáveis e iteráveis. As instâncias da subclasse também possuem uma docstring útil (com _typename_ e _field_names_) e um método útil [`__repr__()`](https://docs.python.org/pt-br/3.12/reference/datamodel.html#object.__repr__ "object.__repr__") que lista o conteúdo da tupla em um formato `nome=valor`.

_field_names_ são uma sequência de strings como `['x', 'y']`. Alternativamente, _field_names_ pode ser uma única string com cada nome de campo separado por espaços em branco e/ou vírgulas como, por exemplo, `'x y'` ou `'x, y'`.

Qualquer identificador Python válido pode ser usado para um nome de campo, exceto para nomes que começam com um sublinhado. Identificadores válidos consistem em letras, dígitos e sublinhados, mas não começam com um dígito ou sublinhado e não podem ser uma [`keyword`](https://docs.python.org/pt-br/3.12/library/keyword.html#module-keyword "keyword: Test whether a string is a keyword in Python.") como _class_, _for_, _return_, _global_, _pass_ ou _raise_.

Se _rename_ for verdadeiro, nomes de campos inválidos serão automaticamente substituídos por nomes posicionais. Por exemplo, `['abc', 'def', 'ghi', 'abc']` é convertido para `['abc', '_1', 'ghi', '_3']`, eliminando a palavra reservada `def` e o nome de campo duplicado `abc`.

_defaults_ pode ser `None` ou um [iterável](https://docs.python.org/pt-br/3.12/glossary.html#term-iterable) de valores padrão. Como os campos com valor padrão devem vir depois de qualquer campo sem padrão, os _padrões_ são aplicados aos parâmetros mais à direita. Por exemplo, se os nomes dos campos forem `['x', 'y', 'z']` e os padrões forem `(1, 2)`, então `x` será um argumento obrigatório, `y` será o padrão `1`, e `z` será o padrão `2`.

Se _module_ for definido, o atributo [`__module__`](https://docs.python.org/pt-br/3.12/reference/datamodel.html#type.__module__ "type.__module__") da tupla nomeada será definido com esse valor.

As instâncias de tuplas nomeadas não possuem dicionários por instância, portanto são leves e não requerem mais memória do que as tuplas normais.

Para prover suporte para a serialização com pickle, a classe de tupla nomeada deve ser atribuída a uma variável que corresponda a _typename_.

Alterado na versão 3.1: Adicionado suporte a _rename_.

Alterado na versão 3.6: Os parâmetros _verbose_ e _rename_ tornaram-se [argumentos somente-nomeados](https://docs.python.org/pt-br/3.12/glossary.html#keyword-only-parameter).

Alterado na versão 3.6: Adicionado o parâmetro _module_.

Alterado na versão 3.7: Removido o parâmetro _verbose_ e o atributo `_source`.

Alterado na versão 3.7: Adicionado o parâmetro _defaults_ e o atributo [`_field_defaults`](https://docs.python.org/pt-br/3.12/library/collections.html#collections.somenamedtuple._field_defaults "collections.somenamedtuple._field_defaults").

```
# Exemplo básico
Point = namedtuple('Point', ['x', 'y'])
p = Point(11, y=22)     # instancia com argumentos nomeados ou posicionais
p[0] + p[1]             # indexável como a tupla plana (11, 22)
33
x, y = p                # desempacota como uma tupla normal
x, y
(11, 22)
p.x + p.y               # campos também acessíveis pelo nome
33
p                       # __repr__ legível com o estilo nome=valor
Point(x=11, y=22)
```

Tuplas nomeadas são especialmente úteis para atribuir nomes de campos a tuplas de resultados retornadas pelos módulos [`csv`](https://docs.python.org/pt-br/3.12/library/csv.html#module-csv "csv: Write and read tabular data to and from delimited files.") ou [`sqlite3`](https://docs.python.org/pt-br/3.12/library/sqlite3.html#module-sqlite3 "sqlite3: A DB-API 2.0 implementation using SQLite 3.x."):

```
EmployeeRecord = namedtuple('EmployeeRecord', 'name, age, title, department, paygrade')


import csv
for emp in map(EmployeeRecord._make, csv.reader(open("employees.csv", "rb"))):
    print(emp.name, emp.title)

import sqlite3
conn = sqlite3.connect('/companydata')
cursor = conn.cursor()
cursor.execute('SELECT name, age, title, department, paygrade FROM employees')
for emp in map(EmployeeRecord._make, cursor.fetchall()):
    print(emp.name, emp.title)
```

Além dos métodos herdados das tuplas, as tuplas nomeadas oferecem suporte a três métodos adicionais e dois atributos. Para evitar conflitos com nomes de campos, os nomes de métodos e atributos começam com um sublinhado.

_classmethod_ somenamedtuple._make(_iterable_)[](https://docs.python.org/pt-br/3.12/library/collections.html#collections.somenamedtuple._make "Link para esta definição")

Método de classe que cria uma nova instância a partir de uma sequência existente ou iterável.

```
t = [11, 22]
Point._make(t)
Point(x=11, y=22)
```

somenamedtuple._asdict()[](https://docs.python.org/pt-br/3.12/library/collections.html#collections.somenamedtuple._asdict "Link para esta definição")

Retorna um novo [`dict`](https://docs.python.org/pt-br/3.12/library/stdtypes.html#dict "dict") que mapeia nomes de campo para seus respectivos valores:

```
p = Point(x=11, y=22)
p._asdict()
{'x': 11, 'y': 22}
```

Alterado na versão 3.1: Retorna um [`OrderedDict`](https://docs.python.org/pt-br/3.12/library/collections.html#collections.OrderedDict "collections.OrderedDict") em vez de um [`dict`](https://docs.python.org/pt-br/3.12/library/stdtypes.html#dict "dict") normal.

Alterado na versão 3.8: Retorna um [`dict`](https://docs.python.org/pt-br/3.12/library/stdtypes.html#dict "dict") regular em vez de um [`OrderedDict`](https://docs.python.org/pt-br/3.12/library/collections.html#collections.OrderedDict "collections.OrderedDict"). A partir do Python 3.7, é garantido que os dicionários regulares sejam ordenados. Se os recursos extras de [`OrderedDict`](https://docs.python.org/pt-br/3.12/library/collections.html#collections.OrderedDict "collections.OrderedDict") forem necessários, a correção sugerida é converter o resultado para o tipo desejado: `OrderedDict(nt._asdict())`.

somenamedtuple._replace(_**kwargs_)[](https://docs.python.org/pt-br/3.12/library/collections.html#collections.somenamedtuple._replace "Link para esta definição")

Retorna uma nova instância da tupla nomeada substituindo os campos especificados por novos valores:

```
p = Point(x=11, y=22)
p._replace(x=33)
Point(x=33, y=22)

for partnum, record in inventory.items():
    inventory[partnum] = record._replace(price=newprices[partnum], timestamp=time.now())

```


[somenamedtuple._fields](https://docs.python.org/pt-br/3.12/library/collections.html#collections.somenamedtuple._fields "Link para esta definição")

Tupla de strings listando os nomes dos campos. Útil para introspecção e para criar novos tipos de tuplas nomeadas a partir de tuplas nomeadas existentes.

```
p._fields            # exibe os nomes de campos
('x', 'y')

Color = namedtuple('Color', 'red green blue')
Pixel = namedtuple('Pixel', Point._fields + Color._fields)
Pixel(11, 22, 128, 255, 0)
Pixel(x=11, y=22, red=128, green=255, blue=0)
```


somenamedtuple._field_defaults[](https://docs.python.org/pt-br/3.12/library/collections.html#collections.somenamedtuple._field_defaults "Link para esta definição")

Dicionário mapeando nomes de campos para valores padrão.

```
Account = namedtuple('Account', ['type', 'balance'], defaults=[0])
Account._field_defaults
{'balance': 0}
Account('premium')
Account(type='premium', balance=0)
```

Para recuperar um campo cujo nome está armazenado em uma string, use a função [`getattr()`](https://docs.python.org/pt-br/3.12/library/functions.html#getattr "getattr"):

```
getattr(p, 'x')
11
```

Para converter um dicionário em uma tupla nomeada, use o operador estrela dupla (conforme descrito em [Desempacotando listas de argumentos](https://docs.python.org/pt-br/3.12/tutorial/controlflow.html#tut-unpacking-arguments)):

```
d = {'x': 11, 'y': 22}
Point(**d)
Point(x=11, y=22)
```

Como uma tupla nomeada é uma classe regular do Python, é fácil adicionar ou alterar funcionalidades com uma subclasse. Veja como adicionar um campo calculado e um formato de impressão de largura fixa:

```
class Point(namedtuple('Point', ['x', 'y'])):
    __slots__ = ()
    @property
    def hypot(self):
        return (self.x ** 2 + self.y ** 2) ** 0.5
    def __str__(self):
        return 'Point: x=%6.3f  y=%6.3f  hypot=%6.3f' % (self.x, self.y, self.hypot)

for p in Point(3, 4), Point(14, 5/7):
    print(p)
Point: x= 3.000  y= 4.000  hypot= 5.000
Point: x=14.000  y= 0.714  hypot=14.018
```

A subclasse mostrada acima define `__slots__` como uma tupla vazia. Isso ajuda a manter baixos os requisitos de memória, evitando a criação de dicionários de instância.

A criação de subclasse não é útil para adicionar novos campos armazenados. Em vez disso, simplesmente crie um novo tipo de tupla nomeado a partir do atributo [`_fields`](https://docs.python.org/pt-br/3.12/library/collections.html#collections.somenamedtuple._fields "collections.somenamedtuple._fields"):

```
Point3D = namedtuple('Point3D', Point._fields + ('z',))
```

Docstrings podem ser personalizados fazendo atribuições diretas aos campos `__doc__`:


```
Book = namedtuple('Book', ['id', 'title', 'authors'])
Book.__doc__ += ': Hardcover book in active collection'
Book.id.__doc__ = '13-digit ISBN'
Book.title.__doc__ = 'Title of first printing'
Book.authors.__doc__ = 'List of authors sorted by last name'
```

Alterado na versão 3.5: Os docstrings de propriedade tornaram-se graváveis.

Ver também:

- Veja [`typing.NamedTuple`](https://docs.python.org/pt-br/3.12/library/typing.html#typing.NamedTuple "typing.NamedTuple") para uma maneira de adicionar dicas de tipo para tuplas nomeadas. Ele também fornece uma notação elegante usando a palavra reservada [`class`](https://docs.python.org/pt-br/3.12/reference/compound_stmts.html#class):
    
```
    class Component(NamedTuple):
        part_number: int
        weight: float
        description: Optional[str] = None
```
    
- Veja [`types.SimpleNamespace()`](https://docs.python.org/pt-br/3.12/library/types.html#types.SimpleNamespace "types.SimpleNamespace") para um espaço de nomes mutável baseado em um dicionário subjacente em vez de uma tupla.
    
- O módulo [`dataclasses`](https://docs.python.org/pt-br/3.12/library/dataclasses.html#module-dataclasses "dataclasses: Generate special methods on user-defined classes.") fornece um decorador e funções para adicionar automaticamente métodos especiais gerados a classes definidas pelo usuário.
    
