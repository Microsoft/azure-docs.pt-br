---
title: Sobre failover e failback no Azure Site Recovery
description: Saiba mais sobre failover e com falha no Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: b900655d6fdf1143d430ac842bfd84eb1dfdf34c
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070726"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Sobre failover/failback de recuperação de desastre local

Este artigo fornece uma visão geral do failover e do failback durante a recuperação de desastre de máquinas locais para o Azure com [Azure site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Estágios de recuperação

O failover e o failback no Site Recovery tem quatro estágios:

- **Estágio 1: fazer failover do local**: depois de configurar a replicação para o Azure para computadores locais, quando o site local ficar inativo, você falhará nessas máquinas no Azure. Após o failover, as VMs do Azure são criadas com base nos dados replicados.
- **Estágio 2: proteger novamente as VMs do Azure**: no Azure, você protege novamente as VMs do Azure para que elas comecem a replicar de volta para o site local. A VM local (se disponível) é desativada durante a nova proteção, para ajudar a garantir a consistência dos dados.
- **Estágio 3: fazer failover do Azure**: quando seu site local está sendo executado normalmente, você executa outro failover, desta vez para realizar failback de VMs do Azure para seu site local. Você pode fazer failback para o local original por meio do qual fez failover ou para um local alternativo.
- **Estágio 4: proteger novamente os computadores locais**: após o failback, habilite novamente a replicação dos computadores locais para o Azure.

## <a name="failover"></a>Failover

Você realiza um failover como parte de sua estratégia de BCDR (continuidade de negócios e recuperação de desastre).

- Como uma primeira etapa em sua estratégia de BCDR, você Replica seus computadores locais para o Azure em uma base contínua. Os usuários acessam cargas de trabalho e aplicativos em execução nos computadores de origem locais.
- Se a necessidade for necessária, por exemplo, se houver uma interrupção local, você falhará na replicação de máquinas no Azure. As VMs do Azure são criadas usando os dados replicados.
- Para a continuidade dos negócios, os usuários podem continuar acessando aplicativos nas VMs do Azure.

O failover é uma atividade de duas fases:

- **Failover**: o failover que cria e abre uma VM do Azure usando o ponto de recuperação selecionado.
- **Confirmar**: após o failover, você verifica a VM no Azure:
    - Em seguida, você pode confirmar o failover para o ponto de recuperação selecionado ou selecionar um ponto diferente para a confirmação.
    - Depois de confirmar o failover, o ponto de recuperação não poderá ser alterado.


## <a name="connect-to-azure-after-failover"></a>Conectar-se ao Azure após o failover

Para se conectar às VMs do Azure criadas após o failover usando RDP/SSH, há vários requisitos.

**Failover** | **Localização** | **Ações**
--- | --- | ---
**VM do Azure executando o Windows** | No computador local antes do failover | **Acesso pela Internet**: habilite o RDP. Verifique se as regras de TCP e UDP foram adicionadas para o **público** e se o RDP é permitido para todos os perfis em   >  **aplicativos permitidos** do firewall do Windows.<br/><br/> **Acesso via VPN site a site**: habilite o RDP no computador. Verifique se o RDP é permitido no **Firewall do Windows**  ->  **aplicativos e recursos permitidos** para redes de **domínio e privadas** .<br/><br/>  Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Verifique se não existem Windows atualizações pendentes na VM virtual quando disparar um failover. Windows Update pode iniciar durante o failover e você não poderá fazer logon na VM até que as atualizações sejam concluídas.
**VM do Azure executando o Windows** | Na VM do Azure após o failover |  [Adicione um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para a VM.<br/><br/> As regras do grupo de segurança de rede na VM com failover (e a sub-rede do Azure à qual ela está conectada) devem permitir conexões de entrada para a porta RDP.<br/><br/> Verifique o **Diagnóstico de inicialização** para examinar uma captura de tela da VM. Se você não conseguir se conectar, verifique se a VM está em execução e examine as [dicas de solução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM do Azure executando Linux** | No computador local antes do failover | Verifique se o serviço Secure Shell na VM está definido para iniciar automaticamente na inicialização do sistema.<br/><br/> Verifique se as regras de firewall permitem uma conexão SSH com ele.
**VM do Azure executando Linux** | Na VM do Azure após o failover | As regras de grupo de segurança de rede na VM com failover (e a sub-rede do Azure à qual ela está conectada) precisam permitir conexões de entrada para a porta SSH.<br/><br/> [Adicione um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para a VM.<br/><br/> Verifique os **Diagnósticos de inicialização** para obter uma captura de tela da VM.<br/><br/>

## <a name="types-of-failover"></a>Tipos de failover

Site Recovery fornece opções de failover diferentes.

**Failover** | **Detalhes** | **Recuperação** | **Fluxo de trabalho**
--- | --- | --- | ---
**Failover de Teste** | Usado para executar uma análise que valida sua estratégia de BCDR, sem nenhuma perda de dados ou tempo de inatividade.| Cria uma cópia da VM no Azure, sem impacto na replicação em andamento ou em seu ambiente de produção. | 1. Execute um failover de teste em uma única VM ou em várias VMs em um plano de recuperação.<br/><br/> 2. Selecione um ponto de recuperação a ser usado para o failover de teste.<br/><br/> 3. Selecione uma rede do Azure na qual a VM do Azure será localizada quando for criada após o failover. A rede é usada somente para o failover de teste.<br/><br/> 4. Verifique se a análise funcionou conforme o esperado. Site Recovery limpa automaticamente as VMs criadas no Azure durante a análise.
**Failover planejado – Hyper-V**  | Geralmente usado para tempo de inatividade planejado.<br/><br/> As VMs de origem são desligadas. Os dados mais recentes são sincronizados antes de iniciar o failover. | Nenhuma perda de dados para o fluxo de trabalho planejado. | 1. planejar uma janela de manutenção de tempo de inatividade e notificar os usuários.<br/><br/> 2. colocar aplicativos voltados para o usuário offline.<br/><br/> 3. Inicie um failover planejado com o último ponto de recuperação. O failover não será executado se o computador não estiver desligado ou se forem encontrados erros.<br/><br/> 4. após o failover, verifique se a VM do Azure de réplica está ativa no Azure.<br/><br/> 5. Confirme o failover para concluir. A ação confirmar exclui todos os pontos de recuperação.
**Failover – Hyper-V** | Normalmente, execute se houver uma interrupção não planejada ou se o site primário não estiver disponível.<br/><br/> Opcionalmente, desligue a VM e sincronize as alterações finais antes de iniciar o failover.  | Perda mínima de dados para aplicativos. | 1. Inicie seu plano do BCDR. <br/><br/> 2. Inicie um failover. Especifique se Site Recovery deve desligar a VM e sincronizar/replicar as alterações mais recentes antes de disparar o failover.<br/><br/> 3. você pode fazer failover para várias opções de ponto de recuperação, resumidas na tabela a seguir.<br/><br/> Se você não habilitar a opção para desligar a VM ou se Site Recovery não puder desligá-la, o último ponto de recuperação será usado.<br/>O failover é executado mesmo que a máquina não possa ser desligada.<br/><br/> 4. após o failover, você verificará se a VM do Azure de réplica está ativa no Azure.<br/> Se necessário, você pode selecionar um ponto de recuperação diferente na janela de retenção de 24 horas.<br/><br/> 5. Confirme o failover para concluir. A ação confirmar exclui todos os pontos de recuperação disponíveis.
**Failover-VMware** | Normalmente, execute se houver uma interrupção não planejada ou se o site primário não estiver disponível.<br/><br/> Opcionalmente, especifique que Site Recovery deve tentar disparar um desligamento da VM e sincronizar e replicar as alterações finais antes de iniciar o failover.  | Perda mínima de dados para aplicativos. | 1. Inicie seu plano do BCDR. <br/><br/> 2. Inicie um failover de Site Recovery. Especifique se Site Recovery deve tentar disparar o desligamento da VM e sincronizar antes de executar o failover.<br/> O failover é executado mesmo que as máquinas não possam ser desligadas.<br/><br/> 3. após o failover, verifique se a VM do Azure de réplica está ativa no Azure. <br/>Se necessário, você pode selecionar um ponto de recuperação diferente na janela de retenção de 72 horas.<br/><br/> 5. Confirme o failover para concluir. A ação confirmar exclui todos os pontos de recuperação.<br/> Para VMs do Windows, o Site Recovery desabilita as ferramentas do VMware durante o failover.

## <a name="failover-processing"></a>Processamento de failover

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. Você pode observar tempos de failover de teste mais longos para:

* VMs VMware executando uma versão do Serviço de Mobilidade anterior à 9.8.
* Servidores físicos.
* VMs VMware Linux.
* VMs Hyper-V protegidas como servidores físicos.
* VMs VMware que não têm o serviço DHCP habilitado.
* VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opções de ponto de recuperação

Durante o failover, você pode selecionar várias opções de ponto de recuperação.

**Opção** | **Detalhes**
--- | ---
**Mais recente (menor RPO)** | Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo. Primeiro, essa opção processa todos os dados enviados ao serviço do Site Recovery para criar um ponto de recuperação para cada VM antes do failover. Esse ponto de recuperação tem todos os dados replicados para o Site Recovery no momento do failover.
**Mais recente processado**  | Essa opção executa failover de VMs para o último ponto de recuperação processado pelo Site Recovery. Para ver o último ponto de recuperação de uma VM específica, verifique os **Pontos de Recuperação Mais Recentes** nas configurações da VM. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
**Consistente com o aplicativo mais recente** |  Essa opção executa failover de VMs para o ponto de recuperação consistente com o aplicativo mais recente processado pelo Site Recovery, se os pontos de recuperação consistentes com o aplicativo estiverem habilitados. Verifique o ponto de recuperação mais recente nas configurações da VM.
**Várias VMs processadas mais recentemente** | Essa opção está disponível para planos de recuperação com uma ou mais VMs com consistência de várias VM habilitada. As VMs com a configuração habilitaram o failover para o último ponto de recuperação consistente com várias VMs em comum. Qualquer outra VM no plano faz failover para o último ponto de recuperação processado.
**Várias VMs mais recentes consistentes com o aplicativo** |  Essa opção está disponível para planos de recuperação com uma ou mais VMs com consistência de várias VM habilitada. As VMs que fazem parte de um failover do grupo de replicação para o ponto de recuperação comum mais recente de várias VMs consistente com aplicativo. Outras VMs fazem failover para seus últimos pontos de recuperação consistentes com aplicativo.
**Custom** | Use esta opção para fazer failover de uma VM específica para um determinado ponto de recuperação no tempo. Essa opção não está disponível para planos de recuperação.

> [!NOTE]
> Os pontos de recuperação não podem ser migrados para outro cofre dos serviços de recuperação.

## <a name="reprotectionfailback"></a>Nova proteção/failback

Após o failover para o Azure, as VMs do Azure replicadas estão em um estado desprotegido.

- Como uma primeira etapa para fazer failback para seu site local, você precisa iniciar as VMs do Azure replicando para o local. O processo de nova proteção depende do tipo de máquinas que você passou por failover.
- Depois que os computadores estiverem replicando do Azure para o local, você poderá executar um failover do Azure para seu site local.
- Depois que os computadores estiverem sendo executados no local novamente, você poderá habilitar a replicação para que eles sejam replicados no Azure para recuperação de desastre.

O failback funciona da seguinte maneira:

- Para fazer failback, uma VM precisa de pelo menos um ponto de recuperação para realizar o failback. Em um plano de recuperação, todas as VMs no plano precisam de pelo menos um ponto de recuperação.
- Recomendamos que você use o ponto de recuperação **mais recente** para realizar o failback (esse é um ponto consistente com falhas).
    - Há uma opção de ponto de recuperação consistente com o aplicativo. Nesse caso, uma única VM é recuperada para seu ponto de recuperação consistente com o aplicativo mais recente disponível. Para um plano de recuperação com um grupo de replicação, cada grupo de replicação é recuperado para seu ponto de recuperação disponível comum.
    - Os pontos de recuperação consistentes com o aplicativo podem ser atrasados no tempo, e pode haver perda de dados.
- Durante o failover do Azure para o site local, Site Recovery desliga as VMs do Azure. Quando você confirma o failover, Site Recovery remove as VMs do Azure com failback no Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/reproteção física/failback

Para proteger novamente e realizar failback de máquinas VMware e servidores físicos do Azure para o local, você precisa de uma infraestrutura de failback e há vários requisitos.

- **Servidor em processo temporário no Azure**: para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor em processo, a fim de manipular a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
- **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou ExpressRoute) da rede do Azure para o site local.
- **Servidor de destino mestre separado**: por padrão, o servidor de destino mestre que foi instalado com o servidor de configuração na VM do VMware local manipula o failback. Se for necessário fazer failback de grandes volumes de tráfego, configure um servidor de destino mestre local separado para essa finalidade.
- **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. Essa política é criada automaticamente quando você cria uma política de replicação de local para o Azure.
    - Essa política é associada automaticamente ao servidor de configuração.
    - Não é possível editar essa política.
    - Valores de política: limite de RPO – 15 minutos; Retenção do ponto de recuperação-24 horas; Frequência de instantâneo consistente com o aplicativo – 60 minutos.

Saiba mais sobre o VMware/reproteção física e failback:
- [Examine](vmware-azure-reprotect.md#before-you-begin) os requisitos adicionais de nova proteção e failback.
- [Implantar](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) um servidor de processo no Azure.
- [Implante](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) um servidor de destino mestre separado.

Ao proteger novamente as VMs do Azure para o local, você pode especificar que deseja realizar o failback para a localização original ou para um local alternativo.

- **Recuperação de local original**: isso faz failback do Azure para o mesmo computador local de origem, se existir. Nesse cenário, somente as alterações são replicadas de volta para o local.
- **Recuperação de local alternativo**: se o computador local não existir, você poderá fazer failback do Azure para um local alternativo. Quando você protege novamente a VM do Azure para o local, a máquina local é criada. A replicação de dados completa ocorre do Azure para o local. -- [Examine](concepts-types-of-failback.md) os requisitos e as limitações do failback de local.



## <a name="hyper-v-reprotectionfailback"></a>Nova proteção/failback do Hyper-V

Para proteger novamente e fazer failback de VMs do Hyper-V do Azure para o local:

- Você só pode realizar failback de VMs do Hyper-V replicando usando uma conta de armazenamento. Não há suporte para failback de VMs do Hyper-V que replicam usando discos gerenciados.
- Os hosts do Hyper-V locais (ou System Center VMM, se usados) devem estar conectados ao Azure.
- Você executa um failback planejado do Azure para o local.
- Não é necessário configurar nenhum componente específico para o failback de VM do Hyper-V.
- Durante o failover planejado, você pode selecionar opções para sincronizar os dados antes do failback:
    - **Sincronizar dados antes do failover**: essa opção minimiza o tempo de inatividade para máquinas virtuais à medida que sincroniza computadores sem desligá-los.
        - Fase 1: tira um instantâneo da VM do Azure e o copia para o host Hyper-V local. O computador continua em execução no Azure.
        - Fase 2: desliga a VM do Azure para que nenhuma nova alteração ocorra lá. O conjunto final de alterações delta é transferido para o servidor local e a VM local é iniciada.
    - **Sincronizar dados somente durante o failover**: essa opção é mais rápida porque esperamos que a maior parte do disco tenha sido alterada e, portanto, não execute cálculos de soma de verificação. Ela executa um download do disco. Recomendamos que você use essa opção se a VM estiver em execução no Azure por um tempo (um mês ou mais) ou se a VM local tiver sido excluída.

[Saiba mais](hyper-v-azure-failback.md) sobre a nova proteção e o failback do Hyper-V.

Ao proteger novamente as VMs do Azure para o local, você pode especificar que deseja realizar o failback para a localização original ou para um local alternativo.

- **Recuperação de local original**: isso faz failback do Azure para o mesmo computador local de origem, se existir. Nesse cenário, você seleciona uma das opções de sincronização descritas no procedimento anterior.
- **Recuperação de local alternativo**: se o computador local não existir, você poderá fazer failback do Azure para um local alternativo. Quando você protege novamente a VM do Azure para o local, a máquina local é criada. Com essa opção, recomendamos que você selecione a opção para sincronizar os dados antes do failover
- [Examine](hyper-v-azure-failback.md) os requisitos e as limitações do failback de local.


Após o failback para o site local, você habilita a **replicação inversa** para iniciar a replicação da VM para o Azure, concluindo o ciclo.




## <a name="next-steps"></a>Próximas etapas
- Failover de [VMs do VMware específicas](vmware-azure-tutorial-failover-failback.md)
- Faça failover de [VMs do Hyper-V específicas](hyper-v-azure-failover-failback-tutorial.md).
- [Crie](site-recovery-create-recovery-plans.md) um plano de recuperação.
- Failover [de VMs em um plano de recuperação](site-recovery-failover.md).
- [Preparar para](vmware-azure-failback.md) Reproteção e failback do VMware.
- Failback [de VMs do Hyper-V](hyper-v-azure-failback.md).
