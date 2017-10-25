---
title: "Pré-requisitos para replicação no Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo resume os pré-requisitos para replicação de VMs e computadores físicos no Azure usando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Pré-requisitos para replicação de máquinas virtuais do Azure para outra região usando Azure Site Recovery

> [!div class="op_single_selector"]
> * [Replicar do Azure para o Azure](site-recovery-azure-to-azure-prereq.md)
> * [Replicar do local para o Azure](site-recovery-prereq.md)

O serviço Azure Site Recovery contribui para sua estratégia de recuperação de desastres e continuidade de negócios (BCDR) ao organizar:
* Replicação de máquinas virtuais do Azure para outra região do Azure.
* Replicação de máquinas virtuais e servidores físicos locais para o Azure ou para um datacenter secundário. 

Quando ocorrem paralisações em seu local primário, você pode fazer failover em um local secundário a fim de manter aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você pode realizar o failback. Para mais informações sobre o Site Recovery, consulte [O que é Site Recovery?](site-recovery-overview.md).

Este artigo resume os pré-requisitos necessários para começar a replicação do Site Recovery no Azure.

Poste comentários no final do artigo ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Requisitos do Azure

**Requisito** | **Detalhes**
--- | ---
**Conta do Azure** | Uma conta do [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Serviço do Site Recovery** | Para obter mais informações sobre os preços do Site Recovery, consulte [Preços do Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). É recomendável que você crie um cofre de serviços de recuperação na região de destino do Azure que você deseja usar como um local de recuperação de desastres. Por exemplo, se sua fonte de VMs está sendo executada no Leste dos EUA, e você deseja replicar para o centro dos EUA, recomendamos que você crie o cofre no centro dos EUA.|
**Capacidade do Azure** | Para o destino de uma região do Azure que você deseja usar como o local de recuperação de desastres, você precisa ter uma assinatura com capacidade suficiente para máquinas virtuais, contas de armazenamento e componentes de rede. Você pode contatar o suporte para aumentar a capacidade.
**Diretrizes de armazenamento** | Certifique-se de que você siga as [diretrizes de armazenamento](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) para as suas máquinas virtuais do Azure de origem a fim de evitar quaisquer problemas de desempenho. Se você seguir as configurações padrão, a recuperação de Site cria as contas de armazenamento necessárias com base na configuração de fonte. Se você personalizar e selecionar suas próprias configurações, certifique-se de que você siga os [destinos de escalabilidade para discos de máquina virtual](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Diretrizes de rede** | Você precisa permitir a conectividade de saída da sua VM do Azure para intervalos de IP ou URLs específicas. Para obter mais informações, consulte o artigo [Diretrizes de rede para replicação de máquinas virtuais do Azure](site-recovery-azure-to-azure-networking-guidance.md).
**VM do Azure** | Certifique-se de que todos os certificados raiz mais recentes estão presentes na VM do Linux ou Windows. Se os certificados raiz mais recentes não estiverem presentes, a VM não poderá ser registrada para a recuperação de Site devido a restrições de segurança.

>[!NOTE]
>Para saber mais sobre o suporte para configurações específicas, leia a [matriz de suporte](site-recovery-support-matrix-azure-to-azure.md).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [Diretrizes de rede para replicar máquinas virtuais do Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Inicie a proteção de suas cargas de trabalho ao [replicar máquinas virtuais do Azure](site-recovery-azure-to-azure.md).
