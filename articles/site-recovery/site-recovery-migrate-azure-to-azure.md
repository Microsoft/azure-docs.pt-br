---
title: "Migrar VMs IaaS do Azure entre regiões do Azure | Microsoft Docs"
description: "Use o Azure Site Recovery para migrar máquinas virtuais IaaS do Azure de uma região do Azure para outra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 86806c5dbafc1fd88c434dcee6292683d050cd2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Azure Site Recovery
## <a name="overview"></a>Visão geral
Bem-vindo ao Azure Site Recovery! Use este artigo se você deseja migrar VMs do Azure entre regiões do Azure.
>[!NOTE]
>
> Para replicar VMs do Azure para outra região para necessidades de migração e de recuperação de desastre, consulte [este documento](site-recovery-azure-to-azure.md). Atualmente, a replicação do Site Recovery para máquinas virtuais do Azure está em versão prévia.

Antes de começar, observe que:

* O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Azure Resource Manager e Clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação. As etapas básicas para migração são as mesmas se você estiver configurando o Site Recovery no Resource Manager ou no clássico. No entanto, as instruções de interface do usuário e capturas de tela neste artigo são relevantes para o Portal do Azure.



Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos
Aqui está o que você precisa para essa implantação:

* **Máquinas virtuais IaaS**: as VMs que você deseja migrar. Você pode migrar essas VMs tratando-as como computadores físicos.

## <a name="deployment-steps"></a>Etapas de implantação.
Esta seção descreve as etapas de implantação no novo Portal do Azure.

1. [Crie um cofre](site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Habilite a replicação](site-recovery-azure-to-azure.md) para as VMs que você deseja migrar e escolha o Azure como a origem.
  >[!NOTE]
  >
  > No momento, não há suporte para replicação nativa de VMs do Azure usando Managed Disks. Você pode usar a opção "Físico para o Azure" [neste documento](site-recovery-vmware-to-azure.md) para migrar VMs com Managed Disks.
3. [Executar um failover](site-recovery-failover.md). Depois que a replicação inicial for concluída, você poderá executar um failover de uma região do Azure para outra. Se preferir, você pode criar um plano de recuperação e executar um failover para migrar várias máquinas virtuais entre regiões. [Saiba mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre outros cenários de replicação em [O que é o Azure Site Recovery?](site-recovery-overview.md)
