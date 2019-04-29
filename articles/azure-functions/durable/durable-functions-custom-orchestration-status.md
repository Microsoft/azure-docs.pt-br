---
title: Status de orquestração personalizada em Funções Duráveis – Azure
description: Saiba como configurar e usar o status de orquestração personalizado para Funções Duráveis.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 12/25/2018
ms.author: v-junlch
ms.openlocfilehash: 8d36c797e80702302a1954d2f00e1e4daabcaa88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709993"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Status de orquestração personalizado em Funções Duráveis (Azure Functions)

O status de orquestração personalizado permite que você defina um valor de status personalizado para a função do orquestrador. Esse status é fornecido por meio da API HTTP GetStatus ou da API `DurableOrchestrationClient.GetStatusAsync`.

## <a name="sample-use-cases"></a>Casos de uso de exemplo

### <a name="visualize-progress"></a>Visualizar progresso

Os clientes podem sondar o ponto de extremidade de status e exibir uma interface do usuário de progresso que visualiza o estágio de execução atual. O exemplo a seguir demonstra o compartilhamento de progresso:

#### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  var outputs = new List<string>();

  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
  context.SetCustomStatus("Tokyo");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
  context.SetCustomStatus("Seattle");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
  context.SetCustomStatus("London");

  // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
  return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
  return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const outputs = [];

    outputs.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    context.df.setCustomStatus("Tokyo");
    outputs.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    context.df.setCustomStatus("Seattle");
    outputs.push(yield context.df.callActivity("E1_SayHello", "London"));
    context.df.setCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
});
```

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

E, em seguida, o cliente receberá a saída de orquestração somente quando o campo `CustomStatus` estiver definido como "London":

#### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
  [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
  [OrchestrationClient] DurableOrchestrationClientBase starter,
  string functionName,
  ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
      await Task.Delay(200);
      durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
      Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    let durableOrchestrationStatus = await client.getStatus(instanceId);
    while (status.customStatus.toString() !== "London") {
        await new Promise((resolve) => setTimeout(resolve, 200));
        durableOrchestrationStatus = await client.getStatus(instanceId);
    }

    const httpResponseMessage = {
        status: 200,
        body: JSON.stringify(durableOrchestrationStatus),
    };

    return httpResponseMessage;
};
```

> [!NOTE]
> Em JavaScript, o campo `customStatus` será definido quando a próxima ação `yield` ou `return` for agendada.

> [!WARNING]
> Ao fazer o desenvolvimento local em JavaScript, você precisará definir a variável de ambiente `WEBSITE_HOSTNAME` como `localhost:<port>`, por exemplo, `localhost:7071` para usar métodos em `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, confira o [problema no GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="output-customization"></a>Personalização de saída

Outro cenário interessante é segmentar usuários retornando saída personalizada com base em características ou interações exclusivas. Com a ajuda do status de orquestração personalizado, o código do lado do cliente ficará genérico. Todas as modificações principais ocorrerão no lado do servidor, conforme mostrado no exemplo a seguir:

#### <a name="c"></a>C#

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userChoice = context.df.getInput();

    switch (userChoice) {
        case 1:
            context.df.setCustomStatus({
                recommendedCities: [ "Tokyo", "Seattle" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
        case 2:
            context.df.setCustomStatus({
                recommendedCities: [ "Seattle", "London" ],
                recommendedSeasons: [ "Summer" ],
            });
            break;
        case 3:
            context.df.setCustomStatus({
                recommendedCity: [ "Tokyo", "London" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
    }

    // Wait for user selection and refine the recommendation
});
```

### <a name="instruction-specification"></a>Especificação de instrução

O orchestrator pode fornecer instruções exclusivas para os clientes por meio de estado personalizado. As instruções de status personalizado serão mapeadas para as etapas no código de orquestração:

#### <a name="c"></a>C#

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userId = context.df.getInput();

    const discount = yield context.df.callActivity("CalculateDiscount", userId);

    context.df.setCustomStatus({
        discount,
        discountTimeout = 60,
        bookingUrl = "https://www.myawesomebookingweb.com",
    });

    const isBookingConfirmed = yield context.df.waitForExternalEvent("bookingConfirmed");

    context.df.setCustomStatus(isBookingConfirmed
        ? { message: "Thank you for confirming your booking." }
        : { message: "The booking was not confirmed on time. Please try again." }
    );

    return isBookingConfirmed;
});
```

## <a name="sample"></a>Amostra

No exemplo a seguir, o status personalizado é definido primeiro;

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { nextActions: [ "A", "B", "C" ], foo: 2, };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Durante a execução da orquestração, clientes externos podem buscar este status personalizado:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

O clientes terão a seguinte resposta:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> O conteúdo do status personalizado é limitado a 16 KB de texto UTF-16 JSON porque ele precisa ser capaz de caber em uma coluna de Armazenamento de Tabelas do Azure. Os desenvolvedores poderão usar o armazenamento externo se eles precisarem de conteúdo maior.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre as APIs HTTP em Funções Duráveis](durable-functions-http-api.md)



<!-- Update_Description: wording update -->