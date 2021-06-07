---
title: Fazer failback de VMs do Hyper-V do Azure com Azure Site Recovery
description: Como fazer failback de VMs do Hyper-V em um site local do Azure com Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: 96c3dce17ab78e08b28bb41c0100e51a72a666e7
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110246"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Executar um failback de VMs Hyper-V

Este artigo descreve como executar failback de VMs do Azure que foram criadas após o failover de VMs do Hyper-V de um site local para o Azure, com [Azure site Recovery](site-recovery-overview.md).

- Faça failback de VMs do Hyper-V do Azure executando um failover planejado do Azure para o site local. Se a direção do failover for do Azure para o local, será considerada um failback.
- Como o Azure é um ambiente altamente disponível e as VMs estão sempre disponíveis, o failback do Azure é uma atividade planejada. Você pode planejar um tempo de inatividade pequeno para que as cargas de trabalho possam começar a ser executadas no local novamente. 
- O failback planejado desativa as VMs no Azure e baixa as alterações mais recentes. Nenhuma perda de dados é esperada.

## <a name="before-you-start"></a>Antes de começar

1. [Examine os tipos de failback](failover-failback-overview.md#hyper-v-reprotectionfailback) você pode usar – recuperação de localização original e recuperação de localização alternativa.
2. Verifique se as VMs do Azure estão usando uma conta de armazenamento e não os discos gerenciados. O failback de máquinas virtuais Hyper-V, que realizaram failover em computadores do Azure usando discos gerenciados, não tem suporte.
3. Verifique se o host Hyper-V local (ou o servidor do System Center VMM, se você estiver usando o com Site Recovery) está em execução e conectado ao Azure. 
4. Verifique se o failover e a confirmação foram concluídos para as VMs. Você não precisa configurar nenhum componente Site Recovery específico para failback de VMs do Hyper-V do Azure.
5. O tempo necessário para concluir a sincronização de dados e iniciar a VM local dependerá de vários fatores. Para acelerar o download de dados, você pode configurar o agente de serviços de recuperação da Microsoft para usar mais threads para paralelizar o download. [Saiba mais](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Failback para o local original

Para fazer failback de VMs do Hyper-V no Azure para a VM local original, execute um failover planejado do Azure para o site local da seguinte maneira:

1. No cofre > **Itens replicados**, selecione a VM. Clique com o botão direito do mouse na VM > **failover planejado**. Se você estiver realizando o failback de um plano de recuperação, selecione o nome do plano **e clique em** failover  >  **planejado** de failover.
2. Em **confirmar failover planejado**, escolha os locais de origem e de destino. Observe a direção do failover. Se o failover do primário funcionou conforme o esperado e todas as máquinas virtuais estão no local secundário, isso é apenas para fins informativos.
3. Em **sincronização de dados**, selecione uma opção:
    - **Sincronizar dados antes do failover (sincronizar somente alterações delta)**– essa opção minimiza o tempo de inatividade para VMs à medida que ela é sincronizada sem desligá-las.
        - **Fase 1**: tira um instantâneo da VM do Azure e o copia para o host Hyper-V local. O computador continua em execução no Azure.
        - **Fase 2**: desliga a VM do Azure para que nenhuma nova alteração ocorra lá. O conjunto final de alterações delta é transferido para o servidor local e a VM local é iniciada.
    - **Sincronizar dados somente durante o failover (download completo)**— essa opção é mais rápida porque supomos que a maior parte do disco foi alterada e não quer gastar tempo calculando as somas de verificação. Essa opção não executará os cálculos de soma de verificação.
        - Ela executa um download do disco. 
        - Recomendamos que você use essa opção se estiver executando o Azure por um tempo (um mês ou mais) ou se a VM local for excluída.

4. Somente para o VMM, se a criptografia de dados estiver habilitada para a nuvem, em **chave de criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor do VMM.
5. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
6. Se você selecionou a opção para sincronizar os dados antes do failover, depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as máquinas virtuais no Azure, clique em **trabalhos** > nome do trabalho > **concluir failover**. Isso faz o seguinte:
    - Desliga o computador do Azure.
    - Transfere as alterações mais recentes para a VM local.
    - Inicia a VM local.
7. Agora você pode entrar no computador da VM local para verificar se ele está disponível conforme o esperado.
8. A máquina virtual está em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover.
9. Para concluir o failback, clique em **replicação inversa** para iniciar a replicação da VM local para o Azure novamente.



## <a name="fail-back-to-an-alternate-location"></a>Failback para um local alternativo 

Faça failback para um local alternativo da seguinte maneira:

1. Se você estiver configurando um novo hardware, instale uma [versão com suporte do Windows](hyper-v-azure-support-matrix.md#replicated-vms)e a função Hyper-V no computador.
2. Crie um comutador de rede virtual com o mesmo nome que você tinha no servidor original.
3. No grupo de proteção **itens protegidos**  >    >  \<ProtectionGroupName>  ->  \<VirtualMachineName> , selecione a VM que você deseja executar o failback e, em seguida, selecione **failover planejado**.
4. Em **confirmar s de failover planejado**, escolha **criar máquina virtual local se ela não existir**.
5. Em **nome do host**, selecione o novo servidor de host Hyper-V no qual você deseja posicionar a VM.
6. Em **sincronização de dados**, recomendamos que você selecione a opção para sincronizar os dados antes do failover. Isso minimiza o tempo de inatividade para VMs à medida que ela é sincronizada sem desligá-las. Ele faz o seguinte:
    - **Fase 1**: tira o instantâneo da VM do Azure e a copia para o host Hyper-V local. O computador continua em execução no Azure.
    - **Fase 2**: desliga a VM do Azure para que nenhuma nova alteração ocorra lá. O conjunto final de alterações é transferido para o servidor local e a máquina virtual local é inicializada.
    
7. Clique na marca de seleção para iniciar o failover (failback).
8. Depois que a sincronização inicial for concluída e você estiver pronto para desligar a VM do Azure, clique em **trabalhos**  >  \<planned failover job>  >  **concluir failover**. Isso desliga a máquina do Azure, transfere as alterações mais recentes para a VM local e a inicia.
9. Você pode entrar na VM local para verificar se tudo está funcionando conforme o esperado.
10. Clique em **confirmar** para concluir o failover. Commit exclui a VM do Azure e seus discos e prepara a VM local para ser protegida novamente.
10. Clique em **replicação inversa** para iniciar a replicação da VM local para o Azure. Somente as alterações delta desde que a VM foi desativada no Azure serão replicadas.

    > [!NOTE]
    > Se você cancelar o trabalho de failback durante a sincronização de dados, a VM local estará em um estado corrompido. Isso ocorre porque a sincronização de dados copia os dados mais recentes dos discos de VM do Azure para os discos de dados locais e, até que a sincronização seja concluída, os dados do disco podem não estar em um estado consistente. Se a VM local for iniciada após a sincronização de dados ser cancelada, ela poderá não ser inicializada. Nesse caso, execute novamente o failover para concluir a sincronização de dados.


## <a name="next-steps"></a>Próximas etapas
Depois que a VM local estiver replicando para o Azure, você poderá [executar outro failover](site-recovery-failover.md) para o Azure, conforme necessário.
