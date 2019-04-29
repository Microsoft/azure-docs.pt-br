---
title: Conversão de um modelo de conjunto de dimensionamento do Azure Resource Manager para usar disco gerenciado | Microsoft Docs
description: Conversão de um modelo de conjunto de dimensionamento para um modelo de conjunto de dimensionamento de disco gerenciado.
keywords: conjuntos de escala de máquina virtual
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: b2d1738b85799079b3af7ab39c5cb1799a38d382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731730"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Conversão de um modelo de conjunto de dimensionamento para um modelo de conjunto de dimensionamento de disco gerenciado

Clientes com um modelo do Resource Manager para criar um conjunto de dimensionamento não usando disco gerenciado podem modificá-lo para usar um disco gerenciado. Este artigo mostra como usar discos gerenciados, usando como um exemplo de uma solicitação de pull dos [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates), um repositório gerenciado pela comunidade que oferece modelos do Resource Manager. A solicitação completa de pull pode ser vista aqui: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), e as partes relevantes do diff estão abaixo, junto com as explicações:

## <a name="making-the-os-disks-managed"></a>Como fazer os discos gerenciados do sistema operacional

Na comparação a seguir, diversas variáveis relacionadas às propriedades de disco e de conta de armazenamento foram removidas. O tipo de conta de armazenamento não é mais necessário (Standard_LRS é o padrão), mas você poderá especificá-lo caso deseje. Apenas Standard_LRS e Premium_LRS são compatíveis com um disco gerenciado. Novos sufixos de conta de armazenamento, matrizes de cadeias de caracteres exclusivas e contagem de sa foram usados no modelo antigo para gerar nomes de contas de armazenamento. Essas variáveis não são mais necessárias no modelo novo porque o disco gerenciado cria automaticamente contas de armazenamento em nome do cliente. Da mesma forma, o nome do contêiner de vhd e o nome do disco do sistema operacional não são mais necessários porque o disco gerenciado nomeia automaticamente os discos e os contêineres de blobs de armazenamento subjacentes.

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


Na comparação a seguir, a API de computação foi atualizada para a versão 2016-04-30 – versão prévia, que é a versão mais antiga necessária para suporte de disco gerenciado com conjuntos de dimensionamento. Você pode usar discos não gerenciados na nova versão da API com a sintaxe antiga, se desejado. Se você atualizar somente a versão da API de computação e não alterar mais nada, o modelo continuará a funcionar como antes.

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

Na comparação a seguir, o recurso de conta de armazenamento foi completamente removido da matriz de recursos. O recurso não é mais necessário, pois o disco gerenciado cria-o automaticamente.

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

Na comparação a seguir, estamos removendo os dependentes da cláusula que se referem do conjunto de dimensionamento para o loop que estava criando as contas de armazenamento. No modelo antigo, isso era para garantir que as contas de armazenamento fossem criadas antes do conjunto de dimensionamento iniciar a criação, mas essa cláusula não é mais necessária em um disco gerenciado. A propriedade de contêineres vhd também foi removida, juntamente com a propriedade de nome de disco do sistema operacional porque essas propriedades são tratadas automaticamente nos bastidores pelo disco gerenciado. Você poderá adicionar `"managedDisk": { "storageAccountType": "Premium_LRS" }` à configuração "osDisk" se desejar os discos premium do sistema operacional. Somente VMs com uma letra "s" maiúscula ou minúscula no sku da VM pode usar discos premium.

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

Se você especificar `n` discos nesta matriz, cada VM no conjunto de dimensionamento obtém `n` discos de dados. No entanto, observe que estes discos de dados são dispositivos brutos. Eles não estão formatados. Cabe ao cliente anexar, particionar e formatar os discos antes de usá-los. Opcionalmente, você também pode especificar `"managedDisk": { "storageAccountType": "Premium_LRS" }` em cada objeto de disco de dados para especificar que ele deve ser um disco de dados premium. Somente VMs com uma letra "s" maiúscula ou minúscula no sku da VM pode usar discos premium.

Para saber mais sobre como usar discos de dados com conjuntos de dimensionamento, veja [este artigo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Próximas etapas
Para modelos do Resource Manager de exemplo usando conjuntos de dimensionamento, pesquise "vmss" no [repositório GitHub de Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter informações gerais, confira a [página de aterrissagem principal para conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

