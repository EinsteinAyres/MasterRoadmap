

  

# Criar rotas para APIs de WebSocket no API Gateway
<a name="websocket-api-develop-routes"></a>

Na sua API WebSocket, mensagens JSON de entrada são direcionadas para integrações de backend com base nas rotas que você configurar. (Mensagens que não apresentam o formato JSON são direcionadas para a rota `$default` que você configurar).

Uma *rota* inclui uma *chave de roteamento*, que é o valor esperado quando uma *expressão de seleção de rotas* é avaliada. O `routeSelectionExpression` é um atributo definido em nível de API. Ele especifica uma propriedade JSON que deve estar presente na carga da mensagem. Para obter mais informações sobre expressões de seleção de rota, consulte [Expressões de seleção de rota](#apigateway-websocket-api-route-selection-expressions).

Por exemplo, se as suas mensagens JSON contiverem uma propriedade `action` e você desejar executar ações diferentes de acordo com essa propriedade, sua expressão de seleção de rotas poderá ser `${request.body.action}`. A tabela de roteamento deve especificar qual ação executar ao corresponder o valor da propriedade `action` com os valores de chave de rotas personalizada que você definiu na tabela.

Há três rotas predefinidas que podem ser usadas: `$connect`, `$disconnect` e `$default`. Além disso, você pode criar rotas personalizadas.
+ O API Gateway chama a rota `$connect` quando uma conexão persistente entre o cliente e uma API WebSocket está sendo iniciada.
+ O API Gateway chama a rota `$disconnect` quando o cliente ou o servidor é desconectado da API.
+ O API Gateway chama uma rota personalizada após a avaliação da expressão de seleção de rotas personalizada em relação à mensagem caso uma rota correspondente seja encontrada; a correspondência determina qual integração é invocada.
+ O API Gateway chamará a rota `$default` se a expressão de seleção de rotas não puder ser avaliada em relação à mensagem ou se nenhuma rota correspondente for encontrada.

## Expressões de seleção de rota
<a name="apigateway-websocket-api-route-selection-expressions"></a>

Uma *expressão de seleção de rota* é avaliada quando o serviço está selecionando a rota para seguir para uma mensagem recebida. O serviço usa a rota cuja `routeKey` corresponde exatamente ao valor avaliado. Se não existir nenhuma correspondência e uma rota com a chave `$default` existir, essa rota será selecionada. Se nenhuma rota corresponder ao valor avaliado e não existir uma rota `$default`, o serviço retornará uma mensagem de erro. Para APIs com base em WebSocket, a expressão deve ser da forma `$request.body.{path_to_body_element}`.

Por exemplo, suponha que você está enviando a seguinte mensagem JSON:

```
{
    "service" : "chat",
    "action" : "join",
    "data" : {
        "room" : "room1234"
   }
}
```

Você pode selecionar o comportamento da API com base na propriedade `action`. Nesse caso, você pode definir a seguinte expressão de seleção de rotas:

```
$request.body.action
```

Neste exemplo, `request.body` refere-se à carga JSON da sua mensagem, e `.action` é uma expressão [JSONPath](https://goessner.net/articles/JsonPath/). Você pode usar qualquer expressão de caminho JSON após `request.body`, mas lembre-se de que o resultado será transformado em string. Por exemplo, se a expressão JSONPath retorna uma matriz de dois elementos, que serão apresentadas como a string `"[item1, item2]"`. Por esse motivo, é uma boa prática ter sua expressão avaliada como um valor e não uma matriz ou um objeto.

Você pode simplesmente usar um valor estático, ou utilizar várias variáveis. A tabela a seguir mostra exemplos e seus resultados avaliados em relação à carga anterior.


| Expressão | Resultado avaliado | Descrição | 
| --- | --- | --- | 
| \$1request.body.action | join | Uma variável desempacotada | 
| \$1\$1request.body.action\$1 | join | Uma variável empacotada | 
| \$1\$1request.body.service\$1/\$1\$1request.body.action\$1 | chat/join | Várias variáveis com valores estáticos | 
| \$1\$1request.body.action\$1-\$1\$1request.body.invalidPath\$1  | join- | Se o JSONPath não for encontrado, a variável será resolvida como "". | 
| action | action | Valor estático | 
| \$1\$1default | \$1default | Valor estático | 

O resultado avaliado é usado para encontrar uma rota. Se houver uma rota com uma chave de rota correspondente, a rota será selecionada para processar a mensagem. Se nenhuma rota correspondente for encontrada, o API Gateway tentará encontrar a rota `$default`, se disponível. Se a rota `$default` não estiver definida, o API Gateway retornará um erro.

## Configurar rotas para uma API WebSocket no API Gateway
<a name="apigateway-websocket-api-routes"></a>

Ao criar uma nova API WebSocket pela primeira vez, há três rotas predefinidas: `$connect`, `$disconnect` e `$default`. É possível criá-las ao utilizar o console, a API ou a AWS CLI. Se desejado, você pode criar rotas personalizadas. Para obter mais informações, consulte [Visão geral das APIs de WebSocket no API Gateway](apigateway-websocket-api-overview.md).

**nota**  
Na CLI, você pode criar rotas antes ou depois de gerar integrações, sendo possível reutilizar a mesma integração para várias rotas.

### Criar uma rota usando o console do API Gateway
<a name="apigateway-websocket-api-route-using-console"></a>

**Como criar uma rota usando o console do API Gateway**

1. Inicie uma sessão no console do API Gateway, escolha a API e selecione **Routes (Rotas)**.

2. Selecione **Criar rota**.

3. Em **Chave de rota**, insira o nome da chave de rota. É possível criar as rotas predefinidas (`$connect`, `$disconnect` e`$default`) ou uma rota personalizada.
**nota**  
Ao criar uma rota personalizada, não use o prefixo `$` no nome da chave de rota. Este prefixo está reservado para rotas predefinidas.

2. Selecione e configure o tipo de integração para a rota. Para obter mais informações, consulte [Configurar uma solicitação de integração de API WebSocket usando o console do API Gateway](apigateway-websocket-api-integration-requests.md#apigateway-websocket-api-integration-request-using-console).

### Criar uma rota usando a AWS CLI
<a name="apigateway-websocket-api-route-using-awscli"></a>

O comando [create-route](https://docs.aws.amazon.com/cli/latest/reference/apigatewayv2/create-route.html) indicado abaixo cria uma rota:

```
aws apigatewayv2 --region us-east-1 create-route --api-id aabbccddee --route-key $default
```

A saída será exibida da seguinte forma:

```
{
    "ApiKeyRequired": false,
    "AuthorizationType": "NONE",
    "RouteKey": "$default",
    "RouteId": "1122334"
}
```

### Especificar as configurações de solicitação de rota para `$connect`
<a name="apigateway-websocket-api-route-request-connect"></a>

Quando você configurar a rota `$connect` para a sua API, as configurações opcionais a seguir serão disponibilizadas para habilitar a autorização para sua API. Para obter mais informações, consulte [A rota `$connect`](apigateway-websocket-api-route-keys-connect-disconnect.md#apigateway-websocket-api-routes-about-connect).
+ **Autorização**: Se a autorização não for necessária, você pode especificar `NONE`. Caso contrário, especifique: 
  + `AWS_IAM` para utilizar políticas padrão do AWS IAM para controlar o acesso à sua API. 
  + `CUSTOM` para implementar a autorização para uma API especificando uma função de autorizador do Lambda criada anteriormente. O autorizador pode residir em sua própria conta da AWS ou em outra conta da AWS. Para obter mais informações sobre autorizadores do Lambda, consulte [Usar os autorizadores do API Gateway Lambda](apigateway-use-lambda-authorizer.md).
**nota**  
No console do API Gateway, a configuração `CUSTOM` é visível somente após a configuração de uma função do autorizador, conforme descrito em [Configurar um autorizador do Lambda (console)](configure-api-gateway-lambda-authorization.md#configure-api-gateway-lambda-authorization-with-console).
**Importante**  
A configuração de **Autorização** é aplicada à API completa, e não apenas à rota `$connect`. A rota `$connect` protege as outras rotas, visto que é chamada em cada conexão.
+ **Chave de API obrigatória**: opcionalmente, você pode exigir uma chave de API para uma rota `$connect` da API. Você pode usar chaves da API com planos de uso para controlar e rastrear o acesso às APIs. Para obter mais informações, consulte [Usar planos e chaves de API para APIs REST no APIs Gateway](api-gateway-api-usage-plans.md).

### Configurar a solicitação de rota `$connect` usando o console do API Gateway
<a name="apigateway-websocket-api-connect-route-request-using-console"></a>

Para configurar uma solicitação de rota `$connect` para uma API WebSocket usando o console do API Gateway:

1. Inicie uma sessão no console do API Gateway, escolha a API e selecione **Routes (Rotas)**.

2. Em **Rotas**, selecione `$connect` ou crie uma rota `$connect` seguindo [[Criar uma rota usando o console do API Gateway]].

3. Na seção **Configurações de solicitação de rota**, selecione **Editar**.

4. Em **Autorização**, selecione um tipo de autorizador.

5. Para exigir uma API para a rota `$connect`, selecione **Exigir chave de API**.

6. Escolha **Salvar alterações**.