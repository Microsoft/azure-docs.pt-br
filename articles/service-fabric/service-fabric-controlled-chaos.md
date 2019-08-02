---
title: Induzir o Chaos em clusters do Service Fabric | Microsoft Docs
description: Como usar as APIs de serviço de Injeção de falhas e análise de cluster para gerenciar o Chaos no cluster.
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 7a22b17d45218c2f78220f980605b3c211495606
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543734"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induzir o Controlled Chaos em clusters do Service Fabric
Os sistemas distribuídos em larga escala, como as infraestruturas de nuvem, não são confiáveis por natureza. O Azure Service Fabric permite aos desenvolvedores escrever serviços distribuídos confiáveis sobre uma infraestrutura não confiável. Para gravar serviços distribuídos robustos sobre uma infraestrutura não confiável, os desenvolvedores precisam poder testar a estabilidade de seus serviços enquanto a infraestrutura subjacente não confiável está passando por transições de estado complicadas devido a falhas.

O [Serviço de Injeção de Falhas e Análise de Cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (também conhecido como Serviço de Análise de Falhas) fornece aos desenvolvedores a capacidade de induzir falhas para testar os serviços. Essas falhas simuladas direcionadas, como [reiniciar uma partição](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), podem ajudar a praticar as transições de estado mais comuns. No entanto, as falhas simuladas direcionadas são tendenciosas por definição e, portanto, podem ignorar bugs que aparecem apenas em uma sequência de transições de estado complicada, longa e difícil de prever. Para um teste imparcial, você pode usar o Chaos.

O Chaos simula falhas intercaladas periódicas (amigáveis e não amigáveis) em todo o cluster durante longos períodos de tempo. Uma falha normal consiste em um conjunto de chamadas de API de Service Fabric, por exemplo, falha de réplica de reinicialização é uma falha normal porque este é um fechamento seguido por uma abertura em uma réplica. Remover réplica, mover a réplica primária e mover secundária são as outras falhas normais exercidas pela caos. Falhas anormais são saídas do processo, como o nó de reinicialização e o pacote de códigos de reinicialização. 

Depois que tiver configurado o Chaos com a taxa e o tipo de falhas, você pode iniciar o Chaos pela API REST, PowerShell ou C# para começar a gerar falhas no cluster e nos serviços. Você pode configurar o Chaos para ser executado por um período especificado (por exemplo, por uma hora), após o qual o Chaos para automaticamente ou pode chamar a API StopChaos (C# ou PowerShell, ou REST) para pará-lo a qualquer momento.

> [!NOTE]
> Em sua forma atual, o Chaos induz apenas falhas seguras, o que significa que, na ausência de falhas externas, uma perda de quórum ou uma perda de dados nunca ocorrerá.
>

Enquanto o Chaos estiver em execução, ele produzirá eventos diferentes que capturam o estado da execução no momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas que o Chaos decidiu executar na iteração. Um ValidationFailedEvent contém os detalhes de uma falha de validação (problemas de integridade ou estabilidade) encontrada durante a validação do cluster. Você pode invocar a API GetChaosReport (C# ou PowerShell, ou REST) para obter o relatório de execuções do Chaos. Esses eventos são persistidos em um [dicionário confiável](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), que tem uma política de truncamento determinada por duas configurações: **MaxStoredChaosEventCount** (o valor padrão é 25000) e **StoredActionCleanupIntervalInSeconds** (o valor padrão é 3600). Todas as verificações do Chaos *StoredActionCleanupIntervalInSeconds* e todos os eventos *MaxStoredChaosEventCount*, exceto os mais recentes, são limpos do dicionário confiável.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas no Chaos
O Chaos gera falhas em todo o cluster do Service Fabric e compacta as falhas vistas em meses ou anos em poucas horas. A combinação de falhas intercaladas com a alta taxa de falhas localiza casos específicos que de outra forma seriam ignorados. Esse exercício do Chaos leva a uma melhoria significativa na qualidade do código do serviço.

O Chaos induz falhas a partir das seguintes categorias:

* Reiniciar um nó
* Reiniciar um pacote de códigos implantado
* Remover uma réplica
* Reiniciar uma réplica
* Mover uma réplica primária (configurável)
* Mover uma réplica secundária (configurável)

O Chaos é executado em várias iterações. Cada iteração é composta por falhas e validações de cluster para o período especificado. Você pode configurar o tempo gasto para o cluster se estabilizar e a validação de êxito. Se uma falha for encontrada na validação do cluster, o Chaos gerará e persistirá um ValidationFailedEvent com o carimbo de data/hora UTC e os detalhes da falha. Por exemplo, considere uma instância do Chaos, definida para ser executada por uma hora com, no máximo, três falhas simultâneas. O Chaos induz três falhas e valida a integridade do cluster. Ele itera através da etapa anterior até que seja explicitamente interrompido por meio da API StopChaosAsync ou após uma hora. Se o cluster se tornar não íntegro em qualquer iteração (ou seja, não se estabilizar dentro do MaxClusterStabilizationTimeout repassado), o Chaos gerará um ValidationFailedEvent. Esse evento indica que algo deu errado e pode precisar de mais investigação.

Para obter quais falhas o Chaos induziu, você pode usar a API GetChaosReport (PowerShell ou C#, ou REST). A API obtém o próximo segmento do relatório do Chaos com base no token de continuação repassado ou no intervalo repassado. Você pode especificar o ContinuationToken para obter o próximo segmento do relatório do Chaos ou especificar o intervalo por meio de StartTimeUtc e EndTimeUtc, mas não pode especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos do Chaos, o relatório do Chaos é retornado em segmentos, em que um segmento contém no máximo 100 eventos do Chaos.

## <a name="important-configuration-options"></a>Opções de configuração importantes
* **TimeToRun**: tempo total durante o qual o Chaos é executado antes de ser finalizado com êxito. Você pode interromper o Chaos antes que ele seja executado pelo período de TimeToRun usando a API StopChaos.

* **MaxClusterStabilizationTimeout**: a quantidade máxima de tempo de espera para que o cluster se torne íntegro antes de produzir um ValidationFailedEvent. Essa espera serve para reduzir a carga no cluster enquanto ele está se recuperando. As verificações executadas são:
  * Se a integridade do cluster está OK
  * Se a integridade do serviço está OK
  * Se o tamanho do conjunto de réplicas de destino é obtido para a partição de serviço
  * Não há réplicas InBuild
* **MaxConcurrentFaults**: o número máximo de falhas simultâneas induzidas em cada iteração. Quanto maior o número, mais agressivo o Chaos é e os failovers e as combinações de transição de estado pelos quais o cluster passa também são mais complexas. 

> [!NOTE]
> Independentemente de quão alto um valor de *MaxConcurrentFaults* está, o Chaos garante, na ausência de falhas externas, que não há perda de quorum ou perda de dados.
>

* **EnableMoveReplicaFaults**: habilita ou desabilita as falhas que causam a movimentação das réplicas primárias ou secundárias. Essas falhas estão desabilitadas por padrão.
* **WaitTimeBetweenIterations**: a quantidade de tempo de espera entre as iterações. Ou seja, a quantidade de tempo que pela qual o Chaos fará uma pausa após ter executado uma rodada de falhas e ter concluído a validação correspondente da integridade do cluster. Quanto maior o valor, menor é a taxa de injeção de falhas média.
* **WaitTimeBetweenFaults**: o tempo de espera entre as duas falhas consecutivas em uma única iteração. Quanto maior o valor, menor a simultaneidade (ou a sobreposição entre) de falhas.
* **ClusterHealthPolicy**: a política de integridade do cluster é usada para validar a integridade do cluster entre iterações do Chaos. Se a integridade do cluster estiver em erro ou se ocorrer uma exceção inesperada durante a execução de falhas, o Chaos aguardará 30 minutos antes da próxima verificação de integridade, para fornecer ao cluster algum tempo para se recuperar.
* **Context**: uma coleção pares chave/valor do tipo (cadeia de caracteres, cadeia de caracteres). O mapa pode ser usado para registrar informações sobre a execução do Chaos. Não pode haver mais de 100 desses pares e cada cadeia de caracteres (chave ou valor) pode ter no máximo 4095 caracteres. Esse mapa é definido pelo iniciador da execução do Chaos para armazenar opcionalmente o contexto sobre a execução específica.
* **ChaosTargetFilter**: este filtro pode ser usado para falhas de Chaos de destino somente para determinados tipos de nó ou apenas algumas instâncias de aplicativo. Se ChaosTargetFilter não for usado, Chaos falha em todas as entidades de cluster. Se ChaosTargetFilter for usado, Chaos falha apenas as entidades que atendem a especificação de ChaosTargetFilter. NodeTypeInclusionList e ApplicationInclusionList permitem apenas semântica de união. Em outras palavras, não é possível especificar uma interseção de NodeTypeInclusionList e ApplicationInclusionList. Por exemplo, não é possível especificar "falha deste aplicativo somente quando ele estiver nesse tipo de nó." Depois que uma entidade é incluída em NodeTypeInclusionList ou ApplicationInclusionList, essa entidade não pode ser excluída usando ChaosTargetFilter. Mesmo se não aparecer aplicativoX no ApplicationInclusionList, em alguns aplicativosX de iteração Chaos podem apresentar falha porque isso acontece de estar em um nó de nodeTypeY que está incluído no NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList são nulos ou vazios, ArgumentException será lançada.
    * **NodeTypeInclusionList**: uma lista de tipos de nó para incluir em falhas de Chaos. Todos os tipos de falhas (reiniciar o nó, reiniciar codepackage, remover a réplica, reinicie a réplica, mover primário e mover secundário) estão habilitados para os nós desses tipos de nó. Se um nodetype (digamos NodeTypeX) não aparece na NodeTypeInclusionList, em seguida, as falhas de nível de nó(como NodeRestart) nunca serão habilitadas para os nós de NodeTypeX, mas falhas de pacote e de réplica de código ainda podem ser habilitadas para NodeTypeX se um aplicativo no ApplicationInclusionList acontece deve residir em um nó de NodeTypeX. No máximo 100 nomes de tipo de nó podem ser incluídos nessa lista, para aumentar esse número, uma atualização de configuração é necessária para a configuração de MaxNumberOfNodeTypesInChaosTargetFilter.
    * **ApplicationInclusionList**: uma lista de URIs de aplicativo para incluir em falhas de Chaos. Todas as réplicas que pertencem aos serviços desses aplicativos são receptivos a falhas de réplica (reinicialização de réplica, remover réplica, mover primário e secundário de movimentação) por Chaos. Chaos pode reiniciar um pacote de código somente se o pacote de código hospeda réplicas somente desses aplicativos. Se um aplicativo não aparecer nessa lista, ele ainda pode falhar em alguma iteração do Chaos se o aplicativo terminar em um nó de um tipo de nó que está incluído em NodeTypeInclusionList. No entanto se aplicativoX for vinculado a nodeTypeY por meio de restrições de posicionamento e aplicativoX está ausente do ApplicationInclusionList e nodeTypeY está ausente do NodeTypeInclusionList, em seguida, aplicativoX será nunca interrompido. No máximo 1000 nomes de tipo de nó podem ser incluídos nessa lista, para aumentar esse número, uma atualização de configuração é necessária para a configuração de MaxNumberOfNodeTypesInChaosTargetFilter.

## <a name="how-to-run-chaos"></a>Como executar o Chaos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
