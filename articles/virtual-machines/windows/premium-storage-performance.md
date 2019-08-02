---
title: 'Armazenamento Premium do Azure: Design para desempenho em VMs do Windows | Microsoft Docs'
description: Crie aplicativos de alto desempenho usando o Armazenamento Premium do Azure. O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure.
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 61f84dc871b4d7261dcb57ab430ce6feee9a0c64
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658179"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho de disco é, na verdade, um gargalo de rede. Nessas situações, você deve otimizar seu [desempenho de rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se você estiver procurando para avaliar o desempenho de disco, consulte nosso artigo sobre [um disco de benchmark](disks-benchmarks.md).
>
> Se sua VM oferecer suporte a rede acelerada, verifique se ela está ativada. Se não estiver ativado, você poderá ativá-lo em VMs já implementadas nos [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) e [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se você estiver familiarizado com o armazenamento Premium, leia primeiro a [selecione um tipo de disco do Azure para VMs IaaS](disks-types.md) e [metas de desempenho e escalabilidade do armazenamento do Azure para contas de armazenamento](../../storage/common/storage-scalability-targets.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
