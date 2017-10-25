---
title: "Replicar máquinas virtuais do Azure entre regiões do Azure para necessidades de recuperação de desastres: do Azure para o Azure | Microsoft Docs"
description: "Resume as etapas que você precisa para replicar máquinas virtuais do Azure entre regiões do Azure (Azure para Azure) com o serviço do Azure Site Recovery para necessidades de recuperação de desastres."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Você pode replicar VMs do Azure entre regiões usando o Site Recovery

>[!NOTE]
>
> Replicação de recuperação de site para máquinas virtuais (VMs) do Azure está atualmente em visualização.

Este artigo descreve como replicar computadores físicos locais para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Publique perguntas e comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="disaster-recovery-in-azure"></a>Recuperação de desastre no Azure

Recursos internos de infraestrutura do Azure contribuem para uma estratégia de disponibilidade robusta e flexível para cargas de trabalho que são executados em máquinas virtuais do Azure. No entanto, há muitas razões por que você precisa para planejar a recuperação de desastres entre regiões do Azure:

- Você precisa atender às diretrizes de conformidade para cargas de trabalho que exigem uma estratégia de recuperação de desastres (BCDR) e continuidade dos negócios e aplicativos específicos.
- Você quer que a capacidade de proteger e recuperar máquinas virtuais do Azure seja com base em suas decisões de negócios e não apenas com base na funcionalidade do Azure embutido.
- Você precisa testar o failover e recuperação de acordo com suas necessidades de negócios e de conformidade, sem afetar a produção.
- Você precisa fazer failover para a região de recuperação em caso de desastre e failback diretamente para a região de origem original.

Use a recuperação de Site para replicação de VM do Azure para o Azure para ajudá-lo a fazer todas essas tarefas.


## <a name="why-use-site-recovery"></a>Por que usar a Recuperação de Site?      

Recuperação de site fornece uma maneira simples para replicar máquinas virtuais do Azure entre regiões:

