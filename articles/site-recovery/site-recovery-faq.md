---
title: 'Azure Site Recovery: perguntas frequentes | Microsoft Docs'
description: "Este artigo aborda dúvidas comuns sobre o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/22/2017
ms.author: raynew
ms.openlocfilehash: 95e31d0ca5983e0946ad6fb993e7a89a6a63d2c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: perguntas frequentes
Este artigo contém perguntas frequentes sobre o Azure Site Recovery. Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Geral
### <a name="what-does-site-recovery-do"></a>O que faz o Site Recovery?
O Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando e automatizando a replicação de VMs do Azure entre regiões, máquinas virtuais locais e servidores físicos no Azure e máquinas locais em um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>O que o Site Recovery pode proteger?
* **VMs do Azure**: o Site Recovery pode replicar qualquer carga de trabalho em execução em uma VM do Azure com suporte
* **Máquinas virtuais do Hyper-V**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM Hyper-V.
* **Servidores físicos**: o Site Recovery pode proteger servidores físicos com o Windows ou o Linux em execução.
* **Máquinas virtuais VMware**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM VMware.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>O Recuperação de Site dá suporte ao modelo do Azure Resource Manager?
O Site Recovery está disponível no Portal do Azure com suporte para o Resource Manager. O Site Recovery oferece suporte a implantações herdadas no Portal Clássico do Azure. Você não pode criar novos cofres no Portal Clássico, e não há suporte para os novos recursos.

