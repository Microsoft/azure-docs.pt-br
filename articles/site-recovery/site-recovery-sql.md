---
title: Configurar a recuperação de desastres para o SQL Server com o SQL Server e o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a recuperação de desastre para SQL Server usando SQL Server e Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 7ee7d6434058da63883f8db0eae6a3f91c778338
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325123"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurar a recuperação de desastres para o SQL Server

Este artigo descreve como ajudar a proteger o back-end de SQL Server de um aplicativo. Você faz isso usando uma combinação de tecnologias SQL Server de BCDR (continuidade de negócios e recuperação de desastre) e [Azure site Recovery](site-recovery-overview.md).

Antes de começar, verifique se você entendeu SQL Server recursos de recuperação de desastres. Esses recursos incluem:

* Clustering de failover
* Grupos de disponibilidade AlwaysOn
* Espelhamento de banco de dados
* Envio de logs
* Replicação geográfica ativa
* Grupos de failover automático

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Combinando tecnologias BCDR com Site Recovery

Sua escolha de uma tecnologia BCDR para recuperar SQL Server instâncias deve ser baseada em seu RTO (objetivo de tempo de recuperação) e nas necessidades de RPO (objetivo de ponto de recuperação), conforme descrito na tabela a seguir. Combine Site Recovery com a operação de failover de sua tecnologia escolhida para orquestrar a recuperação de todo o seu aplicativo.

