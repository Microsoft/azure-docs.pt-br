---
title: Exibir logs de atividade para alterações do RBAC do Azure
description: Exibir logs de atividade para o controle de acesso baseado em função (RBAC do Azure) para recursos do Azure nos últimos 90 dias.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 3e4d75c59d0e3c97b0900dd72a27d4afbf4015c9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492311"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Exibir logs de atividade para alterações do RBAC do Azure

Às vezes, você precisa de informações sobre alterações do Azure RBAC (controle de acesso baseado em função), como para fins de auditoria ou solução de problemas. Sempre que alguém fizer alterações nas atribuições de função ou definições de função em suas assinaturas, as alterações serão registradas no [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md). Você pode exibir os logs de atividade para ver todas as alterações do RBAC do Azure nos últimos 90 dias.

## <a name="operations-that-are-logged"></a>Operações registradas em log

Aqui estão as operações relacionadas ao RBAC do Azure que são registradas no log de atividades:

- Criar atribuição de função
- Excluir atribuição de função
- Criar ou atualizar definição de função personalizada
- Excluir definição de função personalizada

## <a name="azure-portal"></a>Portal do Azure

A maneira mais fácil para começar é exibir os logs de atividades com o portal do Azure. A captura de tela a seguir mostra um exemplo de operações de atribuição de função no log de atividades. Ele também inclui uma opção para baixar os logs como um arquivo CSV.

![Logs de atividade usando o portal – captura de tela](./media/change-history-report/activity-log-portal.png)

O log de atividades no portal tem vários filtros. Aqui estão os filtros relacionados ao RBAC do Azure:

| Filtrar | Valor |
| --------- | --------- |
| Categoria de evento | <ul><li>Administrativa</li></ul> |
| Operação | <ul><li>Criar atribuição de função</li><li>Excluir atribuição de função</li><li>Criar ou atualizar definição de função personalizada</li><li>Excluir definição de função personalizada</li></ul> |

Para obter mais informações sobre logs de atividade, consulte [Exibir logs de atividades para monitorar ações em recursos](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para exibir logs de atividade com o Azure PowerShell, use o comando [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog).

Esse comando abaixo lista todas as alterações de atribuição de função em uma assinatura nos os últimos sete dias:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Esse comando abaixo lista todas as definição de atribuição de função em um grupo de recursos nos os últimos sete dias:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Esse comando lista todas as alterações de atribuição de função e de definição de função em uma assinatura para os últimos sete dias e exibe os resultados em uma lista:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Se você estiver usando uma entidade de serviço para criar atribuições de função, a propriedade do chamador será uma ID de objeto. Você pode usar [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) para obter informações sobre a entidade de serviço.

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>CLI do Azure

Para exibir logs de atividade com a CLI do Azure, use o comando [lista de log de atividades do monitor az](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Esse comando lista os logs de atividade em um grupo de recursos de 27 de fevereiro, olhando sete dias para frente:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Este comando lista os logs de atividade do provedor de recursos de autorização de 27 de fevereiro, olhando sete dias para frente:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Logs do Azure Monitor

[Os logs de Azure monitor](../azure-monitor/log-query/log-query-overview.md) é outra ferramenta que você pode usar para coletar e analisar as alterações do RBAC do Azure para todos os seus recursos do Azure. Os logs de Azure Monitor têm os seguintes benefícios:

- Escrever consultas complexas e lógica
- Integrar com alertas, Power BI e outras ferramentas
- Salvar dados por períodos de retenção mais longos
- Referência cruzada com outros logs, como segurança, máquina virtual e personalizados

Aqui estão as etapas básicas para começar:

1. [Crie um espaço de trabalho log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Configurar a solução Análise do Log de Atividades](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution) para seu workspace.

1. [Exibir os logs de atividade](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution). Uma maneira rápida de navegar até a página de visão geral da solução Análise do Log de Atividades é clicar na opção **logs** .

   ![Opção de logs de Azure Monitor no portal](./media/change-history-report/azure-log-analytics-option.png)

1. Opcionalmente, use o [Azure Monitor log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) para consultar e exibir os logs. Para obter mais informações, consulte Introdução [às consultas de log de Azure monitor](../azure-monitor/log-query/get-started-queries.md).

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
* [Exibir eventos no log de atividades](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorar a atividade da assinatura com o Log de Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md)