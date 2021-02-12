---
title: Dimensionamento programático do Azure Service Fabric
description: Dimensionar um cluster do Azure Service Fabric para expandi-lo ou reduzi-lo por meio de programação de acordo com gatilhos personalizados
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f39bd874c1f5a1be42ca1c88e6ea2fe8df22f87
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648201"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Dimensionar um cluster do Service Fabric por meio de programação 

Os clusters do Service Fabric em execução no Azure são criados sobre conjuntos de dimensionamento de máquinas virtuais.  O [Dimensionamento do cluster](./service-fabric-cluster-scale-in-out.md) descreve como os clusters do Service Fabric podem ser dimensionados manualmente ou com regras de dimensionamento automático. Este artigo descreve como gerenciar credenciais e reduzir horizontalmente e escalar horizontalmente um cluster usando o SDK de computação fluente do Azure, que é um cenário mais avançado. Para obter uma visão geral, leia [métodos programáticos de coordenação de operações de dimensionamento do Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Gerenciar credenciais
Um desafio de se escrever um serviço para manipular o dimensionamento é que o serviço deve ser capaz de acessar recursos do conjunto de dimensionamento de máquinas virtuais sem um logon interativo. O acesso ao cluster do Service Fabric é fácil se o serviço de dimensionamento está modificando seu próprio aplicativo do Service Fabric, mas as credenciais são exigidas para acessar o conjunto de dimensionamento. Para entrar, você pode usar uma [entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli) criada com o [CLI do Azure](https://github.com/azure/azure-cli).

Uma entidade de serviço pode ser criada com as seguintes etapas:

1. Entre no CLI do Azure ( `az login` ) como um usuário com acesso ao conjunto de dimensionamento de máquinas virtuais
2. Crie a entidade de serviço com `az ad sp create-for-rbac`
    1. Anote o appId (chamado de ‘ID do cliente’ em outros lugares), o nome, a senha e o locatário para uso posterior.
    2. Você também precisará da sua ID de assinatura, que pode ser exibida com `az account list`

A biblioteca de computação fluente pode entrar usando essas credenciais da seguinte maneira (Observe que os principais tipos do Azure fluentes como `IAzure` estão no pacote [Microsoft. Azure. Management. Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) ):

```csharp
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

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Como alternativa, o tamanho do conjunto de dimensionamento de máquinas virtuais também pode ser gerenciado com os cmdlets do PowerShell. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) pode recuperar o objeto do conjunto de dimensionamento de máquinas virtuais. A capacidade atual está disponível por meio da propriedade `.sku.capacity`. Depois de alterar a capacidade para o valor desejado, o conjunto de dimensionamento de máquinas virtuais no Azure pode ser atualizado com o [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) comando.

Assim como na adição de um nó manual, a adição de uma instância de conjunto de dimensionamento deve ser suficiente para iniciar um novo nó do Service Fabric, pois o modelo do conjunto de dimensionamento inclui extensões para unir novas instâncias de cluster do Service Fabric automaticamente. 

## <a name="scaling-in"></a>Redução horizontal

O dimensionamento no é semelhante à expansão. As alterações reais do conjunto de dimensionamento de máquinas virtuais são praticamente as mesmas. Mas, como discutido anteriormente, o Service Fabric apenas limpa automaticamente nós removidos com uma durabilidade Ouro ou Prata. Assim, no caso de durabilidade Bronze, é necessário interagir com o cluster do Service Fabric para desligar o nó a ser removido e remover seu estado.

A preparação do nó para desligamento envolve localizar o nó a ser removido (a instância do conjunto de dimensionamento de máquinas virtuais adicionada mais recentemente) e desativá-lo. As instâncias do conjunto de dimensionamento de máquinas virtuais são numeradas na ordem em que são adicionadas, portanto, podem ser localizados nós mais recentes, comparando o sufixo numérico nos nomes dos nós (que correspondem aos nomes da instância do conjunto de dimensionamento de máquinas virtuais). 

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

Depois que o nó a ser removido é encontrado, ele podem ser desativado e removido usando a mesma instância `FabricClient` e a instância `IAzure` anterior.

```csharp
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

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Próximas etapas

Para começar a implementar sua própria lógica de dimensionamento automático, familiarize-se com as APIs úteis e os conceitos abaixo:

- [Dimensionar manualmente ou com regras de dimensionamento automático](./service-fabric-cluster-scale-in-out.md)
- [Bibliotecas de gerenciamento do Azure para .net](https://github.com/Azure/azure-libraries-for-net) (útil para interagir com os conjuntos de dimensionamento de máquinas virtuais subjacentes de um Cluster Service Fabric)
- [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient) (útil para interagir com um cluster do Service Fabric e seus nós)
