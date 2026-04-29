

## Criar e usar ambientes virtuais

### Criar um novo ambiente virtual

[venv](https://packaging.python.org/pt-br/latest/key_projects/#venv) (para Python 3) permite gerenciar instalações de pacotes separadas para projetos diferentes. Ele cria uma instalação Python “virtual” e isolada. Ao alternar projetos, você pode criar um novo ambiente virtual isolado de outros ambientes virtuais. Você se beneficia do ambiente virtual, pois os pacotes podem ser instalados com segurança e não interferirão no ambiente de outro projeto.

Dica

Recomenda-se usar um ambiente virtual ao trabalhar com pacotes de terceiros.

Para criar um ambiente virtual, acesse o diretório do seu projeto e execute o comando a seguir. Isso criará um novo ambiente virtual em uma pasta local chamada `.venv`:

Unix/macOS

python3 -m venv .venv

Windows

O segundo argumento é o local para criar o ambiente virtual. Geralmente, você pode apenas criar isso em seu projeto e chamá-lo de `.venv`.

`venv` irá criar uma instalação virtual Python na pasta `.venv`.

Nota

Você deve excluir seu diretório de ambiente virtual de seu sistema de controle de versão usando `.gitignore` ou similar.

### Ativar um ambiente virtual

Antes de começar a instalar ou usar pacotes em seu ambiente virtual, você precisará ativá-lo, com `activate`. Ativar um ambiente virtual colocará os executáveis `python` e `pip` específicos do ambiente virtual no `PATH` de seu shell.

Unix/macOS

source .venv/bin/activate

Windows

Para confirmar o ambiente virtual está ativado, verifique o local do seu interpretador Python:

Unix/macOS

which python

Windows

Enquanto o ambiente virtual estiver ativo, o comando acima irá gerar um caminho de arquivo que inclui o diretório `.venv`, terminando com o seguinte:

Unix/macOS

.venv/bin/python

Windows

Enquanto um ambiente virtual estiver ativado, pip instalará pacotes naquele ambiente específico. Isso permite que você importe e use pacotes em sua aplicação Python.

### Desativar um ambiente virtual

Se você deseja trocar de projeto ou sair do seu ambiente virtual, desative o ambiente com `deactivate`:

deactivate

Nota

Fechar seu shell desativará o ambiente virtual. Se você abrir uma nova janela do shell e quiser usar o ambiente virtual, reative-o.

### Reativar um ambiente virtual

Se você quiser reativar um ambiente virtual existente, siga as mesmas instruções sobre como ativar um ambiente virtual. Não há necessidade de criar um novo ambiente virtual.

## Preparar o pip

[pip](https://packaging.python.org/pt-br/latest/key_projects/#pip) é o gerenciador de pacotes de referência Python. É usado para instalar e atualizar pacotes. Ele é usado para instalar e atualizar pacotes em um ambiente virtual.

Unix/macOS

Os instaladores de Python para macOS incluem pip. No Linux, você pode ter que instalar um pacote adicional como `python3-pip`. Você pode ter certeza de que o pip está atualizado executando:

python3 -m pip install --upgrade pip
python3 -m pip --version

Depois disso, você deve ter a versão mais recente do pip instalado em seu site de usuário:

pip 23.3.1 from .../.venv/lib/python3.9/site-packages (python 3.9)

Windows

## Instalar pacotes usando pip

Quando seu ambiente virtual estiver ativado, você poderá instalar pacotes. Use o comando `pip install` para instalar pacotes.

### Instalar um pacote

For example, let’s install the [Requests](https://pypi.org/project/requests/) library from the [Python Package Index (PyPI)](https://packaging.python.org/pt-br/latest/glossary/#term-Python-Package-Index-PyPI):

Unix/macOS

python3 -m pip install requests

Windows

pip deve baixar solicitações e todas as suas dependências e instalá-los:

Collecting requests
  Using cached requests-2.18.4-py2.py3-none-any.whl
Collecting chardet<3.1.0,>=3.0.2 (from requests)
  Using cached chardet-3.0.4-py2.py3-none-any.whl
Collecting urllib3<1.23,>=1.21.1 (from requests)
  Using cached urllib3-1.22-py2.py3-none-any.whl
Collecting certifi>=2017.4.17 (from requests)
  Using cached certifi-2017.7.27.1-py2.py3-none-any.whl
Collecting idna<2.7,>=2.5 (from requests)
  Using cached idna-2.6-py2.py3-none-any.whl
Installing collected packages: chardet, urllib3, certifi, idna, requests
Successfully installed certifi-2017.7.27.1 chardet-3.0.4 idna-2.6 requests-2.18.4 urllib3-1.22

### Instalar uma versão específica do pacote

pip permite que você especifique qual versão de um pacote instalar usando [especificadores de versão](https://packaging.python.org/pt-br/latest/glossary/#term-Version-Specifier). Por exemplo, para instalar uma versão específica do `requests`:

Unix/macOS

python3 -m pip install 'requests==2.18.4'

Windows

Para instalar a versão `2.x` mais recente do requests:

Unix/macOS

python3 -m pip install 'requests>=2.0.0,<3.0.0'

Windows

Para instalar versões de pré-lançamento de pacotes, use o sinalizador `--pre`:

Unix/macOS

python3 -m pip install --pre requests

Windows

### Instalar extras

Alguns pacotes possuem [extras](https://setuptools.readthedocs.io/en/latest/userguide/dependency_management.html#optional-dependencies) opcionais. Você pode dizer ao pip para instalá-los especificando o extra entre colchetes:

Unix/macOS

python3 -m pip install 'requests[security]'

Windows

### Instalar um pacote a partir do código-fonte

pip pode instalar um pacote diretamente de seu código-fonteseu. Por exemplo, você pode instalar o código-fonte no diretório `google-auth`:

Unix/macOS

cd google-auth
python3 -m pip install .

Windows

Além disso, pip pode instalar pacotes do código-fonte no [modo de desenvolvimento](https://setuptools.pypa.io/en/latest/userguide/development_mode.html "(em setuptools v82.0.1)"), o que significa que as mudanças no diretório de código-fonte afetarão imediatamente o pacote instalado sem a necessidade de reinstalar:

Unix/macOS

python3 -m pip install --editable .

Windows

### Instalar a partir de sistemas de controle de versão

pip pode instalar pacotes diretamente de seu sistema de controle de versão. Por exemplo, você pode instalar diretamente de um repositório git:

google-auth @ git+https://github.com/GoogleCloudPlatform/google-auth-library-python.git

Para mais informações sobre os sistemas de controle de versão suportados e sintaxe, consulte a documentação do pip em [Suporte VCS](https://pip.pypa.io/en/latest/topics/vcs-support/#vcs-support "(em pip v26.1)").

### Instalar a partir de arquivos locais

Se você tiver uma cópia local de um arquivo do [Pacote de Distribuição](https://packaging.python.org/pt-br/latest/glossary/#term-Distribution-Package) (um arquivo zip, wheel ou tar), você pode instalá-lo diretamente com pip:

Unix/macOS

python3 -m pip install requests-2.18.4.tar.gz

Windows

Se você tiver um diretório contendo arquivos de vários pacotes, você pode dizer ao pip para procurar por pacotes lá e não usar o [Python Package Index (PyPI)](https://packaging.python.org/pt-br/latest/glossary/#term-Python-Package-Index-PyPI) de forma alguma:

Unix/macOS

python3 -m pip install --no-index --find-links=/local/dir/ requests

Windows

Isso é útil se você estiver instalando pacotes em um sistema com conectividade limitada ou se quiser controlar estritamente a origem dos pacotes de distribuição.

### Instalar a partir de outros índices de pacote

Se você quiser baixar pacotes de um índice diferente do [Python Package Index (PyPI)](https://packaging.python.org/pt-br/latest/glossary/#term-Python-Package-Index-PyPI), você pode usar o sinalizador `--index-url`:

Unix/macOS

python3 -m pip install --index-url http://index.example.com/simple/ SomeProject

Windows

Se você deseja permitir pacotes do [Python Package Index (PyPI)](https://packaging.python.org/pt-br/latest/glossary/#term-Python-Package-Index-PyPI) e de um índice separado, você pode usar a sinalização `--extra-index-url` em seu lugar:

Unix/macOS

python3 -m pip install --extra-index-url http://index.example.com/simple/ SomeProject

Windows

## Atualizando pacotes

pip pode atualizar pacotes no local usando o sinalizador `--upgrade`. Por exemplo, para instalar a versão mais recente de `requests` e todas as suas dependências:

Unix/macOS

python3 -m pip install --upgrade requests

Windows

## Usando um arquivo de requisitos

Em vez de instalar pacotes individualmente, pip permite que você declare todas as dependências em um [Arquivo de Requisitos](https://pip.pypa.io/en/latest/user_guide/#requirements-files "(em pip v26.1)"). Por exemplo, você pode criar um arquivo `requirements.txt` contendo:

requests==2.18.4
google-auth==1.1.0

E diga ao pip para instalar todos os pacotes neste arquivo usando o sinalizador `-r`:

Unix/macOS

python3 -m pip install -r requirements.txt

Windows

## Congelando dependências

Pip pode exportar uma lista de todos os pacotes instalados e suas versões usando o comando `freeze`:

Unix/macOS

python3 -m pip freeze

Windows

O que resultará em uma lista de especificadores de pacote, como:

cachetools==2.0.1
certifi==2017.7.27.1
chardet==3.0.4
google-auth==1.1.1
idna==2.6
pyasn1==0.3.6
pyasn1-modules==0.1.4
requests==2.18.4
rsa==3.4.2
six==1.11.0
urllib3==1.22

O comando `pip freeze` útil para criar [Requirements Files](https://pip.pypa.io/en/latest/user_guide/#requirements-files "(em pip v26.1)") que podem recriar as versões exatas de todos os pacotes instalados em um ambiente.