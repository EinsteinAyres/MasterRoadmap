

# Escolher entre APIs REST e APIs HTTP
<a name="http-api-vs-rest"></a>

APIs REST e APIs HTTP são produtos da API RESTful. As APIs REST são compatíveis com mais recursos do que as APIs HTTP, enquanto as APIs HTTP são projetadas com recursos mínimos para que possam ser oferecidas por um preço mais baixo. Escolha APIs REST se precisar de recursos como chaves de API, limitação por cliente, validação de solicitações, integração AWS WAF ou endpoints de API privados. Escolha APIs HTTP se você não precisar dos recursos incluídos nas APIs REST.

As seções a seguir resumem os principais recursos que estão disponíveis em APIs HTTP e REST. Quando necessário, links adicionais são fornecidos para navegar entre as seções da API REST e da API HTTP do Guia do desenvolvedor do API Gateway.

## Tipo de endpoint
<a name="http-api-vs-rest.differences.endpoint-type"></a>

O tipo de endpoint refere-se ao endpoint que o API Gateway cria para sua API. Para obter mais informações, consulte [Tipos de endpoint da API para APIs REST no API Gateway](api-gateway-api-endpoint-types.md). 


| Tipos de endpoint | API REST | API HTTP | 
| --- | --- | --- | 
|  [Otimizado para borda](api-gateway-api-endpoint-types.md#api-gateway-api-endpoint-types-edge-optimized)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Regional](api-gateway-api-endpoint-types.md#api-gateway-api-endpoint-types-regional)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Yes (Sim)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Yes (Sim)  | 
|  [Privado](api-gateway-api-endpoint-types.md#api-gateway-api-endpoint-types-private)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 

## Segurança
<a name="http-api-vs-rest.differences.security"></a>

O API Gateway fornece várias maneiras de proteger sua API de determinadas ameaças, como usuários mal-intencionados ou picos de tráfego. Para saber mais, consulte [Proteger as APIs REST no API Gateway](rest-api-protect.md) e [Proteger as APIs HTTP no API Gateway](http-api-protect.md).


| Recursos de segurança | API REST | API HTTP | 
| --- | --- | --- | 
|  [Autenticação TLS mútua](rest-api-mutual-tls.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](rest-api-mutual-tls.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-mutual-tls.md)  | 
|  [Certificados para autenticação de back-end](getting-started-client-side-ssl-authentication.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) Não  | 
|  [AWS WAF](apigateway-control-access-aws-waf.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 

## Autorização
<a name="http-api-vs-rest.differences.authorization"></a>

O API Gateway oferece suporte a vários mecanismos de controle e gerenciamento de acesso à sua API. Para obter mais informações, consulte [Controlar e gerenciar o acesso a APIs REST no API Gateway](apigateway-control-access-to-api.md) e [Controlar e gerenciar o acesso a APIs HTTP no API Gateway](http-api-access-control.md).


| Opções de autorização | API REST | API HTTP | 
| --- | --- | --- | 
|  [IAM](permissions.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](permissions.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-access-control-iam.md)  | 
|  [Políticas de recursos](apigateway-resource-policies.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)   | 
|  [Amazon Cognito](apigateway-integrate-with-cognito.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim 1  | 
|  [Autorização personalizada com uma função do AWS Lambda](apigateway-use-lambda-authorizer.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](apigateway-use-lambda-authorizer.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-lambda-authorizer.md)  | 
|  [JSON Web Token (JWT)](http-api-jwt-authorizer.md) 2  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) Não  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | 

1 É possível usar o Amazon Cognito com um [Autorizador do JWT](http-api-jwt-authorizer.md).

2 É possível usar um [autorizador do Lambda](apigateway-use-lambda-authorizer.md) para validar JWTs para APIs REST.

## Gerenciamento de APIs
<a name="http-api-vs-rest.differences.management"></a>

Escolha APIs REST se precisar de recursos de gerenciamento de API, como chaves de API e limitação de taxa por cliente. Para ter mais informações, consulte [Distribuir as APIs REST para clientes no API Gateway](rest-api-distribute.md), [Nome de domínio personalizado para APIs REST públicas no API Gateway](how-to-custom-domains.md) e [Nomes de domínio personalizados para APIs HTTP no API Gateway](http-api-custom-domain-names.md).


| Recursos | API REST | API HTTP | 
| --- | --- | --- | 
|  [Domínios personalizados](how-to-custom-domains.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](how-to-custom-domains.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-custom-domain-names.md)  | 
|  [Chaves de API](api-gateway-api-usage-plans.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Limitação de taxa por cliente](api-gateway-request-throttling.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Limitação de uso por cliente](api-gateway-api-usage-plans.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Portal do desenvolvedor](apigateway-portals.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 

## Desenvolvimento
<a name="http-api-vs-rest.differences.development"></a>

Ao desenvolver a API do API Gateway, você decidirá uma série de características da API. Essas características dependem do caso de uso da sua API. Para obter mais informações, consulte [Desenvolver APIs REST no API Gateway](rest-api-develop.md) e [Desenvolver APIs HTTP no API Gateway](http-api-develop.md).


| Recursos | API REST | API HTTP | 
| --- | --- | --- | 
|  [Configuração de CORS](how-to-cors.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](how-to-cors.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-cors.md)  | 
|  [Testar invocações](how-to-test-method.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Armazenamento em cache](api-gateway-caching.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Implantações controladas pelo usuário](how-to-deploy-api.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](how-to-deploy-api.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-stages.md)  | 
|  [Implantações automáticas](http-api-stages.md)  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) Não  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | 
|  [Custom gateway responses (Respostas personalizadas do gateway](api-gateway-gatewayResponse-definition.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Implantações da versão canário](canary-release.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Validação da solicitação](api-gateway-method-request-validation.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Solicitar transformação de parâmetros](rest-api-data-transformations.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](rest-api-data-transformations.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-parameter-mapping.md)  | 
|  [Solicitar transformação do corpo](rest-api-data-transformations.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 

## Monitoramento
<a name="http-api-vs-rest.differences.monitoring"></a>

O API Gateway é compatível com várias opções para registrar solicitações de API e monitorar suas APIs. Para obter mais informações, consulte [Monitorar APIs REST no API Gateway](rest-api-monitor.md) e [Monitorar APIs HTTP no API Gateway](http-api-monitor.md).


| Recurso | API REST | API HTTP | 
| --- | --- | --- | 
|  [Métricas do Amazon CloudWatch](monitoring-cloudwatch.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](monitoring-cloudwatch.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-metrics.md)  | 
|  [Logs de acesso ao CloudWatch Logs](set-up-logging.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](set-up-logging.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-logging.md)  | 
|  [Logs de acesso ao Amazon Data Firehose](apigateway-logging-to-kinesis.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Logs de execução](set-up-logging.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [AWS X-Ray Rastreamento do](apigateway-xray.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 

## Integrações
<a name="http-api-vs-rest.differences.integrations"></a>

As integrações conectam sua API do API Gateway aos recursos de back-end. Para obter mais informações, consulte [Integrações para APIs REST no API Gateway](how-to-integration-settings.md) e [Criar integrações para APIs HTTP no API Gateway](http-api-develop-integrations.md).


| Recurso | API REST | API HTTP | 
| --- | --- | --- | 
|  [Endpoints HTTP públicos](setup-http-integrations.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](setup-http-integrations.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-develop-integrations-http.md)  | 
|  [AWS Serviços da](api-gateway-api-integration-types.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](api-gateway-api-integration-types.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-develop-integrations-aws-services.md)  | 
|  [AWS Lambda functions](set-up-lambda-integrations.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](set-up-lambda-integrations.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-develop-integrations-lambda.md)  | 
|  [Integrações privadas com Network Load Balancers](set-up-private-integration.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](set-up-private-integration.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](http-api-develop-integrations-private.md)  | 
|  [Integrações privadas com Application Load Balancers](http-api-develop-integrations-private.md)  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) [Yes (Sim](set-up-private-integration.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Yes (Sim)  | 
|  [Integrações privadas com AWS Cloud Map](http-api-develop-integrations-private.md)  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) Não   |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | 
|  [Integrações simuladas](how-to-mock-integration.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  | 
|  [Streaming de respostas](response-transfer-mode.md)  |  ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/success_icon.svg) Sim  | ![\[alt text not found\]](http://docs.aws.amazon.com/pt_br/apigateway/latest/developerguide/images/negative_icon.svg) No (Não)  |