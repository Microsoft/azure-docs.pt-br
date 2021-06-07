---
title: Declarar recursos em modelos
description: Descreve como declarar recursos a serem implantados em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744205"
---
# <a name="resource-declaration-in-arm-templates"></a>Declaração de recurso em modelos ARM

Para implantar um recurso por meio de um modelo de Azure Resource Manager (modelo ARM), você adiciona uma declaração de recurso. Use a `resources` matriz para o modelo JSON ou a `resource` palavra-chave para bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Definir tipo de recurso e versão

Ao adicionar um recurso ao seu modelo, Comece definindo o tipo de recurso e a versão da API. Esses valores determinam as outras propriedades que estão disponíveis para o recurso.

O exemplo a seguir mostra como definir o tipo de recurso e a versão de API para uma conta de armazenamento. O exemplo não mostra a declaração de recurso completa.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Para bicep, você define um nome simbólico para o recurso. No exemplo anterior, o nome simbólico é `sa` . Você pode usar qualquer valor para o nome simbólico, mas ele não pode ser o mesmo que outro recurso, parâmetro ou variável no modelo. O nome simbólico não é o mesmo que o nome do recurso. Use o nome simbólico para referenciar facilmente o recurso em outras partes do seu modelo.

## <a name="set-resource-name"></a>Definir nome do recurso

Cada recurso tem um nome. Ao definir o nome do recurso, preste atenção às [regras e restrições de nomes de recursos](../management/resource-name-rules.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Definir local

Muitos recursos exigem um local. Você pode determinar se o recurso precisa de um local por meio do IntelliSense ou da [referência de modelo](/azure/templates/). O exemplo a seguir adiciona um parâmetro Location que é usado para a conta de armazenamento.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

Para obter mais informações, consulte [definir local do recurso no modelo ARM](resource-location.md).

## <a name="set-tags"></a>Definir marcas

Você pode aplicar marcas a um recurso durante a implantação. As marcas ajudam a organizar logicamente os recursos implantados. Para obter exemplos de diferentes maneiras de especificar as marcas, consulte [ARM Template Tags](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Definir propriedades específicas do recurso

As propriedades anteriores são genéricas para a maioria dos tipos de recursos. Depois de definir esses valores, você precisa definir as propriedades que são específicas para o tipo de recurso que está implantando.

Use o IntelliSense ou a [referência de modelo](/azure/templates/) para determinar quais propriedades estão disponíveis e quais delas são necessárias. O exemplo a seguir define as propriedades restantes de uma conta de armazenamento.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>Próximas etapas

* Para implantar condicionalmente um recurso, consulte [implantação condicional em modelos ARM](conditional-resource-deployment.md).
* Para definir as dependências de recurso, consulte [definir a ordem para implantar recursos em modelos ARM](define-resource-dependency.md).
