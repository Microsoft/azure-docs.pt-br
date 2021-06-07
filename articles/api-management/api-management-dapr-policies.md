---
title: Políticas de integração do Dapr de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas de gerenciamento de API do Azure para interagir com extensões de microserviços do Dapr.
author: vladvino
ms.author: vlvinogr
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 051bf4398555f318f613c66d58ec65be1d30e215
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646802"
---
# <a name="api-management-dapr-integration-policies"></a>Políticas de integração do Dapr de gerenciamento de API

Este tópico fornece uma referência para as políticas de gerenciamento de API de integração do Dapr. O Dapr é um tempo de execução portátil para a criação de aplicativos baseados em microserviços com e sem estado com qualquer linguagem ou estrutura. Ele codifica os padrões comuns de microserviço, como descoberta de serviço e invocação com a lógica de repetição de compilação, publicação e assinatura com semântica de entrega pelo menos uma vez ou recursos de ligação conectáveis para facilitar a composição usando serviços externos. Acesse [dapr.Io](https://dapr.io) para obter informações detalhadas e instruções sobre como começar a usar o dapr. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](api-management-howto-policies.md).

> [!CAUTION]
> As políticas referenciadas neste tópico estão em visualização pública e estão sujeitas a [termos de uso complementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> As políticas referenciadas neste tópico só funcionam na [versão hospedada internamente do gateway de gerenciamento de API](self-hosted-gateway-overview.md) com o suporte do Dapr habilitado.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Habilitar o suporte a Dapr no gateway de hospedagem interna

Para ativar o suporte a Dapr no gateway de hospedagem interna, adicione as [anotações Dapr](https://github.com/dapr/docs/blob/master/README.md) abaixo ao [modelo de implantação kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md) substituindo "app-Name" por um nome desejado. A orientação completa da configuração e uso do gerenciamento de API com o Dapr está disponível [aqui](https://aka.ms/apim/dapr/walkthru).
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Políticas de integração do Dapr (Distributed Apps Runtime)

-  [Enviar solicitação para um serviço](api-management-dapr-policies.md#invoke): usa o tempo de execução Dapr para localizar e se comunicar de forma confiável com um microserviço Dapr. Para saber mais sobre a invocação de serviço no Dapr, consulte a descrição neste arquivo [Leiame](https://github.com/dapr/docs/blob/master/README.md#service-invocation) .
-  [Enviar mensagem para o tópico pub/sub](api-management-dapr-policies.md#pubsub): usa o tempo de execução Dapr para publicar uma mensagem em um tópico de publicação/assinatura. Para saber mais sobre as mensagens de publicação/assinatura no Dapr, consulte a descrição neste arquivo [Leiame](https://github.com/dapr/docs/blob/master/README.md) .
-  [Disparar Associação de saída](api-management-dapr-policies.md#bind): usa o tempo de execução Dapr para invocar um sistema externo por meio da Associação de saída. Para saber mais sobre associações no Dapr, consulte a descrição neste arquivo [Leiame](https://github.com/dapr/docs/blob/master/README.md) .

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Enviar solicitação para um serviço

Essa política define a URL de destino para a solicitação atual para `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}` Substituir parâmetros de modelo por valores especificados na instrução de política.

A política pressupõe que Dapr é executado em um contêiner sidecar no mesmo Pod que o gateway. Após o recebimento da solicitação, o tempo de execução do Dapr executa a descoberta de serviço e a invocação real, incluindo a possível conversão de protocolo entre HTTP e gRPC, novas tentativas, rastreamento distribuído e tratamento de erros.

### <a name="policy-statement"></a>Declaração de política

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra como invocar o método chamado "Back" no microserviço chamado "Echo". A `set-backend-service` política define a URL de destino como `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back` . A `forward-request` política despacha a solicitação para o tempo de execução Dapr, que a entrega para o microserviço.

A `forward-request` política é mostrada aqui para fins de clareza. A política é normalmente "herdada" do escopo global por meio da `base` palavra-chave.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento             | Descrição  | Obrigatório |
|---------------------|--------------|----------|
| set-backend-service | Elemento raiz | Sim      |

### <a name="attributes"></a>Atributos

| Atributo        | Descrição                     | Obrigatório | Padrão |
|------------------|---------------------------------|----------|---------|
| backend-id       | Deve ser definido como "dapr"           | Sim      | N/D     |
| dapr-ID do aplicativo      | Nome do microserviço de destino. Usado para formar o parâmetro [AppID](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) em Dapr.| Sim | N/D |
| dapr-método      | Nome do método ou uma URL a ser invocada no microserviço de destino. Mapeia para o parâmetro [Method-Name](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) em Dapr.| Sim | N/D |
| dapr-namespace   | Nome do namespace no qual o microserviço de destino reside. Usado para formar o parâmetro [AppID](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) em Dapr.| Não | N/D |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

- **Seções de política:** de entrada
- **Escopos da política:** todos os escopos

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Enviar mensagem para o tópico pub/sub

Essa política instrui o gateway de gerenciamento de API a enviar uma mensagem para um tópico de publicação/assinatura do Dapr. A política realiza isso fazendo uma solicitação HTTP POST para `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` Substituir parâmetros de modelo e adicionar conteúdo especificado na declaração de política.

A política pressupõe que o tempo de execução Dapr está sendo executado em um contêiner sidecar no mesmo Pod que o gateway. O tempo de execução do Dapr implementa a semântica pub/sub.

### <a name="policy-statement"></a>Declaração de política

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra o envio do corpo da solicitação atual para o [tópico](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) "novo" do [componente](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)pub/sub "Orders". A resposta recebida do tempo de execução Dapr é armazenada na entrada "Dapr-Response" da coleção Variables no objeto [Context](api-management-policy-expressions.md#ContextVariables) .

Se Dapr Runtime não conseguir localizar o tópico de destino, por exemplo, e responder com um erro, a seção "On-Error" será disparada. A resposta recebida do tempo de execução Dapr é retornada para o chamador textualmente. Caso contrário, a `200 OK` resposta padrão será retornada.

A seção "back-end" está vazia e a solicitação não é encaminhada para o back-end.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento             | Descrição  | Obrigatório |
|---------------------|--------------|----------|
| publicar em dapr     | Elemento raiz | Sim      |

### <a name="attributes"></a>Atributos

| Atributo        | Descrição                     | Obrigatório | Padrão |
|------------------|---------------------------------|----------|---------|
| PubSub-nome      | O nome do componente PubSub de destino. Mapeia para o parâmetro [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) em Dapr. Se não estiver presente, o valor do atributo __topic__ deverá estar no formato de `pubsub-name/topic-name` .    | Não       | Nenhum    |
| topic            | O nome do tópico. Mapeia para o parâmetro [topic](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) no Dapr.               | Sim      | N/D     |
| ignore-error     | Se definido como `true` instruir a política para não disparar a seção ["On-Error"](api-management-error-handling-policies.md) após receber o erro do tempo de execução do Dapr | Não | `false` |
| response-variable-name | Nome da entrada de coleção de [variáveis](api-management-policy-expressions.md#ContextVariables) a ser usada para armazenar a resposta do tempo de execução Dapr | Não | Nenhum |
| tempo limite | Tempo (em segundos) para aguardar o tempo de execução do Dapr responder. Pode variar de 1 a 240 segundos. | Não | 5 |
| template | O mecanismo de modelagem a ser usado para transformar o conteúdo da mensagem. "Liquid" é o único valor com suporte. | Não | Nenhum |
| content-type | Tipo do conteúdo da mensagem. "Application/JSON" é o único valor com suporte. | Não | Nenhum |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

- **Seções de política:** de entrada, de saída, em caso de erro
- **Escopos da política:** todos os escopos

## <a name="trigger-output-binding"></a><a name="bind"></a> Disparar Associação de saída

Essa política instrui o gateway de gerenciamento de API a disparar uma [Associação](https://github.com/dapr/docs/blob/master/README.md)Dapr de saída. A política realiza isso fazendo uma solicitação HTTP POST para substituir o `http://localhost:3500/v1.0/bindings/{{bind-name}}` parâmetro de modelo e adicionando o conteúdo especificado na declaração de política.

A política pressupõe que o tempo de execução Dapr está sendo executado em um contêiner sidecar no mesmo Pod que o gateway. O tempo de execução Dapr é responsável por invocar o recurso externo representado pela associação.

### <a name="policy-statement"></a>Declaração de política

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra o disparo da Associação de saída denominada "sistemas externos" com o nome da operação "Create", metadados que consistem em dois itens de chave/valor denominados "origem" e "cliente-IP" e o corpo proveniente da solicitação original. A resposta recebida do tempo de execução Dapr é capturada na entrada "bind-Response" da coleção Variables no objeto [Context](api-management-policy-expressions.md#ContextVariables) .

Se o tempo de execução do Dapr falhar por algum motivo e responder com um erro, a seção "no erro" será disparada e a resposta recebida do tempo de execução do Dapr será retornada ao chamador de forma idêntica. Caso contrário, a `200 OK` resposta padrão será retornada.

A seção "back-end" está vazia e a solicitação não é encaminhada para o back-end.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento             | Descrição  | Obrigatório |
|---------------------|--------------|----------|
| Invoke-dapr-Binding | Elemento raiz | Sim      |
| metadata            | Associação de metadados específicos na forma de pares de chave/valor. Mapeia para a propriedade de [metadados](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) em Dapr. | Não |
| data            | O conteúdo da mensagem. Mapeia para a propriedade [Data](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) em Dapr. | Não |


### <a name="attributes"></a>Atributos

| Atributo        | Descrição                     | Obrigatório | Padrão |
|------------------|---------------------------------|----------|---------|
| name            | Nome da Associação de destino. Deve corresponder ao nome das associações [definidas](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) em Dapr.           | Sim      | N/D     |
| operation       | Nome da operação de destino (específico de associação). Mapeia para a propriedade [Operation](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) em Dapr. | Não | Nenhum |
| ignore-error     | Se definido como `true` instruir a política para não disparar a seção ["On-Error"](api-management-error-handling-policies.md) após receber o erro do tempo de execução do Dapr | Não | `false` |
| response-variable-name | Nome da entrada de coleção de [variáveis](api-management-policy-expressions.md#ContextVariables) a ser usada para armazenar a resposta do tempo de execução Dapr | Não | Nenhum |
| tempo limite | Tempo (em segundos) para aguardar o tempo de execução do Dapr responder. Pode variar de 1 a 240 segundos. | Não | 5 |
| template | O mecanismo de modelagem a ser usado para transformar o conteúdo da mensagem. "Liquid" é o único valor com suporte. | Não | Nenhum |
| content-type | Tipo do conteúdo da mensagem. "Application/JSON" é o único valor com suporte. | Não | Nenhum |

### <a name="usage"></a>Uso

Essa política pode ser usada nas [seções](./api-management-howto-policies.md#sections) e nos [escopos](./api-management-howto-policies.md#scopes) da política a seguir.

- **Seções de política:** de entrada, de saída, em caso de erro
- **Escopos da política:** todos os escopos
