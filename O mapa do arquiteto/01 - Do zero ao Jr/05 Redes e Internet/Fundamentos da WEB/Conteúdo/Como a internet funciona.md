
### 1. A distinção essencial: Internet ≠ Web

Antes de qualquer coisa, separe os conceitos. A maioria das pessoas usa "Internet" e "Web" como sinônimos. Estão erradas.

**Internet** é a infraestrutura — o meio físico e os protocolos que conectam bilhões de computadores. Cabos submarinos, roteadores, endereços IP, protocolos de transmissão. É a ferrovia.

**Web** (World Wide Web) é um serviço que roda sobre essa infraestrutura. É um dos muitos trens que trafegam sobre os trilhos. E-mail, IRC, VoIP, streaming de vídeo — são outros serviços, todos usando a mesma Internet, mas não são a Web.

A confusão é compreensível: você abre um navegador, digita um domínio, e algo aparece. Por trás disso, porém, há uma pilha de camadas que este artigo vai expor.

---

### 2. O bloco fundamental: duas máquinas se falando

No núcleo, uma rede é simples: dois computadores conectados, trocando dados. A conexão pode ser física (cabo Ethernet) ou sem fio (WiFi, Bluetooth). O princípio é o mesmo: um canal de comunicação entre dois pontos.

O problema começa quando você adiciona um terceiro computador. E um quarto. E um décimo.

Para conectar 10 computadores diretamente uns aos outros, você precisaria de 45 cabos — 9 conexões por máquina. Isso é inviável. A solução é um dispositivo central que concentra as conexões.

---

### 3. O roteador: o multiplicador de conexões

Em vez de conectar cada computador a todos os outros, você conecta cada computador a um único dispositivo: o **roteador**. O roteador recebe mensagens de qualquer máquina da rede e as encaminha para o destinatário correto.

Agora, para adicionar um computador novo, você precisa de apenas um cabo — do computador ao roteador. A rede escala linearmente, não exponencialmente.

E roteadores podem ser conectados a outros roteadores. Conecte o roteador da sua casa ao roteador do seu vizinho, e as duas redes se fundem. Continue fazendo isso recursivamente e você obtém uma rede de alcance planetário.

---

### 4. A peça que faltava: a infraestrutura telefônica

Construir uma rede global do zero, cabendoando continentes, é economicamente impossível para qualquer organização. Mas já existe uma rede que chega a quase todos os lugares: a rede telefônica.

Para usar essa infraestrutura, você precisa de um dispositivo que traduza os sinais da sua rede digital para o formato que a rede telefônica entende, e vice-versa. Esse dispositivo é o **modem** (modulador/demodulador).

O modem conecta sua rede local à linha telefônica. Do outro lado, em algum lugar, está o **Provedor de Serviço de Internet** (ISP) — uma empresa que opera roteadores de grande porte, interconectados com roteadores de outros ISPs ao redor do mundo.

O fluxo de uma mensagem, então, é:

text

Sua rede → Modem → Infraestrutura telefônica → ISP → ISP → ... → ISP de destino → Destino

A Internet é exatamente isso: uma rede de redes, onde ISPs trocam tráfego entre si através de acordos de peering e pontos de troca de tráfego (IXPs). Não há um centro de controle. É uma malha descentralizada.

---

### 5. Encontrando um computador específico: endereços IP

Em uma rede com bilhões de dispositivos, como especificar qual deles é o destinatário? Cada dispositivo conectado recebe um **endereço IP** (Internet Protocol address).

O formato mais comum (IPv4) é quatro números de 0 a 255, separados por pontos: `192.168.2.10`. Juntos, esses 4 bytes fornecem cerca de 4,3 bilhões de endereços possíveis. (O IPv6, com 128 bits, expande isso para 340 undecilhões — suficiente para cada átomo na Terra ter seu próprio IP, mas essa é outra história.)

O IP é o "CEP" do computador. Máquinas lidam com ele naturalmente. Humanos, nem tanto.

---

### 6. Nomes de domínio: o atalho para humanos

Memorizar `142.250.190.78` é desconfortável. Memorizar `google.com` é trivial.

O **DNS** (Domain Name System) é o catálogo telefônico da Internet. Ele traduz nomes de domínio em endereços IP. Quando você digita `google.com` no navegador, seu computador consulta um servidor DNS, recebe o IP correspondente, e usa esse IP para estabelecer a conexão.

O nome de domínio é um apelido. O endereço real é o IP. Um pode mudar sem que o outro mude — `google.com` permanece o mesmo, mas o IP para onde ele aponta pode ser alterado pela Google a qualquer momento.

---

### 7. Intranets e Extranets: a Internet, mas privada

Os mesmos protocolos e a mesma infraestrutura da Internet podem ser usados para construir redes privadas.

**Intranet** é uma rede restrita aos membros de uma organização. Usa os mesmos protocolos da Internet (TCP/IP, HTTP), mas o acesso é limitado — por firewall, por rede física isolada, ou por autenticação. Serve para compartilhar documentos, wikis corporativos, portais internos.

**Extranet** é uma intranet que abre parte de seu acesso para parceiros externos — fornecedores, clientes. A estrutura é a mesma; a diferença é o perímetro de autorização.

Ambas funcionam sobre os mesmos protocolos da Internet pública. A separação é de acesso, não de tecnologia.

---

### 8. A hierarquia em uma frase

A Internet é a infraestrutura física e protocolar que conecta bilhões de dispositivos. A Web é um serviço que roda sobre ela, entregando páginas hipertexto via HTTP. Intranets e extranets são recortes privados dessa mesma infraestrutura, usando os mesmos protocolos, com acesso controlado.

Entender essa hierarquia — infraestrutura, protocolo, serviço — é o que separa saber usar a Internet de entender o que ela é.






---
[Como a Internet funciona](https://developer.mozilla.org/pt-BR/docs/Learn_web_development/Howto/Web_mechanics/How_does_the_Internet_work)

