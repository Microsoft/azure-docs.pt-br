---
title: Adicionar uma extensão de conjunto de dimensionamento de máquinas virtuais a um Service Fabric tipo de nó de cluster gerenciado (versão prévia)
description: Veja como adicionar uma extensão de conjunto de dimensionamento de máquinas virtuais a Service Fabric tipo de nó de cluster gerenciado
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 64df4b82795f382e176d66dc61470296447b9e29
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788071"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Adicionar uma extensão de conjunto de dimensionamento de máquinas virtuais a um Service Fabric tipo de nó de cluster gerenciado (versão prévia)

Cada tipo de nó em um Cluster Service Fabric gerenciado é apoiado por um conjunto de dimensionamento de máquinas virtuais. Isso permite que você adicione [extensões do conjunto de dimensionamento de máquinas virtuais](../virtual-machines/extensions/overview.md) a seus Service Fabric tipos de nó de cluster gerenciados.

Você pode adicionar uma extensão de conjunto de dimensionamento de máquinas virtuais a um tipo de nó usando o comando do PowerShell [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) .

Como alternativa, você pode uma extensão de conjunto de dimensionamento de máquinas virtuais em um Service Fabric tipo de nó de cluster gerenciado em seu modelo de Azure Resource Manager, por exemplo:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Para obter mais informações sobre como configurar Service Fabric tipos de nó de cluster gerenciado, consulte [tipo de nó de cluster gerenciado](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os clusters gerenciados do Service Fabric, confira:

> [!div class="nextstepaction"]
> [Perguntas mais frequentes sobre os clusters gerenciados do Service Fabric](./faq-managed-cluster.md)
