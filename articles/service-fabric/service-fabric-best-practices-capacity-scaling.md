---
title: Planejamento de capacidade e dimensionamento para o Azure Service Fabric | Microsoft Docs
description: Melhores práticas para planejamento e dimensionamento de clusters do Service Fabric e aplicativos.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 8ba4763e8d4835911d33d21c0f5bb431851a649b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444708"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planejamento de capacidade e dimensionamento para o Azure Service Fabric

Antes de criar qualquer cluster do Service Fabric do Azure ou recursos que hospedam o cluster de computação de escala, é importante planejar a capacidade. Para obter mais informações sobre o planejamento de capacidade, confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Para obter mais orientações práticas recomendadas para escalabilidade de cluster, consulte [considerações sobre escalabilidade de Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Além de considerar as características de tipo e o cluster de nó, você deve esperar as operações de dimensionamento levar mais de uma hora para ser concluído para um ambiente de produção. Isso é verdadeiro independentemente do número de VMs que você está adicionando.

## <a name="autoscaling"></a>Dimensionamento automático
Você deve executar operações de dimensionamento por meio de modelos do Azure Resource Manager, pois é a prática recomendada para tratar [configurações de recursos como código]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

Usar o dimensionamento automático por meio de conjuntos de dimensionamento de máquina virtual fará com que seu modelo do Resource Manager com controle de versão erroneamente definir suas contagens de instância para conjuntos de dimensionamento de máquina virtual. Definição imprecisa aumenta o risco de que as futuras implantações fará com que as operações de dimensionamento não intencionais. Em geral, você deve usar o dimensionamento automático se:

* Implantar modelos do Resource Manager com capacidade apropriada declarada não dá suporte a seu caso de uso.
     
   Além de dimensionar manual, você pode configurar uma [contínua integração e entrega pipeline nos serviços de DevOps do Azure usando projetos de implantação de grupo de recursos do Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Este pipeline é normalmente disparado por um aplicativo lógico que usa as métricas de desempenho da máquina virtual consultadas a partir de [API de REST do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). O pipeline efetivamente é dimensionado automaticamente com base em qualquer métricas que deseja, enquanto otimizando para modelos do Resource Manager.
* Você precisa escalar horizontalmente a apenas um nó de conjunto de dimensionamento de máquina virtual por vez.
   
   Para escalar horizontalmente por três ou mais nós por vez, você deve [escalar horizontalmente um cluster do Service Fabric com a adição de um conjunto de dimensionamento de máquina virtual](virtual-machine-scale-set-scale-node-type-scale-out.md). É mais seguro reduzir horizontalmente e dimensionamento de máquinas virtuais de expansão horizontalmente, define um nó por vez.
* Você tem confiabilidade Silver ou superior para seu cluster do Service Fabric e a durabilidade Prata ou superior em qualquer escala onde você pode configurar regras de dimensionamento automático.
  
   A capacidade mínima para regras de dimensionamento automático deve ser igual ou maior que cinco instâncias de máquina virtual. Ele também deve ser igual ou maior que o mínimo de camada de confiabilidade para seu tipo de nó primário.

> [!NOTE]
> O Service Fabric com monitoração de estado do service fabric: / System/InfastructureService/< NODE_TYPE_NAME > é executado em cada tipo de nó que tem a durabilidade Prata ou maior. É o serviço de sistema somente há suporte para executar no Azure em qualquer um dos seus tipos de nós de clusters.

## <a name="vertical-scaling-considerations"></a>Considerações de dimensionamento vertical

[O dimensionamento vertical](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) um tipo de nó no Azure Service Fabric requer um número de etapas e considerações. Por exemplo:

* O cluster deve estar íntegro antes do dimensionamento. Caso contrário, você vai desestabilizar ainda mais o cluster.
* A durabilidade prata nível ou superior é necessária para todos os tipos de nó de cluster de Service Fabric que hospedam serviços com monitoração de estado.

> [!NOTE]
> O tipo de nó primário que hospeda os serviços com monitoração de estado do sistema do Service Fabric deve ser maior ou nível de durabilidade prata. Depois de habilitar a durabilidade Prata, as operações de cluster, como atualizações, adicionando ou removendo nós, e assim por diante será mais lento porque o sistema otimiza para segurança de dados sobre velocidade das operações.

Um conjunto de dimensionamento de máquinas virtuais de dimensionamento vertical é uma operação destrutiva. Em vez disso, escale horizontalmente seu cluster, adicionando um novo conjunto de dimensionamento com o SKU desejado. Em seguida, migre os serviços para sua SKU desejado para concluir uma operação de dimensionamento vertical segura. Alterar um recurso de conjunto de dimensionamento de máquina virtual SKU é uma operação destrutiva porque ele Refaz a imagem de seus hosts, que remove todos os localmente um estado persistente.

O cluster usa o Service Fabric [restrições de posicionamento e propriedades de nó](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) para decidir onde hospedar serviços do aplicativo. Quando estiver dimensionando verticalmente seu tipo de nó primário, declare os valores de propriedade idênticas para `"nodeTypeRef"`. Você pode encontrar esses valores na extensão do Service Fabric para conjuntos de dimensionamento de máquina virtual. 

O trecho de um modelo do Resource Manager a seguir mostra as propriedades que você declarar. Ele tem o mesmo valor para os conjuntos de dimensionamento provisionado recentemente que você está dimensionando para, e é suportado apenas como um serviço com estado temporário para o cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Não deixe o cluster executando com vários conjuntos de dimensionamento que usam o mesmo `nodeTypeRef` mais de valor da propriedade necessária para concluir uma operação bem-sucedida de colocação em escala vertical.
>
> Sempre valide a operações em ambientes de teste antes de tentar alterações no ambiente de produção. Por padrão, os serviços de sistema de cluster do Service Fabric têm uma restrição de posicionamento para apenas o tipo de nó primário de destino.

Com as propriedades de nó e as restrições de posicionamento declaradas, siga estas etapas uma instância de VM por vez. Isso permite que os serviços do sistema (e seus serviços com estado) sejam desligados normalmente na instância de VM que você está removendo conforme novas réplicas são criadas em outro lugar.

1. No PowerShell, execute `Disable-ServiceFabricNode` com a intenção `RemoveNode` para desabilitar o nó que você pretende remover. Remova o tipo de nó que tem o número mais alto. Por exemplo, se você tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Execute `Get-ServiceFabricNode` para garantir que o nó tenha feito a transição para desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Isso pode levar algumas horas para cada nó. Não continue até que o nó tenha feito a transição para desabilitado.
3. Diminua o número de VMs em um tipo de nó. A instância da VM mais alta agora será removida.
4. Repita as etapas 1 a 3 conforme necessário, mas nunca reduza verticalmente o número de instâncias no tipo de nó primário para uma quantidade menor do que a camada de confiabilidade garante. Confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.
5. Depois que todas as VMs desapareceram (representado como "Down") a malha: / System/InfrastructureService / [nome do nó] mostrará um estado de erro. Em seguida, você pode atualizar o recurso de cluster para remover o tipo de nó. Você pode usar a implantação de modelo do ARM ou editar o recurso de cluster por meio de [do Azure resource manager](https://resources.azure.com). Isso iniciará uma atualização de cluster que removerá a malha: / / InfrastructureService / [tipo de nó] serviço do sistema que está em estado de erro.
 6. Depois que você pode, opcionalmente, exclua a VMScaleSet, você ainda verá os nós como "Down" do Service Fabric Explorer exibir entanto. A última etapa seria para limpá-las com `Remove-ServiceFabricNodeState` comando.

### <a name="example-scenario"></a>Cenário de exemplo
Um cenário com suporte para quando executar uma operação de dimensionamento vertical é: você deseja migrar o cluster do Service Fabric e o aplicativo de um disco não gerenciado para discos gerenciados, sem tempo de inatividade do aplicativo. 

Você pode provisionar um novo conjunto de dimensionamento de máquina virtual com discos gerenciados e realizar uma atualização de aplicativo com restrições de posicionamento que se destinam a capacidade provisionada. Cluster do Service Fabric, em seguida, pode agendar sua carga de trabalho na capacidade de nó de cluster provisionado está distribuída por domínio de atualização sem tempo de inatividade do aplicativo. 

Pontos de extremidade do pool de back-end para o [SKU básico do Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) podem ser máquinas virtuais em um único conjunto de disponibilidade ou um conjunto de dimensionamento de máquina virtual. Isso significa que você não pode usar um balanceador de carga de SKU básico, se você mover o aplicativo de sistemas do Service Fabric entre conjuntos de dimensionamento, sem causar a inacessibilidade temporária de seu ponto de extremidade de gerenciamento de cluster do Service Fabric. Isso é verdadeiro mesmo que o cluster e seu aplicativo ainda estão em execução.

Os usuários normalmente provisionar um balanceador de carga de SKU Standard, ao executar uma troca de VIP (endereço) do IP virtual entre o balanceador de carga de SKU básico e recursos do balanceador de carga de SKU Standard. Essa técnica limita qualquer inacessibilidade futuras para aproximadamente 30 segundos, necessário para a troca de VIP.

## <a name="horizontal-scaling"></a>Dimensionamento em escala horizontal

Você pode fazer o dimensionamento horizontal, o [manualmente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) ou [programaticamente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se você estiver dimensionando de um tipo de nó que tem a durabilidade Prata ou ouro, o dimensionamento será lento.

### <a name="scaling-out"></a>Expansão

Escalar horizontalmente um cluster do Service Fabric, aumentando a contagem de instâncias para um conjunto de dimensionamento de máquina virtual específica. Você pode escalar horizontalmente por meio de programação usando `AzureClient` e a ID para o conjunto para aumentar a capacidade de dimensionamento desejada.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Para escalar horizontalmente manualmente, atualize a capacidade na propriedade SKU do desejado [conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) recursos.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Redução horizontal

Reduzir exige mais consideração do que expandir. Por exemplo:

* Serviços de sistema do Service Fabric é executado no tipo de nó primário em seu cluster. Nunca desligue nem reduza verticalmente o número de instâncias desse tipo de nó para que você tenha menos instâncias do que o que a camada de confiabilidade requer. 
* Para um serviço com monitoração de estado, você precisa de um determinado número de nós que estão sempre se para manter a disponibilidade e preservar o estado do seu serviço. No mínimo, você precisa de um número de nós igual à contagem de conjunto de réplica de destino da partição ou serviço.

Para reduzir manualmente, siga estas etapas:

1. No PowerShell, execute `Disable-ServiceFabricNode` com a intenção `RemoveNode` para desabilitar o nó que você pretende remover. Remova o tipo de nó que tem o número mais alto. Por exemplo, se você tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Execute `Get-ServiceFabricNode` para garantir que o nó tenha feito a transição para desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Isso pode levar algumas horas para cada nó. Não continue até que o nó tenha feito a transição para desabilitado.
3. Diminua o número de VMs em um tipo de nó. A instância da VM mais alta agora será removida.
4. Repita as etapas 1 a 3 conforme necessário até que você provisionar a capacidade que você deseja. Não reduza o número de instâncias nos tipos de nó primários para menos do que aquilo que a camada de confiabilidade requer. Confira [Planejamento de capacidade do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) para obter uma lista de instâncias recomendadas.

Para Dimensionar manualmente, atualize a capacidade na propriedade SKU do desejado [conjunto de dimensionamento de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) recursos.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Você deve preparar o nó para desligamento ser dimensionados de forma programática. Localize o nó a ser removido (o nó da instância mais alta). Por exemplo:

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Desativar e remover o nó usando o mesmo `FabricClient` instância (`client` nesse caso) e a instância de nó (`instanceIdString` nesse caso) que você usou no código anterior:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Quando você escala verticalmente um cluster, você verá a instância de nó/VM removida exibida em um estado não íntegro no Service Fabric Explorer. Para obter uma explicação desse comportamento, consulte [comportamentos que você pode observar no Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Você pode:
> * Chame o [comando Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) com o nome de nó apropriado.
> * Implantar o [aplicativo de auxiliar de dimensionamento automático do Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) em seu cluster. Esse aplicativo garante que os nós reduzida são removidos do Service Fabric Explorer.

## <a name="reliability-levels"></a>Níveis de confiabilidade

O [nível de confiabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) é uma propriedade de seu recurso de cluster do Service Fabric. Ele não pode ser configurado diferentemente para tipos de nós individuais. Ele controla o fator de replicação dos serviços do sistema para o cluster e é uma configuração no nível do recurso de cluster. 

O nível de confiabilidade determinará o número mínimo de nós que o tipo de nó primário deve ter. A camada de confiabilidade pode ter os valores a seguir:

* Platina: Executa os serviços do sistema com uma contagem de conjunto de réplica de destino de sete e nove nós de semente.
* ouro: Executa os serviços do sistema com uma contagem de conjunto de réplica de destino de sete e sete nós de semente.
* prata: Executa os serviços do sistema com uma contagem de conjunto de réplica de destino de cinco e cinco nós de semente.
* Bronze: Executa os serviços do sistema com uma contagem de conjunto de réplica de destino de três e três nós de semente.

O nível mínimo recomendado de confiabilidade é Prata.

O nível de confiabilidade é definido na seção de propriedades do [recurso Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), semelhante a isto:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Níveis de durabilidade

> [!WARNING]
> Tipos de nó executados com durabilidade Bronze não têm _nenhum privilégio_. Trabalhos de infraestrutura que afetam suas cargas de trabalho sem monitoração de estado não serão interrompidos ou atrasados, que podem afetar suas cargas de trabalho. 
>
> Use a durabilidade Bronze somente para tipos de nós que executam cargas de trabalho sem estado. Para cargas de trabalho de produção, execute Silver ou superiores para garantir a consistência de estado. Escolha a confiabilidade correta com base nas diretrizes na [documentação de planejamento de capacidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

O nível de durabilidade deve ser definido em dois recursos. Um é o perfil da extensão do [recurso de conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Outro recurso está sob `nodeTypes` no [servicefabric/clusters recursos](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

* Criar um cluster em VMs ou em computadores executando o Windows Server: [Criação de cluster do Service Fabric no Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Criar um cluster nas VMS ou computadores executando Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
