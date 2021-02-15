---
title: Monitorar recursos delegados em escala
description: Saiba como usar efetivamente os logs de Azure Monitor de maneira escalonável nos locatários do cliente que você está gerenciando.
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: f3a789c855f7b05d24cdacd0fb31ee7d6d3e188b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379226"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorar recursos delegados em escala

Como um provedor de serviços, você pode ter integrado vários locatários de clientes ao [Azure Lighthouse](../overview.md). O Azure Lighthouse permite que os provedores de serviços realizem operações em escala em vários locatários ao mesmo tempo, tornando as tarefas de gerenciamento mais eficientes.

Este tópico mostra como usar [os logs de Azure monitor](../../azure-monitor/platform/data-platform-logs.md) de maneira escalonável nos locatários do cliente que você está gerenciando. Embora possamos nos referimos a provedores de serviços e clientes neste tópico, essa orientação também se aplica a [empresas que usam o Azure Lighthouse para gerenciar vários locatários](../concepts/enterprise.md).

> [!NOTE]
> Verifique se os usuários em seus locatários de gerenciamento receberam as [funções necessárias para gerenciar espaços de trabalho de log Analytics](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) em suas assinaturas de cliente delegadas.

## <a name="create-log-analytics-workspaces"></a>Criar Log Analytics espaços de trabalho

Para coletar dados, você precisará criar Log Analytics espaços de trabalho. Esses espaços de trabalho Log Analytics são ambientes exclusivos para dados coletados pelo Azure Monitor. Cada workspace tem seu próprio repositório de dados e configuração. As fontes de dados e as soluções são configuradas para armazenar seus dados em um determinado workspace.

É recomendável criar esses espaços de trabalho diretamente nos locatários do cliente. Dessa forma, seus dados permanecem em seus locatários em vez de serem exportados para o seu. Isso também permite o monitoramento centralizado de quaisquer recursos ou serviços com suporte pelo Log Analytics, proporcionando a você mais flexibilidade sobre os tipos de dados que você monitora.

> [!TIP]
> Qualquer conta de automação usada para acessar dados de um espaço de trabalho Log Analytics deve ser criada no mesmo locatário que o espaço de trabalho.

Você pode criar um espaço de trabalho Log Analytics usando o [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md), usando [CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)ou usando [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md).

> [!IMPORTANT]
> Mesmo que todos os espaços de trabalho sejam criados no locatário do cliente, o provedor de recursos Microsoft. insights também deve ser registrado em uma assinatura no locatário de gerenciamento.

## <a name="deploy-policies-that-log-data"></a>Implantar políticas que registram dados

Depois de criar seus espaços de trabalho do Log Analytics, você pode implantar [Azure Policy](../../governance/policy/index.yml) em suas hierarquias de clientes para que os dados de diagnóstico sejam enviados para o espaço de trabalho apropriado em cada locatário. As políticas exatas que você implanta podem variar dependendo dos tipos de recursos que você deseja monitorar.

Para saber mais sobre a criação de políticas, consulte [tutorial: criar e gerenciar políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md). Essa [ferramenta de comunidade](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) fornece um script para ajudá-lo a criar políticas para monitorar os tipos de recursos específicos que você escolher.

Quando você determinar quais políticas implantar, poderá [implantá-las em suas assinaturas delegadas em escala](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analisar os dados coletados

Depois de implantar suas políticas, os dados serão registrados nos espaços de trabalho do Log Analytics que você criou em cada locatário do cliente. Para obter informações sobre todos os clientes gerenciados, você pode usar ferramentas como [pastas de trabalho do Azure monitor](../../azure-monitor/platform/workbooks-overview.md) para coletar e analisar dados a partir de várias fontes.

## <a name="view-alerts-across-customers"></a>Exibir alertas entre clientes

Você pode exibir [alertas](../../azure-monitor/platform/alerts-overview.md) para as assinaturas delegadas nos locatários do cliente que você gerencia.

Do seu locatário de gerenciamento, você pode [criar, exibir e gerenciar alertas do log de atividades](../../azure-monitor/platform/alerts-activity-log.md) no portal do Azure ou por meio de APIs e ferramentas de gerenciamento.

Para atualizar alertas automaticamente em vários clientes, use uma consulta [do grafo de recursos do Azure](../../governance/resource-graph/overview.md) para filtrar alertas. Você pode fixar a consulta ao seu painel e selecionar todos os clientes e assinaturas apropriados. Por exemplo, a consulta abaixo exibirá os alertas de severidade 0 e 1, atualizando a cada 60 minutos.

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>Próximas etapas

- Experimente os [logs de atividade por](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) pasta de trabalho do domínio no github.
- Explore esta [pasta de trabalho de exemplo criada pelo MVP](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks), que controla os relatórios de conformidade de patch [consultando gerenciamento de atualizações logs](../../automation/update-management/query-logs.md) em vários espaços de trabalho do log Analytics. 
- Saiba mais sobre outras [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
