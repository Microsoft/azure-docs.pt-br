---
title: "Conversão de um modelo de conjunto de dimensionamento do Azure Resource Manager para usar disco gerenciado | Microsoft Docs"
description: "Conversão de um modelo de conjunto de dimensionamento para um modelo de conjunto de dimensionamento de disco gerenciado."
keywords: "conjuntos de escala de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 2f5cb85703888c5056611d466f508547ee72e44b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Conversão de um modelo de conjunto de dimensionamento para um modelo de conjunto de dimensionamento de disco gerenciado

Clientes com um modelo do Resource Manager para criar um conjunto de dimensionamento não usando disco gerenciado podem modificá-lo para usar um disco gerenciado. Este artigo mostra como fazer isso usando como exemplo uma solicitação pull dos [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates), um repositório de amostras de modelos do Resource Manager orientado pela comunidade. A solicitação pull completa pode ser vista em: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), e as partes relevantes da comparação estão abaixo, junto com explicações:

## <a name="making-the-os-disks-managed"></a>Como fazer os discos gerenciados do sistema operacional

Na comparação a seguir, podemos ver que removemos diversas variáveis relacionadas às propriedades de disco de armazenamento e conta de armazenamento. O tipo de conta de armazenamento não é mais necessário (Standard_LRS é o padrão), mas, se desejar, você ainda pode especificá-lo. Apenas Standard_LRS e Premium_LRS são compatíveis com um disco gerenciado. Novos sufixos de conta de armazenamento, matrizes de cadeias de caracteres exclusivas e contagem de sa foram usados no modelo antigo para gerar nomes de contas de armazenamento. Essas variáveis não são mais necessárias no modelo novo porque o disco gerenciado cria automaticamente contas de armazenamento em nome do cliente. Da mesma forma, o nome do contêiner de vhd e o nome do disco do sistema operacional não são mais necessários porque o disco gerenciado nomeia automaticamente os discos e os contêineres de blob de armazenamento subjacente.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Na comparação a seguir, podemos ver que atualizamos a versão de api de computação para 2016-04-30-preview, que é a versão mais antiga necessária para suporte de disco gerenciado com conjuntos de dimensionamento. Observe que, se desejado, ainda podemos usar discos não gerenciados na versão de api nova com a sintaxe antiga. Em outras palavras, se atualizarmos somente a versão de api de computação e não alterarmos mais nada, o modelo deverá continuar a funcionar como antes.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Na comparação a seguir, podemos ver que removemos completamente o recurso de conta de armazenamento da matriz de recursos. Não precisamos mais dele, uma vez que o disco gerenciado os cria automaticamente em nosso nome.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Na comparação a seguir, removemos os dependes na cláusula referente do conjunto de dimensionamento para o loop que criava contas de armazenamento. No modelo antigo, isso era para garantir que as contas de armazenamento fossem criadas antes do conjunto de dimensionamento iniciar a criação, mas essa cláusula não é mais necessária em um disco gerenciado. Removemos também a propriedade de contêineres do vhd e a propriedade de nome de disco do sistema operacional, uma vez que essas propriedades são tratadas automaticamente pelo disco gerenciado. Se desejar, adicione `"managedDisk": { "storageAccountType": "Premium_LRS" }` na configuração "osDisk" para discos do sistema operacional premium. Somente VMs com uma letra "s" maiúscula ou minúscula no sku da VM pode usar discos premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Não há nenhuma propriedade explícita na configuração do conjunto de dimensionamento para se usar disco gerenciado ou não gerenciado. O conjunto de dimensionamento sabe que usar com base nas propriedades que estão presentes no perfil de armazenamento. Portanto, ao modificar o modelo é importante garantir que as propriedades corretas estão no perfil de armazenamento do conjunto de dimensionamento.


## <a name="data-disks"></a>Discos de dados

Com as alterações acima, o conjunto de dimensionamento usa managed disks para o disco do sistema operacional, mas como ficam os discos de dados? Para adicionar discos de dados, adicione a propriedade "dataDisks" em "storageProfile" no mesmo nível que "osDisk". O valor da propriedade é uma lista JSON de objetos, cada um tem propriedades "lun" (que deve ser exclusiva por disco de dados em uma VM), "createOption" ("empty" atualmente é a única opção com suporte) e "diskSizeGB" (o tamanho do disco em gigabytes; deve ser maior que 0 e menor que 1024) como no exemplo a seguir: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Se você especificar `n` discos nesta matriz, cada VM no conjunto de dimensionamento obtém `n` discos de dados. No entanto, observe que estes discos de dados são dispositivos brutos. Eles não estão formatados. Cabe ao cliente anexar, particionar e formatar os discos antes de usá-los. Opcionalmente, também podemos especificar `"managedDisk": { "storageAccountType": "Premium_LRS" }` em cada objeto de disco de dados para especificar que deve ser um disco de dados premium. Somente VMs com uma letra "s" maiúscula ou minúscula no sku da VM pode usar discos premium.

Para saber mais sobre como usar discos de dados com conjuntos de dimensionamento, veja [este artigo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Próximas etapas
Para modelos do Resource Manager de exemplo usando conjuntos de escala, procure por "vmss" no [repositório github de Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter informações gerais, confira a [página de aterrissagem principal para conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

