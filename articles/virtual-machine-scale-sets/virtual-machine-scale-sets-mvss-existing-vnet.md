---
title: Fazer referência a uma rede virtual existente em um modelo do conjunto de dimensionamento do Azure
description: Saiba como adicionar uma rede virtual a um modelo existente do conjunto de dimensionamento de máquinas virtuais do Azure
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 04/26/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: fab6e6742fa43e1e38ee661b67896ae4aa11b3ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "83124815"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adicionar referência a uma rede virtual existente em um modelo do conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento básico](virtual-machine-scale-sets-mvss-start.md) para implantar em uma rede virtual existente em vez de criar um novo.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Em um [artigo anterior](virtual-machine-scale-sets-mvss-start.md), criamos um modelo de conjunto de dimensionamento básico. Agora, usaremos esse modelo anterior e o modificaremos para criar um modelo que implanta um conjunto de dimensionamento em uma rede virtual existente. 

Primeiro, adicione um parâmetro `subnetId`. Essa cadeia de caracteres será passada para a configuração do conjunto de dimensionamento, permitindo que o conjunto de dimensionamento identifique a sub-rede pré-criada na qual implantará máquinas virtuais. Essa cadeia de caracteres deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Por exemplo implantar o conjunto de dimensionamento em uma rede virtual existente com o nome `myvnet`, sub-rede `mysubnet`, grupo de recursos `myrg` e assinatura`00000000-0000-0000-0000-000000000000`, o subnetId seria: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Em seguida, exclua o recurso de rede virtual da matriz `resources`, pois você usará uma rede virtual existente e não precisará implantar uma nova.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

A rede virtual já existe antes do modelo ser implantado e, portanto, não é necessário especificar uma cláusula dependsOn do conjunto de dimensionamento para a rede virtual. Exclua as seguintes linhas:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Por fim, passe o `subnetId` parâmetro definido pelo usuário (em vez de usar `resourceId` para obter a ID de uma vnet na mesma implantação, que é o que o modelo de conjunto de dimensionamento viável básico faz).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
