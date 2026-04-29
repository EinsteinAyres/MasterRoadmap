
# Visão geral das APIs de WebSocket no API Gateway


No API Gateway, é possível criar uma API WebSocket como um frontend com estado para um serviço da AWS (como Lambda ou DynamoDB) ou para um endpoint HTTP. A API WebSocket invoca o backend com base no conteúdo de mensagens que recebe a partir de aplicativos do cliente.

Ao contrário de uma API REST, que recebe e responde a solicitações, uma API WebSocket oferece suporte a comunicações bidirecionais entre aplicativos do cliente e backend. O backend pode enviar mensagens de retorno para clientes conectados.

Na sua API WebSocket, mensagens JSON de entrada são direcionadas para integrações de backend com base nas rotas que você configurar. (Mensagens que não apresentam o formato JSON são direcionadas para a rota `$default` que você configurar).

Uma _rota_ inclui uma _chave de roteamento_, que é o valor esperado quando uma _expressão de seleção de rotas_ é avaliada. O `routeSelectionExpression` é um atributo definido em nível de API. Ele especifica uma propriedade JSON que deve estar presente na carga da mensagem. Para obter mais informações sobre expressões de seleção de rota, consulte [[Expressões de seleção de rota]].

Por exemplo, se as suas mensagens JSON contêm uma propriedade `action` e você deseja realizar ações diferentes de acordo com essa propriedade, sua expressão de seleção de rotas pode ser `${request.body.action}`. A tabela de roteamento deve especificar qual ação executar ao corresponder o valor da propriedade `action` com os valores de chave de rotas personalizada que você definiu na tabela.

## Usar rotas para uma API de WebSocket

Há três rotas predefinidas que podem ser usadas: `$connect`, `$disconnect` e `$default`. Além disso, você pode criar rotas personalizadas.

- O API Gateway chama a rota `$connect` quando uma conexão persistente entre o cliente e uma API WebSocket está sendo iniciada.
    
- O API Gateway chama a rota `$disconnect` quando o cliente ou o servidor é desconectado da API.
    
- O API Gateway chama uma rota personalizada após a avaliação da expressão de seleção de rotas personalizada em relação à mensagem caso uma rota correspondente seja encontrada; a correspondência determina qual integração é invocada.
    
- O API Gateway chamará a rota `$default` se a expressão de seleção de rotas não puder ser avaliada em relação à mensagem ou se nenhuma rota correspondente for encontrada.
    

Para obter mais informações sobre as rotas `$connect` e `$disconnect`, consulte [Gerenciar usuários conectados e aplicações clientes: rotas $connect e $disconnect](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-websocket-api-route-keys-connect-disconnect.html).

Para obter mais informações sobre a rota `$default` e rotas personalizadas, consulte [Invocar sua integração de backend com o $default Route e rotas personalizadas no API Gateway](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-websocket-api-routes-integrations.html).

## Enviar dados a aplicações de cliente conectadas

Os serviços de backend podem enviar dados para aplicativos conectados do cliente. É possível enviar dados fazendo o seguinte:

- Use uma integração para enviar uma resposta, que é exibida ao cliente por uma resposta de rota que você tiver definido.
    
- Você pode usar a API `@connections` para enviar uma solicitação POST: Para obter mais informações, consulte [Usar os comandos @connections em seu serviço de backend](https://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/apigateway-how-to-call-websocket-api-connections.html).
    

## Códigos de status API de WebSocket

As APIs de WebSocket do Gateway da API usam os seguintes códigos de status para comunicação do servidor com o cliente, conforme descrito no [Registro de número de código de fechamento WebSocket](https://www.iana.org/assignments/websocket/websocket.xhtml#close-code-number):

1001

O API do Gateway retorna esse código de status quando o cliente fica inativo por 10 minutos ou atinge a vida útil máxima de conexão de 2 horas.

1003

O Gateway da API retorna esse código de status quando um endpoint recebe um tipo de mídia binária. Tipos de mídia binária não têm suporte para APIs de WebSocket.

1005

O Gateway da API retorna esse código de status se o cliente enviar um quadro de fechamento sem um código de encerramento.

1006

O Gateway da API retorna esse código de status se houver um fechamento inesperado da conexão, como a conexão TCP fechada sem um quadro de fechamento do WebSocket.

1008

O Gateway da API retorna esse código de status quando um endpoint recebe muitas solicitações de um cliente específico.

1009

O Gateway da API retorna esse código de status quando um endpoint recebe uma mensagem grande demais para ser processada.

1011

O Gateway da API retorna esse código de status quando há um erro interno do servidor.

1012

O Gateway da API retorna esse código de status se o serviço for reiniciado.