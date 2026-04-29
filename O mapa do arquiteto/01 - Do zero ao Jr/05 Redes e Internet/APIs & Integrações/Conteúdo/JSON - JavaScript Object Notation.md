
# O que é JSON?

Jeffrey Erickson · Estrategista de Conteúdo · 4 de abril de 2024

No desenvolvimento de aplicações web e mobile, um formato de dados versátil reina absoluto: a Notação de Objetos JavaScript, mais conhecida como JSON. O JSON é um formato leve de intercâmbio de dados que oferece uma maneira padronizada e eficiente para diferentes sistemas trocarem informações. Graças à sua simplicidade, flexibilidade e compatibilidade com linguagens de programação populares, o JSON se tornou uma tecnologia fundamental para a construção de aplicações web e conta com o apoio ativo de uma comunidade de desenvolvedores.

Eis o que você precisa saber sobre JSON.

## O que é JSON (JavaScript Object Notation)?

JSON (JavaScript Object Notation) é um formato baseado em texto para armazenar e trocar dados de forma legível tanto para humanos quanto para máquinas. Como resultado, o JSON é relativamente fácil de aprender e de solucionar problemas. Embora o JSON tenha suas raízes no JavaScript, ele evoluiu para um formato de dados muito poderoso que simplifica a troca de dados entre diversas plataformas e linguagens de programação. Se você trabalha com desenvolvimento web, análise de dados ou engenharia de software, o JSON é um formato de dados importante para se compreender.

### Principais conclusões

- JSON é um formato de dados popular, frequentemente usado por desenvolvedores web para transferir dados entre um servidor e uma aplicação web.
- Como o JSON é baseado em texto, ele é facilmente lido por humanos e compreendido por computadores.
- A natureza independente de linguagem do JSON o torna um formato ideal para a troca de dados entre diferentes linguagens de programação e plataformas.
- Surgiram muitos bancos de dados para armazenar e trocar dados em formato JSON.

## JSON explicado

JSON é um formato de dados comumente usado por desenvolvedores web para transferir dados entre um servidor e uma aplicação web. Os desenvolvedores geralmente preferem JSON porque ele simplifica a troca de dados entre diferentes tecnologias. Por exemplo, quando um usuário interage com uma aplicação web para fazer uma compra, a aplicação envia a entrada do usuário para o servidor em formato JSON. O servidor processa os dados e envia uma resposta, também em formato JSON, que é então renderizada pela aplicação web. Isso permite uma troca de dados perfeita entre o cliente e o servidor, facilitando experiências web rápidas, dinâmicas e interativas.

## Por que o JSON é usado?

A natureza independente de linguagem do JSON o torna um formato ideal para a troca de dados entre diferentes linguagens de programação e plataformas. Por exemplo, um aplicativo escrito em Java pode facilmente enviar dados JSON para um aplicativo em Python. Ou um aplicativo móvel escrito em JavaScript pode usar JSON para se comunicar com um servidor backend escrito em PHP. Por quê? Porque ambos os sistemas podem analisar e gerar JSON.

Além do desenvolvimento web, o JSON é frequentemente usado em aplicações ou sistemas de TI para armazenar e gerenciar configurações. Por exemplo, arquivos de configuração em formato JSON podem conter informações essenciais, como detalhes de conexão com o banco de dados, chaves de API ou preferências do usuário. Ao armazenar dados de configuração em arquivos JSON simples, fáceis de ler e analisar, os desenvolvedores podem modificar as configurações da aplicação sem precisar alterar o código.

## Por que o JSON é popular entre os desenvolvedores?

O JSON é popular entre os desenvolvedores por ser um formato flexível para troca de dados, com amplo suporte em linguagens de programação e sistemas de software modernos. É baseado em texto, leve e possui um formato de dados fácil de analisar, o que significa que não requer código adicional para entender e interpretar os dados fornecidos.

O JSON ganhou força na programação de APIs e serviços web por proporcionar uma troca de dados mais rápida e resultados mais eficientes. Além disso, os desenvolvedores têm fácil acesso a bancos de dados de documentos NoSQL de código aberto, como o MongoDB, que armazenam dados em formato JSON e não exigem processamento adicional na troca de informações. [[Bancos de dados relacionais]] populares agora suportam JSON nativamente, ampliando ainda mais as possibilidades de aplicação e aproveitando seus benefícios.

## JSON vs. HTML vs. XML

Existem diversos formatos para armazenar e transmitir dados na web. Três opções populares são JSON, XML e HTML. JSON e XML são formatos usados ​​para armazenar e transmitir dados, e cada um possui vantagens diferentes. HTML é uma linguagem usada para criar a estrutura de uma página web e é frequentemente utilizada em conjunto com esses formatos de armazenamento de dados.

