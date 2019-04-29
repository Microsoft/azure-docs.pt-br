---
title: Executar um failback durante desastres de VMs do Hyper-v do Azure para o local | Microsoft Docs
description: Saiba como executar failback de VMs do Hyper-V para um site local durante a recuperação de desastre para o Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 4030b1905f8d5b50ef6be3ffa61eda74d8a27951
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552409"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Executar um failback de VMs Hyper-V

Este artigo descreve como executar o failback para máquinas virtuais do Hyper-V protegidas pelo Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos
1. Certifique-se de que você tenha lido os detalhes sobre os [tipos diferentes de failback](concepts-types-of-failback.md) e as perdas correspondentes.
1. Verifique se o servidor VMM do site primário ou o servidor Hyper-V está conectado.
2. Você deve ter executado **Confirmar** na máquina virtual.

## <a name="perform-failback"></a>Executar um failback
Depois do failover do local primário no secundário, as máquinas virtuais replicadas não são protegidas pela Recuperação de Site, e o local secundário agora atuará como local ativo. Para fazer failback de VMs em um plano de recuperação, execute um failover planejado do site secundário para o primário como mostrado a seguir. 
1. Selecione **Planos de Recuperação** > *recoveryplan_name*. Clique em **Failover** > **Planned Failover**.
2. Na página **Confirmar Failover Planejado**, escolha os locais de origem e de destino. Observe a direção do failover. Se o failover do local primário funcionar conforme esperado e todas as máquinas virtuais estiverem no local secundário, isso servirá apenas para fins informativos.
3. Se estiver fazendo failback do Azure, selecione as configurações em **Sincronização de Dados**:
    - **Sincronizar os dados antes do failover (sincronizar apenas alterações delta)**: essa opção minimiza o tempo de inatividade das máquinas virtuais, pois elas não são desligadas durante a sincronização. Ela segue as seguintes etapas:
        - Fase 1: Tire instantâneo da máquina virtual no Azure e o copia no host do Hyper-V local. O computador continua em execução no Azure.
        - Fase 2: Desliga a máquina virtual no Azure para que nenhuma alteração seja feita lá. O conjunto final de alterações delta é transferido para o servidor local e a máquina virtual local é inicializada.

    - **Sincronizar os dados apenas durante o failover (download completo)** : esta opção é mais rápida.
        - Essa opção é mais rápida porque esperamos que a maior parte do disco tenha mudado e não queremos perder tempo no cálculo da soma de verificação. Ela executa um download do disco. Ela também é útil quando a máquina virtual local é excluída.
        - É recomendável usar essa opção se você já estiver executando o Azure há algum tempo (um mês ou mais) ou se a máquina virtual local foi excluída. Essa opção não executará os cálculos de soma de verificação.


4. Se a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor VMM.
5. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
6. Se você tiver selecionado a opção para sincronizar os dados antes do failover, depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as máquinas virtuais no Azure, clique em **Trabalhos** > nome do trabalho > **Concluir Failover**. Isso desliga a máquina do Azure, transfere as alterações mais recentes para a máquina virtual local e inicia a VM local.
7. Agora você pode fazer logon na máquina virtual para confirmar se ela está disponível conforme esperado.
8. A máquina virtual está em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover.
9. Para concluir o failback, clique em **Replicação Reversa** para iniciar a proteção da máquina virtual no site primário.


Siga estes procedimentos para fazer failback no site primário original. Este procedimento descreve como executar um failover planejado para um plano de recuperação. Como alternativa, você pode executar o failover de uma única máquina virtual na guia **Máquinas Virtuais** .


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Failback para um local alternativo no ambiente do Hyper-V
Se você implantou a proteção entre um [site do Hyper-V e o Azure](site-recovery-hyper-v-site-to-azure.md) , será possível fazer failback do Azure em um site local alternativo. Isso é útil quando você precisa configurar novos hardwares locais. Veja como fazer isso.

