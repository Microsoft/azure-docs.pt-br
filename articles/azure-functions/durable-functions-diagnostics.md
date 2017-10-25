---
title: "Diagnóstico nas Funções Duráveis – Azure"
description: "Saiba como diagnosticar problemas com a extensão de Funções Duráveis do Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 5ebab8660dfe21984e1a7f9a1cb925aea60de213
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnóstico nas Funções Duráveis (Azure Functions)

Há várias opções para diagnosticar problemas com as [Funções Duráveis](durable-functions-overview.md). Algumas dessas opções são as mesmas usadas para funções regulares e algumas delas são exclusivas das Funções Duráveis.

## <a name="application-insights"></a>Application Insights

O [Application Insights](../application-insights/app-insights-overview.md) é a maneira recomendada de fazer diagnóstico e monitoramento no Azure Functions. O mesmo se aplica às Funções Duráveis. Para obter uma visão geral de como usar o Application Insights em seu aplicativo de funções, consulte [Monitor o Azure Functions](functions-monitoring.md).

A Extensão Durável do Azure Functions também emite *eventos de acompanhamento*, que permitem rastrear a execução de uma orquestração de ponta a ponta. Eles podem ser encontrados e consultados usando a ferramenta [Application Insights Analytics](../application-insights/app-insights-analytics.md) no portal do Azure.

### <a name="tracking-data"></a>Acompanhamento de dados

Cada evento de ciclo de vida de uma instância de orquestração faz com que um evento de acompanhamento seja gravado na coleção de **rastreamentos** no Application Insights. Esse evento tem um conteúdo **customDimensions** com vários campos.  Todos os nomes de campo são prefixados com `prop__`.

* **hubName**: o nome do hub de tarefas no qual suas orquestrações estão em execução.
* **appName**: o nome do aplicativo de funções. Ele é útil quando você tem vários aplicativos de funções compartilhando a mesma instância do Application Insights.
* **slotName**: o [slot de implantação](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) no qual o aplicativo de funções atual está sendo executado. Ele é útil quando você aproveitar os slots de implantação para controlar a versão de suas orquestrações.
* **functionName**: o nome da função de orquestrador ou atividade.
* **functionType**: o tipo da função, como **Orquestrador** ou **Atividade**.
* **instanceId**: a ID exclusiva da instância de orquestração.
* **state**: o estado de execução do ciclo de vida da instância. Os valores válidos incluem:
    * **Scheduled**: a função foi agendada para execução, mas ainda não começou a ser executada.
    * **Started**: a função começou a ser executada, mas não ainda esperou ou foi concluída.
    * **Awaited**: o orquestrador agendou algum trabalho e está aguardando sua conclusão.
    * **Listening**: o orquestrador está escutando uma notificação de evento externo.
    * **Completed**: a função foi concluída com êxito.
    * **Failed**: a função falhou com um erro.
* **reason**: dados adicionais associados ao evento de acompanhamento. Por exemplo, se uma instância estiver aguardando uma notificação de evento externo, esse campo indica o nome do evento que ela está aguardando. Se uma função tiver falhado, ele conterá detalhes do erro.
* **isReplay**: valor booliano que indica se o evento de acompanhamento deve ter a execução reproduzida.
* **extensionVersion**: a versão da extensão da Tarefa Durável. Esse dado é especialmente importante ao relatar possíveis bugs na extensão. Instâncias de execução longa podem relatar várias versões se uma atualização ocorrer durante sua execução. 

O detalhamento dos dados de acompanhamento emitidos para o Application Insights pode ser configurado na seção `logger` do arquivo `host.json`.

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

Por padrão, todos os eventos de acompanhamento são emitidos. O volume dos dados pode ser reduzido definindo `Host.Triggers.DurableTask` como `"Warning"` ou `"Error"`. Nesse caso, os eventos de acompanhamento serão emitidos somente para situações excepcionais.

