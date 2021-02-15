---
title: Habilitar a replicação para um disco de VM do Azure adicionado no Azure Site Recovery
description: Este artigo descreve como habilitar a replicação para um disco adicionado a uma VM do Azure que está habilitada para recuperação de desastre com o Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75973809"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Habilitar a replicação para um disco adicionado a uma VM do Azure


Este artigo descreve como habilitar a replicação para discos de dados que são adicionados a uma VM do Azure que já está habilitada para recuperação de desastre em outra região do Azure, usando [Azure site Recovery](site-recovery-overview.md).

A habilitação da replicação para um disco adicionado a uma VM tem suporte para VMs do Azure com discos gerenciados.

Quando você adiciona um novo disco a uma VM do Azure que está replicando para outra região do Azure, ocorre o seguinte:

-   A integridade da replicação para a VM mostra um aviso e uma observação no portal informa que um ou mais discos estão disponíveis para proteção.
-   Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial do disco.
-   Se optar por não habilitar a replicação para o disco, você poderá optar por ignorar o aviso.

![Novo disco adicionado](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe que você já configurou a recuperação de desastre para a VM à qual você está adicionando o disco. Se você ainda não fez isso, siga o [tutorial de recuperação de desastre do Azure para o Azure](azure-to-azure-tutorial-enable-replication.md).

## <a name="enable-replication-for-an-added-disk"></a>Habilitar a replicação para um disco adicionado

Para habilitar a replicação para um disco adicionado, faça o seguinte:

1. No cofre > **itens replicados**, clique na VM para a qual você adicionou o disco.
2. Clique em **discos**e selecione o disco de dados para o qual você deseja habilitar a replicação (esses discos têm um status **não protegido** ).
3.  Em **detalhes do disco**, clique em **habilitar replicação**.

    ![Habilitar a replicação para disco adicionado](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Depois que o trabalho habilitar replicação é executado e a replicação inicial é concluída, o aviso de integridade da replicação para o problema do disco é removido.



## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