### <a name="can-i-replicate-azure-vms"></a>Posso replicar VMs do Azure?
Sim, você pode replicar VMs do Azure com suporte entre regiões do Azure. [Saiba mais](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>O que preciso no Hyper-V para orquestrar a replicação com a Recuperação de Site?
Para o servidor de host do Hyper-V que você precisa depende do cenário de implantação. Verifique os pré-requisitos do Hyper-V em:

* [Replicando VMs Hyper-V no Azure (sem VMM)](site-recovery-hyper-v-site-to-azure.md)
* [Replicando VMs Hyper-V no Azure (com VMM)](site-recovery-vmm-to-azure.md)
* [Replicando VMs Hyper-V em um datacenter secundário](site-recovery-vmm-to-vmm.md)
* Se você estiver replicando para um site secundário, leia sobre os [Sistemas operacionais convidados com suporte para as VMs Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Se estiver replicando no Azure, o Site Recovery dá suporte a todos os sistemas operacionais convidados [com suporte do Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger VMs quando o Hyper-V está em execução em um sistema operacional cliente?
Não, as VMs devem estar localizadas em um servidor de host do Hyper-V sendo executado em uma máquina do servidor Windows com suporte. Se você precisar proteger um computador cliente, replique-o como um computador físico no [Azure](site-recovery-vmware-to-azure.md) ou em um [datacenter secundário](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais cargas de trabalho posso proteger com o Site Recovery?
Você pode usar a Recuperação de Site para proteger a maioria das cargas de trabalho em execução em uma VM ou em um servidor físico com suporte. O Site Recovery também dá suporte para a replicação com reconhecimento de aplicativo, para que os aplicativos possam ser recuperados para um estado inteligente. Ele se integra aos aplicativos da Microsoft, incluindo o SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hosts Hyper-V precisam estar em nuvens VMM?
Se você quiser replicar para um datacenter secundário, as VMs Hyper-V deverão estar nos servidores host do Hyper-V em uma nuvem VMM. Se você quiser replicar para o Azure, poderá replicar as VMs nos servidores de host do Hyper-V com ou sem nuvens do VMM. [Leia mais](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Posso implantar o Site Recovery com VMM se eu tiver apenas um servidor VMM?

Sim. Você pode a replicar as VMs nos servidores do Hyper-V na nuvem do VMM para o Azure ou pode replicar entre as nuvens do VMM no mesmo servidor. Para a replicação local a local, recomendamos um servidor VMM nos sites primário e secundário.  

### <a name="what-physical-servers-can-i-protect"></a>Quais servidores físicos posso proteger?
Você pode replicar servidores físicos que executem Windows e Linux para o Azure ou para um site secundário. [Saiba mais](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) sobre os requisitos do sistema operacional.  Os mesmos requisitos se aplicam independentemente de se estar replicando servidores físicos para o Azure ou para um site secundário.


Observe que servidores físicos serão executados como VMs no Azure se seu servidor local ficar inativo. Atualmente, não há suporte para Failback para um servidor físico local. Para um computador protegido como físico, você só pode fazer failback para uma máquina virtual VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Quais VMs VMware posso proteger?

Para proteger as VMs VMware, você precisará de um hipervisor vSphere e de máquinas virtuais executando as ferramentas VMware. Também recomendamos que você tenha um servidor VMware vCenter para gerenciar os hipervisores. [Saiba mais](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) sobre os requisitos exatos para replicar os servidores VMware e VMs para o Azure ou um site secundário.


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Posso gerenciar a recuperação de desastre nas minhas filiais com o Site Recovery?
Sim. Ao usar o Site Recovery para administrar a replicação e o failover em suas filiais, você poderá visualizar e administrar unificadamente todas as suas cargas de trabalho corporativas em um local centralizado. Você pode executar failovers e administrar a recuperação de desastre de todas as ramificações do escritório principal facilmente, sem precisar fazer visitas locais.

## <a name="pricing"></a>Preços

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>Quais encargos serão cobrados quando eu usar o Azure Site Recovery?
Ao usar o Site Recovery, você incorre em encargos para a licença do Site Recovery, o Armazenamento do Azure, transações de armazenamento e a transferência de dados de saída. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery).

A licença de Site Recovery é por instância protegida, onde uma instância é uma VM ou um servidor físico.

- Se um disco de VM replica para uma conta de armazenamento padrão, o custo de armazenamento do Azure é para o consumo de armazenamento. Por exemplo, se o tamanho do disco de origem é 1 TB e 400 GB é usado, Site Recovery cria um VHD de 1 TB no Azure, mas o armazenamento cobrado é 400 GB (mais a quantidade de espaço de armazenamento usado para logs de replicação).
- Se um disco de VM replica para uma conta de armazenamento premium, o custo de armazenamento do Azure é para o tamanho de armazenamento provisionado, arredondado para a opção mais próxima de disco de armazenamento premium. Por exemplo, se o tamanho do disco de origem for 50 GB, Site Recovery cria um disco de 50 GB no Azure e o Azure isso mapeia para o disco de armazenamento premium mais próximo (P10).  Os custos são calculados em P10 e não no tamanho do disco de 50 GB.  [Saiba mais](https://aka.ms/premium-storage-pricing).  Se você estiver usando o armazenamento premium, uma conta de armazenamento padrão para o log de replicação também é necessária e a quantidade de espaço de armazenamento padrão usado para esses logs também é cobrada.
- Nenhum disco é criado até a realização de um failover de teste ou de um failover. No estado de replicação, os encargos de armazenamento na categoria "Blob de páginas e de disco", conforme a [Calculadora de preços de armazenamento](https://azure.microsoft.com/en-in/pricing/calculator/), serão cobrados. Esses encargos se baseiam no tipo de armazenamento premium/standard e no tipo de redundância de dados LRS, GRS, RA-GRS etc.
- Se a opção para usar discos gerenciados em um failover for selecionada, os [encargos para discos gerenciados](https://azure.microsoft.com/en-in/pricing/details/managed-disks/) serão aplicados após um failover/failover de teste. Os encargos de discos gerenciados não se aplicam durante a replicação.
- Se a opção para usar discos gerenciados em um failover não for selecionada, os encargos de armazenamento na categoria "Blob de páginas e de disco", de acordo com a [Calculadora de preços de armazenamento](https://azure.microsoft.com/en-in/pricing/calculator/), serão cobrados após o failover. Esses encargos se baseiam no tipo de armazenamento premium/standard e no tipo de redundância de dados LRS, GRS, RA-GRS etc.
- As transações de armazenamento são cobradas durante a replicação de estado estacionário e para operações regulares de VM após um failover/failover de teste. Mas essas cobranças são insignificantes.

Os custos são incorridos também durante o failover de teste, onde os custos de transações de VM, armazenamento, saída e armazenamento serão aplicados.



## <a name="security"></a>Segurança
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está em execução nas máquinas virtuais ou nos servidores físicos.
Os dados de replicação são trocados entre hosts locais do Hyper-V, hipervisores VMware ou servidores físicos e o armazenamento do Azure ou seu site secundário. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  

O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Para fins de conformidade, até os nossos metadados locais têm que permanecer na mesma região geográfica. O Site Recovery pode nos ajudar?
Sim. Quando você cria um cofre do Site Recovery em uma região de sua escolha, garantimos que todos os metadados de que precisamos para habilitar e administrar a replicação e o failover permaneçam dentro dos limites geográficos da região.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?
Para máquinas virtuais e servidores físicos que estão sendo replicados entre sites locais, há suporte para a criptografia em trânsito. Para máquinas virtuais e servidores físicos que estão sendo replicados no Azure, há suporte tanto para a criptografia em trânsito quanto para a [criptografia em repouso (no Azure)](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>É possível replicar em uma VPN site a site para o Azure?
O Azure Site Recovery replica os dados para uma Conta de Armazenamento do Azure em um ponto de extremidade público. A replicação não ocorre através de uma VPN site a site. Você pode criar uma VPN site a site, com uma rede virtual do Azure. Isso não interfere na replicação do Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>É possível usar o ExpressRoute para replicar máquinas virtuais no Azure?
Sim, o ExpressRoute pode ser usado para replicar máquinas virtuais no Azure. O Azure Site Recovery replica os dados para uma Conta de Armazenamento do Azure em um ponto de extremidade público. Você precisa configurar o [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) para usar o ExpressRoute para replicação de Site Recovery. Após o failover das máquinas virtuais para uma rede virtual do Azure, é possível acessá-las usando a configuração de [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) com a rede virtual do Azure.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Há algum pré-requisito para replicar máquinas virtuais no Azure?
As máquinas virtuais que você deseja replicar no Azure devem estar em conformidade com os [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Posso replicar máquinas virtuais Hyper-V de segunda geração no Azure?
Sim. O Site Recovery converte da segunda geração para a primeira geração durante o failover. No failback, a máquina é convertida novamente em segunda geração. [Leia mais](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Se eu replicar no Azure, como faço para pagar as VMs do Azure?
Durante a replicação normal, os dados são replicados no armazenamento do Azure com redundância geográfica e você não precisa pagar nada pela máquina virtual IaaS do Azure, o que é uma vantagem significativa. Quando você executa um failover no Azure, o Site Recovery cria automaticamente máquinas virtuais IaaS do Azure e você será cobrado pelos recursos de computação que consumir no Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar os cenários do Site Recovery com um SDK?
Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Cenários com suporte no momento para implantar a Recuperação de Site usando o PowerShell:

* [Replicar VMs Hyper-V em nuvens VMM para o Azure PowerShell do Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Replicar VMs Hyper-V sem VMM para o Azure PowerShell do Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Se eu replicar no Azure, de que tipo de conta de armazenamento preciso?
* **Portal Clássico do Azure**: se você estiver implantando o Site Recovery no Portal Clássico do Azure, precisará de uma [conta de armazenamento com redundância geográfica padrão](../storage/common/storage-redundancy.md#geo-redundant-storage). Atualmente não há suporte para o armazenamento Premium. A conta deve estar na mesma região que o cofre da Recuperação de Site.
* **Portal do Azure**: se você estiver implantando o Site Recovery no Portal do Azure, precisará de uma conta de armazenamento LRS ou GRS. É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada. A rede deve estar na mesma região do que o cofre dos Serviços de Recuperação. O Armazenamento Premium agora tem suporte para VM do VMware, VM do Hyper-V e replicação de servidores físicos, quando você implantar o Site Recovery no Portal do Azure.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar dados?
* **Hyper-V:** as VMs Hyper-V podem ser replicadas a cada 30 segundos (exceto para armazenamento Premium), cinco minutos ou 15 minutos. Se você tiver definido a replicação da rede SAN, ela será simultânea.
* **VMware e servidores físicos:** não é relevante estabelecer uma frequência de replicação para eles. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Posso estender a replicação do site de recuperação existente para um site terciário?
Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline na primeira vez em que replicar no Azure?
Não há suporte para isso. Solicite esse recurso no [fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos da replicação?
Haverá suporte para isso quando você estiver [replicando VMs VMware e VMs do Hyper-V](site-recovery-exclude-disk.md) no Azure usando o Portal do Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Posso replicar máquinas virtuais com discos dinâmicos?
Os discos dinâmicos têm suporte na replicação de máquinas virtuais Hyper-V. Eles também têm suporte ao replicar VMs VMware e máquinas físicas no Azure. O disco do sistema operacional deve ser um disco básico.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Posso adicionar um novo computador a um grupo de replicação existente?
Há suporte para a adição de novos computadores a grupos de replicação existentes. Para fazer isso, selecione o grupo de replicação (da folha “Itens replicados”) e clique com o botão direito do mouse/selecione o menu de contexto no grupo de replicação e selecione a opção apropriada.

![Adicionar ao grupo de replicação](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Posso restringir a largura de banda alocada para o tráfego de replicação do Hyper-V?
Sim. Você pode ler mais sobre a limitação de largura de banda nos artigos da implantação:

* [Planejamento de capacidade para a replicação de VMs VMware e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planejamento de capacidade para a replicação de VMs Hyper-V em nuvens VMM](site-recovery-vmm-to-azure.md#capacity-planning)
* [Planejamento de capacidade para a replicação de VMs Hyper-V sem VMM](site-recovery-hyper-v-site-to-azure.md)

## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Se estou fazendo failover no Azure, como posso acessar as máquinas virtuais do Azure após o failover?
Você pode acessar as VMs do Azure em uma conexão segura da Internet, em uma VPN site a site ou na ExpressRoute do Azure. Você precisará preparar uma série de coisas para se conectar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se eu fizer failover no Azure, como o Azure poderá garantir a resiliência dos meus dados?
O Azure foi desenvolvido para resiliência. O Site Recovery já foi desenvolvido para fazer failover em um datacenter secundário do Azure de acordo com o SLA do Azure, caso haja a necessidade. Se isso acontecer, faremos com que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ao replicar entre dois datacenters, o que acontece se meu datacenter primário sofrer uma interrupção inesperada?
Você pode disparar um failover não planejado do site secundário. O Site Recovery não precisa de conectividade do local primário para fazer o failover.

### <a name="is-failover-automatic"></a>O failover é automático?
O failover não é automático. Você pode iniciar failovers com um único clique no portal ou pode usar o [PowerShell do Site Recovery](/powershell/module/azurerm.siterecovery) para disparar um failover. O failback é uma ação simples no portal do Site Recovery.

Para automatizar, você pode usar o Orchestrator ou o Operations Manager local para detectar a falha da máquina virtual e disparar o failover usando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
* [Saiba mais](site-recovery-failover.md) sobre failover.
* [Saiba mais](site-recovery-failback-azure-to-vmware.md) como realizar failback das VMs VMware e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Se o meu host local não está respondendo ou falha, é possível fazer failover de volta para um host diferente?
Sim, você pode usar a recuperação em uma localização alternativa para fazer failback para um host diferente do Azure. Leia mais sobre as opções nos links a seguir para máquinas virtuais VMware e Hyper-V.

* [Para máquinas virtuais VMware](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [Para máquinas virtuais Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>Provedores de serviço
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Sou um provedor de serviço. O Site Recovery funciona com modelos de infraestrutura compartilhados e dedicados?
Sim, o Site Recovery dá suporte aos modelos de infraestrutura dedicados e compartilhados.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para o provedor de serviço, a identidade do meu locatário é compartilhada com o serviço da Recuperação de Site?
Não. A identidade do locatário permanece anônima. Seus locatários não precisam acessar o Portal da Recuperação de Site. Somente o administrador do provedor de serviços interage com o portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Os dados de aplicativo do locatário vão para o Azure?
Durante a replicação entre os sites de propriedade do provedor de serviços, os dados de aplicativo nunca vão para o Azure. Os dados são criptografados em trânsito e replicados diretamente entre os sites do provedor de serviço.

Se você estiver replicando no Azure, os dados de aplicativo são enviados para o armazenamento do Azure, mas não para o serviço de Recuperação de Site. Os dados são criptografados em trânsito e permanecem criptografados no Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Meus locatários receberão alguma fatura relativa a serviços do Azure?
Não. A relação de cobrança do Azure é direta com o provedor de serviços. Os provedores de serviços são responsáveis por gerar faturas específicas para seus locatários.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Ao replicar no Azure, precisamos executar máquinas virtuais no Azure o tempo todo?
Não, os dados são replicados para uma conta de armazenamento do Azure na sua assinatura. Quando você executa um failover de teste (análise de DR) ou um failover real, o Site Recovery cria automaticamente máquinas virtuais em sua assinatura.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Vocês garantem o isolamento no nível de locatário quando faço a replicação no Azure?
Sim.

### <a name="what-platforms-do-you-currently-support"></a>A quais plataformas vocês oferecem suporte atualmente?
Damos suporte ao Azure Pack, ao Sistema de Plataforma de Nuvem e a implantações baseadas no System Center (2012 e posterior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do Azure Pack e da Recuperação de Site.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Você também dá suporte implantações únicas de Azure Pack e servidor VMM?
Sim, você pode replicar máquinas virtuais Hyper-V para o Azure ou entre os sites de provedor de serviços.  Observe que, se você replicar entre sites do provedor de serviços, a integração de runbook do Azure não estará disponível.

## <a name="next-steps"></a>Próximas etapas
* Leia a [visão geral do Site Recovery](site-recovery-overview.md)
* Saiba mais sobre a [arquitetura do Site Recovery](site-recovery-components.md)  
