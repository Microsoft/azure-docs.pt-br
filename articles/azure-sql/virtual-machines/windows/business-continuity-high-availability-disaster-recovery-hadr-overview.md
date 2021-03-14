---
title: Alta disponibilidade, recuperação de desastres, continuidade dos negócios
description: Saiba mais sobre as opções de alta disponibilidade, de recuperação de desastre (HADR) e de continuidade de negócios disponíveis para SQL Server em VMs do Azure, como grupos de disponibilidade Always On, instância de cluster de failover, espelhamento de banco de dados, envio de logs e backup & restauração no armazenamento do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: f4d870f458607ceb0f05812b5c0c066ce810448e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508309"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Continuidade de negócios e HADR para SQL Server em máquinas virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A continuidade dos negócios significa continuar seus negócios em caso de desastre, planejar a recuperação e garantir que seus dados estejam altamente disponíveis. SQL Server em máquinas virtuais do Azure pode ajudar a reduzir o custo de uma solução de banco de dados HADR (alta disponibilidade e recuperação de desastre). 

Há suporte para a maioria SQL Server soluções HADR em VMs (máquinas virtuais), como soluções híbridas e somente do Azure. Em uma solução somente Azure, todo o sistema HADR é executado no Azure. Em uma configuração híbrida, parte da solução é executada no Azure e a outra parte é executada localmente em sua organização. A flexibilidade do ambiente do Azure permite que você se mova parcial ou completamente para o Azure para atender aos requisitos de orçamento e HADR de seus sistemas de banco de dados do SQL Server.

Este artigo compara e contrasta as soluções de continuidade de negócios disponíveis para SQL Server em VMs do Azure. 

## <a name="overview"></a>Visão geral

Cabe a você garantir que o seu sistema de banco de dados tenha os recursos HADR que o SLA (contrato de nível de serviço) exige. O fato de que o Azure fornece mecanismos de alta disponibilidade, como a recuperação de serviço para serviços de nuvem e detecção de recuperação de falha para máquinas virtuais, não garante que você possa atender ao SLA. Embora esses mecanismos ajudem a proteger a alta disponibilidade da máquina virtual, eles não protegem a disponibilidade de SQL Server em execução dentro da VM. 

É possível que a instância de SQL Server falhe enquanto a VM estiver online e íntegra. Até mesmo os mecanismos de alta disponibilidade fornecidos pelo Azure permitem o tempo de inatividade das VMs devido a eventos como recuperação de falhas de software ou hardware e atualizações do sistema operacional.