> [!WARNING]
> Por padrão, a amostragem da telemetria do Application Insights é feita segundo o tempo de execução do Azure Functions, para evitar a emissão de dados com frequência excessiva. Isso pode fazer com que informações de acompanhamento sejam perdidas quando muitos eventos de ciclo de vida ocorrerem em um curto período. O [artigo sobre Monitoramento no Azure Functions](functions-monitoring.md#configure-sampling) explica como configurar esse comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A consulta a seguir mostra dados de acompanhamento históricos de uma única instância de orquestração da função [Sequência Hello](durable-functions-sequence.md). Ela é escrita usando a [AIQL (Linguagem de Consulta do Application Insights)](https://docs.loganalytics.io/docs/Language-Reference). Ela filtra a execução de reproduções para que somente o caminho de execução *lógico* seja mostrado.

```AIQL
let targetInstanceId = "bf71335b26564016a93860491aa50c7f";
let start = datetime(2017-09-29T00:00:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| where isReplay == false
| where instanceId == targetInstanceId
| project timestamp, functionName, state, instanceId, appName = cloud_RoleName
```
O resultado é uma lista de eventos de acompanhamento que mostram o caminho de execução da orquestração, incluindo funções de atividade.

![Consulta do Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-query.png)

> [!NOTE]
> Alguns desses eventos de acompanhamento podem estar fora de ordem devido à falta de precisão na coluna `timestamp`. Isso está sendo acompanhado no GitHub como o [problema #71](https://github.com/Azure/azure-functions-durable-extension/issues/71).

### <a name="instance-summary-query"></a>Consulta de resumo da instância

A consulta a seguir exibe o status de todas as instâncias de orquestração que foram executadas em um intervalo de tempo especificado.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay == false
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
O resultado é uma lista de IDs de instância e seu status de tempo de execução atual.

![Consulta do Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registro em log

É importante ter em mente o comportamento de reprodução do orquestrador ao gravar logs diretamente de uma função de orquestrador. Por exemplo, considere a seguinte função de orquestrador:

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

Os dados de log resultantes serão semelhantes ao seguinte:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Lembre-se de que, embora os logs declarem estar chamando F1, F2 e F3, essas funções *realmente* são chamadas apenas na primeira vez em que são encontradas. Chamadas posteriores que ocorrem durante a reprodução são ignoradas e as saídas são reproduzidas para a lógica do orquestrador.

Se quiser registrar no log apenas a execução sem reproduções, você poderá escrever uma expressão condicional para registrar apenas se `IsReplaying` for `false`. Considere o exemplo anterior, mas desta vez com verificações de reprodução.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Com essa alteração, a saída do log será a seguinte:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="debugging"></a>Depurando

O Azure Functions dá suporte à depuração do código de função diretamente e esse mesmo suporte se estende às Funções Duráveis, seja em execução no Azure ou localmente. No entanto, há alguns comportamentos a que você deve estar atento ao depurar:

* **Reprodução**: funções de orquestrador são reproduzidas regularmente quando novas entradas são recebidas. Isso significa que uma única execução *lógica* de uma função de orquestrador pode atingir o mesmo ponto de interrupção várias vezes, especialmente se ele estiver definido no início do código da função.
* **Aguardar**: sempre que um `await` é encontrado, ele leva o controle de volta para o dispatcher do Framework de Tarefa Durável. Se esta for a primeira vez que um determinado `await` foi encontrado, a tarefa associada *nunca* será retomada. Uma vez que a tarefa nunca é retomada, *ignorar* a espera (F10 no Visual Studio) não é possível. Ignorar só funciona quando uma tarefa está sendo reproduzida.
* **Tempos limite de mensagem**: as Funções Duráveis usam internamente mensagens de fila para acionar a execução de funções de orquestrador e de funções de atividade. Em um ambiente com várias VMs, interromper a depuração por longos períodos pode fazer com que outra VM receba a mensagem, resultando em uma execução duplicada. Esse comportamento também existe para funções de gatilho de fila regulares, mas é importante ressaltar neste contexto, uma vez que as filas são um detalhe de implementação.

> [!TIP]
> Ao definir pontos de interrupção, se quiser interromper somente a execução que não é de repetição, você pode definir um ponto de interrupção condicional que interrompe somente se `IsReplaying` for `false`.

## <a name="storage"></a>Armazenamento

Por padrão, as Funções Duráveis armazenam o estado no Armazenamento do Azure. Isso significa que você pode inspecionar o estado de suas orquestrações usando ferramentas como o [Gerenciador de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de tela do Gerenciador de Armazenamento do Azure](media/durable-functions-diagnostics/storage-explorer.png)

Isso é útil para a depuração, pois você vê exatamente em qual estado uma orquestração pode estar. Mensagens nas filas também podem ser examinadas para saber qual trabalho está pendente (ou preso, em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execução no armazenamento de tabelas, evite depender desta tabela. Ela pode mudar à medida que a extensão de Funções Duráveis evoluir.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar temporizadores duráveis](durable-functions-timers.md)