- **Implantação automática**. Ao contrário de um modelo de replicação ativa, não há necessidade para uma infraestrutura cara e complexa na região secundária. Quando você habilitar a replicação, a recuperação de Site cria automaticamente os recursos necessários na região de destino, com base nas configurações de região de origem.
- **Controlar regiões**. Com a Recuperação de Site, você pode replicar de qualquer região para qualquer região dentro de um continente. Compare isso com o armazenamento com redundância geográfica com acesso de leitura, que replica de forma assíncrona entre apenas o padrão de [regiões emparelhadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Armazenamento com redundância geográfica com acesso de leitura fornece acesso somente de leitura aos dados na região de destino.
- **A replicação automatizada**. Recuperação de site fornece replicação contínua automatizada. Failover e failback podem ser disparados com um único clique.
- **RTO e RPO**. Recuperação de site aproveita a infraestrutura de rede do Azure que se conecta a regiões para manter o RTO e RPO muito baixos.
- **Testando**. Você pode executar os exercícios de recuperação de desastres com failovers de teste sob demanda, conforme a necessidade, sem afetar a cargas de trabalho de produção ou de replicação contínua.
- **Planos de recuperação**. Você pode usar planos de recuperação para fazer failover e failback de todo o aplicativo em execução em várias VMs. O recurso de plano de recuperação tem integração avançada de primeira classe com runbooks de automação do Azure.


## <a name="deployment-summary"></a>Resumo da implantação

Aqui está um resumo do que você precisa fazer para configurar a replicação de máquinas virtuais entre regiões do Azure:

1. Crie um cofre dos Serviços de Recuperação. O cofre contém definições de configuração e organiza a replicação.
2. Habilite a replicação para as VMs do Azure.
3. Execute um failover de teste para verificar se tudo está funcionando como esperado.

>[!IMPORTANT]
>
> Você pode verificar a [matriz de suporte para replicação de VM do Azure](./site-recovery-support-matrix-azure-to-azure.md).

>[!IMPORTANT]
>
> Para obter informações sobre como configurar a conectividade de saída de rede necessária para VMs do Azure para replicação de recuperação de Site, consulte o [documento de diretrizes de rede](./site-recovery-azure-to-azure-networking-guidance.md).

### <a name="before-you-start"></a>Antes de começar

* Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.
* Sua assinatura deve ser habilitada para criar VMs do Azure na região de destino que você planeja usar como a região de recuperação de desastres. Contate o suporte para habilitar a cota necessária.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> É recomendável que você crie o cofre de serviços de recuperação no local onde você deseja suas VMs para replicar. Por exemplo, se o local de destino é centro dos EUA, crie o cofre no **centro dos EUA**.

## <a name="enable-replication"></a>Habilitar a replicação

Em **Cofres dos Serviços de Recuperação**, selecione o cofre. No cofre, clique no botão **+ replicar**.

### <a name="step-1-configure-the-source"></a>Etapa 1. Configure a origem
1. Em **fonte**, selecione **Azure - VISUALIZAÇÃO**.
2. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
3. Especifique o modelo de implantação a ser usado: **Gerenciador de Recursos** ou **Clássico**.
4. Selecione o **Grupo de recursos de origem** para máquinas virtuais do Gerenciador de Recursos ou **serviço de nuvem** para VMs clássicas.

    ![Configure a origem](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>Etapa 2. Selecionar máquinas virtuais

* Selecione as máquinas virtuais que você deseja replicar e, em seguida, clique em **Ok**.

    ![Selecione as máquinas virtuais](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>Etapa 3. Configurar definições

1. Para substituir as configurações padrão de destino e especificar as configurações de sua escolha, clique em **Personalizar**. Para obter mais informações, consulte [Personalizar recursos de destino](site-recovery-replicate-azure-to-azure.md##customize-target-resources).

    ![Configurar definições](./media/site-recovery-azure-to-azure/settings.png)


2. Por padrão, a recuperação de Site cria uma política de replicação que usa instantâneos consistentes com o aplicativo a cada 4 horas e retém pontos de recuperação por 24 horas. Para criar uma política com configurações diferentes, clique em **Personalizar** lado a **Política de Replicação**.

    ![Personalizar política](./media/site-recovery-azure-to-azure/customize-policy.png)

3. Para iniciar o provisionamento de recursos de destino, clique em **Criar recursos de destino**. O provisionamento demora alguns minutos. Não feche a folha durante o provisionamento, ou você precisará começar novamente.

4. Para disparar a replicação da máquina virtual selecionada, clique em **Habilitar replicação**.

5. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**.

6. Em **Configurações** > **Itens Replicados**, você pode exibir o status das máquinas virtuais e o andamento da replicação inicial. Clique em VM para fazer uma busca detalhada em suas configurações.

## <a name="run-a-test-failover"></a>Execute um teste de failover

Depois de configurar tudo, execute um failover de teste para garantir que tudo esteja funcionando conforme o esperado:

1. Para fazer failover em um único computador, em **Configurações** > **Itens Replicados**, clique no ícone da VM **+Failover de Teste**.

2. Para fazer failover de um plano de recuperação, em **Configurações** > **Planos de Recuperação**, clique com o botão direito do mouse no plano **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md). 

3. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.

4. Para iniciar o failover, clique em **Ok**. Para acompanhar o progresso, clique na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de teste** no nome do cofre > **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**.

5. Após a conclusão do failover, você também deve conseguir ver a réplica do computador Azure exibida no Portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.

6. Para excluir as VMs que foram criadas durante o failover de teste, clique em **Failover de teste de limpeza** no item replicado ou no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste. 

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre failovers de teste.


## <a name="next-steps"></a>Próximas etapas

Depois de você testar a implantação:

- [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de failovers e como executá-los.
- Saiba mais sobre [usando planos de recuperação](site-recovery-create-recovery-plans.md) para reduzir o RTO.
