---
title: "Coletar logs e as métricas do serviço do Azure para o Log Analytics | Microsoft Docs"
description: "Configure o diagnóstico nos recursos do Azure para gravar logs e métricas no Log Analytics."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7a3785e39f0d1cf849dbbf0d83d89eaed58c5b0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Coletar logs e as métricas do serviço do Azure para uso no Log Analytics

Há quatro maneiras diferentes de coletar logs e métricas para os serviços do Azure:

1. Direcionar o diagnóstico do Azure para o Log Analytics (*Diagnóstico* na tabela a seguir)
2. Enviar o diagnóstico do Azure para o Armazenamento do Azure e para o Log Analytics (*Armazenamento* na tabela a seguir)
3. Conectores para serviços do Azure (*Conectores* na tabela a seguir)
4. Scripts para coletar e postar dados para o Log Analytics (espaços em branco na tabela a seguir e para os serviços que não estão listados)


| O Barramento de                 | Tipo de recurso                           | Logs        | Métricas     | Solução |
| --- | --- | --- | --- | --- |
| Application gateways    | Microsoft.Network/applicationGateways   | Diagnostics | Diagnostics | [Análise de Gateway de Aplicativo do Azure](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application insights    |                                         | Conector   | Conector   | [Conector do Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (visualização) |
| Contas de automação     | Microsoft.Automation/AutomationAccounts | Diagnostics |             | [Mais informações](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Contas do Lote          | Microsoft.Batch/batchAccounts           | Diagnostics | Diagnostics | |
| Serviços de Nuvem clássicos  |                                         | Armazenamento     |             | [Mais informações](log-analytics-azure-storage-iis-table.md) |
| Serviços cognitivos      | Microsoft.CognitiveServices/accounts    |             | Diagnostics | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostics |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | Diagnostics |             | |
| Namespace do Hub de Eventos     | Microsoft.EventHub/namespaces           | Diagnostics | Diagnostics | |
| Hubs IoT                | Microsoft.Devices/IotHubs               |             | Diagnostics | |
| Cofre da Chave               | Microsoft.KeyVault/vaults               | Diagnostics |             | [KeyVault Analytics](log-analytics-azure-key-vault.md) |
| Balanceadores de Carga          | Microsoft.Network/loadBalancers         | Diagnostics |             |  |
| Aplicativos Lógicos              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostics | Diagnostics | |
| Grupos de segurança de rede | Microsoft.Network/networksecuritygroups | Diagnostics |             | [Análise de Grupo de Segurança de Rede do Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Cofres de recuperação         | Microsoft.RecoveryServices/vaults       |             |             | [Análise dos Serviços de Recuperação do Azure (Visualização)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Serviços de pesquisa         | Microsoft.Search/searchServices         | Diagnostics | Diagnostics | |
| Namespace do Barramento de Serviço   | Microsoft.ServiceBus/namespaces         | Diagnostics | Diagnostics | [Análise do Barramento de Serviço (Visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Armazenamento     |             | [Análise do Service Fabric (visualização)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostics | [Azure SQL Analytics (Visualização)](log-analytics-azure-sql.md) |
| Armazenamento                 |                                         |             | Script      | [Análise do Azure Storage (Visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Máquinas Virtuais        | Microsoft.Compute/virtualMachines       | Extensão   | Extensão <br> Diagnostics  | |
| Conjuntos de dimensionamento de Máquinas Virtuais | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostics | |
| Farms do servidor Web        | Microsoft.Web/serverfarms               |             | Diagnostics | |
| Sites               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostics | [Análise dos Aplicativos Web do Azure (Visualização)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) |


> [!NOTE]
> Para o monitoramento de máquinas virtuais do Azure (Linux e Windows), é recomendável instalar a [extensão de VM do Log Analytics](log-analytics-azure-vm-extension.md). O agente fornece informações coletadas das suas máquinas virtuais. Você também pode usar a extensão para ps conjuntos de dimensionamento de máquinas virtuais.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Direcionar o diagnóstico do Azure para o Log Analytics
Muitos recursos do Azure são capazes de gravar logs de diagnóstico e métricas diretamente no Log Analytics e essa é a melhor maneira de coletar os dados para análise. Ao usar o diagnóstico do Azure, os dados são gravados imediatamente no Log Analytics, não sendo necessário primeiro gravá-los no armazenamento.

Os recursos do Azure que dão suporte ao [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) pode enviar os logs e métricas diretamente para o Log Analytics.

* Para obter os detalhes das métricas disponíveis, consulte [métricas compatíveis com o Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Para obter os detalhes dos logs disponíveis, consulte [serviços e esquema com suporte para logs de diagnóstico](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Habilitar diagnóstico com PowerShell
Você precisa da versão de novembro de 2016 (v2.3.0) ou posterior do [Azure PowerShell](/powershell/azure/overview).

O exemplo do PowerShell a seguir mostra como usar [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) para habilitar o diagnóstico em um grupo de segurança de rede. A mesma abordagem funciona para todos os recursos com suporte: defina `$resourceId` para a ID do recurso para a qual você quer habilitar os diagnósticos.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Habilitar diagnósticos com modelos do Resource Manager

Você pode usar um modelo semelhante ao mostrado abaixo para habilitar diagnósticos em um recurso quando ele é criado e fazer com que ele seja enviado para seu espaço de trabalho do Log Analytics. Este exemplo destina-se a uma conta de automação, mas funciona em todos os tipos de recursos com suporte.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Enviar o diagnóstico do Azure para o armazenamento e depois para o Log Analytics

Para coletar logs de dentro de alguns recursos, é possível enviar os logs para o Armazenamento do Azure e, em seguida, configurar o Log Analytics para ler os logs do armazenamento.

O Log Analytics pode usar essa abordagem para coletar diagnósticos do Armazenamento do Azure para os seguintes recursos e logs:

| Recurso | Logs |
| --- | --- |
| Service Fabric |ETWEvent <br> Evento operacional <br> Evento de ator confiável <br> Evento de serviço confiável |
| Máquinas Virtuais |Linux Syslog <br> Evento do Windows <br> Log do IIS <br> Windows ETWEvent |
| Funções web <br> Funções de trabalho |Linux Syslog <br> Evento do Windows <br> Log do IIS <br> Windows ETWEvent |

> [!NOTE]
> São cobradas taxas de dados do Azure normais para armazenamento e transações quando você envia diagnósticos para uma conta de armazenamento e quando o Log Analytics lê os dados de sua conta de armazenamento.
>
>

Consulte [Usar armazenamento de blobs para IIS e armazenamento de tabelas para eventos](log-analytics-azure-storage-iis-table.md) para saber mais sobre como o Log Analytics pode coletar esses logs.

## <a name="connectors-for-azure-services"></a>Conectores para serviços do Azure

Há um conector para o Application Insights, que permite que os dados coletados pelo Application Insights sejam enviados para o Log Analytics.

Saiba mais sobre o [conector do Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Scripts a serem coletados e dados a serem postados no Log Analytics

Para os serviços do Azure que não fornecem uma maneira direta de enviar métricas e logs para o Log Analytics, você pode usar um script de Automação do Azure para coletar os logs e métricas. O script poderá, então, enviar os dados para o Log Analytics usando a [API do coletor de dados](log-analytics-data-collector-api.md)

A Galeria de modelos do Azure tem [exemplos de como usar a Automação do Azure](https://azure.microsoft.com/en-us/resources/templates/?term=OMS) para coletar dados de serviços e enviá-los para o Log Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Use o armazenamento de blobs para IIS e armazenamento de tabelas para eventos](log-analytics-azure-storage-iis-table.md) para ler os logs de serviços do Azure que gravam diagnósticos em armazenamento de tabelas ou de logs do IIS gravados para armazenamento de blobs.
* [Habilitar Soluções](log-analytics-add-solutions.md) para fornecer informações sobre os dados.
* [Usar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.
