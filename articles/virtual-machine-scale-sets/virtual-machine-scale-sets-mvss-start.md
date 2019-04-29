---
title: Saiba mais sobre os modelos do conjunto de dimensionamento de máquinas virtuais | Microsoft Docs
description: Saiba como criar um modelo de conjunto de dimensionamento mínimo viável para conjuntos de dimensionamento de máquinas virtuais
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: d4a3dd6ae390fd48a8085cca33063a6bb74bd96c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805571"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Saiba mais sobre os modelos do conjunto de dimensionamento de máquinas virtuais
Os [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) são uma excelente maneira de implantar grupos de recursos relacionados. Esta série de tutoriais mostra como criar um modelo de conjunto de dimensionamento mínimo viável e como modificar esse modelo para adaptá-lo a vários cenários. Todos os exemplos foram obtidos neste [repositório GitHub](https://github.com/gatneil/mvss). 

Esse modelo é projetado para ser simples. Para obter exemplos mais completos de modelos de conjunto de dimensionamento, consulte o [repositório GitHub de Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates) e pesquise as pastas que contêm a cadeia de caracteres `vmss`.

Se já estiver familiarizado com a criação de modelos, vá para a seção “Próximas etapas” para ver como modificar esse modelo.

## <a name="review-the-template"></a>Examinar o modelo

Use o GitHub para examinar o modelo de conjunto de dimensionamento mínimo viável, o [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

Neste tutorial, examinamos a comparação (`git diff master minimum-viable-scale-set`) para criar o modelo de conjunto de dimensionamento mínimo viável parte por parte.

## <a name="define-schema-and-contentversion"></a>Definir $schema e contentVersion
Primeiro, defina `$schema` e `contentVersion` no modelo. O elemento `$schema` define a versão da linguagem do modelo e é usado para o realce de sintaxe do Visual Studio e para recursos de validação semelhantes. O elemento `contentVersion` não é usado pelo Azure. Em vez disso, ele ajuda a manter o controle de versão do modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Definir parâmetros
Em seguida, defina dois parâmetros, `adminUsername` e `adminPassword`. Parâmetros são valores especificados no momento da implantação. O parâmetro `adminUsername` é simplesmente um tipo `string`, mas como `adminPassword` é um segredo, forneça a ele o tipo `securestring`. Mais tarde, esses parâmetros são passados na configuração do conjunto de dimensionamento.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definir variáveis
Os modelos do Resource Manager também permitem definir variáveis para serem usadas posteriormente no modelo. O exemplo não usa nenhuma variáveis, portanto, o objeto JSON está vazio.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definir recursos
Em seguida, temos a seção de recursos do modelo. Aqui, você define o que realmente deseja implantar. Ao contrário de `parameters` e `variables` (que são objetos JSON), `resources` é uma lista JSON de objetos JSON.

```json
   "resources": [
```

Todos os recursos exigem propriedades `type`, `name`, `apiVersion` e `location`. O primeiro recurso deste exemplo tem tipo [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), nome `myVnet` e apiVersion `2016-03-30`. (Para encontrar a última versão de API de um tipo de recurso, consulte a [referência do modelo do Azure Resource Manager](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Especificar o local
Para especificar o local da rede virtual, use uma [Função de modelo do Resource Manager](../azure-resource-manager/resource-group-template-functions.md). Essa função deve ser colocada entre aspas e colchetes desta forma: `"[<template-function>]"`. Nesse caso, use a função `resourceGroup`. Ele não usa nenhum argumento de entrada e retorna um objeto JSON com metadados sobre o grupo de recursos no qual essa implantação está sendo implantada. O grupo de recursos é definido pelo usuário no momento da implantação. Esse valor será indexado nesse objeto JSON com `.location` para obter o local do objeto JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Especificar as propriedades da rede virtual
Cada recurso do Resource Manager tem sua própria seção `properties` para configurações específicas ao recurso. Nesse caso, especifique que a rede virtual deve ter uma sub-rede que use o intervalo de endereços IP privado `10.0.0.0/16`. Um conjunto de dimensionamento sempre está contido em uma sub-rede. Ele não pode abranger sub-redes.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Adicionar a lista dependsOn
Além das propriedades `type`, `name`, `apiVersion` e `location` obrigatórias, cada recurso pode ter uma lista `dependsOn` opcional de cadeias de caracteres. Essa lista especifica quais outros recursos dessa implantação devem ser concluídos antes da implantação desse recurso.

Nesse caso, há somente um elemento na lista, a rede virtual do exemplo anterior. Especificamos essa dependência porque o conjunto de dimensionamento precisa que a rede exista antes de criar as VMs. Dessa forma, o conjunto de dimensionamento pode fornecer a essas VMs endereços IP privados do intervalo de endereços IP especificado anteriormente nas propriedades de rede. O formato de cada cadeia de caracteres da lista dependsOn é `<type>/<name>`. Use os mesmos `type` e `name` usados anteriormente na definição de recurso de rede virtual.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Especificar as propriedades do conjunto de dimensionamento
Os conjunto de dimensionamento têm várias propriedades para personalizar as VMs no conjunto de dimensionamento. Para obter uma lista completa dessas propriedades, consulte a [referência de modelo](/azure/templates/microsoft.compute/virtualmachinescalesets). Para este tutorial, apenas algumas propriedades mais usadas são definidas.
### <a name="supply-vm-size-and-capacity"></a>Fornecer o tamanho e a capacidade da VM
O conjunto de dimensionamento precisa saber qual tamanho de VM será criado (“nome de SKU”) e quantas dessas VMs serão criadas (“capacidade de SKU”). Para ver quais tamanhos de VM estão disponíveis, consulte a [documentação Tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Escolher o tipo de atualizações
O conjunto de dimensionamento também precisa saber como manipular atualizações no conjunto de dimensionamento. Atualmente, há duas opções, `Manual` e `Automatic`. Para obter mais informações sobre as diferenças entre as duas, consulte a documentação sobre [como atualizar um conjunto de dimensionamento](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Escolher o sistema operacional da VM
O conjunto de dimensionamento precisa saber em qual sistema operacional as VMs serão colocadas. Aqui, crie as VMs com uma imagem do Ubuntu 16.04-LTS com todos os patchs.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Especificar computerNamePrefix
O conjunto de dimensionamento implanta várias VMs. Em vez de especificar o nome de cada VM, especifique `computerNamePrefix`. O conjunto de dimensionamento acrescenta um índice ao prefixo de cada VM e, portanto, os nomes VM têm o formato `<computerNamePrefix>_<auto-generated-index>`.

No snippet a seguir, use os parâmetros anteriores para definir o nome do usuário administrador e a senha para todas as VMs no conjunto de dimensionamento. Esse processo usa o modelo de função `parameters`. Essa função usa uma cadeia de caracteres que especifica qual parâmetro deverá ser referenciado e gera o valor desse parâmetro.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Especificar a configuração de rede da VM
Por fim, especifique a configuração de rede para as VMs no conjunto de dimensionamento. Nesse caso, basta especificar a ID da sub-rede criada anteriormente. Isso informa o conjunto de dimensionamento para colocar as interfaces de rede nessa sub-rede.

Você pode obter a ID da rede virtual que contém a sub-rede usando a função de modelo `resourceId`. Essa função usa o tipo e o nome de um recurso e retorna o identificador totalmente qualificado desse recurso. Essa ID tem o formato: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

No entanto, o identificador da rede virtual não é suficiente. Forneça a sub-rede específica em que as VMs do conjunto de dimensionamento devem estar. Para fazer isso, concatene `/subnets/mySubnet` à ID da rede virtual. O resultado é a ID totalmente qualificada da sub-rede. Fazemos essa concatenação com a função `concat`, que usa uma série de cadeias de caracteres e retorna sua concatenação.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