### Principais diferenças

- JSON (JavaScript Object Notation) é comumente usado para armazenamento e transferência de dados. O JSON é uma escolha popular para aplicações que se beneficiam de um formato de dados simples e fácil de usar.
- XML (Extensible Markup Language) é uma linguagem de marcação de propósito geral semelhante ao JSON, que permite estruturas de dados mais complexas.
- HTML (Hypertext Markup Language) é usado para criar a estrutura e o conteúdo de páginas da web. Você frequentemente o verá sendo usado com outras linguagens, como CSS (Cascading Style Sheets) e JavaScript, para unificar o estilo de um site e adicionar interatividade às suas páginas.

## Tipos de dados JSON

No contexto de desenvolvimento, tipos de dados são os diferentes tipos de valores que podem ser armazenados e manipulados em uma linguagem de programação. Cada tipo de dado possui seu próprio conjunto de atributos e comportamentos. O JSON suporta diversos tipos de dados, incluindo os seguintes:

1. **Objetos** **.**  Um objeto JSON é um conjunto de pares nome-valor inseridos entre chaves {}. As chaves devem ser strings, separadas por vírgula e devem ser únicas.
2. **Matrizes** . Um tipo de dado matriz é uma coleção ordenada de valores. Em JSON, os valores de matriz devem ser do tipo string, number, object, array, Boolean ou null.
3. **Cadeias de caracteres** **.**  Em JSON, as cadeias de caracteres são delimitadas por aspas duplas, podem conter qualquer caractere Unicode e são comumente usadas para armazenar e transmitir dados baseados em texto, como nomes, endereços ou descrições.
4. **Valores booleanos**  são designados como verdadeiro ou falso. Valores booleanos não são delimitados por aspas e são tratados como valores de texto **.**
5. **Nulo** **.**  Nulo representa um valor que foi intencionalmente deixado vazio. Quando nenhum valor é atribuído a uma chave, ela pode ser tratada como nula.
6. **Números** **.**  Os números são usados ​​para armazenar valores numéricos para diversos fins, como cálculos, comparações ou análise de dados. O JSON suporta números positivos e negativos, bem como casas decimais. Um número JSON segue o formato de ponto flutuante de dupla precisão do JavaScript.

**Exemplo JSON**

O JSON funciona representando dados de forma hierárquica, usando pares de chave-valor para armazenar informações. Os dados JSON são delimitados por chaves ({}), com cada par de chave-valor separado por uma vírgula (,). Por exemplo, o JSON a seguir representa as informações de contato de uma pessoa:

``` { "name": "Jane Smith", "age": 35, "city": "San Francisco", "phone": "014158889275", "email": "janesmith@sample.com" } ```

  

Neste exemplo, "nome", "idade", "cidade", "telefone" e "e-mail" são as chaves, e "Jane Smith", "35", "San Francisco", "014158889275" e "janesmith@sample.com" são os valores correspondentes.

## Os 5 principais casos de uso para JSON

O JSON é popular e amplamente utilizado por desenvolvedores, incluindo aqueles que trabalham com tecnologias como MERN, que engloba MongoDB, Express, React e Node.js, e MEAN, que substitui o React pelo Angular.

1. **Transferência de dados entre sistemas.** O JSON é ideal para transferir dados entre diferentes sistemas e linguagens de programação. Por exemplo, imagine que o banco de dados de um site contenha o endereço postal de um cliente, mas esse endereço precise ser verificado por meio de uma API para garantir sua validade. A empresa pode enviar os dados do endereço no formato JSON em que já estão armazenados diretamente para a API do serviço de validação de endereços.
2. **Geração de um objeto JSON a partir de dados gerados pelo usuário.** O JSON é ideal para armazenar dados temporários. Por exemplo, dados temporários podem ser gerados pelo usuário, como um formulário enviado em um site. O JSON também pode ser usado para serialização de dados.
3. **Configuração de dados para aplicações.** Ao desenvolver aplicações, cada uma precisa das credenciais para se conectar a um banco de dados, bem como do caminho do arquivo de log. As credenciais e o caminho do arquivo de log podem ser especificados em um arquivo JSON para serem facilmente lidos e utilizados por todos os sistemas envolvidos.
4. **Simplificando modelos de dados complexos.** O JSON simplifica documentos complexos, reduzindo-os aos componentes considerados relevantes, e converte o processo de extração de dados em um arquivo JSON previsível e legível por humanos.
5. **Arquivos de configuração e armazenamento de dados.** O JSON permite fácil manipulação e recuperação de dados. Especificamente, ele suporta estruturas aninhadas, o que facilita o armazenamento de dados complexos e hierárquicos. O JSON também suporta arrays, tornando-o adequado para armazenar múltiplas instâncias de dados semelhantes.

