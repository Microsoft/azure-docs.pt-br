---
title: Mudar da API herdada de alertas do Log Analytics para a nova API de alertas do Azure
description: Visão geral da desativação da API herdada de alerta do Log Analytics baseada em savedSearch e do processo de mudança de regras de alerta para a nova API ScheduledQueryRules, com detalhes que resolvem preocupações comuns dos clientes.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1706fc050fecd2e4be3a40725ec3e63a9036b3a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995883"
---
# <a name="switch-api-preference-for-log-alerts"></a>Mudar preferência de API para os Alertas de Log

> [!NOTE]
> Conteúdo declarado aplicável aos usuários somente nuvem pública do Azure e **não** para a nuvem Azure governamental ou Azure China.  

Até recentemente, você gerenciava regras de alerta no Portal do Microsoft Operations Management Suite. A nova experiência de alertas foi integrada a vários serviços no Microsoft Azure, incluindo o Log Analytics, e solicitamos [estender as regras de alerta do portal do OMS para o Azure](alerts-extend.md). Mas, para assegurar o mínimo de interrupção para os clientes, o processo não alterou a interface programática do seu consumo – [API de alerta do Log Analytics](api-alerts.md) com base em SavedSearch.

Mas agora você anuncia para os usuários de alerta do Log Analytics uma verdadeira alternativa programática do Azure, [Azure Monitor – API ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), que também se reflete em sua [Cobrança do Azure – para alertas de log](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Para saber mais sobre como gerenciar alertas de log usando a API, confira [Managing log alerts using Azure Resource Template](alerts-log.md#managing-log-alerts-using-azure-resource-template) (Gerenciando alertas de log usando o modelo de recursos do Azure) e [Managing log alerts using PowerShell, CLI, or API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api) (Gerenciando alertas de log usando o PowerShell, a CLI ou a API).

## <a name="benefits-of-switching-to-new-azure-api"></a>Benefícios de mudar para a nova API do Azure

Há várias vantagens em criar e gerenciar alertas usando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) em relação à [API herdada de alerta do Log Analytics](api-alerts.md); listamos algumas das principais abaixo:

- Capacidade de realizar [pesquisa de logs entre workspaces](../log-query/cross-workspace-query.md) em regras de alerta e abranger recursos externos como workspaces do Log Analytics ou até mesmo aplicativos do Application Insights
- Quando vários campos são usados para Agrupar na consulta usando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), o usuário pode especificar qual campo agregar no portal do Azure
- Alertas de log criados usando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) podem ter um período definido de até 48 horas e buscar dados por um período mais longo que antes
- Criar regras de alerta de uma só vez como um único recurso sem a necessidade de criar três níveis de recursos como ocorre com a [API herdada de alerta do Log Analytics](api-alerts.md)
- Interface de programação única para todas as variantes de alertas de log baseados em consulta no Azure – a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pode ser usada para gerenciar regras do Log Analytics, bem como do Application Insights
- Todas as novas funcionalidades de alertas de log e de desenvolvimento futuro estarão disponíveis somente por meio da nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processo de mudar da API herdada de alertas de log

O processo de mudar as regras de alerta da [API herdada de alertas do Log Analytics](api-alerts.md) não envolve a mudança da definição, consulta ou configuração do alerta de nenhuma maneira. Suas regras de alerta e monitoramento são afetado e os alertas não irá parar ou parados durante ou após o comutador.

Os usuários são livres para usar a [API herdada de alertas do Log Analytics](api-alerts.md) ou a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). As regras de alerta criadas por qualquer API *serão gerenciáveis somente pela mesma API* – bem como do portal do Azure. Por padrão, o Azure Monitor continuará usando [a API herdada de alerta do Log Analytics](api-alerts.md) para a criação de uma nova regra de alerta do portal do Azure.

Os impactos da mudança de preferência para a API scheduledQueryRules são compilados abaixo:

- Todas as interações feitas para gerenciar alertas de log por meio de interfaces programáticas agora devem ser feitas usando a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Para obter mais informações, confira o [exemplo de uso por meio do modelo de recursos do Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [exemplo de uso por meio da CLI do Azure e do PowerShell](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)
- Qualquer regra de alerta de log nova criada no portal do Azure será criada usando somente a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e permitirá que os usuários usem a [funcionalidade adicional da nova API](#benefits-of-switching-to-new-azure-api) por meio do portal do Azure também
- Gravidade para regras de alerta do log mudará de: *Crítico, aviso e informativo*, para *valores de severidade de 0, 1 e 2*. Juntamente com a opção de criar/atualizar regras de alerta com severidade 4.

> [!CAUTION]
> Depois que um usuário opta por alternar preferência para o novo [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), as regras não podem aceitar novamente ou reverter para o uso de mais antigo [herdados API alerta do Log Analytics](api-alerts.md).

Qualquer cliente que desejar mudar voluntariamente para a nova [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e bloquear o uso da [API herdada de alertas do Log Analytics](api-alerts.md) poderá fazer isso executando uma chamada PUT na API abaixo para mudar todas as regras de alerta associadas ao espaço de trabalho do Log Analytics específico.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Com o corpo da solicitação contendo o JSON abaixo.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

A API também pode ser acessada por meio de uma linha de comando do PowerShell usando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Azure Resource Manager. Conforme ilustrado abaixo, na chamada PUT de exemplo, a ferramenta ARMclient é usada para alternar todas as regras de alerta associadas ao espaço de trabalho do Log Analytics específico.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se a mudança de todas as regras de alerta no espaço de trabalho do Log Analytics para usar a nova [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) for bem-sucedida, a seguinte resposta será fornecida.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Os usuários também podem verificar o status atual do espaço de trabalho do Log Analytics e ver se a mudança foi realizada ou não para ele usar somente a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Para verificar, os usuários podem realizar uma chamada GET na API abaixo.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Para executar os itens acima usando a linha de comando do PowerShell com a ferramenta [ARMClient](https://github.com/projectkudu/ARMClient), confira o exemplo a seguir.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se o espaço de trabalho do Log Analytics especificado tiver mudado para usar somente a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), o JSON de resposta será conforme listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Além do mais, se o workspace do Log Analytics especificado ainda não tiver sido mudado para usar somente a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), o JSON de resposta será conforme listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Monitor – Alertas de log](alerts-unified-log.md).
- Saiba como criar [alertas de log nos alertas do Azure](alerts-log.md).
- Saiba mais sobre a [experiência de alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
