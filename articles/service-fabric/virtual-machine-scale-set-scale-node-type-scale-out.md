---
title: Adicionar um tipo de nó a um cluster de Service Fabric do Azure
description: Aprenda como dimensionar um cluster do Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: efd329c07b4881c6710d4173857b4186965438d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719318"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Dimensione um cluster do Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais
Este artigo descreve como dimensionar um cluster do Azure Service Fabric adicionando um novo tipo de nó a um cluster existente. Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente. Depois de criar um cluster do Service Fabric, você pode dimensionar um cluster horizontalmente adicionando um novo tipo de nó (conjunto de dimensionamento de máquinas virtuais) a um cluster existente.  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Adicionar mais um conjunto de dimensionamento a um cluster existente
Adicionar um novo tipo de nó (que é apoiado por um conjunto de dimensionamento de máquinas virtuais) a um cluster existente é semelhante à [atualização do tipo de nó primário](service-fabric-scale-up-primary-node-type.md), exceto que você não usará o mesmo NodeTypeRef; Obviamente, não estará desabilitando nenhum conjunto de dimensionamento de máquinas virtuais ativamente utilizado, e você não perderá a disponibilidade do cluster se não atualizar o tipo de nó primário. 

A propriedade NodeTypeRef é declarada dentro das propriedades de extensão do Service Fabric de conjunto de dimensionamento de máqunas virtuais:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Além disso, você precisará adicionar esse novo tipo de nó para o recurso de cluster do Service Fabric:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Próximas etapas
* Aprenda como [expandir o tipo de nó primário](service-fabric-scale-up-primary-node-type.md)
* Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
* [Reduzir horizontalmente ou escalar horizontalmente um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure de forma programática](service-fabric-cluster-programmatic-scaling.md) usando a SDK fluente de computação do Azure.
* [Dimensione um cluster autônomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).

