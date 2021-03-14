---
title: Entenda como o provisionamento se integra com os logs de Azure Monitor no Azure Active Directory.
description: Entenda como o provisionamento se integra com os logs de Azure Monitor no Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f656f55b0c74103053f110b8c66645d353db9fbf
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561561"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Entenda como o provisionamento se integra aos logs de Azure Monitor

O provisionamento se integra com Azure Monitor logs e Log Analytics. Com o monitoramento do Azure, você pode fazer coisas como criar pastas de trabalho, também conhecidas como dashboards, armazenar logs de provisionamento por mais de 30 dias e criar consultas e alertas personalizados. Este artigo discute como os logs de provisionamento são integrados aos logs de Azure Monitor. Para saber mais sobre como o provisionamento de logs funciona em geral, consulte [Provisionando logs](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>Habilitando logs de provisionamento

Você já deve estar familiarizado com o monitoramento e Log Analytics do Azure. Caso contrário, pule para saber mais sobre eles e volte para saber mais sobre os logs de provisionamento de aplicativos. Para saber mais sobre o monitoramento do Azure, confira [Azure monitor visão geral](../../azure-monitor/overview.md). Para saber mais sobre os logs de Azure Monitor e Log Analytics, confira [visão geral das consultas de log no Azure monitor](../../azure-monitor/logs/log-query-overview.md).

Depois de configurar o monitoramento do Azure, você pode habilitar logs para provisionamento de aplicativos. A opção está localizada na página **configurações de diagnóstico** .

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Configurações de diagnóstico de acesso" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Habilitar logs de provisionamento de aplicativos" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Se você acabou de provisionar um espaço de trabalho recentemente, pode levar algum tempo para poder enviar logs para ele. Se você receber um erro de que a assinatura não está registrada para usar *o Microsoft. insights* , então, verifique novamente após alguns minutos.
 
## <a name="understanding-the-data"></a>Compreendendo os dados
O fluxo de dados subjacente que o provisionamento envia para os visualizadores de log é quase idêntico. Os logs de Azure Monitor recebem quase o mesmo fluxo que a interface do usuário do portal do Azure e a API do Azure. Há apenas algumas **diferenças** nos campos de log, conforme descrito na tabela a seguir. Para saber mais sobre esses campos, consulte [list provisioningObjectSummary](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta).

|Logs do Azure Monitor   |Interface do usuário do portal do Azure   |API do Azure |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor pastas de trabalho

Azure Monitor pastas de trabalho fornecem uma tela flexível para análise de dados. Eles também fornecem a criação de relatórios visuais avançados dentro do portal do Azure. Para saber mais, consulte [visão geral de Azure monitor pastas de trabalho](../../azure-monitor/visualize/workbooks-overview.md).

O provisionamento de aplicativos vem com um conjunto de pastas de trabalho criadas previamente. Você pode encontrá-los na página pastas de trabalho. Para exibir os dados, você precisará garantir que todos os filtros (timerange, jobID, appName) sejam preenchidos. Você também precisará certificar-se de ter provisionado um aplicativo, caso contrário, não haverá nenhum dado nos logs.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Pastas de trabalho de provisionamento de aplicativos" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Painel de provisionamento de aplicativos" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Consultas personalizadas

Você pode criar consultas personalizadas e mostrar os dados nos painéis do Azure. Para saber como, consulte [criar e compartilhar painéis de dados de log Analytics](../../azure-monitor/logs/get-started-queries.md). Além disso, não deixe de conferir a [visão geral das consultas de log em Azure monitor](../../azure-monitor/logs/log-query-overview.md).

Aqui estão alguns exemplos para começar a usar o provisionamento de aplicativos.

Consulte os logs para um usuário com base em sua ID no sistema de origem:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Contagem de resumo por ErrorCode:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Resumir contagem de eventos por dia por ação:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Considere eventos de 100 e propriedades de chave do projeto:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Alertas personalizados

Azure Monitor permite configurar alertas personalizados para que você possa ser notificado sobre os principais eventos relacionados ao provisionamento. Por exemplo, talvez você queira receber um alerta sobre picos de falhas. Ou talvez picos em desabilitações ou exclusões. Outro exemplo de onde você talvez queira ser alertado é a falta de qualquer provisionamento, o que indica que algo está errado.

Para saber mais sobre alertas, confira [responder a eventos com Azure monitor alertas](../../azure-monitor/alerts/tutorial-response.md).

Alertar quando houver um pico nas falhas. Substitua o jobID pelo jobID para seu aplicativo.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Alertar quando houver um pico nas falhas." lightbox="media/application-provisioning-log-analytics/alert1.png":::

Pode haver um problema que fez com que o serviço de provisionamento parasse de ser interrompido. Use o alerta a seguir para detectar quando não há eventos de provisionamento durante um determinado intervalo de tempo.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Pode haver um problema que fez com que o serviço de provisionamento parasse de ser interrompido." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Alertar quando houver um pico em desabilitações ou exclusões.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Alertar quando houver um pico em desabilitações ou exclusões." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Contribuições da comunidade

Estamos adotando uma abordagem de software livre e baseada na Comunidade para consultas e painéis de provisionamento de aplicativos. Se você criou uma consulta, um alerta ou uma pasta de trabalho que acha que outras pessoas achariam úteis, certifique-se de publicá-las no [repositório GitHub AzureMonitorCommunity](https://github.com/microsoft/AzureMonitorCommunity). Em seguida, nos leva um email com um link. Vamos revisá-lo e publicá-lo no serviço para que outras pessoas também possam se beneficiar. Você pode em contato conosco em provisioningfeedback@microsoft.com.

## <a name="next-steps"></a>Próximas etapas

- [Análise de log](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Introdução às consultas de log do Azure Monitor](../../azure-monitor/logs/get-started-queries.md)
- [Criar e gerenciar grupos de alertas no portal do Azure](../../azure-monitor/alerts/action-groups.md)
- [Instalar e usar os modos de exibição do Log Analytics do Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [API de logs de provisionamento](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)