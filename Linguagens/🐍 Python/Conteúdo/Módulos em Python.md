
Ao sair e entrar de novo no interpretador Python, as definições anteriores (funções e variáveis) são perdidas. Portanto, se quiser escrever um programa maior, será mais eficiente usar um editor de texto para preparar as entradas para o interpretador, e executá-lo usando o arquivo como entrada. Isso é conhecido como criar um _script_. Se o programa se torna ainda maior, é uma boa prática dividi-lo em arquivos menores, para facilitar a manutenção. Também é preferível usar um arquivo separado para uma função que você escreveria em vários programas diferentes, para não copiar a definição de função em cada um deles.

Para permitir isso, Python tem uma maneira de colocar as definições em um arquivo e então usá-las em um script ou em uma execução interativa do interpretador. Tal arquivo é chamado de _módulo_; definições de um módulo podem ser _importadas_ para outros módulos, ou para o módulo _principal_ (a coleção de variáveis a que você tem acesso num script executado como um programa e no modo calculadora).

Um módulo é um arquivo contendo definições e instruções Python. O nome do arquivo é o nome do módulo acrescido do sufixo `.py`. Dentro de um módulo, o nome do módulo (como uma string) está disponível como o valor da variável global `__name__`. Por exemplo, use seu editor de texto favorito para criar um arquivo chamado `fibo.py` no diretório atual com o seguinte conteúdo:

```
# Módulo de números de Fibonacci

def fib(n):    # escreve a série de Fibonacci até n
    a, b = 0, 1
    while a < n:
        print(a, end=' ')
        a, b = b, a+b
    print()

def fib2(n):   # retorna a série de Fibonacci até n
    result = []
    a, b = 0, 1
    while a < n:
        result.append(a)
        a, b = b, a+b
    return result
```

Agora, entre no interpretador Python e importe esse módulo com o seguinte comando:

```
import fibo
```

