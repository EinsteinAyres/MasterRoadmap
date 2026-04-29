

https://docs.python.org/pt-br/3/library/venv.html


# `venv` — Criação de ambientes virtuais

Adicionado na versão 3.3.

**Código-fonte:** [Lib/venv/](https://github.com/python/cpython/tree/3.12/Lib/venv/)

---

O módulo `venv` oferece suporte à criação de “ambientes virtuais” leves, cada um com seu próprio conjunto independente de pacotes Python instalados em seus diretórios [`site`](https://docs.python.org/pt-br/3.12/library/site.html#module-site "site: Module responsible for site-specific configuration."). Um ambiente virtual é criado sobre uma instalação existente do Python, conhecido como o Python “base” do ambiente virtual, e pode, opcionalmente, ser isolado dos pacotes no ambiente base, de modo que apenas aqueles explicitamente instalados no ambiente virtual estejam disponíveis.

Quando usadas em um ambiente virtual, ferramentas de instalação comuns, como [pip](https://pypi.org/project/pip/), instalarão pacotes Python em um ambiente virtual sem precisar ser instruído a fazê-lo explicitamente.

Um ambiente virtual é (dentre outras coisas):

- Usado para conter um interpretador Python específico e bibliotecas de software e binários necessários para dar suporte a um projeto (biblioteca ou aplicação). Eles são, por padrão, isolados de software em outros ambientes virtuais e de interpretadores e bibliotecas Python instalados no sistema operacional.
    
- Contido em um diretório, convencionalmente denominado `.venv` ou `venv` no diretório do projeto, ou em um diretório contêiner para vários ambientes virtuais, como `~/.virtualenvs`.
    
- Não inserido em sistemas de controle de código-fonte, como Git.
    
- Considerado descartável – deve ser simples excluí-lo e recriá-lo do zero. Você não coloca nenhum código de projeto no ambiente.
    
- Não é considerado móvel ou copiável – basta recriar o mesmo ambiente no local de destino.
    

Veja [**PEP 405**](https://peps.python.org/pep-0405/) para mais informações sobre ambientes virtuais do Python.

Ver também: [Python Packaging User Guide: Criar e usar ambientes virtuais](https://packaging.python.org/pt-br/latest/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments)

[Disponibilidade](https://docs.python.org/pt-br/3.12/library/intro.html#availability)

Este módulo não funciona ou não está disponível em plataformas WebAssembly `wasm32-emscripten` e `wasm32-wasi`. Veja [Plataformas WebAssembly](https://docs.python.org/pt-br/3.12/library/intro.html#wasm-availability) para mais informações.

## Criando ambientes virtuais[](https://docs.python.org/pt-br/3.12/library/venv.html#creating-virtual-environments "Link para este cabeçalho")

As [ambiente virtuais](https://docs.python.org/pt-br/3.12/library/venv.html#venv-def) são criadas executando o módulo `venv`:

python -m venv /caminho/para/novo/ambiente/virtual

Isso cria o diretório de destino (incluindo diretórios pais conforme necessário) e coloca um arquivo `pyvenv.cfg` nele com uma chave `home` apontando para a instalação do Python da qual o comando foi executado. Ele também cria um subdiretório `bin` (ou `Scripts` no Windows) contendo uma cópia ou link simbólico do executável Python (conforme apropriado para a plataforma ou argumentos usados no momento da criação do ambiente). Ele também cria um subdiretório `lib/pythonX.Y/site-packages` (no Windows, este é `Libsite-packages`). Se um diretório existente for especificado, ele será reutilizado.

Alterado na versão 3.5: O uso de `venv` agora é recomendado para a criação de ambientes virtuais.

Deprecated since version 3.6, removed in version 3.8: **pyvenv** era a ferramenta recomendada para criar ambientes virtuais para Python 3.3 e 3.4, e foi substituída na versão 3.5 pela execução direta de `venv`.

No Windows, invoque o comando `venv` da seguinte forma:

```
python -m venv C:\caminho\para\novo\ambiente\virtual
```

O comando, se executado com `-h`, mostrará as opções disponíveis:

usage: venv [-h] [--system-site-packages] [--symlinks | --copies] [--clear]
            [--upgrade] [--without-pip] [--prompt PROMPT] [--upgrade-deps]
            ENV_DIR [ENV_DIR ...]

Creates virtual Python environments in one or more target directories.

positional arguments:
  ENV_DIR               A directory to create the environment in.

options:
  -h, --help            show this help message and exit
  --system-site-packages
                        Give the virtual environment access to the system
                        site-packages dir.
  --symlinks            Try to use symlinks rather than copies, when
                        symlinks are not the default for the platform.
  --copies              Try to use copies rather than symlinks, even when
                        symlinks are the default for the platform.
  --clear               Delete the contents of the environment directory
                        if it already exists, before environment creation.
  --upgrade             Upgrade the environment directory to use this
                        version of Python, assuming Python has been
                        upgraded in-place.
  --without-pip         Skips installing or upgrading pip in the virtual
                        environment (pip is bootstrapped by default)
  --prompt PROMPT       Provides an alternative prompt prefix for this
                        environment.
  --upgrade-deps        Upgrade core dependencies (pip) to the latest
                        version in PyPI

Once an environment has been created, you may wish to activate it, e.g. by
sourcing an activate script in its bin directory.

Alterado na versão 3.4: Instala o pip por padrão, adicionadas as opções `--without-pip` e `--copies`.

Alterado na versão 3.4: Nas versões anteriores, se o diretório de destino já existia, era levantado um erro, a menos que a opção `--clear` ou `--upgrade` fosse fornecida.

Alterado na versão 3.9: Adiciona a opção `--upgrade-deps` para atualizar pip + setuptools para a última no PyPI.

Alterado na versão 3.12: `setuptools` não é mais uma dependência central do venv.

Nota

 

Embora haja suporte a links simbólicos no Windows, eles não são recomendados. É importante notar que clicar duas vezes em `python.exe` no Explorador de Arquivos resolverá o link simbólico com entusiasmo e ignorará o ambiente virtual.

Nota

 

No Microsoft Windows, pode ser necessário ativar o script `Activate.ps1`, definindo a política de execução para o usuário. Você pode fazer isso executando o seguinte comando do PowerShell:

PS C:\> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

Consulte [About Execution Policies](https://go.microsoft.com/fwlink/?LinkID=135170) para mais informações.

O arquivo `pyvenv.cfg` criado também inclui a chave `include-system-site-packages`, definida como `true` se `venv` for executado com a opção `--system-site-packages`; caso contrário, `false`.

A menos que a opção `--without-pip` seja dada, [`ensurepip`](https://docs.python.org/pt-br/3.12/library/ensurepip.html#module-ensurepip "ensurepip: Bootstrapping the \"pip\" installer into an existing Python installation or virtual environment.") será chamado para inicializar o `pip` no ambiente virtual.

Vários caminhos podem ser dados para `venv`, caso em que um ambiente virtual idêntico será criado, de acordo com as opções fornecidas, em cada caminho fornecido.

## Como funcionam os venvs[](https://docs.python.org/pt-br/3.12/library/venv.html#how-venvs-work "Link para este cabeçalho")

Quando um interpretador Python está sendo executado a partir de um ambiente virtual, [`sys.prefix`](https://docs.python.org/pt-br/3.12/library/sys.html#sys.prefix "sys.prefix") e [`sys.exec_prefix`](https://docs.python.org/pt-br/3.12/library/sys.html#sys.exec_prefix "sys.exec_prefix") apontam para os diretórios do ambiente virtual, enquanto [`sys.base_prefix`](https://docs.python.org/pt-br/3.12/library/sys.html#sys.base_prefix "sys.base_prefix") e [`sys.base_exec_prefix`](https://docs.python.org/pt-br/3.12/library/sys.html#sys.base_exec_prefix "sys.base_exec_prefix") apontam para aqueles do Python base usado para criar o ambiente. É suficiente verificar `sys.prefix != sys.base_prefix` para determinar se o interpretador atual está sendo executado a partir de um ambiente virtual.

Um ambiente virtual pode ser “ativado” usando um script em seu diretório binário (`bin` no POSIX; `Scripts` no Windows). Isso precederá esse diretório ao seu `PATH`, de modo que a execução de **python** invoque o interpretador Python do ambiente e você possa executar os scripts instalados sem precisar usar o caminho completo. A invocação do script de ativação é específica da plataforma (`_<venv>_` deve ser substituído pelo caminho para o diretório que contém o ambiente virtual):

|Plataforma|Shell|Comando para ativar o ambiente virtual|
|---|---|---|
|POSIX|bash/zsh|`$ source _<venv>_/bin/activate`|
|fish|`$ source _<venv>_/bin/activate.fish`|
|csh/tcsh|`$ source _<venv>_/bin/activate.csh`|
|pwsh|`$ _<venv>_/bin/Activate.ps1`|
|Windows|cmd.exe|`C:\> _<venv>_\Scripts\activate.bat`|
|PowerShell|`PS C:\> _<venv>_\Scripts\Activate.ps1`|

Adicionado na versão 3.4: Os scripts de ativação **fish** e **csh**.

Adicionado na versão 3.8: Scripts de ativação de PowerShell instalados sob POSIX para suporte a PowerShell Core.

Você não _precisa_ especificamente ativar um ambiente virtual, pois pode apenas especificar o caminho completo para o interpretador Python desse ambiente ao invocar o Python. Além disso, todos os scripts instalados no ambiente devem ser executáveis sem ativá-lo.

Para isso, os scripts instalados em ambientes virtuais possuem uma linha “shebang” que aponta para o interpretador Python do ambiente `#!/_<caminho-para-venv>_/bin/python`. Isso significa que o script será executado com esse interpretador independentemente do valor de `PATH`. No Windows, o processamento de linha “shebang” é suportado se você tiver o [Inicializador Python para Windows](https://docs.python.org/pt-br/3.12/using/windows.html#launcher) instalado. Assim, clicar duas vezes em um script instalado em uma janela do Windows Explorer deve executá-lo com o interpretador correto sem que o ambiente precise ser ativado ou no `PATH`.

Quando um ambiente virtual é ativado, a variável de ambiente `VIRTUAL_ENV` é definida como o caminho do ambiente. Como não é necessário ativar explicitamente um ambiente virtual para usá-lo, `VIRTUAL_ENV` não pode ser usado para determinar se um ambiente virtual está sendo usado.

Aviso

 

Como os scripts instalados em ambientes não devem esperar que o ambiente seja ativado, suas linhas shebang contêm os caminhos absolutos para os interpretadores de seus ambientes. Por causa disso, os ambientes são inerentemente não portáteis, no caso geral. Você sempre deve ter um meio simples de recriar um ambiente (por exemplo, se você tiver um arquivo de requisitos `requirements.txt`, você pode invocar `pip install -r requirements.txt` usando o `pip` do ambiente para instalar todos os pacotes necessários para o ambiente). Se por algum motivo você precisar mover o ambiente para um novo local, deverá recriá-lo no local desejado e excluir o do local antigo. Se você mover um ambiente porque moveu um diretório pai dele, deverá recriar o ambiente em seu novo local. Caso contrário, o software instalado no ambiente pode não funcionar conforme o esperado.

Você pode desativar um ambiente virtual digitando `deactivate` em seu shell. O mecanismo exato é específico da plataforma e é um detalhe de implementação interna (normalmente, um script ou função de shell será usado).

## API[](https://docs.python.org/pt-br/3.12/library/venv.html#api "Link para este cabeçalho")

O método de alto nível descrito acima utiliza uma API simples que fornece mecanismos para que criadores de ambientes virtuais de terceiros personalizem a criação do ambiente de acordo com suas necessidades, a classe [`EnvBuilder`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder "venv.EnvBuilder").

_class_ venv.EnvBuilder(_system_site_packages=False_, _clear=False_, _symlinks=False_, _upgrade=False_, _with_pip=False_, _prompt=None_, _upgrade_deps=False_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder "Link para esta definição")

A classe [`EnvBuilder`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder "venv.EnvBuilder") aceita os seguintes argumentos nomeados na instanciação:

- _system_site_packages_ – um valor booleano indicando que os pacotes de sites do sistema Python devem estar disponíveis para o ambiente (o padrão é `False`).
    
- _clear_ – um valor booleano que, se verdadeiro, excluirá o conteúdo de qualquer diretório de destino existente, antes de criar o ambiente.
    
- _symlinks_ – um valor booleano que indica se você deseja vincular o binário Python ao invés de copiar.
    
- _upgrade_ – um valor booleano que, se verdadeiro, atualizará um ambiente existente com o Python em execução - para uso quando o Python tiver sido atualizado localmente (o padrão é `False`).
    
- _with_pip_ – um valor booleano que, se verdadeiro, garante que o pip seja instalado no ambiente virtual. Isso usa [`ensurepip`](https://docs.python.org/pt-br/3.12/library/ensurepip.html#module-ensurepip "ensurepip: Bootstrapping the \"pip\" installer into an existing Python installation or virtual environment.") com a opção `--default-pip`.
    
- _prompt_ – uma string a ser usada após o ambiente virtual ser ativado (o padrão é `None`, o que significa que o nome do diretório do ambiente seria usado). Se a string especial `"."` for fornecida, o nome da base do diretório atual será usado como prompt.
    
- _upgrade_deps_ – Atualiza os módulos base do venv para os mais recentes no PyPI
    

Alterado na versão 3.4: Adicionado o parâmetro `with_pip`

Alterado na versão 3.6: Adicionado o parâmetro `prompt`

Alterado na versão 3.9: Adicionado o parâmetro `upgrade_deps`

[`EnvBuilder`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder "venv.EnvBuilder") pode ser usado como uma classe base.

create(_env_dir_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.create "Link para esta definição")

Cria um ambiente virtual especificando o diretório de destino (absoluto ou relativo ao diretório atual) que deve conter o ambiente virtual. O método `create` cria o ambiente no diretório especificado ou levanta uma exceção apropriada.

O método `create` da classe [`EnvBuilder`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder "venv.EnvBuilder") ilustra os ganchos disponíveis para personalização de subclasse:

def create(self, env_dir):
    """
    Cria um ambiente Python virtualizado em um diretório.
    env_dir é o diretório de destino para criar um ambiente.
    """
    env_dir = os.path.abspath(env_dir)
    context = self.ensure_directories(env_dir)
    self.create_configuration(context)
    self.setup_python(context)
    self.setup_scripts(context)
    self.post_setup(context)

Cada um dos métodos [`ensure_directories()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.ensure_directories "venv.EnvBuilder.ensure_directories"), [`create_configuration()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.create_configuration "venv.EnvBuilder.create_configuration"), [`setup_python()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.setup_python "venv.EnvBuilder.setup_python"), [`setup_scripts()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.setup_scripts "venv.EnvBuilder.setup_scripts") e [`post_setup()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.post_setup "venv.EnvBuilder.post_setup") pode ser substituído.

ensure_directories(_env_dir_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.ensure_directories "Link para esta definição")

Cria o diretório do ambiente e todos os subdiretórios necessários que ainda não existem e retorna um objeto de contexto. Este objeto de contexto é apenas um detentor de atributos (como caminhos) para uso pelos outros métodos. Se o [`EnvBuilder`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder "venv.EnvBuilder") for criado com o argumento `clear=True`, o conteúdo do diretório do ambiente será limpo e todos os subdiretórios necessários serão recriados.

O objeto de contexto retornado é um [`types.SimpleNamespace`](https://docs.python.org/pt-br/3.12/library/types.html#types.SimpleNamespace "types.SimpleNamespace") com os seguintes atributos:

- `env_dir` - A localização do ambiente virtual. Usado para `__VENV_DIR__` em scripts de ativação (veja [`install_scripts()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.install_scripts "venv.EnvBuilder.install_scripts")).
    
- `env_name` - O nome do ambiente virtual. Usado para `__VENV_NAME__` em scripts de ativação (veja [`install_scripts()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.install_scripts "venv.EnvBuilder.install_scripts")).
    
- `prompt` - O prompt a ser usado pelos scripts de ativação. Usado para `__VENV_PROMPT__` em scripts de ativação (veja [`install_scripts()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.install_scripts "venv.EnvBuilder.install_scripts")).
    
- `executable` - O executável Python subjacente usado pelo ambiente virtual. Isso leva em consideração o caso em que um ambiente virtual é criado a partir de outro ambiente virtual.
    
- `inc_path` - O caminho de inclusão para o ambiente virtual.
    
- `lib_path` - O caminho purelib para o ambiente virtual.
    
- `bin_path` - O caminho do script para o ambiente virtual.
    
- `bin_name` - O nome do caminho do script relativo ao local do ambiente virtual. Usado para `__VENV_BIN_NAME__` em scripts de ativação (consulte [`install_scripts()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.install_scripts "venv.EnvBuilder.install_scripts")).
    
- `env_exe` - O nome do interpretador Python no ambiente virtual. Usado para `__VENV_PYTHON__` em scripts de ativação (veja [`install_scripts()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.install_scripts "venv.EnvBuilder.install_scripts")).
    
- `env_exec_cmd` - O nome do interpretador Python, levando em consideração os redirecionamentos do sistema de arquivos. Isso pode ser usado para executar o Python no ambiente virtual.
    

Alterado na versão 3.11: O [esquema de instalação de sysconfig](https://docs.python.org/pt-br/3.12/library/sysconfig.html#installation-paths) do _venv_ é usado para construir os caminhos dos diretórios criados.

Alterado na versão 3.12: O atributo `lib_path` foi adicionado ao contexto, e o objeto de contexto foi documentado.

create_configuration(_context_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.create_configuration "Link para esta definição")

Cria o arquivo de configuração `pyvenv.cfg` no ambiente.

setup_python(_context_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.setup_python "Link para esta definição")

Cria uma cópia ou link simbólico para o executável Python no ambiente. Nos sistemas POSIX, se um executável específico `python3.x` foi usado, links simbólicos para `python` e `python3` serão criados apontando para esse executável, a menos que já existam arquivos com esses nomes.

setup_scripts(_context_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.setup_scripts "Link para esta definição")

Instala scripts de ativação apropriados para a plataforma no ambiente virtual.

upgrade_dependencies(_context_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.upgrade_dependencies "Link para esta definição")

Atualiza os principais pacotes de dependência do venv (atualmente [pip](https://pypi.org/project/pip/)) no ambiente. Isso é feito através da distribuição do executável `pip` no ambiente.

Adicionado na versão 3.9.

Alterado na versão 3.12: [setuptools](https://pypi.org/project/setuptools/) não é mais uma dependência central do venv.

post_setup(_context_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.post_setup "Link para esta definição")

Um método de espaço reservado que pode ser substituído em implementações de terceiros para pré-instalar pacotes no ambiente virtual ou executar outras etapas pós-criação.

install_scripts(_context_, _path_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.install_scripts "Link para esta definição")

Este método utilitário que pode ser chamado de [`setup_scripts()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.setup_scripts "venv.EnvBuilder.setup_scripts") ou [`post_setup()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.post_setup "venv.EnvBuilder.post_setup") nas subclasses para ajudar na instalação de scripts personalizados no ambiente virtual.

_path_ é o caminho para um diretório que deve conter subdiretórios `common`, `posix`, `nt`; cada um contendo scripts destinados ao diretório `bin` no ambiente. O conteúdo de `common` e o diretório correspondente a [`os.name`](https://docs.python.org/pt-br/3.12/library/os.html#os.name "os.name") são copiados após alguma substituição de texto dos espaços reservados:

- `__VENV_DIR__` é substituído pelo caminho absoluto do diretório do ambiente.
    
- `__VENV_NAME__` é substituído pelo nome do ambiente (segmento do caminho final do diretório do ambiente).
    
- `__VENV_PROMPT__` é substituído pelo prompt (o nome do ambiente entre parênteses e com o seguinte espaço)
    
- `__VENV_BIN_NAME__` é substituído pelo nome do diretório bin (`bin` ou `Scripts`).
    
- `__VENV_PYTHON__` é substituído pelo caminho absoluto do executável do ambiente.
    

É permitido que os diretórios existam (para quando um ambiente existente estiver sendo atualizado).

Alterado na versão 3.7.2: O Windows agora usa scripts redirecionadores para `python[w].exe` em vez de copiar os binários reais. No 3.7.2, somente [`setup_python()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.setup_python "venv.EnvBuilder.setup_python") não faz nada a menos que seja executado a partir de uma construção na árvore de origem.

Alterado na versão 3.7.3: O Windows copia os scripts redirecionadores como parte do [`setup_python()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.setup_python "venv.EnvBuilder.setup_python") em vez de [`setup_scripts()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.setup_scripts "venv.EnvBuilder.setup_scripts"). Este não foi o caso em 3.7.2. Ao usar links simbólicos, será feito link para os executáveis originais.

Há também uma função de conveniência no nível do módulo:

venv.create(_env_dir_, _system_site_packages=False_, _clear=False_, _symlinks=False_, _with_pip=False_, _prompt=None_, _upgrade_deps=False_)[](https://docs.python.org/pt-br/3.12/library/venv.html#venv.create "Link para esta definição")

Cria um [`EnvBuilder`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder "venv.EnvBuilder") com os argumentos nomeados fornecidos e chame seu método [`create()`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder.create "venv.EnvBuilder.create") com o argumento _env_dir_.

Adicionado na versão 3.3.

Alterado na versão 3.4: Adicionado o parâmetro _with_pip_

Alterado na versão 3.6: Adicionado o parâmetro _prompt_

Alterado na versão 3.9: Adicionado o parâmetro _upgrade_deps_

## Um exemplo de extensão de `EnvBuilder`[](https://docs.python.org/pt-br/3.12/library/venv.html#an-example-of-extending-envbuilder "Link para este cabeçalho")

O script a seguir mostra como estender [`EnvBuilder`](https://docs.python.org/pt-br/3.12/library/venv.html#venv.EnvBuilder "venv.EnvBuilder") implementando uma subclasse que instala setuptools e pip em um ambiente virtual criado:

import os
import os.path
from subprocess import Popen, PIPE
import sys
from threading import Thread
from urllib.parse import urlparse
from urllib.request import urlretrieve
import venv

class ExtendedEnvBuilder(venv.EnvBuilder):
    """
    This builder installs setuptools and pip so that you can pip or
    easy_install other packages into the created virtual environment.

    :param nodist: If true, setuptools and pip are not installed into the
                   created virtual environment.
    :param nopip: If true, pip is not installed into the created
                  virtual environment.
    :param progress: If setuptools or pip are installed, the progress of the
                     installation can be monitored by passing a progress
                     callable. If specified, it is called with two
                     arguments: a string indicating some progress, and a
                     context indicating where the string is coming from.
                     The context argument can have one of three values:
                     'main', indicating that it is called from virtualize()
                     itself, and 'stdout' and 'stderr', which are obtained
                     by reading lines from the output streams of a subprocess
                     which is used to install the app.

                     If a callable is not specified, default progress
                     information is output to sys.stderr.
    """

    def __init__(self, *args, **kwargs):
        self.nodist = kwargs.pop('nodist', False)
        self.nopip = kwargs.pop('nopip', False)
        self.progress = kwargs.pop('progress', None)
        self.verbose = kwargs.pop('verbose', False)
        super().__init__(*args, **kwargs)

    def post_setup(self, context):
        """
        Set up any packages which need to be pre-installed into the
        virtual environment being created.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        os.environ['VIRTUAL_ENV'] = context.env_dir
        if not self.nodist:
            self.install_setuptools(context)
        # Can't install pip without setuptools
        if not self.nopip and not self.nodist:
            self.install_pip(context)

    def reader(self, stream, context):
        """
        Read lines from a subprocess' output stream and either pass to a progress
        callable (if specified) or write progress information to sys.stderr.
        """
        progress = self.progress
        while True:
            s = stream.readline()
            if not s:
                break
            if progress is not None:
                progress(s, context)
            else:
                if not self.verbose:
                    sys.stderr.write('.')
                else:
                    sys.stderr.write(s.decode('utf-8'))
                sys.stderr.flush()
        stream.close()

    def install_script(self, context, name, url):
        _, _, path, _, _, _ = urlparse(url)
        fn = os.path.split(path)[-1]
        binpath = context.bin_path
        distpath = os.path.join(binpath, fn)
        # Download script into the virtual environment's binaries folder
        urlretrieve(url, distpath)
        progress = self.progress
        if self.verbose:
            term = '\n'
        else:
            term = ''
        if progress is not None:
            progress('Installing %s ...%s' % (name, term), 'main')
        else:
            sys.stderr.write('Installing %s ...%s' % (name, term))
            sys.stderr.flush()
        # Install in the virtual environment
        args = [context.env_exe, fn]
        p = Popen(args, stdout=PIPE, stderr=PIPE, cwd=binpath)
        t1 = Thread(target=self.reader, args=(p.stdout, 'stdout'))
        t1.start()
        t2 = Thread(target=self.reader, args=(p.stderr, 'stderr'))
        t2.start()
        p.wait()
        t1.join()
        t2.join()
        if progress is not None:
            progress('done.', 'main')
        else:
            sys.stderr.write('done.\n')
        # Clean up - no longer needed
        os.unlink(distpath)

    def install_setuptools(self, context):
        """
        Install setuptools in the virtual environment.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        url = "https://bootstrap.pypa.io/ez_setup.py"
        self.install_script(context, 'setuptools', url)
        # clear up the setuptools archive which gets downloaded
        pred = lambda o: o.startswith('setuptools-') and o.endswith('.tar.gz')
        files = filter(pred, os.listdir(context.bin_path))
        for f in files:
            f = os.path.join(context.bin_path, f)
            os.unlink(f)

    def install_pip(self, context):
        """
        Install pip in the virtual environment.

        :param context: The information for the virtual environment
                        creation request being processed.
        """
        url = 'https://bootstrap.pypa.io/get-pip.py'
        self.install_script(context, 'pip', url)

def main(args=None):
    import argparse

    parser = argparse.ArgumentParser(prog=__name__,
                                     description='Creates virtual Python '
                                                 'environments in one or '
                                                 'more target '
                                                 'directories.')
    parser.add_argument('dirs', metavar='ENV_DIR', nargs='+',
                        help='A directory in which to create the '
                             'virtual environment.')
    parser.add_argument('--no-setuptools', default=False,
                        action='store_true', dest='nodist',
                        help="Don't install setuptools or pip in the "
                             "virtual environment.")
    parser.add_argument('--no-pip', default=False,
                        action='store_true', dest='nopip',
                        help="Don't install pip in the virtual "
                             "environment.")
    parser.add_argument('--system-site-packages', default=False,
                        action='store_true', dest='system_site',
                        help='Give the virtual environment access to the '
                             'system site-packages dir.')
    if os.name == 'nt':
        use_symlinks = False
    else:
        use_symlinks = True
    parser.add_argument('--symlinks', default=use_symlinks,
                        action='store_true', dest='symlinks',
                        help='Try to use symlinks rather than copies, '
                             'when symlinks are not the default for '
                             'the platform.')
    parser.add_argument('--clear', default=False, action='store_true',
                        dest='clear', help='Delete the contents of the '
                                           'virtual environment '
                                           'directory if it already '
                                           'exists, before virtual '
                                           'environment creation.')
    parser.add_argument('--upgrade', default=False, action='store_true',
                        dest='upgrade', help='Upgrade the virtual '
                                             'environment directory to '
                                             'use this version of '
                                             'Python, assuming Python '
                                             'has been upgraded '
                                             'in-place.')
    parser.add_argument('--verbose', default=False, action='store_true',
                        dest='verbose', help='Display the output '
                                             'from the scripts which '
                                             'install setuptools and pip.')
    options = parser.parse_args(args)
    if options.upgrade and options.clear:
        raise ValueError('you cannot supply --upgrade and --clear together.')
    builder = ExtendedEnvBuilder(system_site_packages=options.system_site,
                                   clear=options.clear,
                                   symlinks=options.symlinks,
                                   upgrade=options.upgrade,
                                   nodist=options.nodist,
                                   nopip=options.nopip,
                                   verbose=options.verbose)
    for d in options.dirs:
        builder.create(d)

if __name__ == '__main__':
    rc = 1
    try:
        main()
        rc = 0
    except Exception as e:
        print('Error: %s' % e, file=sys.stderr)
    sys.exit(rc)

Esse script também está disponível para download [online](https://gist.github.com/vsajip/4673395).