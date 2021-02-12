---
title: Perguntas gerais sobre o serviço do Azure Site Recovery
description: Este artigo aborda dúvidas comuns sobre o Azure Site Recovery.
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 9db91a15c0ee5c982f73f36a36f12b38b969a125
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820189"
---
# <a name="general-questions-about-azure-site-recovery"></a>Perguntas gerais sobre o Azure Site Recovery

Este artigo resume as perguntas frequentes sobre o Azure Site Recovery. Para cenários específicos, examine estes artigos

- [Perguntas sobre a recuperação de desastre de VMs do Azure para o Azure](azure-to-azure-common-questions.md)
- [Perguntas sobre a recuperação de desastre de VMs VMware para o Azure](vmware-azure-common-questions.md)
- [Perguntas sobre a recuperação de desastre de VMs Hyper-V para o Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz o Site Recovery?

O Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando e automatizando a replicação de VMs do Azure entre regiões, máquinas virtuais locais e servidores físicos no Azure e máquinas locais em um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Posso proteger uma máquina virtual que tem um disco do Docker?

Não. Esse é um cenário sem suporte.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>O que Site Recovery fazer para garantir a integridade dos dados?

Há várias medidas tomadas pelo Site Recovery para garantir a integridade dos dados. Uma conexão segura é estabelecida entre todos os serviços usando o protocolo HTTPS. Isso garante que qualquer malware ou entidades externas não possam adulterar os dados. Outra medida tomada é usar somas de verificação. A transferência de dados entre a origem e o destino é executada por meio da computação de somas de verificação de dados entre eles. Isso garante que os dados transferidos sejam consistentes.

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
Não, os dados são replicados para o armazenamento do Azure em sua assinatura. Quando você executa um failover de teste (análise de DR) ou um failover real, o Site Recovery cria automaticamente máquinas virtuais em sua assinatura.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Vocês garantem o isolamento no nível de locatário quando faço a replicação no Azure?
Sim.

### <a name="what-platforms-do-you-currently-support"></a>A quais plataformas vocês oferecem suporte atualmente?
Damos suporte ao Azure Pack, ao Sistema de Plataforma de Nuvem e a implantações baseadas no System Center (2012 e posterior). [Saiba mais](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) sobre a integração do Azure Pack e da Recuperação de Site.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Você também dá suporte implantações únicas de Azure Pack e servidor VMM?
Sim, você pode replicar máquinas virtuais Hyper-V para o Azure ou entre os sites de provedor de serviços.  Observe que, se você replicar entre sites do provedor de serviços, a integração de runbook do Azure não estará disponível.

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-pricing-information"></a>Onde posso encontrar informações sobre preços?
Veja os detalhes de [Preços do Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como posso calcular preços aproximados durante o uso do Site Recovery?

Você pode usar a [calculadora de preços](https://aka.ms/asr_pricing_calculator) para estimar os custos ao usar o Azure Site Recovery.

Para obter uma estimativa detalhada dos custos, execute a ferramenta planejadora de implantação para [VMware](./site-recovery-deployment-planner.md) ou [Hyper-V](https://aka.ms/asr-deployment-planner) e use o [relatório de estimativa de custo](./site-recovery-vmware-deployment-planner-cost-estimation.md).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Os discos gerenciados agora são usados para replicar VMs VMware e servidores físicos. Posso receber cobranças adicionais para a conta de armazenamento em cache com discos gerenciados?

Não, não há cobranças adicionais para o cache. Quando você faz replicação para a conta de armazenamento padrão, esse armazenamento em cache faz parte da mesma conta de armazenamento de destino.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou usuário do Azure Site Recovery há mais de um mês. Eu ainda terei os primeiros 31 dias gratuitos para todas as instâncias protegidas?

Sim. Nenhuma instância protegida gera cobranças do Azure Site Recovery durante os primeiros 31 dias. Por exemplo, se você esteve protegendo 10 instâncias nos últimos 6 meses e conectar uma 11ª instância ao Azure Site Recovery, não haverá cobranças para a 11ª instância durante os primeiros 31 dias. As primeiras 10 instâncias continuam gerando encargos do Azure Site Recovery, uma vez que elas foram protegidas por mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, serei cobrado por outras tarifas do Azure?

Sim. Entretanto, embora o Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, você poderá ser cobrado pelo Armazenamento do Microsoft Azure, por transações de armazenamento e por transferência de dados. Uma máquina virtual recuperada também poderá gerar encargos de computação do Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Há um custo associado para executar testes de recuperação de desastre/failover de teste?

Não há nenhum custo separado para análise de recuperação de desastres. Haverá cobranças de computação depois que a VM for criada após o failover de teste.



## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está em execução nas máquinas virtuais ou nos servidores físicos.
Os dados de replicação são trocados entre hosts locais do Hyper-V, hipervisores VMware ou servidores físicos e o armazenamento do Azure ou seu site secundário. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  

O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Para fins de conformidade, até os nossos metadados locais têm que permanecer na mesma região geográfica. O Site Recovery pode nos ajudar?
Sim. Quando você cria um cofre do Site Recovery em uma região de sua escolha, garantimos que todos os metadados de que precisamos para habilitar e administrar a replicação e o failover permaneçam dentro dos limites geográficos da região.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?
Para máquinas virtuais e servidores físicos que estão sendo replicados entre sites locais, há suporte para a criptografia em trânsito. Para máquinas virtuais e servidores físicos que estão sendo replicados no Azure, há suporte tanto para a criptografia em trânsito quanto para a [criptografia em repouso (no Azure)](../storage/common/storage-service-encryption.md).

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>O Azure para Azure Site Recovery usa TLS 1.2 para todas as comunicações entre os microsserviços do Azure?
Sim, o protocolo TLS 1.2 é imposto por padrão para o cenário de Azure para Azure Site Recovery. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Como posso aplicar TLS 1.2 em cenários de VMware para Azure e servidor físico para Azure Site Recovery?
Os agentes de mobilidade instalados nos itens replicados se comunicam com o Servidor de Processo somente no TLS 1.2. No entanto, a comunicação do Servidor de Configuração para o Azure e do Servidor de Processo para o Azure pode estar em TLS 1.1 ou 1.0. Siga as [diretrizes](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) para impor o TLS 1.2 em todos os Servidores de Configuração e Servidores de Processo configurados por você.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Como posso aplicar TLS 1.2 em cenários de Hyper-V para Azure Site Recovery?
Toda a comunicação entre os microsserviços do Azure Site Recovery ocorre no protocolo TLS 1.2. O Site Recovery usa provedores de segurança configurados no sistema (SO) e usa o protocolo TLS mais recente disponível. Será necessário habilitar explicitamente o TLS 1.2 no Registro e, em seguida, o Site Recovery começará a usar TLS 1.2 para comunicação com serviços. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Como posso impor acesso restrito em minhas contas de armazenamento, que são acessadas pelo serviço Site Recovery para leitura/gravação de dados de replicação?
Você pode alternar a identidade gerenciada do cofre dos serviços de recuperação acessando a configuração de *identidade* . Depois que o cofre é registrado com Azure Active Directory, você pode acessar suas contas de armazenamento e fornecer as seguintes atribuições de função ao cofre:

- Contas de armazenamento baseadas no Resource Manager (tipo padrão):
  - [Colaborador](../role-based-access-control/built-in-roles.md#contributor)
  - [Colaborador de dados de blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Contas de armazenamento baseadas no Resource Manager (tipo Premium):
  - [Colaborador](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietário de Dados do Blob de Armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Contas de armazenamento clássicas:
  - [Colaborador da conta de armazenamento clássica](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

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


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Há compatibilidade com a recuperação de desastre para VMs do Azure?

Sim, o Site Recovery é compatível com desastres para VMs do Azure entre regiões do Azure. [Examine as perguntas comuns](azure-to-azure-common-questions.md) sobre a recuperação de desastre de VM do Azure. Se você quiser replicar entre duas regiões do Azure no mesmo continente, use nossa oferta de DR do Azure para o Azure. Não é necessário configurar o servidor de configuração/servidor de processo e as conexões de ExpressRoute.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>A recuperação de desastre é compatível com VMs VMware?

Sim, o Site Recovery é compatível com a recuperação de desastre de VMs VMware locais. [Examine as perguntas comuns](vmware-azure-common-questions.md) para recuperação de desastre de VMs VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Há compatibilidade com a recuperação de desastre para VMs do Hyper-V?
Sim, o Site Recovery permite a recuperação de desastre de VMs do Hyper-V locais. [Examine as perguntas comuns](hyper-v-azure-common-questions.md) para recuperação de desastre de VMs do Hyper-V.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>Há compatibilidade com a recuperação de desastre para servidores físicos?
Sim, o Site Recovery permite a recuperação de desastre de servidores físicos locais que executam o Windows e o Linux no Azure ou em um site secundário. Saiba mais sobre os requisitos de recuperação de desastre para [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) e para[um site secundário](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Observe que os servidores físicos serão executados como VMs no Azure após o failover. Atualmente, não há suporte para Failback do Azure para um servidor físico local. Só é possível fazer failback para uma máquina virtual VMware.





## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>É possível replicar em uma VPN site a site para o Azure?
O Azure Site Recovery replica os dados para uma Conta de Armazenamento do Azure ou em discos gerenciados em um ponto de extremidade público. A replicação não ocorre através de uma VPN site a site. 

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não é possível replicar em VPN?

Quando você replica para o Azure, o tráfego de replicação atinge os pontos de extremidade públicos de um Armazenamento do Microsoft Azure. Assim, só é possível replicar pela Internet pública ou por ExpressRoute (emparelhamento da Microsoft ou um emparelhamento público existente).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Posso usar o Riverbed SteelHeads para replicação?

Nosso parceiro, Riverbed, mostra orientações detalhadas sobre como trabalhar com Azure Site Recovery. Examine o [guia de solução](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>É possível usar o ExpressRoute para replicar máquinas virtuais no Azure?
Sim, o [ExpressRoute pode ser usado](concepts-expressroute-with-site-recovery.md) para replicar máquinas virtuais locais no Azure.

- O Azure Site Recovery replica os dados para um Armazenamento do Microsoft Azure em um ponto de extremidade público. É preciso configurar o [emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou usar um [emparelhamento público](../expressroute/about-public-peering.md) existente (preterido para novos circuitos) para usar o ExpressRoute para a replicação do Site Recovery.
- Emparelhamento da Microsoft é o domínio de roteamento recomendado para replicação.
- Não há suporte para a replicação sobre emparelhamento privado.
- Se você estiver protegendo computadores VMware ou máquinas físicas, verifique se os [Requisitos de Rede](vmware-azure-configuration-server-requirements.md#network-requirements) para o Servidor de Configuração também são atendidos. A conectividade com URLs específicas é exigida pelo Servidor de Configuração para orquestração da replicação do Site Recovery. O ExpressRoute não pode ser usado para essa conectividade.
- Após o failover das máquinas virtuais para uma rede virtual do Azure, é possível acessá-las usando a configuração de [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering) com a rede virtual do Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se eu replicar no Azure, de que tipo de conta de armazenamento ou disco gerenciado preciso?

Não há suporte para o uso de contas de armazenamento como armazenamento de destino pelo Azure Site Recovery. É recomendável usar discos gerenciados como o armazenamento de destino para seus computadores. O Managed disks dá suporte apenas ao tipo LRS para resiliência de dados.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar dados?
* **Hyper-V:** VMs Hyper-V podem ser replicadas a cada 30 segundos (exceto para o armazenamento premium), 5 minutos ou 15 minutos.
* **VMs do Azure, VMs VMware e servidores físicos:** Uma frequência de replicação não é relevante aqui. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Posso estender a replicação do site de recuperação existente para um site terciário?
Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline na primeira vez em que replicar no Azure?
Não há suporte para isso. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos da replicação?
Haverá suporte para isso quando você estiver replicando VMs VMware e VMs do Hyper-V no Azure usando o Portal do Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Posso replicar máquinas virtuais com discos dinâmicos?
Discos dinâmicos são compatíveis ao replicar máquinas virtuais Hyper-V e ao replicar VMs VMware e máquinas físicas para o Azure. O disco do sistema operacional deve ser um disco básico.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Posso restringir a largura de banda alocada para o tráfego de replicação?
Sim. Você pode ler mais sobre a limitação de largura de banda nos artigos:

* [Planejamento de capacidade para a replicação de VMs VMware e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planejamento de capacidade para a replicação de VMs Hyper-V para o Azure](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Posso habilitar a replicação com consistência de aplicativo em servidores Linux? 
Sim. O Azure Site Recovery para o sistema operacional Linux dá suporte a scripts personalizados de aplicativo para consistência de aplicativo. O script personalizado com pré e pós-opções será usado pelo agente de mobilidade Azure Site Recovery durante a consistência do aplicativo. Abaixo estão as etapas para habilitá-lo.

1. Entre como raiz no computador.
2. Altere o diretório para Azure Site Recovery local de instalação do agente de mobilidade. O padrão é "/usr/local/ASR"<br>
    `# cd /usr/local/ASR`
3. Altere o diretório para "VX/scripts" em local de instalação<br>
    `# cd VX/scripts`
4. Crie um script de shell bash chamado "customscript.sh" com permissões de execução para o usuário raiz.<br>
    a. O script deve dar suporte às opções de linha de comando "--pre" e "--post" (Observe os traços duplos)<br>
    b. Quando o script é chamado com a opção pre-Option, ele deve congelar a entrada/saída do aplicativo e, quando chamado com post-Option, ele deve descongelar a entrada/saída do aplicativo.<br>
    c. Um modelo de exemplo –<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Adicione os comandos congelar e descongelar entrada/saída nas etapas anteriores e posteriores para os aplicativos que exigem consistência de aplicativo. Você pode optar por adicionar outro script especificando aqueles e chamá-lo de "customscript.sh" com as opções pre e post.

>[!Note]
>A versão do agente de Site Recovery deve ser 9,24 ou superior para dar suporte a scripts personalizados.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma política de replicação define as configurações para o histórico de retenção dos pontos de recuperação. A política também define a frequência de instantâneos consistentes em termos de aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de:

- 24 horas para o histórico de retenção dos pontos de recuperação.
- 4 horas para a frequência de instantâneos consistentes com o aplicativo.

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de falha?

Um ponto de recuperação consistente em termos de falhas tem os dados em disco como se você tivesse recebido o cabo de alimentação do servidor durante o instantâneo. Ele não inclui o que estava na memória quando o instantâneo foi tirado.

Atualmente, a maioria dos aplicativos pode recuperar-se bem de instantâneos consistente em termos de falha. Um ponto de recuperação consistente em termos de falha geralmente é suficiente para sistemas operacionais sem banco de dados e aplicativos como servidores, servidores DHCP, servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência de geração de ponto de recuperação consistente com a falha?

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de aplicativo?

Pontos de recuperação consistentes em termos de aplicativo são criados com base em instantâneos consistentes em termos de aplicativo. Pontos de recuperação consistentes em termos de aplicativo capturam os mesmos dados de instantâneos consistentes em termos de falhas, além de todos os dados na memória e todas as transações em andamento.

Devido a seu conteúdo adicional, instantâneos de aplicativo consistente são os mais envolvidos e levam mais tempo para executar. Recomendamos pontos de recuperação consistentes em termos de aplicativo para sistemas operacionais de banco de dados como o SQL Server.

>[!Note]
>A criação de pontos de recuperação consistentes com o aplicativo falha no computador Windows, se ele tiver mais de 64 volumes.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto de pontos de recuperação consistentes em termos de aplicativo no desempenho do aplicativo?

Os pontos de recuperação consistentes com o aplicativo capturam todos os dados na memória e em processamento. Como os pontos de recuperação capturam esses dados, eles exigem estruturas como o Serviço de Cópias de Sombra de Volume no Windows para desativar o aplicativo. Se o processo de captura for frequente, poderá afetar o desempenho quando a carga de trabalho já estiver ocupada. Não é recomendável usar a frequência baixa para pontos de recuperação consistentes com o aplicativo em cargas de trabalho que não são de banco de dados. Mesmo para a carga de trabalho do banco de dados, 1 hora é suficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima de geração de ponto de recuperação consistente com o aplicativo?

O Site Recovery pode criar um ponto de recuperação consistente com o aplicativo com frequência mínima de 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como os pontos de recuperação são gerados e salvos?

Para entender como Site Recovery gera pontos de recuperação, veja um exemplo de uma política de replicação. Essa política de replicação tem um ponto de recuperação com uma janela de retenção de 24 horas e um instantâneo de frequência consistente com o aplicativo de 1 hora.

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos. Você não pode alterar essa frequência. Na última hora, você pode escolher entre 12 pontos consistentes com falhas e 1 ponto consistente com o aplicativo. Conforme o tempo decorre, o Site Recovery remove todos os pontos de recuperação além da última hora e salva apenas um ponto de recuperação por hora.

A captura de tela a seguir ilustra o exemplo. Na captura de tela:

- Na última hora, há pontos de recuperação com uma frequência de 5 minutos.
- Depois da última hora, o Site Recovery mantém apenas um ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?

O ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Tenho uma política de replicação de 24 horas. O que acontecerá se um problema impedir o Site Recovery de gerar pontos de recuperação por mais de 24 horas? Meus pontos de recuperação anteriores serão perdidos?

Não, o Site Recovery manterá todos os pontos de recuperação anteriores. Dependendo da janela de retenção dos pontos de recuperação, o Site Recovery substituirá o ponto mais antigo somente se ele gerar novos pontos. Devido ao problema, o Site Recovery não pode gerar novos pontos de recuperação. Até que haja novos pontos de recuperação, todos os pontos antigos permanecerão depois que você chegar à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Após a replicação ser habilitada em uma VM, como fazer para alterar a política de replicação?

Vá para **Cofre do Site Recovery** > **Infraestrutura do Site Recovery** > **Políticas de replicação**. Selecione a política que você deseja editar e salve as alterações. Qualquer alteração também será aplicada a todas as replicações existentes.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa da VM ou diferencial?

O primeiro ponto de recuperação gerado tem a cópia completa. Os pontos de recuperação sucessivos têm alterações delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Aumentar o período de retenção de pontos de recuperação aumenta o custo de armazenamento?

Sim, se você aumentar o período de retenção de 24 horas para 72 horas, o Site Recovery salvará os pontos de recuperação por mais 48 horas. Adicionar tempo gerará em encargos de armazenamento. Por exemplo, um ponto de recuperação pode ter alterações delta de 10 GB com um custo por GB de US$ 0,16 por mês. Os encargos adicionais seriam de US$ 1,60 × 48 por mês.


## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se estou fazendo failover no Azure, como posso acessar as VMs do Azure após o failover?

Você pode acessar as VMs do Azure em uma conexão segura da Internet, em uma VPN site a site ou na Azure ExpressRoute. Você precisa preparar uma série de coisas para se conectar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se eu fizer failover no Azure, como o Azure poderá garantir a resiliência dos meus dados?
O Azure foi desenvolvido para resiliência. O Site Recovery já foi projetado para failover em um datacenter do Azure secundário, de acordo com o SLA do Azure. Se isso acontecer, faremos com que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ao replicar entre dois datacenters, o que acontece se meu datacenter primário sofrer uma interrupção inesperada?
Você pode disparar um failover não planejado do site secundário. O Site Recovery não precisa de conectividade do local primário para fazer o failover.

### <a name="is-failover-automatic"></a>O failover é automático?
O failover não é automático. Você pode iniciar failovers com um único clique no portal ou pode usar o [PowerShell do Site Recovery](/powershell/module/az.recoveryservices) para disparar um failover. O failback é uma ação simples no portal do Site Recovery.

Para automatizar, você pode usar o Orchestrator ou o Operations Manager local para detectar a falha da máquina virtual e disparar o failover usando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
* [Saiba mais](site-recovery-failover.md) sobre failover.
* [Saiba mais](./vmware-azure-failback.md) como realizar failback das VMs VMware e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se o meu host local não está respondendo ou falha, é possível fazer failover de volta para um host diferente?
Sim, você pode usar a recuperação em uma localização alternativa para fazer failback para um host diferente do Azure.

* [Para máquinas virtuais VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Para máquinas virtuais Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>Qual é a diferença entre migração completa, confirmar e desabilitar a replicação?

Após o failover de um computador do local de origem para o local de destino, há três opções disponíveis para sua escolha. Todos os três servem para diferentes finalidades –

1.  A **migração completa** significa que você não vai mais para o local de origem. Você migrou para a região de destino e agora está pronto. Clicar em concluir migração dispara a confirmação e, em seguida, desabilita a replicação internamente. 
2.  **Confirmar** significa que esse não é o fim do processo de replicação. O item de replicação junto com todas as configurações permanecerá e você poderá clicar em **proteger novamente** em um momento posterior para habilitar a replicação de seus computadores de volta para a região de origem. 
3.  **Desabilitar a replicação** desabilitará a replicação e removerá todas as configurações relacionadas. Ele não afetará a máquina já existente na região de destino.

## <a name="automation"></a>Automação

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar os cenários do Site Recovery com um SDK?
Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Cenários com suporte no momento para implantar a Recuperação de Site usando o PowerShell:

* [Replicar VMs Hyper-V em nuvens VMM para o Azure PowerShell do Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replicar VMs Hyper-V sem VMM para o Azure PowerShell do Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replicar o VMware no Azure com o Gerenciador de Recursos do PowerShell](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Upgrade de componente/provedor

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Onde posso encontrar as notas sobre a versão/pacotes cumulativos do Azure Site Recovery?

[Saiba mais](site-recovery-whats-new.md) sobre novas atualizações e [informações de rollup](service-updates-how-to.md).

## <a name="next-steps"></a>Próximas etapas
* Leia a [visão geral do Site Recovery](site-recovery-overview.md)