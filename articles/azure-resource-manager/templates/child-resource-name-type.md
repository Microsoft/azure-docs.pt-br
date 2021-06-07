---
title: Recursos filho em modelos
description: Descreve como definir o nome e o tipo de recursos filho em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a950d72751b829c0a2aa3ba5ca27316a0544d9cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963905"
---
# <a name="set-name-and-type-for-child-resources"></a>Definir nome e tipo para recursos filho

Recursos filho são recursos que existem somente dentro do contexto de outro recurso. Por exemplo, uma [extensão de máquina virtual](/azure/templates/microsoft.compute/virtualmachines/extensions) não pode existir sem uma [máquina virtual](/azure/templates/microsoft.compute/virtualmachines). O recurso de extensão é um filho da máquina virtual.

Cada recurso pai aceita somente determinados tipos de recurso como recursos filho. O tipo de recurso para o recurso filho inclui o tipo de recurso para o recurso pai. Por exemplo, `Microsoft.Web/sites/config` e `Microsoft.Web/sites/extensions` são recursos filho do `Microsoft.Web/sites` . Os tipos de recurso aceitos são especificados no [esquema do modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso pai.

Em um modelo de Azure Resource Manager (modelo ARM), você pode especificar o recurso filho dentro do recurso pai ou fora do recurso pai. O exemplo a seguir mostra o recurso filho incluído na propriedade Resources do recurso pai.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Os recursos filho só podem ser definidos em cinco níveis de profundidade.

O exemplo a seguir mostra o recurso filho fora do recurso pai. Você pode usar essa abordagem se o recurso pai não estiver implantado no mesmo modelo ou se quiser usar [copiar](copy-resources.md) para criar mais de um recurso filho.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Os valores que você fornece para o nome e tipo do recurso variam de acordo com a definição do recurso filho dentro ou fora do recurso pai.

## <a name="within-parent-resource"></a>Dentro do recurso pai

Quando definido dentro do tipo de recurso pai, você formata os valores de tipo e nome como uma única palavra sem barras.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

O exemplo a seguir mostra uma rede virtual e uma sub-rede. Observe que a sub-rede está incluída na matriz de recursos para a rede virtual. O nome é definido como **Subnet1** e o tipo é definido como **sub-redes**. O recurso filho é marcado como dependente do recurso pai porque o recurso pai deve existir antes que o recurso filho possa ser implantado.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

O tipo de recurso completo ainda é `Microsoft.Network/virtualNetworks/subnets` . `Microsoft.Network/virtualNetworks/` não é fornecido porque ele é presumido do tipo de recurso pai.

O nome do recurso filho é definido como **Subnet1** , mas o nome completo inclui o nome do pai. Você não fornece **VNet1** porque ele é assumido do recurso pai.

## <a name="outside-parent-resource"></a>Recurso pai externo

Quando definido fora do recurso pai, você formata o tipo e com barras para incluir o tipo e o nome pai.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

O exemplo a seguir mostra uma rede virtual e uma sub-rede que são definidas no nível raiz. Observe que a sub-rede não está incluída na matriz de recursos para a rede virtual. O nome é definido como **VNet1/Subnet1** e o tipo é definido como `Microsoft.Network/virtualNetworks/subnets` . O recurso filho é marcado como dependente do recurso pai porque o recurso pai deve existir antes que o recurso filho possa ser implantado.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de modelos do ARM, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para saber mais sobre o formato do nome do recurso ao referenciar o recurso, consulte a [função de referência](template-functions-resource.md#reference).
