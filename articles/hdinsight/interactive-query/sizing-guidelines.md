---
title: Guia de dimensionamento do cluster de Consulta Interativa no Azure HDInsight
description: Guia de Dimensionamento da Consulta Interativa no Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a7baa9340a1f0a99b94bfcbe535c73d0b502e2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933061"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Guia de dimensionamento do cluster de Consulta Interativa no Azure HDInsight

Este documento descreve o dimensionamento do cluster de Consulta Interativa do HDInsight (LLAP) para uma carga de trabalho normal para obter um desempenho aceitável. As recomendações deste documento são cargas de trabalho genéricas e específicas que podem precisar de ajuste específico.

## <a name="default-vm-types-for-interactive-query"></a>Tipos de VM padrão para Consulta Interativa

| Tipo de nó | Instância | Tamanho |
|---|---|---|
| Head | D13 v2 | 8 VCPUS, 56 GB de RAM, SSD de 400 GB |
| Trabalho | D14 v2 | 16 VCPUS, 112 GB de RAM, SSD de 800 GB |
| ZooKeeper | A4 v2 | 4 VCPUS, 8 GB de RAM, SSD de 40 GB |

## <a name="recommended-configurations"></a>Configurações recomendadas

Os valores de configuração recomendados se baseiam no nó de trabalho do tipo D14 v2.

| Chave | Valor | Descrição |
|---|---|---|
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Memória total fornecida, em MB, para todos os contêineres de YARN em um nó. |
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | A alocação máxima de cada solicitação de contêiner no RM, em MBs. As solicitações de memória maiores que esse valor não entrarão em vigor. |
| yarn.scheduler.maximum-allocation-vcores | 12 |O número máximo de núcleos de CPU de cada solicitação de contêiner no Resource Manager. As solicitações maiores que esse valor não entrarão em vigor. |
| yarn.scheduler.capacity.root.llap.capacity | 90% | Alocação de capacidade do YARN para fila de LLAP.  |
| hive.server2.tez.sessions.per.default.queue | number_of_worker_nodes |O número de sessões de cada fila chamada no hive.server2.tez.default.queues. Esse número corresponde ao número de coordenadores de consulta (Tez AMs). |
| tez.am.resource.memory.mb | 4096 (MB) | A quantidade de memória em MB a ser usada pelo tez AppMaster. |
| hive.tez.container.size | 4096 (MB) | Tamanho do contêiner Tez especificado em MB. |
| hive.llap.daemon.num.executors | 12 | Número de executores por daemon de LLAP. |
| hive.llap.io.threadpool.size | 12 | Tamanho do pool de threads para executores. |
| hive.llap.daemon.yarn.container.mb | 86016 (MB) | Memória total em MB usada por daemons de LLAP individuais (memória por daemon).|
| hive.llap.io.memory.size | 409600 (MB) | Tamanho do cache em MB por daemon LLAP, desde que o cache de SSD esteja habilitado. |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | tamanho da memória em MB para fazer a Junção de mapa. |

## <a name="llap-daemon-size-estimations"></a>Estimativas de tamanho do daemon de LLAP  

### <a name="yarnnodemanagerresourcememory-mb"></a>yarn.nodemanager.resource.memory-mb

Esse valor indica uma soma máxima da memória em MB usada pelos contêineres YARN em cada nó. Ele especifica a quantidade de memória que o YARN pode usar nesse nó. Portanto, esse valor deve ser menor que a memória total nesse nó.

Defina esse valor = [memória física total no nó] – [memória do sistema operacional + outros serviços].

É recomendável definir esse valor como aproximadamente 90% da RAM disponível. Para D14 v2, o valor recomendado é **102400 MB**.

### <a name="yarnschedulermaximum-allocation-mb"></a>yarn.scheduler.maximum-allocation-mb

Esse valor indica a alocação máxima de cada solicitação de contêiner no Azure Resource Manager, em MB. As solicitações de memória maiores que o valor especificado não entrarão em vigor. O Azure Resource Manager só pode fornecer memória a contêineres em incrementos de `yarn.scheduler.minimum-allocation-mb` e não pode exceder o tamanho especificado por `yarn.scheduler.maximum-allocation-mb`. Esse valor não deve ser maior que a memória total fornecida do nó, que é especificada por `yarn.nodemanager.resource.memory-mb`.

Para nós de trabalho D14 v2, o valor recomendado é **102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>yarn.scheduler.maximum-allocation-vcores

