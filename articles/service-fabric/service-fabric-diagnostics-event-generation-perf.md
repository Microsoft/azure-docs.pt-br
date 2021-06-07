---
title: Monitoramento de desempenho do Azure Service Fabric
description: Saiba mais sobre os contadores de desempenho para o monitoramento e diagnóstico de clusters do Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: abe1b67bee863f287f148fdd9af13198b67db0f0
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628859"
---
# <a name="performance-metrics"></a>Métricas de desempenho

As métricas devem ser coletadas para entender o desempenho de seu cluster, bem como os aplicativos em execução nele. Para clusters do Service Fabric, recomendamos coletar os seguintes contadores de desempenho.

## <a name="nodes"></a>Nós

Para as máquinas em seu cluster, considere a possibilidade de coletar os seguintes contadores de desempenho a fim de entender melhor a carga em cada computador e tomar decisões apropriadas sobre o dimensionamento do cluster.

| Categoria do Contador | Nome do contador |
| --- | --- |
| Disco Lógico | Espaço livre em disco lógico |
| PhysicalDisk(per Disco) | Comprimento médio da fila de leitura de disco |
| PhysicalDisk(per Disco) | Comprimento médio da fila de gravação de disco |
| PhysicalDisk(per Disco) | Média de segundos/Leitura do Disco |
| PhysicalDisk(per Disco) | Média de segundos/Gravação do Disco |
| PhysicalDisk(per Disco) | Leituras de Disco/s |
| PhysicalDisk(per Disco) | Bytes Lidos no Disco/s |
| PhysicalDisk(per Disco) | Gravações de Disco/s |
| PhysicalDisk(per Disco) | Bytes Gravados no Disco/s |
| Memória | MBytes Disponíveis |
| PagingFile | % Uso |
| Processador(total) | % Tempo do Processador |
| Processo (por serviço) | % Tempo do Processador |
| Processo (por serviço) | ID do Processo |
| Processo (por serviço) | Bytes Particulares |
| Processo (por serviço) | Contagem de Threads |
| Processo (por serviço) | Bytes Virtuais |
| Processo (por serviço) | Conjunto de Trabalho |
| Processo (por serviço) | Conjunto de Trabalho - Particular |
| Interface de rede(todas as instâncias) | Bytes recd |
| Interface de rede(todas as instâncias) | Bytes sent |
| Interface de rede(todas as instâncias) | Bytes total |
| Interface de rede(todas as instâncias) | Tamanho da Fila de Saída |
| Interface de rede(todas as instâncias) | Pacotes de Saída Descartados |
| Interface de rede(todas as instâncias) | Pacotes Recebidos Descartados |
| Interface de rede(todas as instâncias) | Erros de Pacotes de Saída |
| Interface de rede(todas as instâncias) | Erros de Pacotes Recebidos |

## <a name="net-applications-and-services"></a>Aplicativos e serviços de .NET

Colete os contadores a seguir se você estiver implantando serviços .NET em seu cluster. 

| Categoria do Contador | Nome do contador |
| --- | --- |
| Memória CLR .NET (por serviço) | ID do Processo |
| Memória CLR .NET (por serviço) | Nº Total de Bytes confirmados |
| Memória CLR .NET (por serviço) | N º Total de Bytes reservados |
| Memória CLR .NET (por serviço) | N º de Bytes em todos os Heaps |
| Memória CLR .NET (por serviço) | Tamanho de Heap de objeto grande |
| Memória CLR .NET (por serviço) | # Identificadores de GC |
| Memória CLR .NET (por serviço) | Nº de Coleções Geração 0 |
| Memória CLR .NET (por serviço) | Nº de Coleções Geração 1 |
| Memória CLR .NET (por serviço) | Nº de Coleções Geração 2 |
| Memória CLR .NET (por serviço) | % de Tempo na GC |

### <a name="service-fabrics-custom-performance-counters"></a>Contadores de desempenho personalizados do Service Fabric

O Service Fabric gera uma quantidade significativa de contadores de desempenho personalizados. Se o SDK estiver instalado, você poderá ver a lista abrangente em seu computador com Windows em seu aplicativo de Monitor de Desempenho (Iniciar > Monitor de Desempenho). 

Nos aplicativos que você está implantando no cluster, se você estiver usando os Reliable Actors, adicione contadores das categorias `Service Fabric Actor` e `Service Fabric Actor Method` (confira [Diagnóstico dos Reliable Actors do Service Fabric](service-fabric-reliable-actors-diagnostics.md)).

Se você usa Serviços Confiáveis ou Remoting de Serviço, da mesma forma temos `Service Fabric Service` e `Service Fabric Service Method` categorias de contador das quais você deve coletar contadores, consulte [monitoramento com contadores remotos](service-fabric-reliable-serviceremoting-diagnostics.md) e [contador de desempenho de serviços confiáveis](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Se você usar Coleções Confiáveis, recomendamos adicionar o `Avg. Transaction ms/Commit` do `Service Fabric Transactional Replicator` para coletar a latência média de confirmação por métrica da transação.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [geração de eventos no nível de plataforma](service-fabric-diagnostics-event-generation-infra.md) do Service Fabric
* Coletar métricas de desempenho por meio do [Agente do Log Analytics](service-fabric-diagnostics-oms-agent.md)
