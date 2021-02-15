---
title: Funções de modelo em implantações com escopo
description: Descreve como as funções de modelo são resolvidas em implantações com escopo. O escopo pode ser um locatário, grupos de gerenciamento, assinaturas e grupos de recursos.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492086"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>Funções de modelo ARM em escopos de implantação

Com modelos de Azure Resource Manager (modelos ARM), você pode implantar em grupos de recursos, assinaturas, grupos de gerenciamento ou locatários. Geralmente, as [funções de modelo do ARM](template-functions.md) funcionam da mesma forma para todos os escopos. Este artigo descreve as diferenças existentes para algumas funções dependendo do escopo.

## <a name="supported-functions"></a>Funções suportadas

Ao implantar em escopos diferentes, há algumas considerações importantes:

* A função [resourcegroup ()](template-functions-resource.md#resourcegroup) tem **suporte** para implantações de grupo de recursos.
* A função [Subscription ()](template-functions-resource.md#subscription) tem **suporte** para implantações de assinatura e grupo de recursos.
* As funções [Reference ()](template-functions-resource.md#reference) e [list ()](template-functions-resource.md#list) têm **suporte** para todos os escopos.
* Use [ResourceId ()](template-functions-resource.md#resourceid) para obter a ID de um recurso implantado no grupo de recursos.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Use a função [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) para obter a ID de um recurso implantado na assinatura.

  Por exemplo, para obter a ID de recurso para uma definição de política que é implantada em uma assinatura, use:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Use a função [extensionResourceId ()](template-functions-resource.md#extensionresourceid) para recursos que são implementados como extensões do grupo de gerenciamento. As definições de política personalizadas que são implantadas no grupo de gerenciamento são extensões do grupo de gerenciamento.

  Para obter a ID de recurso para uma definição de política personalizada no nível do grupo de gerenciamento, use:

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Use a função [tenantResourceId ()](template-functions-resource.md#tenantresourceid) para obter a ID de um recurso implantado no locatário. As definições de política internas são recursos de nível de locatário. Ao atribuir uma política interna no nível do grupo de gerenciamento, use a função tenantResourceId.

  Para obter a ID de recurso para uma definição de política interna, use:

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Resolução de função em escopos

Quando você implanta em mais de um escopo, as funções [resourcegroup ()](template-functions-resource.md#resourcegroup) e [Subscription ()](template-functions-resource.md#subscription) são resolvidas de forma diferente com base em como você especifica o modelo. Quando você vincula a um modelo externo, as funções sempre são resolvidas para o escopo desse modelo. Quando você aninha um modelo dentro de um modelo pai, use a propriedade `expressionEvaluationOptions` para especificar se as funções são resolvidas para o grupo de recursos e a assinatura para o modelo pai ou o modelo aninhado. Defina a propriedade como `inner` para resolver para o escopo do modelo aninhado. Defina a propriedade como `outer` para resolver para o escopo do modelo pai.

A tabela a seguir mostra se as funções são resolvidas para o grupo de recursos pai ou aninhado e para a assinatura.

| Tipo do modelo | Escopo | Resolução |
| ------------- | ----- | ---------- |
| aninhado        | externo (padrão) | Grupo de recursos pai |
| aninhado        | interna | Subgrupo de recursos |
| vinculado        | N/D   | Subgrupo de recursos |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) a seguir mostra:

* o modelo aninhado com escopo padrão (externo)
* o modelo aninhado com escopo interno
* o modelo vinculado

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Para testar o modelo anterior e ver os resultados, use o PowerShell ou a CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

O resultado do exemplo anterior é:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

O resultado do exemplo anterior é:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Próximas etapas

* Para entender como definir parâmetros em seu modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para obter informações sobre como implantar um modelo que requer um token SAS, consulte [implantar modelo de ARM privado com token SAS](secure-template-with-sas-token.md).
