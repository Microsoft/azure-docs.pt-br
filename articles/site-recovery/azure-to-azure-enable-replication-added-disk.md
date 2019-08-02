---
title: Habilitar a replicação para um disco adicionado a uma VM do Azure replicada pelo Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como habilitar a replicação para um disco adicionado à VM do Azure que está habilitado para recuperação de desastres com o Azure Site Recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: asgang
ms.openlocfilehash: 4a262a3a0c32516988890a6afc6eef34d8655c89
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671867"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Habilitar a replicação para um disco adicionado a uma VM do Azure


Este artigo descreve como habilitar a replicação para os discos de dados que são adicionados a uma VM do Azure que já está habilitado para recuperação de desastres em outra região do Azure, usando [Azure Site Recovery](site-recovery-overview.md).

Habilitar a replicação para um disco que você adiciona a uma VM tem suporte para VMs do Azure com discos gerenciados.

Quando você adiciona um novo disco a uma VM do Azure que está replicando para outra região do Azure, ocorre o seguinte:

-   Integridade da replicação da VM mostra um aviso e uma nota no portal informa que um ou mais discos estão disponíveis para proteção.
-   Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se você optar por não habilitar a replicação para o disco, você pode selecionar para ignorar o aviso.

![Novo disco adicionado](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe que você já tiver definido a recuperação de desastres para a VM à qual você está adicionando o disco. Se você ainda não tiver, siga as [tutorial de recuperação de desastre do Azure para](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Habilitar a replicação para um disco adicionado 

Para habilitar a replicação para o disco adicionado, faça o seguinte:

1. No cofre > **itens replicados**, clique na VM à qual você adicionou o disco.
2. Clique em **discos**e, em seguida, selecione o disco de dados para o qual você deseja habilitar a replicação (esses discos têm um **não protegido** status).
3.  Na **detalhes do disco**, clique em **habilitar a replicação**.

    ![Habilitar a replicação para o disco adicionado](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Depois que o trabalho de replicação de habilitação é executada e a replicação inicial terminar, o aviso de integridade de replicação para o problema de disco é removido.



# <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
