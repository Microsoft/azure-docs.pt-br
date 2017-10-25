---
title: Local do recurso do Azure no modelo | Microsoft Docs
description: Mostra como configurar um local para um recurso em um modelo do Azure Resource Manager
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
ms.openlocfilehash: 73e50a593c41e841dcaf184abb895406ff5001e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>Configure os locais dos recursos de modelos do Azure Resource Manager
Ao implantar um modelo, é necessário fornecer um local para cada recurso. Este tópico mostra como determinar os locais disponíveis para a sua assinatura de cada tipo de recurso.

## <a name="determine-supported-locations"></a>Determinar locais com suporte

Para obter uma lista completa de locais com suporte para cada tipo de recurso, consulte [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/). No entanto, sua assinatura pode não ter acesso a todos os locais da lista. Para ver uma lista personalizada de locais disponíveis para sua assinatura, use o Azure PowerShell ou a CLI do Azure. 

O exemplo a seguir usa o PowerShell para obter os locais para o tipo de recurso `Microsoft.Web\sites`:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

O exemplo a seguir usa a CLI do Azure 2.0 para obter os locais para o tipo de recurso `Microsoft.Web\sites`:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>Configurar o local no modelo

Depois de determinar os locais com suporte para seus recursos, é necessário definir esse local em seu modelo. A maneira mais fácil de definir esse valor é criar um grupo de recursos em um local que dê suporte aos tipos de recursos e defina cada local como `[resourceGroup().location]`. É possível reimplantar o modelo para grupos de recursos em diferentes locais e não alterar quaisquer valores no modelo ou nos parâmetros. 

O exemplo a seguir mostra uma conta de armazenamento implantada no mesmo local que o grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Se for necessário codificar o local em seu modelo, informe o nome de uma das regiões com suporte. O exemplo a seguir mostra uma conta de armazenamento que sempre é implantada para o Centro-Norte dos EUA:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas
* Para ver recomendações sobre como criar modelos, consulte [Práticas recomendadas para criação de modelos do Azure Resource Manager](resource-manager-template-best-practices.md).