Essa configuração indica o número máximo de núcleos de CPU virtual de cada solicitação de contêiner no Azure Resource Manager. A solicitação de um valor maior que essa configuração não entrará em vigor. Essa configuração é uma propriedade global do Agendador do YARN. Para o contêiner do daemon de LLAP, esse valor pode ser definido como 75% do total de VCORES (núcleos virtuais) disponíveis. Os 25% restantes devem ser reservados para NodeManager, DataNode e outros serviços em execução nos nós de trabalho.  

Para nós de trabalho D14 v2, há 16 VCORES e 75% das 16 VCORES podem ser fornecidos. Portanto, o valor recomendado para o contêiner do daemon de LLAP é **12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>hive.server2.tez.sessions.per.default.queue

Esse valor de configuração determina o número de sessões de Tez que devem ser iniciadas paralelamente para cada uma das filas especificadas por `hive.server2.tez.default.queues`. O valor corresponde ao número de Tez AMs (Coordenadores de Consulta). É recomendável que seja igual ao número de nós de trabalho para ter um Tez AM por nó. O número de Tez AMs pode ser maior que o número de nós do daemon de LLAP. Sua principal responsabilidade é coordenar a execução da consulta e atribuir fragmentos do plano de consulta aos daemons de LLAP correspondentes para execução. É recomendável mantê-lo como múltiplo de vários nós daemon LLAP para obter maior taxa de transferência.  

O cluster HDInsight padrão tem quatro daemons de LLAP em execução em quatro nós de trabalho. Portanto, o valor recomendado é **4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez.am.resource.memory.mb, hive.tez.container.size

`tez.am.resource.memory.mb` define o tamanho mestre do aplicativo Tez.  
O valor recomendado é **4096 MB**.

`hive.tez.container.size` define a quantidade de memória fornecida para o contêiner Tez. Esse valor deve ser definido entre o tamanho mínimo do contêiner do YARN (`yarn.scheduler.minimum-allocation-mb`) e o tamanho máximo do contêiner do YARN (`yarn.scheduler.maximum-allocation-mb`).  
É recomendável que seja definido como **4096 MB**.  

A regra geral é mantê-la menor que a quantidade de memória por processador, considerando um processador por contêiner. `Rreserve` memória do número de Tez AMs em um nó antes de fornecer a memória para o daemon de LLAP. Por exemplo, se você estiver usando dois Tez AMs (4 GB cada) por nó, forneça 82 GB dos 90 GB para o daemon de LLAP, reservando 8 GB para dois Tez AMs.

### <a name="yarnschedulercapacityrootllapcapacity"></a>yarn.scheduler.capacity.root.llap.capacity

Esse valor indica uma porcentagem da capacidade fornecida para a fila de LLAP. O cluster de consulta interativa HDInsights fornece 90% da capacidade total para a fila de LLAP e os 10% restantes são definidos como a fila padrão para outras alocações de contêiner.  
Para nós de trabalho D14v2, o valor recomendado é **90 MB** para a fila de LLAP.

### <a name="hivellapdaemonyarncontainermb"></a>hive.llap.daemon.yarn.container.mb

O tamanho total da memória para o daemon de LLAP depende dos seguintes componentes:

* Configuração do tamanho do contêiner do YARN (`yarn.scheduler.maximum-allocation-mb`, `yarn.scheduler.maximum-allocation-mb`, `yarn.nodemanager.resource.memory-mb`)

* Memória de heap usada pelos executores (Xmx)

    Sua quantidade de RAM disponível após remover o tamanho da reserva dinâmica.  
    Para D14 v2, HDI 4.0 - esse valor é (86 GB - 6 GB) = 80 GB  
    Para D14 v2, HDI 3.6 - esse valor é (84 GB - 6 GB) = 78 GB

* Cache na memória fora do heap por daemon (hive.llap.io.memory.size)

* Reserva dinâmica

    É uma parte da memória fora do heap usada para sobrecarga de VM Java (metaespaço, pilha de threads, estruturas de dados GC e assim por diante). Observa-se que essa parte tem cerca de 6% do tamanho da pilha (Xmx). Para estar no lado mais seguro, ela pode ser calculada como 6% do tamanho total da memória do daemon de LLAP. Como é possível quando o cache SSD está ativado, ela permite que o daemon de LLAP use todo o espaço disponível na memória para ser usado apenas para heap.  
    Para D14 v2, o valor recomendado é teto (86 GB x 0.06) ~= **6 GB**.  

