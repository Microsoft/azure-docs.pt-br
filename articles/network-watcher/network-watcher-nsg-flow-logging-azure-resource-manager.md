---
title: Observador de Rede - Crie logs do fluxo NSG usando um modelo do Azure Resource Manager
description: Use um modelo do Azure Resource Manager e o PowerShell para configurar facilmente os logs do fluxo NSG.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: fasttrack-edit
ms.openlocfilehash: 2cae673dd319e55670357ae468d21ff63e4f9c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669433"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Configurar os logs do fluxo NSG a partir de um modelo do Azure Resource Manager

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


O [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) é o modo nativo e poderoso do Azure para gerenciar a [infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code).

Este artigo mostra como habilitar os [logs do fluxo NSG](./network-watcher-nsg-flow-logging-overview.md) programaticamente usando um modelo do Azure Resource Manager e o Azure PowerShell. Começamos fornecendo uma visão geral das propriedades do objeto de log do fluxo NSG, seguido por alguns modelos de amostra. Em seguida, implantamos o modelo usando uma instância do PowerShell local.


## <a name="nsg-flow-logs-object"></a>Objeto de logs do fluxo NSG

O objeto de logs do fluxo NSG com todos os parâmetros é mostrado abaixo.
Para obter uma visão geral completa das propriedades, você pode ler a [referência do modelo dos logs do fluxo NSG](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

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
Para criar um recurso Microsoft.Network/networkWatchers/flowLogs, adicione o JSON acima à seção de recursos do seu modelo.


## <a name="creating-your-template"></a>Criando seu modelo

Se você estiver usando modelos do Azure Resource Manager pela primeira vez, use os links abaixo para obter mais informações.

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
* [Tutorial: Criar e implantar seu primeiro modelo do Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Abaixo temos dois exemplos de modelos completos para configurar os logs do fluxo NSG.

**Exemplo 1**:  A versão mais simples do acima com os parâmetros mínimos passados. O modelo abaixo habilita os logs do fluxo NSG em um NSG de destino e os armazena em uma determinada conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> * O nome do recurso tem o formato “Recurso pai_Recurso filho”. Aqui, o recurso pai é a instância do Observador de Rede regional (formato: ObservadordeRede_NomedaRegião. Exemplo: ObservadordeRede_euacentraleuap)
> * targetResourceId é a ID do recurso do NSG de destino
> * storageId é a ID do recurso da conta de armazenamento de destino

**Exemplo 2**: Os modelos a seguir habilitam os logs do fluxo NSG (versão 2) com retenção por 5 dias. Habilitando a Análise de Tráfego com um intervalo de processamento de 10 minutos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
        "retentionPolicy": {
          "days": 5,
          "enabled": true
        },
        "format": {
          "type": "JSON",
          "version": 2
        }
      }
    }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Implantando o modelo do Azure Resource Manager

Este tutorial pressupõe que você tem um grupo de Recursos existente e um NSG que pode habilitar o log do fluxo.
Você pode salvar qualquer um dos modelos do exemplo acima localmente como `azuredeploy.json`. Atualize os valores da propriedade para que eles apontem para recursos válidos em sua assinatura.

Para implantar o modelo, execute o seguinte comando no PowerShell.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId <SubscriptionId>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Os comandos acima estão implantando um recurso no grupo de recursos NetworkWatcherRG e não no grupo de recursos que contém o NSG


## <a name="verifying-your-deployment"></a>Verificando a implantação

Há algumas maneiras de verificar se sua implantação foi bem-sucedida. O console do PowerShell deve mostrar “ProvisioningState” como “Bem-sucedido”. Além disso, você pode visitar a [página do portal de logs do fluxo NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar suas alterações. Se houver problemas com a implantação, consulte [Solução de problemas comuns de implantação do Azure com o Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="deleting-your-resource"></a>Excluindo o recurso
O Azure permite a exclusão de recursos por meio do modo de implantação “Completo”. Para excluir um recurso de logs do fluxo, especifique uma implantação no modo Completo sem incluir o recurso que você deseja excluir. Leia mais sobre o [Modo de implantação completa](../azure-resource-manager/templates/deployment-modes.md#complete-mode)

## <a name="next-steps"></a>Próximas etapas

Aprenda a visualizar os dados do fluxo NSG usando:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Ferramentas de software livre](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Análise de Tráfego do Azure](./traffic-analytics.md)
