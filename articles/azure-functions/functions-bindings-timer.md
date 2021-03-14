---
title: Gatilho de temporizador para o Azure Functions
description: Entenda como usar gatilhos de temporizador no Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: dbcd6d55ee921c7fabd8e746e0fdcd6f1427733c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210699"
---
# <a name="timer-trigger-for-azure-functions"></a>Gatilho de temporizador para o Azure Functions

Este artigo explica como trabalhar com gatilhos de temporizador no Azure Functions. Um gatilho de temporizador permite executar uma função em uma agenda.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para obter informações sobre como executar manualmente uma função disparada por temporizador, consulte [executar manualmente uma função não disparada por http](./functions-manually-run-non-http.md).

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

O gatilho de timer é fornecido no [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pacote NuGet, versão 2. x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

O gatilho de timer é fornecido no [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pacote NuGet, versão 2. x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que é executada cada vez que os minutos têm um valor divisível por cinco (por exemplo, se a função começar em 18:57:00, o próximo desempenho será em 19:00:00). O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função grava um log que indica se esta chamada de função deve-se a uma ocorrência de agendamento ausente. O [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objeto é passado para a função.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código de script do C#:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

A função de exemplo a seguir é disparada e executada a cada cinco minutos. A anotação `@TimerTrigger` na função define o agendamento usando o mesmo formato de cadeia de caracteres que as [expressões CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava um log que indica se esta chamada de função deve-se a uma ocorrência de agendamento ausente. Um [objeto de timer](#usage) é passado para a função.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como configurar o *function.jsem* e *run.ps1* arquivo para um gatilho de temporizador no [PowerShell](./functions-reference-powershell.md).

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

Uma instância do [objeto de timer](#usage) é passada como o primeiro argumento para a função.

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir usa uma associação de gatilho de temporizador cuja configuração é descrita na *function.jsno* arquivo. A [função Python](functions-reference-python.md) real que usa a associação é descrita no arquivo *__init__. py* . O objeto passado para a função é do tipo [objeto Azure. Functions. TimerRequest](/python/api/azure-functions/azure.functions.timerrequest). A lógica de função grava nos logs indicando se a invocação atual é devido a uma ocorrência de agendamento ausente.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Aqui está o código Python:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), utilize o atributo [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

O construtor do atributo usa a expressão CRON ou um `TimeSpan`. Você só poderá usar `TimeSpan` se o aplicativo de funções estiver em execução em um plano do serviço de aplicativo. `TimeSpan` Não tem suporte para funções Premium de consumo ou elástico.

O exemplo a seguir mostra uma expressão CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

A anotação `@TimerTrigger` na função define o agendamento usando o mesmo formato de cadeia de caracteres que as [expressões CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `TimerTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "timerTrigger". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como "in". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa o objeto de temporizador no código de função. | 
|**agendamento**|**ScheduleExpression**|Um [expressão CRON](#ncrontab-expressions) ou um valor [TimeSpan](#timespan). É possível usar um `TimeSpan` somente para um aplicativo de função executado em um Plano do Serviço de Aplicativo. Você pode colocar a expressão de agendamento em uma configuração de aplicativo e definir essa propriedade como o nome da configuração do aplicativo encapsulado em **%** sinais, como neste exemplo: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Se `true`, a função será invocada quando o runtime for iniciado. Por exemplo, o runtime inicia quando o aplicativo de função desperta depois de ficar ocioso devido à inatividade. Quando o aplicativo de funções é reiniciado devido a alterações de função e quando o aplicativo de funções é dimensionado horizontalmente. Portanto, **runOnStartup** deve ser raramente, se já estiver definido como `true` , especialmente em produção. |
|**useMonitor**|**UseMonitor**|Definido como `true` ou `false` para indicar se o agendamento deve ser monitorado. Agendar o monitoramento persiste as ocorrências de agendamento para ajudar a garantir que o agendamento seja mantido corretamente mesmo quando instâncias do aplicativo de função forem reiniciadas. Se não for definido explicitamente, o padrão será `true` para agendas que têm um intervalo de recorrência maior ou igual a 1 minuto. Para agendamentos que disparam mais de uma vez por minuto, o padrão é `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Recomendamos a configuração **runOnStartup** para `true` em produção. Usar essa configuração faz com que código seja executado em momentos altamente imprevisíveis. Em determinadas configurações de produção, essas execuções extras podem resultar em custos significativamente mais altos para aplicativos hospedados em planos de consumo. Por exemplo, com **runOnStartup** habilitado, o gatilho é invocado sempre que seu aplicativo de funções é dimensionado. Verifique se você compreender totalmente o comportamento de produção de suas funções antes de habilitar **runOnStartup** em produção.

## <a name="usage"></a>Uso

Quando uma função de gatilho de temporizador é invocada, um objeto de temporizador é passado para a função. O JSON a seguir é uma representação de exemplo do objeto timer.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

A propriedade `isPastDue` é `true` quando a invocação da função atual é posterior ao agendado. Por exemplo, uma reinicialização do aplicativo de função pode causar a perda de uma invocação.

## <a name="ncrontab-expressions"></a>Expressões NCRONTAB

Azure Functions usa a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar as expressões NCronTab. Uma expressão NCRONTAB é semelhante a uma expressão CRON, exceto que ela inclui um sexto campo adicional no início a ser usado para a precisão de tempo em segundos:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Cada campo pode ter um dos seguintes tipos de valores:

|Type  |Exemplo  |Quando disparado  |
|---------|---------|---------|
|Um valor específico |<nobr>`0 5 * * * *`</nobr>| Uma vez a cada hora do dia, no minuto 5 de cada hora |
|Todos os valores (`*`)|<nobr>`0 * 5 * * *`</nobr>| A cada minuto na hora, começando na hora 5 |
|Um intervalo (`-` operador)|<nobr>`5-7 * * * * *`</nobr>| Três vezes por minuto em segundos de 5 a 7 durante cada minuto de cada hora de cada dia |
|Um conjunto de valores (`,` operador)|<nobr>`5,8,10 * * * * *`</nobr>| Três vezes por minuto em segundos 5, 8 e 10 durante cada minuto de cada hora de cada dia |
|Um valor de intervalo (`/` operador)|<nobr>`0 */5 * * * *`</nobr>| 12 vezes por hora – em segundo 0 de cada quinto minuto de cada hora de cada dia |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Exemplos de NCRONTAB

Aqui estão alguns exemplos de expressões NCRONTAB que você pode usar para o gatilho de temporizador no Azure Functions.

| Exemplo            | Quando disparado                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | uma vez a cada cinco minutos            |
| `0 0 * * * *`      | uma vez a cada hora      |
| `0 0 */2 * * *`    | uma vez a cada duas horas               |
| `0 0 9-17 * * *`   | uma vez a cada hora entre 9h e 17h  |
| `0 30 9 * * *`     | às 9h30 todos os dias               |
| `0 30 9 * * 1-5`   | às 9h30 todo dia útil           |
| `0 30 9 * Jan Mon` | em 9H30 toda segunda-feira em janeiro |

> [!NOTE]
> A expressão NCRONTAB requer um formato de **seis campos** . A posição sexto campo é um valor para segundos que é colocado no início da expressão. Não há suporte para expressões cron de cinco campos no Azure.

### <a name="ncrontab-time-zones"></a>NCRONTAB fuso horário

Os números em uma expressão CRON se referem a uma hora e uma data, não a um período de tempo. Por exemplo, um 5 no campo `hour` se refere a 5h, não cada cinco horas.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>TimeSpan

 É possível usar um `TimeSpan` somente para um aplicativo de função executado em um Plano do Serviço de Aplicativo.

Ao contrário de uma expressão CRON, um valor `TimeSpan` especifica o intervalo de tempo entre cada invocação de função. Quando uma função é concluída após a execução por mais tempo do que o intervalo especificado, o temporizador imediatamente chama a função novamente.

Expresso como uma cadeia de caracteres, o formato `TimeSpan` é `hh:mm:ss` quando `hh` é menor que 24. Quando os dois primeiros dígitos são 24 ou superior, o formato é `dd:hh:mm`. Estes são alguns exemplos:

| Exemplo      | Quando disparado |
|--------------|----------------|
| "01:00:00"   | a cada hora     |
| "00:01:00"   | a cada minuto   |
| "25:00:00"   | a cada 25 dias  |
| "1,00:00:00" | Todos os dias      |

## <a name="scale-out"></a>Escalabilidade horizontal

Se um aplicativo de funções se expandir para várias instâncias, apenas uma única instância de uma função disparada por temporizador será executada em todas as instâncias. Ele não será disparado novamente se houver uma invocação pendente ainda em execução.

## <a name="function-apps-sharing-storage"></a>Armazenamento de compartilhamento de aplicativos de função

Se você estiver compartilhando contas de armazenamento entre aplicativos de funções que não são implantados no serviço de aplicativo, talvez seja necessário atribuir explicitamente a ID do host a cada aplicativo.

| Versão do Functions | Configuração                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (e superior)  | A variável de ambiente `AzureFunctionsWebHost__hostid` |
| 1.x               | `id` em *host.jsem*                                  |

Você pode omitir o valor de identificação ou definir manualmente cada aplicativo de função que identifica a configuração para um valor diferente.

O gatilho de temporizador usa um bloqueio de armazenamento para garantir que haja apenas uma instância de temporizador quando um aplicativo de funções é dimensionado para várias instâncias. Se dois aplicativos de funções compartilharem a mesma configuração de identificação e cada um usar um gatilho de temporizador, apenas um temporizador será executado.

## <a name="retry-behavior"></a>Tentar comportamento novamente

Ao contrário do gatilho de fila, o gatilho de temporizador não tenta novamente após a falha de uma função. Quando uma função falha, ele não é chamado novamente até a próxima vez na agenda.

## <a name="troubleshooting"></a>Solução de problemas

Para obter informações sobre o que fazer quando o gatilho de timer não funcionar conforme o esperado, confira [Investigar e relatar problemas com funções disparadas de timer não acionadas](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Vá para um guia de início rápido que use um gatilho de temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
