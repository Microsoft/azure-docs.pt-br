---
title: Escalar ou reduzir horizontalmente um cluster do Service Fabric | Microsoft Docs
description: "Escale ou reduza horizontalmente um cluster do Service Fabric para que ele corresponda à demanda, definindo regras de dimensionamento automático para cada tipo de nó/conjunto de dimensionamento de máquinas virtuais. Adicionar ou remover nós de um cluster do Service Fabric"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: d26a97ee0e5416fb1fe38ef0fb18fa4eb0e2963d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Escalar ou reduzir horizontalmente um cluster do Service Fabric usando regras de autoescala
Os conjuntos de escala de Máquina Virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric está configurado como um conjunto de dimensionamento de máquinas virtuais separado. Cada tipo de nó pode ser escalado ou reduzido horizontalmente de forma independente, ter conjuntos diferentes de portas abertas e métricas de capacidade diferentes. Leia mais sobre isso no documento [Tipos de nó do Service Fabric](service-fabric-cluster-nodetypes.md) . Uma vez que os tipos de nó do Service Fabric no cluster são compostos por conjuntos de dimensionamento de máquinas virtuais no back-end, você precisa configurar regras de dimensionamento automático para cada tipo de nó/conjunto de dimensionamento de máquinas virtuais.

> [!NOTE]
> Sua assinatura precisa ter uma quantidade suficiente de núcleos para adicionar as novas VMs que compõe esse cluster. Atualmente, não há nenhuma validação de modelo. Logo, você receberá uma falha de tempo de implantação se qualquer um dos limites de cota for atingido.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Escolher o tipo de nó/conjunto de dimensionamento de máquinas virtuais a ser dimensionado
Atualmente, não é possível especificar as regras de dimensionamento automático para os conjuntos de dimensionamento de máquinas virtuais usando o portal, por esse motivo, vamos usar o Azure PowerShell (1.0+) para listar os tipos de nó e, em seguida, adicionar as regras de dimensionamento automático a eles.

Para obter a lista de conjuntos de dimensionamento de máquinas virtuais que compõem o cluster, execute os seguintes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Definir regras de dimensionamento automático para o tipo de nó/conjunto de dimensionamento de máquinas virtuais
Se o cluster tiver vários tipos de nós, repita este procedimento para cada tipo de nó/conjuntos de dimensionamento de máquinas virtuais que você quiser escalar ou reduzir horizontalmente. Leve em conta o número de nós que você precisa ter antes de configurar o dimensionamento automático. O número mínimo de nós necessários para o tipo de nó primário é controlado pelo nível de confiabilidade escolhido. Leia mais sobre os [níveis de confiabilidade](service-fabric-cluster-capacity.md).

> [!NOTE]
> A redução vertical do tipo de nó primário para um número inferior ao mínimo causará a instabilidade ou indisponibilidade do cluster. Isso poderá resultar em perda de dados de seus aplicativos e dos serviços do sistema.
> 
> 

Atualmente, o recurso de escala automática não é controlado pelas cargas que os aplicativos podem relatar ao Service Fabric. Portanto, nesse momento, o dimensionamento automático obtido é meramente controlado pelos contadores de desempenho que são emitidos por cada uma das instâncias do conjunto de dimensionamento de máquinas virtuais.  

