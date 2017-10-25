---
title: "Fazer referência a uma imagem personalizada no modelo do conjunto de dimensionamento do Azure | Microsoft Docs"
description: "Saiba como adicionar uma imagem personalizada a um modelo existente do conjunto de dimensionamento de máquinas virtuais do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: cf52fc9e95267c4bc5c0106aadf626685ddd5c24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento do Azure

Esse artigo mostra como modificar o [modelo do conjunto de dimensionamento mínimo viável](./virtual-machine-scale-sets-mvss-start.md) para a implantação de uma imagem personalizada.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Nosso modelo de conjunto de dimensionamento mínimo viável pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json) e nosso modelo para implantar o conjunto de dimensionamento de uma imagem personalizada pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Vamos examinar a comparação usada para criar esse modelo (`git diff minimum-viable-scale-set custom-image`), parte por parte:

### <a name="creating-a-managed-disk-image"></a>Criando uma imagem de disco gerenciada

Se você já tiver uma imagem de disco gerenciado personalizado (um recurso do tipo `Microsoft.Compute/images`), você poderá ignorar esta seção.

Primeiro, vamos adicionar um parâmetro `sourceImageVhdUri`, que é o URI para o blob generalizado no Armazenamento do Azure que contém a imagem personalizada da qual implantar.


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

Em seguida, adicionamos um recurso do tipo `Microsoft.Compute/images`, que é a imagem de disco gerenciada com base no blob generalizado localizado no URI `sourceImageVhdUri`. Essa imagem deve estar na mesma região que o conjunto de dimensionamento que a utiliza. Nas propriedades da imagem, especificamos o tipo do sistema operacional, a localização do blob (do parâmetro `sourceImageVhdUri`) e o tipo de conta de armazenamento:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
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

No recurso de conjunto de dimensionamento, adicionamos uma cláusula `dependsOn` referindo-se à imagem personalizada para garantir que a imagem seja criada antes de o conjunto de dimensionamento tentar realizar a implantação dessa imagem:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Alterando as propriedades do conjunto de dimensionamento para usar a imagem de disco gerenciado

Na `imageReference` do conjunto de dimensionamento `storageProfile`, em vez especificar o editor, a oferta, o SKU e a versão de uma imagem de plataforma, especificamos o `id` do recurso `Microsoft.Compute/images`:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, usamos a função `resourceId` para obter a ID de recurso da imagem criada no mesmo modelo. Se você criou a imagem de disco gerenciado com antecedência, deve fornecer a ID da imagem. Essa ID deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