Memória por daemon = [tamanho do cache na memória] + [tamanho do heap] + [reserva dinâmica].

Pode ser calculada da seguinte maneira:

Memória do Tez AM por node = [(número de Tez AMs/número de nós do daemon de LLAP) * tamanho do Tez AM].
Tamanho do contêiner do daemon de LLAP = [90% da memória máxima do contêiner do YARN] - [memória do Tez AM por nó].

Para o nó de trabalho D14 v2, HDI 4.0 - o valor recomendado é (90 - (1/1 * 4 GB)) = **86 GB**.
(Para HDI 3.6, o valor recomendado é **84 GB** porque você deve reservar cerca de 2 GB para o AM do controle deslizante.)  

### <a name="hivellapiomemorysize"></a>hive.llap.io.memory.size

Essa configuração é a quantidade de memória disponível como cache para o daemon de LLAP. Os daemons de LLAP podem usar o SSD como cache. A configuração `hive.llap.io.allocator.mmap` = true habilita o cache SSD. O D14 v2 é fornecido com aproximadamente 800 GB de SSD e o cache SSD é habilitado por padrão para o cluster de consulta interativa (LLAP). Ele está configurado para usar 50% do espaço SSD para o cache fora do heap.

Para D14 v2, o valor recomendado é **409600 MB**.  

Para outras VMs, sem cache SSD habilitado, é útil fornecer parte da RAM disponível para o cache LLAP a fim de obter melhor desempenho. Ajuste o tamanho total da memória para o daemon de LLAP da seguinte maneira:  

Memória total do daemon de LLAP = [tamanho do cache LLAP] + [tamanho do heap] + [reserva dinâmica].

É recomendável ajustar o tamanho do cache e o tamanho do heap mais adequado para a carga de trabalho.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

Essa configuração controla o número de executores que podem realizar tarefas em paralelo por daemon LLAP. Esse valor é um equilíbrio entre o número de VCORES disponíveis, a quantidade de memória fornecida por executor e a memória total disponível por daemon de LLAP. Normalmente, gostaríamos que esse valor fosse o mais próximo possível do número de núcleos.

Para D14 v2, há 16 VCORES disponíveis. Contudo, nem todas as VCORES podem ser fornecidas. Os nós de trabalho também executam outros serviços como NodeManager, DataNode e Monitor de Métricas, que precisam de uma parte das VCORES disponíveis. Esse valor pode ser configurado até 75% do total de VCORES disponíveis nesse nó.

Para D14 v2, o valor recomendado é (0,75 X 16) = **12**

É recomendável reservar cerca de 6 GB do espaço de heap por executor. Ajuste o número de executores com base no tamanho do daemon de LLAP disponível e no número de VCORES disponíveis por nó.  

### <a name="hivellapiothreadpoolsize"></a>hive.llap.io.threadpool.size

Esse valor especifica o tamanho do pool de threads para executores. Como os executores são corrigidos conforme especificado, será igual ao número de executores por daemon de LLAP.

Para D14 v2, é recomendável definir esse valor como **12**.

Essa configuração não pode exceder o valor `yarn.nodemanager.resource.cpu-vcores`.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>hive.auto.convert.join.noconditionaltask.size

Verifique se você habilitou `hive.auto.convert.join.noconditionaltask` para que esse parâmetro entre em vigor. Essa configuração permite especificar o tamanho das tabelas que podem caber na memória para fazer a Junção de mapa. Se a soma do tamanho de n-1 do `tables/partitions` para a junção de n vias for menor que o valor configurado, a Junção de mapa é escolhida. O tamanho da memória do executor de LLAP deve ser usado para calcular o limite da conversão automática para a Junção de mapa.

Para D14 v2, é recomendável definir esse valor como **2048 MB**.

Recomendamos ajustar esse valor adequado à carga de trabalho, pois definir esse valor muito baixo pode não usar o recurso de conversão automática. Definir um valor muito alto pode resultar em pausas no GC, o que pode afetar adversamente o desempenho da consulta.

## <a name="next-steps"></a>Próximas etapas

* [Diretrizes de gateway](gateway-best-practices.md)
* [Desmistificar ajuste de memória do Apache Tez - passo a passo](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Tamanho da memória do mapa de junção para LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP - uma visão geral da arquitetura de uma página](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Aprofundamento do LLAP do Apache Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
