---
title: Funções definidas pelo usuário em modelos
description: Descreve como definir e usar funções definidas pelo usuário em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: f428fa3bc827af3820ad9f928f4f92b881c9c84c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934672"
---
# <a name="user-defined-functions-in-arm-template"></a>Funções definidas pelo usuário no modelo ARM

Dentro de seu modelo, você pode criar suas próprias funções. Essas funções estão disponíveis para uso em seu modelo. As funções definidas pelo usuário são separadas das [funções de modelo padrão](template-functions.md) que estão automaticamente disponíveis no seu modelo. Crie suas próprias funções quando tiver expressões complicadas que são usadas repetidamente em seu modelo.

Este artigo descreve como adicionar funções definidas pelo usuário em seu modelo de Azure Resource Manager (modelo ARM).

## <a name="define-the-function"></a>Definir a função

As suas funções exigem um valor de namespace para evitar conflitos de nomenclatura com funções de modelo. O exemplo a seguir mostra uma função que retorna um nome exclusivo:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Usar a função

O exemplo a seguir mostra um modelo que inclui uma função definida pelo usuário. Ele usa essa função para obter um nome exclusivo para uma conta de armazenamento. O modelo tem um parâmetro chamado `storageNamePrefix` que ele passa como um parâmetro para a função.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Limitações

Ao definir uma função de usuário, há algumas restrições:

* A função não pode acessar variáveis.
* A função só pode usar os parâmetros que são definidos na função. Quando você usa a função [Parameters](template-functions-deployment.md#parameters) em uma função definida pelo usuário, você está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo usuário.
* A função não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções de [lista](template-functions-resource.md#list) .
* Os parâmetros para a função não podem ter valores padrão.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para funções definidas pelo usuário, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para obter uma lista das funções de modelo disponíveis, consulte [ARM template Functions](template-functions.md).
