---
title: Executar um failover durante a recuperação de desastre com Azure Site Recovery
description: Como fazer failover de VMs/servidores físicos para o Azure com Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 6737f64773f91ede1631d42cd7f28c7d961c0454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368614"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Execute um failover do local para o Azure

Este artigo descreve como fazer failover de computadores locais para o Azure no [Azure site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Antes de começar

- [Saiba mais](failover-failback-overview.md) sobre o processo de failover na recuperação de desastre.
- Se você quiser fazer failover de vários computadores, [Aprenda](recovery-plan-overview.md) a reunir computadores em um plano de recuperação.
- Antes de fazer um failover completo, execute uma [análise de recuperação de desastre](site-recovery-test-failover-to-azure.md) para garantir que tudo esteja funcionando conforme o esperado.

## <a name="prepare-to-connect-after-failover"></a>Preparar para conectar após o failover

Para garantir que você possa se conectar às VMs do Azure criadas após o failover, aqui estão várias coisas que você precisa fazer no local antes do failover.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Preparar local para se conectar após o failover

Se você quiser se conectar a VMs do Azure usando o RDP/SSH após o failover, há várias coisas que você precisa fazer no local antes do failover.

**Depois do failover** | **Localidade** | **Ações**
--- | --- | ---
**VM do Azure executando o Windows** | Computador local antes do failover | Para acessar a VM do Azure pela Internet, habilite o RDP e fazer com que as regras de TCP e UDP sejam adicionadas para o **Público** e que o RDP foi permitido para todos os perfis no **Firewall do Windows** > **Aplicativos Permitidos**.<br/><br/> Para acessar a VM do Azure em uma conexão site a site, habilite o RDP no computador e verifique se o RDP é permitido no **Firewall do Windows**  ->  **aplicativos e recursos permitidos** para redes **privadas** e de domínio.<br/><br/> <br/><br/> Remova todas as rotas persistentes estáticas e o proxy WinHTTP. Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Verifique se não existem Windows atualizações pendentes na VM virtual quando disparar um failover. A atualização do Windows poderá iniciar quando o failover for feito, e você não poderá fazer logon na VM até que a atualização seja concluída.
**VM do Azure executando Linux** | Computador local antes do failover | Verifique se o serviço Secure Shell na VM está definido para iniciar automaticamente na inicialização do sistema.<br/><br/> Verifique se as regras de firewall permitem uma conexão SSH com ele.


## <a name="run-a-failover"></a>Executar um failover

Este procedimento descreve como executar um failover para um [plano de recuperação](site-recovery-create-recovery-plans.md). Se você quiser executar um failover para uma única VM, siga as instruções para uma [VM VMware](vmware-azure-tutorial-failover-failback.md), um [servidor físico](physical-to-azure-failover-failback.md)ou uma [VM Hyper-V](hyper-v-azure-failover-failback-tutorial.md).


Execute o failover do plano de recuperação da seguinte maneira:

1. No cofre site Recovery, selecione **planos de recuperação**  >  *recoveryplan_name*.
2. Clique em **Failover**.

    ![Captura de tela de Azure Site Recovery mostrando o painel ADRP com failover selecionado no menu mais.](./media/site-recovery-failover/Failover.png)

3. Em   >  **direção de failover** de failover, deixe o padrão se você estiver replicando para o Azure.
4. Em **failover**, selecione um **ponto de recuperação** para o qual fazer failover.

    - **Mais recente**: Use o ponto mais recente. Isso processa todos os dados que foram enviados para Site Recovery serviço e cria um ponto de recuperação para cada computador. Essa opção fornece o RPO mais baixo (objetivo de ponto de recuperação) porque a VM criada após o failover tem todos os dados que foram replicados para Site Recovery quando o failover foi disparado.
    Observe que, quando a região de origem fica inativa, não há mais processamento de log possível. Portanto, você terá de fazer failover para o último ponto de recuperação processado. Veja o próximo ponto para entender mais.
   - **Mais recente processado**: Use essa opção para fazer failover de VMs para o ponto de recuperação mais recente já processado pelo site Recovery. Você pode ver o ponto de recuperação processado mais recentemente nos **pontos de recuperação mais recentes** da VM. Essa opção fornece um RTO baixo, uma vez que nenhum tempo é gasto para processar os dados não processados
   - **Consistente com o aplicativo mais recente**: Use essa opção para fazer failover de VMs para o ponto de recuperação consistente do aplicativo mais recente que foi processado pelo site Recovery.
   - **Várias VMs processadas mais recentemente**: com essa opção, as VMs que fazem parte de um failover de grupo de replicação para o ponto de recuperação consistente de várias VMs comum mais recente. Outras máquinas virtuais fazem failover para o último ponto de recuperação processado. Essa opção é apenas para planos de recuperação que têm pelo menos uma VM com consistência de várias VMs habilitada.
   - **Várias VMs mais recentes consistentes** com o aplicativo: com essa opção, as VMs que fazem parte de um grupo de replicação fazem failover para o ponto de recuperação consistente mais recente do aplicativo de várias VMs comum. Outras máquinas virtuais fazem failover para seus últimos pontos de recuperação consistentes com aplicativo. Somente para planos de recuperação que têm pelo menos uma VM com consistência de várias VMs habilitada.
   - **Personalizado**: não disponível para planos de recuperação. Essa opção é apenas para failover de VMs individuais.

5. Selecione **desligar computador antes de iniciar o failover** se desejar site Recovery desligar as VMs de origem antes de iniciar o failover. O failover continuará mesmo o desligamento falhar.  

    > [!NOTE]
    > Se você fizer failover de VMs do Hyper-V, o desligamento tentará sincronizar e replicar os dados locais que ainda não foram enviados para o serviço, antes de disparar o failover. 

6. Siga o progresso do failover na página **trabalhos** . Mesmo se ocorrerem erros, o plano de recuperação será executado até que seja concluído.
7. Após o failover, entre na VM para validá-la. 
8. Se você quiser alternar para o ponto de recuperação diferente a ser usado para o failover, use **alterar ponto de recuperação**.
9. Quando estiver pronto, você poderá confirmar o failover. A ação **confirmar** exclui todos os pontos de recuperação disponíveis com o serviço. A opção **alterar ponto de recuperação** não estará mais disponível.

## <a name="run-a-planned-failover-hyper-v"></a>Executar um failover planejado (Hyper-V)

Você pode executar um failover planejado para VMs do Hyper-V.

- Um failover planejado é uma opção de failover de perda de dados zero.
- Quando um failover planejado é acionado, primeiro as máquinas virtuais de origem são desligadas, depois os dados mais recentes são sincronizados e, em seguida, um failover é acionado.
- Você executa um failover planejado usando a opção de **failover planejado** . Ele é executado de forma semelhante a um failover regular.
 
## <a name="track-failovers"></a>Acompanhar failovers

Há vários trabalhos associados ao failover.

![Captura de tela da página trabalhos mostrando uma lista de trabalhos com o grupo 1: Iniciar (1) expandido na coluna nome. A linha para o trabalho do SQLServer está realçada.](./media/site-recovery-failover/FailoverJob.png)

- **Verificação de pré-requisitos**: garante que todas as condições necessárias para o failover sejam atendidas.
- **Failover**: processa os dados para que uma VM do Azure possa ser criada a partir dele. Se você tiver escolhido o ponto de recuperação **mais recente** , um ponto de recuperação será criado a partir dos dados que foram enviados para o serviço.
- **Iniciar**: cria uma VM do Azure usando os dados processados na etapa anterior.

> [!WARNING]
> **Não cancelar um failover em andamento**: antes de o failover ser iniciado, a replicação s parou para a VM. Se você cancelar um trabalho em andamento, o failover será interrompido, mas a VM não começará a ser replicada. A replicação não pode ser iniciada novamente.


### <a name="extra-failover-time"></a>Tempo de failover extra

Em alguns casos, o failover de VM requer uma etapa intermediária que geralmente leva cerca de oito a 10 minutos para ser concluído. Estes são os computadores afetados por esta etapa/tempo adicional:

* Máquinas virtuais VMware que executam uma versão do serviço de mobilidade com mais de 9,8.
* Servidores físicos e VMs do Hyper-V protegidos como servidores físicos.
* VMs VMware Linux.
* VMs VMware nas quais esses drivers não estão presentes como drivers de inicialização:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* VMs do VMware que não têm o DHCP habilitado, independentemente de estarem usando endereços IP estáticos ou DHCP.


## <a name="automate-actions-during-failover"></a>Automatizar ações durante o failover

Talvez você queira automatizar ações durante o failover. Para fazer isso, você pode usar scripts ou runbooks de automação do Azure em planos de recuperação.

- [Saiba mais](site-recovery-create-recovery-plans.md) sobre como criar e personalizar planos de recuperação, incluindo a adição de scripts.
- [Saiba](site-recovery-runbook-automation.md) como adicionar Runbooks de automação do Azure aos planos de recuperação.


## <a name="configure-settings-after-failover"></a>Definir configurações após o failover

### <a name="retain-drive-letters-after-failover"></a>Reter as letras da unidade após o failover

Site Recovery lida com a retenção de letras de unidade. Se você estiver excluindo discos durante a replicação da VM, [examine um exemplo](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) de como isso funciona.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Preparar no Azure para se conectar após o failover

Se você quiser se conectar às VMs do Azure que são criadas após o failover usando RDP ou SSH, siga os requisitos resumidos na tabela.

**Failover** | **Localidade** | **Ações**
--- | --- | ---
**VM do Azure executando o Windows** | VM do Azure após o failover |  [Adicione um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para a VM.<br/><br/> As regras de grupo de segurança de rede na VM com failover e a sub-rede do Azure à qual ela está conectada precisam permitir conexões de entrada para a porta RDP.<br/><br/> Verifique o **Diagnóstico de inicialização** para examinar uma captura de tela da VM.<br/><br/> Se você não conseguir se conectar, verifique se a VM está em execução e examine essas [dicas de solução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM do Azure executando Linux** | VM do Azure após o failover | As regras de grupo de segurança de rede na VM com failover (e a sub-rede do Azure à qual ela está conectada) precisam permitir conexões de entrada para a porta SSH.<br/><br/> [Adicione um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para a VM.<br/><br/> Verifique os **Diagnósticos de inicialização** para obter uma captura de tela da VM.<br/><br/>

Siga as etapas descritas [aqui](site-recovery-failover-to-azure-troubleshoot.md) para solucionar quaisquer problemas de conectividade após o failover.

## <a name="set-up-ip-addressing"></a>Configurar endereçamento IP

- **Endereços IP internos**: para definir o endereço IP interno de uma VM do Azure após o failover, você tem algumas opções:
    - Manter o mesmo endereço IP: você pode usar o mesmo endereço IP na VM do Azure como aquele alocado para o computador local.
    - Usar endereço IP diferente: você pode usar um endereço IP diferente para a VM do Azure.
    - [Saiba mais](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) sobre como configurar endereços IP internos.
- **Endereços IP externos**: você pode reter endereços IP públicos no failover. As VMs do Azure criadas como parte do processo de failover devem ser atribuídos a um endereço IP público do Azure disponível na região do Azure. Você pode atribuir um endereço IP público manualmente ou automatizando o processo com um plano de recuperação. [Saiba mais](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Próximas etapas

Após o failover, você precisará proteger novamente para iniciar a replicação das VMs do Azure de volta para o site local. Depois que a replicação estiver em funcionamento, você poderá fazer failback no local quando estiver pronto.

- [Saiba mais](failover-failback-overview.md#reprotectionfailback) sobre a nova proteção e o failback.
- [Prepare-se](vmware-azure-reprotect.md) para a nova proteção e failback do VMware.
- [Failback](hyper-v-azure-failback.md) VMs do Hyper-V.
- [Saiba mais sobre](physical-to-azure-failover-failback.md) o processo de failover e failback para servidores físicos.