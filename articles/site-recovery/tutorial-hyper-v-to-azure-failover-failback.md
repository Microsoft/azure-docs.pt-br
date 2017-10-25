---
title: Executar failover e failback de VMs do Hyper-V replicadas no Azure com o Site Recovery | Microsoft Docs
description: Saiba como executar failover de VMs do Hyper-V no Azure e failback no site local com o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Executar failover e failback em VMs do Hyper-V replicadas no Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) gerencia e orquestra a replicação, o failover e o failback de máquinas locais e de VMs (máquinas virtuais) do Azure.

Este tutorial descreve como executar failover de uma VM do Hyper-V no Azure. Depois de executar failover, execute failback para o site local quando ele estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Fazer failover de VMs do Hyper-V do local para o Azure
> * Executar failback do Azure para o local e iniciar a replicação para o Azure

## <a name="overview"></a>Visão geral
O failover e o failback têm duas fases:

1. **Failover para o Azure**: faz failover de computadores do site local para o Azure.
2. **Failback do Azure para o local**: executa um failover planejado do Azure para o local. Após o failover planejado, você deverá habilitar a replicação inversa para iniciar a replicação do local para o Azure novamente. 


## <a name="fail-over-to-azure"></a>Fazer failover para o Azure

### <a name="failover-prerequisites"></a>Pré-requisitos de failover

Para concluir o failover:

- Verifique se você concluiu uma [simulação de recuperação de desastre](tutorial-dr-drill-azure.md) para verificar se tudo está funcionando conforme o esperado.
- Como alternativa, prepare-se para se conectar às VMs do Azure antes de fazer failover.
- Verifique as propriedades da VM antes de executar o failover.

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Se você quiser se conectar às VMs do Azure usando o RDP após o failover, faça o seguinte:

##### <a name="azure-vms-running-windows"></a>VMs do Azure que executam o Windows

1. Para acessar as VMs do Azure pela Internet, habilite o RDP na VM local antes do failover. Verifique se as regras de TCP e de UDP foram adicionadas no perfil **Público** e se o RDP é permitido no **Firewall do Windows** > **Aplicativos permitidos** para todos os perfis.
2. Para acessar por meio da VPN site a site, habilite o RDP no computador local. O RDP deve ser permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para redes de **Domínio e Privadas**. Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135). Não deve haver nenhuma atualização pendente do Windows na VM quando você dispara um failover. Se houver, não será possível fazer logon na máquina virtual até que a atualização seja concluída. 
3. Na VM do Microsoft Azure após o failover, verifique o **Diagnóstico de inicialização** para exibir uma captura de tela da VM. Se você não puder se conectar, verifique se a VM está em execução e examine estas [dicas de solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


##### <a name="azure-vms-running-linux"></a>VMs do Azure executando o Linux

1. No computador local antes do failover, verifique se o serviço Secure Shell está definido para iniciar automaticamente na inicialização do sistema. Verifique se as regras de firewall permitem uma conexão SSH.
2. Na VM do Azure após o failover, permita as conexões de entrada com a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada.  [Adicione um endereço IP público](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) para a VM. Você pode verificar o **Diagnóstico de inicialização** para exibir uma captura de tela da VM.


#### <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Confira as propriedades da VM e verifique se a VM está em conformidade com os [Requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
3. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as [configurações de disco gerenciado](#managed-disk-considerations)
4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.
5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.


### <a name="run-a-failover-from-on-premises-to-azure"></a>Execute um failover do local para o Azure

Você pode executar um failover regular ou planejado nas VMs do Hyper-V

- Use um failover regular nas interrupções inesperadas. Quando você executa esse failover, o Site Recovery cria uma VM do Azure e a liga. Execute o failover em relação a um ponto de recuperação específico. Dependendo do ponto de recuperação usado, poderá ocorrer perda de dados.
- Um failover planejado pode ser usado para manutenção ou durante uma interrupção esperada. Essa opção não causa perda de dados. Quando um failover planejado é disparado, as VMs de origem são desligadas. Os dados não sincronizados são sincronizados e o failover é disparado.

Este procedimento descreve como executar um failover regular.


1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Você pode usar uma das seguintes opções:
    - **Último** (padrão): essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
    - **Últimos processados**: essa opção executa failover da VM para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
    - **Consistente com o aplicativo mais recente**: essa opção executa failover de todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery. 
    - **Personalizado**: especifica um ponto de recuperação.
3. Se estiver fazendo failover das VMs do Hyper-V nas nuvens do System Center VMM para o Azure a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor VMM.
4. Selecione **Desligue o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das máquinas virtuais de origem antes de disparar o failover. O Site Recovery também tenta sincronizar os dados locais que ainda não foram enviados para o Azure antes de acionar o failover. Observe que o failover continuará mesmo se o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
5. Se você preparou a conexão com a VM do Azure, conecte-se para validá-la após o failover.
6. Depois de verificar, **Confirme** o failover. Essa ação exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar um failover em andamento**: antes de iniciar o failover, a replicação da VM é interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.  


## <a name="fail-back-from-azure-to-on-premises"></a>Failback do Azure para o local

### <a name="prerequisites-for-failback"></a>Pré-requisitos para o failback

Para concluir o failback, verifique se o servidor do site primário do servidor VMM/servidor Hyper-V foi conectado ao Site Recovery.


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>Executar um failback e proteger novamente as VMs locais

Faça failover do Azure para o site local e inicie a replicação das VMs do site local para o Azure.

1. Em **Configurações** > **Itens Replicados**, clique na VM > **Failover Planejado**.
2. Em **Confirmar Failover Planejado**, verifique a direção do failover (do Azure) e selecione os locais de origem e de destino. 
3. Em **Sincronização de Dados**, especifique como deseja sincronizar:
    - **Sincronizar os dados antes do failover (sincronizar apenas alterações delta)**— Essa opção minimiza o tempo de inatividade da VM, pois os dados são sincronizados sem que a VM seja desligada. Aqui está o que ele faz:
        1. Tira um instantâneo da VM no Azure e o copia no host do Hyper-V local. A VM continua em execução no Azure.
        2. Desliga a VM do Azure, de modo que nenhuma nova alteração ocorrerá nela. O conjunto final de alterações delta é transferido para o servidor local e a VM local é inicializada.
    - **Sincronizar os dados apenas durante o failover (download completo)**: use essa opção se você estiver executando no Azure por um longo período. Esta opção é mais rápida, porque esperamos várias mudanças de disco e não desperdiçamos tempo em cálculos de soma de verificação. Essa opção executa um download de disco. Ela também é útil quando a VM local é excluída.
4. Se estiver fazendo failover das VMs do Hyper-V nas nuvens do System Center VMM para o Azure a criptografia de dados estiver habilitada para a nuvem, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor VMM.
5. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
6. Se você selecionou para sincronizar os dados antes do failover, depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as VMs no Azure, clique em **Trabalhos** > nome do trabalho de failover planejado > **Concluir Failover**. Isso desliga a VM do Azure, transfere as alterações mais recentes para o local e inicia a VM local.
7. Faça logon na VM local para verificar se ela está disponível conforme o esperado.
8. A VM local agora está em um estado de confirmação pendente. Clique em **Confirmar** para confirmar o failover. Isso exclui as VMs do Azure e seus discos e prepara a VM local para a replicação inversa.
9. Para iniciar a replicação da VM local para o Azure, habilite a **Replicação Inversa**.


> [!NOTE]
> A replicação inversa só replica as alterações ocorridas desde que a VM do Azure foi desativada e apenas as alterações delta são enviadas.

