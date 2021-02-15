---
title: Remover um tipo de nó no Azure Service Fabric | Microsoft Docs
description: Saiba como remover um tipo de nó de um cluster do Service Fabric em execução no Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163570"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Como remover um tipo de nó Service Fabric
Este artigo descreve como dimensionar um cluster do Azure Service Fabric removendo um tipo de nó existente de um cluster. Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente. Depois de criar um cluster do Service Fabric, você pode dimensionar um cluster horizontalmente removendo um tipo de nó (conjunto de dimensionamento de máquinas virtuais) e todos os seus nós.  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

> [!WARNING]
> O uso dessa abordagem para remover um tipo de nó de um cluster de produção não é recomendável para ser usado com frequência. Esse é um comando muito perigoso, pois ele exclui o recurso de conjunto de dimensionamento de máquinas virtuais por trás do tipo de nó. 

## <a name="durability-characteristics"></a>Características de durabilidade
A segurança é priorizada em velocidade ao usar remove-AzServiceFabricNodeType. O tipo de nó deve ser do [nível de durabilidade](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Prata ou Ouro, porque:
- Bronze não dá nenhuma garantia sobre como salvar informações de estado.
- A durabilidade Prata ou Ouro intercepta todas as alterações para o conjunto de dimensionamento.
- Ouro também dá controle sobre as atualizações do Azure sob o conjunto de dimensionamento.

O Service Fabric "orquestra" alterações subjacentes e atualiza de forma que os dados não sejam perdidos. No entanto, quando você remove um tipo de nó com durabilidade bronze, você pode perder informações de estado. Se você estiver removendo um tipo de nó primário e seu aplicativo estiver sem estado, bronze será aceitável. Quando você executar cargas de trabalho com monitoramento de estado na produção, a configuração mínima deve ser Prata. Da mesma forma, cenários de produção do tipo de nó primário devem ser sempre Prata ou Ouro.

### <a name="more-about-bronze-durability"></a>Mais informações sobre durabilidade de Bronze

Ao remover um tipo de nó que é Bronze, todos os nós no tipo de nó diminuem imediatamente. O Service Fabric não intercepta todas as atualizações do conjunto de dimensionamento de nós de Bronze, assim, todas as VMs ficam inativas imediatamente. Se você tiver qualquer coisa com o monitoamento de estado em nós, os dados são perdidos. Agora, mesmo se você estivesse sem estado, todos os nós no Service Fabric participam do anel, portanto, uma vizinhança inteira pode ser perdida, o que pode desestabilizar o cluster em si.

## <a name="remove-a-node-type"></a>Remover um tipo de nó

1. Tome cuidado com esses pré-requisitos antes de iniciar o processo.

    - O cluster está íntegro.
    - Ainda haverá capacidade suficiente depois que o tipo de nó for removido, por exemplo, número de nós para inserir a contagem de réplicas necessárias.

2. Mova todos os serviços que têm restrições de posicionamento para usar o tipo de nó fora do tipo de nó.

    - Modifique o manifesto do aplicativo/serviço para não referenciar mais o tipo de nó.
    - Implante a alteração.

    Em seguida, valide isso:
    - Todos os serviços modificados acima não estão mais em execução no nó que pertence ao tipo de nó.
    - Todos os serviços estão íntegros.

3. Desmarcar o tipo de nó como não primário (ignorar para tipos de nó não primários)

    - Localize o modelo de Azure Resource Manager usado para implantação.
    - Localize a seção relacionada ao tipo de nó na seção Service Fabric.
    - Altere a propriedade IsPrimary para false. * * Não remova a seção relacionada ao tipo de nó nesta tarefa.
    - Implante o modelo de Azure Resource Manager modificado. * * Dependendo da configuração do cluster, essa etapa pode demorar um pouco.
    
    Em seguida, valide isso:
    - Service Fabric seção no portal indica que o cluster está pronto.
    - O cluster está íntegro.
    - Nenhum dos nós pertencentes ao tipo de nó são marcados como nó semente.

4. Desabilite cada nó no tipo de nó.

    Conecte-se ao cluster usando o PowerShell e, em seguida, execute a etapa a seguir.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - Para durabilidade de bronze, aguarde até que todos os nós cheguem ao estado desabilitado
    - Para durabilidade prata e ouro, alguns nós entrarão em desabilitado e o restante estará no estado desabilitando. Verifique a guia detalhes dos nós no estado desabilitando, se todos estiverem presos na garantia de quorum para partições de serviço de infraestrutura, será seguro continuar.

5. Parar dados para o tipo de nó.

    Conecte-se ao cluster usando o PowerShell e, em seguida, execute a etapa a seguir.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Aguarde até que todos os nós do tipo de nó sejam marcados como inativos.

6. Desalocar nós no conjunto de dimensionamento de máquinas virtuais original
    
    Faça logon na assinatura do Azure em que o conjunto de dimensionamento foi implantado e remova o conjunto de dimensionamento de máquinas virtuais. 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. Remova os dados do tipo de nó.

    Conecte-se ao cluster usando o PowerShell e, em seguida, execute a etapa a seguir.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Aguarde até que todos os nós sejam removidos do cluster. Os nós não devem ser exibidos em SFX.

8. Remova o tipo de nó da seção Service Fabric.

    - Localize o modelo de Azure Resource Manager usado para implantação.
    - Localize a seção relacionada ao tipo de nó na seção Service Fabric.
    - Remova a seção correspondente ao tipo de nó.
    - Somente para clusters de durabilidade prata e mais alta, atualize o recurso de cluster no modelo e configure as políticas de integridade para ignorar a malha:/integridade do aplicativo do sistema adicionando `applicationDeltaHealthPolicies` sob recurso `properties` de cluster, conforme indicado abaixo. A política abaixo deve ignorar os erros existentes, mas não permitir novos erros de integridade. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Implante o modelo de Azure Resource Manager modificado. * * Esta etapa levará um tempo, geralmente até duas horas. Essa atualização irá alterar as configurações para o InfrastructureService, portanto, é necessária uma reinicialização de nó. Nesse caso, `forceRestart` é ignorado. 
    O parâmetro `upgradeReplicaSetCheckTimeout` especifica o tempo máximo que Service Fabric aguarda até que uma partição esteja em um estado seguro, se ainda não estiver em um estado seguro. Depois que as verificações de segurança forem aprovadas para todas as partições em um nó, Service Fabric continuará com a atualização nesse nó.
    O valor do parâmetro `upgradeTimeout` pode ser reduzido para 6 horas, mas para a segurança máxima 12 horas deve ser usada.

    Em seguida, valide isso:
    - Service Fabric recurso no portal mostra pronto.

9. Remova todas as referências aos recursos relacionados ao tipo de nó do modelo ARM.

    - Localize o modelo de Azure Resource Manager usado para implantação.
    - Remova o conjunto de dimensionamento de máquinas virtuais e outros recursos relacionados ao tipo de nó do modelo.
    - Implante as alterações.

    Em seguida:
    - Aguarde a conclusão da implantação.
    
10. Remova os recursos relacionados ao tipo de nó que não estão mais em uso. Load Balancer de exemplo e IP público. 

    - Para remover esses recursos, você pode usar o mesmo comando do PowerShell, conforme usado na etapa 6, especificando o tipo de recurso e a versão da API específicos. 

> [!Note]
> Essa etapa é opcional se o mesmo Load Balancer e o IP é reutilizado entre os tipos de nó.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o cluster [características de durabilidade](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster).
- Saiba mais sobre [tipos de nós e Conjuntos de Dimensionamento de Máquinas Virtuais](service-fabric-cluster-nodetypes.md).
- Saiba mais sobre [Dimensionamento do cluster do Service Fabric](service-fabric-cluster-scaling.md).
