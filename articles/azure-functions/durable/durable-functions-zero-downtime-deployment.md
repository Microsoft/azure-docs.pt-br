---
title: Implantação sem tempo de inatividade para Durable Functions
description: Saiba como habilitar sua orquestração de Durable Functions para implantações sem tempo de inatividade.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 2c96f2cc37c47c77b82ca86d5fd0295f0c66a896
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009476"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Implantação sem tempo de inatividade para Durable Functions

O [modelo de execução confiável](./durable-functions-orchestrations.md) do Durable Functions exige que as orquestrações sejam determinísticas, o que cria um desafio adicional a ser considerado ao implantar atualizações. Quando uma implantação contém alterações nas assinaturas da função de atividade ou na lógica do Orchestrator, as instâncias de orquestração em andamento falham. Essa situação é especialmente um problema para instâncias de orquestrações de longa execução, que podem representar horas ou dias de trabalho.

Para evitar que essas falhas ocorram, você tem duas opções: 
- Adie a implantação até que todas as instâncias de orquestração em execução tenham sido concluídas.
- Certifique-se de que qualquer instância de orquestração em execução use as versões existentes de suas funções. 

O gráfico a seguir compara as três principais estratégias para obter uma implantação sem tempo de inatividade para Durable Functions: 

| Estratégia |  Quando usar | Vantagens | Desvantagens |
| -------- | ------------ | ---- | ---- |
| [Controle de versão](#versioning) |  Aplicativos que não experimentam [alterações significativas](durable-functions-versioning.md) frequentes. | Simples de implementar. |  Maior tamanho do aplicativo de funções na memória e no número de funções.<br/>Duplicação de código. |
| [Verificação de status com slot](#status-check-with-slot) | Um sistema que não tem orquestrações de longa execução que duram mais de 24 horas ou orquestrações que se sobrepõem com frequência. | Base de código simples.<br/>Não requer gerenciamento de aplicativo de funções adicional. | Requer gerenciamento de Hub de tarefas ou conta de armazenamento adicional.<br/>Requer períodos de tempo em que nenhuma orquestração está em execução. |
| [Roteamento de aplicativos](#application-routing) | Um sistema que não tem períodos de tempo em que as orquestrações não estão sendo executadas, como os períodos de tempo com orquestrações que duram mais de 24 horas ou com orquestrações com maior frequência. | Lida com novas versões de sistemas com orquestrações continuamente em execução que têm alterações significativas. | Requer um roteador de aplicativo inteligente.<br/>Pode esgotar o número de aplicativos de funções permitidos pela sua assinatura. O padrão é 100. |

## <a name="versioning"></a>Controle de versão

Defina novas versões de suas funções e deixe as versões antigas em seu aplicativo de funções. Como você pode ver no diagrama, a versão da função se torna parte do seu nome. Como as versões anteriores das funções são preservadas, as instâncias de orquestração em andamento podem continuar a referenciá-las. Enquanto isso, as solicitações para novas instâncias de orquestração chamam a versão mais recente, à qual sua função de cliente de orquestração pode fazer referência a partir de uma configuração de aplicativo.

![Estratégia de controle de versão](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Nessa estratégia, cada função deve ser copiada e suas referências a outras funções devem ser atualizadas. Você pode torná-lo mais fácil escrevendo um script. Aqui está um [projeto de exemplo](https://github.com/TsuyoshiUshio/DurableVersioning) com um script de migração.

>[!NOTE]
>Essa estratégia usa slots de implantação para evitar o tempo de inatividade durante a implantação. Para obter informações mais detalhadas sobre como criar e usar novos slots de implantação, consulte [Azure Functions slots de implantação](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Verificação de status com slot

Embora a versão atual do seu aplicativo de funções esteja em execução no slot de produção, implante a nova versão do seu aplicativo de funções em seu slot de preparo. Antes de trocar os slots de produção e de preparo, verifique se há alguma instância de orquestração em execução. Depois que todas as instâncias de orquestração forem concluídas, você poderá fazer a permuta. Essa estratégia funciona quando você tem períodos previsíveis quando não há instâncias de orquestração em trânsito. Essa é a melhor abordagem quando suas orquestrações não são de execução longa e quando suas execuções de orquestração não se sobrepõem com frequência.

### <a name="function-app-configuration"></a>Configuração do aplicativo de funções

Use o procedimento a seguir para configurar esse cenário.

1. [Adicione slots de implantação](../functions-deployment-slots.md#add-a-slot) ao seu aplicativo de funções para preparo e produção.

1. Para cada slot, defina a [configuração de aplicativo AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) como a cadeia de conexão de uma conta de armazenamento compartilhada. Essa cadeia de conexão da conta de armazenamento é usada pelo Azure Functions Runtime. Essa conta é usada pelo tempo de execução do Azure Functions e gerencia as chaves da função.

1. Para cada slot, crie uma nova configuração de aplicativo, por exemplo, `DurableManagementStorage` . Defina seu valor como a cadeia de conexão de contas de armazenamento diferentes. Essas contas de armazenamento são usadas pela extensão de Durable Functions para [execução confiável](./durable-functions-orchestrations.md). Use uma conta de armazenamento separada para cada slot. Não marque essa configuração como uma configuração de slot de implantação.

1. Em seu aplicativo de funções [host.jsna seção durableTask do arquivo](durable-functions-bindings.md#hostjson-settings), especifique `connectionStringName` (durável 2. x) ou `azureStorageConnectionStringName` (durável 1. x) como o nome da configuração do aplicativo que você criou na etapa 3.

O diagrama a seguir mostra a configuração descrita de Slots de implantação e contas de armazenamento. Nesse cenário potencial de pré-implantação, a versão 2 de um aplicativo de funções está em execução no slot de produção, enquanto a versão 1 permanece no slot de preparo.

![Slots de implantação e contas de armazenamento](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.jsexemplos

Os fragmentos JSON a seguir são exemplos da configuração da cadeia de conexão no *host.jsno* arquivo.

#### <a name="functions-20"></a>Funções 2,0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "storageProvider": {
        "connectionStringName": "DurableManagementStorage"
      }
    }
  }
}
```

#### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Configuração de pipeline de CI/CD

Configure seu pipeline de CI/CD para implantar somente quando seu aplicativo de funções não tiver instâncias de orquestração pendentes ou em execução. Quando você estiver usando Azure Pipelines, poderá criar uma função que verifica essas condições, como no exemplo a seguir:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

Em seguida, configure o portão de preparo para aguardar até que nenhuma orquestração esteja em execução. Para obter mais informações, consulte [liberar controle de implantação usando Gates](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Portão de implantação](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines verifica seu aplicativo de funções para executar instâncias de orquestração antes do início da implantação.

![Portão de implantação (em execução)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Agora, a nova versão do seu aplicativo de funções deve ser implantada no slot de preparo.

![Slot de preparo](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Por fim, slots de permuta. 

As configurações do aplicativo que não são marcadas como configurações do slot de implantação também são trocadas, portanto, o aplicativo da versão 2 mantém sua referência à conta de armazenamento A. Como o estado de orquestração é acompanhado na conta de armazenamento, todas as orquestrações em execução no aplicativo da versão 2 continuam a ser executadas no novo slot sem interrupção.

![Slot de implantação](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Para usar a mesma conta de armazenamento para ambos os slots, você pode alterar os nomes dos hubs de tarefas. Nesse caso, você precisa gerenciar o estado de seus slots e as configurações de HubName do seu aplicativo. Para saber mais, confira [hubs de tarefas em Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Roteamento de aplicativos

Essa estratégia é a mais complexa. No entanto, ele pode ser usado para aplicativos de funções que não têm tempo entre orquestrações em execução.

Para essa estratégia, você deve criar um *roteador de aplicativo* na frente de seu Durable functions. Esse roteador pode ser implementado com Durable Functions. O roteador tem a responsabilidade de:

* Implante o aplicativo de funções.
* Gerencie a versão do Durable Functions. 
* Rotear solicitações de orquestração para aplicativos de funções.

Na primeira vez que uma solicitação de orquestração é recebida, o roteador realiza as seguintes tarefas:

1. Cria um novo aplicativo de funções no Azure.
2. Implanta o código do aplicativo de funções no novo aplicativo de funções no Azure.
3. Encaminha a solicitação de orquestração para o novo aplicativo.

O roteador gerencia o estado de qual versão do código do aplicativo é implantada em qual aplicativo de funções no Azure.

![Roteamento de aplicativos (primeira vez)](media/durable-functions-zero-downtime-deployment/application-routing.png)

O roteador direciona as solicitações de implantação e orquestração para o aplicativo de funções apropriado com base na versão enviada com a solicitação. Ele ignora a versão do patch.

Ao implantar uma nova versão do seu aplicativo sem uma alteração significativa, você pode incrementar a versão do patch. O roteador é implantado em seu aplicativo de funções existente e envia solicitações para as versões antigas e novas do código, que são roteadas para o mesmo aplicativo de funções.

![Roteamento de aplicativos (sem alteração significativa)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Ao implantar uma nova versão do seu aplicativo com uma alteração significativa, você pode incrementar a versão principal ou secundária. Em seguida, o roteador de aplicativo cria um novo aplicativo de funções no Azure, implanta-o e roteia solicitações para a nova versão do seu aplicativo para ele. No diagrama a seguir, a execução de orquestrações na versão 1.0.1 do aplicativo continua em execução, mas as solicitações para a versão 1.1.0 são roteadas para o novo aplicativo de funções.

![Roteamento de aplicativos (alteração significativa)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

O roteador monitora o status das orquestrações na versão 1.0.1 e remove os aplicativos após a conclusão de todas as orquestrações. 

### <a name="tracking-store-settings"></a>Configurações do repositório de rastreamento

Cada aplicativo de funções deve usar filas de agendamento separadas, possivelmente em contas de armazenamento separadas. Se você quiser consultar todas as instâncias de orquestrações em todas as versões do seu aplicativo, você pode compartilhar instâncias e tabelas de histórico em seus aplicativos de funções. Você pode compartilhar tabelas Configurando `trackingStoreConnectionStringName` as `trackingStoreNamePrefix` configurações e no arquivo de [ configuraçõeshost.js](durable-functions-bindings.md#host-json) para que todas usem os mesmos valores.

Para obter mais informações, consulte [gerenciar instâncias no Durable Functions no Azure](durable-functions-instance-management.md).

![Configurações do repositório de rastreamento](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Durable Functions de controle de versão](durable-functions-versioning.md)
