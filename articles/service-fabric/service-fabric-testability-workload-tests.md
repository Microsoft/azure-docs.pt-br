---
title: Simular falhas nos aplicativos Service Fabric do Azure
description: Saiba mais sobre como proteger seus serviços de Service Fabric do Azure contra falhas normais e não-cortesia.
ms.topic: conceptual
ms.date: 06/15/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c714ae30c64ea073cbac521eac5e15a8d968b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531303"
---
# <a name="simulate-failures-during-service-workloads"></a>Simular falhas durante cargas de trabalho de serviço
Os cenários de possibilidade de teste no Service Fabric do Azure isentam os desenvolvedores da preocupação com o tratamento de falhas individuais. No entanto, há cenários em que uma intercalação explícita das falhas e da carga de trabalho do cliente pode ser necessária. A intercalação das falhas e da carga de trabalho do cliente garante que o serviço realmente execute alguma ação quando a falha acontece. Dado o nível de controle que a possibilidade de teste fornece, elas podem estar em pontos precisos da execução da carga de trabalho. Essa indução de falhas em diferentes estados no aplicativo pode encontrar bugs e melhorar a qualidade.

## <a name="sample-custom-scenario"></a>Exemplo de cenário personalizado
Esse teste mostra um cenário que intercala a carga de trabalho de negócios com [falhas normais e anormais](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). As falhas devem ser induzidas durante as operações ou cálculos do serviço para obter melhores resultados.

Vamos examinar um exemplo de um serviço que expõe quatro cargas de trabalho: A, B, C e D. Cada uma corresponde a um conjunto de fluxos de trabalho que pode ser computação, armazenamento ou uma combinação. Para simplificar, vamos abstrair as cargas de trabalho de nosso exemplo. As diferentes falhas executadas neste exemplo são:

* RestartNode: falha anormal para simular uma reinicialização do computador.
* RestartDeployedCodePackage: falha anormal para simular uma falha do processo de host de serviço.
* RemoveReplica: falha normal para simular remoção de réplica.
* MovePrimary: falha normal para simular movimentações de réplica disparadas pelo balanceador de carga do Service Fabric.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
