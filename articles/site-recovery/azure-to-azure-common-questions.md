---
title: 'Perguntas comuns: Recuperação de desastre de Azure para Azure com o Azure Site Recovery | Microsoft Docs'
description: Este artigo resume as perguntas comuns ao configurar a recuperação de desastre de VMs do Azure para outra região do Azure usando o Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 03/29/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 52a5022b49bac990321c3cf8661aa2a04e93b39a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790839"
---
# <a name="common-questions-azure-to-azure-replication"></a>Perguntas comuns: Replicação de Azure para Azure

Este artigo fornece respostas a perguntas comuns sobre como implantar a DR (recuperação de desastres) de VMs do Azure para outra região do Azure usando o Azure Site Recovery. Se você tiver dúvidas após a leitura deste artigo, publique-as no [fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é o Site Recovery é precificado?
Analise os detalhes em [Preços do Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Como funciona a camada gratuita do Azure Site Recovery?
Cada instância protegida pelo Azure Site Recovery é gratuita durante os primeiros 31 dias de proteção. A partir do 32º dia, a proteção da instância é cobrada de acordo com as taxas acima.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, serei cobrado por outras tarifas do Azure?
Sim, embora o Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, você poderá ser cobrado pelo Armazenamento do Azure, por transações de armazenamento e por transferência de dados. Uma máquina virtual recuperada também poderá gerar encargos de computação do Azure. Obtenha detalhes completos sobre preços [aqui](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="what-are-the-best-practices-for-configuring-site-recovery-on-azure-vms"></a>Quais são as melhores práticas para configurar o Site Recovery em VMs do Azure?
1. [Entender a arquitetura de Azure para Azure](azure-to-azure-architecture.md)
2. [Examinar as configurações com suporte e sem suporte](azure-to-azure-support-matrix.md)
3. [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-how-to-enable-replication.md)
4. [Executar um failover de teste](azure-to-azure-tutorial-dr-drill.md)
5. [Fazer failover e failback para a região primária](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-target-region-for-azure-vms"></a>Como a capacidade é garantia na região de destino para VMs do Azure?
A equipe do Azure Site Recovery (ASR) funciona com a equipe de gerenciamento de capacidade do Azure para planejar a capacidade de infraestrutura suficiente, em uma tentativa para garantir que as VMs protegidas pelo ASR para o desastre recuperação com êxito será implantada na região de DR (recuperação) de desastre, sempre que as operações de failover do ASR são iniciadas.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Posso replicar VMs habilitadas por meio da criptografia de disco do Azure?
Sim, é possível replicá-las. Veja o artigo [Replicar máquinas virtuais habilitadas para criptografia de disco do Azure para outra região do Azure](azure-to-azure-how-to-enable-replication-ade-vms.md). Atualmente, o Azure Site Recovery dá suporte a somente a VMs do Azure que estão executando um sistema de operacional do Windows e habilitadas para criptografia com aplicativos do Azure AD (Azure Active Directory).

### <a name="can-i-replicate-vms-to-another-subscription"></a>É possível replicar VMs para outra assinatura?
Sim, você pode replicar VMs do Azure para uma assinatura diferente no mesmo locatário do Azure AD.
Configurar a recuperação de desastre [entre assinaturas](https://azure.microsoft.com/blog/cross-subscription-dr) é simples. É possível selecionar outra assinatura no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Posso replicar VMs do Azure fixadas em zonas para outra região?
Sim, é possível [replicar VMs fixadas em zonas](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) para outra região.

### <a name="can-i-exclude-disks"></a>É possível excluir discos?

Sim, é possível excluir discos no momento da proteção usando o PowerShell. Para obter mais informações, consulte [artigo](azure-to-azure-exclude-disks.md)

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?
A replicação é contínua quando você está replicando VMs do Azure para outra região do Azure. Para saber mais, veja [Arquitetura de replicação do Azure para o Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Posso replicar máquinas virtuais dentro de uma região? Preciso que isso migre as VMs.
Não é possível usar uma solução de DR de Azure para Azure para replicar VMs dentro de uma região.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>É possível replicar VMs para qualquer região do Azure?
Com o Site Recovery, é possível replicar e recuperar VMs entre quaisquer duas regiões dentro do mesmo cluster geográfico. Clusters geográficos são definidos pensando em latência de dados e soberania. Para obter mais informações, veja [Matriz de suporte regional](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) do Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>O Site Recovery requer conectividade com a Internet?

Não, o Site Recovery não requer conectividade com a Internet. Porém, ele requer acesso aos intervalos de IP e URLs do Site Recovery, conforme mencionado [neste artigo](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>É possível replicar o aplicativo que tem um grupo de recursos separado para camadas separadas?
Sim, é possível replicar o aplicativo e manter a configuração de recuperação de desastre em um grupo de recursos separado também.
Por exemplo, se você tiver um aplicativo com aplicativo, banco de dados e Web de cada camada no grupo de recursos separado, será necessário clicar no [assistente de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) três vezes para proteger todas as camadas. O ASR replicará essas três camadas em três grupos de recursos diferentes.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?
Ela define as configurações para o histórico de retenção de pontos de recuperação e a frequência dos instantâneos consistentes em termos de aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de:

* 24 horas para o histórico de retenção dos pontos de recuperação.
* 60 minutos para a frequência dos instantâneos consistentes em termos de aplicativo.

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de falha?
Um ponto de recuperação consistente em termos de falha representa os dados em disco, como se a VM falhou ou o cabo de alimentação foi retirado do servidor no momento em que o instantâneo foi tirado. Ele não inclui tudo o que estava na memória quando o instantâneo foi tirado.

Atualmente, a maioria dos aplicativos pode recuperar-se bem de instantâneos consistente em termos de falha. Um ponto de recuperação consistente em termos de falha geralmente é suficiente para sistemas operacionais sem banco de dados e aplicativos como servidores, servidores DHCP, servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência de geração de ponto de recuperação consistente com a falha?
O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de aplicativo?
Pontos de recuperação consistentes em termos de aplicativo são criados com base em instantâneos consistentes em termos de aplicativo. Pontos de recuperação consistentes em termos de aplicativo capturam os mesmos dados de instantâneos consistentes em termos de falhas, além de todos os dados na memória e todas as transações em andamento.
Devido a seu conteúdo extra, instantâneos de aplicativo consistente são os mais envolvidos e levam mais tempo para executar. Recomendamos pontos de recuperação consistentes em termos de aplicativo para sistemas operacionais de banco de dados como o SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto de pontos de recuperação consistentes em termos de aplicativo no desempenho do aplicativo?
Considerando que os pontos de recuperação consistentes em termos de aplicativo capturam todos os dados na memória e no processo, isso requer que a estrutura como VSS na janela desative o aplicativo. Isso, se feito com muita frequência, poderá ter um impacto no desempenho se a carga de trabalho já estiver muito ocupada. Geralmente, sugerimos não usar baixa frequência para pontos de recuperação consistentes em termos de aplicativo para cargas de trabalho que não são de banco de dados e, inclusive para a carga de trabalho de banco de dados, 1 hora é suficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima de geração de ponto de recuperação consistente com o aplicativo?
Recuperação de site pode cria um ponto de recuperação consistente de aplicativo com uma frequência mínima de em 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como os pontos de recuperação são gerados e salvos?
Para entender como o Site Recovery gera pontos de recuperação, vamos examinar um exemplo da política de replicação que tem uma janela de retenção de ponto de recuperação de 24 horas e um instantâneo de frequência consistente em termos de aplicativo de 1 hora.

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos. O usuário não pode alterar essa frequência. Assim, durante a última uma hora, o usuário terá 12 pontos consistentes em termos de falha e 1 ponto consistentes em termos de aplicativo para escolher. Conforme o tempo decorre, o Site Recovery remove todos os pontos de recuperação além da última 1 hora e salva apenas um ponto de recuperação por hora.

A captura de tela a seguir ilustra o exemplo. Na captura de tela:

1. Para o período inferior à última 1 hora, há pontos de recuperação com uma frequência de 5 minutos.
2. Para um tempo além da última 1 hora, o Site Recovery mantém apenas um ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?
O ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>O que acontecerá se eu tiver uma política de replicação de 24 horas e um problema impedir o Site Recovery de gerar pontos de recuperação por mais de 24 horas? Meus pontos de recuperação anteriores serão perdidos?
Não, o Site Recovery manterá todos os pontos de recuperação anteriores. Dependendo da janela de retenção de pontos de recuperação, 24 horas neste caso, o Site Recovery substituirá o ponto mais antigo somente se houver uma geração de novos pontos. Neste caso, uma vez que não há nenhum novo ponto de recuperação gerado devido a algum problema, todos os pontos antigos permanecerão intactos quando chegarmos à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Após a replicação ser habilitada em uma VM, como fazer para alterar a política de replicação?
Vá para **Cofre do Site Recovery** > **Infraestrutura do Site Recovery** > **Políticas de replicação**. Selecione a política que você deseja editar e salve as alterações. Qualquer alteração também será aplicada a todas as replicações existentes.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa da VM ou diferencial?
O primeiro ponto de recuperação gerado tem a cópia completa. Os pontos de recuperação sucessivos têm alterações delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Aumentar o período de retenção de pontos de recuperação aumenta o custo de armazenamento?
Sim. Se você aumentar o período de retenção de 24 horas a 72 horas, o Site Recovery salvará os pontos de recuperação por mais 48 horas. Adicionar tempo gerará em encargos de armazenamento. Por exemplo se um ponto de recuperação único tem alterações delta de 10 GB e custo por GB é de US $0,16 por mês, os encargos adicionais seriam de US$ 1,6 * 48 por mês.

## <a name="multi-vm-consistency"></a>Consistência de várias VMs

### <a name="what-is-multi-vm-consistency"></a>O que é consistência de várias VMs?
Isso significa verificar se o ponto de recuperação é consistente em todas as máquinas virtuais replicadas.
O Site Recovery fornece uma opção de "Consistência de várias VMs" que, quando você seleciona, cria um grupo de replicação para replicar todos os computadores que fazem parte do grupo.
Todas as máquinas virtuais terão pontos de recuperação consistentes em termos de aplicativo e falha quando passaram por failover.
Veja o tutorial para [habilitar a consistência de várias VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Posso realizar o failover de uma única máquina virtual dentro de um grupo de replicação de consistência de várias VMs?
Ao selecionar a opção "Consistência de várias VMs", você está dizendo que o aplicativo tem uma dependência e todas as máquinas virtuais dentro de um grupo. Portanto, o failover de única máquina virtual não é permitido.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quantas máquinas virtuais posso replicar como parte de um grupo de replicação de consistência de várias VMs?
Você pode replicar 16 máquinas virtuais ao mesmo tempo em um grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Quando devo habilitar a consistência de várias VMs?
Uma vez que isso faz uso intenso da CPU, habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho. Ela deverá ser usada somente se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência entre vários computadores. Por exemplo, se você tiver duas instâncias do SQL Server e dois servidores Web em um aplicativo, deverá ter consistência de várias VMs somente para as instâncias do SQL Server.


## <a name="failover"></a>Failover

### <a name="how-is-capacity-guaranteed-in-target-region-for-azure-vms"></a>Como a capacidade é garantia na região de destino para VMs do Azure?
A equipe do Azure Site Recovery (ASR) funciona com a equipe de gerenciamento de capacidade do Azure para planejar a capacidade de infraestrutura suficiente, em uma tentativa para garantir que as VMs protegidas pelo ASR para o desastre recuperação com êxito será implantada na região de DR (recuperação) de desastre, sempre que as operações de failover do ASR são iniciadas.

### <a name="is-failover-automatic"></a>O failover é automático?

O failover não é automático. Você inicia failovers com um único clique no portal ou usando o [PowerShell](azure-to-azure-powershell.md) para disparar um failover.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>É possível reter um endereço IP público após o failover?

O endereço IP público do aplicativo de produção *não pode ser retido em failover*. As cargas de trabalho colocadas em operação como parte do processo de failover devem receber um recurso de IP público do Azure disponível na região de destino. Você pode executar essa etapa manualmente ou automatizá-la por meio de um plano de recuperação. Para atribuir um endereço IP público por meio de um plano de recuperação, veja [Configurar endereços IP públicos após o failover](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Posso reter um endereço IP privado durante o failover?
Sim, é possível reter o endereço IP privado. Por padrão, quando você habilita a DR para VMs do Azure, o Site Recovery cria recursos de destino com base nas configurações do recurso de origem. Para VMs do Azure configuradas com endereços IP estáticos, o Site Recovery tentará provisionar o mesmo endereço IP para a VM de destino se ele não estiver em uso. Para reter o endereço IP privado em diferentes condições, veja [Reter endereços IP durante o failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-the-server-doesnt-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-a-new-ip-address"></a>Após o failover, o servidor não tem o mesmo endereço IP que a VM de origem. Por que é atribuído um novo endereço IP a ele?

O Site Recovery tenta fornecer o endereço IP no momento do failover. Se outra máquina virtual estiver usando esse endereço, o Site Recovery definirá o próximo endereço IP disponível como destino.
Para obter uma explicação completa de como o Site Recovery trata endereçamento, veja [Configurar o mapeamento de rede e endereçamento IP para redes virtuais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Quais são os pontos de recuperação **Mais recentes (menor RPO)**?
A opção **Mais recentes (menor RPO)** primeiro processa todos os dados enviados ao serviço do Site Recovery para criar um ponto de recuperação para cada VM antes de fazer failover para ela. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM criada após o failover tem todos os dados replicados para o Site Recovery de quando o failover foi disparado.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Os pontos de recuperação **Mais recentes (menor RPO)** afetam o RTO do failover?
Sim. O Site Recovery processa todos os dados pendentes antes de fazer failover, portanto, essa opção tem um RTO (objetivo de tempo de recuperação) mais alto em comparação a outras opções.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>O que significa a opção **Últimos processados** nos pontos de recuperação?
A opção **Últimos processados** executa failover de todas as VMs no plano para o ponto de recuperação mais recente que o Site Recovery processou. Para ver o último ponto de recuperação de uma VM específica, verifique os **Pontos de Recuperação Mais Recentes** nas configurações da VM. Essa opção fornece um RTO baixo, pois nenhum tempo é gasto no processamento de dados não processados.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Se estiver replicando entre duas regiões do Azure, o que acontece se minha região primária passar por uma interrupção inesperada?
É possível disparar um failover após a interrupção. O Site Recovery não precisa de conectividade da região primária para realizar o failover.

### <a name="what-is-a-rto-of-a-virtual-machine-failover-"></a>O que é um RTO de um failover de máquina virtual?
O Site Recovery tem um [SLA de RTO de 2 horas](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). No entanto, a maioria das vezes, o Site Recovery faz failover de máquinas virtuais em minutos. É possível calcular o RTO acessando os trabalhos de failover, que mostram o tempo necessário para criar a VM. Para a RTO do plano de recuperação, veja a seção abaixo.

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="what-is-a-recovery-plan"></a>O que é um plano de recuperação?
Um plano de recuperação no Site Recovery orquestra a recuperação de failover de VMs. Os planos de recuperação também ajudam a tornar a recuperação precisa, repetível e automatizada de forma consistente. Um plano de recuperação atende às seguintes necessidades do usuário:

- Definir um grupo de máquinas virtuais que fazem failover em conjunto
- Definir as dependências entre as máquinas virtuais para que o aplicativo seja executado com precisão
- Automatizar a recuperação juntamente com ações manuais personalizadas para realizar tarefas que não o failover de máquinas virtuais

[Saiba mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Como o sequenciamento é feito em um plano de recuperação?

Em um plano de recuperação, é possível criar vários grupos para realizar o sequenciamento. Cada grupo faz o failover ao mesmo tempo. As VMs que fazem parte do mesmo grupo fazem fazer failover juntas, seguidas por outro grupo. Para saber como modelar um aplicativo usando um plano de recuperação, veja [Sobre planos de recuperação](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como localizar o RTO de um plano de recuperação?
Para verificar o RTO de um plano de recuperação, faça um failover de teste para o plano de recuperação e vá para **trabalhos do Site Recovery**.
No exemplo a seguir, o trabalho chamado SAPTestRecoveryPlan levou 8 minutos e 59 segundos para fazer failover de todas as máquinas virtuais e realizar ações especificadas.

![Lista de trabalhos do Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>É possível adicionar runbooks de automação ao plano de recuperação?
Sim, é possível integrar runbooks de Automação do Azure ao plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Nova proteção e failback

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Após um failover da região primária para uma região de recuperação de desastre, as VMs em uma região de DR são protegidas automaticamente?
Não. Ao fazer [failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) das VMs do Azure de uma região para outra, as VMs iniciam na região de DR em um estado desprotegido. Para fazer failback das VMs na região primária, é necessário [proteger novamente](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) as VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>No momento da nova proteção, o Site Recovery replica dados completos da região secundária para a região primária?
Depende da situação. Por exemplo, se a região de origem da VM existir, apenas as alterações entre o disco de origem e o disco de destino serão sincronizadas. O Site Recovery calcula os diferenciais comparando os discos e, em seguida, transfere os dados. Esse processo normalmente leva algumas horas. Para obter mais informações sobre o que acontece durante a nova proteção, veja [Nova proteção de VMs do Azure com fazer failover para a região primária]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo demora para fazer failback?
Após a nova proteção, o tempo para failback geralmente é similar ao tempo para failover da região primária para uma região secundária.

## <a name="capacity"></a>capacidade

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Como capacidade garantida na região de destino para máquinas virtuais do Azure?
A equipe do Azure Site Recovery (ASR) funciona com a equipe de gerenciamento de capacidade do Azure para planejar a capacidade de infraestrutura suficiente, em uma tentativa para garantir que as VMs protegidas pelo ASR para o desastre recuperação com êxito será implantada na região de DR (recuperação) de desastre, sempre que as operações de failover do ASR são iniciadas.

### <a name="does-site-recovery-work-with-reserved-instances"></a>O Site Recovery funciona com as instâncias reservadas?
Sim, você pode comprar [reservar instâncias](https://azure.microsoft.com/pricing/reserved-vm-instances/) a recuperação de Desastre região e operações de failover do ASR usará-los. </br> Nenhuma configuração adicional é necessária dos clientes.


## <a name="security"></a>Segurança
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está sendo executado nas máquinas virtuais. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  
O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?
Sim, tanto criptografia em trânsito como [criptografia no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) têm suporte.

## <a name="next-steps"></a>Próximas etapas
* [Analisar](azure-to-azure-support-matrix.md) os requisitos de suporte.
* [Configurar](azure-to-azure-tutorial-enable-replication.md) replicação de Azure para Azure.