Siga estas instruções [para configurar o dimensionamento automático para cada conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Em um cenário de redução vertical, a menos que o tipo de nó tenha um nível de durabilidade Gold ou Silver, você precisará chamar o cmdlet [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) pelo nome de nó apropriado.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Adicionar as VMs manualmente a um tipo de nó/conjunto de dimensionamento de máquinas virtuais
Siga os modelos/instruções da [galeria de modelos de Início Rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada Nodetype. 

> [!NOTE]
> O processo de adicionar VMs leva tempo. Portanto, não espere que as adições sejam instantâneas. Sendo assim, planeje adicionar capacidade pontual para permitir mais 10 minutos antes que a capacidade da VM esteja disponível para que as réplicas/instâncias de serviço sejam alocadas.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Remover as VMs manualmente do tipo de nó primário/conjunto de dimensionamento de máquinas virtuais
> [!NOTE]
> Os serviços do sistema Service Fabric são executados no tipo de nó Primário do cluster. Deste modo, nunca desligue ou reduza verticalmente o número de instâncias neste tipo de nó para uma quantidade menor do que a camada de confiabilidade garante. Confira [os detalhes sobre os níveis de confiabilidade aqui](service-fabric-cluster-capacity.md). 
> 
> 

Você precisa executar as etapas a seguir em uma instância VM por vez. Isso permite que os serviços do sistema (e seus serviços com estado) sejam desligados normalmente na instância VM que você estiver removendo e que novas réplicas sejam criadas em outros nós.

1. Execute [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) com a intenção 'RemoveNode' para desabilitar o nó que você removerá (a instância mais alta deste tipo de nó).
2. Execute [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para verificar se o nó realmente foi desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Não tenha pressa nesta etapa.
3. Siga os modelos/instruções da [galeria de modelos de Início Rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar a quantidade de VMs para uma no Nodetype. A instância removida é a instância VM mais alta. 
4. Repita as etapas 1 a 3 conforme necessário, mas nunca reduza verticalmente o número de instâncias no tipo de nó primário para uma quantidade menor do que a camada de confiabilidade garante. Confira [os detalhes sobre os níveis de confiabilidade aqui](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Remover as VMs manualmente do tipo de nó não primário/conjunto de dimensionamento de máquinas virtuais
> [!NOTE]
> Para um serviço com estado, você precisa de um determinado número de nós que devem estar sempre ativos para manter a disponibilidade e preservar o estado do serviço. No mínimo, é necessário que o número de nós seja igual à contagem de conjunto de réplicas de destino do serviço/partição. 
> 
> 

Você precisa executar as seguintes etapas em uma instância VM por vez. Isso permite que os serviços do sistema (e seus serviços com estado) sejam desligados normalmente na instância VM que você estiver removendo e que novas réplicas sejam criadas em outro lugar.

1. Execute [Disable-ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) com a intenção 'RemoveNode' para desabilitar o nó que você removerá (a instância mais alta deste tipo de nó).
2. Execute [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para verificar se o nó realmente foi desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Não tenha pressa nesta etapa.
3. Siga os modelos/instruções da [galeria de modelos de Início Rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar a quantidade de VMs para uma no Nodetype. Agora, isso removerá a instância VM mais alta. 
4. Repita as etapas 1 a 3 conforme necessário, mas nunca reduza verticalmente o número de instâncias no tipo de nó primário para uma quantidade menor do que a camada de confiabilidade garante. Confira [os detalhes sobre os níveis de confiabilidade aqui](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos que podem ser observados no Service Fabric Explorer
Ao escalar verticalmente um cluster, o Service Fabric Explorer refletirá o número de nós (instâncias de conjunto de dimensionamento de máquinas virtuais) que fazem parte do cluster.  No entanto, ao reduzir verticalmente um cluster, você verá o nó removido/instância VM exibida em um estado não íntegro, a menos que você chame o cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) pelo nome de nó apropriado.   

Veja a seguir a explicação para esse comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços do sistema do Service Fabric (especificamente, FM) conhecem sobre o número de nós que o cluster tinha/tem. Ao reduzir verticalmente o conjunto de dimensionamento de máquinas virtuais, observe que a VM foi excluída, mas o serviço do sistema do FM ainda acreditará que o nó (mapeado para a VM que foi excluída) voltará. Portanto, o Service Fabric Explorer continua exibindo o nó (embora o estado de integridade possa ser erro ou desconhecido).

Para certificar-se de que um nó será removido quando uma VM for removida, você tem duas opções:

1) Escolha um nível de durabilidade de Gold ou Silver para os tipos de nó no seu cluster, o que fornece a integração de infraestrutura. Isso, em seguida, removerá automaticamente os nós do estado (FM) dos serviços do sistema ao reduzir verticalmente.
Confira [os detalhes sobre os níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

2) Depois que a instância VM tiver sido reduzida verticalmente, você precisará chamar o [cmdlet Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

> [!NOTE]
> Os clusters de Service Fabric exigem um determinado número de nós que devem estar ativos o tempo todo para manter a disponibilidade e preservar o estado – conhecido como "manter o quórum". Desta forma, em geral, não é seguro desligar todos os computadores no cluster, a menos que você tenha executado primeiro um [backup completo do estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos para saber também sobre como planejar a capacidade do cluster, atualizar um cluster e particionar os serviços:

* [Planejar a capacidade do cluster](service-fabric-cluster-capacity.md)
* [Atualizações do cluster](service-fabric-cluster-upgrade.md)
* [Serviços com estado de partição para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
