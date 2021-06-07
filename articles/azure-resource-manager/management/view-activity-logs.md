---
title: Exibir os logs de atividades do Azure para monitorar recursos
description: Use os logs de atividade para examinar erros e ações do usuário. Mostra portal do Azure PowerShell, CLI do Azure e REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 14015e9b2792515e6818af551b8bd9f54c686bee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91371585"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Exibir logs de atividade para monitorar ações em recursos

Com os logs de atividade, você pode determinar:

* quais operações foram executadas nos recursos em sua assinatura
* quem iniciou a operação
* quando a operação ocorreu
* o status da operação
* os valores de outras propriedades que podem ajudar você a pesquisar a operação

O log de atividades contém todas as operações de gravação (PUT, POST, DELETE) de seus recursos. Ele não inclui operações de leitura (GET). Para obter uma lista de ações de recursos, consulte [operações do provedor de recursos do Azure](../../role-based-access-control/resource-provider-operations.md). É possível usar os logs de atividade para encontrar um erro ao solucionar problemas ou para monitorar como um usuário de sua organização modificou um recurso.

Os logs de atividades são mantidos por 90 dias. Você pode consultar qualquer intervalo de datas, desde que a data inicial não seja anterior a 90 dias no passado.

Você pode recuperar informações dos logs de atividade por meio do Portal, do PowerShell, da CLI do Azure, da API REST do Insights ou da [Biblioteca .NET do Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Portal do Azure

Para exibir os logs de atividade por meio do portal, siga estas etapas:

1. No menu portal do Azure, selecione **Monitor** ou pesquise e selecione **Monitor** em qualquer página.

    ![Selecionar monitor](./media/view-activity-logs/select-monitor-from-menu.png)

1. Selecione **log de atividades**.

    ![Selecionar o log de atividades](./media/view-activity-logs/select-activity-log.png)

1. Você verá um resumo das operações recentes. Um conjunto padrão de filtros é aplicado às operações. Observe que as informações no resumo incluem quem iniciou a ação e quando ela aconteceu.

    ![Exibir o resumo das operações recentes](./media/view-activity-logs/audit-summary.png)

1. Para executar rapidamente um conjunto predefinido de filtros, selecione **insights rápidos**.

    ![Selecionar insights rápidos](./media/view-activity-logs/select-quick-insights.png)

1. Selecione uma das opções. Por exemplo, selecione **implantações com falha** para ver erros de implantações.

    ![Selecionar implantações com falha](./media/view-activity-logs/select-failed-deployments.png)

1. Observe que os filtros foram alterados para se concentrar em erros de implantação nas últimas 24 horas. Somente as operações que correspondem aos filtros são exibidas.

    ![Exibir filtros](./media/view-activity-logs/view-filters.png)

1. Para se concentrar em operações específicas, mude os filtros ou aplique novos. Por exemplo, a imagem a seguir mostra um novo valor para **Intervalo de Tempo**, e **Tipo de Recurso** está definido como contas de armazenamento.

    ![Definir opções de filtragem](./media/view-activity-logs/set-filter.png)

1. Se você precisar executar a consulta novamente mais tarde, selecione **Fixar filtros atuais**.

    ![Fixar filtros](./media/view-activity-logs/pin-filters.png)

1. Nomeie o filtro.

    ![Nomear filtros](./media/view-activity-logs/name-filters.png)

1. O filtro está disponível no painel. No menu do portal do Azure, selecione **Painel**.

    ![Mostrar filtro no painel](./media/view-activity-logs/activity-log-on-dashboard.png)

1. No portal, você pode exibir as alterações em um recurso. Volte para o modo de exibição padrão no monitor e selecione uma operação que envolvia a alteração de um recurso.

    ![Selecionar operação](./media/view-activity-logs/select-operation.png)

1. Selecione **histórico de alterações (versão prévia)** e escolha uma das operações disponíveis.

    ![Selecionar histórico de alterações](./media/view-activity-logs/select-change-history.png)

1. As alterações no recurso são exibidas.

    ![Mostrar alterações](./media/view-activity-logs/show-changes.png)

Para saber mais sobre o histórico de alterações, consulte [obter alterações de recurso](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para recuperar as entradas de log, execute o comando **Get-AzLog**. Forneça parâmetros adicionais para filtrar a lista de entradas. Se você não especificar uma hora de início e de término, as entradas dos últimos sete dias serão retornadas.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

O exemplo a seguir mostra como usar o log de atividades para operações de pesquisa executadas durante um período especificado. As datas de início e de término são especificadas em um formato de data.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Outra opção é usar funções de data para especificar o intervalo de datas, como os últimos 14 dias.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Você pode pesquisar as ações executadas por um usuário específico.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Você pode filtrar para mostrar operações com falha.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Você pode focar em um erro examinando a mensagem de status dessa entrada.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Você pode selecionar valores específicos para limitar os dados que são retornados.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Dependendo da hora de início que você especificar, os comandos anteriores poderão retornar uma longa lista de operações para o grupo de recursos. Você pode, pelo fornecimento de critérios de pesquisa, filtrar os resultados para o que você está procurando. Por exemplo, você pode filtrar por tipo de operação.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Você pode usar o grafo de recursos para ver o histórico de alterações de um recurso. Para obter mais informações, consulte [obter alterações de recurso](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>CLI do Azure

Para recuperar as entradas de log, execute o comando [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) com um deslocamento para indicar o período de tempo.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

O exemplo a seguir mostra como usar o log de atividades para operações de pesquisa executadas durante um período especificado. As datas de início e de término são especificadas em um formato de data.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Você pode procurar as ações realizadas por um determinado usuário, mesmo para um grupo de recursos que não existe mais.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Você pode filtrar para mostrar operações com falha.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Você pode focar em um erro examinando a mensagem de status dessa entrada.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Você pode selecionar valores específicos para limitar os dados que são retornados.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Dependendo da hora de início que você especificar, os comandos anteriores poderão retornar uma longa lista de operações para o grupo de recursos. Você pode, pelo fornecimento de critérios de pesquisa, filtrar os resultados para o que você está procurando. Por exemplo, você pode filtrar por tipo de operação.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Você pode usar o grafo de recursos para ver o histórico de alterações de um recurso. Para obter mais informações, consulte [obter alterações de recurso](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>API REST

As operações de REST para trabalhar com o log de atividade fazem parte da [API REST do Insights](/rest/api/monitor/). Para recuperar os eventos de log de atividade, confira [Listar os eventos de gerenciamento em uma assinatura](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Próximas etapas

* Os logs de atividade do Azure podem ser usados com o Power BI para obter mais informações sobre as ações em sua assinatura. Confira [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)(Exibir e analisar logs de atividade do Azure no Power BI e muito mais).
* Para saber mais sobre como definir políticas de segurança, consulte [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md).
* Para exibir mais detalhes sobre as alterações em seus aplicativos da camada de infraestrutura até a implantação do aplicativo, consulte [usar a análise de alterações do aplicativo no Azure monitor](../../azure-monitor/app/change-analysis.md).
* Para saber mais sobre os comandos para exibir as operações de implantação, consulte [Exibir operações de implantação](../templates/deployment-history.md).
* Para saber como impedir exclusões em um recurso para todos os usuários, confira [Bloquear recursos com o Azure Resource Manager](lock-resources.md).
* Para ver a lista de operações disponíveis para cada provedor Microsoft Azure Resource Manager, consulte [operações do provedor de recursos do Azure](../../role-based-access-control/resource-provider-operations.md)