Isso não adiciona os nomes das funções definidas em `fibo` diretamente ao [espaço de nomes](https://docs.python.org/pt-br/3.12/glossary.html#term-namespace) atual (veja [Escopos e espaços de nomes do Python](https://docs.python.org/pt-br/3.12/tutorial/classes.html#tut-scopes) para mais detalhes); isso adiciona somente o nome do módulo `fibo`. Usando o nome do módulo você pode acessar as funções:

```
fibo.fib(1000)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
fibo.fib2(100)
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
fibo.__name__
'fibo'
```

Se você pretende usar uma função muitas vezes, você pode atribui-lá a um nome local:

```
fib = fibo.fib
fib(500)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377
```

## 1. Mais sobre módulos[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#more-on-modules "Link para este cabeçalho")

Um módulo pode conter tanto instruções executáveis quanto definições de funções e classes. Essas instruções servem para inicializar o módulo. Eles são executados somente na _primeira_ vez que o módulo é encontrado em uma instrução de importação. (Também rodam se o arquivo é executado como um script.)

Cada módulo tem seu próprio espaço de nomes privado, que é usado como espaço de nomes global para todas as funções definidas no módulo. Assim, o autor de um módulo pode usar variáveis globais no seu módulo sem se preocupar com conflitos acidentais com as variáveis globais do usuário. Por outro lado, se você precisar usar uma variável global de um módulo, poderá fazê-lo com a mesma notação usada para se referir às suas funções, `nomemodulo.nomeitem`.

Módulos podem importar outros módulos. É costume, porém não obrigatório, colocar todas as instruções `import` no início do módulo (ou script , se preferir). As definições do módulo importado, se colocados no nível de um módulo (fora de quaisquer funções ou classes), elas são adicionadas a espaço de nomes global da módulo.

Existe uma variante da instrução `import` que importa definições de um módulo diretamente para o espaço de nomes do módulo importador. Por exemplo:

```
from fibo import fib, fib2
fib(500)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377
```

Isso não coloca o nome do módulo de onde foram feitas as importações no espaço de nomes local (assim, no exemplo, `fibo` não está definido).

Existe ainda uma variante que importa todos os nomes definidos em um módulo:

```
from fibo import *
fib(500)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377
```

Isso importa todos as declarações de nomes, exceto aqueles que iniciam com um sublinhado (`_`). Na maioria dos casos, programadores Python não usam esta facilidade porque ela introduz um conjunto desconhecido de nomes no ambiente, podendo esconder outros nomes previamente definidos.

Note que, em geral, a prática do `import *` de um módulo ou pacote é desaprovada, uma vez que muitas vezes dificulta a leitura do código. Contudo, é aceitável para diminuir a digitação em sessões interativas.

Se o nome do módulo é seguido pela palavra-chave `as`, o nome a seguir é vinculado diretamente ao módulo importado.

```
import fibo as fib
fib.fib(500)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377
```

Isto efetivamente importa o módulo, da mesma maneira que `import fibo` fará, com a única diferença de estar disponível com o nome `fib`.

Também pode ser utilizado com a palavra-chave `from`, com efeitos similares:

```
from fibo import fib as fibonacci
fibonacci(500)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377
```

Nota:
Por razões de eficiência, cada módulo é importado apenas uma vez por sessão do interpretador. Portanto, se você alterar seus módulos, deverá reiniciar o interpretador ou, se for apenas um módulo que você deseja testar interativamente, use importlib.reload(), por exemplo, import importlib; importlib.reload(nome_do_módulo).

### 1.1. Executando módulos como scripts[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#executing-modules-as-scripts "Link para este cabeçalho")

Quando você rodar um módulo Python com

```
python fibo.py <argumentos>
```

o código no módulo será executado, da mesma forma que quando é importado, mas com a variável `__name__` com valor `"__main__"`. Isto significa que adicionando este código ao final do seu módulo:

```
if __name__ == "__main__":
    import sys
    fib(int(sys.argv[1]))
```

você pode tornar o arquivo utilizável tanto como script quanto como um módulo importável, porque o código que analisa a linha de comando só roda se o módulo é executado como arquivo “principal”:

```
python fibo.py 50
0 1 1 2 3 5 8 13 21 34
```

Se o módulo é importado, o código não é executado:

```
import fibo
```

Isso é frequentemente usado para fornecer uma interface de usuário conveniente para um módulo, ou para realizar testes (rodando o módulo como um script executa um conjunto de testes).

### 1.2. O caminho de busca dos módulos[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#the-module-search-path "Link para este cabeçalho")

Quando um módulo chamado `spam` é importado, o interpretador procura um módulo embutido com este nome. Estes nomes de módulo são listados em [`sys.builtin_module_names`](https://docs.python.org/pt-br/3.12/library/sys.html#sys.builtin_module_names "sys.builtin_module_names"). Se não encontra, procura um arquivo chamado `spam.py` em uma lista de diretórios incluídos na variável [`sys.path`](https://docs.python.org/pt-br/3.12/library/sys.html#sys.path "sys.path"). A [`sys.path`](https://docs.python.org/pt-br/3.12/library/sys.html#sys.path "sys.path") é inicializada com estes locais:

- O diretório que contém o script importador (ou o diretório atual quando nenhum arquivo é especificado).
    
- A variável de ambiente [`PYTHONPATH`](https://docs.python.org/pt-br/3.12/using/cmdline.html#envvar-PYTHONPATH) (uma lista de nomes de diretórios, com a mesma sintaxe da variável de ambiente `PATH`).
    
- O padrão dependente da instalação (por convenção, incluindo um diretório `site-packages`, tratado pelo módulo [`site`](https://docs.python.org/pt-br/3.12/library/site.html#module-site "site: Module responsible for site-specific configuration.")).
    

Mais detalhes em [A inicialização do caminho de pesquisa de módulos sys.path](https://docs.python.org/pt-br/3.12/library/sys_path_init.html#sys-path-init).

Nota
Nos sistemas de arquivos que suportam links simbólicos, o diretório contendo o script de entrada é resultante do diretório apontado pelo link simbólico. Em outras palavras o diretório que contém o link simbólico **não** é adicionado ao caminho de busca de módulos.

Após a inicialização, programas Python podem modificar [`sys.path`](https://docs.python.org/pt-br/3.12/library/sys.html#sys.path "sys.path"). O diretório que contém o script sendo executado é colocado no início da lista de caminhos, à frente do caminho da biblioteca padrão. Isto significa que módulos nesse diretório serão carregados, no lugar de módulos com o mesmo nome na biblioteca padrão. Isso costuma ser um erro, a menos que seja intencional. Veja a seção [Módulos padrões](https://docs.python.org/pt-br/3.12/tutorial/modules.html#tut-standardmodules) para mais informações.

### 1.3. Arquivos Python “compilados”[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#compiled-python-files "Link para este cabeçalho")

Para acelerar o carregamento de módulos, o Python guarda versões compiladas de cada módulo no diretório `__pycache__` com o nome `modulo._versão_.pyc`, onde a versão corresponde ao formato do arquivo compilado; geralmente contêm o número da versão Python utilizada. Por exemplo, no CPython release 3.3 a versão compilada de spam.py será guardada como `__pycache__/spam.cpython-33.pyc`. Esta convenção de nomes permite a coexistência de módulos compilados de diferentes releases e versões de Python.

O Python verifica a data de modificação do arquivo fonte mediante a versão compilada, para ver se está desatualizada e precisa ser recompilada. É um processo completamente automático. Além disso, os módulos compilados são independentes de plataforma, portanto a mesma biblioteca pode ser compartilhada entre sistemas de arquiteturas diferentes.

O Python não verifica as versões compiladas em duas circunstâncias. Primeiro, sempre recompila e não armazena o resultado para módulos carregados diretamente da linha de comando. Segundo, não verifica se não houver um módulo fonte. Para suportar uma distribuição sem fontes (somente as versões compiladas), o módulo compilado deve estar no diretório de fontes, e não deve haver um módulo fonte.

Algumas dicas para especialistas:

- Você pode usar as opções [`-O`](https://docs.python.org/pt-br/3.12/using/cmdline.html#cmdoption-O) ou [`-OO`](https://docs.python.org/pt-br/3.12/using/cmdline.html#cmdoption-OO) no comando Python para reduzir o tamanho de um módulo compilado. A opção `-O` remove as instruções assert, e a opção `-OO` remove, além das instruções assert, as strings de documentações. Como alguns programas podem contar com essa disponibilidade, só use essa opção se souber o que está fazendo. Módulos “otimizados” tem uma marcação `opt-` e são geralmente de menor tamanho. Futuros releases podem mudar os efeitos da otimização.
    
- Um programa não roda mais rápido quando é lido de um arquivo `.pyc` do que quando lido de um arquivo `.py`; a única coisa que é mais rápida com arquivos `.pyc` é sua velocidade de carregamento.
    
- O módulo [`compileall`](https://docs.python.org/pt-br/3.12/library/compileall.html#module-compileall "compileall: Tools for byte-compiling all Python source files in a directory tree.") pode criar arquivos .pyc para todos os módulos de um diretório.
    
- Há mais detalhes desse processo, incluindo um fluxograma de decisões, no [**PEP 3147**](https://peps.python.org/pep-3147/).
    

## 2. Módulos padrões[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#standard-modules "Link para este cabeçalho")

O Python traz uma biblioteca padrão de módulos, descrita em um documento em separado, a Referência da Biblioteca Python (doravante “Referência da Biblioteca”). Alguns módulos estão embutidos no interpretador; estes possibilitam acesso a operações que não são parte do núcleo da linguagem, mas estão no interpretador seja por eficiência ou para permitir o acesso a chamadas do sistema operacional. O conjunto destes módulos é uma opção de configuração que depende também da plataforma utilizada. Por exemplo, o módulo [`winreg`](https://docs.python.org/pt-br/3.12/library/winreg.html#module-winreg "winreg: Routines and objects for manipulating the Windows registry. (Windows)") só está disponível em sistemas Windows. Existe um módulo que requer especial atenção: [`sys`](https://docs.python.org/pt-br/3.12/library/sys.html#module-sys "sys: Access system-specific parameters and functions."), que é embutido em qualquer interpretador Python. As variáveis `sys.ps1` e `sys.ps2` definem as strings utilizadas como prompt primário e secundário:

```
import sys
sys.ps1
'>>> '
sys.ps2
'... '
sys.ps1 = 'C> '
C> print('ECA!')
Yuck!
C>
```

Essas variáveis só estão definidas se o interpretador está em modo interativo.

A variável `sys.path` contém uma lista de strings que determina os caminhos de busca de módulos conhecidos pelo interpretador. Ela é inicializada para um caminho padrão, determinado pela variável de ambiente [`PYTHONPATH`](https://docs.python.org/pt-br/3.12/using/cmdline.html#envvar-PYTHONPATH), ou por um valor padrão embutido, se [`PYTHONPATH`](https://docs.python.org/pt-br/3.12/using/cmdline.html#envvar-PYTHONPATH) não estiver definida. Você pode modificá-la com as operações típicas de lista, por exemplo:

```
import sys
sys.path.append('/ufs/guido/lib/python')
```

## 6.3. A função [`dir()`](https://docs.python.org/pt-br/3.12/library/functions.html#dir "dir")[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#the-dir-function "Link para este cabeçalho")

A função embutida [`dir()`](https://docs.python.org/pt-br/3.12/library/functions.html#dir "dir") é usada para descobrir quais nomes são definidos por um módulo. Ela devolve uma lista ordenada de strings:

```
import fibo, sys
dir(fibo)
['__name__', 'fib', 'fib2']
dir(sys)
['__breakpointhook__', '__displayhook__', '__doc__', '__excepthook__',
 '__interactivehook__', '__loader__', '__name__', '__package__', '__spec__',
 '__stderr__', '__stdin__', '__stdout__', '__unraisablehook__',
 '_clear_type_cache', '_current_frames', '_debugmallocstats', '_framework',
 '_getframe', '_git', '_home', '_xoptions', 'abiflags', 'addaudithook',
 'api_version', 'argv', 'audit', 'base_exec_prefix', 'base_prefix',
 'breakpointhook', 'builtin_module_names', 'byteorder', 'call_tracing',
 'callstats', 'copyright', 'displayhook', 'dont_write_bytecode', 'exc_info',
 'excepthook', 'exec_prefix', 'executable', 'exit', 'flags', 'float_info',
 'float_repr_style', 'get_asyncgen_hooks', 'get_coroutine_origin_tracking_depth',
 'getallocatedblocks', 'getdefaultencoding', 'getdlopenflags',
 'getfilesystemencodeerrors', 'getfilesystemencoding', 'getprofile',
 'getrecursionlimit', 'getrefcount', 'getsizeof', 'getswitchinterval',
 'gettrace', 'hash_info', 'hexversion', 'implementation', 'int_info',
 'intern', 'is_finalizing', 'last_traceback', 'last_type', 'last_value',
 'maxsize', 'maxunicode', 'meta_path', 'modules', 'path', 'path_hooks',
 'path_importer_cache', 'platform', 'prefix', 'ps1', 'ps2', 'pycache_prefix',
 'set_asyncgen_hooks', 'set_coroutine_origin_tracking_depth', 'setdlopenflags',
 'setprofile', 'setrecursionlimit', 'setswitchinterval', 'settrace', 'stderr',
 'stdin', 'stdout', 'thread_info', 'unraisablehook', 'version', 'version_info',
 'warnoptions']
```

Sem argumentos, [`dir()`](https://docs.python.org/pt-br/3.12/library/functions.html#dir "dir") lista os nomes atualmente definidos:

```
a = [1, 2, 3, 4, 5]
import fibo
fib = fibo.fib
dir()
['__builtins__', '__name__', 'a', 'fib', 'fibo', 'sys']
```

Observe que ela lista todo tipo de nomes: variáveis, módulos, funções, etc.

[`dir()`](https://docs.python.org/pt-br/3.12/library/functions.html#dir "dir") não lista os nomes de variáveis e funções embutidas. Esta lista está disponível no módulo padrão [`builtins`](https://docs.python.org/pt-br/3.12/library/builtins.html#module-builtins "builtins: The module that provides the built-in namespace."):

```
import builtins
dir(builtins)
['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException',
 'BlockingIOError', 'BrokenPipeError', 'BufferError', 'BytesWarning',
 'ChildProcessError', 'ConnectionAbortedError', 'ConnectionError',
 'ConnectionRefusedError', 'ConnectionResetError', 'DeprecationWarning',
 'EOFError', 'Ellipsis', 'EnvironmentError', 'Exception', 'False',
 'FileExistsError', 'FileNotFoundError', 'FloatingPointError',
 'FutureWarning', 'GeneratorExit', 'IOError', 'ImportError',
 'ImportWarning', 'IndentationError', 'IndexError', 'InterruptedError',
 'IsADirectoryError', 'KeyError', 'KeyboardInterrupt', 'LookupError',
 'MemoryError', 'NameError', 'None', 'NotADirectoryError', 'NotImplemented',
 'NotImplementedError', 'OSError', 'OverflowError',
 'PendingDeprecationWarning', 'PermissionError', 'ProcessLookupError',
 'ReferenceError', 'ResourceWarning', 'RuntimeError', 'RuntimeWarning',
 'StopIteration', 'SyntaxError', 'SyntaxWarning', 'SystemError',
 'SystemExit', 'TabError', 'TimeoutError', 'True', 'TypeError',
 'UnboundLocalError', 'UnicodeDecodeError', 'UnicodeEncodeError',
 'UnicodeError', 'UnicodeTranslateError', 'UnicodeWarning', 'UserWarning',
 'ValueError', 'Warning', 'ZeroDivisionError', '_', '__build_class__',
 '__debug__', '__doc__', '__import__', '__name__', '__package__', 'abs',
 'all', 'any', 'ascii', 'bin', 'bool', 'bytearray', 'bytes', 'callable',
 'chr', 'classmethod', 'compile', 'complex', 'copyright', 'credits',
 'delattr', 'dict', 'dir', 'divmod', 'enumerate', 'eval', 'exec', 'exit',
 'filter', 'float', 'format', 'frozenset', 'getattr', 'globals', 'hasattr',
 'hash', 'help', 'hex', 'id', 'input', 'int', 'isinstance', 'issubclass',
 'iter', 'len', 'license', 'list', 'locals', 'map', 'max', 'memoryview',
 'min', 'next', 'object', 'oct', 'open', 'ord', 'pow', 'print', 'property',
 'quit', 'range', 'repr', 'reversed', 'round', 'set', 'setattr', 'slice',
 'sorted', 'staticmethod', 'str', 'sum', 'super', 'tuple', 'type', 'vars',
 'zip']
```

## 4. Pacotes[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#packages "Link para este cabeçalho")

Os pacotes são uma maneira de estruturar o “espaço de nomes” dos módulos Python, usando “nomes de módulo com pontos”. Por exemplo, o nome do módulo `A.B` designa um submódulo chamado `B`, em um pacote chamado `A`. Assim como o uso de módulos evita que os autores de módulos diferentes tenham que se preocupar com nomes de variáveis globais, o uso de nomes de módulos com pontos evita que os autores de pacotes com muitos módulos, como NumPy ou Pillow, tenham que se preocupar com os nomes dos módulos uns dos outros.

Suponha que você queira projetar uma coleção de módulos (um “pacote”) para o gerenciamento uniforme de arquivos de som. Existem muitos formatos diferentes (normalmente identificados pela extensão do nome de arquivo, por exemplo `.wav`, `.aiff`, `.au`), de forma que você pode precisar criar e manter uma crescente coleção de módulos de conversão entre formatos. Ainda podem existir muitas operações diferentes, passíveis de aplicação sobre os arquivos de som (mixagem, eco, equalização, efeito stereo artificial). Logo, possivelmente você também estará escrevendo uma coleção sempre crescente de módulos para aplicar estas operações. Eis uma possível estrutura para o seu pacote (expressa em termos de um sistema de arquivos hierárquico):

```
sound/                          pacote de nível superior
      __init__.py               Inicializa o pacote de som
      formats/                  Subpacote para as conversões entre formatos de arquivos
              __init__.py
              wavread.py
              wavwrite.py
              aiffread.py
              aiffwrite.py
              auread.py
              auwrite.py
              ...
      effects/                  Subpacote para efeitos de som
              __init__.py
              echo.py
              surround.py
              reverse.py
              ...
      filters/                  Subpacote para filtros
              __init__.py
              equalizer.py
              vocoder.py
              karaoke.py
              ...
```

Ao importar esse pacote, Python busca pelo subdiretório com mesmo nome, nos diretórios listados em `sys.path`.

Os arquivos `__init__.py` são necessários para que o Python trate diretórios contendo o arquivo como pacotes (a menos que se esteja usando um [pacote de espaço de nomes](https://docs.python.org/pt-br/3.12/glossary.html#term-namespace-package), um recurso relativamente avançado). Isso impede que diretórios com um nome comum, como `string`, ocultem, involuntariamente, módulos válidos que ocorrem posteriormente no caminho de busca do módulo. No caso mais simples, `__init__.py` pode ser apenas um arquivo vazio, mas pode também executar código de inicialização do pacote, ou configurar a variável `__all__`, descrita mais adiante.

Usuários do pacote podem importar módulos individuais, por exemplo:

```
import sound.effects.echo
```

Isso carrega o submódulo `sound.effects.echo`. Ele deve ser referenciado com seu nome completo, como em:

```
sound.effects.echo.echofilter(input, output, delay=0.7, atten=4)
```

Uma maneira alternativa para a importação desse módulo é:

```
from sound.effects import echo
```

Isso carrega o submódulo `echo` sem necessidade de mencionar o prefixo do pacote no momento da utilização, assim:

```
echo.echofilter(input, output, delay=0.7, atten=4)
```

Também é possível importar diretamente uma única variável ou função:

```
from sound.effects.echo import echofilter
```

Novamente, isso carrega o submódulo `echo`, mas a função `echofilter()` está acessível diretamente sem prefixo:

```
echofilter(input, output, delay=0.7, atten=4)
```

Observe que ao utilizar `from pacote import item`, o item pode ser um subpacote, submódulo, classe, função ou variável. A instrução `import` primeiro testa se o item está definido no pacote, senão presume que é um módulo e tenta carregá-lo. Se falhar em encontrar o módulo, uma exceção [`ImportError`](https://docs.python.org/pt-br/3.12/library/exceptions.html#ImportError "ImportError") é levantada.

Em oposição, em uma construção como `import item.subitem.subsubitem`, cada item, com exceção do último, deve ser um pacote. O último pode ser também um pacote ou módulo, mas nunca uma classe, função ou variável contida em um módulo.

### 4.1. Importando * de um pacote[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#importing-from-a-package "Link para este cabeçalho")

Agora, o que acontece quando um usuário escreve `from sound.effects import *` ? Idealmente, poderia se esperar que este comando vasculhasse o sistema de arquivos, encontrasse todos os submódulos presentes no pacote, e os importasse. Isso poderia demorar muito e a importação de submódulos pode ocasionar efeitos colaterais, que somente deveriam ocorrer quando o submódulo é explicitamente importado.

A única solução é o autor do pacote fornecer um índice explícito do pacote. A instrução [`import`](https://docs.python.org/pt-br/3.12/reference/simple_stmts.html#import) usa a seguinte convenção: se o arquivo `__init__.py` do pacote define uma lista chamada `__all__`, então esta lista indica os nomes dos módulos a serem importados quando a instrução `from pacote import *` é acionada. Fica a cargo do autor do pacote manter esta lista atualizada, inclusive fica a seu critério excluir inteiramente o suporte a importação direta de todo o pacote através de `from pacote import *`. Por exemplo, o arquivo `sounds/effects/__init__.py` poderia conter apenas:

```
__all__ = ["echo", "surround", "reverse"]
```

Isso significaria que `from sound.effects import *` importaria os três submódulos nomeados do pacote `sound.effects`.

Esteja ciente de que os submódulos podem ficar sobrepostos por nomes definidos localmente. Por exemplo, se você adicionou uma função `reverse` ao arquivo `sound/effects/__init__.py`, usar `from sound.effects import *` só importaria os dois submódulos `echo` e `surround`, mas _não_ o submódulo `reverse`, porque ele fica sobreposto pela função `reverse` definida localmente:

```
__all__ = [
    "echo",      # refere-se ao arquivo 'echo.py'
    "surround",  # refere-se ao arquivo 'surround.py'
    "reverse",   # !!! refere-se à função 'reverse' agora !!!
]

def reverse(msg: str):  # <-- este nome ofusca o submódulo 'reverse.py'
    return msg[::-1]    #     no caso de uma importação 'from sound.effects import *'
```

Se `__all__` não estiver definido, a instrução `from sound.effects import *` não importa todos os submódulos do pacote `sound.effects` no espaço de nomes atual. Há apenas garantia que o pacote `sound.effects` foi importado (possivelmente executando qualquer código de inicialização em `__init__.py`) juntamente com os nomes definidos no pacote. Isso inclui todo nome definido em `__init__.py` bem como em qualquer submódulo importado a partir deste. Também inclui quaisquer submódulos do pacote que tenham sido carregados explicitamente por instruções [`import`](https://docs.python.org/pt-br/3.12/reference/simple_stmts.html#import) anteriores. Considere o código abaixo:

```
import sound.effects.echo
import sound.effects.surround
from sound.effects import *
```

Nesse exemplo, os módulos `echo` e `surround` são importados no espaço de nomes atual, no momento em que a instrução `from...import` é executada, pois estão definidos no pacote `sound.effects`. (Isso também funciona quando `__all__` estiver definida.)

Apesar de que certos módulos são projetados para exportar apenas nomes conforme algum critério quando se faz `import *`, ainda assim essa sintaxe é considerada uma prática ruim em código de produção.

Lembre-se, não há nada errado em usar `from pacote import submodulo_especifico`! De fato, essa é a notação recomendada, a menos que o módulo importado necessite usar submódulos com o mesmo nome, de diferentes pacotes.

### 4.2. Referências em um mesmo pacote[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#intra-package-references "Link para este cabeçalho")

Quando pacotes são estruturados em subpacotes (como no pacote `sound` do exemplo), pode-se usar a sintaxe de importações absolutas para se referir aos submódulos de pacotes irmãos (o que na prática é uma forma de fazer um import relativo, a partir da base do pacote). Por exemplo, se o módulo `sound.filters.vocoder` precisa usar o módulo `echo` do pacote `sound.effects`, é preciso importá-lo com `from sound.effects import echo`.

Também é possível escrever imports relativos, com a forma `from module import name`. Esses imports usam pontos para indicar o pacote pai e o atual, envolvidos no import relativo. Do módulo `surround`, por exemplo, pode-se usar:

```
from . import echo
from .. import formats
from ..filters import equalizer
```

Note que imports relativos são baseados no nome do módulo atual. Uma vez que o nome do módulo principal é sempre `"__main__"`, módulos destinados ao uso como módulo principal de um aplicativo Python devem sempre usar imports absolutos.

### 4.3. Pacotes em múltiplos diretórios[](https://docs.python.org/pt-br/3.12/tutorial/modules.html#packages-in-multiple-directories "Link para este cabeçalho")

Pacotes possuem mais um atributo especial, [`__path__`](https://docs.python.org/pt-br/3.12/reference/datamodel.html#module.__path__ "module.__path__"). Inicializado como uma [sequência](https://docs.python.org/pt-br/3.12/glossary.html#term-sequence) de strings contendo o nome do diretório onde está o arquivo `__init__.py` do pacote, antes do código naquele arquivo ser executado. Esta variável pode ser modificada; isso afeta a busca futura de módulos e subpacotes contidos no pacote.

Apesar de não ser muito usado, esse mecanismo permite estender o conjunto de módulos encontrados em um pacote.


Notas de rodapé

[[1](https://docs.python.org/pt-br/3.12/tutorial/modules.html#id1)]

[#] Na verdade, definições de funções também são ‘instruções’ que são ‘executados’; a execução da definição de uma função adiciona o nome da função no espaço de nomes global do módulo.