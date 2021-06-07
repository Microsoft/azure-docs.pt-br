---
title: Mover uma máquina virtual para outra região (Azure Site Recovery)
description: Saiba como você pode migrar sua máquina virtual do SQL Server de uma região para outra no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: migration
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 789554121af1c83d9077e6153ca9db01477bde25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97360145"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Mover uma VM SQL Server para outra região no Azure com Azure Site Recovery
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo ensina como usar o Azure Site Recovery para migrar sua VM (máquina virtual) do SQL Server de uma região para outra no Azure. 

Mover uma VM do SQL Server para uma região diferente requer o seguinte:
1. [Preparação](#prepare-to-move): Confirme se a VM do SQL Server de origem e a região de destino estão preparadas adequadamente para a movimentação. 
1. [Configuração](#configure-azure-site-recovery-vault): Mover a VM do SQL Server exige que ela seja um objeto replicado no cofre do Azure Site Recovery. Você precisa adicionar sua VM do SQL Server ao cofre do Azure Site Recovery. 
1. [Teste](#test-move-process): Migrar a VM SQL Server requer o failover da região de origem para a região de destino replicada. Para garantir que o processo de movimentação terá êxito, você precisa primeiro testar se o seu SQL Server VM pode fazer failover com êxito para a região de destino. Isso ajudará a expor quaisquer problemas e evitá-los ao executar a movimentação real. 
1. [Movimentação](#move-the-sql-server-vm): Depois que o failover de teste for aprovado, e você souber que é seguro migrar sua VM do SQL Server, você poderá executar a movimentação da VM para a região de destino. 
1. [Limpeza](#clean-up-source-resources): Para evitar encargos de cobrança, remova a VM do SQL Server do cofre e todos os recursos desnecessários que são deixados no grupo de recursos. 

## <a name="verify-prerequisites"></a>Verificar pré-requisitos 

- Confirme se há [suporte](../../../site-recovery/azure-to-azure-support-matrix.md#region-support) para a mudança da região de origem para a região de destino.  
- Revise a [arquitetura do cenário e os componentes](../../../site-recovery/azure-to-azure-architecture.md), bem como as [limitações e requisitos de suporte](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Verificar permissões da conta. Se você criou a conta gratuita do Azure, você será o administrador da assinatura. Se você não for o administrador da assinatura, trabalhe com o administrador para atribuir as permissões necessárias. Para habilitar a replicação em uma VM e copiar os dados usando o Azure Site Recovery, é necessário ter: 
    - Permissões para criar uma VM. A função interna *Colaborador da Máquina Virtual* tem essas permissões, que incluem: 
        - Permissões para criar uma VM no grupo de recursos selecionado. 
        - Permissões para criar uma VM na rede virtual selecionada. 
        - Permissões para gravar na conta de armazenamento selecionada. 
      - Permissões para gerenciar as operações do Azure Site Recovery. A função *Colaborador do Site Recovery* tem todas as permissões necessárias para gerenciar operações do Site Recovery em um cofre dos Serviços de Recuperação.  

## <a name="prepare-to-move"></a>Preparar para mover
Prepare a VM do SQL Server de origem e a região de destino para a movimentação. 

### <a name="prepare-the-source-sql-server-vm"></a>Prepare a VM do SQL Server de origem

- Verifique se todos os certificados raiz mais recentes estão na VM do SQL Server que você deseja mover. Se os certificados raiz mais recentes não estiverem lá, as restrições de segurança impedirão a cópia de dados na região de destino. 
- Para VMs Windows, instale todas as atualizações do Windows mais recentes na VM para que todos os certificados raiz confiáveis estejam no computador. Em um ambiente desconectado, siga o Windows Update padrão e o processo de atualização de certificado para sua organização. 
- Para VMs do Linux, siga as diretrizes fornecidas pelo distribuidor Linux para obter os certificados raiz confiáveis mais recentes e a lista de certificados revogados na VM. 
- Certifique-se de que você não esteja usando um proxy de autenticação para controlar a conectividade de rede das VMs que você quer mover. 
- Se a VM que você está tentando mover não tiver acesso à Internet ou estiver usando um proxy de firewall para controlar o acesso de saída, verifique os requisitos. 
- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Isso inclui, mas não se limita a, balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos. 

### <a name="prepare-the-target-region"></a>Preparar a região de destino

- Verifique se a assinatura do Azure permite criar VMs na região de destino utilizada para recuperação de desastre. Contate o suporte para habilitar a cota necessária. 
- Certifique-se de que a assinatura tenha recursos suficientes para dar suporte a VMs com tamanhos que correspondam às VMs de origem. Se você estiver usando o Site Recovery para copiar dados para o destino, o Site Recovery escolherá o mesmo tamanho ou o tamanho mais próximo possível para a VM de destino. 
- Certifique-se de criar um recurso de destino para cada componente identificado no layout de rede de origem. Essa etapa é importante para garantir que as VMs tenham toda a funcionalidade e recursos na região de destino que você tinha na região de origem. 
    - O Azure Site Recovery descobre e cria automaticamente uma rede virtual quando você habilita a replicação para a VM de origem. Você também pode pré-criar uma rede e atribuí-la à VM no fluxo do usuário para habilitar a replicação. Você precisa criar manualmente outros recursos na região de destino.
- Para criar os recursos de rede mais utilizados e relevantes com base na configuração da VM de origem, consulte a documentação a seguir: 
    - [Grupos de segurança de rede](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Balanceador de carga](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [Endereço IP público](../../../virtual-network/virtual-network-public-ip-address.md)
    - Para quaisquer componentes de rede adicionais, confira a [documentação da rede](../../../virtual-network/virtual-networks-overview.md).
- Crie manualmente uma rede de não produção na região de destino, caso você queira testar a configuração antes de executar a movimentação final para a região de destino. Essa etapa é recomendável porque garante interferência mínima na rede de produção. 

## <a name="configure-azure-site-recovery-vault"></a>Configurar o cofre do Azure Site Recovery

As etapas a seguir mostram como usar o Azure Site Recovery para copiar dados para a região de destino. Crie o cofre dos Serviços de Recuperação em qualquer região que não seja a região de origem. 

1. Entre no [portal do Azure](https://portal.azure.com). 
1. Escolha **Criar um recurso** no canto superior esquerdo do painel de navegação. 
1. Selecione **Ferramentas de TI e Gerenciamento** e, em seguida, selecione **Backup e Site Recovery**. 
1. Na guia **Noções Básicas**, em **Detalhes do Projeto**, crie um grupo de recursos na região de destino ou selecione um grupo de recursos existente na região de destino. 
1. Em **Detalhes da Instância**, especifique um nome para seu cofre e selecione sua **Região** de destino no menu suspenso. 
1. Selecione **Revisar + Criar** para criar seu cofre dos Serviços de Recuperação. 
1. Selecione **Todos os serviços** no canto superior esquerdo do painel de navegação e, na caixa de pesquisa, digite `recovery services`. 
1. (Opcionalmente) Selecione a estrela ao lado de **Cofres dos Serviços de Recuperação** para adicionar o recurso à sua barra de navegação rápida. 
1. Selecione **Cofres dos Serviços de Recuperação** e, em seguida, selecione o cofre dos Serviços de Recuperação que você criou. 
1. No painel **Visão Geral**, selecione **Replicar**. 

   ![Configurar replicação](./media/move-sql-vm-different-region/configure-replication.png)

1. Selecione **Origem** e, em seguida, selecione **Azure** como a origem. Selecione os valores apropriados para os outros campos suspensos, como o local das VMs de origem. Somente grupos de recursos localizados na região **Local de origem** serão visíveis no campo **Grupo de recursos de origem**. 
1. Selecione **Máquinas virtuais** e, em seguida, escolha a máquina virtual que você deseja migrar. Selecione **OK** para salvar sua seleção de VM. 
1. Selecione **Configurações** e escolha o **Local de destino** no menu suspenso. Esse deve ser o grupo de recursos que você preparou anteriormente. 
1. Depois de personalizar a replicação, selecione **Criar recursos de destino** para criar os recursos no novo local. 
1. Quando a criação do recurso estiver concluída, selecione **Habilitar replicação** para iniciar a replicação da sua VM do SQL Server da origem à região de destino.
1. Você pode verificar o status da replicação navegando até o cofre de recuperação, selecionando **Itens replicados** e visualizando o **Status** da sua VM do SQL Server. O status **Protegido** indica que a replicação foi concluída. 

   ![Verificar status de replicação](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Testar o processo de movimentação
As etapas a seguir mostram como usar o Azure Site Recovery para testar o processo de movimentação. 

1. Navegue para o **Cofre dos Serviços de Recuperação** no [portal do Azure](https://portal.azure.com) e selecione **Itens replicados**. 
1. Selecione a VM do SQL Server que você deseja mover, verifique se a **Integridade da Replicação** é exibida como **Íntegra** e selecione **Testar Failover**. 

   ![Testar failover para sua VM](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. Na página **Testar Failover**, selecione o ponto de recuperação **consistente com o aplicativo mais recente** a ser usado para o failover, pois esse é o único tipo de captura instantânea que pode garantir a consistência dos dados do SQL Server. 
1. Selecione a rede virtual em **Rede Virtual do Azure** e, em seguida, selecione **OK** para testar o failover. 
   
   >[!IMPORTANT]
   > É recomendável utilizar uma rede VM separada do Azure para o teste de failover. Não utilize a rede de produção que foi configurada quando você habilitou a replicação e que eventualmente poderá querer mover as VMs. 

1. Para monitorar o progresso, navegue até o seu cofre, selecione **Trabalhos do Site Recovery** em **Monitoramento** e, em seguida, selecione o trabalho **Testar Failover** em andamento.

   ![Monitorar o progresso do teste de failover](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. Quando o teste for concluído, navegue até **Máquinas virtuais** no portal e verifique a máquina virtual recém-criada. Certifique-se de que a VM do SQL Server esteja em execução, tenha o tamanho adequado e esteja conectada à rede apropriada. 
1. Exclua a VM que foi criada como parte do teste, pois a opção **Failover** ficará acinzentada até que os recursos do teste de failover sejam limpos. Volte para o cofre, selecione **Itens replicados**, selecione a VM do SQL Server e, em seguida, selecione **Limpar failover de teste**. Registre e salve as observações associadas ao teste na seção **Notas** e marque a caixa de seleção ao lado de **Teste concluído. Exclua as máquinas virtuais do failover de teste**. Selecione **OK** para limpar os recursos após o teste. 

   ![limpar itens após o teste de failover](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Mover a VM do SQL Server 
As etapas a seguir mostram como mover a VM do SQL Server da região de origem para a região de destino. 

1. Navegue até o cofre dos **Serviços de Recuperação**, selecione **Itens Replicados**, selecione a VM e selecione **Failover**. 

   ![Iniciar failover](./media/move-sql-vm-different-region/initiate-failover.png)

1. Selecione o ponto de recuperação **consistente com o aplicativo mais recente** em **Ponto de Recuperação**. 
1. Marque a caixa de seleção ao lado de **desligar o computador antes de iniciar o failover**. O Site Recovery tentará desligar a VM de origem antes de disparar o failover. O failover continuará mesmo se o desligamento falhar. 
1. Selecione **OK** para iniciar o failover.
1. Você pode monitorar o processo de failover a partir da mesma página **Trabalhos do Site Recovery** que visualizou ao monitorar o teste de failover na seção anterior. 
1. Após a conclusão da trabalho, verifique se a VM do SQL Server aparece na região de destino conforme o esperado. 
1. Volte para o cofre, selecione **Itens Replicados**, selecione a VM do SQL Server e selecione **Confirmar** para concluir o processo de movimentação para a região de destino. Aguarde até que o trabalho de confirmação seja concluído. 
1. Registre sua VM SQL Server com a extensão do agente IaaS do SQL para habilitar a gerenciabilidade da **máquina virtual do SQL** no portal do Azure e os recursos associados à extensão. Para obter mais informações, consulte [registrar SQL Server VM com a extensão do agente IaaS do SQL](sql-agent-extension-manually-register-single-vm.md). 

  > [!WARNING]
  > A consistência dos dados do SQL Server é garantida apenas com instantâneos consistentes com o aplicativo. O instantâneo **processado mais recente** não pode ser usado para failover do SQL Server, pois um instantâneo de recuperação de falha não pode garantir a consistência dos dados do SQL Server. 

## <a name="clean-up-source-resources"></a>Limpar recursos de origem
Para evitar encargos de cobrança, remova a VM do SQL Server do cofre e exclua todos os recursos associados desnecessários. 

1. Volte para o cofre do **Site Recovery**, selecione **Itens replicados** e selecione a VM do SQL Server. 
1. Selecione **Desabilitar Replicação**. Selecione um motivo para desativar a proteção e selecione **OK** para desabilitar a replicação. 

   >[!IMPORTANT]
   > É importante executar essa etapa para evitar ser cobrado pela replicação do Azure Site Recovery. 

1. Se você não planeja reutilizar nenhum dos recursos na região de origem, exclua todos os recursos de rede relevantes e as contas de armazenamento correspondentes. 


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server em um FAQ de VM do Windows](frequently-asked-questions-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](doc-changes-updates-release-notes.md)