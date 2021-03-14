---
title: Planejamento de capacidade e dimensionamento do Azure Service Fabric
description: Melhores práticas para planejamento e dimensionamento de clusters do Service Fabric e aplicativos.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: 32a9c26bb9e89cf4057cc753b02ad3c006d0bae6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595071"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planejamento de capacidade e dimensionamento do Azure Service Fabric

Antes de criar qualquer cluster de Service Fabric do Azure ou dimensionar recursos de computação que hospedam o cluster, é importante planejar a capacidade. Para obter mais informações sobre o planejamento de capacidade, confira [Planejamento de capacidade do cluster do Service Fabric](./service-fabric-cluster-capacity.md). Para obter mais diretrizes de práticas recomendadas para a escalabilidade do cluster, consulte [Service Fabric considerações sobre escalabilidade](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Além de considerar o tipo de nó e as características do cluster, você deve esperar que as operações de dimensionamento demorem mais de uma hora para serem concluídas em um ambiente de produção. Essa consideração é verdadeira independentemente do número de VMs que você está adicionando.

## <a name="autoscaling"></a>Dimensionamento automático
Você deve executar operações de dimensionamento por meio de modelos de Azure Resource Manager, pois é a melhor prática tratar [as configurações de recursos como código](./service-fabric-best-practices-infrastructure-as-code.md). 

Usar o dimensionamento automático por meio de conjuntos de dimensionamento de máquinas virtuais fará com que o modelo com versão do Resource Manager defina incorretamente as contagens de instâncias para conjuntos de dimensionamento de máquinas virtuais. A definição imprecisa aumenta o risco de implantações futuras causarem operações de dimensionamento indesejadas. Em geral, você deve usar o dimensionamento automático se:

* Implantar modelos do Resource Manager com capacidade apropriada declarada não dá suporte a seu caso de uso.
     
   Além do dimensionamento manual, você pode configurar um [pipeline de integração e entrega contínua no Azure DevOps Services usando projetos de implantação do grupo de recursos do Azure](../azure-resource-manager/templates/add-template-to-azure-pipelines.md). Esse pipeline é geralmente disparado por um aplicativo lógico que usa métricas de desempenho de máquina virtual consultada a partir da [API REST do Azure monitor](../azure-monitor/essentials/rest-api-walkthrough.md). O pipeline é efetivamente dimensionado de forma eficiente com base em quaisquer métricas que você desejar, ao mesmo tempo em que otimiza os modelos do Resource Manager.
* Você precisa dimensionar horizontalmente apenas um nó do conjunto de dimensionamento de máquinas virtuais por vez.
   
   Para escalar horizontalmente por três ou mais nós de uma vez, você deve [escalar horizontalmente um cluster Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais](virtual-machine-scale-set-scale-node-type-scale-out.md). É mais seguro reduzir e escalar horizontalmente os conjuntos de dimensionamento de máquinas virtuais, um nó por vez.
* Você tem confiabilidade Silver ou superior para seu cluster de Service Fabric e durabilidade prateada ou superior em qualquer escala em que você configurar regras de dimensionamento automático.
  
   A capacidade mínima para regras de dimensionamento automático deve ser igual ou maior que cinco instâncias de máquina virtual. Ele também deve ser igual ou maior que o mínimo da camada de confiabilidade para o tipo de nó primário.

> [!NOTE]
> O Service Fabric com estado Service Fabric:/System/InfastructureService/<NODE_TYPE_NAME> é executado em cada tipo de nó que tem durabilidade prateada ou mais alta. É o único serviço do sistema com suporte para ser executado no Azure em qualquer um dos seus tipos de nó de clusters.

> [!IMPORTANT]
> Service Fabric o dimensionamento automático dá suporte `Default` e `NewestVM` configurações de [dimensionamento](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)de conjunto de dimensionamento de máquinas virtuais.

## <a name="vertical-scaling-considerations"></a>Considerações de dimensionamento vertical

[Dimensionamento vertical](./virtual-machine-scale-set-scale-node-type-scale-out.md) um tipo de nó no Azure Service Fabric requer várias etapas e considerações. Por exemplo: 

* O cluster deve estar íntegro antes do dimensionamento. Caso contrário, você desestabilizará o cluster.
* O nível de durabilidade prateada ou maior é necessário para todos os Service Fabric tipos de nó de cluster que hospedam serviços com estado.

> [!NOTE]
> O tipo de nó primário que hospeda os serviços de sistema Service Fabric com estado deve ser um nível de durabilidade Silver ou superior. Depois de habilitar a durabilidade prateada, as operações de cluster, como atualizações, adição ou remoção de nós e assim por diante, serão mais lentas porque o sistema otimiza a segurança de dados em uma velocidade de operações.

O dimensionamento vertical de um conjunto de dimensionamento de máquinas virtuais simplesmente alterando seu SKU de recursos é uma operação destrutiva, pois ele recria a imagem de seus hosts, removendo todos os Estados persistentes localmente. Em vez disso, você desejará dimensionar horizontalmente o cluster adicionando um novo conjunto de dimensionamento com o SKU desejado e, em seguida, migrar seus serviços para o novo conjunto de dimensionamento para concluir uma operação de dimensionamento vertical segura.

O cluster usa [as propriedades do nó Service Fabric e as restrições de posicionamento](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) para decidir onde hospedar os serviços do aplicativo. Ao dimensionar verticalmente um tipo de nó primário, você implantará um segundo tipo de nó primário e, em seguida, definirá ( `"isPrimary": false` ) no tipo de nó primário original e continuará a desabilitar seus nós e removerá seu conjunto de dimensionamento e seus recursos relacionados. Para obter detalhes, consulte [escalar verticalmente um tipo de nó primário de cluster Service Fabric](service-fabric-scale-up-primary-node-type.md).

> [!NOTE]
> Sempre valide as operações em ambientes de teste antes de tentar as alterações no ambiente de produção. Por padrão, Service Fabric serviços do sistema de cluster têm uma restrição de posicionamento apenas para o tipo de nó primário de destino.

Com as propriedades de nó e as restrições de posicionamento declaradas, siga estas etapas uma instância de VM por vez. Isso permite que os serviços do sistema (e seus serviços com estado) sejam desligados normalmente na instância de VM que você está removendo à medida que novas réplicas são criadas em outro lugar.

1. No PowerShell, execute `Disable-ServiceFabricNode` com intenção `RemoveNode` para desabilitar o nó que você pretende remover. Remova o tipo de nó que tem o número mais alto. Por exemplo, se você tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Execute `Get-ServiceFabricNode` para garantir que o nó tenha feito a transição para desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Isso pode levar algumas horas para cada nó. Não continue até que o nó tenha feito a transição para desabilitado.
3. Diminua o número de VMs por um nesse tipo de nó. A instância da VM mais alta agora será removida.
4. Repita as etapas 1 a 3, conforme necessário, mas nunca dimensione o número de instâncias nos tipos de nó primários menos do que a camada de confiabilidade garante. Confira [Planejamento de capacidade do cluster do Service Fabric](./service-fabric-cluster-capacity.md) para obter uma lista de instâncias recomendadas.
5. Depois que todas as VMs estiverem ausentes (representadas como "inativas"), o Fabric:/System/InfrastructureService/[nome do nó] mostrará um estado de erro. Em seguida, você pode atualizar o recurso de cluster para remover o tipo de nó. Você pode usar a implantação de modelo ARM ou editar o recurso de cluster por meio do [Azure Resource Manager](https://resources.azure.com). Isso iniciará uma atualização de cluster, o que removerá o serviço Fabric:/System/InfrastructureService/[nó Type] que está em estado de erro.
 6. Depois disso, você pode, opcionalmente, excluir o VMScaleSet, você ainda verá os nós como "inativos" do modo de exibição de Service Fabric Explorer no entanto. A última etapa seria limpá-los com o `Remove-ServiceFabricNodeState` comando.

## <a name="horizontal-scaling"></a>Dimensionamento horizontal

Você pode fazer o dimensionamento horizontal [manualmente](./service-fabric-cluster-scale-in-out.md) ou [programaticamente](./service-fabric-cluster-programmatic-scaling.md).

> [!NOTE]
> Se você estiver dimensionando um tipo de nó que tem durabilidade prata ou ouro, o dimensionamento será lento.

### <a name="scaling-out"></a>Expansão

Escale horizontalmente um cluster de Service Fabric aumentando a contagem de instâncias para um determinado conjunto de dimensionamento de máquinas virtuais. Você pode escalar horizontalmente programaticamente usando `AzureClient` e a ID do conjunto de dimensionamento desejado para aumentar a capacidade.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Para expandir manualmente, atualize a capacidade na propriedade SKU do recurso do conjunto de [dimensionamento de máquinas virtuais](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desejado.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Redução horizontal

O dimensionamento no requer mais consideração do que escalar horizontalmente. Por exemplo:

* Service Fabric serviços do sistema são executados no tipo de nó primário no cluster. Nunca desligue ou dimensione o número de instâncias para esse tipo de nó para que você tenha menos instâncias do que a garantia da camada de confiabilidade. 
* Para um serviço com estado, você precisa de um determinado número de nós que sempre estão ativos para manter a disponibilidade e preservar o estado do serviço. No mínimo, você precisa de um número de nós igual à contagem de conjuntos de réplicas de destino da partição ou do serviço.

Para reduzir manualmente, siga estas etapas:

1. No PowerShell, execute `Disable-ServiceFabricNode` com intenção `RemoveNode` para desabilitar o nó que você pretende remover. Remova o tipo de nó que tem o número mais alto. Por exemplo, se você tiver um cluster de seis nós, remova a instância de máquina virtual "MyNodeType_5".
2. Execute `Get-ServiceFabricNode` para garantir que o nó tenha feito a transição para desabilitado. Caso contrário, aguarde até que o nó seja desabilitado. Isso pode levar algumas horas para cada nó. Não continue até que o nó tenha feito a transição para desabilitado.
3. Diminua o número de VMs por um nesse tipo de nó. A instância da VM mais alta agora será removida.
4. Repita as etapas 1 a 3, conforme necessário, até que você provisione a capacidade desejada. Não dimensione o número de instâncias nos tipos de nó primários para menos do que a camada de confiabilidade garante. Confira [Planejamento de capacidade do cluster do Service Fabric](./service-fabric-cluster-capacity.md) para obter uma lista de instâncias recomendadas.

Para dimensionar manualmente, atualize a capacidade na propriedade SKU do recurso de conjunto de [dimensionamento de máquinas virtuais](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desejado.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Você deve preparar o nó para que o desligamento seja reduzido de forma programática. Localize o nó a ser removido (o nó de instância mais alta). Por exemplo: 

```csharp
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

Desative e remova o nó usando a mesma `FabricClient` instância ( `client` nesse caso) e a instância de nó ( `instanceIdString` nesse caso) que você usou no código anterior:

```csharp
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
> Ao dimensionar em um cluster, você verá a instância do nó/VM removida exibida em um estado não íntegro em Service Fabric Explorer. Para obter uma explicação desse comportamento, consulte [comportamentos que podem ser observados em Service Fabric Explorer](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer). Você pode:
> * Chame o [comando Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) com o nome do nó apropriado.
> * Implante o [aplicativo auxiliar AutoScale Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) no cluster. Esse aplicativo garante que os nós reduzidos sejam apagados de Service Fabric Explorer.

## <a name="reliability-levels"></a>Níveis de confiabilidade

O [nível de confiabilidade](./service-fabric-cluster-capacity.md) é uma propriedade do recurso de Cluster Service Fabric. Ele não pode ser configurado de modo diferente para tipos de nó individuais. Ele controla o fator de replicação dos serviços do sistema para o cluster e é uma configuração no nível do recurso de cluster. 

O nível de confiabilidade determinará o número mínimo de nós que o tipo de nó primário deve ter. A camada de confiabilidade pode ter os valores a seguir:

* Platina: executa os serviços do sistema com uma contagem de conjuntos de réplicas de destino de sete e nove nós de semente.
* Ouro: executa os serviços do sistema com uma contagem de conjuntos de réplicas de destino de sete e sete nós de semente.
* Prata: executa os serviços do sistema com uma contagem de conjuntos de réplicas de destino de cinco e cinco nós de semente.
* Bronze: executa os serviços do sistema com uma contagem de conjunto de réplicas de destino de três e três nós de semente.

O nível mínimo recomendado de confiabilidade é Prata.

O nível de confiabilidade é definido na seção de propriedades do [recurso Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/2018-02-01/clusters), semelhante a isto:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Níveis de durabilidade

> [!WARNING]
> Tipos de nó executados com durabilidade Bronze não têm _nenhum privilégio_. Trabalhos de infraestrutura que afetam suas cargas de trabalho sem estado não serão interrompidos ou atrasados, o que pode afetar suas cargas de trabalho. 
>
> Use a durabilidade Bronze somente para tipos de nós que executam cargas de trabalho sem estado. Para cargas de trabalho de produção, execute prata ou superior para garantir a consistência do estado. Escolha a confiabilidade correta com base nas diretrizes na [documentação de planejamento de capacidade](./service-fabric-cluster-capacity.md).

O nível de durabilidade deve ser definido em dois recursos. Um é o perfil de extensão do [recurso do conjunto de dimensionamento de máquinas virtuais](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

O outro recurso está sob `nodeTypes` o [recurso Microsoft. perfabric/clusters](/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Service Fabric a criação de cluster para o Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Criar um cluster em VMs ou computadores que executam [o Linux: criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md).
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
