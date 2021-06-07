---
title: Exibir logs de atividade para alterações do RBAC do Azure
description: Exibir logs de atividade para as alterações do Azure RBAC (controle de acesso baseado em função) nos últimos 90 dias.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042106"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Exibir logs de atividade para alterações do RBAC do Azure

Às vezes, você precisa de informações sobre alterações do Azure RBAC (controle de acesso baseado em função), como para fins de auditoria ou solução de problemas. Sempre que alguém fizer alterações nas atribuições de função ou definições de função em suas assinaturas, as alterações serão registradas no [log de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md). Você pode exibir os logs de atividade para ver todas as alterações do RBAC do Azure nos últimos 90 dias.

## <a name="operations-that-are-logged"></a>Operações registradas em log

Aqui estão as operações relacionadas ao RBAC do Azure que são registradas no log de atividades:

- Criar atribuição de função
- Excluir atribuição de função
- Criar ou atualizar definição de função personalizada
- Excluir definição de função personalizada

## <a name="azure-portal"></a>Portal do Azure

A maneira mais fácil para começar é exibir os logs de atividades com o portal do Azure. A captura de tela a seguir mostra um exemplo de operações de atribuição de função no log de atividades. Ele também inclui uma opção para baixar os logs como um arquivo CSV.

![Logs de atividade usando o portal – captura de tela](./media/change-history-report/activity-log-portal.png)

Para obter mais informações, clique em uma entrada para abrir o painel Resumo. Clique na guia **JSON** para obter um log detalhado.

![Logs de atividade usando o portal com o painel Resumo tela de captura de tela aberta](./media/change-history-report/activity-log-summary-portal.png)

O log de atividades no portal tem vários filtros. Aqui estão os filtros relacionados ao RBAC do Azure:

| Filtrar | Valor |
| --------- | --------- |
| Categoria de evento | <ul><li>Administrativa</li></ul> |
| Operação | <ul><li>Criar atribuição de função</li><li>Excluir atribuição de função</li><li>Criar ou atualizar definição de função personalizada</li><li>Excluir definição de função personalizada</li></ul> |

Para obter mais informações sobre logs de atividade, consulte [Exibir logs de atividades para monitorar ações em recursos](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).


## <a name="interpret-a-log-entry"></a>Interpretar uma entrada de log

A saída de log da guia JSON, Azure PowerShell ou CLI do Azure pode incluir muitas informações. Aqui estão algumas das propriedades de chave a serem procuradas ao tentar interpretar uma entrada de log. Para obter maneiras de filtrar a saída de log usando Azure PowerShell ou CLI do Azure, consulte as seções a seguir.

> [!div class="mx-tableFixed"]
> | Propriedade | Valores de exemplo | Descrição |
> | --- | --- | --- |
> | autorização: ação | Microsoft.Authorization/roleAssignments/write | Criar atribuição de função |
> |  | Microsoft.Authorization/roleAssignments/delete | Excluir atribuição de função |
> |  | Microsoft.Authorization/roleDefinitions/write | Criar ou atualizar definição de função |
> |  | Microsoft.Authorization/roleDefinitions/delete | Excluir definição de função |
> | autorização: escopo | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Escopo da ação |
> | chamador | admin@example.com<br/>ObjectID | Quem iniciou a ação |
> | eventTimestamp | 2021-03-01T22:07:41.126243 Z | Hora em que a ação ocorreu |
> | status: valor | Iniciado<br/>Com sucesso<br/>Com falha | Status da ação |

## <a name="azure-powershell"></a>Azure PowerShell

Para exibir logs de atividade com o Azure PowerShell, use o comando [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog).

Esse comando abaixo lista todas as alterações de atribuição de função em uma assinatura nos os últimos sete dias:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Esse comando abaixo lista todas as definição de atribuição de função em um grupo de recursos nos os últimos sete dias:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Filtrar saída do log

A saída de log pode incluir muitas informações. Este comando lista todas as alterações de atribuição de função e definição de função em uma assinatura dos últimos sete dias e filtra a saída:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

Veja a seguir um exemplo da saída de log filtrada ao criar uma atribuição de função:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Se você estiver usando uma entidade de serviço para criar atribuições de função, a propriedade do chamador será uma ID de objeto da entidade de serviço. Você pode usar [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) para obter informações sobre a entidade de serviço.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>CLI do Azure

Para exibir logs de atividade com a CLI do Azure, use o comando [lista de log de atividades do monitor az](/cli/azure/monitor/activity-log#az_monitor_activity_log_list).

Esse comando lista os logs de atividade em um grupo de recursos a partir de 1º de março, olhando sete dias para frente:

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Este comando lista os logs de atividade do provedor de recursos de autorização a partir de 1º de março, observando sete dias:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Filtrar saída do log

A saída de log pode incluir muitas informações. Esse comando lista todas as alterações de atribuição de função e definição de função em uma assinatura olhando sete dias e filtra a saída:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

Veja a seguir um exemplo da saída de log filtrada ao criar uma atribuição de função:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Logs do Azure Monitor

[Os logs de Azure monitor](../azure-monitor/logs/log-query-overview.md) é outra ferramenta que você pode usar para coletar e analisar as alterações do RBAC do Azure para todos os seus recursos do Azure. Os logs de Azure Monitor têm os seguintes benefícios:

- Escrever consultas complexas e lógica
- Integrar com alertas, Power BI e outras ferramentas
- Salvar dados por períodos de retenção mais longos
- Referência cruzada com outros logs, como segurança, máquina virtual e personalizados

Aqui estão as etapas básicas para começar:

1. [Crie um espaço de trabalho log Analytics](../azure-monitor/logs/quick-create-workspace.md).

1. [Configurar a solução Análise do Log de Atividades](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) para seu workspace.

1. [Exibir os logs de atividade](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). Uma maneira rápida de navegar até a página de visão geral da solução Análise do Log de Atividades é clicar na opção **logs** .

   ![Opção de logs de Azure Monitor no portal](./media/change-history-report/azure-log-analytics-option.png)

1. Opcionalmente, use o [Azure Monitor log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) para consultar e exibir os logs. Para obter mais informações, consulte Introdução [às consultas de log em Azure monitor](../azure-monitor/logs/get-started-queries.md).

Aqui está uma consulta que retorna novas atribuições de função organizadas por provedor de recursos de destino:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Aqui está uma consulta que retorna as alterações de atribuição de função exibidas em um gráfico:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Logs de atividade usando o portal Advanced Analytics - captura de tela](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Próximas etapas
* [Exibir logs de atividade para monitorar ações em recursos](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorar a atividade de assinatura com o log de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md)