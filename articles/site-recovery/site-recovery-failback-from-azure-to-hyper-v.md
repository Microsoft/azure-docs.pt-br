---
title: "Failback no Azure Site Recovery para máquinas virtuais Hyper-v | Microsoft Docs"
description: "O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failback do Azure para o datacenter local."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 7f478a61ee448d2d18b3ac7bc0a579b6e341c30d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="failback-in-site-recovery-for-hyper-v-virtual-machines"></a>Failback no Site Recovery para máquinas virtuais do Hyper-V

Este artigo descreve como executar o failback para máquinas virtuais protegidas pela Recuperação de Site.

## <a name="prerequisites"></a>Pré-requisitos
1. Verifique se o servidor VMM de site primário/servidor Hyper-V está conectado.
2. Você deve ter executado **Confirmar** na máquina virtual.

## <a name="why-is-there-no-button-called-failback"></a>Por que não há um botão chamado failback?
No portal, não há um gesto explícito chamado failback. O failback é uma etapa em que você volta para o site primário. Por definição, o failover é quando você executa o failover nas máquinas virtuais do site principal (local) para recuperação (Azure), e failback é quando você executa o failover nas máquinas virtuais de recuperação de volta para o principal.

Quando você inicia um failover, a folha informa sobre a direção do trabalho. Se a direção for do Azure para o local, será um failback.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Por que há apenas um gesto de failover planejado para failback?
O Azure é um ambiente altamente disponível, e as máquinas virtuais estarão sempre disponíveis. O failback é uma atividade planejada em que você decide ter um breve intervalo de inatividade, para que as cargas de trabalho possam começar a ser executadas no local novamente. Isso não tem expectativa de perda de dados. Portanto, só está disponível um gesto de failover planejado, que desativará as VMs no Azure, baixará as últimas alterações e garantirá que não haja perda de dados.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Preciso de um servidor de processo no Azure para realizar o failback para o Hyper-v?
Não, um servidor de processo só é necessário quando você está protegendo as máquinas virtuais VMware. Nenhum componente adicional precisa ser implantado ao proteger/realizar o failback de máquinas virtuais Hyper-v.

## <a name="initiate-failback"></a>Iniciar o failback
Depois do failover do local primário no secundário, as máquinas virtuais replicadas não são protegidas pela Recuperação de Site, e o local secundário agora atuará como local ativo. Siga estes procedimentos para fazer failback no site primário original. Este procedimento descreve como executar um failover planejado para um plano de recuperação. Como alternativa, você pode executar o failover de uma única máquina virtual na guia **Máquinas Virtuais** .

1. Selecione **Planos de Recuperação** > *recoveryplan_name*. Clique em **Failover** > **Planned Failover**.
2. Na página **Confirmar Failover Planejado**, escolha os locais de origem e de destino. Observe a direção do failover. Se o failover do local primário funcionar conforme esperado e todas as máquinas virtuais estiverem no local secundário, isso servirá apenas para fins informativos.
3. Se estiver fazendo failback do Azure, selecione as configurações em **Sincronização de Dados**:

   * **Sincronizar os dados antes do failover (sincronizar apenas alterações delta)**: essa opção minimiza o tempo de inatividade das máquinas virtuais, pois elas não são desligadas durante a sincronização. Ele faz o seguinte:
     * Fase 1: tira instantâneo da máquina virtual no Azure e o copia no host do Hyper-V local. O computador continua em execução no Azure.
     * Fase 2: Desliga a máquina virtual no Azure para que nenhuma alteração seja feita lá. O conjunto final de alterações delta é transferido para o servidor local, e a máquina virtual local é inicializada.

    - **Sincronizar os dados apenas durante o failover (download completo)**: use essa opção se você estiver executando no Azure por um longo período. Essa opção é mais rápida porque esperamos que a maior parte do disco tenha mudado e não queremos perder tempo no cálculo da soma de verificação. Ela executa um download do disco. Ela também é útil quando a máquina virtual local é excluída.

    >[!NOTE]
    >É recomendável usar essa opção se você já está executando o Azure há algum tempo (um mês ou mais) ou se a máquina virtual local foi excluída. Essa opção não executa cálculos de soma de verificação.


