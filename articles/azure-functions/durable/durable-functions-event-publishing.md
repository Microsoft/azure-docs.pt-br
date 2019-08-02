---
title: Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)
description: Saiba como configurar a publicação automática da Grade de Eventos do Azure para Funções Duráveis.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733689"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)

Este artigo mostra como configurar as Durable Functions para publicar eventos de ciclo de vida de orquestração (como criados, concluídos e com falha) em um [Tópico de Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview) personalizado.

A seguir, alguns cenários em que esse recurso é útil:

* **Cenários de DevOps como implantações verdes/azuis**: Convém saber se as tarefas estão em execução antes de implementar a [estratégia de implantação lado a lado](durable-functions-versioning.md#side-by-side-deployments).

* **Suporte avançado a monitoramento e diagnóstico**: Você pode manter controle das informações de status de orquestração em um repositório externo otimizado para consultas, como Banco de Dados ou CosmosDB.

* **Atividade em segundo plano de execução longa**: Se você usar as Durable Functions para uma atividade em segundo plano de execução longa, esse recurso o ajudará a saber o status atual.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc ou posterior em seu projeto de Funções Duráveis.
* Instale o [Emulador de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Criar um tópico de grade de eventos personalizados

Crie um tópico de grade de eventos para enviar eventos de funções duráveis. As instruções a seguir mostram como criar um tópico usando a CLI do Azure. Para obter informações sobre como fazer isso usando o PowerShell ou o portal do Azure, consulte os seguintes artigos:

* [Guias de início rápido do EventGrid: Criar eventos personalizados – PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [Guias de início rápido do EventGrid: Criar eventos personalizados – portal do Azure](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Atualmente, a grade de eventos do Azure não dá suporte a todas as regiões. Para obter informações sobre quais regiões têm suporte, consulte o [visão geral da grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico de grade de eventos fornece um ponto de extremidade definidos pelo usuário que você publicar seu evento para. Substitua `<topic_name>` por um nome exclusivo para o tópico. O nome do tópico deve ser exclusivo, pois se torna uma entrada DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obter o ponto de extremidade e a chave

Obter o ponto de extremidade do tópico. Substitua `<topic_name>` pelo nome escolhido.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Obtenha a chave do tópico. Substitua `<topic_name>` pelo nome escolhido.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Agora, você pode enviar eventos para o tópico.

## <a name="configure-azure-event-grid-publishing"></a>Configurar publicação da Grade de Eventos do Azure

No projeto de Funções Duráveis, encontre o arquivo `host.json`.

Adicione `eventGridTopicEndpoint` e `eventGridKeySettingName` em uma propriedade `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

As possíveis propriedades de configuração de grade de eventos do Azure podem ser encontradas na [documentação do host. JSON](../functions-host-json.md#durabletask). Depois de configurar o `host.json` arquivo, seu aplicativo de função envia eventos de ciclo de vida para o tópico de grade de eventos. Isso funciona quando você executa seu aplicativo de funções, tanto localmente quanto no Azure. ' '

Defina a configuração do aplicativo para a chave do tópico no Aplicativo de Funções e `local.setting.json`. O JSON a seguir é um exemplo do `local.settings.json` para depuração local. Substitua `<topic_key>` pela chave do tópico.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Verifique se o [Emulador de Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) está funcionando. É recomendável executar o comando `AzureStorageEmulator.exe clear all` antes da execução.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que escutam eventos

Crie um Aplicativo de Funções. É melhor para localizá-lo na mesma região que o tópico de grade de eventos.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho de grade de eventos

Crie uma função para receber os eventos de ciclo de vida. Selecione **Função Personalizada**.

![Selecione Criar uma função personalizada.](./media/durable-functions-event-publishing/functions-portal.png)

Escolha o Gatilho de Grade de Eventos e selecione `C#`.

![Selecione o Gatilho de Grade de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Digite o nome da função e selecione `Create`.

![Crie o Gatilho de Grade de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Uma função com o código a seguir é criada:

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Selecione `Add Event Grid Subscription`. Essa operação adiciona uma assinatura de grade de eventos para o tópico de grade de eventos que você criou. Para obter mais informações, consulte [Conceitos na Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/concepts)

![Selecione o link do Gatilho de Grade de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selecione `Event Grid Topics` para **Tipo de Tópico**. Selecione o grupo de recursos que você criou para o tópico de grade de eventos. Em seguida, selecione a instância do tópico de grade de eventos. Pressione `Create`.

![Criar uma assinatura na Grade de Eventos.](./media/durable-functions-event-publishing/eventsubscription.png)

Agora, você está pronto para receber eventos de ciclo de vida.

## <a name="create-durable-functions-to-send-the-events"></a>Crie Durable Functions para enviar os eventos

No seu projeto de Funções Duráveis, inicie a depuração em seu computador local.  O código a seguir é o mesmo que o código de modelo para as Funções Duráveis. Você já configurou `host.json` e `local.settings.json` em seu computador local.

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Se chamar `Sample_HttpStart` com Postman ou seu navegador, a Função Durável começará a enviar eventos de ciclo de vida. O ponto de extremidade é geralmente `http://localhost:7071/api/Sample_HttpStart` para depuração local.

Consulte os logs da função que você criou no portal do Azure.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esquema do evento

A lista a seguir explica o esquema de eventos de ciclo de vida:

* **`id`** : Identificador exclusivo para o evento de grade de eventos.
* **`subject`** : Caminho para o assunto do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`** : Parâmetros Específicos de Durable Functions.
  * **`hubName`** : Nome de [TaskHub](durable-functions-task-hubs.md).
  * **`functionName`** : Nome de função do Orchestrator.
  * **`instanceId`** : instanceId de Durable Functions.
  * **`reason`** : dados adicionais associados ao evento de acompanhamento. Para obter mais informações, consulte [Diagnóstico nas Funções Duráveis (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : Status de Tempo de Execução de Orquestração. Em Execução, Concluído, Falha, Cancelado.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Hora do Evento (UTC).
* **`dataVersion`** : Versão do esquema de evento do ciclo de vida.
* **`metadataVersion`** :  Valor dos metadados.
* **`topic`** : Recurso do tópico de grade de eventos.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, use [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conhecer a instância de gerenciamento em Funções Duráveis](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Conhecer controle de versão em Funções Duráveis](durable-functions-versioning.md)
