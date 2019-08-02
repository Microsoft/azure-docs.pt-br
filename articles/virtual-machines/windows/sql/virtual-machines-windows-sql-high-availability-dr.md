---
title: Alta Disponibilidade e Recuperação de Desastre para SQL Server | Microsoft Docs
description: Uma discussão sobre os diversos tipos de estratégias HADR do SQL Server em execução em máquinas virtuais do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: aac7a573b254c2e46d2ecf25d176df28e5e5d7da
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137615"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Alta disponibilidade e recuperação de desastre para SQL Server nas Máquinas Virtuais do Azure

VMs (Máquinas virtuais) do Microsoft Azure com SQL Server podem ajudar a reduzir o custo de uma solução de banco de dados HADR (Alta Disponibilidade e Recuperação de Desastre). A maioria das soluções HADR do SQL Server tem suporte em máquinas virtuais do Azure, tanto como somente Azure ou como soluções híbridas. Em uma solução somente Azure, todo o sistema HADR é executado no Azure. Em uma configuração híbrida, parte da solução é executada no Azure e a outra parte é executada localmente em sua organização. A flexibilidade do ambiente do Azure permite que você se mova parcial ou completamente para o Azure para atender aos requisitos de orçamento e HADR de seus sistemas de banco de dados do SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Compreendendo a necessidade de uma solução HADR
Cabe a você garantir que seu sistema de banco de dados possua os recursos HADR que o SLA (Contrato de Nível de Serviço) exige. O fato de que o Azure fornece mecanismos de alta disponibilidade, como recuperação de serviço para serviços de nuvem e detecção de recuperação de falhas para máquinas virtuais, não é garantia de que você possa atender ao SLA desejado. Esses mecanismos protegem a alta disponibilidade das VMs, mas não a alta disponibilidade do SQL Server em execução nas VMs. É possível que a instância do SQL Server falhe enquanto a VM estiver online e íntegra. Além disso, até mesmo os mecanismos de alta disponibilidade fornecidos pelo Azure permitem tempo de inatividade das VMs em razão de eventos como recuperação de software ou falhas de hardware e atualizações do sistema operacional.

