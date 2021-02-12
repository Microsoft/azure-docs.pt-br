---
title: Dimensionar ou reduzir horizontalmente um Service Fabric cluster
description: Dimensione um cluster de Service Fabric de entrada ou saída para corresponder à demanda definindo regras de dimensionamento automático para cada tipo de nó/conjunto de dimensionamento de máquinas virtuais. Adicionar ou remover nós de um cluster do Service Fabric
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409936"
---
# <a name="scale-a-cluster-in-or-out"></a>Reduzir ou escalar um cluster horizontalmente

> [!WARNING]
> Leia esta seção antes de dimensionar

O dimensionamento de recursos de computação para originar a carga de trabalho de seu aplicativo requer planejamento intencional, quase sempre levará mais de uma hora para ser concluído em um ambiente de produção e exige que você entenda sua carga de trabalho e contexto de negócios; Na verdade, se você nunca fez essa atividade antes, é recomendável começar lendo e compreendendo [considerações sobre o planejamento de capacidade de cluster do Service Fabric](service-fabric-cluster-capacity.md), antes de continuar com o restante deste documento. Esta recomendação é para evitar problemas não intencionais do LiveSite, e também é recomendado testar com sucesso as operações que você decidir executar em um ambiente que não seja de produção. A qualquer momento você pode [relatar problemas de produção ou solicitar suporte pago para o Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Para engenheiros alocados para executar essas operações que possuam contexto apropriado, este artigo descreverá as operações de dimensionamento, mas você deve decidir e entender quais operações são apropriadas para seu caso de uso; como os recursos a serem dimensionados (CPU, Armazenamento, Memória), a direção a ser dimensionada (vertical ou horizontalmente) e as operações a serem executadas (implantação de modelo de recurso, portal, PowerShell / CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Reduzir horizontalmente ou escalar horizontalmente um cluster do Service Fabric usando regra de dimensionamento automático ou manualmente
Os conjuntos de escala de Máquina Virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric está configurado como um conjunto de dimensionamento de máquinas virtuais separado. Cada tipo de nó pode ser escalado ou reduzido horizontalmente de forma independente, ter conjuntos diferentes de portas abertas e métricas de capacidade diferentes. Leia mais sobre isso no documento [tipos de nó Service Fabric](service-fabric-cluster-nodetypes.md) . Como os tipos de nó Service Fabric no cluster são compostos de conjuntos de dimensionamento de máquinas virtuais no back-end, você precisa configurar regras de dimensionamento automático para cada tipo de nó/conjunto de dimensionamento de máquinas virtuais.

> [!NOTE]
> Sua assinatura precisa ter uma quantidade suficiente de núcleos para adicionar as novas VMs que compõe esse cluster. Atualmente, não há nenhuma validação de modelo. Logo, você receberá uma falha de tempo de implantação se qualquer um dos limites de cota for atingido. Além disso, um único tipo de nó não pode simplesmente exceder 100 nós por VMSS. Pode ser necessário adicionar VMSS para atingir a escala desejada, e o escalonamento automático não pode adicionar automaticamente VMSS. Adicionar in-loco do VMSS a um cluster ativo é uma tarefa desafiadora e, geralmente, isso resulta em usuários provisionando novos clusters com os tipos de nó apropriados provisionados no momento da criação; [planeje a capacidade do cluster](./service-fabric-cluster-capacity.md) de acordo. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Escolher o tipo de nó/conjunto de dimensionamento de máquinas virtuais a ser dimensionado
No momento, não é possível especificar as regras de dimensionamento automático para conjuntos de dimensionamento de máquinas virtuais usando o portal, portanto para criar um Cluster do Service Fabric. Portanto, vamos utilizar o Microsoft Azure PowerShell (1.0+) para listar os tipos de nó e, em seguida, adicionar regras de dimensionamento automático a eles.

Para obter a lista de conjuntos de dimensionamento de máquinas virtuais que compõem o cluster, execute os cmdlets a seguir:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Definir regras de dimensionamento automático para o tipo de nó/conjunto de dimensionamento de máquinas virtuais
Se o cluster tiver vários tipos de nó, repita isso para cada tipo de nó/conjuntos de dimensionamento de máquinas virtuais que você deseja dimensionar (para dentro ou para fora). Leve em conta o número de nós que você precisa ter antes de configurar o dimensionamento automático. O número mínimo de nós necessários para o tipo de nó primário é controlado pelo nível de confiabilidade escolhido. Leia mais sobre os [níveis de confiabilidade](service-fabric-cluster-capacity.md).

> [!NOTE]
> O dimensionamento no tipo de nó primário para menor que o número mínimo fará com que o cluster fique instável ou até mesmo o desative. Isso poderá resultar em perda de dados de seus aplicativos e dos serviços do sistema.
> 
> 

Atualmente, o recurso de escala automática não é controlado pelas cargas que os aplicativos podem relatar ao Service Fabric. Portanto, nesse momento, o dimensionamento automático obtido é meramente controlado pelos contadores de desempenho que são emitidos por cada uma das instâncias do conjunto de dimensionamento de máquinas virtuais.  

Siga estas instruções [para configurar o dimensionamento automático para cada conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> Em uma escala no cenário, a menos que o tipo de nó tenha um [nível de durabilidade][durability] ouro ou prata, você precisa chamar o [cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome do nó apropriado. Para a durabilidade de bronze, não é recomendável dimensionar em mais de um nó por vez.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Adicionar VMs manualmente a um tipo de nó/conjunto de dimensionamento de máquinas virtuais

Ao aumentar, você adicionar mais instâncias de máquina virtual ao conjunto de dimensionamento. Essas instâncias tornam-se os nós que o Service Fabric usa. O Service Fabric sabe quando o conjunto de dimensionamento tem mais instâncias adicionadas (aumentando) e reage automaticamente. 

> [!NOTE]
> A adição de VMs leva tempo, portanto, não espere que as adições sejam instantâneas. Planeje adicionar capacidade com antecedência, permitindo mais de 10 minutos antes que a capacidade da VM esteja disponível para que as réplicas/instâncias de serviço sejam colocadas.
> 

### <a name="add-vms-using-a-template"></a>Adicionar VMs usando um modelo
Siga os exemplos/instruções na [Galeria de modelos de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada tipo de nó. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Adicionar VMs usando os comandos do PowerShell ou da CLI
O código a seguir obtém um conjunto de dimensionamento por nome e aumenta a **capacidade** do conjunto de dimensionamento em 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Esse código define a capacidade para 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Remover manualmente as VMs de um tipo de nó/conjunto de dimensionamento de máquinas virtuais
Ao dimensionar em um tipo de nó, você remove as instâncias de VM do conjunto de dimensionamento. Se o tipo de nó for nível de durabilidade bronze, Service Fabric não saberá o que aconteceu e relatará que um nó desapareceu. Service Fabric então relata um estado não íntegro para o cluster. Para evitar esse estado inadequado, você deve remover explicitamente o nó do cluster e remover o estado do nó.

Os serviços do sistema do Service Fabric são executados no tipo de nó primário no cluster. Ao dimensionar no tipo de nó primário, nunca reduza o número de instâncias para menos do que a [camada de confiabilidade](service-fabric-cluster-capacity.md) garante. 
 
Para um serviço com estado, você precisa de um determinado número de nós que devem estar sempre ativos para manter a disponibilidade e preservar o estado do serviço. No mínimo, é necessário que o número de nós seja igual à contagem de conjunto de réplicas de destino do serviço/partição.

### <a name="remove-the-service-fabric-node"></a>Remover o nó do Service Fabric

As etapas para remover manualmente o estado do nó se aplicam somente a tipos de nós com uma camada de durabilidade *bronze* .  Para a camada de durabilidade *prata* e *ouro* , essas etapas são feitas automaticamente pela plataforma. Para obter mais informações sobre durabilidade, consulte [Planejamento de capacidade do cluster do Service Fabric][durability].

>[!NOTE]
> Mantenha uma contagem mínima de cinco nós para qualquer conjunto de dimensionamento de máquinas virtuais que tenha o nível de durabilidade Ouro ou Prata habilitado. O cluster entrará em estado de erro se você reduzir abaixo desse limite e precisará limpar manualmente os nós removidos.

Para manter os nós do cluster distribuídos uniformemente entre domínios de falha e atualização e, portanto, habilitar sua utilização uniforme, o nó criado mais recentemente deve ser removido primeiro. Em outras palavras, os nós devem ser removidos na ordem inversa à de sua criação. O nó criado mais recentemente é aquele com o maior valor da propriedade `virtual machine scale set InstanceId`. Os exemplos de código a seguir retornam o nó criado mais recentemente.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

O cluster do Service Fabric precisa saber que este nó será removido. Você precisa executar três etapas:

1. Desabilite o nó para que ele não seja mais uma replicação de dados.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Pare o nó de modo que o runtime do Service Fabric seja desligado corretamente e o aplicativo obtenha uma solicitação de encerramento.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Remova o nó do cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Depois que essas três etapas tiverem sido aplicadas ao nó, ele poderá ser removido do conjunto de dimensionamento. Se você estiver usando qualquer camada de durabilidade além [bronze][durability], estas etapas serão executadas para você quando a instância do conjunto de dimensionamento for removida.

O bloco de código a seguir obtém o último nó criado, desabilita, para e remove o nó do cluster.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

No código **sfctl** abaixo, o comando a seguir é usado para obter o valor **node-name** do último nó criado: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Use as seguintes consultas **sfctl** para verificar o status de cada etapa
>
> **Verificar status da desativação**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Verificar status de parada**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Reduzir horizontalmente o conjunto de dimensionamento

Agora que o nó do Service Fabric foi removido do cluster, o conjunto de dimensionamento de máquinas virtuais pode ser reduzido horizontalmente. No exemplo a seguir, a capacidade do conjunto de dimensionamento é reduzida em 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Esse código define a capacidade para 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos que podem ser observados no Service Fabric Explorer
Quando você escala horizontalmente um cluster, a Service Fabric Explorer refletirá o número de nós (instâncias do conjunto de dimensionamento de máquinas virtuais) que fazem parte do cluster.  No entanto, ao dimensionar um cluster no, você verá a instância do nó/VM removida exibida em um estado não íntegro, a menos que chame [Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome do nó apropriado.   

Veja a seguir a explicação para esse comportamento.

Os nós listados no Service Fabric Explorer são um reflexo do que os serviços do sistema do Service Fabric (especificamente, FM) conhecem sobre o número de nós que o cluster tinha/tem. Quando você dimensiona o conjunto de dimensionamento de máquinas virtuais no, a VM foi excluída, mas o serviço de sistema de FM ainda pensa que o nó (que foi mapeado para a VM que foi excluída) voltará. Portanto, o Service Fabric Explorer continua exibindo o nó (embora o estado de integridade possa ser erro ou desconhecido).

Para certificar-se de que um nó será removido quando uma VM for removida, você tem duas opções:

1. Escolha um nível de durabilidade de Gold ou Silver para os tipos de nó no seu cluster, o que fornece a integração de infraestrutura. Que, em seguida, removerá automaticamente os nós do nosso estado de FM (serviços do sistema) quando você dimensionar horizontalmente.
Confira [os detalhes sobre os níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

> [!NOTE]
> Mantenha uma contagem mínima de cinco nós para qualquer conjunto de dimensionamento de máquinas virtuais que tenha o nível de durabilidade Ouro ou Prata habilitado. O cluster entrará em estado de erro se você reduzir abaixo desse limite e precisará limpar manualmente os nós removidos.

2. Depois que a instância de VM for dimensionada no, você precisará chamar o [cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate).

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
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