4. Se a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor VMM.
5. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
6. Se você selecionou a opção para sincronizar os dados antes do failover, depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as máquinas virtuais no Azure, clique em **Trabalhos** nome do trabalho de failover planejado **Concluir Failover**. Isso desliga a máquina do Azure, transfere as alterações mais recentes para a máquina virtual local e inicia a VM local.
7. Agora você pode fazer logon na máquina virtual para confirmar se ela está disponível conforme esperado.
8. A máquina virtual está em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover.
9. Agora, para concluir o failback, clique em **Replicação Inversa** para iniciar a proteção da máquina virtual no site primário.

## <a name="failback-to-an-alternate-location"></a>Failback em um local alternativo
Se você implantou a proteção entre um [site do Hyper-V e o Azure](site-recovery-hyper-v-site-to-azure.md) , será possível fazer failback do Azure em um site local alternativo. Isso é útil quando você precisa configurar novos hardwares locais. Veja como fazer isso.

1. Se você estiver definindo um novo hardware, instale o Windows Server 2012 R2 e a função do Hyper-V no servidor.
2. Crie um comutador de rede virtual com o mesmo nome que você tinha no servidor original.
3. Selecione **Itens Protegidos** -> **Grupo de Proteção** -> <ProtectionGroupName> -> <VirtualMachineName> do qual deseja realizar failback e selecione **Failover Planejado**.
4. Em **Confirmar Failover Planejado** select **Criar máquina virtual local se ela não existir**.
5. Em **Nome do Host** , selecione o novo servidor host do Hyper-V no qual deseja colocar a máquina virtual.
6. Em Sincronização de Dados, é recomendável selecionar a opção **Sincronizar os dados antes do failover**. Essa opção minimiza o tempo de inatividade das máquinas virtuais, uma vez que faz a sincronização sem desligá-las. Ele faz o seguinte:

   * Fase 1: tira instantâneo da máquina virtual no Azure e o copia no host do Hyper-V local. O computador continua em execução no Azure.
   * Fase 2: Desliga a máquina virtual no Azure para que nenhuma alteração seja feita lá. O conjunto final de alterações é transferido para o servidor local e a máquina virtual local é inicializada.
7. Clique na marca de seleção para iniciar o failover (failback).
8. Depois que sincronização inicial for finalizada e você estiver pronto para desligar a máquina virtual no Azure, clique em **Trabalhos** > <planned failover job> > **Concluir Failover**. Isso desliga a máquina do Azure, transfere as alterações mais recentes para a máquina virtual local e a inicia.
9. Você pode fazer logon na máquina virtual no local para verificar se tudo está funcionando conforme esperado. Em seguida, clique em **Confirmar** para concluir o failover.
10. Clique em **Replicação Inversa** para iniciar a proteção da máquina virtual local.

    > [!NOTE]
    > Se você cancelar o trabalho de failback durante a etapa de Sincronização de Dados, a VM local ficará em um estado corrompido. Isso ocorre porque a Sincronização de Dados copia os dados mais recentes dos discos da VM do Azure para os discos de dados locais e, até que a sincronização seja concluída, o disco de dados pode não estar em um estado consistente. Se a VM local for inicializada após o cancelamento da Sincronização de Dados, talvez ela não seja inicializada. Dispare novamente o failover para concluir a Sincronização de Dados.

## <a name="time-taken-to-failback"></a>Tempo necessário para o failback
O tempo necessário para concluir a sincronização de dados e iniciar a máquina virtual depende de vários fatores. Para ter uma ideia do tempo gasto, explicaremos o que acontece durante a sincronização de dados.

A sincronização de dados tira um instantâneo dos discos da máquina virtual e inicia a verificação bloco a bloco, depois calcula a soma de verificação. Essa soma de verificação calculada é enviada ao local para comparação com a soma de verificação local do mesmo bloco. Caso as somas de verificação correspondam, o bloco de dados não será transferido. Se não corresponderem, o bloco de dados será transferido para o local. Esse tempo de transferência depende da largura de banda disponível. A velocidade da soma de verificação é de alguns GBs por minuto. 

Para acelerar o download dos dados, configure o agente MARS para usar mais threads a fim de paralisar o download. Consulte [este documento](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) sobre como alterar os threads de download no agente.


## <a name="next-steps"></a>Próximas etapas

Depois de concluir o trabalho de failback, **Confirme** a máquina virtual. A confirmação exclui a máquina virtual do Azure e seus discos e prepara a VM para ser protegida novamente.

Depois de **Confirmar**, você poderá iniciar a *Replicação Inversa*. Isso iniciará a proteção da máquina virtual do local para o Azure. Observe que isso só replicará as alterações, pois a VM foi desativada no Azure e, assim, enviará somente alterações diferenciais.
