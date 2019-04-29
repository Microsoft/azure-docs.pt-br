---
title: 'Azure Site Recovery: Perguntas frequentes | Microsoft Docs'
description: Este artigo aborda dúvidas comuns sobre o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/08/2019
ms.author: raynew
ms.openlocfilehash: 824782e54f2cd989f9ab13857d9b894b215fc550
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61278881"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: perguntas frequentes
Este artigo resume as perguntas frequentes sobre o Azure Site Recovery. 

## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz o Site Recovery?
O Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando e automatizando a replicação de VMs do Azure entre regiões, máquinas virtuais locais e servidores físicos no Azure e máquinas locais em um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Posso proteger uma máquina virtual que tem um disco de Docker?

Não. Esse é um cenário sem suporte.

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
Não, os dados são replicados para armazenamento do Azure em sua assinatura. Quando você executa um failover de teste (análise de DR) ou um failover real, o Site Recovery cria automaticamente máquinas virtuais em sua assinatura.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Vocês garantem o isolamento no nível de locatário quando faço a replicação no Azure?
Sim.

### <a name="what-platforms-do-you-currently-support"></a>A quais plataformas vocês oferecem suporte atualmente?
Damos suporte ao Azure Pack, ao Sistema de Plataforma de Nuvem e a implantações baseadas no System Center (2012 e posterior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do Azure Pack e da Recuperação de Site.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Você também dá suporte implantações únicas de Azure Pack e servidor VMM?
Sim, você pode replicar máquinas virtuais Hyper-V para o Azure ou entre os sites de provedor de serviços.  Observe que, se você replicar entre sites do provedor de serviços, a integração de runbook do Azure não estará disponível.

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-pricing-information"></a>Onde posso encontrar informações sobre preços?
Revisão [preços do Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) detalhes.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como posso calcular preços aproximados durante o uso do Site Recovery?

Você pode usar o [Calculadora de preços](https://aka.ms/asr_pricing_calculator) estimar os custos ao usar o Site Recovery.

Para a previsão detalhada nos custos, executar a ferramenta de Planejador de implantação para [VMware](https://aka.ms/siterecovery_deployment_planner) ou [Hyper-V](https://aka.ms/asr-deployment-planner)e usar o [relatório de estimativa de custo](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Agora, os discos gerenciados são usados para replicar VMs VMware e servidores físicos. Eu incorro em encargos adicionais para a conta de armazenamento de cache com discos gerenciados?

Não, não há encargos adicionais para o cache. Quando você replica para conta de armazenamento padrão, esse armazenamento em cache é parte da mesma conta de armazenamento de destino.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou usuário do Azure Site Recovery há mais de um mês. Eu ainda terei os primeiros 31 dias gratuitos para todas as instâncias protegidas?

Sim. Nenhuma instância protegida gera cobranças do Azure Site Recovery durante os primeiros 31 dias. Por exemplo, não se você esteve protegendo 10 instâncias nos últimos 6 meses e conectar uma 11 ª instância ao Azure Site Recovery, há nenhum encargo para a 11ª instância durante os primeiros 31 dias. As primeiras 10 instâncias continuam a incorrer em encargos do Azure Site Recovery, pois eles já foram protegidos por mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, serei cobrado por outras tarifas do Azure?

Sim, mesmo que o Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, você pode incorrer em encargos para o armazenamento do Azure, as transações de armazenamento e transferência de dados. Uma máquina virtual recuperada também poderá gerar encargos de computação do Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Há um custo associado para executar a recuperação de desastres análises/teste de failover?

Não há nenhum custo separado para análise de recuperação de desastres. Haverá encargos de computação depois que a VM é criada após o failover de teste.



## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está em execução nas máquinas virtuais ou nos servidores físicos.
Os dados de replicação são trocados entre hosts locais do Hyper-V, hipervisores VMware ou servidores físicos e o armazenamento do Azure ou seu site secundário. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  

O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Para fins de conformidade, até os nossos metadados locais têm que permanecer na mesma região geográfica. O Site Recovery pode nos ajudar?
Sim. Quando você cria um cofre do Site Recovery em uma região de sua escolha, garantimos que todos os metadados de que precisamos para habilitar e administrar a replicação e o failover permaneçam dentro dos limites geográficos da região.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?
Para máquinas virtuais e servidores físicos que estão sendo replicados entre sites locais, há suporte para a criptografia em trânsito. Para máquinas virtuais e servidores físicos que estão sendo replicados no Azure, há suporte tanto para a criptografia em trânsito quanto para a [criptografia em repouso (no Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption).




## <a name="disaster-recovery"></a>Recuperação de desastre

### <a name="what-can-site-recovery-protect"></a>O que o Site Recovery pode proteger?
* **VMs do Azure**: O Site Recovery pode replicar qualquer carga de trabalho em execução em uma VM do Azure com suporte
* **Máquinas virtuais do Hyper-V**: O Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM do Hyper-V.
* **Servidores físicos**: O Site Recovery pode proteger servidores físicos que executam Windows ou Linux.
* **Máquinas virtuais do VMware**: O Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM do VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais cargas de trabalho posso proteger com o Site Recovery?
Você pode usar a Recuperação de Site para proteger a maioria das cargas de trabalho em execução em uma VM ou em um servidor físico com suporte. O Site Recovery também dá suporte para a replicação com reconhecimento de aplicativo, para que os aplicativos possam ser recuperados para um estado inteligente. Ele se integra aos aplicativos da Microsoft, incluindo o SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Posso gerenciar a recuperação de desastre nas minhas filiais com o Site Recovery?
Sim. Ao usar o Site Recovery para administrar a replicação e o failover em suas filiais, você poderá visualizar e administrar unificadamente todas as suas cargas de trabalho corporativas em um local centralizado. Você pode executar failovers e administrar a recuperação de desastre de todas as ramificações do escritório principal facilmente, sem precisar fazer visitas locais.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Há suporte para recuperação de desastre para VMs do Azure?

Sim, Site Recovery dá suporte a desastres para VMs do Azure entre regiões do Azure. [Examine as perguntas comuns](azure-to-azure-common-questions.md) sobre recuperação de desastres de VM do Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Há suporte para recuperação de desastre para VMs VMware?

Sim, o Site Recovery dá suporte a recuperação de desastre de VMs do VMware local. [Examine as perguntas comuns](vmware-azure-common-questions.md) para recuperação de desastre de VMs VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Há suporte para recuperação de desastre para VMs do Hyper-V?
Sim, o Site Recovery dá suporte a recuperação de desastre de VMs do Hyper-V local. [Examine as perguntas comuns](hyper-v-azure-common-questions.md) para recuperação de desastre de VMs Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Há suporte para recuperação de desastre para servidores físicos?
Sim, o Site Recovery dá suporte à recuperação de desastre de servidores físicos locais, que executam o Windows e Linux no Azure ou em um site secundário. Saiba mais sobre os requisitos de recuperação de desastres para [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)e, ao[um site secundário](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Observe que os servidores físicos serão executados como VMs no Azure após o failover. Atualmente, não há suporte para failback do Azure para um servidor de local físico. Você só pode executar failback para uma máquina virtual VMware.





## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>É possível replicar em uma VPN site a site para o Azure?
O Azure Site Recovery replica os dados a uma conta de armazenamento do Azure ou discos gerenciados, ao longo de um ponto de extremidade público. A replicação não ocorre através de uma VPN site a site. 

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não é possível replicar em VPN?

Quando você replica para o Azure, o tráfego de replicação alcança os pontos de extremidade públicos de um armazenamento do Azure. Portanto, só é possível replicar pela internet pública com o ExpressRoute (emparelhamento público) e VPN não funciona.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Pode usar SteelHeads Riverbed para replicação?

Nosso parceiro, Riverbed, fornece orientações detalhadas sobre como trabalhar com o Azure Site Recovery. Examine suas [guia de solução](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>É possível usar o ExpressRoute para replicar máquinas virtuais no Azure?
Sim, o [ExpressRoute pode ser usado](concepts-expressroute-with-site-recovery.md) para replicar máquinas virtuais locais no Azure.

- O Azure Site Recovery replica os dados para um armazenamento do Azure em um ponto de extremidade público. Você precisa configurar o [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#publicpeering) ou [emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) para usar o ExpressRoute para replicação de Site Recovery.
- Emparelhamento da Microsoft é o domínio de roteamento recomendado para replicação.
- Após o failover das máquinas virtuais para uma rede virtual do Azure, é possível acessá-las usando a configuração de [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering) com a rede virtual do Azure.
- Não há suporte para a replicação sobre emparelhamento privado.
- Se você estiver protegendo máquinas do VMware ou computadores físicos, certifique-se de que o servidor de configuração seja compatível com [requisitos de rede](vmware-azure-configuration-server-requirements.md#network-requirements) para replicação. 



### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se eu replicar no Azure, que tipo de conta de armazenamento ou o disco gerenciado é necessário?

Você precisa de um armazenamento LRS ou GRS. É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada. A rede deve estar na mesma região do que o cofre dos Serviços de Recuperação. O Armazenamento Premium tem suporte para VM do VMware, VM do Hyper-V e replicação de servidores físicos, quando você implantar o Site Recovery no Portal do Azure. Discos gerenciados só oferecem suporte ao LRS.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar dados?
* **Hyper-V:** VMs Hyper-V podem ser replicadas a cada cinco minutos, o 30 segundos (exceto para o armazenamento premium)
* **Azure VMs, VMs VMware, de servidores físicos:** Uma frequência de replicação não é relevante aqui. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Posso estender a replicação do site de recuperação existente para um site terciário?
Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline na primeira vez em que replicar no Azure?
Não há suporte para isso. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos da replicação?
Haverá suporte para isso quando você estiver replicando VMs VMware e VMs do Hyper-V no Azure usando o Portal do Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Posso replicar máquinas virtuais com discos dinâmicos?
Discos dinâmicos têm suporte ao replicar máquinas virtuais Hyper-V e ao replicar VMs VMware e máquinas físicas no Azure. O disco do sistema operacional deve ser um disco básico.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>É possível limitar a largura de banda alocada para o tráfego de replicação?
Sim. Você pode ler mais sobre a limitação de largura de banda nesses artigos:

* [Planejamento de capacidade para a replicação de VMs VMware e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planejamento de capacidade para a replicação de VMs Hyper-V para o Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se eu estou fazendo failover no Azure, como fazer para acessar as VMs do Azure após o failover?

Você pode acessar as VMs do Azure em uma conexão segura da Internet, em uma VPN site a site ou na Azure ExpressRoute. Você precisa preparar uma série de coisas para se conectar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se eu fizer failover no Azure, como o Azure poderá garantir a resiliência dos meus dados?
O Azure foi desenvolvido para resiliência. Recuperação de site já foi desenvolvida para fazer failover em um datacenter secundário do Azure, acordo com o SLA do Azure. Se isso acontecer, faremos com que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ao replicar entre dois datacenters, o que acontece se meu datacenter primário sofrer uma interrupção inesperada?
Você pode disparar um failover não planejado do site secundário. O Site Recovery não precisa de conectividade do local primário para fazer o failover.

### <a name="is-failover-automatic"></a>O failover é automático?
O failover não é automático. Você pode iniciar failovers com um único clique no portal ou pode usar o [PowerShell do Site Recovery](/powershell/module/az.recoveryservices) para disparar um failover. O failback é uma ação simples no portal do Site Recovery.

Para automatizar, você pode usar o Orchestrator ou o Operations Manager local para detectar a falha da máquina virtual e disparar o failover usando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
* [Saiba mais](site-recovery-failover.md) sobre failover.
* [Saiba mais](site-recovery-failback-azure-to-vmware.md) como realizar failback das VMs VMware e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se o meu host local não está respondendo ou falha, pode fazer failback para um host diferente?
Sim, você pode usar a recuperação em uma localização alternativa para fazer failback para um host diferente do Azure.

* [Para máquinas virtuais VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Para máquinas virtuais Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automação

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar os cenários do Site Recovery com um SDK?
Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Cenários com suporte no momento para implantar a Recuperação de Site usando o PowerShell:

* [Replicar VMs Hyper-V em nuvens VMM para o Azure PowerShell do Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replicar VMs Hyper-V sem VMM para o Azure PowerShell do Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replicar o VMware no Azure com o Gerenciador de Recursos do PowerShell](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Atualização de componente/provedor

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Onde posso encontrar os versão notas/pacotes cumulativos de atualizações do Site Recovery

[Saiba mais](site-recovery-whats-new.md) sobre novas atualizações, e [Obtenha informações do pacote cumulativo de atualizações](service-updates-how-to.md).

## <a name="next-steps"></a>Próximas etapas
* Leia a [visão geral do Site Recovery](site-recovery-overview.md)