## O que é um banco de dados de documentos JSON?

A popularidade do JSON entre os desenvolvedores gerou uma série de bancos de dados altamente capazes, dedicados a esse formato de dados, incluindo bancos de dados [SQL](https://www.oracle.com/autonomous-database/autonomous-json-database/) e [NoSQL](https://www.oracle.com/database/nosql/what-is-nosql/) .

Bancos de dados NoSQL armazenam dados diretamente em formato JSON, sem necessidade de processamento adicional. Bancos de dados NoSQL populares, como MongoDB, Redis e Couchbase, também suportam aninhamento, referências a objetos e arrays, o que facilita a manutenção de um banco de dados JSON. Nos últimos anos, esses bancos de dados NoSQL evoluíram para oferecer vantagens como esquemas flexíveis e melhor escalabilidade e desempenho. Com seu suporte a estruturas de dados flexíveis e dinâmicas, esses bancos de dados se destacam no armazenamento de dados semiestruturados, como documentos de texto, imagens ou feeds de mídias sociais.

Bancos de dados SQL amplamente utilizados, como [o Oracle Database](https://www.oracle.com/autonomous-database/autonomous-json-database/) , agora oferecem JSON como um tipo de dados, permitindo que os desenvolvedores trabalhem com JSON sem precisar adicionar um banco de dados JSON especializado aos seus projetos. Isso proporciona às equipes de desenvolvimento os benefícios já consagrados do SQL, além da capacidade de trabalhar com outros tipos de dados em um [único banco de dados](https://blogs.oracle.com/database/post/what-is-a-converged-database) , incluindo dados gráficos, espaciais, REST, blockchain e relacionais.

## Comece a usar o Oracle Autonomous JSON Database gratuitamente.

Para quem busca utilizar JSON para gerenciar seus dados, [o Oracle Autonomous JSON Database](https://www.oracle.com/autonomous-database/autonomous-json-database/) é uma excelente opção. Trata-se de um serviço de banco de dados de documentos completo e baseado em nuvem, que simplifica o desenvolvimento de aplicações centradas em JSON. O Oracle Autonomous JSON Database também oferece uma ampla gama de recursos sofisticados de banco de dados, incluindo APIs de documentos no estilo NoSQL via Oracle SODA e Oracle Database API for MongoDB, escalabilidade sem servidor, transações ACID de alto desempenho e segurança abrangente, com preços acessíveis por uso.

A Oracle também oferece um serviço de banco de dados fácil de usar que automatiza o gerenciamento de bancos de dados, incluindo recursos para provisionamento, configuração, ajuste, escalonamento, aplicação de patches, criptografia e reparo de bancos de dados. Você pode começar a usar o Oracle Autonomous JSON Database gratuitamente e aproveitar muitos [recursos úteis que não estão disponíveis no MongoDB Atlas](https://www.oracle.com/autonomous-database/autonomous-json-database/oracle-json-vs-mongodb-atlas/) .

O JSON tornou-se um formato de dados simples, versátil e onipresente em diversos domínios, incluindo desenvolvimento web, troca de dados, gerenciamento de configuração e transmissão de dados. Sua ampla adoção e suporte entre as linguagens de programação mais populares solidificaram a posição do JSON como um pilar da comunicação e troca de dados modernas.

## Perguntas frequentes sobre JSON

   

### JSON é um arquivo ou um código?

JSON não é um arquivo nem um código. Em vez disso, é um formato simples usado para armazenar e transportar dados. É um formato de texto simples, o que permite a fácil troca de dados entre diferentes linguagens de programação. O JSON é frequentemente usado para enviar dados entre aplicações web e servidores.

### JSON é uma linguagem de programação?

JSON não é uma linguagem de programação. É, na verdade, um formato leve para intercâmbio de dados. Embora tenha sido derivado do JavaScript, o JSON em si não suporta funções da mesma forma que uma linguagem de programação propriamente dita. O JSON é simplesmente usado para armazenar e transmitir dados entre um servidor e uma aplicação web ou entre sistemas diferentes.

### JSON é melhor que XML?

Embora JSON e XML sejam ambos usados ​​por desenvolvedores para armazenar e transferir dados entre sistemas, eles geralmente são utilizados em circunstâncias diferentes. XML (Extensible Markup Language) é uma linguagem de marcação de propósito geral que permite a criação de estruturas de dados complexas e hierárquicas, enquanto a natureza leve e compacta do JSON o torna uma escolha melhor para transmitir dados em redes — especialmente em aplicações onde a largura de banda é limitada ou a velocidade de transmissão de dados é crítica.

---
[JSON - JavaScript Object Notation](https://www.oracle.com/database/what-is-json/)