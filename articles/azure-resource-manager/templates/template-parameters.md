---
title: Parâmetros em modelos
description: Descreve como definir parâmetros em um modelo de Azure Resource Manager (modelo ARM) e arquivo bicep.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: a8608e4733960d7f5ba7e5f548c47f16a2b244bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123387"
---
# <a name="parameters-in-arm-templates"></a>Parâmetros em modelos ARM

Este artigo descreve como definir e usar parâmetros em seu modelo de Azure Resource Manager (modelo ARM) e arquivo bicep. Ao fornecer valores diferentes para parâmetros, você pode reutilizar um modelo para ambientes diferentes.

O Resource Manager resolve os valores de parâmetro antes de iniciar as operações de implantação. Sempre que o parâmetro for usado no modelo, o Resource Manager o substituirá pelo valor resolvido.

Cada parâmetro deve ser definido como um dos [tipos de dados](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>Declaração mínima

No mínimo, cada parâmetro precisa de um nome e tipo. No bicep, um parâmetro não pode ter o mesmo nome que uma variável, recurso, saída ou outro parâmetro no mesmo escopo.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>Parâmetros de segurança

Você pode marcar cadeias de caracteres ou parâmetros de objeto como seguros. O valor de um parâmetro seguro não é salvo no histórico de implantação e não é registrado.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>Valores permitidos

Você pode definir valores permitidos para um parâmetro. Você fornece os valores permitidos em uma matriz. A implantação falhará durante a validação se um valor for passado para o parâmetro que não é um dos valores permitidos.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>Valor padrão

Você pode especificar um valor padrão para um parâmetro. O valor padrão é usado quando um valor não é fornecido durante a implantação.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

Para especificar um valor padrão junto com outras propriedades para o parâmetro, use a sintaxe a seguir.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

Você pode usar expressões com o valor padrão. Você não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções de [lista](template-functions-resource.md#list) na seção de parâmetros. Essas funções obtêm o estado de tempo de execução de um recurso e não podem ser executadas antes da implantação quando os parâmetros são resolvidos.

Expressões não são permitidas com outras propriedades de parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Você pode usar outro valor de parâmetro para criar um valor padrão. O modelo a seguir constrói um nome de plano de host a partir do nome do site.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

---

## <a name="length-constraints"></a>Restrições de comprimento

Você pode especificar comprimentos mínimo e máximo para parâmetros de cadeia de caracteres e matriz. Você pode definir uma ou ambas as restrições. Para Strings, o comprimento indica o número de caracteres. Para matrizes, o comprimento indica o número de itens na matriz.

O exemplo a seguir declara dois parâmetros. Um parâmetro é para um nome de conta de armazenamento que deve ter de 1 a 3-24 caracteres. O outro parâmetro é uma matriz que deve ter de 1-5 itens.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>Restrições de inteiro

Você pode definir valores mínimo e máximo para parâmetros de inteiro. Você pode definir uma ou ambas as restrições.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>Descrição

Você pode adicionar uma descrição a um parâmetro para ajudar os usuários do seu modelo a entender o valor a ser fornecido. Ao implantar o modelo por meio do portal, o texto que você fornece na descrição é usado automaticamente como uma dica para esse parâmetro. Somente adicione uma descrição quando o texto fornecer mais informações do que pode ser inferido do nome do parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>Usar parâmetro

Em um modelo JSON, você faz referência ao valor do parâmetro usando a função [Parameters](template-functions-deployment.md#parameters) . No bicep, você usa o nome do parâmetro. O exemplo a seguir usa um valor de parâmetro para um nome de Key Vault.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados, passando-os como um objeto. Essa abordagem também reduz o número de parâmetros no modelo.

O exemplo a seguir mostra um parâmetro que é um objeto. O valor padrão mostra as propriedades esperadas para o objeto. Essas propriedades são usadas ao definir o recurso a ser implantado.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir demonstram cenários para o uso de parâmetros.

|Modelo  |Descrição  |
|---------|---------|
|[parâmetros com funções de valores padrão](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como usar funções de modelo ao definir valores padrão para parâmetros. O modelo não implanta todos os recursos. Ele cria valores de parâmetro e retorna os valores. |
|[objeto de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra como usar um objeto para um parâmetro. O modelo não implanta todos os recursos. Ele cria valores de parâmetro e retorna os valores. |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para parâmetros, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para saber mais sobre a passagem de valores de parâmetro como um arquivo, consulte [criar arquivo de parâmetro do Resource Manager](parameter-files.md).
* Para obter recomendações sobre como criar parâmetros, consulte [Best Practices-Parameters](template-best-practices.md#parameters).
