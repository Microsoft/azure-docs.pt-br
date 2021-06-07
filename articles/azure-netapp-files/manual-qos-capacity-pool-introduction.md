---
title: Pool de capacidade de QoS manual de Azure NetApp Files | Microsoft Docs
description: Fornece uma introdução ao pool de capacidade de QoS manual e faz referência a informações adicionais.
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 13acee8b21adf946192544afcea17b4a8d9b9ec9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581118"
---
# <a name="manual-qos-capacity-pool"></a>Pool de capacidades de QoS manual

Este artigo fornece uma introdução à funcionalidade de pool de capacidade de QoS (qualidade de serviço) manual.

## <a name="how-manual-qos-differs-from-auto-qos"></a>Como a QoS manual difere da QoS automática

O [tipo de QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) é um atributo de um pool de capacidade. O Azure NetApp Files fornece dois tipos de QoS de pools de capacidade – automático (padrão) e manual.  

Em um pool de capacidade de QoS *manual* , você pode atribuir a capacidade e a taxa de transferência para um volume de forma independente. Para obter os níveis de taxa de transferência mínimo e máximo, consulte [limites de recursos para Azure NetApp files](azure-netapp-files-resource-limits.md#resource-limits). A taxa de transferência total de todos os volumes criados com um pool de capacidade com QoS manual é limitada pela taxa de transferência total do pool. Esse valor é determinado pela combinação do tamanho do pool com a taxa de transferência no nível do serviço. 

Em um pool de capacidade de QoS *automático* , a taxa de transferência é atribuída automaticamente aos volumes no pool, proporcionalmente à cota de tamanho atribuída aos volumes.  

Consulte [hierarquia de armazenamento de considerações de Azure NetApp files](azure-netapp-files-understand-storage-hierarchy.md) e [desempenho para Azure NetApp files](azure-netapp-files-performance-considerations.md) para obter considerações sobre tipos de QoS.

## <a name="example-of-using-manual-qos"></a>Exemplo de uso de QoS manual

Quando você usa um pool de capacidade de QoS manual com, por exemplo, um sistema de SAP HANA, um banco de dados Oracle ou outras cargas de trabalho que exigem vários volumes, o pool de capacidade pode ser usado para criar esses volumes de aplicativos.  Cada volume pode fornecer o tamanho individual e a taxa de transferência para atender aos requisitos do aplicativo.  Consulte os [exemplos de limite de taxa de transferência de volumes em um pool de capacidade de QoS manual](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) para obter detalhes sobre os benefícios.  

## <a name="how-to-specify-the-manual-qos-type"></a>Como especificar o tipo de QoS manual

Ao [criar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md), você pode especificar para o pool de capacidade usar o tipo de QoS manual.  Você também pode [alterar um pool de capacidade existente](manage-manual-qos-capacity-pool.md#change-to-qos) para usar o tipo de QoS manual. 

Definir o tipo de capacidade como QoS manual é uma alteração permanente. Não é possível converter uma ferramenta de capacidade de tipo de QoS manual em um pool de capacidade de QoS automático. 

Usar o tipo de QoS manual requer que você [Registre o recurso](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar um pool de capacidade de QoS manual](manage-manual-qos-capacity-pool.md)
* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Hierarquia de armazenamento](azure-netapp-files-understand-storage-hierarchy.md) 
* [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Considerações de desempenho para o Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Modelo de custo para o Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Criar um volume NFS](azure-netapp-files-create-volumes.md)
* [Criar um volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Criar um volume de protocolo duplo](create-volumes-dual-protocol.md)
* [Métricas do Azure NetApp Files](azure-netapp-files-metrics.md)
* [Solucionar problemas de pools de capacidade](troubleshoot-capacity-pools.md)