1. Se você estiver definindo um novo hardware, instale o Windows Server 2012 R2 e a função do Hyper-V no servidor.
2. Crie um comutador de rede virtual com o mesmo nome que você tinha no servidor original.
3. Selecione **itens protegidos** -> **grupo de proteção** -> \<Nomegrupoproteção > -> \<VirtualMachineName > você deseja realizar o failback, e selecione **Failover planejado**.
4. Em **Confirmar Failover Planejado** select **Criar máquina virtual local se ela não existir**.
5. Em Nome do Host,** selecione o novo servidor host do Hyper-V no qual deseja colocar a máquina virtual.
6. Em Sincronização de Dados, é recomendável selecionar a opção para sincronizar os dados antes do failover. Essa opção minimiza o tempo de inatividade das máquinas virtuais, uma vez que faz a sincronização sem desligá-las. Ele faz o seguinte:

    - Fase 1: Tire instantâneo da máquina virtual no Azure e o copia no host do Hyper-V local. O computador continua em execução no Azure.
    - Fase 2: Desliga a máquina virtual no Azure para que nenhuma alteração seja feita lá. O conjunto final de alterações é transferido para o servidor local e a máquina virtual local é inicializada.
    
7. Clique na marca de seleção para iniciar o failover (failback).
8. Depois que a sincronização inicial for concluída e você está pronto para desligar a máquina virtual no Azure, clique em **trabalhos** > \<trabalho de failover planejado >> **concluir Failover** . Isso desliga a máquina do Azure, transfere as alterações mais recentes para a máquina virtual local e a inicia.
9. Você pode fazer logon na máquina virtual local para verificar se tudo está funcionando conforme o esperado. Em seguida, clique em **Confirmar** para concluir o failover. A confirmação exclui a máquina virtual do Azure e seus discos e prepara a VM para ser protegida novamente.
10. Clique em **Replicação Inversa** para iniciar a proteção da máquina virtual local.

    > [!NOTE]
    > Se você cancelar o trabalho de failback durante a etapa de Sincronização de Dados, a VM local ficará em um estado corrompido. Isso ocorre porque a Sincronização de Dados copia os dados mais recentes dos discos da VM do Azure para os discos de dados locais e, até que a sincronização seja concluída, o disco de dados pode não estar em um estado consistente. Se a VM local for inicializada após o cancelamento da Sincronização de Dados, talvez ela não seja inicializada. Dispare novamente o failover para concluir a Sincronização de Dados.


## <a name="why-is-there-no-button-called-failback"></a>Por que não há um botão chamado failback?
No portal, não há um gesto explícito chamado failback. O failback é uma etapa em que você volta para o site primário. Por definição, o failback é quando você faz failover das máquinas virtuais da recuperação para primário.

Quando você inicia um failover, a folha informa a direção na qual as máquinas virtuais devem ser movidas, caso a direção seja do Azure para o local, é um failback.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Por que há apenas um gesto de failover planejado para failback?
O Azure é um ambiente altamente disponível, e as máquinas virtuais estão sempre disponíveis. O failback é uma atividade planejada em que você decide ter um breve intervalo de inatividade, para que as cargas de trabalho possam começar a ser executadas no local novamente. Isso não tem expectativa de perda de dados. Portanto, só está disponível um gesto de failover planejado, que desativará as VMs no Azure, baixará as últimas alterações e garantirá que não haja perda de dados.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Preciso de um servidor de processo no Azure para realizar o failback para o Hyper-v?
Não, um servidor de processo só é necessário quando você está protegendo as máquinas virtuais VMware. Nenhum componente adicional precisa ser implantado ao proteger/realizar o failback de máquinas virtuais Hyper-v.


## <a name="time-taken-to-failback"></a>Tempo necessário para o failback
O tempo necessário para concluir a sincronização de dados e iniciar a máquina virtual depende de vários fatores. Para ter uma ideia do tempo gasto, explicaremos o que acontece durante a sincronização de dados.

A sincronização de dados tira um instantâneo dos discos da máquina virtual e inicia a verificação bloco a bloco, depois calcula a soma de verificação. Essa soma de verificação calculada é enviada ao local para comparação com a soma de verificação local do mesmo bloco. Caso as somas de verificação correspondam, o bloco de dados não será transferido. Se não corresponderem, o bloco de dados será transferido para o local. Esse tempo de transferência depende da largura de banda disponível. A velocidade da soma de verificação é de alguns GBs por minuto. 

Para acelerar o download dos dados, configure o agente MARS para usar mais threads a fim de paralisar o download. Consulte [este documento](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) sobre como alterar os threads de download no agente.


## <a name="next-steps"></a>Próximas etapas

Depois de **Confirmar**, você poderá iniciar a *Replicação Inversa*. Isso inicia a proteção da máquina virtual desde local para o Azure. Isso só replicará as alterações, pois a VM foi desativada no Azure e, assim, enviará somente alterações diferenciais.
