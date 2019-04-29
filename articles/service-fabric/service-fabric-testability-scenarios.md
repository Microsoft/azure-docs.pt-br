---
title: Criar testes de caos e failover para o Azure Service Fabric | Microsoft Docs
description: Usando os cenários de testes de caos e failover do Service Fabric para induzir falhas e verificar a confiabilidade de seus serviços.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d12c5097d4ba5e0ccfe0e2b2cbc8ccd758c32d98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864987"
---
# <a name="testability-scenarios"></a>Cenários da possibilidade de teste
Sistemas grandes distribuídos como infraestruturas de nuvem não são confiáveis por natureza. O Service Fabric do Azure permite aos desenvolvedores gravar os serviços para serem executados em infraestruturas não confiáveis. Para gravar serviços de alta qualidade, os desenvolvedores precisam ser capazes de induzir essa infraestrutura não confiável a testar a estabilidade dos seus serviços.

O serviço Análise de Falhas fornece aos desenvolvedores a capacidade de induzir ações de falha para testar serviços em caso de falhas. No entanto, falhas simuladas direcionadas não o levarão tão longe. Para fazer mais testes, você pode usar os cenários de teste no Service Fabric: um teste de caos e um de failover. Esses cenários simulam falhas intercaladas contínuas, amigáveis e não amigáveis, em todo o cluster por longos períodos de tempo. Quando um teste é configurado com a taxa e o tipo de falha, ele pode ser iniciado por meio de APIs do C# ou do PowerShell para gerar falhas no cluster e no serviço.

> [!WARNING]
> ChaosTestScenario está sendo substituído por um Chaos baseado em serviço mais resiliente. Confira o novo artigo [Controlled Chaos](service-fabric-controlled-chaos.md) para obter mais detalhes.
> 
> 

## <a name="chaos-test"></a>Teste de caos
O cenário de caos gera falhas em todo o cluster do Service Fabric. O cenário compacta falhas geralmente vistas em meses ou em anos em apenas algumas horas. A combinação de falhas intercaladas com a alta taxa de falhas localiza casos específicos que de outra forma seriam ignorados. Isso leva a uma melhoria significativa na qualidade do código do serviço.

### <a name="faults-simulated-in-the-chaos-test"></a>Falhas simuladas no teste de caos
* Reiniciar um nó
* Reiniciar um pacote de códigos implantado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (opcional)
* Mover uma réplica secundária (opcional)

O teste de caos executa várias iterações de falhas e validações de cluster durante o período de tempo especificado. O tempo gasto para o cluster se estabilizar e a validação de êxito também são configuráveis. O cenário falha quando nos deparamos com uma única falha na validação do cluster.

Por exemplo, considere um teste definido para ser executado por uma hora com um máximo de três falhas simultâneas. O teste induzirá três falhas e validará a integridade do cluster. O teste será iterado por meio da etapa anterior até que o cluster perca a integridade ou tenha decorrido uma hora. Se o cluster se tornar não íntegro em qualquer iteração, ou seja, não se estabilizar em um tempo configurado, o teste falhará com uma exceção. Essa exceção indica que algo deu errado e precisa de mais investigação.

Em sua forma atual, o mecanismo de geração de falha no teste de caos induz somente a falhas seguras. Isso significa que, na ausência de falhas externas, uma perda de quorum ou dados nunca ocorrerá.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: Tempo total que o teste será executado antes de concluir com êxito. O teste pode ser finalizado antes, no lugar de uma falha de validação.
* **MaxClusterStabilizationTimeout**: Quantidade máxima de tempo de espera para o cluster se torne Íntegro antes do teste falhar. As verificações executadas são as seguintes: se a integridade do cluster está OK, se a integridade do serviço está OK, se o tamanho do conjunto de réplicas de destino foi atingido para a partição de serviço e se não existe réplica do InBuild.
* **MaxConcurrentFaults**: Número máximo de falhas simultâneas induzidas em cada iteração. Quanto maior o número, mais agressivo o teste, resultando em failovers mais complexos e combinações de transição. O teste garante que, na ausência de falhas externas, não haverá uma perda de quorum ou de dados, independentemente de quão alta essa configuração está.
* **EnableMoveReplicaFaults**: Habilita ou desabilita as falhas que estão causando a movimentação das réplicas primárias ou secundárias. Essas falhas estão desabilitadas por padrão.
* **WaitTimeBetweenIterations**: Quantidade de tempo de espera entre as iterações, isto é, após uma rodada de falhas e a validação correspondente.

### <a name="how-to-run-the-chaos-test"></a>Como executar o teste de caos
Exemplo de C#

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Teste de failover
O cenário do teste de failover é uma versão do cenário de teste de caos que visa uma partição de serviço específica. Ele testa o efeito de failover em uma partição de serviço específica, sem afetar os outros serviços. Quando configurado com as informações de partição de destino e outros parâmetros, ele é executado como uma ferramenta do lado do cliente que usa as APIs do C# ou o PowerShell para gerar falhas para uma partição de serviço. O cenário é iterado por meio de uma sequência de falhas simuladas e validação de serviço enquanto a lógica de negócios é executada ao lado para fornecer uma carga de trabalho. Uma falha na validação do serviço indica um problema que precisa de mais investigação.

### <a name="faults-simulated-in-the-failover-test"></a>Falhas simuladas no teste de failover
* Reiniciar um pacote de códigos implantado onde a partição está hospedada
* Remover uma réplica primária/secundária ou uma instância sem estado
* Reiniciar uma réplica primária secundária (se o serviço persistir)
* Mover uma réplica primária
* Mover uma réplica secundária
* Reiniciar a partição

O teste de failover induz a uma falha escolhida e depois executa a validação no serviço para assegurar sua estabilidade. O teste de failover induz apenas a uma falha de cada vez, em vez de possíveis várias falhas no teste de caos. Se a partição de serviço não estabilizar no tempo limite configurado após cada falha, o teste falhará. O teste induz apenas a falhas seguras. Isso significa que, na ausência de falhas externas, não ocorre uma perda de quorum ou dados.

### <a name="important-configuration-options"></a>Opções de configuração importantes
* **PartitionSelector**: Objeto de seletor que especifica a partição que precisa ser direcionada.
* **TimeToRun**: Tempo total que o teste será executado antes de concluir.
* **MaxServiceStabilizationTimeout**: Quantidade máxima de tempo de espera para o cluster se torne Íntegro antes do teste falhar. As verificações executadas são as seguintes: se a integridade do serviço está OK, se o tamanho do conjunto de réplicas de destino foi atingido para todas as partições e se não existe réplica do InBuild.
* **WaitTimeBetweenFaults**: Quantidade de tempo de espera entre cada ciclo de falha e de validação.

### <a name="how-to-run-the-failover-test"></a>Como executar o teste de failover
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
