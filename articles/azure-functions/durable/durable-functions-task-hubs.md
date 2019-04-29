---
title: Hubs de tarefas nas Funções Duráveis – Azure
description: Saiba o que é um hub de tarefas na extensão de Funções Duráveis do Azure Functions. Saiba como configurar hubs de tarefas.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2017
ms.date: 02/21/2019
ms.author: v-junlch
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730253"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hubs de tarefas nas Funções Duráveis (Azure Functions)

Um *hub de tarefas* nas [Funções Duráveis](durable-functions-overview.md) é um contêiner lógico dos recursos do Armazenamento do Azure usado para orquestrações. Funções de orquestrador e atividade só podem interagir entre si quando pertencem ao mesmo hub de tarefas.

Se vários aplicativos de funções compartilham uma conta de armazenamento, cada aplicativo de funções *deve* ser configurado com um nome de hub de tarefas separado. Uma conta de armazenamento pode conter vários hubs de tarefas. O diagrama a seguir ilustra um hub de tarefas por aplicativo de funções em contas de armazenamento compartilhadas e dedicadas.

![Diagrama mostrando contas de armazenamento compartilhadas e dedicadas.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Recursos de Armazenamento do Azure

Um hub de tarefas é composto pelos seguinte recursos de armazenamento:

- Uma ou mais filas de controle.
- Uma fila de item de trabalho.
- Uma tabela de histórico.
- Uma tabela de instâncias.
- Um contêiner de armazenamento que contém um ou mais blobs de concessão.

Todos esses recursos são criados automaticamente na conta padrão do Armazenamento do Azure quando funções de atividade ou orquestrador são executadas ou estão agendadas para execução. O artigo [Desempenho e Escala](durable-functions-perf-and-scale.md) explica como esses recursos são usados.

## <a name="task-hub-names"></a>Nomes de hub de tarefas

Os hubs de tarefas são identificados por um nome declarado no arquivo *host.json*, conforme mostrado no exemplo a seguir:

### <a name="hostjson-functions-1x"></a>host.json (funções 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (funções 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

Os hubs de tarefas também podem ser configurados usando as configurações do aplicativo, conforme mostrado no seguinte arquivo de exemplo *host.json*:

### <a name="hostjson-functions-1x"></a>host.json (funções 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (funções 2.x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

O nome do hub de tarefas será definido com o valor da configuração do aplicativo `MyTaskHub`. O seguinte `local.settings.json` demonstra como definir a `MyTaskHub`configurar como `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Confira um exemplo pré-compilado em C# de como escrever uma função que usa um [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) para trabalhar com um hub de tarefas configurado como uma Configuração de Aplicativo:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

E abaixo está a configuração necessária para JavaScript. A propriedade do hub de tarefas no arquivo `function.json` é definida por meio da Configuração de Aplicativo:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome padrão será **DurableFunctionsHub**.

> [!NOTE]
> O nome é o que diferencia um hub de tarefas de outro quando há vários em uma conta de armazenamento compartilhada. Se você tiver vários aplicativos de funções compartilhando uma conta de armazenamento, será necessário configurar, explicitamente, nomes diferentes para cada hub de tarefas nos arquivos *host.json*. Caso contrário, vários aplicativos de funções competirão entre si pelas mensagens, o que pode resultar em comportamento indefinido.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão](durable-functions-versioning.md)

<!-- Update_Description: wording update -->