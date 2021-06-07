---
title: Saídas em modelos
description: Descreve como definir valores de saída em um modelo de Azure Resource Manager (modelo ARM) e arquivo bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 2b6a6afa127bf43102103baadae576233843f00d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123404"
---
# <a name="outputs-in-arm-templates"></a>Saídas em modelos ARM

Este artigo descreve como definir valores de saída em seu modelo de Azure Resource Manager (modelo ARM) e arquivo bicep. Você usa saídas quando precisa retornar valores dos recursos implantados.

O formato de cada valor de saída deve ser resolvido para um dos [tipos de dados](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Definir valores de saída

O exemplo a seguir mostra como retornar uma propriedade de um recurso implantado.

# <a name="json"></a>[JSON](#tab/json)

Para JSON, adicione a `outputs` seção ao modelo. O valor de saída Obtém o nome de domínio totalmente qualificado para um endereço IP público.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Para bicep, use a `output` palavra-chave.

No exemplo a seguir, `publicIP` é o identificador de um endereço IP público implantado no arquivo bicep. O valor de saída Obtém o nome de domínio totalmente qualificado para o endereço IP público.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

Se você precisar gerar uma propriedade que tenha um hífen no nome, use colchetes ao invés do nome em vez de notação de ponto. Por exemplo, use  `['property-name']` em vez de `.property-name` .

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Saída condicional

Você pode retornar um valor condicionalmente. Normalmente, você usa uma saída condicional quando tiver [implantado](conditional-resource-deployment.md) um recurso condicionalmente. O exemplo a seguir mostra como retornar condicionalmente a ID de recurso para um endereço IP público com base no fato de um novo ter sido implantado:

# <a name="json"></a>[JSON](#tab/json)

Em JSON, adicione o `condition` elemento para definir se a saída é retornada.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Para especificar uma saída condicional em bicep, use o `?` operador. O exemplo a seguir retorna uma URL de ponto de extremidade ou uma cadeia de caracteres vazia, dependendo de uma condição.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

Para obter um exemplo simples de saída condicional, consulte [modelo de saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Número dinâmico de saídas

Em alguns cenários, você não sabe o número de instâncias de um valor que precisa retornar ao criar o modelo. Você pode retornar um número variável de valores usando a saída iterativa.

# <a name="json"></a>[JSON](#tab/json)

Em JSON, adicione o `copy` elemento para iterar uma saída.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

A saída iterativa não está disponível no momento para bicep.

---

Para obter mais informações, consulte [Iteration de saída em modelos ARM](copy-outputs.md).

## <a name="linked-templates"></a>Modelos vinculados

Em modelos JSON, você pode implantar modelos relacionados usando [modelos vinculados](linked-templates.md). Para recuperar o valor de saída de um modelo vinculado, use a função de [referência](template-functions-resource.md#reference) no modelo pai. A sintaxe no modelo pai é:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

O exemplo a seguir mostra como definir o endereço IP em um balanceador de carga recuperando um valor de um modelo vinculado.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Se o nome da propriedade tiver um hífen, use colchetes ao invés do nome em vez da notação de ponto.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

Não é possível usar a `reference` função na seção de saídas de um [modelo aninhado](linked-templates.md#nested-template). Para retornar os valores de um recurso implantado em um modelo aninhado, converta seu modelo aninhado em um modelo vinculado.

O [modelo de endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) cria um endereço IP público e gera a ID do recurso. O [modelo de balanceador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) vincula-se ao modelo anterior. Ele usa a ID do recurso na saída ao criar o balanceador de carga.

## <a name="modules"></a>Módulos

Em arquivos bicep, você pode implantar modelos relacionados usando módulos. Para recuperar um valor de saída de um módulo, use a seguinte sintaxe:

```bicep
<module-name>.outputs.<property-name>
```

O exemplo a seguir mostra como definir o endereço IP em um balanceador de carga recuperando um valor de um módulo. O nome do módulo é `publicIP` .

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Modelo de exemplo

O modelo a seguir não implanta nenhum recurso. Ele mostra algumas maneiras de retornar saídas de tipos diferentes.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Atualmente, o bicep não dá suporte a loops.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Obter valores de saída

Quando a implantação for realizada com sucesso, os valores de saída serão retornados automaticamente nos resultados da implantação.

Para obter valores de saída do histórico de implantação, você pode usar o script.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para saídas, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
