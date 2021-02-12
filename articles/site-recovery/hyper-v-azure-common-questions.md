---
title: Perguntas comuns sobre a recuperação de desastres do Hyper-V com o Azure Site Recovery
description: Este artigo resume perguntas comuns sobre a configuração da recuperação de desastre de VMs do Hyper-V locais para o Azure usando o site do Azure Site Recovery.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 649bd69f14cdf8d81fe05d3a5f5cac3389419fc3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879437"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Perguntas comuns - Recuperação de desastre do Hyper-V para o Azure

Este artigo fornece respostas a perguntas comuns que vemos ao replicar VMs do Hyper-V no local para o Azure. 

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é o Site Recovery é precificado?
Analise os detalhes em [Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>Como fazer para pagar as VMs do Azure?
Durante a replicação, os dados são replicados para o armazenamento do Azure e não é necessário pagar nenhuma alteração de VM. Ao executar um failover no Azure, o Site Recovery cria automaticamente as máquinas virtuais da IaaS do Azure. Depois disso, serão cobrados os recursos de computação consumidos no Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Há alguma diferença no custo ao replicar para Uso Geral conta de armazenamento v2?

Normalmente, você verá um aumento no custo das transações incorridos nas contas de armazenamento GPv2, uma vez que Azure Site Recovery tem transações pesadas. [Leia mais](../storage/common/storage-account-upgrade.md#pricing-and-billing) para estimar a alteração.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>O que preciso no Hyper-V para orquestrar a replicação com a Recuperação de Site?

Para o servidor de host do Hyper-V que você precisa depende do cenário de implantação. Verifique os pré-requisitos do Hyper-V em:

* [Replicando VMs Hyper-V no Azure (sem VMM)](./hyper-v-azure-tutorial.md)
* [Replicando VMs Hyper-V no Azure (com VMM)](./hyper-v-vmm-disaster-recovery.md)
* [Replicando VMs Hyper-V em um datacenter secundário](./hyper-v-vmm-disaster-recovery.md)
* Se você estiver replicando para um site secundário, leia sobre os [Sistemas operacionais convidados com suporte para as VMs Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/mt126277(v=ws.11)).
* Se estiver replicando no Azure, o Site Recovery dá suporte a todos os sistemas operacionais convidados [com suporte do Azure](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger VMs quando o Hyper-V está em execução em um sistema operacional cliente?
Não, as VMs devem estar localizadas em um servidor de host do Hyper-V sendo executado em uma máquina do servidor Windows com suporte. Se você precisar proteger um computador cliente, replique-o como um computador físico no [Azure](./vmware-azure-tutorial.md) ou em um [datacenter secundário](./vmware-physical-secondary-disaster-recovery.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hosts Hyper-V precisam estar em nuvens VMM?
Se você quiser replicar para um datacenter secundário, as VMs Hyper-V deverão estar nos servidores host do Hyper-V em uma nuvem VMM. Se você quiser replicar para o Azure, poderá replicar as VMs com ou sem nuvens do VMM. [Leia mais](./hyper-v-azure-tutorial.md) sobre replicação do Hyper-V para o Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Posso replicar máquinas virtuais Hyper-V de segunda geração no Azure?
Sim. O Site Recovery converte da segunda geração para a primeira geração durante o failover. No failback, a máquina é convertida novamente em segunda geração. [Leia mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Posso implantar o Site Recovery com VMM se eu tiver apenas um servidor VMM?

Sim. Você pode a replicar as VMs nos servidores do Hyper-V na nuvem do VMM para o Azure ou pode replicar entre as nuvens do VMM no mesmo servidor. Para a replicação local a local, recomendamos um servidor VMM nos sites primário e secundário. 

### <a name="what-do-i-need-in-azure"></a>O que é necessário no Azure?
É necessário uma assinatura do Azure, um cofre do Recovery Services, uma conta de armazenamento e uma rede virtual. O cofre, a conta de armazenamento e a rede devem estar na mesma região.

### <a name="what-azure-storage-account-do-i-need"></a>Qual conta de armazenamento do Azure é necessária?
Você precisa de uma conta de armazenamento LRS ou GRS. É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada. O armazenamento Premium fornece suportado.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?
Se você é um administrador da assinatura, tem as permissões de replicação necessárias. Caso contrário, precisará de permissões para criar uma VM do Azure no grupo de recursos e na rede virtual especificados durante a configuração do Site Recovery e as permissões para gravar na conta de armazenamento selecionada. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para o Site Recovery?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está sendo executado nas VMs. Os dados de replicação são trocados entre hosts do Hyper-V e o armazenamento do Azure. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  

O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Podemos manter os metadados locais em uma região geográfica?
Sim. Quando você cria um cofre em uma região, garantimos que todos os metadados utilizados pelo Site Recovery permanecem dentro do limite geográfico dessa região.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?
Sim, tanto criptografia em trânsito como [criptografia no Azure](../storage/common/storage-service-encryption.md) têm suporte.


## <a name="deployment"></a>Implantação

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>O que posso fazer com a replicação do Hyper-V para o Azure?

- **Recuperação de desastre**: é possível configurar a recuperação de desastre completa. Nesse cenário, você pode replicar VMs do Hyper-V locais no armazenamento do Azure:
    - Você pode replicar VMs do Azure. Se a sua infraestrutura local não estiver disponível, você fará o failover para o Azure.
    - Ao fazer failover, as VMs do Azure são criadas usando os dados replicados. Você pode acessar aplicativos e cargas de trabalho nas VMs do Azure.
    - Quando seu datacenter local estiver disponível novamente, você poderá fazer o failback do Azure para o site no local.
- **Migração**: você pode usar o Site Recovery para migrar VMs do Hyper-V no local para o armazenamento do Azure. Em seguida, você faz failover do local para o Azure. Após o failover, os aplicativos e as cargas de trabalho estarão disponíveis e em execução nas VMs do Azure.


### <a name="what-do-i-need-on-premises"></a>O que é necessário no local?

Você precisa de uma ou mais VMs em execução em um ou mais hosts Hyper-V independentes ou em cluster. Você também pode replicar as VMs em execução nos hosts gerenciados pelo System Center Virtual Machine Manager (VMM).
- Se você não estiver executando o VMM, durante a implantação do Site Recovery, você reunirá os hosts e clusters do Hyper-V nos sites do Hyper-V. Você instala os agentes do Site Recovery (provedor do Azure Site Recovery e do Recovery Services) em cada host do Hyper-V.
- Se os hosts do Hyper-V estiverem localizados em uma nuvem do VMM, você orquestra a replicação no VMM. Você instala o Provedor de Recuperação do Site no servidor do VMM e o agente dos Serviços de Recuperação em cada host do Hyper-V. Mapear entre redes lógicas/VM do VMM e redes virtuais do Azure.
- [Saiba mais](hyper-v-azure-architecture.md) sobre o Hyper-V para arquitetura do Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>É possível replicar as VMs localizadas em um cluster do Hyper-V?

Sim, o Site Recovery dá suporte a hosts do Hyper-V em cluster. Observe que:

- Todos os nós do cluster devem estar registrados no mesmo cofre.
- Se você não estiver usando o VMM, todos os hosts do Hyper-V no cluster deverão ser adicionados ao mesmo site do Hyper-V.
- Você instala o agente do Azure Site Recovery Provider e do Recovery Services em cada host do Hyper-V no cluster e adiciona cada host a um site do Hyper-V.
- Não é necessário realizar etapas específicas no cluster.
- Se você executar a ferramenta Planejador de Implantação para o Hyper-V, a ferramenta coletará os dados do perfil do nó que está em execução e onde a VM está sendo executada. A ferramenta não pode coletar nenhum dado de um nó que esteja desativado, mas rastreará esse nó. Depois que o nó estiver ativo e em execução, a ferramenta começará a coletar os dados do perfil da VM a partir dele (se a VM fizer parte da lista da VM do perfil e estiver em execução no nó).
- Se uma VM em um host do Hyper-V em uma área segura do Site Recovery migrar para um host Hyper-V diferente no mesmo cluster ou para um host autônomo, a replicação da VM não será afetada. O host Hyper-V deve atender aos [pré-requisitos](hyper-v-azure-support-matrix.md#on-premises-servers)e ser configurado em um cofre de recuperação de Site. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger VMs quando o Hyper-V está em execução em um sistema operacional cliente?
Não, as VMs devem estar localizadas em um servidor de host do Hyper-V sendo executado em uma máquina do servidor Windows com suporte. Se você precisar proteger um computador cliente, você poderia [replicá-la como uma máquina física](physical-azure-disaster-recovery.md) para o Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Posso replicar máquinas virtuais Hyper-V de segunda geração no Azure?
Sim. O Site Recovery converte da segunda geração para a primeira geração durante o failover. No failback, a máquina é convertida novamente em segunda geração.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar os cenários do Site Recovery com um SDK?
Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Cenários atualmente suportados para replicar o Hyper-V para o Azure usando o PowerShell:

- [Replique Hyper-V sem o VMM usando o PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replicando o Hyper-V com o VMM usando o PowerShell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replicação

### <a name="where-do-on-premises-vms-replicate-to"></a>Para onde as VMs locais são replicadas?
Os dados replicam para o armazenamento do Azure. Ao executar um failover, o Site Recovery cria automaticamente VMs do Azure a partir da conta de armazenamento.

### <a name="what-apps-can-i-replicate"></a>Quais aplicativos eu posso replicar?
Você pode replicar qualquer aplicativo ou carga de trabalho que esteja executando uma VM do Hyper-V que esteja em conformidade com os [requisitos de replicação](hyper-v-azure-support-matrix.md#replicated-vms). O Site Recovery fornece suporte para replicação com reconhecimento de aplicativo, de modo que os aplicativos possam fazer failover e failback para um estado inteligente. O Site Recovery integra-se a aplicativos da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Active Directory e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.

### <a name="whats-the-replication-process"></a>O que é o processo de replicação?

1. Quando a replicação inicial é acionada, um instantâneo de VM do Hyper-V é obtido.
2. Discos rígidos virtuais na VM são replicados individualmente até serem todos copiados para o Azure. Isso pode demorar um pouco, dependendo do tamanho da VM e largura de banda de rede. Saiba como aumentar a largura de banda de rede.
3. Se houver alterações no disco durante a replicação inicial, o Rastreador de Replicação de Réplica do Hyper-V mostrará essas alterações como logs de replicação do Hyper-V (.hrl). Esses arquivos de log estão localizados na mesma pasta que os discos. Cada disco tem um arquivo .hrl associado que é enviado ao armazenamento secundário. O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
4. Quando a replicação inicial termina, o instantâneo de VM é excluído.
5. Qualquer alteração de disco no log é sincronizada e mesclada ao disco pai.
6. Após a conclusão da replicação inicial, a proteção Finalizar no trabalho da máquina virtual é executada. Ele configura a rede e outras configurações pós-replicação, de modo que a VM fica protegida.
7. Neste estágio, você pode verificar as configurações da VM para certificar-se de que ela está pronta para failover. Você pode executar uma simulação de recuperação de desastre (failover de teste) para a VM, para verificar se ela faz failover conforme esperado.
8. Após a replicação inicial, a replicação delta se inicia, de acordo com a política de replicação.
9. As alterações são arquivos. hrl conectado. Cada disco configurado para a replicação tem um arquivo .hrl associado.
10. Esse log é enviado para a conta de armazenamento do cliente. Quando um log está em trânsito para o Azure, as alterações no disco primário são rastreadas em outro arquivo de log na mesma pasta.
11. Durante a replicação inicial e delta, você pode monitorar a VM no portal do Azure.

[Saiba mais](hyper-v-azure-architecture.md#replication-process) sobre o processo de replicação.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>É possível replicar para o Azure com uma VPN site a site?

Site Recovery replica dados do local para o armazenamento do Azure por meio de um ponto de extremidade público ou usando o emparelhamento da Microsoft do ExpressRoute. A replicação de uma rede VPN site a site não tem suportada.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>É possível replicar para o Azure com o ExpressRoute?

Sim, o ExpressRoute pode ser utilizado para replicar VMs para o Azure. Site Recovery replica dados para uma conta de armazenamento do Azure em um ponto de extremidade público e você precisa configurar [o emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) para site Recovery replicação. Após fazer failover das VMs para uma rede virtual do Azure, será possível acessá-las, utilizando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Por que não é possível replicar em VPN?

Quando você replica para o Azure, o tráfego de replicação atinge os pontos de extremidade públicos de uma conta de armazenamento do Azure. Assim, você só pode replicar pela Internet pública com o ExpressRoute (emparelhamento da Microsoft) e a VPN não funciona. 

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicadas?

Para replicação, uma VM do Hyper-V deve estar executando um sistema operacional suportado. Além disso, a VM deve atender aos requisitos das VMs do Azure. [Saiba mais](hyper-v-azure-support-matrix.md#replicated-vms) na matriz de suporte.

### <a name="why-is-an-additional-standard-storage-account-required-if-i-replicate-my-virtual-machine-disks-to-premium-storage"></a>Por que uma conta de armazenamento padrão adicional é necessária se eu replicar meus discos de máquina virtual para o armazenamento Premium?

Quando você Replica seus servidores físicos/máquinas virtuais locais para o armazenamento Premium, todos os dados que residem nos discos da máquina protegida são replicados para a conta de armazenamento Premium. Uma conta de armazenamento padrão adicional é necessária para armazenar os logs de replicação. Depois que a fase inicial de replicação dos dados do disco estiver concluída, todas as alterações nos dados do disco local serão rastreadas continuamente e armazenadas como logs de replicação nessa conta de armazenamento padrão adicional.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?

As VMs do Hyper-V podem ser replicadas a cada 30 segundos (exceto para armazenamento Premium) ou 5 minutos.

### <a name="can-azure-site-recovery-and-hyper-v-replica-be-configured-together-on-a-hyper-v-machine"></a>O Azure Site Recovery e a réplica do Hyper-V podem ser configurados juntos em uma máquina Hyper-V?

Sim, as réplicas Azure Site Recovery e Hyper-V podem ser configuradas em conjunto para um computador. Mas o computador precisará ser protegido como um computador físico e será replicado para o Azure usando um servidor de configuração/processo. Saiba mais sobre como proteger computadores físicos [aqui](./physical-azure-architecture.md).

### <a name="can-i-extend-replication"></a>É possível estender replicação?
Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>É possível fazer uma replicação inicial offline?
Não há suporte para isso. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>É possível excluir discos?
Sim, é possível excluir discos da replicação. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>É possível replicar VMs com discos dinâmicos?
Discos dinâmicos podem ser replicados. O disco do sistema operacional deve ser um disco básico.



## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Qual acesso o Site Recovery precisa para hosts Hyper-V

O Site Recovery precisa acessar os hosts do Hyper-V para replicar as VMs selecionadas. O Site Recovery instala o seguinte nos hosts do Hyper-V:

- Se você não estiver executando o VMM, o agente do Azure Site Recovery Provider e do Recovery Services será instalado em cada host.
- Se você estiver executando o VMM, o agente do Recovery Services será instalado em cada host. O provedor é executado no servidor do VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>O que a recuperação de Site instalar em VMs do Hyper-V?

O Site Recovery não instala explicitamente nada nas VMs do Hyper-V ativadas para replicação.




## <a name="failover-and-failback"></a>Failover e failback


### <a name="how-do-i-fail-over-to-azure"></a>Como fazer failover para o Azure?

Você pode executar um failover planejado ou não planejado de VMs Hyper-V locais para o Azure.

- Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
- Você pode executar um failover não planejado se o seu site primário não está acessível.
- Você pode fazer o failover de um único computador ou criar planos de recuperação para orquestrar o failover de várias máquinas virtuais.
- O failover está em duas partes:
    - Após a conclusão do primeiro estágio do failover, você deverá ser capaz de ver as VMs de réplica criadas no Azure. Você pode atribuir um endereço IP público à VM, se necessário.
    - Você confirma então o failover para começar a acessar a carga de trabalho por meio da VM do Azure de réplica.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como fazer para acessar VMs do Azure após o failover?
Após o failover, você poderá acessar as VMs do Azure por meio de uma conexão segura com a Internet, por meio de uma VPN site a site ou por meio do Microsoft Azure ExpressRoute. Você precisará preparar uma série de coisas para se conectar. [Saiba mais](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Failover de dados é resiliente?
O Azure foi desenvolvido para resiliência. O Site Recovery foi projetado para failover em um datacenter do Azure secundário, de acordo com o SLA do Azure. Quando ocorre um failover, garantimos que seus metadados e cofres permaneçam na mesma região geográfica escolhida para o cofre.

### <a name="is-failover-automatic"></a>O failover é automático?
O [failover](site-recovery-failover.md) não é automático. Você inicia failovers com um único clique no portal ou pode usar o [PowerShell](/powershell/module/az.recoveryservices) para disparar um failover.

### <a name="how-do-i-fail-back"></a>Como posso realizar o failback?

Depois que sua infraestrutura local estiver funcionando novamente, você poderá fazer failback. O failback ocorre em três estágios:

1. Você inicia um failover planejado do Azure para o site local usando algumas opções diferentes:

    - Minimize o tempo de inatividade: se você usar essa opção, o Site Recovery sincronizará os dados antes do failover. Ele verifica blocos de dados alterados e baixa-os para o site local, enquanto a VM do Azure continua em execução, minimizando o tempo de inatividade. Quando você especificar manualmente que o failover deve ser concluído, a VM do Azure será desligada, quaisquer eventuais alterações delta finais serão copiadas e o failover iniciará.
    - Download completo: Com essa opção, os dados são sincronizados durante o failover. Esta opção baixa todo o disco. Ela é mais rápida porque nenhuma soma de verificação é calculada, mas há mais tempo de inatividade. Use essa opção se você esteve executando as VMs de réplica do Azure por algum tempo ou se a VM local foi excluída.

2. Você pode selecionar para fazer failback para a mesma VM ou para uma outra VM. Você pode especificar que o Site Recovery deve criar a VM se ela ainda não existe.
3. Após a conclusão da sincronização inicial, você seleciona para concluir o failover. Após a conclusão, você pode entrar na VM local para verificar se tudo está funcionando conforme o esperado. No Portal do Azure, você pode ver que as VMs do Azure foram interrompidas.
4. Você confirma o failover para concluir e começar a acessar a carga de trabalho da VM local novamente.
5. Após o failback das cargas de trabalho, você habilita a replicação inversa, de modo que as VMs locais são replicadas novamente para o Azure.

### <a name="can-i-fail-back-to-a-different-location"></a>É possível fazer failback para um local diferente?
Sim, se você fez failover no Azure poderá fazer failback para um local diferente, se o original não estiver disponível. [Saiba mais](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).