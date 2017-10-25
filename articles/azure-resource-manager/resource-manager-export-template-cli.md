---
title: Exportar modelo do Resource Manager com a CLI do Azure | Microsoft Docs
description: Use o Azure Resource Manager e a CLI do Azure para exportar um modelo de um grupo de recursos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2017
ms.author: tomfitz
ms.openlocfilehash: 617664129a5353e25da1e90c742c4b009db172ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Exportar modelos do Azure Resource Manager com a CLI do Azure

O Gerenciador de Recursos permite que você exporte um modelo do Gerenciador de Recursos a partir dos recursos existentes em sua assinatura. Você pode usar esse modelo gerado para saber mais sobre a sintaxe do modelo ou automatizar a reimplantação de sua solução, conforme o necessário.

É importante observar que há duas maneiras diferentes de exportar um modelo:

* Você pode exportar o modelo real que usou para uma implantação. O modelo exportado inclui todas as variáveis e parâmetros exatamente como apareceram no modelo original. Essa abordagem é útil quando você precisa recuperar um modelo.
* Você pode exportar um modelo que representa o estado atual do grupo de recursos. O modelo exportado não é baseado em nenhum modelo que você usou para a implantação. Ao contrário, ele cria um modelo que é um instantâneo do grupo de recursos. O modelo exportado tem muitos valores embutidos e provavelmente menos parâmetros do que você normalmente definiria. Essa abordagem é útil quando você modificou o grupo de recursos. Agora, você precisa capturar o grupo de recursos como um modelo.

Este tópico mostra as duas abordagens.

## <a name="deploy-a-solution"></a>Implantar uma solução

Para ilustrar as duas abordagens de exportação de um modelo, vamos começar implantando uma solução em sua assinatura. Se você já tiver um grupo de recursos em sua assinatura que queira exportar, não será preciso implantar essa solução. No entanto, o restante deste artigo refere-se ao modelo dessa solução. O script de exemplo implanta uma conta de armazenamento.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>Salvar modelo do histórico de implantações

Você pode recuperar um modelo do histórico de implantações usando o comando [az group deployment export](/cli/azure/group/deployment#export). O exemplo a seguir salva o modelo implantado anteriormente:

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

Retorna o modelo. Copie o JSON e salve como um arquivo. Observe que é o modelo exato que foi usado para implantação. Os parâmetros e as variáveis correspondem ao modelo do GitHub. Você pode reimplantar esse modelo.


## <a name="export-resource-group-as-template"></a>Exportar grupo de recursos como modelo

Em vez de recuperar um modelo do histórico de implantações, você pode recuperar um modelo que representa o estado atual de um grupo de recursos usando o comando [az group export](/cli/azure/group#export). Use esse comando quando você fez muitas alterações no seu grupo de recursos e nenhum modelo existente representa todas as alterações.

```azurecli
az group export --name ExampleGroup
```

Retorna o modelo. Copie o JSON e salve como um arquivo. Observe que ele é diferente do modelo no GitHub. Ele tem parâmetros diferentes e nenhuma variável. O SKU e o local de armazenamento são embutidos em código para valores. O exemplo abaixo mostra o modelo exportado, mas seu modelo tem um nome de parâmetro ligeiramente diferente:

```json
{
  "parameters": {
    "storageAccounts_mcyzaljiv7qncstandardsa_name": {
      "type": "String",
      "defaultValue": null
    }
  },
  "variables": {},
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "resources": [
    {
      "tags": {},
      "dependsOn": [],
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "type": "Microsoft.Storage/storageAccounts",
      "location": "centralus",
      "name": "[parameters('storageAccounts_mcyzaljiv7qncstandardsa_name')]",
      "properties": {}
    }
  ],
  "contentVersion": "1.0.0.0"
}
```

Você pode reimplantar esse modelo, mas ele exige a suposição de um nome exclusivo para a conta de armazenamento. O nome do parâmetro é ligeiramente diferente.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>Personalizar modelo exportado

Você pode modificar esse modelo para facilitar o uso e torná-lo mais flexível. Para permitir mais locais, altere a propriedade de local para usar o mesmo local do grupo de recursos:

```json
"location": "[resourceGroup().location]",
```

Para não ter que supor um nome exclusivo para a conta de armazenamento, remova o parâmetro do nome da conta de armazenamento. Adicione um parâmetro para um sufixo de nome de armazenamento e um SKU de armazenamento:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Adicione uma variável que construa o nome da conta de armazenamento com a função uniqueString:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Defina o nome da conta de armazenamento para a variável:

```json
"name": "[variables('storageAccountName')]",
```

Defina o SKU para o parâmetro:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

O modelo agora se parece com:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Reimplante o modelo modificado.

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre como usar o portal para exportar um modelo, confira [Exportar um modelo do Azure Resource Manager de recursos existentes](resource-manager-export-template.md).
* Para definir os parâmetros no modelo, consulte [Criando modelos](resource-group-authoring-templates.md#parameters).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).