---
title: Gatilho de temporizador do Azure Functions | Microsoft Docs
description: Entenda como usar gatilhos de temporizador no Azure Functions.
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: ed4afa2b946fe11d5802a95f8a490f3419dc0f39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-timer-trigger"></a>Gatilho de temporizador do Azure Functions

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar gatilhos de temporizador no Azure Functions. O Azure Functions tem uma associação de gatilho de timer que permite executar o código de função com base em um agendamento definido. 

O gatilho de timer dá suporte à expansão em várias instâncias. Uma única instância de uma função específica de timer é executada em todas as instâncias.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>Gatilho de temporizador
O gatilho de timer de uma função usa o seguinte objeto JSON na matriz `bindings` de function.json:

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

O valor de `schedule` é uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos: 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>Muitas das expressões cron encontradas online omitem o campo `{second}`. Se você copiar de um deles, precisará ajustá-lo para o campo `{second}`. Para obter exemplos específicos, consulte [Agendar exemplos](#examples) abaixo.

O fuso horário padrão usado com as expressões CRON é a Hora Universal Coordenada (UTC). Para que a expressão CRON se baseie em outro fuso horário, crie uma nova configuração de aplicativo para o aplicativo de funções denominada `WEBSITE_TIME_ZONE`. Defina o valor para o nome do fuso horário desejado, conforme mostrado no [Índice de fuso horário da Microsoft](https://msdn.microsoft.com/library/ms912391.aspx). 

Por exemplo, a *Hora padrão da costa leste dos EUA* é UTC-05:00. Para que o timer dispare às 10:00 AM EST diariamente, use a seguinte expressão CRON que considera o fuso horário UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Como alternativa, você pode adicionar uma nova configuração de aplicativo para seu aplicativo de funções denominada `WEBSITE_TIME_ZONE` e definir o valor como **Horário padrão da costa leste dos EUA**.  Em seguida, a seguinte expressão CRON poderia ser usada para 10:00 EST: 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>Exemplos de agendamento
Aqui estão alguns exemplos de expressões CRON que você pode usar para a propriedade `schedule`. 

Para disparar uma vez a cada cinco minutos:

```json
"schedule": "0 */5 * * * *"
```

Para disparar uma vez a cada hora:

```json
"schedule": "0 0 * * * *",
```

Para disparar uma vez a cada duas horas:

```json
"schedule": "0 0 */2 * * *",
```

Para disparar uma vez a cada hora das 9h às 17h:

```json
"schedule": "0 0 9-17 * * *",
```

Para disparar às 9h30 todos os dias:

```json
"schedule": "0 30 9 * * *",
```

Para disparar às 9h30 todos os dias da semana:

```json
"schedule": "0 30 9 * * 1-5",
```

<a name="usage"></a>

## <a name="trigger-usage"></a>Uso de gatilho
Quando uma função de gatilho de timer é chamada, o [objeto timer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) é passado para a função. O JSON a seguir é uma representação de exemplo do objeto timer. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

<a name="sample"></a>

## <a name="trigger-sample"></a>Exemplo de gatilho
Suponha que você tenha o seguinte gatilho de timer na matriz `bindings` de function.json:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Consulte o exemplo específico da linguagem que lê o objeto de timer para ver se ele está atrasado.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemplo de gatilho em C# #
```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Exemplo de gatilho em F# #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemplo de gatilho em Node.js
```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

