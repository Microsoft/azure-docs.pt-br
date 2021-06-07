---
title: 'Início Rápido: Configurar os logs de fluxo do grupo de segurança de rede usando um modelo do ARM (Azure Resource Manager)'
description: Saiba como habilitar programaticamente os logs de fluxo do NSG (grupo de segurança de rede) usando um modelo do ARM (Azure Resource Manager) e o Azure PowerShell.
services: network-watcher
author: damendo
ms.author: damendo
ms.date: 01/07/2021
ms.topic: quickstart
ms.service: network-watcher
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: df0ccb5bf5ecd60d80526085983e35abf58e9966
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532434"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Início Rápido: configurar os logs de fluxo do grupo de segurança de rede usando um modelo do ARM

Neste início rápido, saiba como habilitar os [logs de fluxo do NSG (grupo de segurança de rede)](network-watcher-nsg-flow-logging-overview.md) usando um modelo do ARM ([Azure Resource Manager](../azure-resource-manager/management/overview.md)) e o Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Começamos com uma visão geral das propriedades do objeto do log de fluxo do NSG. Fornecemos modelos de amostra. Em seguida, usamos uma instância local do Azure PowerShell para implantar o modelo.

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo é aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo que usamos neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Esses recursos do Azure estão definidos no modelo:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Objeto de logs de fluxo NSG

O código a seguir mostra um objeto dos logs de fluxo do NSG e os parâmetros desse objeto. Para criar um recurso `Microsoft.Network/networkWatchers/flowLogs`, adicione esse código à seção de recursos do modelo:

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

Para obter uma visão geral completa das propriedades do objeto dos logs de fluxo do NSG, confira [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Criar seu modelo

Se você estiver usando modelos do ARM pela primeira vez, confira os seguintes artigos para saber mais sobre os modelos do ARM:

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

O exemplo a seguir é um modelo completo. Também é a versão mais simples do modelo. O exemplo contém os parâmetros mínimos que são passados para configurar os logs de fluxo do NSG. Para obter mais exemplos, confira o artigo de visão geral [Configurar os logs de fluxo do NSG pro meio de um modelo do Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md).

### <a name="example"></a>Exemplo

O seguinte modelo habilita os logs de fluxo para um NSG e armazena os logs em uma conta de armazenamento específica:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - O nome do recurso usa o formato _ParentResource_ChildResource_. Em nosso exemplo, o recurso pai é a instância regional do Observador de Rede do Azure:
>    - **Formatar**: NetworkWatcher_RegionName
>    - **Exemplo**: NetworkWatcher_centraluseuap
> - `targetResourceId` é a ID do recurso do NSG de destino.
> - `storageId` é a ID do recurso da conta de armazenamento de destino.

## <a name="deploy-the-template"></a>Implantar o modelo

Este tutorial pressupõe que você tem um grupo de recursos existente e um NSG no qual pode habilitar o log de fluxo.

Você pode salvar qualquer um dos modelos de exemplo mostrados neste artigo localmente como *azuredeploy.json*. Atualize os valores da propriedade a fim de que eles apontem para recursos válidos da assinatura.

Para implantar o modelo, execute o seguinte comando no Azure PowerShell:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Esses comandos implantam um recurso no grupo de recursos de exemplo NetworkWatcherRG e não no grupo de recursos que contém o NSG.

## <a name="validate-the-deployment"></a>Validar a implantação

Você tem duas opções para ver se a implantação foi bem-sucedida:

- O console do PowerShell mostra `ProvisioningState` como `Succeeded`.
- Vá para a [página do portal dos logs de fluxo do NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar as alterações.

Se houver problemas com a implantação, confira [Solucionar problemas comuns de implantação do Azure com o Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode excluir os recursos do Azure usando o modo de implantação completo. Para excluir um recurso dos logs de fluxo, especifique uma implantação no modo completo sem incluir o recurso que deseja excluir. Leia mais sobre o [modo de implantação completo](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Você também pode desabilitar um log de fluxo do NSG no portal do Azure:

1. Entre no portal do Azure.
1. Selecione **Todos os serviços**. Na caixa **Filtro**, insira **Observador de Rede**. Nos resultados da pesquisa, selecione **Observador de Rede**.
1. Em **Logs**, selecione **Logs de fluxo do NSG**.
1. Na lista de NSGs, selecione o NSG para o qual deseja desabilitar os logs de fluxo.
1. Em **Configurações dos logs de fluxo**, selecione **Desativado**.
1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a habilitar os logs de fluxo do NSG usando um modelo do ARM. Em seguida, saiba como visualizar os dados de fluxo do NSG usando uma destas opções:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Ferramentas de software livre](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Análise de Tráfego do Azure](traffic-analytics.md)
