---
title: Parâmetros em modelos
description: Descreve como definir parâmetros em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 929d871bc0087d4fda585773b349dee4e0945c7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934706"
---
# <a name="parameters-in-arm-templates"></a>Parâmetros em modelos ARM

Este artigo descreve como definir e usar parâmetros em seu modelo de Azure Resource Manager (modelo ARM). Ao fornecer valores diferentes para parâmetros, você pode reutilizar um modelo para ambientes diferentes.

O Resource Manager resolve os valores de parâmetro antes de iniciar as operações de implantação. Sempre que o parâmetro for usado no modelo, o Resource Manager o substituirá pelo valor resolvido.

Cada parâmetro deve ser definido como um dos [tipos de dados](template-syntax.md#data-types).

## <a name="define-parameter"></a>Definir parâmetro

O exemplo a seguir mostra uma definição de parâmetro simples. Ele define um parâmetro chamado `storageSKU` . O parâmetro é um valor de cadeia de caracteres e aceita apenas valores que são válidos para seu uso pretendido. O parâmetro usa um valor padrão quando nenhum valor é fornecido durante a implantação.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Usar parâmetro

No modelo, você faz referência ao valor do parâmetro usando a função [Parameters](template-functions-deployment.md#parameters) . No exemplo a seguir, o valor do parâmetro é usado para definir a SKU para a conta de armazenamento.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Funções de modelo

Ao especificar o valor padrão para um parâmetro, você pode usar a maioria das funções de modelo. Você pode usar outro valor de parâmetro para criar um valor padrão. O modelo a seguir demonstra o uso de funções no valor padrão. Quando nenhum nome é fornecido para o site, ele cria um valor de cadeia de caracteres exclusivo e o anexa ao **site**. Quando nenhum nome for fornecido para o plano de host, ele usará o valor para o site e acrescentará o **plano**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Você não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções de [lista](template-functions-resource.md#list) na seção de parâmetros. Essas funções obtêm o estado de tempo de execução de um recurso e não podem ser executadas antes da implantação quando os parâmetros são resolvidos.

## <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados, passando-os como um objeto. Essa abordagem também reduz o número de parâmetros no modelo.

O exemplo a seguir mostra um parâmetro que é um objeto. O valor padrão mostra as propriedades esperadas para o objeto.

```json
"parameters": {
  "VNetSettings": {
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
```

Você faz referência às propriedades do objeto usando o operador ponto.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

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
