---
title: "Dimensionamento programático do Azure Service Fabric | Microsoft Docs"
description: "Dimensionar um cluster do Azure Service Fabric para expandi-lo ou reduzi-lo por meio de programação de acordo com gatilhos personalizados"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: mikerou
ms.openlocfilehash: 46b0b62f92abbac57bc27bbcdd5821eafedf5519
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Dimensionar um cluster do Service Fabric por meio de programação 

Os conceitos básicos do dimensionamento de um cluster do Azure Service Fabric são abordados na documentação sobre [dimensionamento de cluster](./service-fabric-cluster-scale-up-down.md). Esse artigo aborda como clusters do Service Fabric são criados em cima de conjuntos de dimensionamento de máquinas virtuais e podem ser dimensionados manualmente ou com regras de dimensionamento automático. Este documento examina métodos programáticos de coordenação das operações de dimensionamento do Azure para cenários mais avançados. 

## <a name="reasons-for-programmatic-scaling"></a>Razões para um dimensionamento programático
Em muitos cenários, o dimensionamento manual ou por meio de regras de dimensionamento automático é uma boas solução. Em outros cenários, no entanto, ele pode não ser adequado. As desvantagens desses métodos incluem:

- O dimensionamento manual requer que você faça logon e solicite as operações de dimensionamento explicitamente. Se as operações de dimensionamento são necessárias com frequência ou em momentos imprevisíveis, essa abordagem não pode ser uma boa solução.
- Quando as regras de dimensionamento automático removem uma instância de um conjunto de dimensionamento de máquinas virtuais, elas não removem o conhecimento do nó do cluster do Service Fabric associado, a menos que o tipo de nó tenha um nível de durabilidade de Prata ou Ouro. Como as regras de dimensionamento automático funcionam no nível do conjunto de dimensionamento (em vez do nível do Service Fabric), elas podem remover nós do Service Fabric sem desligá-lo normalmente. Essa remoção de nó sem maiores cuidados deixará um estado do nó do Service Fabric “fantasma” de rastro após as operações de redução horizontal. Uma pessoa (ou um serviço) precisaria limpar periodicamente o estado do nó removido no cluster do Service Fabric.
  - Observe que um tipo de nó com um nível de durabilidade Ouro ou Prata limpará os nós removidos automaticamente.  
- Embora haja [várias métricas](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) com suporte pelas regras de dimensionamento automático, o conjunto ainda é limitado. Se seu cenário exigir dimensionamento com base em alguma métrica não abordada no conjunto, as regras de dimensionamento automático podem não ser uma boa opção.

Com base nessas limitações, convém implementar mais modelos de dimensionamento personalizados. 

## <a name="scaling-apis"></a>Dimensionando APIs
Existem APIs do Azure que permitem aos aplicativos trabalhar com conjuntos de dimensionamento de máquinas virtuais e clusters do Service Fabric por meio de programação. Se as opções de dimensionamento automático existentes não funcionam em seu cenário, essas APIs possibilitam a implementação de lógica de dimensionamento personalizada. 

Uma abordagem para implementar essa funcionalidade de dimensionamento automático “caseira” é adicionar um novo serviço sem estado ao aplicativo do Service Fabric para gerenciar operações de dimensionamento. No método `RunAsync` do serviço, um conjunto de disparadores pode determinar se o dimensionamento é necessário (inclusive verificando parâmetros como o tamanho máximo do cluster e o dimensionamento de cooldowns).   

