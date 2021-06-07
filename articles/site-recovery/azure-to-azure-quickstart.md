---
title: Configurar a recuperação de desastre da VM do Azure em uma região secundária com o Azure Site Recovery
description: Configure rapidamente a recuperação de desastres em outra região do Azure para uma VM do Azure, usando o serviço Azure Site Recovery.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135702"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Início Rápido: Configurar a recuperação de desastre para uma região secundária do Azure para uma VM do Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade dos negócios e recuperação de desastres) mantendo seus aplicativos de negócios online durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este início rápido descreve como configurar a recuperação de desastre para uma VM do Azure replicando-a para uma região do Azure secundária. Em geral, as configurações padrão são usadas para habilitar a replicação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura do Azure e de uma VM.

- Caso não tenha uma conta do Azure com uma assinatura ativa, [crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- É recomendável uma VM com um mínimo de 1 GB de RAM. [Saiba mais](../virtual-machines/windows/quick-create-portal.md) sobre como criar uma VM.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Habilitar a replicação para a VM do Azure

As etapas a seguir habilitam a replicação de VM para um local secundário.

1. No portal do Azure, no menu **Iniciar** > **Máquinas Virtuais**, selecione uma VM para replicar.
1. Em **Operações**, clique em **Recuperação de desastre**.
1. Em **Noções básicas** > **Região de destino**, selecione a região de destino.
1. Para exibir as configurações de replicação, selecione **Examinar + iniciar replicação**. Se você precisar alterar os padrões, selecione **Configurações avançadas**.
1. Para iniciar o trabalho que habilita a replicação de VM, selecione **Iniciar a replicação**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Habilitar a replicação.":::

## <a name="verify-settings"></a>Verificar as configurações

Após o trabalho de replicação ser concluído, você poderá verificar o status de replicação, modificar as configurações de replicação e testar a implantação.

1. No menu do portal do Azure, selecione **Máquinas virtuais** e selecione a VM que você deseja replicar.
1. Em **Operações**, clique em **Recuperação de desastre**.
1. Para exibir os detalhes de replicação da **Visão geral**, selecione **Essentials**. Mais detalhes são mostrados em **Integridade e status**, **Preparação para failover** e o mapa de **Exibição de infraestrutura**.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Status de replicação.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Para interromper a replicação da VM na região primária, você deve desabilitar a replicação:

- As configurações de replicação de origem são limpas automaticamente.
- A extensão do Site Recovery instalada na VM durante a replicação não é removida.
- A cobrança do Site Recovery para a VM é interrompida.

Para desabilitar a replicação, siga estas etapas:

1. No menu do portal do Azure, selecione **Máquinas virtuais** e selecione a VM que você deseja replicar.
1. Em **Operações**, clique em **Recuperação de desastre**.
1. Em **Visão geral**, selecione **Desabilitar Replicação**.
1. Para desinstalar a extensão do Site Recovery, vá para **Configurações** > **Extensões** da VM.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Desabilitar a replicação.":::

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você replicou uma única VM para uma região secundária. Em seguida, configure a replicação para várias VMs do Azure.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-tutorial-enable-replication.md)