Tipo de implantação | Tecnologia BCDR | RTO esperado para SQL Server | RPO esperado para SQL Server |
--- | --- | --- | ---
SQL Server em uma VM (máquina virtual) de IaaS (infraestrutura como serviço) do Azure ou no local.| [Grupo de disponibilidade Sempre Ativo](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | O tempo necessário para tornar a réplica secundária como primária. | Como a replicação para a réplica secundária é assíncrona, há alguma perda de dados.
SQL Server em uma VM IaaS do Azure ou no local.| [Clustering de failover (FCI AlwaysOn)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | O tempo necessário para fazer failover entre os nós. | Como Always On FCI usa o armazenamento compartilhado, a mesma exibição da instância de armazenamento está disponível no failover.
SQL Server em uma VM IaaS do Azure ou no local.| [Espelhamento de banco de dados (modo de alto desempenho)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | O tempo necessário para forçar o serviço, que usa o servidor espelho como um servidor em espera passiva. | A replicação é assíncrona. O banco de dados espelho pode atrasar um pouco atrás do banco de dados principal. O retardo é normalmente pequeno. Mas poderá se tornar grande se o sistema do servidor principal ou espelho estiver sob uma carga pesada.<br/><br/>O envio de logs pode ser um suplemento para espelhamento de banco de dados. É uma alternativa favorável ao espelhamento de banco de dados assíncrono.
SQL como PaaS (plataforma como serviço) no Azure.<br/><br/>Esse tipo de implantação inclui pools elásticos e servidores de banco de dados SQL do Azure. | Replicação geográfica ativa | 30 segundos após o failover ser disparado.<br/><br/>Quando o failover é ativado para um dos bancos de dados secundários, todos os outros secundários são vinculados automaticamente ao novo primário. | RPO de cinco segundos.<br/><br/>A replicação geográfica ativa usa a tecnologia de Always On do SQL Server. Ele Replica de forma assíncrona as transações confirmadas no banco de dados primário para um banco de dados secundário usando o isolamento de instantâneo.<br/><br/>Os dados secundários têm garantia de nunca ter transações parciais.
SQL como PaaS configurado com replicação geográfica ativa no Azure.<br/><br/>Esse tipo de implantação inclui uma instância gerenciada do banco de dados SQL, pools elásticos e servidores de banco de dados SQL. | Grupos de failover automático | RTO de uma hora. | RPO de cinco segundos.<br/><br/>Os grupos de failover automático fornecem a semântica do grupo sobre a replicação geográfica ativa. Mas o mesmo mecanismo de replicação assíncrona é usado.
SQL Server em uma VM IaaS do Azure ou no local.| Replicação com Azure Site Recovery | O RTO é normalmente menos de 15 minutos. Para saber mais, leia o [SLA de RTO fornecido pelo site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Uma hora para a consistência do aplicativo e cinco minutos para a consistência de falhas.

> [!NOTE]
> Algumas considerações importantes quando você está ajudando a proteger cargas de trabalho do SQL com Site Recovery:
> * Site Recovery é independente de aplicativo. Site Recovery pode ajudar a proteger qualquer versão do SQL Server implantada em um sistema operacional com suporte. Para saber mais, consulte a [matriz de suporte para recuperação](vmware-physical-azure-support-matrix.md#replicated-machines) de máquinas replicadas.
> * Você pode optar por usar Site Recovery para qualquer implantação no Azure, Hyper-V, VMware ou infraestrutura física. Siga as orientações no final deste artigo sobre [como ajudar a proteger um cluster SQL Server](#how-to-help-protect-a-sql-server-cluster) com site Recovery.
> * Verifique se a taxa de alteração de dados observada na máquina está dentro dos [limites de site Recovery](vmware-physical-azure-support-matrix.md#churn-limits). A taxa de alteração é medida em bytes de gravação por segundo. Para computadores que executam o Windows, você pode exibir essa taxa de alteração selecionando a guia **desempenho** no Gerenciador de tarefas. Observe a velocidade de gravação de cada disco.
> * O Site Recovery dá suporte à replicação de instâncias de cluster de failover no Espaços de Armazenamento Diretos. Para saber mais, consulte [como habilitar a replicação de espaços de armazenamento diretos](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Recuperação de desastre de um aplicativo

Site Recovery orquestra o failover de teste e o failover de todo o seu aplicativo com a ajuda dos planos de recuperação.

Há alguns pré-requisitos para garantir que seu plano de recuperação seja totalmente personalizado de acordo com sua necessidade. Qualquer implantação de SQL Server normalmente precisa de uma implantação de Active Directory. Ele também precisa de conectividade para a camada de aplicativo.

### <a name="step-1-set-up-active-directory"></a>Etapa 1: Configurar o Active Directory

Configure o Active Directory no site de recuperação secundário para que SQL Server seja executado corretamente.

* **Pequena empresa**: Você tem um pequeno número de aplicativos e um único controlador de domínio para o site local. Se você quiser fazer failover de todo o site, use Site Recovery replicação. Esse serviço Replica o controlador de domínio para o datacenter secundário ou para o Azure.
* **Média para grande empresa**: Talvez seja necessário configurar controladores de domínio adicionais.
  - Se você tiver um grande número de aplicativos, tiver uma floresta Active Directory e desejar fazer failover por aplicativo ou carga de trabalho, configure outro controlador de domínio no datacenter secundário ou no Azure.
  -  Se você estiver usando grupos de disponibilidade Always On para recuperar em um site remoto, configure outro controlador de domínio no site secundário ou no Azure. Esse controlador de domínio é usado para a instância de SQL Server recuperada.

As instruções neste artigo pressupõem que um controlador de domínio esteja disponível no local secundário. Para saber mais, consulte os procedimentos para [ajudar a proteger Active Directory com site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Etapa 2: Garantir a conectividade com outras camadas

Depois que a camada de banco de dados estiver em execução na região do Azure de destino, verifique se você tem conectividade com o aplicativo e as camadas da Web. Execute as etapas necessárias com antecedência para validar a conectividade com o failover de teste.

Para entender como você pode criar aplicativos para considerações de conectividade, consulte estes exemplos:

* [Criar um aplicativo para recuperação de desastre na nuvem](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Estratégias de recuperação de desastre do pool elástico](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Etapa 3: Interopere com Always On, replicação geográfica ativa e grupos de failover automático

As tecnologias BCDR Always On, replicação geográfica ativa e grupos de failover automático têm réplicas secundárias de SQL Server em execução na região do Azure de destino. A primeira etapa para o failover do aplicativo é especificar essa réplica como primária. Esta etapa pressupõe que você já tem um controlador de domínio no secundário. A etapa pode não ser necessária se você optar por fazer um failover automático. Faça failover de suas camadas Web e de aplicativo somente após a conclusão do failover de banco de dados.

> [!NOTE]
> Se você tiver ajudado a proteger as máquinas do SQL com Site Recovery, basta criar um grupo de recuperação desses computadores e adicionar seu failover no plano de recuperação.

[Crie um plano de recuperação](site-recovery-create-recovery-plans.md) com máquinas virtuais de aplicativo e de camada da Web. As etapas a seguir mostram como adicionar o failover da camada de banco de dados:

1. Importe os scripts para fazer failover do grupo de disponibilidade do SQL em uma [máquina virtual do Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e em uma [máquina virtual clássica](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importe os scripts para sua conta de automação do Azure.

    [![Imagem de um logotipo "implantar no Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione o script ASR-SQL-FailoverAG como uma ação prévia do primeiro grupo do plano de recuperação.

1. Siga as instruções disponíveis no script para criar uma variável de automação. Essa variável fornece o nome dos grupos de disponibilidade.

### <a name="step-4-conduct-a-test-failover"></a>Etapa 4: Conduzir um failover de teste

Algumas tecnologias BCDRs, como o SQL Always On, não oferecem suporte nativo ao failover de teste. Recomendamos a seguinte abordagem *somente ao usar essas tecnologias*.

1. Configure o [backup do Azure](../backup/backup-azure-arm-vms.md) na VM que hospeda a réplica do grupo de disponibilidade no Azure.

1. Antes de disparar o failover de teste do plano de recuperação, recupere a VM do backup feito na etapa anterior.

    ![Captura de tela mostrando a janela para restaurar uma configuração do backup do Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Force um quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na VM que foi restaurada do backup.

1. Atualize o endereço IP do ouvinte para que ele seja um endereço disponível na rede de failover de teste.

    ![Captura de tela da janela regras e da caixa de diálogo Propriedades do endereço IP](./media/site-recovery-sql/update-listener-ip.png)

1. Coloque o ouvinte online.

    ![Captura de tela da janela rotulada Content_AG mostrando nomes e status do servidor](./media/site-recovery-sql/bring-listener-online.png)

1. Criar um balanceador de carga. Para cada ouvinte de grupo de disponibilidade, crie um endereço IP do pool de IPS de front-end. Além disso, adicione a VM SQL Server ao pool de back-end.

     ![Captura de tela da janela chamada "SQL-AlwaysOn-LB-frontend IP pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Captura de tela de janela intitulada "SQL-AlwaysOn-LB-pool de IPS de back-end](./media/site-recovery-sql/create-load-balancer2.png)

1. Em grupos de recuperação posteriores, adicione o failover da camada de aplicativo seguido por sua camada da Web para este plano de recuperação.

1. Faça um failover de teste do plano de recuperação para testar o failover de ponta a ponta de seu aplicativo.

## <a name="steps-to-do-a-failover"></a>Etapas para fazer um failover

Depois de adicionar o script na etapa 3 e validá-lo na etapa 4, você poderá fazer um failover do plano de recuperação criado na etapa 3.

As etapas de failover para o aplicativo e para as camadas da Web devem ser as mesmas nos planos de failover de teste e de recuperação de failover.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Como ajudar a proteger um cluster SQL Server

Para um cluster que executa o SQL Server Standard Edition ou o SQL Server 2008 R2, recomendamos que você use a replicação Site Recovery para ajudar a proteger SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure para Azure e local para o Azure

Site Recovery não fornece suporte ao cluster de convidado ao replicar para uma região do Azure. O SQL Server Standard Edition também não fornece uma solução de recuperação de desastre de baixo custo. Nesse cenário, recomendamos que você proteja o cluster SQL Server para uma instância SQL Server autônoma no local principal e recupere-o no secundário.

1. Configure uma instância de SQL Server autônoma adicional na região primária do Azure ou no site local.

1. Configure a instância do para servir como um espelho para os bancos de dados que você deseja ajudar a proteger. Configure o espelhamento no modo de alta segurança.

1. Configure Site Recovery no site primário para VMs [do Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md)ou [VMware e servidores físicos](site-recovery-vmware-to-azure-classic.md).

1. Use Site Recovery replicação para replicar a nova instância de SQL Server no site secundário. Como é uma cópia espelhada de alta segurança, ela será sincronizada com o cluster primário, mas replicada usando Site Recovery replicação.

   ![Imagem de um cluster padrão que mostra a relação e o fluxo entre um site primário, Site Recovery e o Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerações sobre failback

Para clusters SQL Server Standard, o failback após um failover não planejado requer um backup e uma restauração de SQL Server. Essa operação é feita a partir da instância de espelho para o cluster original com o restabelecimento do espelho.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Como SQL Server ser licenciado quando usado com Site Recovery?

A replicação Site Recovery para SQL Server é coberta pelo benefício de recuperação de desastre do Software Assurance. Essa cobertura se aplica a todos os cenários de Site Recovery: local para a recuperação de desastre do Azure e recuperação de desastres IaaS do Azure entre regiões. Confira [Azure site Recovery preços](https://azure.microsoft.com/pricing/details/site-recovery/) para obter mais informações.

### <a name="will-site-recovery-support-my-sql-server-version"></a>O Site Recovery dará suporte à minha versão do SQL Server?

Site Recovery é independente de aplicativo. Site Recovery pode ajudar a proteger qualquer versão do SQL Server implantada em um sistema operacional com suporte. Para obter mais informações, consulte a [matriz de suporte para recuperação](vmware-physical-azure-support-matrix.md#replicated-machines) de máquinas replicadas.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [arquitetura de site Recovery](site-recovery-components.md).
* Para SQL Server no Azure, saiba mais sobre [soluções de alta disponibilidade](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) para recuperação em uma região secundária do Azure.
* Para o banco de dados SQL, saiba mais sobre as opções de continuidade de [negócios](../sql-database/sql-database-business-continuity.md) e [alta disponibilidade](../sql-database/sql-database-high-availability.md) para recuperação em uma região secundária do Azure.
* Para computadores SQL Server no local, saiba mais sobre as opções de [alta disponibilidade](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) para recuperação em máquinas virtuais do Azure.
