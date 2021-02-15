---
title: Solucionar problemas de alertas de log no Azure Monitor | Microsoft Docs
description: Problemas comuns, erros e resoluções para regras de alerta de log no Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: e9a1bef582053eccdbfef63c2159cf540ffd9bfb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186585"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Solucionar problemas de alertas de log no Azure Monitor  

Este artigo mostra como resolver problemas comuns com alertas de log no Azure Monitor. Ele também fornece soluções para problemas comuns com a funcionalidade e a configuração de alertas de log.

Os alertas de log permitem que os usuários usem uma consulta [log Analytics](../log-query/log-analytics-tutorial.md) para avaliar os logs de recursos a cada frequência definida e acionar um alerta com base nos resultados. As regras podem disparar uma ou mais ações usando [grupos de ações](./action-groups.md). [Saiba mais sobre a funcionalidade e a terminologia de alertas de log](alerts-unified-log.md).

> [!NOTE]
> Este artigo não considera casos em que a portal do Azure mostra uma regra de alerta disparada e uma notificação não é executada por um grupo de ação associado. Para esses casos, consulte os detalhes sobre solução de problemas [aqui](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

## <a name="log-alert-didnt-fire"></a>Alerta de log não acionado

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para logs

O Azure Monitor processa terabytes de logs de clientes de todo o mundo, o que pode causar a [latência de ingestão de logs](./data-ingestion-time.md).

Os logs são dados semiestruturados e inerentemente mais Lates do que as métricas. Se você estiver enfrentando um atraso de mais de 4 minutos em alertas acionados, considere o uso de [alertas de métrica](alerts-metric-overview.md). Você pode enviar dados para o repositório de métricas de logs usando [alertas de métricas para logs](alerts-metric-logs.md).

O sistema tenta novamente a avaliação do alerta várias vezes para reduzir a latência. Depois que os dados chegam, o alerta é disparado, o que, na maioria dos casos, não é igual ao tempo de registro de log.

### <a name="incorrect-query-time-range-configured"></a>Intervalo de tempo de consulta incorreto configurado

O intervalo de tempo de consulta é definido na definição de condição de regra. Esse campo é chamado **período** para espaços de trabalho e Application insights e chamado **intervalo de tempo de consulta de substituição** para todos os outros tipos de recursos. Assim como no log Analytics, o intervalo de tempo limita os dados de consulta ao período especificado. Mesmo que o comando **atrás** seja usado na consulta, o intervalo de tempo será aplicado. 

Por exemplo, uma consulta examina 60 minutos, quando o intervalo de tempo é de 60 minutos, mesmo se o texto contiver **atrás (1D)**. O intervalo de tempo e a filtragem de tempo de consulta precisam corresponder. No caso de exemplo, alterar o **Period**  /  **intervalo de tempo de consulta de substituição** de período para um dia funcionaria como esperado.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>As ações estão sem áudio na regra de alerta

Os alertas de log fornecem uma opção para ativar mudo de ações de alerta para um determinado período de tempo. Esse campo é chamado de **supressão de alertas** em espaços de trabalho e Application insights. Em todos os outros tipos de recursos, ele é chamado de **ações de mudo**. 

Um problema comum é que você imagina que o alerta não acionasse as ações devido a um problema de serviço. Ainda assim, a configuração da regra foi mudo.

![Suprimir alertas](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Regra de alerta de medição métrica com divisão usando a API de Log Analytics herdada

[Medição de métrica](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) é um tipo de alerta de log baseado em resultados de série temporal resumidos. Essas regras permitem agrupar por colunas para [dividir os alertas](alerts-unified-log.md#split-by-alert-dimensions). Se você estiver usando a API de Log Analytics herdada, a divisão não funcionará conforme o esperado. Não há suporte para a escolha do agrupamento na API herdada.

A API ScheduledQueryRules atual permite que você defina a **agregação** em regras de [medição métricas](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) , que funcionarão conforme o esperado. [Saiba mais sobre como alternar para a API ScheduledQueryRules atual](alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>Alerta de log disparado desnecessariamente

Uma [regra de alerta de log](./alerts-log.md) configurada no Azure monitor pode ser disparada inesperadamente. As seções a seguir descrevem alguns motivos comuns.

### <a name="alert-triggered-by-partial-data"></a>Alerta disparado por dados parciais

O Azure Monitor processa terabytes de logs de clientes de todo o mundo, o que pode causar a [latência de ingestão de logs](./data-ingestion-time.md).

Os logs são dados semiestruturados e inerentemente mais Lates do que as métricas. Se você estiver enfrentando muitos incêndios inadequados em alertas acionados, considere o uso de [alertas de métrica](alerts-metric-overview.md). Você pode enviar dados para o repositório de métricas de logs usando [alertas de métricas para logs](alerts-metric-logs.md).

Os alertas de log funcionam melhor quando você tenta detectar dados nos logs. Ele funciona menos bem quando você tenta detectar falta de dados nos logs. Por exemplo, alertas na pulsação da máquina virtual. 

Embora haja recursos internos para evitar falsos alertas, eles ainda podem ocorrer em dados muito latentes (mais de aproximadamente 30 minutos) e dados com picos de latência.

### <a name="query-optimization-issues"></a>Problemas de otimização de consulta

O serviço de alerta altera sua consulta para otimizar a latência de alerta e carga inferior. O fluxo de alertas foi criado para transformar os resultados que indicam o problema para um alerta. Por exemplo, em um caso de uma consulta como:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Se a intenção do usuário for alertar, quando esse tipo de evento ocorrer, a lógica de alerta acrescentará `count` à consulta. A consulta que será executada será:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Não há necessidade de adicionar lógica de alerta à consulta e fazer isso pode até mesmo causar problemas. No exemplo acima, se você incluir `count` em sua consulta, ela sempre resultará no valor 1, pois o serviço de alerta fará `count` do `count` .

A consulta otimizada é o que o serviço de alerta de log executa. Você pode executar a consulta modificada no [portal](../log-query/log-query-overview.md) log Analytics ou na [API](/rest/api/loganalytics/).

Para espaços de trabalho e Application Insights, ele é chamado de **consulta a ser executada** no painel condição. Em todos os outros tipos de recursos, selecione **Ver consulta de alerta final** na guia condição.

![Consulta a ser executada](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>O alerta de log foi desabilitado

As seções a seguir listam alguns motivos pelos quais Azure Monitor pode desabilitar uma regra de alerta de log. Também incluímos um [exemplo do log de atividades que é enviado quando uma regra é desabilitada](#activity-log-example-when-rule-is-disabled).

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>O escopo do alerta não existe mais ou foi movido

Quando os recursos de escopo de uma regra de alerta não forem mais válidos, a execução da regra falhará. Nesse caso, a cobrança também é interrompida.

Azure Monitor desabilitará o alerta de log após uma semana se ele falhar continuamente.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>A consulta usada em um alerta de log não é válida

Quando uma regra de alerta de log é criada, a consulta é validada para a sintaxe correta. Mas, às vezes, a consulta fornecida na regra de alerta de log pode começar a falhar. Alguns motivos comuns são:

- As regras foram criadas por meio da API e a validação foi ignorada pelo usuário.
- A consulta [é executada em vários recursos](../log-query/cross-workspace-query.md) e um ou mais dos recursos foram excluídos ou movidos.
- A [consulta falha](https://dev.loganalytics.io/documentation/Using-the-API/Errors) porque:
    - A solução de log não foi [implantada no espaço de trabalho](../insights/solutions.md#install-a-monitoring-solution), portanto, as tabelas não são criadas.
    - Os dados interromperam o fluxo para uma tabela na consulta por mais de 30 dias.
    - As [tabelas de logs personalizados](data-sources-custom-logs.md) ainda não foram criadas, pois o fluxo de dados não foi iniciado.
- As alterações na [linguagem de consulta](/azure/kusto/query/) incluem um formato revisado para comandos e funções. Portanto, a consulta fornecida anteriormente não é mais válida.

O [Azure Advisor](../../advisor/advisor-overview.md) avisa você sobre esse comportamento. Ele adiciona uma recomendação sobre a regra de alerta de log afetada. A categoria usada é ' alta disponibilidade ' com impacto médio e uma descrição de ' reparar sua regra de alerta de log para garantir o monitoramento '.

## <a name="alert-rule-quota-was-reached"></a>A cota da regra de alerta foi atingida

O número de regras de alerta da pesquisa de logs por assinatura e recurso está sujeito aos limites de cota descritos [aqui](../service-limits.md).

### <a name="recommended-steps"></a>Etapas Recomendadas
    
Se você atingiu o limite de cota, as etapas a seguir podem ajudar a resolver o problema.

1. Tente excluir ou desabilitar as regras de alerta de pesquisa de logs que não são mais usadas.
1. Tente usar a [divisão de alertas por dimensões](alerts-unified-log.md#split-by-alert-dimensions) para reduzir a contagem de regras. Essas regras podem monitorar muitos recursos e casos de detecção.
1. Se você precisar que o limite de cota seja aumentado, continue a abrir uma solicitação de suporte e forneça as seguintes informações:

    - IDs de assinatura e IDs de recurso para os quais o limite de cota precisa ser aumentado.
    - Motivo para aumento de cota.
    - Tipo de recurso para o aumento de cota: **log Analytics**, **Application insights** e assim por diante.
    - Limite de cota solicitado.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Para verificar o uso atual de novas regras de alerta de log
    
#### <a name="from-the-azure-portal"></a>No portal do Azure

1. Abra a tela *alertas* e selecione *gerenciar regras de alerta*
2. Filtrar para a assinatura relevante usando o controle suspenso *Assinatura*
3. Certifique-se de não filtrar para um grupo de recursos, tipo de recurso ou recurso específico
4. No controle suspenso *Tipo de sinal*, selecione 'Pesquisa de logs'
5. Verifique se o controle suspenso *Status* está definido como 'Habilitado'
6. O número total de regras de alerta de pesquisa de logs será exibido acima da lista de regras

#### <a name="from-api"></a>Na API

- PowerShell- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- API REST - [Lista por assinatura](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Exemplo de log de atividades quando a regra está desabilitada

Se a consulta falhar por sete dias continuamente, Azure Monitor desabilitará o alerta do log e interromperá a cobrança da regra. Você pode descobrir o momento exato em que Azure Monitor desabilitou o alerta de log no [log de atividades do Azure](../../azure-resource-manager/management/view-activity-logs.md). Consulte este exemplo:

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [alertas de log no Azure](./alerts-unified-log.md).
- Saiba mais sobre como [configurar alertas de log](../log-query/log-query-overview.md).
- Saiba mais sobre [consultas de log](../log-query/log-query-overview.md).