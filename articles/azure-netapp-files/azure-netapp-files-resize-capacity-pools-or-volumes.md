---
title: Redimensionar o pool de capacidade ou um volume para o Azure NetApp Files | Microsoft Docs
description: Saiba como alterar o tamanho de um pool de capacidade ou um volume. Redimensionar o pool de capacidade altera a capacidade adquirida do Azure NetApp Files.
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
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594471"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionar um volume ou um pool de capacidade
Você pode alterar o tamanho de um pool de capacidade ou de um volume, conforme necessário. 

## <a name="resize-the-capacity-pool"></a>Redimensionar o pool de capacidade 

Você pode alterar o tamanho do pool de capacidade em incrementos de 1 TiB ou decrementas. No entanto, o tamanho do pool de capacidade não pode ser inferior a 4 TiB. Redimensionar o pool de capacidade altera a capacidade adquirida do Azure NetApp Files.

1. Na folha Gerenciar Conta do NetApp, clique no pool de capacidade que você deseja redimensionar. 
2. Clique com o botão direito do mouse no nome do pool de capacidade ou clique no ícone "..." no final da linha do pool de capacidade para exibir o menu de contexto. 
3. Use as opções de menu de contexto para redimensionar ou excluir o pool de capacidade.

## <a name="resize-a-volume"></a>Redimensionar um volume

Você pode alterar o tamanho de um volume conforme necessário. O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume.

1. Na folha Gerenciar Conta do NetApp, clique em **Volumes**. 
2. Clique com o botão direito do mouse no nome do volume que você deseja redimensionar ou clique no ícone "..." no final da linha do volume para exibir o menu de contexto.
3. Use as opções de menu de contexto para redimensionar ou excluir o volume.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Redimensionar um volume de destino de replicação entre regiões 

Em uma relação de [replicação entre regiões](cross-region-replication-introduction.md) , um volume de destino é redimensionado automaticamente com base no tamanho do volume de origem. Assim, você não precisa redimensionar o volume de destino separadamente. Esse comportamento de redimensionamento automático é aplicável quando os volumes estão em uma relação de replicação ativa ou quando o emparelhamento de replicação é interrompido com a [operação de ressincronização](cross-region-replication-manage-disaster-recovery.md#resync-replication). 

A tabela a seguir descreve o comportamento de redimensionamento do volume de destino com base no [estado do espelho](cross-region-replication-display-health-status.md):

|  Estado do espelho  | Comportamento de redimensionamento de volume de destino |
|-|-|
| *Espelhado* | Quando o volume de destino foi inicializado e está pronto para receber atualizações de espelhamento, o redimensionamento do volume de origem redimensiona automaticamente os volumes de destino. |
| *Ininterrupta* | Quando você redimensiona o volume de origem e o estado do espelho é *interrompido*, o volume de destino é redimensionado automaticamente com a [operação de ressincronização](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Não Inicializado* | Quando você redimensiona o volume de origem e o estado do espelho ainda não foi *inicializado*, o redimensionamento do volume de destino precisa ser feito manualmente. Como tal, é recomendável aguardar a conclusão da inicialização (ou seja, quando o estado do espelho se torna *espelhado*) para redimensionar o volume de origem. | 

> [!IMPORTANT]
> Verifique se você tem espaço suficiente nos pools de capacidade para os volumes de origem e de destino da replicação entre regiões. Quando você redimensiona o volume de origem, o volume de destino é redimensionado automaticamente. Mas se o pool de capacidade que hospeda o volume de destino não tiver espaço suficiente, o redimensionamento dos volumes de origem e de destino falhará.

## <a name="next-steps"></a>Próximas etapas

- [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- [Gerenciar um pool de capacidade de QoS manual](manage-manual-qos-capacity-pool.md)
- [Alterar dinamicamente o nível de serviço de um volume](dynamic-change-volume-service-level.md) 