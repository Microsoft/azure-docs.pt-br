---
title: Simular falhas nos microsserviços do Azure | Microsoft Docs
description: Este artigo fala sobre as ações da Possibilidade de Teste encontradas na Malha de Serviço do Microsoft Azure.
services: service-fabric
documentationcenter: .net
author: motanv
manager: chackdan
editor: heeldin
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 37a794387f3a2f02124805705d380ad9f1fc1270
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544766"
---
# <a name="testability-actions"></a>Ações da Possibilidade de Teste
Para simular uma infraestrutura não confiável, o Service Fabric do Azure fornece a você, o desenvolvedor, maneiras de simular várias falhas e transições de estado reais. Elas são expostas como ações de possibilidade de teste. As ações são as APIs de nível baixo que causam uma injeção de falha específica, transição de estado ou validação. Ao combinar essas ações, você pode criar cenários de teste abrangentes para seus serviços.

O Service Fabric fornece alguns cenários comuns de teste compostos por essas ações. É altamente recomendável utilizar esses cenários internos, que são escolhidos cuidadosamente para testar transições de estado comuns e casos de falha. No entanto, as ações podem ser usadas para criar cenários de teste personalizados quando você desejar adicionar cobertura para cenários que ainda não estão cobertos pelos cenários internos ou personalizados sob medida para seu aplicativo.

As implementações das ações em C# são encontradas no assembly System.Fabric.dll. O módulo Service Fabric PowerShell é encontrado no assembly Microsoft.ServiceFabric.Powershell.dll. Como parte da instalação em tempo de execução, o módulo do PowerShell ServiceFabric é instalado para fins de facilidade de uso.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Ações de falha normais x anormais
As ações da Possibilidade de Teste são classificadas em dois blocos principais:

* Falhas anormais: simulam falhas como reinicializações de máquina e panes de processo. No caso dessas falhas, o contexto de execução do processo é interrompido abruptamente. Isso significa que nenhuma limpeza de estado pode ser executada antes de o aplicativo ser iniciado novamente.
* Falhas normais: simulam ações normais como movimentações de réplica e remoções acionadas pelo balanceamento de carga. Nesses casos, o serviço recebe uma notificação de fechamento e pode limpar o estado antes de sair.

Para validação de melhor qualidade, execute a carga de trabalho de serviço e comercial enquanto estiver induzindo várias falhas normais e anormais. Falhas anormais utilizam cenários em que o processo de serviço é interrompido abruptamente no meio de algum fluxo de trabalho. Isso testa o caminho de recuperação assim que a réplica do serviço é restaurada pelo Service Fabric. Isso ajudará na consistência dos dados de teste e a verificar se o estado do serviço é mantido corretamente após as falhas. O outro conjunto de falhas (falhas normais) testa se o serviço reage corretamente à movimentação das réplicas pelo Service Fabric. Isso testa o tratamento de cancelamento no método RunAsync. O serviço precisa verificar se o token de cancelamento que está sendo definido salva seu estado e encerra o método RunAsync corretamente.

## <a name="testability-actions-list"></a>Lista de ações da Possibilidade de Teste
| Ação | DESCRIÇÃO | API gerenciada | Cmdlet do Powershell | Falhas normais/anormais |
| --- | --- | --- | --- | --- |
| CleanTestState |Remove todo o estado de teste do cluster em caso de desligamento repentino do driver de teste. |CleanTestStateAsync |Remove-ServiceFabricTestState |Não aplicável |
| InvokeDataLoss |Induz a perda de dados em uma partição de serviço. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Normal |
| InvokeQuorumLoss |Coloca uma determinada partição de serviço com estado na perda de quórum. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Normal |
| MovePrimary |Move a réplica primária especificada do serviço com estado para o nó de cluster especificado. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Normal |
| MoveSecondary |Move a réplica secundária atual de um serviço com  estado para outro nó de cluster. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Normal |
| RemoveReplica |Simula uma falha de réplica removendo uma réplica de um cluster. Isso fecha a réplica e faz a transição dela para a função “Nenhum”, removendo todo o seu estado do cluster. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Normal |
| RestartDeployedCodePackage |Simula uma falha de processo do pacote de códigos reiniciando um pacote de códigos implantado em um nó em um cluster. Isso anula o processo do pacote de códigos, o que reiniciará todas as réplicas de serviço do usuário hospedadas nesse processo. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Anormais |
| RestartNode |Simula uma falha de nó de cluster da Malha de Serviço reinicializando um nó. |RestartNodeAsync |Restart-ServiceFabricNode |Anormais |
| RestartPartition |Simula um cenário de blecaute do datacenter ou cluster reiniciando algumas ou todas as réplicas de uma partição. |RestartPartitionAsync |Restart-ServiceFabricPartition |Normal |
| RestartReplica |Simula uma falha de réplica reiniciando uma réplica persistente em um cluster, fechando a réplica e reabrindo-a. |RestartReplicaAsync |Restart-ServiceFabricReplica |Normal |
| StartNode |Inicia um nó em um cluster que já foi interrompido. |StartNodeAsync |Start-ServiceFabricNode |Não aplicável |
| StopNode |Simula uma falha de nó interrompendo um nó em um cluster. O nó permanecerá inativo até que StartNode seja chamado. |StopNodeAsync |Stop-ServiceFabricNode |Anormais |
| ValidateApplication |Valida a disponibilidade e a integridade de todos os serviços da Malha de Serviço em um aplicativo, geralmente depois de induzir alguma falha no sistema. |ValidateApplicationAsync |Test-ServiceFabricApplication |Não aplicável |
| ValidateService |Valida a disponibilidade e a integridade de um serviço da Malha de Serviço, geralmente depois de induzir alguma falha no sistema. |ValidateServiceAsync |Test-ServiceFabricService |Não aplicável |

