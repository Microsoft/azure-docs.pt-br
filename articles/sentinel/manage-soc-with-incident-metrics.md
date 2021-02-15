---
title: Gerencie melhor seu SOC com métricas de incidentes no Azure Sentinel | Microsoft Docs
description: Use as informações da tela de métricas de incidentes do Azure Sentinel e da pasta de trabalho para ajudá-lo a gerenciar seu SOC (Security Operations Center).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 408913fed864ee5f966b96c81afbfee4b2dc8678
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660722"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>Gerenciar melhor seu SOC com métricas de incidentes

> [!IMPORTANT]
> Os recursos de métricas de incidentes estão atualmente em visualização pública.
> Esses recursos são fornecidos sem um contrato de nível de serviço e não são recomendados para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Como um Gerenciador de SOC (Security Operations Center), você precisa ter métricas e medidas de eficiência geral ao alcance para medir o desempenho da sua equipe. Você desejará ver as operações de incidentes ao longo do tempo por muitos critérios diferentes, como gravidade, táticas de MITRE, tempo médio de triagem, tempo médio de resolução e muito mais. Agora, o Azure Sentinel disponibiliza esses dados para você com a nova tabela e o esquema do **SecurityIncident** no log Analytics e a pasta de trabalho de **eficiência das operações de segurança** que o acompanha. Você poderá visualizar o desempenho da sua equipe ao longo do tempo e usar essa percepção para melhorar a eficiência. Você também pode escrever e usar suas próprias consultas KQL na tabela de incidentes para criar pastas de trabalho personalizadas que atendam às suas necessidades e KPIs de auditoria específicos.

## <a name="use-the-security-incidents-table"></a>Usar a tabela incidentes de segurança

A tabela **SecurityIncident** é incorporada ao Azure Sentinel. Você o encontrará com as outras tabelas na coleção **SecurityInsights** em **logs**. Você pode consultá-lo como qualquer outra tabela no Log Analytics.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="Tabela de incidentes de segurança":::

Toda vez que você criar ou atualizar um incidente, uma nova entrada de log será adicionada à tabela. Isso permite que você acompanhe as alterações feitas em incidentes e permite métricas de SOC ainda mais poderosas, mas você precisa estar atento a isso ao construir consultas para essa tabela, pois talvez seja necessário remover entradas duplicadas de um incidente (dependendo da consulta exata que você está executando). 

Por exemplo, se você quisesse retornar uma lista de todos os incidentes classificados por seu número de incidente, mas quisesse apenas retornar o log mais recente por incidente, você poderia fazer isso usando o [operador de resumo](/azure/data-explorer/kusto/query/summarizeoperator) KQL com a `arg_max()` [função de agregação](/azure/data-explorer/kusto/query/arg-max-aggfunction):


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>Mais consultas de exemplo

Tempo médio para encerramento:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

Tempo médio para triagem:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>Pasta de trabalho de eficiência de operações de segurança

Para complementar a tabela **SecurityIncidents** , fornecemos um modelo de pasta de trabalho de **eficiência de operações de segurança** pronto para uso que você pode usar para monitorar suas operações de SOC. A pasta de trabalho contém as seguintes métricas: 
- Incidente criado ao longo do tempo 
- Incidentes criados por fechamento de classificação, gravidade, proprietário e status 
- Tempo médio para triagem 
- Tempo médio de fechamento 
- Incidentes criados por gravidade, proprietário, status, produto e táticas ao longo do tempo 
- Tempo para a triagem de percentuais 
- Percentual de tempo para fechamento 
- Tempo médio para triagem por proprietário 
- Atividades recentes 
- Classificações de fechamento recentes  

Você pode encontrar esse novo modelo de pasta de trabalho escolhendo **pastas de trabalho** no menu de navegação do Azure Sentinel e selecionando a guia **modelos** . Escolha a **eficiência das operações de segurança** na galeria e clique em um dos botões **Exibir pasta de trabalho salva** e **Exibir modelo** .

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="Galeria de pastas de trabalho de incidentes de segurança":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="Pasta de trabalho incidentes de segurança concluída":::

Você pode usar o modelo para criar suas próprias pastas de trabalho personalizadas adaptadas às suas necessidades específicas.

## <a name="securityincidents-schema"></a>Esquema SecurityIncidents

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o Azure Sentinel, você precisa ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [integrar seus dados ao Azure Sentinel](quickstart-onboard.md) e [obtenha visibilidade de seus dados e de possíveis ameaças](quickstart-get-visibility.md).