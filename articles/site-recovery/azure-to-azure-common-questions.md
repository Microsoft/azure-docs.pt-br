---
title: Perguntas comuns sobre a recuperação de desastre de VM do Azure com o Azure Site Recovery
description: Este artigo responde a perguntas comuns sobre a recuperação de desastre de VM do Azure quando você usa o Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: d4fa7348df647cf699ebd55dd6415a79454ab5f1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397936"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Perguntas comuns: Recuperação de desastre do Azure para o Azure

Este artigo responde a perguntas comuns sobre a recuperação de desastre de VMs do Azure para outra região do Azure, usando o serviço de [Azure site Recovery](site-recovery-overview.md) .

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é o Site Recovery é precificado?

Saiba mais sobre [os custos](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) da recuperação de desastres de VM do Azure.

### <a name="how-does-the-free-tier-work"></a>Como funciona a camada gratuita?

Cada instância protegida com Site Recovery é gratuita para os primeiros 31 dias de proteção. Após esse período, a proteção para cada instância tem as tarifas resumidas nos [detalhes de preços](https://azure.microsoft.com/pricing/details/site-recovery/). Você pode estimar os custos usando a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=site-recovery).

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Eu incorre em outros encargos do Azure nos primeiros 31 dias?

Sim. Entretanto, embora o Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, você poderá ser cobrado pelo Armazenamento do Azure, por transações de armazenamento e por transferência de dados. Uma VM recuperada também pode incorrer em encargos de computação do Azure. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Como fazer introdução à recuperação de desastres de VM do Azure?

1. [Entenda](azure-to-azure-architecture.md) a arquitetura de recuperação de desastre da VM do Azure.
2. [Analisar](azure-to-azure-support-matrix.md) os requisitos de suporte.
3. [Configure](azure-to-azure-how-to-enable-replication.md) a recuperação de desastre para VMs do Azure.
4. [Execute uma análise de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md) com um failover de teste.
5. [Execute um failover completo](azure-to-azure-tutorial-failover-failback.md) para uma região secundária do Azure.
6. [Faça failback](azure-to-azure-tutorial-failback.md) da região secundária para a região primária.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Como garantir a capacidade na região de destino?

A equipe de Site Recovery e a equipe de gerenciamento de capacidade do Azure planejam a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery sejam implantadas na região de destino.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-with-disk-encryption"></a>Posso replicar VMs com criptografia de disco?

Sim. O Site Recovery dá suporte à recuperação de desastre de VMs que têm o Azure Disk Encryption (ADE) habilitado. Quando você habilita a replicação, o Azure copia Todas as chaves de criptografia de disco necessárias e os segredos da região de origem para a região de destino, no contexto do usuário. Se você não tiver as permissões necessárias, o administrador de segurança poderá usar um script para copiar as chaves e os segredos.

- O Site Recovery dá suporte a ADE para VMs do Azure que executam o Windows.
- O Site Recovery dá suporte a:
    - ADE versão 0,1, que tem um esquema que requer Azure Active Directory (Azure AD).
    - ADE versão 1,1, que não requer o Azure AD. Para a versão 1,1, as VMs do Windows Azure devem ter discos gerenciados.
    - [Saiba mais](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). sobre os esquemas de extensão.

[Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md) sobre como habilitar a replicação de VMs criptografadas.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Posso selecionar uma conta de automação de um grupo de recursos diferente?

Quando você permite que Site Recovery gerencie atualizações para a extensão do serviço de mobilidade em execução em VMs do Azure replicadas, ele implanta um runbook global (usado pelos serviços do Azure), por meio de uma conta de automação do Azure. Você pode usar a conta de automação que o Site Recovery cria ou selecionar para usar uma conta de automação existente. 

No momento, no portal, você só pode selecionar uma conta de automação no mesmo grupo de recursos que o cofre. Você pode selecionar uma conta de automação de um grupo de recursos diferente usando o PowerShell. [Saiba mais](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Se eu usar uma conta de automação do cliente que não está no grupo de recursos do cofre, posso excluir o runbook padrão?

Sim, você pode excluí-lo se não precisar dele. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>É possível replicar VMs para outra assinatura?

Sim, você pode replicar VMs do Azure para qualquer assinatura dentro do mesmo locatário do Azure AD. Quando você habilita a recuperação de desastre para VMs, por padrão, a assinatura de destino mostrada é a da VM de origem. Você pode modificar a assinatura de destino e outras configurações (como grupo de recursos e rede virtual) são preenchidas automaticamente da assinatura selecionada.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Posso replicar VMs em uma zona de disponibilidade para outra região?

Sim, você pode replicar VMs em zonas de disponibilidade para outra região do Azure. A VM de destino pode ser implantada como uma única instância, em um conjunto de disponibilidade ou em uma zona de disponibilidade, se houver suporte na região de destino. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Posso replicar VMs que não são de zona para uma zona na mesma região? 

Isso não tem suporte no Portal. Você pode usar a API REST/PowerShell para fazer isso.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Posso replicar VMs zoneada para uma zona diferente na mesma região?

O suporte para isso é limitado a algumas regiões. [Saiba mais](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>Posso excluir discos da replicação?

Sim, você pode excluir discos ao configurar a replicação usando o PowerShell. [Saiba mais](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Posso replicar novos discos adicionados às VMs replicadas?

Para VMs replicadas com discos gerenciados, você pode adicionar novos discos e habilitar a replicação para eles. Quando você adiciona um novo disco, a VM replicada mostra uma mensagem de aviso de que um ou mais discos na VM estão disponíveis para proteção. 

- Se você habilitar a replicação para os discos adicionados, o aviso desaparecerá após a replicação inicial.
- Se não quiser habilitar a replicação para o disco, você poderá ignorar o aviso.
- Se você fizer failover de uma VM com discos adicionados, os pontos de replicação mostrarão os discos disponíveis para recuperação. Por exemplo, se você adicionar um segundo disco a uma VM com um disco, um ponto de replicação criado antes de você adicionar aparecerá como "1 de 2 discos".

Site Recovery não dá suporte a "remoção a quente" de discos de uma VM replicada. Se você remover um disco de VM, será necessário desabilitar e reabilitar a replicação para a VM.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?

A replicação é contínua ao replicar VMs do Azure para outra região do Azure. [Saiba mais](./azure-to-azure-architecture.md#replication-process) sobre como a replicação funciona.

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Posso replicar máquinas virtuais dentro de uma região? 

Você não pode usar Site Recovery para replicar discos em uma região.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>É possível replicar instâncias de VM em qualquer região do Azure?

É possível replicar e recuperar VMs entre duas regiões quaisquer dentro do mesmo cluster geográfico. Clusters geográficos são definidos pensando em latência de dados e soberania. [Saiba mais](./azure-to-azure-support-matrix.md#region-support) sobre o suporte de região.

### <a name="does-site-recovery-need-internet-connectivity"></a>Site Recovery precisa de conectividade com a Internet?

Não, mas as VMs precisam acessar Site Recovery URLs e intervalos de IP. [Saiba mais](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Posso replicar um aplicativo em camadas entre grupos de recursos?

Sim, você pode replicar o aplicativo e manter a configuração de recuperação de desastre em um grupo de recursos separado.

Por exemplo, se os aplicativos tiverem três camadas (aplicativo/banco de dados/Web) em grupos de recursos diferentes, você precisará habilitar a replicação três vezes para proteger todas as camadas. Site Recovery Replica as três camadas em três grupos de recursos diferentes.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>É possível mover contas de armazenamento entre grupos de recursos?

Não, não há suporte para isso. Se você acidentalmente mover contas de armazenamento para um grupo de recursos diferente e excluir o grupo de recursos original, poderá criar um novo grupo de recursos com o mesmo nome do grupo de recursos antigo e, em seguida, mover a conta de armazenamento para esse grupo de recursos.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma política de replicação define o histórico de retenção de pontos de recuperação e a frequência de instantâneos consistentes com o aplicativo.  Site Recovery cria uma política de replicação padrão da seguinte maneira:

- Mantenha os pontos de recuperação por 24 horas.
- Pegue instantâneos consistentes com o aplicativo a cada quatro horas.

[Saiba mais](azure-to-azure-how-to-enable-replication.md#customize-target-resources) sobre as configurações de replicação.

### <a name="whats-a-crash-consistent-recovery-point"></a>O que é um ponto de recuperação consistente com falhas?

Um ponto de recuperação consistente com falhas contém dados em disco, como se você tiver recebido o cabo de alimentação do servidor durante o instantâneo. Ele não inclui nada que estava na memória quando o instantâneo foi tirado.

Hoje, a maioria dos aplicativos pode se recuperar bem de instantâneos consistentes com falhas. Um ponto de recuperação consistente com falhas geralmente é suficiente para sistemas operacionais que não são de banco de dados e aplicativos como servidores de arquivos, servidores DHCP e servidores de impressão.

Site Recovery cria automaticamente um ponto de recuperação consistente com falhas a cada cinco minutos.

### <a name="whats-an-application-consistent-recovery-point"></a>O que é um ponto de recuperação consistente com o aplicativo?

Os pontos de recuperação consistentes com aplicativo são criados com base nos instantâneos consistentes com aplicativo. Eles capturam os mesmos dados como instantâneos consistentes com falhas e, além disso, capturam dados na memória e todas as transações em andamento.

Devido ao conteúdo extra, os instantâneos consistentes com o aplicativo são os mais envolvidos e são mais longos. Recomendamos pontos de recuperação consistentes com o aplicativo para sistemas operacionais de banco de dados e aplicativos como SQL Server. Para o Windows, os instantâneos consistentes com o aplicativo usam o Serviço de Cópias de Sombra de Volume (VSS).

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Os pontos de recuperação consistentes com o aplicativo impactam o desempenho?

 Como os pontos de recuperação consistentes com o aplicativo capturam todos os dados na memória e no processo, se forem capturados com frequência, isso poderá afetar o desempenho quando a carga de trabalho já estiver ocupada. Não recomendamos que você capture com muita frequência para cargas de trabalho que não sejam de banco de dados. Mesmo para cargas de trabalho de banco de dados, uma hora deve ser suficiente.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Qual é a frequência mínima para gerar pontos de recuperação consistentes com o aplicativo?

Site Recovery pode criar pontos de recuperação consistentes com o aplicativo com uma frequência mínima de uma hora.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Posso habilitar a replicação consistente com o aplicativo para VMs do Linux?

Sim. O agente de mobilidade para Linux dá suporte a scripts personalizados para consistência de aplicativo. Um script personalizado com pré e pós-opções é usado pelo agente. [Saiba mais](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Como os pontos de recuperação são gerados e salvos?

Para entender como Site Recovery gera pontos de recuperação, vamos usar um exemplo. 

- Uma política de replicação retém os pontos de recuperação por 24 horas e leva um instantâneo de frequência consistente com o aplicativo a cada hora.
- Site Recovery cria um ponto de recuperação consistente com falhas a cada cinco minutos. Você não pode alterar essa frequência.
- Site Recovery remove os pontos de recuperação após uma hora, salvando um ponto por hora.

Portanto, na última hora, você pode escolher entre 12 pontos consistentes com falhas e um ponto consistente com o aplicativo, como mostrado no gráfico.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?

O ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>O que acontece se Site Recovery não puder gerar pontos de recuperação por mais de 24 horas? 

Se você tiver uma política de replicação de 24 horas e Site Recovery não puder gerar pontos de recuperação por mais de 24 horas, os pontos de recuperação antigos permanecerão. Site Recovery substituirá apenas o ponto mais antigo se ele gerar novos pontos. Até que haja novos pontos de recuperação, todos os pontos antigos permanecerão depois que você chegar à janela de retenção.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Posso alterar a política de replicação depois que a replicação estiver habilitada?

Sim. No cofre > políticas de replicação de **infraestrutura site Recovery**  >  **Replication policies** , selecione e edite a política. As alterações se aplicam também a políticas existentes.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Todos os pontos de recuperação são uma cópia de VM completa?

O primeiro ponto de recuperação gerado tem a cópia completa. Pontos de recuperação sucessivos têm alterações delta.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>As aumentos na retenção do ponto de recuperação aumentam os custos de armazenamento?

Sim. Por exemplo, se você aumentar a retenção de 24 horas para 72, Site Recovery salvará os pontos de recuperação por um adicional de 48 horas. O tempo adicionado provoca alterações de armazenamento. Somente como exemplo, se um único ponto de recuperação tiver alterações delta de 10 GB, com um custo por GB de $0.16 por mês, os encargos adicionais serão $1.60 × 48 por mês.

## <a name="multi-vm-consistency"></a>Consistência de várias VMs

### <a name="what-is-multi-vm-consistency"></a>O que é consistência de várias VMs?

A consistência de várias VMs garante que os pontos de recuperação sejam consistentes entre as máquinas virtuais replicadas.

- Quando você habilita a consistência de várias VMS, o Site Recovery cria um grupo de replicação de todos os computadores com a opção habilitada. 
- Quando você faz failover dos computadores no grupo de replicação, eles têm pontos de recuperação consistentes com o aplicativo e com falhas.

[Saiba](azure-to-azure-tutorial-enable-replication.md#enable-replication) como habilitar a consistência de várias VMS.

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Posso fazer failover de uma única VM em um grupo de replicação?

Não. Quando você habilita a consistência de várias VMS, ela infere que um aplicativo tem uma dependência em todas as VMs no grupo de replicação, e o failover de VM única não é permitido.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Quantas VMs posso replicar juntas em um grupo?

Você pode replicar 16 VMs juntas em um grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando habilitar a consistência de várias VMs?

A consistência de várias VMs tem uso intensivo de CPU e a habilitação pode afetar o desempenho da carga de trabalho. Habilitar somente se as VMs estiverem executando a mesma carga de trabalho e você precisar de consistência em vários computadores. Por exemplo, se você tiver duas instâncias de SQL Server e dois servidores Web em um aplicativo, habilite a consistência de várias VMs somente para as instâncias de SQL Server.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Posso adicionar uma VM replicando a um grupo de replicação?

Ao habilitar a replicação para uma VM, você pode adicioná-la a um novo grupo de replicação ou a um grupo existente. Você não pode adicionar uma VM que já está sendo replicada para um grupo. 
 
## <a name="failover"></a>Failover

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Como garantir a capacidade na região de destino?

A equipe de Site Recovery e a equipe de gerenciamento de capacidade do Azure planejam a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery possam ser implantadas na região de destino.

### <a name="is-failover-automatic"></a>O failover é automático?

O failover não é automático. Você pode iniciar um failover com um único clique no portal ou usar o  [PowerShell](azure-to-azure-powershell.md) para disparar um failover.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Posso manter um endereço IP público após o failover?

Você não pode manter o endereço IP público para um aplicativo de produção após um failover.

Ao abrir uma carga de trabalho como parte do processo de failover, você precisa atribuir um recurso de endereço IP público do Azure a ela. O recurso deve estar disponível na região de destino. Você pode atribuir o recurso de endereço IP público do Azure manualmente ou pode automatizá-lo com um plano de recuperação. [Saiba](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) como configurar endereços IP públicos após o failover.

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Posso manter um endereço IP privado após o failover?

Sim. Por padrão, quando você habilita a recuperação de desastre para VMs do Azure, Site Recovery cria recursos de destino com base nas configurações de recurso de origem. Para VMs do Azure configuradas com endereços IP estáticos, o Site Recovery tentará provisionar o mesmo endereço IP para a VM de destino se ele não estiver em uso.
[Saiba mais sobre como](site-recovery-retain-ip-azure-vm-failover.md) manter os endereços IP após o failover.

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Por que uma VM atribuiu um novo endereço IP após o failover?

O Site Recovery tenta fornecer o endereço IP no momento do failover. Se outra VM usar esse endereço, Site Recovery definirá o próximo endereço IP disponível como o destino.

[Saiba mais sobre como](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) configurar o mapeamento de rede e o endereçamento IP para redes virtuais.

### <a name="whats-the-latest-recovery-point"></a>Qual é o ponto de recuperação *mais recente* ?

A opção de ponto de recuperação *mais recente (menor RPO)* faz o seguinte:

1. Primeiro, ele processa todos os dados que foram enviados para Site Recovery.
2. Depois que o serviço processa os dados, ele cria um ponto de recuperação para cada VM, antes de fazer failover para a VM. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo.
3. A VM criada após o failover tem todos os dados replicados para Site Recovery, de quando o failover foi disparado.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>Os pontos de recuperação *mais recentes* afetam o RTO do failover?

Sim. O Site Recovery processa todos os dados pendentes antes do failover; portanto, essa opção tem um RTO (objetivo de tempo de recuperação) maior do que outras opções.

### <a name="whats-the-latest-processed-recovery-option"></a>O que é a opção de recuperação *mais recente processada* ?

A opção *mais recente processada* faz o seguinte:

1. Ele executa failover de todas as VMs para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO baixo, pois nenhum tempo é gasto no processamento de dados não processados.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>E se houver uma interrupção inesperada na região primária?

Você pode iniciar o failover. O Site Recovery não precisa de conectividade da região primária para fazer failover.

### <a name="what-is-the-rto-of-a-vm-failover"></a>Qual é o RTO de um failover de VM?

Site Recovery tem um SLA de RTO de [duas horas](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Na maioria das vezes, Site Recovery executa failover de VMs em minutos. Para calcular o RTO, examine o trabalho de failover, que mostra o tempo necessário para abrir uma VM. 

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="whats-a-recovery-plan"></a>O que é um plano de recuperação?

Um [plano de recuperação](site-recovery-create-recovery-plans.md) no site Recovery orquestra o failover e a recuperação de VMs. Ele ajuda a tornar a recuperação consistentemente precisa, repetível e automatizada. Ele faz o seguinte:

- Define um grupo de VMs que fazem failover juntas
- Define as dependências entre as VMs, para que o aplicativo apareça com precisão.
- Automatiza a recuperação, com a opção de ações manuais personalizadas para tarefas diferentes do failover de VM. 


### <a name="how-does-sequencing-work"></a>Como funciona o sequenciamento?

Em um plano de recuperação, você pode criar vários grupos de VMs para sequenciamento. Os grupos falham por um de cada vez, para que as VMs que fazem parte do mesmo grupo façam failover juntas. [Saiba mais](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como localizar o RTO de um plano de recuperação?

Para verificar o RTO de um plano de recuperação, faça um failover de teste para o plano de recuperação. Em **site Recovery trabalhos** , verifique a duração do failover de teste. Na captura de tela de exemplo, o trabalho de failover de teste **SAPTestRecoveryPlan** levou 8 minutos e 59 segundos.

![Listar trabalhos que mostram a duração do failover de teste para RTO](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Posso adicionar runbooks de automação aos planos de recuperação?

Sim. [Saiba mais](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Nova proteção e failback

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Após o failover, as VMs na região secundária são protegidas automaticamente? 

Não. Quando você faz failover de VMs de uma região para outra, as VMs são iniciadas na região de recuperação de desastre de destino em um estado desprotegido. Para [proteger](./azure-to-azure-how-to-reprotect.md) novamente as VMs na região secundária, você habilita a replicação de volta para a região primária.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Quando protejo novamente, todos os dados são replicados da região secundária para a primária? 

Depende. Se a VM da região de origem existir, somente as alterações entre o disco de origem e o disco de destino serão sincronizadas. Site Recovery compara os discos com o que é diferente e, em seguida, transfere os dados. Esse processo normalmente leva algumas horas. [Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>Quanto tempo leva failback?

Após a nova proteção, o failback assume a mesma quantidade de tempo que levou para fazer failover da região primária para uma região secundária.

## <a name="capacity"></a><a name="capacity"></a>Capacidade

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Como garantir a capacidade na região de destino?

A equipe do Site Recovery e equipe de gerenciamento de capacidade do Azure planejam a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery possam ser implantadas na região de destino.

### <a name="does-site-recovery-work-with-reserved-instances"></a>O Site Recovery funciona com instâncias reservadas?

Sim, você pode comprar [VMs do Azure reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) na região de recuperação de desastre e site Recovery as operações de failover as usam. Nenhuma configuração adicional é necessária.

## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?

Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está sendo executado nas VMs. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

O Site Recovery tem os certificados ISO 27001:2013, 27018, HIPAA e DPA. O serviço está passando por avaliações SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?

Sim, a criptografia em trânsito e a [em repouso](../storage/common/storage-service-encryption.md) são compatíveis com o Azure.

## <a name="next-steps"></a>Próximas etapas

- [Examine os requisitos de compatibilidade de Azure para Azure](azure-to-azure-support-matrix.md).
- [Configurar a replicação de Azure para Azure](azure-to-azure-tutorial-enable-replication.md).
- Se você tiver dúvidas após a leitura deste artigo, publique-as na [página de perguntas P e R da Microsoft dos Serviços de Recuperação do Azure](/answers/topics/azure-site-recovery.html).