Além disso, o GRS (Armazenamento com Redundância Geográfica) no Azure, que é implementado com um recurso chamado replicação geográfica, pode não ser uma solução de recuperação de desastres adequada para seus bancos de dados. Como a replicação geográfica envia dados de forma assíncrona, atualizações recentes podem ser perdidas no caso de desastre. Mais informações sobre limitações de replicação geográfica são abordadas na seção [Replicação geográfica sem suporte para arquivos de dados e log em discos separados](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Arquiteturas de implantação de HADR
As tecnologias HADR do SQL Server que têm suporte no Azure incluem:

* [Grupos de disponibilidade AlwaysOn](https://technet.microsoft.com/library/hh510230.aspx)
* [Instâncias do cluster de failover AlwaysOn](https://technet.microsoft.com/library/ms189134.aspx)
* [Envio de logs](https://technet.microsoft.com/library/ms187103.aspx)
* [Backup e restauração do SQL Server com o Serviço de armazenamento de blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx)
* [Espelhamento de banco de dados](https://technet.microsoft.com/library/ms189852.aspx) - preterido no SQL Server 2016

É possível combinar as tecnologias para implementar uma solução SQL Server com alta disponibilidade e recursos de recuperação de desastre. Dependendo da tecnologia usada, uma implantação híbrida pode exigir um túnel VPN com a rede virtual do Azure. As seções a seguir mostram algumas das arquiteturas de implantação de exemplo.

## <a name="azure-only-high-availability-solutions"></a>Somente Azure: Soluções de alta disponibilidade

É possível ter uma solução de alta disponibilidade para o SQL Server em um nível de banco de dados com Grupos de Disponibilidade AlwaysOn - chamados de grupos de disponibilidade. Você também pode criar uma solução de alta disponibilidade no nível da instância com Instâncias de Cluster de Failover do Always On - instâncias de cluster de failover. Para adicionar redundância, crie a redundância nos dois níveis, criando grupos de disponibilidade em instâncias de cluster de failover. 

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |As réplicas de disponibilidade em execução em VMs do Azure para a mesma região fornecem alta disponibilidade. Você precisa configurar uma VM de controlador de domínio, porque o clustering de failover do Windows exige um domínio do Active Directory.<br/> ![Grupos de Disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Para saber mais, consulte [Configurar Grupos de Disponibilidade no Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instâncias do cluster de failover** |FCI (Instâncias de Cluster de Failover), que exigem armazenamento compartilhado, podem ser criadas de três maneiras diferentes.<br/><br/>1. Um cluster de failover de dois nós em execução nas VMs do Azure com o armazenamento anexado, usando os [Espaços de Armazenamento Diretos do Windows Server 2016 \(S2D\)](virtual-machines-windows-portal-sql-create-failover-cluster.md) para fornecer uma SAN virtual baseada em software.<br/><br/>2. Um cluster de failover de dois nós em execução nas VMs do Azure com armazenamento com suporte em uma solução de clustering de terceiros. Para obter um exemplo específico que usa o SIOS DataKeeper, consulte [Alta disponibilidade para um compartilhamento de arquivos usando o clustering de failover e o software de terceiros SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Um cluster de failover de dois nós em execução nas VMs do Azure com armazenamento em bloco compartilhado do Destino iSCSI remoto por meio do ExpressRoute. Por exemplo, o NPS (Armazenamento Privado do NetApp) expõe um destino iSCSI por meio do ExpressRoute com o Equinix para VMs do Azure.<br/><br/>Em caso de problemas relacionados ao acesso a dados no failover, contate o fornecedor para obter soluções de terceiros para armazenamento compartilhado e replicação de dados.<br/><br/>Observe que ainda não há suporte para o uso do FCI no [Armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/) , pois esta solução não utiliza o Armazenamento Premium. Estamos trabalhando para dar suporte a isso em breve. |

## <a name="azure-only-disaster-recovery-solutions"></a>Somente Azure: Soluções de recuperação de desastre
Você pode ter uma solução de recuperação de desastres para seus bancos de dados do SQL Server no Azure usando Grupos de Disponibilidade, espelhamento de banco de dados ou backup e restauração com blobs de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de Disponibilidade** |Réplicas de disponibilidade executadas em vários datacenters em VMs do Azure para recuperação de desastres. Essa solução de regiões cruzadas protege contra interrupção completa de site. <br/> ![Grupos de Disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar dentro do mesmo serviço de nuvem e na mesma VNet. Como cada região terá uma VNet separada, essas soluções necessitam de VNet para conectividade VNet. Para obter mais informações, consulte [Configurar uma conexão VNet a VNet usando o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obter instruções detalhadas, confira [Configurar um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure em diferentes regiões](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Espelhamento de banco de dados** |Servidores principal e de espelho em execução em diferentes datacenters para recuperação de desastres. Você deve implantar usando certificados de servidor. Não há suporte para espelhamento de banco de dados do SQL Server para SQL Server 2008 ou SQL Server 2008 R2 em uma VM do Azure. <br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Backup e restauração com o Serviço de armazenamento de blob do Azure** |Bancos de dados de produção com backup direto no armazenamento de blob em um datacenter diferente para recuperação de desastre.<br/>![Backup e restauração](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Para obter mais informações, consulte [Backup e Restauração para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replicação e failover do SQL Server para o Azure com o Azure Site Recovery** |SQL Server de produção de um datacenter do Azure replicado diretamente para o Armazenamento de um datacenter do Azure diferente para recuperação de desastre.<br/>![Replicar usando o Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [Proteger o SQL Server usando a recuperação de desastre do SQL Server e o Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>TI híbrida: Soluções de recuperação de desastre
Você pode ter uma solução de recuperação de desastres para seus bancos de dados do SQL Server em um ambiente de TI híbrida, usando Grupos de disponibilidade, espelhamento de banco de dados, envio de log e backup e restauração com o armazenamento de blog do Azure.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de Disponibilidade** |Algumas réplicas de disponibilidade executadas em VMs do Azure e outras réplicas executadas localmente para recuperação de desastres intersite. O site de produção pode ser local ou em um datacenter do Azure.<br/>![Grupos de Disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Como todas as réplicas de disponibilidade devem estar no mesmo cluster de failover, o cluster deve abranger as duas redes (um cluster de failover de várias sub-redes). Essa configuração requer uma conexão VPN entre o Azure e a rede local.<br/><br/>Para recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local da recuperação de desastres.<br/><br/>É possível usar o Assistente de Adição de Réplica no SSMS para adicionar uma réplica do Azure a um grupo de disponibilidade AlwaysOn existente. Para obter mais informações, consulte o Tutorial: Estenda seu grupo de disponibilidade AlwaysOn no Azure. |
| **Espelhamento de banco de dados** |Um parceiro executado em uma VM do Azure e o outro executado localmente para recuperação de desastres intersite usando certificados de servidor. Os parceiros não precisam estar no mesmo domínio do Active Directory e nenhuma conexão VPN é necessária.<br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Outro cenário que o espelhamento de banco de dados envolve é um parceiro em execução em uma VM do Azure e o outro em execução localmente no mesmo domínio do Active Directory para recuperação de desastres intersite. Uma [conexão VPN entre a rede virtual do Azure e a rede local](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) é necessária.<br/><br/>Para recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local da recuperação de desastres. Não há suporte para espelhamento de banco de dados do SQL Server para SQL Server 2008 ou SQL Server 2008 R2 em uma VM do Azure. |
| **Envio de logs** |Um servidor em execução em uma VM do Azure e outro em execução local para recuperação de desastre intersite. O envio de log depende do compartilhamento de arquivos do Windows, assim, uma conexão VPN entre a rede virtual do Azure e a rede local é necessária.<br/>![Envio de logs](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Para recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local da recuperação de desastres. |
| **Backup e restauração com o Serviço de armazenamento de blob do Azure** |Bancos de dados de produção local com backup diretamente no armazenamento de blob do Azure para recuperação de desastres.<br/>![Backup e restauração](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Para obter mais informações, consulte [Backup e Restauração para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replicação e failover do SQL Server para o Azure com o Azure Site Recovery** |SQL Server de produção local replicado diretamente no Armazenamento do Azure para recuperação de desastre.<br/>![Replicar usando o Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [Proteger o SQL Server usando a recuperação de desastre do SQL Server e o Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para HADR do SQL Server no Azure
VMs do Azure, armazenamento e rede têm características operacionais diferentes da infraestrutura de TI local, não virtualizada. Uma implementação bem-sucedida de uma solução HADR SQL Server no Azure requer que você compreenda essas diferenças e crie sua solução para acomodá-las.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nós de alta disponibilidade em um conjunto de disponibilidade
Conjuntos de disponibilidade no Azure permitem que você coloque os nós de alta disponibilidade em FDs (Domínios de Falha) e UDs (Domínios de Atualização) separados. Para que VMs do Azure sejam colocadas no mesmo conjunto de disponibilidade, você deve implantá-las no mesmo serviço de nuvem. Somente nós no mesmo serviço de nuvem podem participar do mesmo conjunto de disponibilidade. Para obter mais informações, consulte [Gerenciar a Disponibilidade de Máquinas Virtuais](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamento do cluster de failover na rede do Azure
O serviço DHCP não compatível com RFC no Azure pode causar uma falha na criação de algumas configurações do cluster de failover, devido à atribuição do nome da rede de cluster a um endereço IP duplicado, por exemplo, o mesmo endereço IP de um dos nós do cluster. Isso é um problema quando você implementa Grupos de Disponibilidade, que dependem do recurso de cluster de failover do Windows.

Considere o cenário onde um cluster de dois nós é criado e colocado online:

1. O cluster fica online e NODE1 solicita um endereço IP atribuído dinamicamente ao nome de rede de cluster.
2. Nenhum endereço IP que não seja o próprio endereço IP do NODE1 é fornecido pelo serviço DHCP, já que o serviço DHCP reconhece que a solicitação vem do próprio NODE1.
3. O Windows detecta que um endereço duplicado é atribuído a NODE1 e ao nome da rede de cluster de failover, e o grupo de clusters padrão não fica online.
4. O grupo de clusters padrão é movido para o NODE2, que trata o endereço IP do NODE1 como o endereço IP do cluster e coloca o grupo de clusters padrão online.
5. Quando NODE2 tenta estabelecer conectividade com NODE1, pacotes direcionados ao NODE1 nunca deixam o NODE2, pois ele resolve o endereço IP do NODE1 para si mesmo. O NODE2 não pode estabelecer conectividade com NODE1, perde quorum e fecha o cluster.
6. Enquanto isso, o NODE1 pode enviar pacotes para o NODE2, mas NODE2 não pode responder. O NODE1 perde quorum e fecha o cluster.

Esse cenário pode ser evitado atribuindo um estático endereço IP não usado, como um endereço IP de link local como 169.254.1.1, ao nome da rede de cluster para colocar o nome de rede do cluster online. Para simplificar esse processo, consulte [Configurar cluster de failover do Windows no Azure para grupos de disponibilidade](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para saber mais, consulte [Configurar grupos de disponibilidade no Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Suporte do ouvinte do grupo de disponibilidade
Ouvintes do grupo de disponibilidade têm suporte em VMs do Azure que executam o Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Esse suporte é possibilitado pelo uso de pontos de extremidade habilitados em VMs do Azure, que são nós do grupo de disponibilidade. Você deve seguir etapas especiais de configuração para que os ouvintes trabalhem para ambos os aplicativos clientes que estão em execução no Azure, bem como os que estão em execução local.

Há duas opções principais para configurar o ouvinte: externo (público) ou interno. O ouvinte (público) externo usa um balanceador de carga voltado para a Internet e é associado a um IP Virtual público (VIP) acessível pela Internet. Um ouvinte interno usa um balanceador de carga interno e só oferece suporte a clientes na mesma rede virtual. Para qualquer desses dois tipos de balanceador de carga, você deve habilitar o retorno de servidor direto. 

Se o Grupo de disponibilidade abranger várias sub-redes do Azure (como uma implantação que cruza regiões do Azure), a cadeia de conexão do cliente deve incluir "**MultisubnetFailover=True**". Isso resulta em tentativas de conexão em paralelo às réplicas nas diferentes sub-redes. Para obter instruções sobre como configurar um ouvinte, consulte

* [Configurar um ouvinte de ILB para grupos de disponibilidade no Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Configurar um ouvinte externo para grupos de disponibilidade no Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Você pode ainda se conectar a cada réplica de disponibilidade separadamente conectando-se diretamente à instância do serviço. Além disso, como os grupos de disponibilidade são compatíveis com versões anteriores com clientes de espelhamento de banco de dados, você pode se conectar a réplicas de disponibilidade, como parceiros de espelhamento, desde que as réplicas sejam configuradas de forma semelhante ao espelhamento do banco de dados:

* Uma réplica primária e uma réplica secundária
* A réplica secundária é configurada como ilegível (**opção Secundária Legível** definida como **Não**)

Um exemplo de cadeia de conexão de cliente que corresponde à configuração deste espelhamento de banco de dados usando ADO.NET ou o SQL Server Native Client é ilustrado abaixo:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obter mais informações sobre conectividade de cliente, consulte:

* [Usando palavras-chave da cadeia de conexão com o SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Conectar clientes a uma sessão de espelhamento de banco de dados (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Conectando-se ao ouvinte do grupo de disponibilidade em TI híbrida](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Ouvintes de grupo de disponibilidade, conectividade de cliente e failover de aplicativo (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Usando cadeias de conexão de espelhamento de banco de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latência de rede em TI híbrida
Você deve implantar sua solução HADR com a suposição de que pode haver períodos com alta latência da rede entre sua rede local e o Azure. Ao implantar réplicas do Azure, você deve usar confirmação assíncrona, em vez de confirmação síncrona, para o modo de sincronização. Ao implantar servidores de espelhamento de banco de dados localmente e no Azure, use o modo de alto desempenho, em vez do modo de alta segurança.

### <a name="geo-replication-support"></a>Suporte para replicação geográfica
A replicação geográfica em discos do Azure não dá suporte ao arquivo de dados e ao arquivo de log do mesmo banco de dados a ser armazenado em discos separados. GRS replica as alterações em cada disco, de forma independente e assíncrona. Esse mecanismo garante a ordem de gravação em um único disco na cópia replicada geograficamente, mas não em cópias replicadas geograficamente de vários discos. Se você configurar um banco de dados para armazenar arquivo de dados e arquivo de log em discos separados, os discos recuperados após um desastre poderão conter uma cópia mais recente do arquivo de dados do que o arquivo de log, interrompendo o log write-ahead no SQL Server e as propriedades ACID das transações. Se você não tiver a opção de desabilitar a replicação geográfica na conta de armazenamento, deverá manter todos os dados e arquivos de log em determinado banco de dados no mesmo disco. Se você precisar usar mais de um disco devido ao tamanho do banco de dados, terá de implantar uma das soluções de recuperação de desastres listadas anteriormente para garantir a redundância dos dados.

## <a name="next-steps"></a>Próximas etapas
Se você precisar criar uma máquina virtual do Azure com SQL Server, consulte [Provisionando uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obter o melhor desempenho do SQL Server em execução em uma VM do Azure, consulte as diretrizes em [Práticas Recomentadas para o Desempenho do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Outros recursos
* [Instalar uma nova floresta do Active Directory no Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Criar um cluster de failover para grupos de disponibilidade em uma VM do Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