O GRS (armazenamento com redundância geográfica) no Azure é implementado com um recurso chamado replicação geográfica. GRS pode não ser uma solução de recuperação de desastre adequada para seus bancos de dados. Como a replicação geográfica envia dados de forma assíncrona, as atualizações recentes podem ser perdidas em um desastre. Mais informações sobre as limitações da replicação geográfica são abordadas na seção [suporte à replicação geográfica](#geo-replication-support) .

## <a name="deployment-architectures"></a>Arquiteturas de implantação
O Azure dá suporte a essas tecnologias de SQL Server para continuidade dos negócios:

* [grupos de disponibilidade Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On FCIs (instâncias de cluster de failover)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Envio de logs](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [SQL Server Backup e restauração com o armazenamento de BLOBs do Azure](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Espelhamento de banco de dados](/sql/database-engine/database-mirroring/database-mirroring-sql-server) -preterido no SQL Server 2016

Você pode combinar as tecnologias para implementar uma solução de SQL Server que tenha recursos de alta disponibilidade e recuperação de desastres. Dependendo da tecnologia que você usa, uma implantação híbrida pode exigir um túnel VPN com a rede virtual do Azure. As seções a seguir mostram alguns exemplos de arquiteturas de implantação.

## <a name="azure-only-high-availability-solutions"></a>Somente Azure: soluções de alta disponibilidade

Você pode ter uma solução de alta disponibilidade para SQL Server em um nível de banco de dados com grupos de disponibilidade Always On. Você também pode criar uma solução de alta disponibilidade em um nível de instância com Always On instâncias de cluster de failover. Para proteção adicional, você pode criar redundância em ambos os níveis criando grupos de disponibilidade em instâncias de cluster de failover. 

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |As réplicas de disponibilidade em execução em VMs do Azure para a mesma região fornecem alta disponibilidade. Você precisa configurar uma VM de controlador de domínio, porque o clustering de failover do Windows exige um domínio do Active Directory.<br/><br/> Para maior redundância e disponibilidade, as VMs do Azure podem ser implantadas em diferentes [zonas de disponibilidade](../../../availability-zones/az-overview.md) , conforme documentado na [visão geral do grupo de disponibilidade](availability-group-overview.md). Se as SQL Server VMs em um grupo de disponibilidade forem implantadas em zonas de disponibilidade, use o [Standard Load Balancer do Azure](../../../load-balancer/load-balancer-overview.md) para o ouvinte, conforme documentado nos artigos [CLI de VM](./availability-group-az-commandline-configure.md) do Azure SQL e [modelos de início rápido do Azure](availability-group-quickstart-template-configure.md) .<br/> ![Diagrama que mostra o "controlador de domínio" acima do "Cluster WSFC" criado da "réplica primária", "réplica secundária" e "testemunha de compartilhamento de arquivos".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Para saber mais, consulte [Configurar grupos de disponibilidade no Azure (GUI)](./availability-group-quickstart-template-configure.md). |
| **Instâncias de cluster de failover** |As instâncias de cluster de failover têm suporte em VMs SQL Server. Como o recurso FCI requer armazenamento compartilhado, cinco soluções funcionarão com SQL Server em VMs do Azure: <br/><br/> -Usando [discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) para o Windows Server 2019. Discos gerenciados compartilhados são um produto do Azure que permite anexar um disco gerenciado a várias máquinas virtuais simultaneamente. As VMs no cluster podem ler ou gravar em seu disco anexado com base na reserva escolhida pelo aplicativo clusterizado por meio de reservas persistentes de SCSI (RP PR). O SCSI PR é uma solução de armazenamento padrão do setor que é usada por aplicativos executados em uma rede de área de armazenamento (SAN) local. Habilitar o SCSI PR em um disco gerenciado permite que você migre esses aplicativos para o Azure como estão. <br/><br/>-Usando [espaços de armazenamento diretos \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) para fornecer uma SAN virtual baseada em software para o Windows Server 2016 e posterior.<br/><br/>-Usando um [compartilhamento de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md) para o Windows Server 2012 e posterior. Os compartilhamentos de arquivos Premium são apoiados em SSD, têm baixa latência consistentemente e têm suporte total para uso com FCI.<br/><br/>-Usando o armazenamento com suporte de uma solução de parceiro para clustering. Para um exemplo específico que usa o SIOS datakeeper, consulte a entrada de blog [clustering de failover e sios Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>-Usando o armazenamento de bloco compartilhado para um destino iSCSI remoto por meio do Azure ExpressRoute. Por exemplo, o NPS (Armazenamento Privado do NetApp) expõe um destino iSCSI por meio do ExpressRoute com o Equinix para VMs do Azure.<br/><br/>Para soluções de replicação de dados e armazenamento compartilhado de parceiros da Microsoft, contate o fornecedor para solucionar problemas relacionados ao acesso a dados no failover.<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>Somente Azure: soluções de recuperação de desastre
Você pode ter uma solução de recuperação de desastre para seus bancos de dados SQL Server no Azure usando grupos de disponibilidade, espelhamento de banco de dados ou backup e restauração com blobs de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Réplicas de disponibilidade executadas em vários datacenters em VMs do Azure para recuperação de desastres. Essa solução de região cruzada ajuda a proteger contra uma interrupção completa do site. <br/> ![Diagrama que mostra duas regiões com uma "réplica primária" e "réplica secundária" conectada por uma "confirmação assíncrona".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar dentro do mesmo serviço de nuvem e da mesma rede virtual. Como cada região terá uma rede virtual separada, essas soluções exigem conectividade de rede para rede. Para obter mais informações, consulte [Configurar uma conexão de rede para rede usando o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obter instruções detalhadas, consulte [configurar um SQL Server Always on grupo de disponibilidade em diferentes regiões do Azure](availability-group-manually-configure-multiple-regions.md).|
| **Espelhamento de banco de dados** |Servidores principal e de espelho em execução em diferentes datacenters para recuperação de desastres. Você deve implantá-los usando certificados de servidor. Não há suporte para o espelhamento de banco de dados SQL Server para SQL Server 2008 ou SQL Server 2008 R2 em uma VM do Azure. <br/>![Diagrama que mostra a "entidade" em uma região conectada ao espelho em outra região com "alto desempenho".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Backup e restauração com o armazenamento de BLOBs do Azure** |Os bancos de dados de produção com backup direto no armazenamento de BLOBs em um datacenter diferente para recuperação de desastres.<br/>![Diagrama que mostra um "banco de dados" em uma região que faz backup de "armazenamento de BLOBs" em outra região.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Para obter mais informações, consulte [backup e restauração para SQL Server em VMs do Azure](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replicar e fazer failover de SQL Server no Azure com Azure Site Recovery** |A instância de SQL Server de produção em um datacenter do Azure foi replicada diretamente para o armazenamento do Azure em um datacenter do Azure diferente para recuperação de desastre.<br/>![Diagrama que mostra um "banco de dados" em um datacenter do Azure usando a "replicação ASR" para recuperação de desastre em outro datacenter. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [Proteger o SQL Server usando a recuperação de desastre do SQL Server e o Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>TI Híbrida: Soluções de recuperação de desastre
Você pode ter uma solução de recuperação de desastre para seus bancos de dados SQL Server em um ambiente de ti híbrido usando grupos de disponibilidade, espelhamento de banco de dados, envio de logs e backup e restauração com o armazenamento de BLOBs do Azure.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Algumas réplicas de disponibilidade executadas em VMs do Azure e outras réplicas executadas localmente para recuperação de desastres intersite. O site de produção pode ser local ou em um datacenter do Azure.<br/>![Grupos de disponibilidade](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Como todas as réplicas de disponibilidade devem estar no mesmo cluster de failover, o cluster deve abranger as duas redes (um cluster de failover de várias sub-redes). Essa configuração requer uma conexão VPN entre o Azure e a rede local.<br/><br/>Para recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local da recuperação de desastres.|
| **Espelhamento de banco de dados** |Um parceiro em execução em uma VM do Azure e o outro executado localmente para recuperação de desastres entre sites usando certificados de servidor. Os parceiros não precisam estar no mesmo domínio Active Directory e nenhuma conexão VPN é necessária.<br/>![Espelhamento de banco de dados](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Outro cenário que o espelhamento de banco de dados envolve é um parceiro em execução em uma VM do Azure e o outro em execução localmente no mesmo domínio do Active Directory para recuperação de desastres intersite. Uma [conexão VPN entre a rede virtual do Azure e a rede local](../../../vpn-gateway/tutorial-site-to-site-portal.md) é necessária.<br/><br/>Para recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local da recuperação de desastres. Não há suporte para o espelhamento de banco de dados SQL Server para SQL Server 2008 ou SQL Server 2008 R2 em uma VM do Azure. |
| **Envio de logs** |Um servidor em execução em uma VM do Azure e outro em execução local para recuperação de desastre intersite. O envio de log depende do compartilhamento de arquivos do Windows, assim, uma conexão VPN entre a rede virtual do Azure e a rede local é necessária.<br/>![Envio de logs](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Para recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local da recuperação de desastres. |
| **Backup e restauração com o armazenamento de BLOBs do Azure** |Bancos de dados de produção locais com backup direto no armazenamento de BLOBs do Azure para recuperação de desastre.<br/>![Backup e restauração](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Para obter mais informações, consulte [backup e restauração para SQL Server em máquinas virtuais do Azure](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replicar e fazer failover de SQL Server no Azure com Azure Site Recovery** |Instância de SQL Server de produção local replicada diretamente no armazenamento do Azure para recuperação de desastre.<br/>![Replicar usando o Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [Proteger o SQL Server usando a recuperação de desastre do SQL Server e o Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Réplica de DR gratuita no Azure

Se você tiver o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), poderá implementar planos de Dr (recuperação de desastre híbrido) com o SQL Server sem incorrer em custos de licenciamento adicionais para a instância de recuperação de desastre passiva.

Por exemplo, você pode ter dois secundários passivos gratuitos quando todas as três réplicas são hospedadas no Azure: 

![Dois passivos gratuitos quando tudo no Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

Ou você pode configurar um ambiente de failover híbrido, com um primário licenciado local, um passivo gratuito para HA, um passivo gratuito para DR local e um passivo gratuito para DR no Azure:

![Três passivos gratuitos quando o ambiente é híbrido com uma réplica local primária](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

Para obter mais informações, confira os [termos de licenciamento de produtos](https://www.microsoft.com/licensing/product-licensing/products). 

Para habilitar esse benefício, vá para seu [SQL Server recurso de máquina virtual](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Selecione **definir** em **configurações** e, em seguida, escolha a opção **recuperação de desastre** em **SQL Server licença**. Marque a caixa de seleção para confirmar que esta SQL Server VM será usada como uma réplica passiva e, em seguida, selecione **aplicar** para salvar suas configurações. 

![Configurar uma réplica de recuperação de desastre no Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para HADR do SQL Server no Azure
VMs do Azure, armazenamento e rede têm características operacionais diferentes da infraestrutura de TI local, não virtualizada. Uma implementação bem-sucedida de uma solução de SQL Server HADR no Azure exige que você compreenda essas diferenças e projete sua solução para acomodá-las.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nós de alta disponibilidade em um conjunto de disponibilidade
Os conjuntos de disponibilidade no Azure permitem que você coloque os nós de alta disponibilidade em domínios de falha e domínios de atualização separados. A plataforma Azure atribui um domínio de atualização e um domínio de falha a cada máquina virtual em seu conjunto de disponibilidade. Essa configuração em um datacenter garante que, durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível e atenda ao SLA do Azure de 99,95%. 

Para configurar uma configuração de alta disponibilidade, Coloque todas as máquinas virtuais SQL Serverdas no mesmo conjunto de disponibilidade para evitar a perda de aplicativos ou dados durante um evento de manutenção. Somente nós no mesmo serviço de nuvem podem participar do mesmo conjunto de disponibilidade. Para saber mais, veja [Gerenciar a disponibilidade de máquinas virtuais](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Nós de alta disponibilidade em uma zona de disponibilidade
As zonas de disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. A separação física das zonas de disponibilidade em uma região ajuda a proteger aplicativos e dados de falhas do datacenter, garantindo que pelo menos uma máquina virtual esteja disponível e atenda ao SLA do Azure de 99,99%. 

Para configurar a alta disponibilidade, faça parte SQL Server máquinas virtuais distribuídas entre zonas de disponibilidade na região. Haverá encargos adicionais para transferências de rede para rede entre zonas de disponibilidade. Para obter mais informações, confira [Zonas de disponibilidade](../../../availability-zones/az-overview.md). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamento do cluster de failover na rede do Azure
O serviço DHCP não compatível com RFC no Azure pode fazer com que a criação de determinadas configurações de cluster de failover falhe. Essa falha ocorre porque o nome de rede do cluster recebe um endereço IP duplicado, como o mesmo endereço IP de um dos nós do cluster. Esse é um problema quando você usa grupos de disponibilidade, que dependem do recurso de cluster de failover do Windows.

Considere o cenário onde um cluster de dois nós é criado e colocado online:

1. O cluster fica online e, em seguida, o NODE1 solicita um endereço IP atribuído dinamicamente para o nome de rede do cluster.
2. O serviço DHCP não fornece nenhum endereço IP diferente de Node1 próprio endereço IP, pois o serviço DHCP reconhece que a solicitação vem do NODE1 em si.
3. O Windows detecta que um endereço duplicado é atribuído a NODE1 e ao nome de rede do cluster de failover, e o grupo de clusters padrão não é colocado online.
4. O grupo de clusters padrão é movido para NODE2. NODE2 trata o endereço IP do Node1 como o endereço IP do cluster e coloca o grupo de clusters padrão online.
5. Quando o NODE2 tenta estabelecer a conectividade com o NODE1, os pacotes direcionados a NODE1 nunca deixam o NODE2 porque resolve o endereço IP do Node1 para ele mesmo. NODE2 não pode estabelecer conectividade com NODE1 e, em seguida, perde quorum e desliga o cluster.
6. NODE1 pode enviar pacotes para NODE2, mas o NODE2 não pode responder. O NODE1 perde quorum e fecha o cluster.

Você pode evitar esse cenário atribuindo um endereço IP estático não usado ao nome de rede do cluster para colocar o nome da rede de cluster online. Por exemplo, você pode usar um endereço IP de link local como 169.254.1.1. Para simplificar esse processo, consulte [Configurar cluster de failover do Windows no Azure para grupos de disponibilidade](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para saber mais, consulte [Configurar grupos de disponibilidade no Azure (GUI)](./availability-group-quickstart-template-configure.md).

### <a name="support-for-availability-group-listeners"></a>Suporte para ouvintes do grupo de disponibilidade
Os ouvintes do grupo de disponibilidade têm suporte em VMs do Azure que executam o Windows Server 2012 e posterior. Esse suporte é possibilitado pelo uso de pontos de extremidade habilitados em VMs do Azure, que são nós do grupo de disponibilidade. Você deve seguir as etapas de configuração especiais para que os ouvintes funcionem para ambos os aplicativos cliente em execução no Azure e aqueles em execução no local.

Há duas opções principais para configurar o ouvinte: externo (público) ou interno. O ouvinte externo (público) usa um balanceador de carga voltado para a Internet e está associado a um IP virtual público que é acessível pela Internet. Um ouvinte interno usa um balanceador de carga interno e dá suporte apenas a clientes dentro da mesma rede virtual. Para qualquer desses dois tipos de balanceador de carga, você deve habilitar o retorno de servidor direto. 

Se o grupo de disponibilidade abranger várias sub-redes do Azure (como uma implantação que atravessa regiões do Azure), a cadeia de conexão do cliente deverá incluir `MultisubnetFailover=True` . Isso resulta em tentativas de conexão em paralelo às réplicas nas diferentes sub-redes. Para obter instruções sobre como configurar um ouvinte, consulte [configurar um ouvinte de ILB para grupos de disponibilidade no Azure](availability-group-listener-powershell-configure.md).


Você pode ainda se conectar a cada réplica de disponibilidade separadamente conectando-se diretamente à instância do serviço. Além disso, como os grupos de disponibilidade são compatíveis com os clientes de espelhamento de banco de dados, você pode se conectar às réplicas de disponibilidade como parceiros de espelhamento de banco de dados, desde que as réplicas sejam configuradas de forma semelhante ao espelhamento de banco

* Há uma réplica primária e uma réplica secundária.
* A réplica secundária é configurada como não legível (opção **secundária legível** definida como **não**).

Aqui está um exemplo de cadeia de conexão de cliente que corresponde a essa configuração de espelhamento de banco de dados usando ADO.NET ou SQL Server Native Client:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Para obter mais informações sobre conectividade de cliente, consulte:

* [Usando palavras-chave da cadeia de conexão com o SQL Server Native Client](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Conectar clientes a uma sessão de espelhamento de banco de dados (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Conectando-se ao ouvinte do grupo de disponibilidade em TI híbrida](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Ouvintes do grupo de disponibilidade, conectividade de cliente e failover de aplicativo (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Usando cadeias de conexão de espelhamento de banco de dados com grupos de disponibilidade](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>Latência de rede em TI híbrida
Implante sua solução HADR com a suposição de que pode haver períodos de alta latência de rede entre sua rede local e o Azure. Quando você estiver implantando réplicas no Azure, use a confirmação assíncrona em vez da confirmação síncrona para o modo de sincronização. Quando você estiver implantando servidores de espelhamento de banco de dados locais e no Azure, use o modo de alto desempenho em vez do modo de alta segurança.

### <a name="geo-replication-support"></a>Suporte para replicação geográfica
A replicação geográfica em discos do Azure não dá suporte ao arquivo de dados e ao arquivo de log do mesmo banco de dados a ser armazenado em discos separados. GRS replica as alterações em cada disco, de forma independente e assíncrona. Esse mecanismo garante a ordem de gravação em um único disco na cópia replicada geograficamente, mas não em cópias replicadas geograficamente de vários discos. Se você configurar um banco de dados para armazenar seu arquivo e seu arquivo de log em discos separados, os discos recuperados após um desastre poderão conter uma cópia mais atualizada do arquivo de dados do que o arquivo de log, o que interrompe o log write-ahead em SQL Server e as propriedades ACID (atomicidade, consistência, isolamento e durabilidade) das transações. 

Se você não tiver a opção de desabilitar a replicação geográfica na conta de armazenamento, mantenha todos os arquivos de dados e de log de um banco de dado no mesmo disco. Se você precisar usar mais de um disco devido ao tamanho do banco de dados, implante uma das soluções de recuperação de desastres listadas anteriormente para garantir a redundância de dado.

## <a name="next-steps"></a>Próximas etapas

Decida se um [grupo de disponibilidade](availability-group-overview.md) ou uma [instância de cluster de failover](failover-cluster-instance-overview.md) é a melhor solução de continuidade de negócios para sua empresa. Em seguida, examine as [práticas recomendadas](hadr-cluster-best-practices.md) para configurar seu ambiente para alta disponibilidade e recuperação de desastres.