---
title: Azure Functions Associação de gatilho do serviço Signalr
description: Saiba como enviar mensagens de serviço de sinal de Azure Functions.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763499"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Associação de gatilho de serviço signalr para Azure Functions

Use a associação de gatilho de *sinalização* para responder às mensagens enviadas do serviço de Signaler do Azure. Quando a função é disparada, as mensagens passadas para a função são analisadas como um objeto JSON.

No modo sem servidor do serviço de sinalização, o serviço de sinalização usa o recurso [upstream](../azure-signalr/concept-upstream.md) para enviar mensagens do cliente para o aplicativo de funções. E Aplicativo de funções usa a associação de gatilho de serviço de sinalização para lidar com essas mensagens. A arquitetura geral é mostrada abaixo: :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="arquitetura de gatilho de sinalização":::

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](functions-bindings-signalr-service.md).

## <a name="example"></a>Exemplo

O exemplo a seguir mostra uma função que recebe uma mensagem usando a associação de gatilho e registra a mensagem.

# <a name="c"></a>[C#](#tab/csharp)

A associação de gatilho do serviço signalr para C# tem dois modelos de programação. Modelo baseado em classe e modelo tradicional. O modelo baseado em classe pode fornecer uma experiência consistente de programação do servidor de sinalização. E o modelo tradicional fornece mais flexibilidade e semelhantes a outras associações de função.

### <a name="with-class-based-model"></a>Com o modelo baseado em classe

Consulte [modelo baseado em classe](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) para obter detalhes.

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Com modelo tradicional

O modelo tradicional obedece à Convenção da função do Azure desenvolvida pelo C#. Se você não estiver familiarizado com ele, poderá aprender com os [documentos](./functions-dotnet-class-library.md).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Use o atributo `[SignalRParameter]` para simplificar `ParameterNames`

Como é difícil de usar `ParameterNames` , `SignalRParameter` o é fornecido para alcançar a mesma finalidade.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Este é o código do script C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Aqui está o código Python:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Configuração

### <a name="signalrtrigger"></a>SignalRTrigger

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `SignalRTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**| n/d | Deve ser definido como `SignalRTrigger`.|
|**direction**| n/d | Deve ser definido como `in`.|
|**name**| n/d | Nome da variável usada no código de função para objeto de contexto de invocação de gatilho. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do Hub do Signalr para a função a ser disparada.|
|**category**|**Categoria**| Esse valor deve ser definido como a categoria de mensagens para a função a ser disparada. A categoria pode ser um dos seguintes valores: <ul><li>**conexões**: incluindo eventos *conectados* e *desconectados*</li><li>**mensagens**: incluindo todos os outros eventos, exceto aqueles na categoria *conexões*</li></ul> |
|**event**|**Evento**| Esse valor deve ser definido como o evento de mensagens para a função a ser disparada. Para categoria de *mensagens* , o evento é o *destino* na [mensagem de invocação](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) que os clientes enviam. Para categoria de *conexões* , somente *conectado* e *desconectado* é usado. |
|**parâmetros**|**Parâmetros**| Adicional Uma lista de nomes que se associa aos parâmetros. |
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do SignalR Service (o padrão é "AzureSignalRConnectionString") |

## <a name="payload"></a>Carga útil

O tipo de entrada do gatilho é declarado como `InvocationContext` ou um tipo personalizado. Se você escolher `InvocationContext` , terá acesso completo ao conteúdo da solicitação. Para um tipo personalizado, o runtime tenta analisar o corpo da solicitação JSON para definir as propriedades do objeto.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext contém todo o conteúdo da mensagem enviar do serviço Signalr.

|Propriedade em InvocationContext | Descrição|
|------------------------------|------------|
|Argumentos| Disponível para a categoria de *mensagens* . Contém *argumentos* na [mensagem de invocação](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Erro| Disponível para evento *desconectado* . Ele poderá ficar vazio se a conexão for fechada sem erro ou contiver as mensagens de erro.|
|Hub| O nome do Hub ao qual a mensagem pertence.|
|Categoria| A categoria da mensagem.|
|Evento| O evento da mensagem.|
|ConnectionId| A ID de conexão do cliente que envia a mensagem.|
|UserId| A identidade do usuário do cliente que envia a mensagem.|
|Cabeçalhos| Os cabeçalhos da solicitação.|
|Consulta| A consulta da solicitação quando os clientes se conectam ao serviço.|
|Declarações| As declarações do cliente.|

## <a name="using-parameternames"></a>Usando `ParameterNames`

A propriedade `ParameterNames` no `SignalRTrigger` permite que você vincule argumentos de mensagens de invocação aos parâmetros de funções. O nome que você definiu pode ser usado como parte de [expressões de associação](../azure-functions/functions-bindings-expressions-patterns.md) em outra associação ou como parâmetros em seu código. Isso oferece uma maneira mais conveniente de acessar argumentos do `InvocationContext` .

Digamos que você tenha um cliente de sinalização JavaScript tentando invocar o método `broadcast` no Azure Functions com dois argumentos `message1` , `message2` .

```javascript
await connection.invoke("broadcast", message1, message2);
```

Depois de definido `parameterNames` , o nome que você definiu será, respectivamente, correspondente aos argumentos enviados no lado do cliente. 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

Em seguida, o conterá `arg1` o conteúdo de e conterá `message1` `arg2` o conteúdo de `message2` .


### <a name="remarks"></a>Comentários

Para a associação de parâmetro, a ordem é importante. Se você estiver usando o `ParameterNames` , a ordem em `ParameterNames` corresponde à ordem dos argumentos que você invoca no cliente. Se você estiver usando `[SignalRParameter]` o atributo em C#, a ordem dos argumentos nos métodos de função do Azure corresponderá à ordem dos argumentos nos clientes.

`ParameterNames` e o atributo `[SignalRParameter]` **não pode** ser usado ao mesmo tempo ou você receberá uma exceção.

## <a name="signalr-service-integration"></a>Integração do serviço signalr

O serviço signalr precisa de uma URL para acessar Aplicativo de funções quando você está usando a associação de gatilho do serviço Signalr. A URL deve ser configurada em **configurações de upstream** no lado do serviço signalr. 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="Portal upstream":::

Ao usar o gatilho de serviço Signalr, a URL pode ser simples e formatada, conforme mostrado abaixo:

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

O `Function_App_URL` pode ser encontrado na página de visão geral do aplicativo de funções e o `API_KEY` é gerado pelo Azure function. Você pode obter o `API_KEY` de `signalr_extension` na folha de **chaves do aplicativo** de aplicativo de funções.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="Chave de API":::

Se você quiser usar mais de uma Aplicativo de funções junto com um serviço Signalr, o upstream também poderá oferecer suporte a regras de roteamento complexas. Encontre mais detalhes em [configurações de upstream](../azure-signalr/concept-upstream.md).

## <a name="step-by-step-sample"></a>Exemplo passo a passo

Você pode seguir o exemplo no GitHub para implantar uma sala de chat em Aplicativo de funções com associação de gatilho do serviço Signalr e recurso upstream: [exemplo de sala de chat bidirecional](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>Próximas etapas

* [Desenvolvimento de funções do Azure e a configuração com o serviço do Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Exemplo de associação de gatilho de serviço signalr](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
