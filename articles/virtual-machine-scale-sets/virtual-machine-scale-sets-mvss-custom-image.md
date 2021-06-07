---
title: Fazer referência a uma imagem personalizada no modelo do conjunto de dimensionamento do Azure
description: Saiba como adicionar uma imagem personalizada a um modelo existente do conjunto de dimensionamento de máquinas virtuais do Azure
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/26/2018
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5ed9ee79dde73e738417031b928a675ea913179c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83124900"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento do Azure

Esse artigo mostra como modificar o [modelo do conjunto de dimensionamento básico](virtual-machine-scale-sets-mvss-start.md) para a implantação de uma imagem personalizada.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo
Em um [artigo anterior](virtual-machine-scale-sets-mvss-start.md), criamos um modelo de conjunto de dimensionamento básico. Agora vamos usar esse modelo anterior e modificá-lo para criar um modelo que implanta um conjunto de dimensionamento com base em uma imagem personalizada.  

### <a name="creating-a-managed-disk-image"></a>Criando uma imagem de disco gerenciada

Se você já tiver uma imagem de disco gerenciado personalizado (um recurso do tipo `Microsoft.Compute/images`), você poderá ignorar esta seção.

Primeiro, adicione um parâmetro `sourceImageVhdUri`, que é o URI para o blob generalizado no Armazenamento do Azure que contém a imagem personalizada a ser usada para a implantação.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Em seguida, adicione um recurso do tipo `Microsoft.Compute/images`, que é a imagem do disco gerenciado no blob generalizado localizado no URI `sourceImageVhdUri`. Essa imagem deve estar na mesma região que o conjunto de dimensionamento que a utiliza. Nas propriedades da imagem, especifique o tipo do sistema operacional, a localização do blob (do parâmetro `sourceImageVhdUri`) e o tipo de conta de armazenamento:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

No recurso de conjunto de dimensionamento, adicione uma cláusula `dependsOn` referindo-se à imagem personalizada para garantir que a imagem seja criada antes que o conjunto de dimensionamento tente implantar usando essa imagem:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Alterando as propriedades do conjunto de dimensionamento para usar a imagem de disco gerenciado

Na `imageReference` do conjunto de dimensionamento `storageProfile`, em vez especificar o editor, a oferta, o SKU e a versão de uma imagem de plataforma, especifique o `id` do recurso `Microsoft.Compute/images`:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, use a função `resourceId` para obter a ID do recurso da imagem criada no mesmo modelo. Se você já criou a imagem do disco gerenciado com antecedência, forneça a ID da imagem. Essa ID deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
