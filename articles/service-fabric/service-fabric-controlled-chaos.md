---
title: Induzir o Chaos em clusters do Service Fabric | Microsoft Docs
description: "Como usar as APIs de serviço de Injeção de falhas e análise de cluster para gerenciar o Chaos no cluster."
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: motanv
ms.openlocfilehash: 3b3b93bc9ec5ecdcfc289e5b62e84de6aa4172ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induzir o Controlled Chaos em clusters do Service Fabric
Os sistemas distribuídos em larga escala, como as infraestruturas de nuvem, não são confiáveis por natureza. O Azure Service Fabric permite aos desenvolvedores escrever serviços distribuídos confiáveis sobre uma infraestrutura não confiável. Para gravar serviços distribuídos robustos sobre uma infraestrutura não confiável, os desenvolvedores precisam poder testar a estabilidade de seus serviços enquanto a infraestrutura subjacente não confiável está passando por transições de estado complicadas devido a falhas.

O [Serviço de Injeção de Falhas e Análise de Cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (também conhecido como Serviço de Análise de Falhas) fornece aos desenvolvedores a capacidade de induzir falhas para testar os serviços. Essas falhas simuladas direcionadas, como [reiniciar uma partição](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), podem ajudar a praticar as transições de estado mais comuns. No entanto, as falhas simuladas direcionadas são tendenciosas por definição e, portanto, podem ignorar bugs que aparecem apenas em uma sequência de transições de estado complicada, longa e difícil de prever. Para um teste imparcial, você pode usar o Chaos.

O Chaos simula falhas intercaladas periódicas (amigáveis e não amigáveis) em todo o cluster durante longos períodos de tempo. Depois que tiver configurado o Chaos com a taxa e o tipo de falhas, você pode iniciar o Chaos pela API do PowerShell ou C# para começar a gerar falhas no cluster e nos serviços. Você pode configurar o Chaos para ser executado por um período especificado (por exemplo, por uma hora), após o qual o Chaos para automaticamente ou pode chamar a API StopChaos (C# ou PowerShell) para pará-lo a qualquer momento.

> [!NOTE]
> Em sua forma atual, o Chaos induz apenas falhas seguras, o que significa que, na ausência de falhas externas, uma perda de quórum ou uma perda de dados nunca ocorrerá.
>

Enquanto o Chaos estiver em execução, ele produzirá eventos diferentes que capturam o estado da execução no momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas que o Chaos decidiu executar na iteração. Um ValidationFailedEvent contém os detalhes de uma falha de validação (problemas de integridade ou estabilidade) encontrada durante a validação do cluster. Você pode invocar a API GetChaosReport (C# ou PowerShell) para obter o relatório de execuções do Chaos. Esses eventos são persistidos em um [dicionário confiável](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), que tem uma política de truncamento determinada por duas configurações: **MaxStoredChaosEventCount** (o valor padrão é 25000) e **StoredActionCleanupIntervalInSeconds** (o valor padrão é 3600). Todas as verificações do Chaos *StoredActionCleanupIntervalInSeconds* e todos os eventos *MaxStoredChaosEventCount*, exceto os mais recentes, são limpos do dicionário confiável.

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

Para obter quais falhas o Chaos induziu, você pode usar a API GetChaosReport (PowerShell ou C#). A API obtém o próximo segmento do relatório do Chaos com base no token de continuação repassado ou no intervalo repassado. Você pode especificar o ContinuationToken para obter o próximo segmento do relatório do Chaos ou especificar o intervalo por meio de StartTimeUtc e EndTimeUtc, mas não pode especificar o ContinuationToken e o intervalo de tempo na mesma chamada. Quando há mais de 100 eventos do Chaos, o relatório do Chaos é retornado em segmentos, em que um segmento contém no máximo 100 eventos do Chaos.

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
* **Contexto**: uma coleção pares chave/valor do tipo (cadeia de caracteres, cadeia de caracteres). O mapa pode ser usado para registrar informações sobre a execução do Chaos. Não pode haver mais de 100 desses pares e cada cadeia de caracteres (chave ou valor) pode ter no máximo 4095 caracteres. Esse mapa é definido pelo iniciador da execução do Chaos para armazenar opcionalmente o contexto sobre a execução específica.

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
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

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

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

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
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

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
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
