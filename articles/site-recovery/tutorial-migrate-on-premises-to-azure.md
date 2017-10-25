---
title: "Migrar máquinas locais para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como migrar máquinas locais para o Azure, usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.openlocfilehash: bbee77e669f49bdebb57121df8672a9253945b3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas locais para o Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) gerencia e orquestra a replicação, o failover e o failback de máquinas locais e de VMs (máquinas virtuais) do Azure.

Este tutorial mostra como migrar VMs locais e servidores físicos para o Azure, com o Site Recovery. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar pré-requisitos para a implantação
> * Criar um cofre de Serviços de Recuperação para o Site Recovery
> * Implantar servidores de gerenciamento locais
> * Configurar uma política de replicação e habilitar a replicação
> * Executar uma simulação de recuperação de desastre para verificar se tudo está funcionando
> * Executar um failover avulso para o Azure

## <a name="overview"></a>Visão geral

Migre um computador habilitando a replicação para ela e executando seu failover para o Azure.


## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que você precisa fazer para este tutorial.

- [Preparar](tutorial-prepare-azure.md) recursos do Azure, incluindo uma assinatura do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- [Preparar](tutorial-prepare-on-premises-vmware.md) os servidores e as VMs VMware locais.
- Observe que não há suporte para dispositivos exportados por drivers paravirtualizados.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecionar uma meta de proteção

Selecione o que você deseja replicar e para onde deseja replicar.
1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
3. Em **Meta de proteção**, selecione:
    - **VMware**: selecione **Para o Azure** > **Sim, com o VMWare vSphere Hypervisor**.
    - **Computador físico**: selecione **Para o Azure** > **Não virtualizados/outros**.
    - **Hyper-V**: selecione **Para o Azure** > **Sim, com o Hyper-V**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

- [Configure](tutorial-vmware-to-azure.md#set-up-the-source-environment) o ambiente de origem das VMs VMware.
- [Configure](tutorial-physical-to-azure.md#set-up-the-source-environment) o ambiente de origem para os servidores físicos.
- [Configure](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) o ambiente de origem para VMs do Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação de destino.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

- [Configure uma política de replicação](tutorial-vmware-to-azure.md#create-a-replication-policy) para VMs VMware.


## <a name="enable-replication"></a>Habilitar a replicação

- [Habilite a replicação](tutorial-vmware-to-azure.md#enable-replication) para VMs VMware.


## <a name="run-a-test-migration"></a>Execute um teste de migração

Execute um [failover de teste](tutorial-dr-drill-azure.md) para o Azure, para verificar se tudo está funcionando conforme o esperado.


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute um failover para as máquinas que você deseja migrar.

1. Em **Configurações** > **Itens replicados** clique no computador > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Selecione o ponto de recuperação mais recente.
3. A configuração de chave de criptografia não é relevante para esse cenário.
4. Selecione **Desligue o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
5. Verifique se a VM do Azure aparece no Azure, conforme o esperado.
6. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Concluir Migração**. Isso conclui o processo de migração, interrompe a replicação da VM e interrompe a cobrança do Site Recovery para a VM.

    ![Migração completa](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Não cancelar um failover em andamento**: antes de iniciar o failover, a replicação da VM é interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. É possível observar um tempo de failover de teste mais longo para servidores físicos, máquinas VMware Linux, VMs VMware que não têm o serviço DHCP habilitado e VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Replicando VMs do Azure para outra região após a migração para o Azure](site-recovery-azure-to-azure-after-migration.md)
