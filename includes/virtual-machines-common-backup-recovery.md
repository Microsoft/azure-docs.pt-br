---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 03/09/2018
ms.date: 04/16/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543696"
---
## <a name="azure-backup"></a>Serviço de Backup do Azure

Para fazer backup de máquinas virtuais do Azure executando cargas de trabalho de produção, use o Backup do Azure. O Backup do Azure dá suporte a backups consistentes com o aplicativo para VMs Windows e Linux. O Backup do Azure cria pontos de recuperação que são armazenados em cofres de recuperação com redundância geográfica. Ao restaurar de um ponto de recuperação, você pode restaurar a VM inteira ou apenas arquivos específicos. 

Para obter uma introdução simples e prática para Backup do Azure para máquinas virtuais do Azure, consulte a "Fazer backup do tutorial de máquinas virtuais do Azure" para [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Para saber mais sobre como o Backup do Azure funciona, veja [Planejar sua infraestrutura de backup de VM no Azure](../articles/backup/backup-azure-vms-introduction.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

O Azure Site Recovery protege suas VMs de um cenário principal de recuperação de desastre, quando uma região inteira sofre uma interrupção devido a um grande desastre natural ou a uma interrupção do serviço generalizada. É possível configurar o Azure Site Recovery para suas VMs, de modo que você possa recuperar o aplicativo com um único clique em questão de minutos. É possível replicar para uma região do Azure de sua escolha e não restrito a regiões emparelhadas. 

Você pode executar os exercícios de recuperação de desastres com failovers de teste sob demanda, sem afetar a cargas de trabalho de produção ou de replicação contínua. Crie planos de recuperação para fazer failover e failback de todo o aplicativo em execução em várias VMs. O recurso de plano de recuperação é integrado a runbooks de automação do Azure.

Você pode começar [replicando suas máquinas virtuais](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Instantâneos gerenciados 

Em ambientes de desenvolvimento e teste, os instantâneos fornecem uma opção simples e rápida para fazer backup de máquinas virtuais que usam discos gerenciados. Um instantâneo gerenciado é uma cópia completa somente leitura de um disco gerenciado. Os instantâneos existem independentemente do disco de origem e podem ser usados para criar novos discos gerenciados para a recriação de uma VM. Elas são cobradas com base na parte usada do disco. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GB e tamanho real de dados usados de 10 GB, o instantâneo será cobrado somente pelo tamanho dos dados usados de 10 GB.  

Para saber mais sobre a criação de instantâneos, veja:

* [Criar cópia de VHD armazenado como um Disco Gerenciado usando instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Criar cópia de VHD armazenado como um Disco Gerenciado usando instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

## <a name="next-steps"></a>Próximos passos
Você pode experimentar o Backup do Azure ao seguir o "tutorial Fazer backup de máquinas virtuais do Windows" para [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) ou [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).