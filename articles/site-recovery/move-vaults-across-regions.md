---
title: Mover um cofre do Azure Site Recovery para outra região
description: Descreve como mover o cofre dos Serviços de Recuperação e o Azure Site Recovery para outra região do Azure
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: a8cb5ec782b5932c13e321b2ba2d6513597fef52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87422633"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Mover o cofre dos Serviços de Recuperação e a configuração do Azure Site Recovery para outra região do Azure

Há vários cenários em que é possível mover seus recursos existentes do Azure de uma região para outra. Os exemplos são para fins de capacidade de gerenciamento e governança ou devido a fusões e aquisições de empresas. Um dos recursos relacionados que você pode querer mover quando você move as VMs do Azure é a configuração de recuperação de desastre. 

Não há nenhuma maneira não trabalhosa de mover uma configuração de recuperação de desastre existente de uma região para outra. Isso ocorre porque você configurou sua região de destino com base na região da VM de origem. Quando você decide alterar a região de origem, as configurações anteriormente existentes da região de destino não podem ser reutilizadas e devem ser redefinidas. Este artigo define o processo passo a passo para redefinir a configuração de recuperação de desastre e movê-la para uma região diferente.

Neste documento, você vai:

> [!div class="checklist"]
> * Verifique os pré-requisitos da movimentação.
> * Identifique os recursos que foram usados pelo Azure Site Recovery.
> * Desabilite a replicação.
> * Exclua os recursos.
> * Configure novamente o Site Recovery com base na nova região de origem das VMs.

> [!IMPORTANT]
> No momento, não há uma maneira não trabalhosa de mover um cofre dos Serviços de Recuperação e a configuração de recuperação de desastre no estado em que se encontram para uma região diferente. Este artigo explica o processo de desabilitar a replicação e configurá-la na nova região.

## <a name="prerequisites"></a>Pré-requisitos

- Não deixe de remover e excluir a configuração de recuperação de desastre antes de tentar mover as VMs do Azure para uma região diferente. 

  > [!NOTE]
  > Se a nova região de destino para a VM do Azure for igual à região de destino da recuperação de desastre, você poderá usar a configuração de replicação existente e movê-la. Sigas as etapas em [Mover uma VM IaaS do Azure para outra região do Azure](azure-to-azure-tutorial-migrate.md).

- Tome uma decisão informada e garanta que os stakeholders sejam comunicados. Sua VM não estará protegida contra desastres até a conclusão da sua movimentação.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identificar os recursos que foram usados pelo Azure Site Recovery
Recomendamos que você siga essa etapa antes de passar para a próxima. É mais fácil identificar os recursos relevantes enquanto as VMs estão sendo replicadas.

Para cada VM do Azure que está sendo replicada, acesse **Itens Protegidos** > **Itens Replicados** > **Propriedades** e identifique os recursos a seguir:

- Grupo de recursos de destino
- Conta de armazenamento em cache
- Conta de armazenamento de destino (no caso de uma VM do Azure baseada em disco não gerenciado) 
- Rede de destino


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Desabilitar a configuração de recuperação de desastre existente

1. Acesse o cofre dos Serviços de Recuperação.
2. Em **Itens Protegidos** > **Itens Replicados**, clique com o botão direito do mouse no computador e selecione **Desabilitar replicação**.
3. Repita esta etapa para todas as VMs que você deseja mover.

> [!NOTE]
> O serviço de mobilidade não será desinstalado dos servidores protegidos. É necessário desinstalá-lo manualmente. Se planeja proteger o servidor novamente, você pode ignorar a desinstalação do serviço de mobilidade.

## <a name="delete-the-resources"></a>Excluir os recursos

1. Acesse o cofre dos Serviços de Recuperação.
2. Selecione **Excluir**.
3. Exclua todos os outros recursos que você [identificou anteriormente](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Mover VMs do Azure para a nova região de destino

Siga as etapas nestes artigos com base em sua necessidade de mover VMs do Azure para a região de destino:

- [Mover as VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
- [Mover as VMs do Azure para Zonas de Disponibilidade](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Configure o Site Recovery com base na nova região de origem das VMs

Configure a recuperação de desastre para as VMs do Azure que foram movidas para a nova região seguindo as etapas em [Configurar recuperação de desastre para VMs do Azure](azure-to-azure-tutorial-enable-replication.md).