A API usada para interações de conjunto de dimensionamento de máquinas virtuais (tanto para verificar o número atual de instâncias da máquina virtual quanto para modificá-lo) é a [biblioteca de computação do Gerenciamento do Azure fluente](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A biblioteca de computação fluente fornece uma API fácil de usar para interagir com conjuntos de dimensionamento de máquinas virtuais.

Para interagir com o próprio cluster do Service Fabric, use [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

É claro que o código de dimensionamento não precisa ser executado como um serviço no cluster para ser dimensionado. Tanto o `IAzure` quanto o `FabricClient` podem se conectar aos recursos do Azure associados remotamente e, portanto, o serviço de dimensionamento pode facilmente ser um aplicativo de console ou serviço do Windows executado de fora do aplicativo do Service Fabric. 

## <a name="credential-management"></a>Gerenciamento de credenciais
Um desafio de se escrever um serviço para manipular o dimensionamento é que o serviço deve ser capaz de acessar recursos do conjunto de dimensionamento de máquinas virtuais sem um logon interativo. O acesso ao cluster do Service Fabric é fácil se o serviço de dimensionamento está modificando seu próprio aplicativo do Service Fabric, mas as credenciais são exigidas para acessar o conjunto de dimensionamento. Para fazer logon, você pode usar uma [entidade de serviço](https://github.com/Azure/azure-sdk-for-net/blob/Fluent/AUTH.md#creating-a-service-principal-in-azure) criada com a [CLI do Azure 2.0](https://github.com/azure/azure-cli).

Uma entidade de serviço pode ser criada com as seguintes etapas:

1. Faça logon na CLI do Azure (`az login`) como um usuário com acesso ao conjunto de dimensionamento de máquinas virtuais
2. Crie a entidade de serviço com `az ad sp create-for-rbac`
    1. Anote o appId (chamado de ‘ID do cliente’ em outros lugares), o nome, a senha e o locatário para uso posterior.
    2. Você também precisará da sua ID de assinatura, que pode ser exibida com `az account list`

A biblioteca de computação fluente pode fazer logon usando essas credenciais da seguinte maneira (observe que os tipos de núcleo fluente do Azure como `IAzure` estão no pacote [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/)):

```C#
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Depois da conexão, a contagem de instâncias do conjunto de dimensionamento pode ser consultada por meio de `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Expansão
Usando o SDK de computação do Azure fluente, instâncias podem ser adicionadas ao conjunto de dimensionamento de máquinas virtuais com apenas algumas chamadas-

```C#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Como alternativa, o tamanho do conjunto de dimensionamento de máquinas virtuais também pode ser gerenciado com os cmdlets do PowerShell. O [`Get-AzureRmVmss`](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmss) pode recuperar o objeto do conjunto de dimensionamento de máquinas virtuais. A capacidade atual será armazenada na propriedade `.sku.capacity`. Depois de alterar a capacidade para o valor desejado, o conjunto de dimensionamento de máquinas virtuais do Azure pode ser atualizado com o comando [ `Update-AzureRmVmss` ](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/update-azurermvmss).

Assim como na adição de um nó manual, a adição de uma instância de conjunto de dimensionamento deve ser suficiente para iniciar um novo nó do Service Fabric, pois o modelo do conjunto de dimensionamento inclui extensões para unir novas instâncias de cluster do Service Fabric automaticamente. 

## <a name="scaling-in"></a>Redução horizontal

A redução horizontal é semelhante à expansão. As alterações do conjunto de dimensionamento de máquinas virtuais são praticamente as mesmas. Mas, como discutido anteriormente, o Service Fabric apenas limpa automaticamente nós removidos com uma durabilidade Ouro ou Prata. Assim, no caso de durabilidade Bronze, é necessário interagir com o cluster do Service Fabric para desligar o nó a ser removido e remover seu estado.

A preparação do nó para desligamento envolve localizar o nó a ser removido (o nó adicionado mais recentemente) e desativá-lo. No caso de nós sem propagação, os nós mais recentes podem ser encontrados comparando `NodeInstanceId`. 

```C#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n => n.NodeInstanceId)
        .FirstOrDefault();
```

Lembre-se de que os nós de *propagação* nem sempre parecem seguir a convenção de que as IDs de instâncias maiores são removidas primeiro.

Depois que o nó a ser removido é encontrado, ele podem ser desativado e removido usando a mesma instância `FabricClient` e a instância `IAzure` anterior.

```C#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Com a expansão, os cmdlets do PowerShell para modificar a capacidade do conjunto de dimensionamento de máquinas virtuais também pode ser usada aqui se uma abordagem de script for preferível. Depois que a instância da máquina virtual é removida, o estado do nó do Service Fabric pode ser removido.

```C#
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="potential-drawbacks"></a>Possíveis desvantagens

Conforme demonstrado nos trechos de código anteriores, a criação do seu próprio serviço de dimensionamento fornece o mais alto grau de controle e personalização do comportamento de dimensionamento do aplicativo. Isso pode ser útil para cenários que exigem controle preciso sobre quando ou como um aplicativo pode ser expandido ou reduzido horizontalmente. No entanto, esse controle é fornecido ao custo de uma maior complexidade do código. O uso dessa abordagem significa que você precisa possuir código de dimensionamento, o que não é simples.

Como você deve abordar o dimensionamento do Service Fabric depende de seu cenário. Se o dimensionamento for incomum, a capacidade de adicionar ou remover nós manualmente provavelmente será suficiente. Para cenários mais complexos, as regras de dimensionamento automático e os SDKs que expõem a capacidade de dimensionamento programático oferecem alternativas poderosas.

## <a name="next-steps"></a>Próximas etapas

Para começar a implementar sua própria lógica de dimensionamento automático, familiarize-se com as APIs úteis e os conceitos abaixo:

- [Dimensionar manualmente ou com regras de dimensionamento automático](./service-fabric-cluster-scale-up-down.md)
- [Bibliotecas do Gerenciamento do Azure para .NET fluentes](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (útil para interagir com conjuntos de dimensionamento de máquinas virtuais subjacentes a um cluster do Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (útil para interagir com um cluster do Service Fabric e seus nós)
