---
title: Entender os trabalhos de Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – agendar trabalhos para execução em vários dispositivos conectados ao seu Hub IoT. Os trabalhos podem atualizar marcações e propriedades desejadas e invocar métodos diretos em vários dispositivos.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2018
ms.openlocfilehash: aacb0ab69dad45f9ca7655daaae0c2acff0403f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740432"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Agendar trabalhos em vários dispositivos

O Hub IoT permite diversos blocos de construção como [marcas e propriedades do dispositivo gêmeo](iot-hub-devguide-device-twins.md) e [métodos diretos](iot-hub-devguide-direct-methods.md). Normalmente, os aplicativos back-end permitem que administradores e operadores do dispositivo atualizem e interajam com dispositivos IoT em massa e em um horário agendado. Os trabalhos executam atualizações do dispositivo gêmeo e métodos diretos em um conjunto de dispositivos em um horário agendado. Por exemplo, um operador poderia usar um aplicativo de back-end que inicia e controla um trabalho a fim de reinicializar um conjunto de dispositivos no edifício 43, no terceiro andar, em um horário que não interromperia as operações do edifício.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Considere o uso de trabalhos quando precisar agendar e acompanhar o andamento de uma das seguintes atividades em um conjunto de dispositivos:

* Atualizar as propriedades desejadas
* Marcas de atualização
* Chamar métodos diretos

## <a name="job-lifecycle"></a>Ciclo de vida do trabalho

Os trabalhos são iniciados pelo back-end da solução e mantidos pelo Hub IoT. Você pode iniciar um trabalho por meio de um URI voltado para o serviço (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) e consultar o andamento de um trabalho em execução por meio de um URI voltado para o serviço (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Para atualizar o status de trabalhos em execução quando um trabalho é iniciado, execute uma consulta de trabalho.

> [!NOTE]
> Quando você inicia um trabalho, os valores e nomes de propriedade só podem conter caracteres alfanuméricos imprimíveis US-ASCII, exceto pelo seguinte conjunto: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Trabalhos para execução de métodos diretos

O snippet de código a seguir mostra os detalhes da solicitação HTTPS 1.1 para execução de um [método direto](iot-hub-devguide-direct-methods.md) em um conjunto de dispositivos usando um trabalho:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleDirectMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

A condição de consulta também pode ser em uma única ID de dispositivo ou em uma lista de IDs de dispositivo, conforme mostrado nos exemplos a seguir:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

A [Linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md) aborda a linguagem de consulta do Hub IoT em detalhes adicionais.

## <a name="jobs-to-update-device-twin-properties"></a>Trabalhos para atualização das propriedades do dispositivo gêmeo

O snippet de código a seguir mostra os detalhes da solicitação HTTPS 1.1 de atualização das propriedades do dispositivo gêmeo usando um trabalho:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleTwinUpdate",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

## <a name="querying-for-progress-on-jobs"></a>Consultar o andamento dos trabalhos

O snippet de código a seguir mostra os detalhes da solicitação HTTPS 1.1 para consulta de trabalhos:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>
```

O continuationToken é fornecido pela resposta.

É possível consultar o status de execução do trabalho em cada dispositivo usando a [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Propriedades dos trabalhos

A lista a seguir mostra as propriedades e descrições correspondentes que podem ser usadas durante a consulta por trabalhos ou por resultados do trabalho.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| **jobId** |ID fornecida pelo aplicativo para o trabalho. |
| **startTime** |Hora de início fornecida pelo aplicativo (ISO 8601) para o trabalho. |
| **endTime** |Data fornecida pelo Hub IoT (ISO-8601) para a conclusão do trabalho. Válida somente após o trabalho atingir o estado 'concluído'. |
| **tipo** |Tipos de trabalhos: |
| | **scheduledUpdateTwin**: Um trabalho usado para atualizar um conjunto de propriedades desejadas ou marcas. |
| | **scheduledDeviceMethod**: Um trabalho usado para invocar um método de dispositivo em um conjunto de dispositivos gêmeos. |
| **status** |Estado atual do trabalho. Valores possíveis para o status: |
| | **pendente**: Agendado e aguardando ser selecionado pelo serviço do trabalho. |
| | **scheduled**: Agendado para um horário no futuro. |
| | **Executando**: Trabalho ativo no momento. |
| | **canceled**: Trabalho foi cancelado. |
| | **failed**: Falha no trabalho. |
| | **Concluído**: Trabalho foi concluído. |
| **deviceJobStatistics** |Estatísticas sobre a execução do trabalho. |
| | Propriedades **deviceJobStatistics**: |
| | **deviceJobStatistics.deviceCount**: Número de dispositivos no trabalho. |
| | **deviceJobStatistics.failedCount**: Número de dispositivos nos quais o trabalho falhou. |
| | **deviceJobStatistics.succeededCount**: Número de dispositivos nos quais o trabalho teve êxito. |
| | **deviceJobStatistics.runningCount**: Número de dispositivos que estão executando o trabalho no momento. |
| | **deviceJobStatistics.pendingCount**: Número de dispositivos com execução pendente do trabalho. |

### <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md) descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.

* [Limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.

* [SDKs do dispositivo e do serviço IoT do Azure](iot-hub-devguide-sdks.md) lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.

* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta de Hub IoT. Use essa linguagem de consulta para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.

* O [suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IoT ao protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Para experimentar alguns dos conceitos descritos neste artigo, consulte o tutorial do Hub IoT a seguir:

* [Agendar e transmitir trabalhos](iot-hub-node-node-schedule-jobs.md)