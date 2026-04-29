

# Manipulando arquivos com Python

Saber trabalhar com arquivos é essencial para qualquer desenvolvedor e é certo que em sua vida como desenvolvedor, em algum momento você terá que lidar com esta situação. Por isso, veremos neste artigo como manipular arquivos com Python.

É de grande importância para qualquer desenvolvedor saber manipular arquivos, seja para criar backups, consumir uma lista de alguma planilha ou qualquer motivo que seja. Por isso, a maioria das linguagens de programação possuem meios para essa manipulação.

No Python não é diferente e a manipulação de arquivos é mais simples do que você possa imaginar. Por isso, veremos neste artigo como realizar as principais operações com arquivos utilizando o Python.

### Criando e abrindo arquivos

Para criar arquivos (e, consequentemente, abrí-los), utilizamos o método `open()`. Este método irá abrir o arquivo que passarmos como parâmetro com um determinado modo de uso (que também será passado como parâmetro).

Há diversos modos de uso, como podemos ver na imagem abaixo:

![](https://dkrn4sk0rn31v.cloudfront.net/2020/02/18145652/Untitled.png)

Estes modos são passados como segundo parâmetro do método open. Ou seja, se quisermos abrir um arquivo em modo de escrita, utilizamos a seguinte sintaxe:

```python
arquivo = open("contatos.txt", "a")
```

Passamos o nome do arquivo e sua extensão, além do modo que queremos utilizar o arquivo. Este modo pode ser alterado conforme as opções da imagem anterior.

Sendo assim, ao executar o código acima, o arquivo contatos.txt será aberto em modo escrita (caso ele não exista, um novo arquivo será criado):

![](https://dkrn4sk0rn31v.cloudfront.net/2020/02/18145701/2.png)

### Escrevendo dados em arquivos

Agora que já sabemos como criar arquivos e seus diferentes modos de uso, podemos realizar as primeiras manipulações. Neste tópico, veremos como escrever dados e salvar em arquivo utilizando o Python.

Para isso, a linguagem fornece dois métodos. O primeiro é o método `write()` que recebe uma string como parâmetro e a insere no arquivo:

```python
arquivo = open("contatos.txt", "a")
arquivo.write("Olá, mundo!")
```

Com a execução do código acima, a string `Olá, mundo!` será inserida no arquivo texto.txt

O segundo método é o `writelines()` que recebe um objeto iterável (seja uma lista, uma [[Tupla]], um [dicionário](https://www.treinaweb.com.br/blog/manipulando-dicionarios-no-python/ "dicionário"), etc). Com este método, várias linhas poderão ser inseridas no arquivo, diferente do método `write()` que só recebe uma string por vez:

Copiar

```python
arquivo = open("texto.txt", "a")

frases = list()
frases.append("TreinaWeb \n")
frases.append("Python \n")
frases.append("Arquivos \n")
frases.append("Django \n")

arquivo.writelines(frases)
```

Como a lista é um objeto iterável, podemos passá-la como parâmetro do método `writelines()`. Utilizamos, também, o `\n` para saltar a linha ao escrevê-la no arquivo. Com isso, o resultado será o seguinte:![](https://dkrn4sk0rn31v.cloudfront.net/2020/02/18150133/4.png)

![Desenvolvedor Python Júnior](https://d2knvm16wkt3ia.cloudfront.net/assets/svg-icon/python.svg)

##### FormaçãoDesenvolvedor Python Júnior

[Conhecer a formação](https://www.treinaweb.com.br/formacao/desenvolvedor-python-junior)

### Lendo dados de arquivos

Além de escrever dados em arquivos, precisamos, também, saber ler os dados que os arquivos possuem. Para isso, existem dois métodos, o primeiro é o `readline()` que irá ler uma quantidade N de caracteres da primeira linha passadas como parâmetro:

Copiar

```python
arquivo = open("texto.txt", "r")

print(arquivo.readline(3))
```

A execução acima retornará os três primeiros caracteres da primeira linha do arquivo:

![](https://dkrn4sk0rn31v.cloudfront.net/2020/02/18150240/5.png)O segundo método disponível é o `readlines()`. Este método irá retornar todas as linhas do arquivo:

Copiar

```python
arquivo = open("texto.txt", "r")

print(arquivo.readlines())
```

![](https://dkrn4sk0rn31v.cloudfront.net/2020/02/18150255/6.png)

[![](https://dkrn4sk0rn31v.cloudfront.net/uploads/2023/06/sessao-mentoria.jpg)](https://www.treinaweb.com.br/checkout/pagamento/plano/anual?forma=cartao)

### Considerações finais

Saber trabalhar com arquivos é essencial para qualquer desenvolvedor. É certo que em sua vida como desenvolvedor, em algum momento você terá que lidar com esta situação.

Como vimos neste artigo, a manipulação de arquivos com Python é uma tarefa bem simples, porém de grande importância.