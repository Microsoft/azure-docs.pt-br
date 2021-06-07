---
title: Guardrails Azure Resource Manager de implantação do Service Fabric
description: Este artigo fornece uma visão geral dos erros comuns feitos ao implantar um cluster de Service Fabric por meio de Azure Resource Manager e como evitá-los.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247817"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
Ao implantar um cluster de Service Fabric, os guardrails são colocados em vigor, o que falhará em uma implantação de Azure Resource Manager no caso de uma configuração de cluster inválida. As seções a seguir fornecem uma visão geral dos problemas comuns de configuração de cluster e as etapas necessárias para mitigar esses problemas. 

## <a name="durability-mismatch"></a>Incompatibilidade de durabilidade
### <a name="overview"></a>Visão geral
O valor de durabilidade para um Service Fabric tipo de nó é definido em duas seções diferentes de um modelo de Azure Resource Manager. A seção extensão do conjunto de dimensionamento de máquinas virtuais do recurso de conjunto de dimensionamento de máquinas virtuais e a seção tipo de nó do recurso de Cluster Service Fabric. É um requisito que o valor de durabilidade nessas seções corresponde, caso contrário, a implantação de recursos falhará.

A seção a seguir contém um exemplo de incompatibilidade de durabilidade entre a configuração de durabilidade da extensão do conjunto de dimensionamento de máquinas virtuais e a configuração de durabilidade do tipo de nó Service Fabric  

**Configuração de durabilidade do conjunto de dimensionamento de máquinas virtuais**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Configuração de durabilidade do tipo de nó Service Fabric** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Mensagens de erro
* A incompatibilidade de durabilidade do conjunto de dimensionamento de máquinas virtuais não corresponde ao nível de durabilidade Service Fabric atual do tipo de nó
* A durabilidade do conjunto de dimensionamento de máquinas virtuais não corresponde ao nível de durabilidade do tipo de nó de Service Fabric
* A durabilidade do conjunto de dimensionamento de máquinas virtuais corresponde ao nível de durabilidade atual Service Fabric ou ao nível de durabilidade do tipo de nó de Service Fabric 

### <a name="mitigation"></a>Atenuação
Para corrigir uma incompatibilidade de durabilidade, que é indicada por qualquer uma das mensagens de erro acima:
1. Atualize o nível de durabilidade na seção extensão do conjunto de dimensionamento de máquinas virtuais ou Service Fabric tipo de nó do modelo de Azure Resource Manager para garantir que os valores correspondam.
2. Reimplante o modelo de Azure Resource Manager com os valores atualizados.


## <a name="seed-node-deletion"></a>Exclusão do nó de semente 
### <a name="overview"></a>Visão geral
Um Cluster Service Fabric tem uma propriedade de [camada de confiabilidade](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) que é usada para determinar o número de réplicas dos serviços do sistema que são executados no tipo de nó primário do cluster. O número de réplicas necessárias determinará o número mínimo de nós que devem ser mantidos no tipo de nó primário do cluster. Se o número de nós no tipo de nó primário ficar abaixo do mínimo necessário para a camada de confiabilidade, o cluster ficará instável.  

### <a name="error-messages"></a>Mensagens de erro 
A operação de remoção do nó de semente foi detectada e será rejeitada. 
* Essa operação resultaria apenas em {0} possíveis nós de semente permanecerem no cluster, enquanto {1} são necessários no mínimo.
* Remover {0} nós de semente para fora do {1} resultaria no desligamento do cluster devido à perda do quorum do nó de semente. O número máximo de nós de semente que podem ser removidos por vez é {2} .
 
### <a name="mitigation"></a>Atenuação 
Verifique se o tipo de nó primário tem máquinas virtuais suficientes para a confiabilidade especificada no cluster. Você não poderá remover uma máquina virtual se ela for colocar o conjunto de dimensionamento de máquinas virtuais abaixo do número mínimo de nós para a camada de confiabilidade fornecida.
* Se a camada de confiabilidade for especificada corretamente, verifique se você tem nós suficientes no tipo de nó primário conforme necessário para a camada de confiabilidade. 
* Se a camada de confiabilidade estiver incorreta, inicie uma alteração no recurso de Service Fabric para reduzir o nível de confiabilidade primeiro antes de iniciar qualquer operação do conjunto de dimensionamento de máquinas virtuais e aguarde a conclusão.
* Se a camada de confiabilidade for bronze, siga estas [etapas](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) para dimensionar o cluster normalmente.

## <a name="next-steps"></a>Próximas etapas
* Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou em computadores que estejam executando o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Solucionar problemas Service Fabric: [guias de solução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
