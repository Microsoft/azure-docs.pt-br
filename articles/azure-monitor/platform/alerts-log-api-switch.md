---
title: Atualizar para a API de alertas de log de Azure Monitor atual
description: Saiba como alternar para a API de ScheduledQueryRules de alertas de log
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 868a8eb6cf38d471eb9dc1f47c903404d05ffc0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294505"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Atualizar para a API de alertas de log atual da API de alerta de Log Analytics herdado

> [!NOTE]
> Este artigo só é relevante para o público do Azure (**não** para o Azure governamental ou para a nuvem do Azure China).

> [!NOTE]
> Quando um usuário opta por alternar a preferência para a [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) atual, não é possível reverter de volta para a [API de alerta de log Analytics herdada](api-alerts.md)mais antiga.

No passado, os usuários usaram a [API de alerta de log Analytics herdado](api-alerts.md) para gerenciar as regras de alerta de log. Os espaços de trabalho atuais usam a [API ScheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Este artigo descreve os benefícios e o processo de alternar da API herdada para a API atual.

## <a name="benefits"></a>Vantagens

- Modelo único para criação de regras de alerta (anteriormente necessários três modelos separados).
- API única para Log Analytics espaços de trabalho ou recursos de Application Insights.
- [Suporte a cmdlets do PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Alinhamento de severidades com todos os outros tipos de alerta.
- Capacidade de criar um [alerta de log entre espaços de trabalho](../log-query/cross-workspace-query.md) que abrange vários recursos externos, como espaços de trabalho log Analytics ou recursos de Application insights.
- Os usuários podem especificar dimensões para dividir os alertas usando o parâmetro ' Aggregate on '.
- Os alertas de log têm um período estendido de até dois dias de dados (anteriormente limitados a um dia).

## <a name="impact"></a>Impacto

- Todas as novas regras devem ser criadas/editadas com a API atual. Consulte [exemplo de uso por meio do modelo de recurso do Azure](alerts-log-create-templates.md) e [uso de exemplo por meio do PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- À medida que as regras se tornam Azure Resource Manager recursos controlados na API atual e devem ser exclusivas, a ID de recurso de regras será alterada para esta estrutura: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Os nomes de exibição da regra de alerta permanecerão inalterados.

## <a name="process"></a>Processo

O processo de alternância não é interativo e não requer etapas manuais, na maioria dos casos. Suas regras de alerta não são interrompidas ou interrompidas durante ou após o comutador.
Faça essa chamada para mudar todas as regras de alerta associadas ao espaço de trabalho específico do Log Analytics:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Com o corpo da solicitação que contém o JSON abaixo:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Aqui está um exemplo de como usar o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre, que simplifica a invocação da chamada de API acima:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se a opção for bem-sucedida, a resposta será:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Verificar o status de mudança do espaço de trabalho

Você também pode usar essa chamada à API para verificar o status do comutador:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Você também pode usar a ferramenta [ARMClient](https://github.com/projectkudu/ARMClient) :

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se o espaço de trabalho Log Analytics foi alternado para a [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules), a resposta será:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Se o espaço de trabalho Log Analytics não foi alternado, a resposta será:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Monitor – Alertas de log](alerts-unified-log.md).
- Saiba como [gerenciar seus alertas de log usando a API](alerts-log-create-templates.md).
- Saiba como [Gerenciar alertas de log usando o PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Saiba mais sobre a [experiência de alertas do Azure](./alerts-overview.md).
