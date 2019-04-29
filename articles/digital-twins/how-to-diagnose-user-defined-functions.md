---
title: Como depurar UDFs nos Gêmeos Digitais do Azure | Microsoft Docs
description: Diretrizes sobre como depurar UDFs em Gêmeos Digitais do Azure.
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: 6122cd4507ed0883d1b78ca519269c25098e55ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924849"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Como depurar funções definidas pelo usuário em Gêmeos Digitais do Azure

Este artigo resume como diagnosticar e depurar funções definidas pelo usuário. Em seguida, ele identifica alguns dos cenários mais comuns encontrados ao depurá-las.

>[!TIP]
> Leia [Como configurar monitoramento e registro em log](./how-to-configure-monitoring.md) para saber mais sobre a configuração de ferramentas de depuração em Gêmeos Digitais do Azure usando Logs de Atividades, Logs de Diagnóstico e Azure Monitor.

## <a name="debug-issues"></a>Problemas de depuração

Saber como diagnosticar problemas que surgem em sua instância de Gêmeos Digitais do Azure ajuda você a identificar com eficiência o problema, a causa do problema e uma solução.

### <a name="enable-log-analytics-for-your-instance"></a>Habilitar a análise de logs para sua instância

Os logs e métricas para sua instância de Gêmeos Digitais do Azure são exibidos no Azure Monitor. Esta documentação pressupõe que você criou um [registra em log do Azure Monitor](../azure-monitor/log-query/log-query-overview.md) espaço de trabalho por meio de [Portal do Azure](../azure-monitor/learn/quick-create-workspace.md), por meio [CLI do Azure](../azure-monitor/learn/quick-create-workspace-cli.md), ou por meio das [ PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> Você pode enfrentar um atraso de 5 minutos durante o envio de eventos em logs do Azure Monitor pela primeira vez.

Para configurar monitoramento e registro em log de recursos Gêmeos Digitais do Azure, leia [Como configurar monitoramento e registro em log](./how-to-configure-monitoring.md).

Leia o artigo [Coletar e consumir dados de log de seus recursos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md) para definir configurações de log de diagnóstico nos Gêmeos Digitais do Azure por meio do portal do Azure, da CLI do Azure ou do PowerShell.

>[!IMPORTANT]
> Selecione todas as categorias de log, métricas e seu espaço de trabalho do Azure Log Analytics.

### <a name="trace-sensor-telemetry"></a>Sensor de telemetria de rastreamento

Para rastrear telemetria do sensor, verifique se as configurações de diagnóstico estão habilitadas para sua instância dos Gêmeos Digitais do Azure. Em seguida, verifique se todas as categorias de log desejadas estão selecionadas. Por fim, confirme que os logs desejados estão sendo enviados para os logs do Azure Monitor.

Para corresponder a uma mensagem de telemetria do sensor para seus respectivos logs, você pode especificar uma ID de Correlação nos dados de evento que estão sendo enviados. Para fazer isso, defina a propriedade `x-ms-client-request-id` para um GUID.

Depois de enviar a telemetria, abra o log Analytics para consultar os logs usando o conjunto de ID de correlação:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valor da consulta | Substitua por |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | A ID de Correlação que foi especificada nos dados de evento |

Se você habilitar o registro em log para sua função definida pelo usuário, esses logs são exibidos na sua instância do log analytics com a categoria `UserDefinedFunction`. Para recuperá-los, digite a seguinte condição de consulta no log analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Para obter mais informações sobre operações de consulta avançada, leia [Introdução às consultas](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identificar problemas comuns

O diagnóstico e a identificação de problemas comuns são importantes durante a solução de problemas em sua solução. Vários problemas que são comumente encontrados ao desenvolver funções definidas pelo usuário estão resumidos nas subseções a seguir.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Verificar se uma atribuição de função foi criada

Sem uma atribuição de função criada na API de Gerenciamento, a função definida pelo usuário não tem acesso para executar nenhuma ação, como enviar notificações, recuperar metadados e definir valores computadores na topologia.

Verifique se existe uma atribuição de função para sua função definida pelo usuário por meio de sua API de Gerenciamento:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Valor de parâmetro | Substitua por |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | A ID da função definida pelo usuário para a qual serão recuperadas as atribuições de função|

Leia [Como criar uma atribuição de função para a função definida pelo usuário](./how-to-user-defined-functions.md), se nenhuma atribuição de função existir.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Verifique se o correspondente funciona para a telemetria de um sensor

Com a chamada a seguir à API de Gerenciamento das instâncias dos Gêmeos Digitais do Azure, você poderá determinar se um determinado correspondente se aplica para o sensor determinado.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | A ID do correspondente que você deseja avaliar |
| *YOUR_SENSOR_IDENTIFIER* | A ID do sensor que você deseja avaliar |

Resposta:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Verifique o que um sensor dispara

Com a seguinte chamada para as APIs de Gerenciamento dos Gêmeos Digitais do Azure, você pode determinar os identificadores de suas funções definidas pelo usuário disparadas pela telemetria de entrada do sensor determinado:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | A ID do sensor para enviar telemetria |

Resposta:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problema com o recebimento de notificações

Quando você não estiver recebendo notificações da função definida pelo usuário disparada, confirme que seu parâmetro de tipo de objeto de topologia corresponde ao tipo de identificador que está sendo usado.

Exemplo **Incorreto**:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Esse cenário ocorre porque o identificador usado refere-se a um sensor, enquanto o tipo de objeto de topologia especificado é `Space`.

Exemplo **Correto**:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

A maneira mais fácil de não ter esse problema é usar o método `Notify` no objeto de metadados.

Exemplo:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Exceções de diagnóstico comuns

Se habilitar as configurações de diagnóstico, você poderá encontrar estas exceções comuns:

1. **Limitação**: se sua função definida pelo usuário exceder os limites da taxa de execução descritos no artigo [Limites de serviço](./concepts-service-limits.md), ela será limitada. Nenhuma outra operação será executada com êxito até que os limites de limitação expirem.

1. **Dados Não Encontrados**: se sua função definida pelo usuário tentar acessar metadados que não existem, a operação falhará.

1. **Não Autorizado**: se sua função definida pelo usuário não tiver uma atribuição de função definida ou não tiver permissões suficientes para acessar alguns metadados da topologia, a operação falhará.

## <a name="next-steps"></a>Próximas etapas

- Saiba como habilitar [logs e monitoramento](../azure-monitor/platform/activity-logs-overview.md) nos Gêmeos Digitais do Azure.
