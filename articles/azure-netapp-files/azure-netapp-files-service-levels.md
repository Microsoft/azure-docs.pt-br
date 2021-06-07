---
title: Níveis de serviço no Azure NetApp Files | Microsoft Docs
description: Descreve o desempenho de taxa de transferência para os níveis de serviço do Azure NetApp Files.
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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 7eac6a40476cffe875a03de49c9c9311ffbf4d39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017059"
---
# <a name="service-levels-for-azure-netapp-files"></a>Níveis de serviço do Azure NetApp Files
Os níveis de serviço são um atributo de um pool de capacidade. Os níveis de serviço são definidos e diferenciados pela taxa de transferência máxima permitida para um volume no pool de capacidade com base na cota atribuída ao volume.

## <a name="supported-service-levels"></a>Níveis de serviço com suporte

O Azure NetApp Files dá suporte a três níveis de serviço: *ultra*, *Premium* e *Standard*. 

* <a name="Ultra"></a>Ultra armazenamento

    A camada de armazenamento ultra fornece até 128 MiB/s de taxa de transferência por 1 TiB de capacidade provisionada. 

* <a name="Premium"></a>Armazenamento Premium

    A camada de armazenamento Premium fornece até 64 MiB/s de taxa de transferência por 1 TiB de capacidade provisionada. 

* <a name="Standard"></a>Armazenamento Standard

    A camada de armazenamento Standard fornece até 16 MiB/s de taxa de transferência por 1 TiB de capacidade provisionada.

## <a name="throughput-limits"></a>Limites de taxa de transferência

O limite de taxa de transferência para um volume é determinado pela combinação dos seguintes fatores:
* O nível de serviço do pool de capacidade ao qual o volume pertence
* A cota atribuída ao volume  
* O tipo de QoS (*automático* ou *manual*) do pool de capacidade  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Exemplos de limite de taxa de transferência de volumes em um pool de capacidade de QoS automático

O diagrama a seguir mostra exemplos de limite de taxa de transferência de volumes em um pool de capacidade de QoS automático:

![Ilustração de nível de serviço](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* No exemplo 1, um volume de um pool de capacidade de QoS automático com a camada de armazenamento Premium atribuído a 2 TiB de cota será atribuído a um limite de taxa de transferência de 128 MiB/s (2 TiB * 64 MiB/s). Esse cenário se aplica independentemente do tamanho do pool de capacidade ou do consumo real do volume.

* No exemplo 2, um volume de um pool de capacidade de QoS automático com a camada de armazenamento Premium atribuído 100 GiB de cota será atribuído a um limite de taxa de transferência de 6,25 MiB/s (0, 9765625 TiB * 64 MiB/s). Esse cenário se aplica independentemente do tamanho do pool de capacidade ou do consumo real do volume.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Exemplos de limite de taxa de transferência de volumes em um pool de capacidade de QoS manual 

Se você usar um pool de capacidade de QoS manual, poderá atribuir a capacidade e a taxa de transferência para um volume de forma independente. Ao criar um volume em um pool de capacidade de QoS manual, você pode especificar o valor da taxa de transferência (MiB/S). A taxa de transferência total atribuída a volumes em um pool de capacidade de QoS manual depende do tamanho do pool e do nível de serviço. Ele é limitado por (tamanho do pool de capacidade no nível de serviço TiB x taxa de transferência/TiB). Por exemplo, um pool de capacidade de 10 TiB com o nível de ultra serviço tem uma capacidade de taxa de transferência total de 1280 MiB/s (10 TiB x 128 MiB/s/TiB) disponíveis para os volumes.

Por exemplo, para um sistema de SAP HANA, esse pool de capacidade pode ser usado para criar os seguintes volumes. Cada volume fornece o tamanho individual e a taxa de transferência para atender aos requisitos do aplicativo:

* SAP HANA volume de dados: tamanho 4 TiB com até 704 MiB/s
* Volume de log do SAP HANA: tamanho de 0,5 TiB com até 256 MiB/s
* SAP HANA volume compartilhado: tamanho 1 TiB com até 64 MiB/s
* SAP HANA volume de backup: tamanho de 4,5 TiB com até 256 MiB/s

O diagrama a seguir ilustra os cenários para os volumes de SAP HANA:

![Cenários de volume de SAP HANA de QoS](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Próximas etapas

- [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Modelo de custo para o Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Criar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- [Contrato de Nível de Serviço (SLA) para Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Alterar dinamicamente o nível de serviço de um volume](dynamic-change-volume-service-level.md) 
- [Objetivos de nível de serviço para replicação entre regiões](cross-region-replication-introduction.md#service-level-objectives)
