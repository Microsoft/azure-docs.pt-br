---
title: Remover um tipo de nó no Azure Service Fabric | Microsoft Docs
description: Saiba como remover um tipo de nó de um cluster do Service Fabric em execução no Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 779051135a994574cb2bed7bfc4879270ec1d8fa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443028"
---
# <a name="remove-a-service-fabric-node-type"></a>Remover o tipo de nó do Service Fabric
Este artigo descreve como dimensionar um cluster do Azure Service Fabric removendo um tipo de nó existente de um cluster. Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente. Depois de criar um cluster do Service Fabric, você pode dimensionar um cluster horizontalmente removendo um tipo de nó (conjunto de dimensionamento de máquinas virtuais) e todos os seus nós.  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Use [AzServiceFabricNodeType remover](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) para remover um tipo de nó do Service Fabric.

As três operações que ocorrem quando é chamado de Remove-AzServiceFabricNodeType são:
1.  O conjunto de dimensionamento de máquinas virtuais por trás do tipo de nó é excluído.
2.  O tipo de nó é removido do cluster.
3.  Para cada nó dentro desse tipo de nó, todo o estado para esse nó é removido do sistema. Se houver serviços nesse nó, em seguida, os serviços são primeiro movidos para outro nó. Se o gerenciador de cluster não puder localizar um nó para o serviço/réplica, a operação é atrasada/bloqueada.

> [!WARNING]
> Não é recomendável usar Remove-AzServiceFabricNodeType para remover um tipo de nó de um cluster de produção a ser usado com frequência. Esse é um comando muito perigoso, pois ele exclui o recurso de conjunto de dimensionamento de máquinas virtuais por trás do tipo de nó. 

## <a name="durability-characteristics"></a>Características de durabilidade
Segurança terá prioridade sobre velocidade ao usar Remove-AzServiceFabricNodeType. O tipo de nó deve ser do [nível de durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Prata ou Ouro, porque:
- Bronze não dá nenhuma garantia sobre como salvar informações de estado.
- A durabilidade Prata ou Ouro intercepta todas as alterações para o conjunto de dimensionamento.
- Ouro também dá controle sobre as atualizações do Azure sob o conjunto de dimensionamento.

O Service Fabric "orquestra" alterações subjacentes e atualiza de forma que os dados não sejam perdidos. No entanto, quando você remove um nó com durabilidade Bronze, você pode perder informações de estado. Se você estiver removendo um tipo de nó primário e seu aplicativo estiver sem estado, Bronze é aceitável. Quando você executar cargas de trabalho com monitoramento de estado na produção, a configuração mínima deve ser Prata. Da mesma forma, cenários de produção do tipo de nó primário devem ser sempre Prata ou Ouro.

### <a name="more-about-bronze-durability"></a>Mais informações sobre durabilidade de Bronze

Ao remover um tipo de nó que é Bronze, todos os nós no tipo de nó diminuem imediatamente. O Service Fabric não intercepta todas as atualizações do conjunto de dimensionamento de nós de Bronze, assim, todas as VMs ficam inativas imediatamente. Se você tiver qualquer coisa com o monitoamento de estado em nós, os dados são perdidos. Agora, mesmo se você estivesse sem estado, todos os nós no Service Fabric participam do anel, portanto, uma vizinhança inteira pode ser perdida, o que pode desestabilizar o cluster em si.

## <a name="recommended-node-type-removal-process"></a>Recomendado o processo de remoção do tipo de nó

Para remover o tipo de nó, execute o cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype).  O cmdlet leva algum tempo para ser concluído.  Depois que todas as VMs desapareceram (representado como "Down") a malha: / System/InfrastructureService / [nome do nodetype] mostrará um estado de erro.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

Em seguida, você pode atualizar o recurso de cluster para remover o tipo de nó. Você pode usar a implantação de modelo do ARM ou editar o recurso de cluster por meio de [do Azure resource manager](https://resources.azure.com). Isso iniciará uma atualização de cluster que removerá a malha: / / InfrastructureService / [nome do nodetype] serviço do sistema que está em estado de erro.

Você ainda verá que os nós são "Down" no Service Fabric Explorer. Execute [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) em cada um dos nós que você deseja remover.


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o cluster [características de durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Saiba mais sobre [tipos de nós e Conjuntos de Dimensionamento de Máquinas Virtuais](service-fabric-cluster-nodetypes.md).
- Saiba mais sobre [Dimensionamento do cluster do Service Fabric](service-fabric-cluster-scaling.md).
