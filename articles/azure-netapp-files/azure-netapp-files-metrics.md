---
title: Métricas do Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files fornece métricas sobre armazenamento alocado, uso de armazenamento real, IOPS de volume e latência. Use essas métricas para entender o uso e o desempenho.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: b-juche
ms.openlocfilehash: a17e6cc0479cf8ff2306736994a369d9e44dfdda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745937"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

Azure NetApp Files fornece métricas sobre armazenamento alocado, uso de armazenamento real, IOPS de volume e latência. Analisando essas métricas, você pode obter um melhor entendimento sobre o padrão de uso e o desempenho do volume de suas contas do NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Métricas de uso de pools de capacidade

- *Tamanho alocado do pool*   
    O tamanho provisionado do pool.

- *Pool alocado para o tamanho do volume*  
    O total de cotas de volume (GiB) em um determinado pool de capacidade (ou seja, o total dos tamanhos provisionados pelos volumes no pool de capacidade).  
    Esse tamanho é o tamanho que você selecionou durante a criação do volume.  

- *Tamanho consumido do pool*  
    O total de espaço lógico (GiB) usado em volumes em um pool de capacidade.  

- *Tamanho total do instantâneo do pool*    
    A soma do tamanho do instantâneo de todos os volumes no pool.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Métricas de uso de volumes

- *Percentual de tamanho consumido por volume*    
    O percentual do volume consumido, incluindo instantâneos.  
- *Tamanho alocado do volume*   
    O tamanho provisionado de um volume
- *Tamanho da cota de volume*    
    O tamanho da cota (GiB) em que o volume é provisionado.   
- *Tamanho consumido por volume*   
    Tamanho lógico do volume (bytes usados).  
    Esse tamanho inclui espaço lógico usado por sistemas de arquivos ativos e instantâneos.  
- *Tamanho do instantâneo de volume*   
   O tamanho de todos os instantâneos em um volume.  

## <a name="performance-metrics-for-volumes"></a>Métricas de desempenho para volumes

- *Latência média de leitura*   
    O tempo médio para leituras do volume em milissegundos.
- *Latência média de gravação*   
    O tempo médio para gravações do volume em milissegundos.
- *IOPS de leitura*   
    O número de leituras para o volume por segundo.
- *IOPS de gravação*   
    O número de gravações no volume por segundo.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Métricas de replicação de volume

> [!NOTE] 
> * O tamanho da transferência de rede (por exemplo, as métricas de *transferência total de replicação de volume* ) pode ser diferente dos volumes de origem ou de destino de uma replicação entre regiões. Esse comportamento é um resultado de um mecanismo de replicação eficiente ser usado para minimizar o custo de transferência de rede.
> * As métricas de replicação de volume estão atualmente populadas para volumes de destino de replicação e não a origem da relação de replicação.

- *O status de replicação do volume é íntegro*   
    A condição da relação de replicação. Um estado íntegro é indicado pelo `1` . Um estado não íntegro é indicado por `0` .

- *A replicação de volume está sendo transtransferência*    
    Se o status da replicação do volume é ' Transferindo '. 
 
- *Tempo de retardo da replicação do volume*   
    A quantidade de tempo em segundos pela qual os dados no espelho atrasam a origem. 

- *Duração da última transferência da replicação de volume*   
    A quantidade de tempo em segundos que levou para a última transferência ser concluída. 

- *Tamanho da última transferência da replicação de volume*    
    O número total de bytes transferidos como parte da última transferência. 

- *Progresso da replicação do volume*    
    A quantidade total de dados transferidos para a operação de transferência atual. 

- *Transferência total de replicação de volume*   
    Os bytes cumulativos transferidos para a relação. 

## <a name="next-steps"></a>Próximas etapas

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume para Azure NetApp Files](azure-netapp-files-create-volumes.md)
