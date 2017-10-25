---
title: Replicar aplicativos (VMware para o Azure) | Microsoft Docs
description: "Este artigo descreve como configurar a replicação de máquinas virtuais que são executadas em uma região do Azure para outra."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 5/22/2017
ms.author: asgang
ms.openlocfilehash: f9f97cf840b722c8cfee169dd1640e0682f287ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicação de máquinas virtuais do Azure para outra região do Azure



>[!NOTE]
>
> Replicação de recuperação de site para máquinas virtuais do Azure está atualmente em visualização.

Este artigo descreve como configurar a replicação de máquinas virtuais que são executadas em uma região do Azure para outra.

## <a name="prerequisites"></a>Pré-requisitos

* O artigo supõe que você já sabe sobre o Cofre de serviços de recuperação e recuperação de Site. Você precisa ter uma versão anterior de 'Cofre de serviços de recuperação' criada.

    >[!NOTE]
    >
    > É recomendável que você crie o cofre de serviços de recuperação no local onde você deseja suas VMs para replicar. Por exemplo, se o local de destino é ‘Centro dos EUA’, crie o cofre no ‘Centro dos EUA’.

* Se você estiver usando Regras de Grupos de Segurança de Rede (NSG) ou proxy de firewall para controlar o acesso à conectividade de internet de saída nas VMs do Azure, certifique-se de que você permita os URLs ou o IPs necessários. Consulte o [Documento de diretrizes de rede](./site-recovery-azure-to-azure-networking-guidance.md) para mais detalhes.

* Se você tiver um ExpressRoute ou uma conexão VPN entre locais e o local de origem no Azure, siga o documento [Considerações de recuperação de Site do Azure para ExpressRoute/configuração de VPN local](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).

* Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.

* Sua assinatura deve ser habilitada para criar VMs do Azure na região de destino que você planeja usar como a região de recuperação de desastres. Contate o suporte para habilitar a cota necessária.

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Habilite a replicação do cofre Azure Site Recovery
Nesta ilustração, replicaremos as VMs em execução em 'Ásia Oriental' do Azure local para o local de 'Sudeste da Ásia'. As etapas são as seguintes:

 Clique em **+ Replicar** no cofre para habilitar a replicação para as máquinas virtuais.

1. **Fonte:** refere-se para o ponto de origem das máquinas que nesse caso é **Azure**.

2. **Local de origem:** é a região do Azure de onde você deseja proteger suas máquinas virtuais. Para esta ilustração, o local de origem será 'Ásia Oriental'

3. **Modelo de implantação:** refere-se ao modelo de implantação do Azure de computadores de origem. Você pode selecionar qualquer clássico ou Gerenciador de recursos e computadores que pertençam a um modelo específico serão listados para a proteção na próxima etapa.

      >[!NOTE]
      >
      > Você pode apenas replicar a máquina virtual clássica e recuperá-la como uma máquina virtual clássica. Você não pode recuperá-la como uma máquina virtual de gerenciamento de recursos.

4. **Grupo de recursos:** é o grupo de recursos ao qual pertencem as máquinas virtuais de origem. Todas as máquinas virtuais do grupo de recursos selecionado serão listadas para a proteção na próxima etapa.

    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

Em **Máquinas Virtuais > Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em OK.
    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


Na seção de Configurações, você pode configurar as propriedades do site de destino

1. **Local de destino:** Esse é o local onde seus dados de máquina virtual de origem serão replicados. Dependendo de seu local de máquinas selecionadas, a recuperação de Site fornece a lista de regiões de destino adequado.

    > [!TIP]
    > É recomendável manter o local de destino igual a do Cofre de serviços de recuperação.

2. **Grupo de recursos de destino:** é o grupo de recursos ao qual todas as suas máquinas virtuais replicadas pertencerão. Por padrão, a ASR criará um novo grupo de recursos na região de destino com um nome com o sufixo "asr". No caso de grupo de recursos criado pelo ASR já existir, ela será reutilizada. Também é possível personalizá-lo, conforme mostrado na seção a seguir.    
3. **Rede Virtual de Destino:** Por padrão, a ASR criará um novo grupo de recursos na região de destino com um nome com o sufixo "asr". Isso será mapeado para sua rede de origem e será usado para qualquer proteção futura.

    > [!NOTE]
    > [Verifique os detalhes de rede](site-recovery-network-mapping-azure-to-azure.md) para saber mais sobre mapeamento de rede.

4. **Contas de Armazenamento de Destino:** Por padrão, o ASR criará a nova conta de armazenamento de destino imitando sua configuração de armazenamento de máquina virtual de origem. No caso de conta de armazenamento criada por ASR já existir, ela será reutilizada.

5. **Contas de armazenamento de cache:** ASR precisa de conta de armazenamento extra, chamada de armazenamento em cache da região de origem. Todas as alterações ocorrendo nas máquinas virtuais de origem são controladas e enviadas para a conta de armazenamento do cache antes de replicar para o local de destino.

6. **Conjunto de Disponibilidade:** Por padrão, a ASR criará um novo grupo de recursos na região de destino com um nome com o sufixo "asr". No caso de conjunto de disponibilidade criado pelo ASR já existir, ela será reutilizada.

7.  **Política de replicação:** define as configurações para histórico de retenção de ponto de recuperação e frequência de instantâneo consistente do aplicativo. Por padrão, o ASR criará uma nova política de replicação com configurações padrão de '24 horas’ para retenção de ponto de recuperação e '60 minutos’ para a frequência do instantâneo consistente de aplicativo.

    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personalizar os recursos de destino

No caso de você desejar alterar os padrões usados pela ASR, você pode alterar as configurações de acordo com suas necessidades.

1. **Personalizar:** Clique para alterar os padrões usados pela ASR.

2. **Grupo de recursos de destino:** Você pode selecionar o grupo de recursos da lista de todos os grupos de recursos existentes no local de destino dentro da assinatura.

3. **Rede Virtual de Destino:** Você pode encontrar a lista de todas as redes virtuais no local de destino.

4. **Conjunto de disponibilidade:** você só pode adicionar configurações de conjuntos de disponibilidade para as máquinas virtuais que fazem parte de disponibilidade na região de origem.

5. **Contas de Armazenamento de Destino:**

![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG) Clique em **Criar o recurso de destino** e Habilitar a replicação


Depois que as máquinas virtuais são protegidas, você pode verificar o status de integridade de VMs em **Itens replicados**

>[!NOTE]
>Durante o tempo de replicação inicial existe uma possibilidade de que o status demore para atualizar e você não veja nenhum progresso por algum tempo. Você pode clicar no botão Atualizar na parte superior da folha para obter o status mais recente.
>

![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
- [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de failovers e como executá-los.
- Saiba mais sobre [usando planos de recuperação](site-recovery-create-recovery-plans.md) para reduzir o RTO.
- Saiba mais sobre [proteger novamente as VMs do Azure](site-recovery-how-to-reprotect.md) após o failover.
