---
title: Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)
description: Saiba como configurar a publicação automática da Grade de Eventos do Azure para Funções Duráveis.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 44df100a5c794abf918a09dea0f94d30ddf916d3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175950"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Publicação de Funções Duráveis para a Grade de Eventos do Azure (visualização)

Este artigo mostra como configurar as Durable Functions para publicar eventos de ciclo de vida de orquestração (como criados, concluídos e com falha) em um [Tópico de Grade de Eventos do Azure](../../event-grid/overview.md) personalizado.

A seguir, alguns cenários em que esse recurso é útil:

* **Cenários de DevOps como implantações verdes/azuis**: Convém saber se as tarefas estão em execução antes de implementar a [estratégia de implantação lado a lado](durable-functions-versioning.md#side-by-side-deployments).

* **Suporte avançado a monitoramento e diagnóstico**: Você pode controlar as informações de status de orquestração em um repositório externo otimizado para consultas, como o Banco de Dados SQL do Azure ou o Azure Cosmos DB.

* **Atividade em segundo plano de execução longa**: Se você usar as Durable Functions para uma atividade em segundo plano de execução longa, esse recurso o ajudará a saber o status atual.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) no seu projeto de Durable Functions.
* Instale o [Emulador de Armazenamento do Azure](../../storage/common/storage-use-emulator.md) (apenas Windows) ou use uma conta já existente de Armazenamento do Azure.
* Instalar a [CLI do Azure](/cli/azure/) ou usar o [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Criar um tópico de Grade de Eventos personalizado

Crie um tópico de Grade de Eventos para enviar eventos a partir de Funções Duráveis. As instruções a seguir mostram como criar um tópico usando a CLI do Azure. Você também pode criar o tópico [usando o PowerShell](../../event-grid/custom-event-quickstart-powershell.md) ou [usando o portal do Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Atualmente, a Grade de Eventos do Azure não oferece suporte a todas as regiões. Para obter informações sobre quais regiões têm suporte, consulte a [Visão geral da Grade de Eventos do Azure](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico de Grade de Eventos fornece um ponto de extremidade definido pelo usuário no qual você posta seu evento. Substitua `<topic_name>` por um nome exclusivo para o tópico. O nome do tópico deve ser exclusivo, pois se torna uma entrada DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obter o ponto de extremidade e a chave

Obter o ponto de extremidade do tópico. Substitua `<topic_name>` pelo nome escolhido.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Obtenha a chave do tópico. Substitua `<topic_name>` pelo nome escolhido.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Agora, você pode enviar eventos para o tópico.

## <a name="configure-event-grid-publishing"></a>Configurar publicação da Grade de Eventos

No projeto de Funções Duráveis, encontre o arquivo `host.json`.

### <a name="durable-functions-1x"></a>Durable Functions 1.x

Adicione `eventGridTopicEndpoint` e `eventGridKeySettingName` em uma propriedade `durableTask`.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2.x

Adicione uma seção `notifications` à propriedade `durableTask` do arquivo, substituindo `<topic_name>` pelo nome que você escolher. Se a propriedade `durableTask` ou `extensions` não existir, crie-a como neste exemplo:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

As propriedades de configuração possível da Grade de Eventos do Azure estão na [Documentação do host.json](../functions-host-json.md#durabletask). Após a configuração do arquivo `host.json`, seu aplicativo de funções envia eventos de ciclo de vida ao tópico da Grade de Eventos. Essa ação é iniciada quando você executa seu aplicativo de funções localmente e no Azure.

Defina a configuração do aplicativo para a chave do tópico no Aplicativo de Funções e `local.settings.json`. O JSON a seguir é um exemplo do `local.settings.json` para depuração local. Substitua `<topic_key>` pela chave do tópico.  

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

Se você estiver usando o [Emulador de Armazenamento](../../storage/common/storage-use-emulator.md) (apenas Windows), verifique se ele está funcionando. É recomendável executar o comando `AzureStorageEmulator.exe clear all` antes da execução.

Se você estiver usando uma conta existente de Armazenamento do Azure, substitua `UseDevelopmentStorage=true` em `local.settings.json` pela respectiva cadeia de conexão.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que escutam eventos

Usando o portal do Azure, crie outro aplicativo de funções para escutar eventos publicados pelo seu aplicativo Durable Functions. É melhor localizá-lo na mesma região que o tópico de Grade de Eventos.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho de Grade de Eventos

1. No seu aplicativo de funções, escolha **Funções** e selecione **+ Adicionar**. 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Adicione uma função no portal do Azure." border="true":::

1. Pesquise **Grade de Eventos** e, em seguida, selecione o modelo **Gatilho da Grade de Eventos do Azure**. 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Selecione o modelo de gatilho da grade de eventos no portal do Azure." border="true":::

1. Nomeie o novo gatilho e, em seguida, selecione **Criar Função**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Nomeie o gatilho da grade de eventos no portal do Azure." border="true":::


    Uma função com o código a seguir é criada:

    # <a name="c-script"></a>[Script do C#](#tab/csharp-script)

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

   # <a name="javascript"></a>[JavaScript](#tab/javascript)

   ```javascript
   module.exports = async function(context, eventGridEvent) {
       context.log(typeof eventGridEvent);
       context.log(eventGridEvent);
   }
   ```

---

### <a name="add-an-event-grid-subscription"></a>Adicionar uma assinatura de Grade de Eventos

Agora você pode adicionar uma assinatura de Grade de Eventos ao tópico de Grade de Eventos que criou. Para obter mais informações, confira [Conceitos na Grade de Eventos do Azure](../../event-grid/concepts.md).

1. Na sua nova função, selecione **Integração** e, em seguida, escolha **Gatilho da Grade de Eventos** (eventGridEvent). 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Selecione o link do Gatilho de Grade de Eventos." border="true":::

1. Selecione **Criar Descrição da Grade de Eventos**.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Crie a assinatura da Grade de Eventos." border="true":::

1. Nomeie sua assinatura do evento e selecione o tipo de tópico **Tópicos de Grade de Eventos**. 

1. Selecione a assinatura. Em seguida, selecione o grupo de recursos e o recurso que você criou para o tópico de Grade de Eventos. 

1. Selecione **Criar**.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Crie uma assinatura da Grade de Eventos." border="true":::

Agora, você está pronto para receber eventos de ciclo de vida.

## <a name="run-durable-functions-app-to-send-the-events"></a>Executar aplicativo do Durable Functions para enviar os eventos

No projeto de Durable Functions que você configurou anteriormente, comece a depuração no computador local e inicie uma orquestração. O aplicativo publica eventos de ciclo de vida do Durable Functions na Grade de Eventos. Verifique se a Grade de Eventos dispara a função de ouvinte que você criou, consultando os logs no portal do Azure.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esquema do evento

A lista a seguir explica o esquema de eventos de ciclo de vida:

* **`id`** : Identificador exclusivo para o evento de Grade de Eventos.
* **`subject`** : Caminho para o assunto do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`** : Parâmetros Específicos de Durable Functions.
  * **`hubName`** : Nome de [TaskHub](durable-functions-task-hubs.md).
  * **`functionName`** : Nome de função do Orchestrator.
  * **`instanceId`** : instanceId de Durable Functions.
  * **`reason`** : dados adicionais associados ao evento de acompanhamento. Para obter mais informações, consulte [Diagnóstico nas Funções Duráveis (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : Status de Runtime de Orquestração. Em Execução, Concluído, Falha, Cancelado.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Hora do Evento (UTC).
* **`dataVersion`** : Versão do esquema de evento do ciclo de vida.
* **`metadataVersion`** :  Valor dos metadados.
* **`topic`** : Recurso de tópico de grade de eventos.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, leia [Depuração local do gatilho da Grade de Eventos do Azure Functions](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conhecer a instância de gerenciamento em Funções Duráveis](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Conhecer controle de versão em Funções Duráveis](durable-functions-versioning.md)