## <a name="running-a-testability-action-using-powershell"></a>Executando uma ação de possibilidade de teste usando o PowerShell
Este tutorial mostra como executar uma ação de possibilidade de teste com o PowerShell. Você aprenderá a executar uma ação da possibilidade de teste em um cluster local (one-box) ou em um cluster do Azure. Microsoft.Fabric.Powershell.dll – o módulo Service Fabric PowerShell – é instalado automaticamente quando você instala o Microsoft Service Fabric MSI. O módulo é carregado automaticamente quando você abre um prompt do PowerShell.

Segmentos do tutorial:

* [Executar uma ação em um cluster one-box](#run-an-action-against-a-one-box-cluster)
* [Executar uma ação em um cluster do Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Executar uma ação em um cluster one-box
Para executar uma ação da Possibilidade Teste em um cluster local, primeiramente, conecte-se ao cluster e abra o prompt do PowerShell no modo de administrador. Vejamos a ação **Restart-ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Aqui, a ação **Restart-ServiceFabricNode** está sendo executada em um nó chamado "Node1". O modo de preenchimento especifica que ela não deve verificar se a ação de reinicialização do nó foi bem-sucedida. Especificar o modo de preenchimento como "Verify" fará com que ele verifique se a ação de reinicialização foi bem-sucedida. Em vez de especificar o nó diretamente por seu nome, você pode especificá-lo por meio de uma chave de partição e pelo tipo de réplica, da seguinte maneira:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** deve ser usada para reiniciar um nó da Malha de Serviço em um cluster. Isso encerrará o processo Fabric.exe, que reiniciará todo o serviço do sistema e as réplicas de serviço do usuário hospedadas nesse nó. Usar essa API para testar seu serviço ajuda a descobrir bugs ao longo dos caminhos de recuperação de failover. Ela ajuda a simular falhas de nó no cluster.

A captura de tela a seguir mostra o comando **Restart-ServiceFabricNode** da possibilidade de teste em ação.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

A saída do primeiro **Get-ServiceFabricNode** (um cmdlet do módulo do PowerShell do Service Fabric) mostra que o cluster local tem cinco nós: Node.1 para Node.5. Depois que a ação da possibilidade de teste (cmdlet) **Restart-ServiceFabricNode** for executada no nó, denominado Node.4, poderemos ver o tempo de atividade do nó redefinido.

### <a name="run-an-action-against-an-azure-cluster"></a>Executar uma ação em um cluster do Azure
Executar uma ação da possibilidade de teste (usando o PowerShell) em um cluster do Azure é semelhante ao executar a ação em um cluster local. A única diferença é que, antes de executar a ação, em vez de conectar-se ao cluster local, você precisa se conectar ao cluster do Azure pela primeira vez.

## <a name="running-a-testability-action-using-c35"></a>Executando uma ação de possibilidade de teste usando o C&#35;
Para executar uma ação da possibilidade de teste usando C#, você precisa se conectar ao cluster usando o FabricClient. Em seguida, obtenha os parâmetros necessários para executar a ação. Parâmetros diferentes podem ser usados para executar a mesma ação.
Uma maneira de executar a ação RestartServiceFabricNode é usando as informações do nó (nome do nó e id da instância do nó) no cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicação do parâmetro:

* **CompleteMode** especifica que o modo não deve verificar se a ação de reinicialização de fato foi bem-sucedida. Especificar o modo de preenchimento como "Verify" fará com que ele verifique se a ação de reinicialização foi bem-sucedida.  
* **OperationTimeout** define a quantidade de tempo para conclusão da operação antes que uma exceção TimeoutException seja lançada.
* **CancellationToken** permite que uma chamada pendente seja cancelada.

Em vez de especificar o nó diretamente por seu nome, você pode especificá-lo por meio de uma chave de partição e pelo tipo de réplica.

Para obter mais informações, consulte PartitionSelector e ReplicaSelector.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector e ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector é um auxiliar exposto na possibilidade de teste e é usado para selecionar uma partição específica na qual executar qualquer uma das ações da possibilidade de teste. Ele pode ser usado para selecionar uma partição específica se a ID da partição for conhecida. Ou você pode fornecer a chave de partição e a operação resolverá a ID da partição internamente. Você também tem a opção de selecionar uma partição aleatória.

Para usar esse auxiliar, crie o objeto PartitionSelector e selecione a partição usando um dos métodos Select *. Em seguida, passe o objeto PartitionSelector para a API que precisa dele. Se nenhuma opção for selecionada, ela será padronizada para partição aleatória.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector é um auxiliar exposto na possibilidade de teste usado para ajudar a selecionar uma réplica na qual executar qualquer uma das ações da possibilidade de teste. Ele pode ser usado para selecionar uma réplica específica se a ID da réplica já for conhecida. Além disso, você tem a opção de selecionar uma réplica primária ou uma secundária aleatória. ReplicaSelector deriva de PartitionSelector, de modo que você precisa selecionar a réplica e a partição nas quais deseja executar a operação da possibilidade de teste.

Para usar esse auxiliar, crie um objeto ReplicaSelector e defina a maneira como deseja selecionar a réplica e a partição. Em seguida, passe-o para a API que o exige. Se nenhuma opção for selecionada, ela será padronizada para uma réplica ou partição aleatória.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Próximas etapas
* [Cenários da possibilidade de teste](service-fabric-testability-scenarios.md)
* Como testar seu serviço
  * [Simular falhas durante cargas de trabalho de serviço](service-fabric-testability-workload-tests.md)
  * [Falhas de comunicação entre serviços](service-fabric-testability-scenarios-service-communication.md)

