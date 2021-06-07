---
title: Monitorar os logs e as métricas do Firewall do Azure
description: Neste artigo, você aprende a habilitar e a gerenciar os logs e as métricas do firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 52c6ef9edfc42bf1ad3b3279e0fa4e19b4cf502c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788257"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Monitorar os logs e as métricas do Firewall do Azure

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure. Usando as métricas, você pode exibir contadores de desempenho no portal.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para [Azure monitor logs](../azure-monitor/insights/azure-networking-analytics.md), armazenamento e hubs de eventos e analisados em logs de Azure monitor ou ferramentas diferentes, como Excel e Power bi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve ler [os logs e as métricas do firewall do Azure](logs-and-metrics.md) para obter uma visão geral dos logs de diagnóstico e das métricas disponíveis para o Firewall do Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Habilitar o log de diagnósticos por meio do portal do Azure

Pode levar alguns minutos até que os dados sejam exibidos em seus logs depois de concluir este procedimento para ativar o log de diagnósticos. Se você não vir algo a princípio, verifique novamente depois de alguns minutos.

1. No portal do Azure, abra o grupo de recursos de firewall e selecione o firewall.
2. Em **Monitoramento**, selecione **Configurações de diagnóstico**.

   Para o Firewall do Azure, quatro logs específicos do serviço estão disponíveis:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Selecione **Adicionar configuração de diagnóstico**. A página **Configurações de diagnóstico** fornece as configurações dos logs de diagnóstico.
5. Neste exemplo, os logs do Azure Monitor armazenam os logs; portanto, digite **Log Analytics do Firewall** para o nome.
6. Em **log**, selecione **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule**, **AzureFirewallThreatIntelLog** e **AzureFirewallDnsProxy** para coletar os logs.
7. Selecione **Enviar para log Analytics** para configurar seu espaço de trabalho.
8. Selecione sua assinatura.
9. Clique em **Salvar**.

## <a name="enable-diagnostic-logging-by-using-powershell"></a>Habilitar o log de diagnóstico usando o PowerShell

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. O log de diagnósticos deve ser habilitado para começar a coletar os dados disponíveis por meio desses logs.

Para habilitar o log de diagnóstico com o PowerShell, use as seguintes etapas:

1. Anote a ID de recurso do espaço de trabalho Log Analytics, em que os dados de log são armazenados. Esse valor está no formato: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>` .

   Você pode usar qualquer espaço de trabalho em sua assinatura. Use o portal do Azure para encontrar essas informações. As informações estão localizadas na página **Propriedades** do recurso.

2. Anote a ID do recurso Firewall para o qual o log está habilitado. Esse valor está no formato: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Use o portal para encontrar essas informações.

3. Habilite o log de diagnóstico para todos os logs e métricas usando o seguinte cmdlet do PowerShell:

   ```powershell
   $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
   }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>Habilitar o log de diagnósticos usando o CLI do Azure

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. O log de diagnósticos deve ser habilitado para começar a coletar os dados disponíveis por meio desses logs.

Para habilitar o log de diagnóstico com o CLI do Azure, use as seguintes etapas:

1. Anote a ID de recurso do espaço de trabalho Log Analytics, em que os dados de log são armazenados. Esse valor está no formato: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Você pode usar qualquer espaço de trabalho em sua assinatura. Use o portal do Azure para encontrar essas informações. As informações estão localizadas na página **Propriedades** do recurso.

2. Anote a ID do recurso Firewall para o qual o log está habilitado. Esse valor está no formato: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Use o portal para encontrar essas informações.

3. Habilite o log de diagnóstico para todos os logs e métricas usando o seguinte comando de CLI do Azure:

   ```azurecli-interactive
   az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

Você pode exibir e analisar os dados do log de atividades usando um dos seguintes métodos:

* **Ferramentas do Azure**: recupere informações do log de atividades por meio do Azure PowerShell, da CLI do Azure, da API REST do Azure ou do portal do Azure. As instruções passo a passo para cada método são detalhadas no artigo [Activity operations with Resource Manager](../azure-resource-manager/management/view-activity-logs.md) (Operações de atividade com o Resource Manager).
* **Power BI**: se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), experimente uma gratuitamente. Com o [pacote de conteúdo dos Logs de Atividades do Azure para Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), você pode analisar seus dados com painéis pré-configurados que podem ser usados no estado em que se encontram ou ser personalizados.
* **Azure Sentinel**: você pode conectar os logs de firewall do Azure ao Azure Sentinel, permitindo que você exiba dados de log em pastas de trabalho, use-os para criar alertas personalizados e incorporá-los para melhorar sua investigação. O conector de dados do firewall do Azure no Azure Sentinel está atualmente em visualização pública. Para obter mais informações, consulte [conectar dados do firewall do Azure](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Exibir e analisar os logs de regra de aplicativo e de rede

Os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) coletam os arquivos de log de eventos e de contador. Ele inclui visualizações e funcionalidades de pesquisa avançadas para analisar os logs.

Para consultas de exemplo do Log Analytics do Firewall do Azure, confira [Amostras do Log Analytics do Firewall do Azure](./firewall-workbook.md).

A [pasta de trabalho do firewall do Azure](firewall-workbook.md) fornece uma tela flexível para análise de dados do firewall do Azure. Você pode usá-lo para criar relatórios visuais avançados dentro do portal do Azure. Você pode aproveitar vários firewalls implantados no Azure e combiná-los em experiências interativas unificadas.

Você também pode se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de desempenho e acesso. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, no Power BI ou em qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="view-metrics"></a>Exibir métricas
Navegue até um firewall do Azure, em **monitoramento** selecione **métricas**. Para exibir os valores disponíveis, selecione a lista suspensa **MÉTRICA**.

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o firewall para coletar logs, poderá explorar os logs do Azure Monitor para exibir seus dados.

[Monitorar logs usando a pasta de trabalho do firewall do Azure](firewall-workbook.md)

[Soluções de monitoramento de rede nos logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)