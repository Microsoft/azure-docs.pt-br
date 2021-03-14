---
title: Amostras de modelo do Resource Manager para o Azure Monitor
description: Implantar e configurar recursos do Azure Monitor usando modelos do Resource Manager
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.openlocfilehash: ac2ed571359958feddd4d7952ea3b5f549688c77
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046968"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Amostras de modelo do Resource Manager para o Azure Monitor

O Azure Monitor pode ser implantado e configurado em escala usando o [modelo do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Os artigos a seguir fornecem modelos de amostra para diferentes recursos do Azure Monitor. Essas amostras podem ser modificadas para os seus requisitos específicos e implantadas usando qualquer método padrão para a implantação de modelos do Resource Manager. 

## <a name="deploying-the-sample-templates"></a>Implantar os modelos de amostra
As etapas básicas para usar as amostras são:

1. Copie o modelo e salve como um arquivo JSON.
2. Modifique os parâmetros do seu ambiente e salve como um arquivo JSON.
4. Implante o modelo usando [qualquer método de implantação para recursos do Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). 

Por exemplo, use os comandos a seguir para implantar o modelo e o arquivo de parâmetros em um grupo de recursos usando o PowerShell ou a CLI do Azure.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Lista de modelos de amostra

- [Agentes](agents/resource-manager-agent.md) – Implante e configure o agente do Log Analytics e a extensão de diagnóstico.
- Alertas
  - [Regras de alerta de log](alerts/resource-manager-alerts-log.md) – Alertas de consultas de log e log de atividades do Azure.
  - [Regras de alerta de métrica](alerts/resource-manager-alerts-metric.md) – Alertas de métricas usando diferentes tipos de lógica.
- [Application Insights](app/resource-manager-app-resource.md)
- [Configurações de diagnóstico](essentials/resource-manager-diagnostic-settings.md) – Crie configurações de diagnóstico para encaminhar logs e métricas de diferentes tipos de recursos.
- [Consultas de log](logs/resource-manager-log-queries.md) – Crie consultas de log salvas em um workspace do Log Analytics.
- [Workspace do Log Analytics](logs/resource-manager-workspace.md) – Crie um workspace do Log Analytics e configure a coleta de diferentes fontes de dados do agente do Log Analytics.
- [Pastas de trabalho](visualize/resource-manager-workbooks.md) – Crie pastas de trabalho.
- [Insights do contêiner](containers/resource-manager-container-insights.md) – Integrar clusters aos insights do contêiner.
- [Azure Monitor para VMs](vm/resource-manager-vminsights.md) – Integre máquinas virtuais ao Azure Monitor para VMs.



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [modelos do Resource Manager](../azure-resource-manager/templates/overview.md)