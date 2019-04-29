---
title: Diagnóstico para Reliable Services com monitoração de estado do Azure Service Fabric | Microsoft Docs
description: Funcionalidade de diagnóstico para Reliable Services com estado no Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: f49176f944aa2abfa1d355ce0bd207d1b544c275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772951"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidade de diagnóstico para Reliable Services com monitoração de estado
A classe StatefulServiceBase dos Reliable Services com Estado do Azure Service Fabric emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que podem ser usados para depurar o serviço, fornecer informações sobre como o tempo de execução está funcionando e ajudar a solucionar problemas.

## <a name="eventsource-events"></a>Eventos EventSource
O nome do EventSource da classe StatefulServiceBase de Reliable Services com estado é "Microsoft-ServiceFabric-Services." Os eventos dessa origem de eventos aparecem na janela [Eventos de Diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o serviço está sendo [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam a coletar e/ou exibir eventos EventSource são [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) e [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos
| Nome do evento | ID do evento | Nível | Descrição do evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informativo |Emitido quando a tarefa RunAsync do serviço é iniciada |
| StatefulRunAsyncCancellation |2 |Informativo |Emitido quando a tarefa RunAsync do serviço é cancelada |
| StatefulRunAsyncCompletion |3 |Informativo |Emitido quando a tarefa RunAsync do serviço é concluída |
| StatefulRunAsyncSlowCancellation |4 |Aviso |Emitido quando a tarefa RunAsync do serviço leva muito tempo para concluir o cancelamento |
| StatefulRunAsyncFailure |5 |Erro |Emitido quando a tarefa RunAsync do serviço lança uma exceção |

## <a name="interpret-events"></a>Interpretar eventos
Os eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation são úteis para o gravador de serviço para entender o ciclo de vida de um serviço, bem como o tempo em que um serviço é iniciado, cancelado ou concluído. Essas informações podem ser úteis ao depurar problemas de serviço ou entender o ciclo de vida do serviço.

Gravadores de serviço devem prestar muita atenção nos eventos StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure, pois eles indicam problemas com o serviço.

StatefulRunAsyncFailure é emitido sempre que a tarefa RunAsync() do serviço lança uma exceção. Geralmente uma exceção lançada indica um erro ou um bug no serviço. Além disso, a exceção faz com que o serviço falhe e seja movido para um nó diferente. Essa operação pode ser dispendiosa e pode atrasar as solicitações recebidas enquanto o serviço é movido. Gravadores de serviço devem determinar a causa da exceção e atenuá-la se possível.

StatefulRunAsyncSlowCancellation é emitido sempre que uma solicitação de cancelamento da tarefa RunAsync leva mais de quatro segundos. Quando um serviço leva muito tempo para concluir o cancelamento, ele afeta a capacidade de o serviço ser reiniciado rapidamente em outro nó. Este cenário pode afetar a disponibilidade geral do serviço.

## <a name="performance-counters"></a>contadores de desempenho
O tempo de execução dos Reliable Services define as categorias de contador de desempenho a seguir:

| Category | DESCRIÇÃO |
| --- | --- |
| Replicador Transacional do Service Fabric |Contadores específicos para o Replicador Transacional do Azure Service Fabric |
| Service Fabric TStore |Contadores específicos para o Replicador Transacional |

O replicador transacional do Service Fabric é usado pelo [Gerenciador de estado confiável](service-fabric-reliable-services-reliable-collections-internals.md) para replicar as transações em um determinado conjunto de [réplicas](service-fabric-concepts-replica-lifecycle.md).

O Service Fabric TStore é um componente usado nas [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections-internals.md) para armazenar e recuperar pares de valor de chave.

O aplicativo [Monitor de Desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) está disponível por padrão no sistema operacional Windows e pode ser usado para coletar e exibir dados do contador de desempenho. [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para coletar dados do contador de desempenho e carregá-los nas tabelas do Azure.

### <a name="performance-counter-instance-names"></a>Nomes da instância do contador de desempenho
Um cluster que tem um grande número de serviços confiáveis ou partições de serviço confiáveis terá um grande número de instâncias do contador de desempenho do replicador transacional. Isso também é o caso para os contadores TStore contadores de desempenho, mas também é multiplicado pelo número de dicionários confiáveis e filas confiáveis usadas. Os nomes da instância do contador de desempenho podem ajudar a identificar a réplica de [partição](service-fabric-concepts-partitioning.md) específica e o fornecedor de estado no caso de TStore que a instância do contador de desempenho está associada.

#### <a name="service-fabric-transactional-replicator-category"></a>Categoria do Replicador Transacional do Service Fabric
Para a categoria `Service Fabric Transactional Replicator`, os nomes da instância do contador estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionID* é a representação da cadeia de caracteres da ID da partição do Service Fabric à qual a instância do contador de desempenho está associada. A ID da partição é um GUID e sua representação da cadeia de caracteres é gerada por meio do [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

O *ServiceFabricReplicaId* é a ID associada a uma determinada réplica de um serviço confiável. A ID de Réplica está incluida no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outras instâncias do contador de desempenho. Mais detalhes sobre réplicas e sua função de serviços confiáveis podem ser encontrados [aqui](service-fabric-concepts-replica-lifecycle.md).

O seguinte nome de instância do contador é típico para um contador na `Service Fabric Transactional Replicator` categoria:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cadeia de caracteres da ID da partição do Service Fabrica, e `131652217797162571` é a ID de réplica.

#### <a name="service-fabric-tstore-category"></a>Categoria do Service Fabric TStore
Para a categoria `Service Fabric TStore`, os nomes da instância do contador estão no seguinte formato:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionID* é a representação da cadeia de caracteres da ID da partição do Service Fabric à qual a instância do contador de desempenho está associada. A ID da partição é um GUID e sua representação da cadeia de caracteres é gerada por meio do [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) com o especificador de formato "D".

O *ServiceFabricReplicaId* é a ID associada a uma determinada réplica de um serviço confiável. A ID de Réplica está incluida no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outras instâncias do contador de desempenho. Mais detalhes sobre réplicas e sua função de serviços confiáveis podem ser encontrados [aqui](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* é a ID associada a um provedor de estado dentro de um serviço confiável. O ID do provedor de estado está incluído no nome de instância do contador de desempenho para diferenciar um TStore de outro.

*PerformanceCounterInstanceDifferentiator* é uma ID de diferenciação associada a uma instância do contador de desempenho dentro de um provedor de estado. Este diferenciador está incluido no nome da instância do contador de desempenho para garantir sua exclusividade e evitar conflito com outras instâncias do contador geradas pelo mesmo fornecedor de estado.

O seguinte nome de instância do contador é típico para um contador na `Service Fabric TStore` categoria:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

No exemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` é a representação de cadeia de caracteres da ID da partição do Service Fabric `131652217797162571` é a ID de réplica `142652217797162571` é a ID do provedor de estado, e `1337` é o diferenciador de instância do contador de desempenho.

### <a name="transactional-replicator-performance-counters"></a>Contadores de desempenho de replicador transacional

O tempo de execução de Serviços Confiáveis emite os eventos a seguir na `Service Fabric Transactional Replicator` categoria

 Nome do contador | DESCRIÇÃO |
| --- | --- |
| Operações de Início de Trans./s | Número de novas transações de gravação criadas por segundo.|
| Operações de Transação/s | O número de operações de adicionar/atualizar/excluir executadas em coleções confiáveis por segundo.|
| Bytes de Liberação de Log/s | O número de bytes que estão sendo liberados para o disco pelo Replicador Transacional por segundo |
| Operações Limitadas/s | O número de operações rejeitadas a cada segundo pelo Replicador Transacional devido à limitação. |
| Média Transação em ms/confirmação | Latência de confirmação média por transação em milissegundos |
| Média Latência Média de Liberação (ms) | Duração média das operações de limpeza de disco iniciada pelo Replicador Transacional em milissegundos |

### <a name="tstore-performance-counters"></a>Contadores de desempenho TStore

O tempo de execução de Serviços Confiáveis emite os eventos a seguir na `Service Fabric TStore` categoria

 Nome do contador | DESCRIÇÃO |
| --- | --- |
| Contagem de Itens | O número de itens no repositório.|
| Tamanho do disco | Tamanho total do disco, em bytes, dos arquivos de ponto de verificação para o repositório.|
| Bytes de gravação de arquivo de ponto de verificação por segundo | O número de bytes gravados por segundo para o arquivo de ponto de verificação mais recente.|
| Bytes de transferência de disco de cópia por segundo | O número de bytes de disco lidos (na réplica primária) ou gravados (em uma réplica secundária) por segundo durante uma cópia do repositório.|

## <a name="next-steps"></a>Próximas etapas
[Provedores de EventSource no PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
