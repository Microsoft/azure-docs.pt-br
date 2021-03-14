---
title: 'SAP no Azure: cenários com suporte com VMs do Azure'
description: Cenários com suporte de máquinas virtuais do Azure com carga de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 274db058f9f291d720fc350bb23f7bfdde2791e9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670929"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Carga de trabalho do SAP em cenários compatíveis com a máquina virtual do Azure
A criação da arquitetura de sistemas SAP NetWeaver, Business One `Hybris` ou S/4HANA no Azure abre muitas oportunidades diferentes para várias arquiteturas e ferramentas a serem usadas para chegar a uma implantação escalonável, eficiente e altamente disponível. Embora dependam do sistema operacional ou do DBMS usado, há restrições. Além disso, nem todos os cenários com suporte no local têm suporte da mesma maneira no Azure. Este documento orientará as configurações de não alta disponibilidade e as arquiteturas e configurações de alta disponibilidade com suporte usando VMs do Azure exclusivamente. Para cenários com suporte com [instâncias grandes do Hana](./hana-overview-architecture.md), verifique o artigo [cenários com suporte para instâncias grandes do Hana](./hana-supported-scenario.md). 


## <a name="2-tier-configuration"></a>configuração de duas camadas
Uma configuração de camada SAP 2 é considerada como sendo criada fora de uma camada combinada do DBMS do SAP e da camada de aplicativo que é executada no mesmo servidor ou unidade de VM. A segunda camada é considerada como a camada de interface do usuário. No caso de uma configuração de duas camadas, o DBMS e a camada de aplicativo SAP compartilham os recursos da VM do Azure. Como resultado, você precisa configurar os diferentes componentes de forma que eles não concorram em recursos. Você também precisa ter cuidado para não se inscrever nos recursos da VM. Essa configuração não fornece nenhuma alta disponibilidade, além dos contratos de [nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/) dos diferentes componentes do Azure envolvidos.

Uma representação gráfica de tal configuração pode ser semelhante a:

![Configuração de duas camadas simples](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Essas configurações têm suporte com Windows, Red Hat, SUSE e Oracle Linux para os sistemas DBMS do SQL Server, Oracle, DB2, maxDB e SAP ASE para casos de produção e não produção. Para SAP HANA como DBMS, esse tipo de configuração tem suporte apenas para casos de não produção. Isso também inclui o caso de implantação do [Azure Hana em instâncias grandes](./hana-overview-architecture.md) .
Para todas as combinações de so/DBMS com suporte no Azure, há suporte para esse tipo de configuração. No entanto, é obrigatório que você defina a configuração do DBMS e dos componentes do SAP de uma forma que os componentes DBMS e SAP não concorram por recursos de memória e CPU e, portanto, exceda os recursos físicos disponíveis. Isso precisa ser feito restringindo a memória que o DBMS tem permissão para alocar. Você também precisa limitar a memória estendida do SAP em instâncias do aplicativo. Você também precisa monitorar o consumo de CPU da VM em geral para garantir que os componentes não maximizem os recursos da CPU. 

> [!NOTE]
> Para sistemas SAP de produção, recomendamos a alta disponibilidade adicional e as configurações eventuals de recuperação de desastre, conforme descrito posteriormente neste documento


## <a name="3-tier-configuration"></a>configuração de três camadas
Nessas configurações, você separa a camada de aplicativo SAP e a camada DBMS em diferentes VMs. Normalmente, você faz isso para sistemas maiores e por motivos de ser mais flexível nos recursos da camada de aplicativo SAP. Na configuração mais simples, não há alta disponibilidade além dos contratos de [nível de serviço do Azure](https://azure.microsoft.com/support/legal/sla/) dos diferentes componentes do Azure envolvidos. 

A representação gráfica é semelhante a:

![Diagrama que mostra uma configuração simples de três camadas.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Esse tipo de configuração tem suporte no Windows, Red Hat, SUSE e Oracle Linux para os sistemas DBMS do SQL Server, Oracle, DB2, SAP HANA, maxDB e SAP ASE para casos de produção e não produção. Essa é a configuração de implantação padrão para [instâncias grandes do Azure Hana](./hana-overview-architecture.md). Para simplificar, não nos distinguemos entre os serviços SAP central e as instâncias de diálogo SAP na camada de aplicativo SAP. Nessa configuração simples de três camadas, não haveria proteção de alta disponibilidade para os serviços SAP central.

> [!NOTE]
> Para sistemas SAP de produção, recomendamos a alta disponibilidade adicional e as configurações eventuals de recuperação de desastre, conforme descrito posteriormente neste documento


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Várias instâncias de DBMS por VM ou unidade de instância grande do HANA
Nesse tipo de configuração, você hospeda várias instâncias de DBMS por VM do Azure ou unidade de instância grande do HANA. A motivação pode ser ter menos sistemas operacionais para manter e com esse custo reduzido. Outras motivações podem ter mais flexibilidade e mais eficiência, compartilhando recursos de uma VM maior ou uma unidade de instância grande do HANA entre várias instâncias de DBMS. Até agora, essas configurações estavam sendo exibidas principalmente para sistemas que não são de produção.

Uma configuração como essa poderia ser semelhante a:

![Várias instâncias DBMS em uma unidade](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Esse tipo de implantação de DBMS tem suporte para:

- SQL Server no Windows
- IBM DB2. Localizar detalhes no artigo [várias instâncias (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Para Oracle. Para obter detalhes, consulte [Nota de suporte sap #1778431](https://launchpad.support.sap.com/#/notes/1778431) e notas SAP relacionadas
- Por SAP HANA, várias instâncias em uma VM, o SAP chama esse método de implantação MCOS, tem suporte. Para obter detalhes, consulte o artigo do SAP [vários SAP HANA Systems em um host (MCOS)] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Executando várias instâncias de banco de dados em um host, você precisa certificar-se de que as diferentes instâncias não estão competindo por recursos e, portanto, excedem os limites de recursos físicos da VM. Isso é especialmente verdadeiro para a memória em que você precisa limitar a memória que qualquer um das instâncias que compartilham a VM pode alocar. Isso também pode ser verdadeiro para os recursos de CPU que as diferentes instâncias de banco de dados podem aproveitar. Todo o DBMS mencionado tem configurações que permitem limitar a alocação de memória e os recursos de CPU em um nível de instância.
Para ter suporte para essa configuração para VMs do Azure, espera-se que os discos ou volumes que são usados para os arquivos de log de dados e de log/restauração dos bancos de dados gerenciados pelas instâncias diferentes sejam separados. Ou, em outras palavras, os arquivos de log/restauração de dados ou logs de bancos de dado gerenciados por instância diferente do DBMS não devem compartilhar os mesmos discos ou volumes. 

A configuração de disco para instâncias grandes do HANA é fornecida configurada e é detalhada em [cenários com suporte para instâncias grandes do Hana](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> Para sistemas SAP de produção, recomendamos a alta disponibilidade adicional e as configurações eventuals de recuperação de desastre, conforme descrito posteriormente neste documento. Não há suporte para VMs com várias instâncias de DBMS com as configurações de alta disponibilidade descritas posteriormente neste documento.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Várias instâncias de caixa de diálogo do SAP em uma VM
Em muitos casos, várias instâncias de caixa de diálogo foram implantadas em servidores bare-metal ou até mesmo em VMs em execução em nuvens privadas. O motivo para essas configurações era adaptar certas instâncias de caixa de diálogo SAP a determinadas cargas de trabalho, funcionalidades de negócios ou tipos de carga de trabalho. O motivo para não isolar essas instâncias em VMs separadas era o esforço de manutenção e operações do sistema operacional. Ou, em vários casos, os custos no caso do hoster ou operador da VM estão solicitando uma taxa mensal por VM operada e administrada. No Azure, um cenário de Hospedagem de várias instâncias de caixa de diálogo do SAP em uma única VM dos EUA com suporte para fins de produção e não produção nos sistemas operacionais Windows, Red Hat, SUSE e Oracle Linux. O parâmetro de kernel SAP PHYS_MEMSIZE, disponível no Windows e em kernels Linux modernos, deve ser definido se várias instâncias do servidor de aplicativos SAP estiverem em execução em uma única VM. Também é recomendável limitar a expansão da memória estendida do SAP em sistemas operacionais, como o Windows, em que o aumento automático da memória estendida do SAP é implementado. Isso pode ser feito com o parâmetro de perfil do SAP `em/max_size_MB` .

Na configuração de três camadas, em que várias instâncias de caixa de diálogo do SAP são executadas em VMs do Azure podem ser semelhantes a:

![Diagrama que mostra uma configuração de três camadas em que várias instâncias de caixa de diálogo do SAP são executadas em VMs do Azure.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Para simplificar, não nos distinguemos entre os serviços SAP central e as instâncias de diálogo SAP na camada de aplicativo SAP. Nessa configuração simples de três camadas, não haveria proteção de alta disponibilidade para os serviços SAP central. Para sistemas de produção, não é recomendável deixar os serviços centrais do SAP desprotegidos. Para obter informações específicas sobre como chamadas de configurações de vários SID em relação às instâncias do SAP central e à alta disponibilidade dessas configurações de vários SID, consulte as seções posteriores deste documento.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Proteção de alta disponibilidade para a camada do DBMS do SAP
Ao procurar implantar sistemas de produção SAP, você precisa considerar o tipo de espera ativo de configurações de alta disponibilidade. Especialmente com SAP HANA, onde os dados precisam ser carregados na memória antes de serem capazes de obter o desempenho completo e a escalabilidade de volta, a recuperação de serviço do Azure não é uma medida ideal para alta disponibilidade. 

Em geral, a Microsoft dá suporte apenas a configurações de alta disponibilidade e pacotes de software descritos na seção de carga de trabalho do SAP em docs.microsoft.com. Você pode ler a mesma instrução no SAP Note [#1928533](https://launchpad.support.sap.com/#/notes/1928533). A Microsoft não fornecerá suporte para outras estruturas de software de terceiros de alta disponibilidade que não estão documentadas pela Microsoft em conjunto com a carga de trabalho do SAP. Nesses casos, o fornecedor de terceiros da estrutura de alta disponibilidade é a parte de suporte para a configuração de alta disponibilidade que precisa ser contratada por você como um cliente no processo de suporte. As exceções serão mencionadas neste artigo. 

Em geral, a Microsoft dá suporte a um conjunto limitado de configurações de alta disponibilidade em VMs do Azure ou em unidades de instâncias grandes do HANA. Para os cenários com suporte do HANA em instâncias grandes, leia os [cenários com suporte do documento para instâncias grandes do Hana](./hana-supported-scenario.md).

Para VMs do Azure, há suporte para as seguintes configurações de alta disponibilidade no nível do DBMS:

- SAP HANA a replicação do sistema com base no Linux pacemaker no SUSE e no Red Hat. Consulte os artigos detalhados:
    - [Alta disponibilidade do SAP HANA nas VMs do Azure no SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [Alta disponibilidade do SAP HANA em VMs do Azure no Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- SAP HANA configurações de escala horizontal de n + m usando [Azure NetApp files](https://azure.microsoft.com/services/netapp/) no SUSE e no Red Hat. Os detalhes são listados nestes artigos:
    - [Implantar um sistema de escalabilidade horizontal SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no SUSE Linux Enterprise Server}](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Implantar um sistema de expansão SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- SQL Server Cluster de failover com base nos serviços de arquivo do Windows Scale-Out. Embora a recomendação para sistemas de produção seja usar SQL Server Always On em vez de clustering. SQL Server Always On fornece melhor disponibilidade usando armazenamento separado. Os detalhes são descritos neste artigo: 
    - [Configurar uma instância de cluster de failover do SQL Server em máquinas virtuais do Azure](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always On tem suporte com o sistema operacional Windows para SQL Server no Azure. Essa é a recomendação padrão para as instâncias de SQL Server de produção no Azure. Os detalhes são descritos nestes artigos:
    - [Introdução aos grupos de disponibilidade do AlwaysOn do SQL Server em máquinas virtuais do Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
    - [Configurar um Grupo de Disponibilidade Always On em máquinas virtuais do Azure em diferentes regiões](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
    - [Configurar um balanceador de carga para um grupo de disponibilidade Always On no Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).
- Oracle Data Guard para Windows e Oracle Linux. Os detalhes de Oracle Linux podem ser encontrados neste artigo:
    - [Implementar o Oracle Data Guard em uma máquina virtual Linux do Azure](../oracle/configure-oracle-dataguard.md)
- A documentação detalhada do IBM DB2 HADR em SUSE e RHEL para SUSE e RHEL usando o pacemaker é fornecida aqui:
    - [Alta disponibilidade do IBM DB2 LUW em VMs do Azure em SUSE Linux Enterprise Server com pacemaker](./dbms-guide-ha-ibm.md)
    - [Alta disponibilidade do IBM Db2 LUW nas VMs do Azure no Red Hat Enterprise Linux Server](./high-availability-guide-rhel-ibm-db2-luw.md)
- Configuração do SAP ASE e do SAP maxDB conforme detalhado nestes documentos:
    - [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](./dbms_guide_sapase.md)
    - [SAP MaxDB, liveCache e implantação do Servidor de Conteúdo nas VMs do Azure](./dbms_guide_maxdb.md)
- Os cenários de alta disponibilidade do HANA em instâncias grandes são detalhados em:
    - [Cenários com suporte para instâncias grandes do HANA – HSR com STONITH para alta disponibilidade](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [Cenários com suporte para o HANA em instâncias grandes – failover automático do host (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> Para nenhum dos cenários descritos acima, damos suporte a configurações de várias instâncias de DBMS em uma VM. Significa que, em cada um dos casos, apenas uma instância de banco de dados pode ser implantada por VM e protegida com os métodos de alta disponibilidade descritos. **Não** há suporte para a proteção de várias instâncias de DBMS no mesmo cluster de failover do Windows ou pacemaker neste momento. Também há suporte para o Oracle Data Guard somente para casos de implantação de instância única por VM. 

Vários sistemas de banco de dados permitem hospedar vários bancos de dados em uma instância DBMS. Como no caso de SAP HANA, vários bancos de dados podem ser hospedados em vários contêineres de banco de dados (MDC). Para casos em que essas configurações de vários bancos de dados estão funcionando em um recurso de cluster de failover, há suporte para essas configurações. As configurações que não são suportadas são casos em que vários recursos de cluster seriam necessários. Para configurações em que você definiria vários grupos de disponibilidade SQL Server, em uma instância de SQL Server.


![Configuração de HA do DBMS](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Dependendo do DBMS, um/ou sistemas operacionais, componentes como o balanceador de carga do Azure podem ou não ser necessários como parte da arquitetura da solução. 

Especificamente para maxDB, a configuração de armazenamento precisa ser diferente. No maxDB, os arquivos de dados e de log precisam estar localizados no armazenamento compartilhado para configurações de alta disponibilidade. Somente no caso do maxDB, o armazenamento compartilhado tem suporte para alta disponibilidade. Para todas as outras pilhas de armazenamento separadas do DBMS por nó estão as únicas configurações de disco com suporte.

Outras estruturas de alta disponibilidade são conhecidas e também são conhecidas para serem executadas em Microsoft Azure. No entanto, a Microsoft não testei essas estruturas. Se você quiser criar sua configuração de alta disponibilidade com essas estruturas, será necessário trabalhar com o provedor do software para:

- Desenvolver uma arquitetura de implantação
- Implantação da arquitetura
- Suporte da arquitetura

> [!IMPORTANT]
> O Microsoft Azure Marketplace oferece uma variedade de dispositivos de software que fornecem soluções de armazenamento sobre o armazenamento nativo do Azure. Esses dispositivos de software podem ser usados para criar compartilhamentos NFS e, teoricamente, poderiam ser usados nas implantações de expansão SAP HANA em que um nó em espera é necessário. Devido a vários motivos, nenhum desses aparelhos de software de armazenamento tem suporte para qualquer uma das implantações de DBMS da Microsoft e do SAP no Azure. Não há suporte para implantações de DBMS em compartilhamentos SMB no momento. As implantações do DBMS em compartilhamentos NFS são limitadas a compartilhamentos NFS 4,1 em [Azure NetApp files](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Alta disponibilidade para o serviço central do SAP
Os serviços centrais do SAP são um segundo ponto único de falha de sua configuração do SAP. Como resultado, você também precisaria proteger esses processos de serviços centrais. A oferta com suporte e documentado para leituras de carga de trabalho SAP, como:

- Servidor de cluster de failover do Windows usando serviços de arquivo de expansão do Windows para sapmnt e diretório de transporte global. Os detalhes são descritos no artigo:
    - [Clustering de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o arquivo compartilhado de cluster no Azure](./sap-high-availability-guide-wsfc-file-share.md)
    - [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Servidor de cluster de failover do Windows usando compartilhamento SMB com base em [Azure NetApp files](https://azure.microsoft.com/services/netapp/) para sapmnt e diretório de transporte global. Os detalhes estão listados no artigo:
    - [Alta disponibilidade para SAP NetWeaver em VMs do Azure no Windows com Azure NetApp Files (SMB) para aplicativos SAP](./high-availability-guide-windows-netapp-files-smb.md)
- Servidor de cluster de failover do Windows baseado em SIOS `Datakeeper` . Embora documentado pela Microsoft, você precisa de uma relação de suporte com SIOS, portanto, você pode se envolver com o suporte SIOS ao usar essa solução. Os detalhes são descritos no artigo:
    - [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Prepare a infraestrutura do Azure para alta disponibilidade do SAP usando o cluster de failover do Windows e o disco compartilhado para a instância SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker no sistema operacional SUSE com a criação de um compartilhamento NFS altamente disponível usando duas VMs SUSE e `drdb` para a replicação de arquivos. Os detalhes estão documentados no artigo
    - [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP](./high-availability-guide-suse.md)
    - [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- Sistema operacional pacemaker SUSE com aproveitamento de compartilhamentos NFS fornecidos pelo [Azure NetApp files](https://azure.microsoft.com/services/netapp/). Os detalhes estão documentados em
    - [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o Azure NetApp Files para aplicativos SAP](./high-availability-guide-suse-netapp-files.md)
- Pacemaker no sistema operacional Red Hat com compartilhamento NFS hospedado em um `glusterfs` cluster. Os detalhes podem ser encontrados nos artigos
    - [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker no sistema operacional Red Hat com compartilhamento NFS hospedado em [Azure NetApp files](https://azure.microsoft.com/services/netapp/). Os detalhes são descritos no artigo
    - [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP](./high-availability-guide-rhel-netapp-files.md)

Das soluções listadas, você precisa de uma relação de suporte com SIOS para dar suporte ao `Datakeeper` produto e para se envolver com sios diretamente em caso de problemas. Dependendo do modo como você licenciou o Windows, o Red Hat e/ou o sistema operacional SUSE, você também pode precisar ter um contrato de suporte com seu provedor de sistema operacional para ter suporte total das configurações de alta disponibilidade listadas.

A configuração também pode ser exibida como:

![Configuração de alta disponibilidade do DBMS e do ASCS](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

No lado direito dos gráficos, os serviços centrais do SAP altamente disponíveis são mostrados. Além de ter os serviços centrais do SAP protegidos com uma estrutura de cluster de failover que pode fazer failover em caso de um problema, há uma necessidade de um compartilhamento de NFS ou SMB altamente disponível, ou de um disco compartilhado do Windows para garantir que o sapmnt e o diretório de transporte global estejam disponíveis independentemente da existência de uma única VM. Algumas soluções adicionais, como o servidor de cluster de failover do Windows e o pacemaker, exigirão que um Azure Load Balancer direcione ou direcione o tráfego para um nó íntegro.

Na lista mostrada, não há nenhuma menção do sistema operacional Oracle Linux. Oracle Linux não oferece suporte a pacemaker como uma estrutura de cluster. Se você quiser implantar seu sistema SAP em Oracle Linux e precisar de uma estrutura de alta disponibilidade para Oracle Linux, você precisará trabalhar com fornecedores terceirizados. Um dos fornecedores é SIOS com seu conjunto de proteção para Linux com suporte do SAP no Azure. Para obter mais informações, leia SAP Note [#1662610-detalhes de suporte para o sios Protection Suite para Linux](https://launchpad.support.sap.com/#/notes/1662610) para obter mais detalhes.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Armazenamento com suporte com os cenários de serviços centrais do SAP listados acima
Como apenas um subconjunto de tipos de armazenamento do Azure está fornecendo compartilhamentos NFS ou SMB altamente disponíveis, essa qualidade para o uso em nossos cenários de cluster de serviços centrais SAP é uma lista de tipos de armazenamento com suporte

- O servidor de cluster de failover do Windows com servidor de arquivos de escalabilidade horizontal do Windows pode ser implantado em todos os tipos de armazenamento do Azure nativos, exceto Azure NetApp Files. No entanto, a recomendação é aproveitar o armazenamento Premium devido a contratos de nível de serviço superiores em taxa de transferência e IOPS.
- O servidor de cluster de failover do Windows com SMB no Azure NetApp Files tem suporte no Azure NetApp Files. **Não** há suporte para compartilhamentos SMB nos serviços de arquivos do Azure neste momento.
- O servidor de cluster de failover do Windows com disco compartilhado do Windows baseado em SIOS `Datakeeper` pode ser implantado em todos os tipos de armazenamento do Azure nativos, exceto Azure NetApp files. No entanto, a recomendação é aproveitar o armazenamento Premium devido a contratos de nível de serviço superiores em taxa de transferência e IOPS.
- O SUSE ou o Red Hat pacemaker usando compartilhamentos NFS em Azure NetApp Files tem suporte no Azure NetApp Files. 
- O SUSE pacemaker usando uma `drdb` configuração entre duas VMs tem suporte usando tipos de armazenamento nativos do Azure, exceto Azure NetApp files. No entanto, a recomendação é aproveitar o armazenamento Premium devido a contratos de nível de serviço superiores em taxa de transferência e IOPS.
- O Red Hat pacemaker usando `glusterfs` para fornecer o compartilhamento NFS tem suporte usando tipos de armazenamento nativos do Azure, exceto Azure NetApp files. No entanto, a recomendação é aproveitar o armazenamento Premium devido a contratos de nível de serviço superiores em taxa de transferência e IOPS.

> [!IMPORTANT]
> O Microsoft Azure Marketplace oferece uma variedade de dispositivos de software que fornecem soluções de armazenamento sobre o armazenamento nativo do Azure. Esses dispositivos de software podem ser usados para criar compartilhamentos de NFS ou SMB, bem que, teoricamente, poderiam ser usados também nos serviços SAP centrais de cluster de failover. Essas soluções não têm suporte direto para a carga de trabalho do SAP pela Microsoft. Se você decidir usar uma solução desse tipo para criar seu compartilhamento NFS ou SMB, o suporte para a configuração do serviço central SAP precisará ser fornecido pela terceiros que possui o software no dispositivo de software de armazenamento.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Clusters de failover de serviços centrais SAP de vários SIDs
Para reduzir o número de VMs que são necessárias em grandes cenários do SAP, o SAP permite executar instâncias de serviços SAP central de vários sistemas SAP diferentes na configuração de cluster de failover. Imagine casos em que você tem 30 ou mais sistemas de produção NetWeaver ou S/4HANA. Sem o clustering de vários SID, essas configurações exigirão 60 ou mais VMs em 30 ou mais configurações de cluster de failover do Windows ou pacemaker. Além dos clusters de failover do DBMS necessários. A implantação de vários serviços SAP central em dois nós em uma configuração de cluster de failover pode reduzir significativamente o número de VMs. No entanto, a implantação de várias instâncias de serviços SAP central em uma única configuração de cluster de dois nós também tem algumas desvantagens. Os problemas relacionados a uma única VM na configuração do cluster se aplicam a vários sistemas SAP. A manutenção no SO convidado em execução na configuração do cluster requer mais coordenação, já que vários sistemas SAP de produção são afetados. Ferramentas como o SAP LaMa não dão suporte a clustering de vários SIDs no processo de clonagem do sistema.

No Azure, uma configuração de cluster de vários SID tem suporte para o sistema operacional Windows com ENSA1 e ENSA2. A recomendação não combina a ENSA1 (arquitetura de serviço de replicação de enfileiramento) mais antiga com a nova arquitetura (ENSA2) em um cluster de vários SID. Os detalhes sobre essa arquitetura são documentados nos artigos

- [Alta disponibilidade de vários SIDs da instância do SAP ASCS/SCS com clustering de failover do Windows Server e disco compartilhado no Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [Alta disponibilidade de vários SIDs da instância do SAP ASCS com clustering de failover do Windows Server e compartilhamento de arquivos no Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

Para o SUSE, um cluster de vários SID baseado em pacemaker também tem suporte. Até agora, a configuração tem suporte para:

- Um máximo de cinco instâncias do SAP ASCS/SCS
- A antiga arquitetura de Ice do servidor de replicação (ENSA1)
- Configurações de cluster pacemaker de dois nós

A configuração está documentada em [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para o guia de vários Sid de aplicativos SAP](./high-availability-guide-suse-multi-sid.md)

Um cluster de vários SID com o Queue Replication Server tem a aparência esquemático

![Diagrama que mostra um cluster de vários SID com o servidor de replicação de enfileiramento.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Cenários de escala horizontal SAP HANA
SAP HANA cenários de expansão têm suporte para um subconjunto das VMs do Azure com certificação do HANA, conforme listado no [diretório SAP Hana hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Todas as VMs marcadas com ' Sim ' na coluna ' clustering ' podem ser usadas para a expansão de OLAP ou S/4HANA. As configurações sem espera têm suporte com os tipos de armazenamento do Azure de: 

- Armazenamento Premium do Azure, incluindo o acelerador de gravação do Azure para o volume/Hana/log
- [Disco Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA configurações de expansão para OLAP ou S/4HANA com nó (s) em espera têm suporte exclusivo com o NFS compartilhado hospedado em Azure NetApp Files.

Para obter mais informações sobre as configurações de armazenamento exatas com ou sem o nó em espera, verifique os artigos:

- [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](./hana-vm-operations-storage.md) 
- [Implantar um sistema de expansão SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Implantar um sistema de expansão SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [#2080991 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2080991)

Para obter detalhes sobre as configurações de escala horizontal HANA com suporte de instâncias grandes do HANA, a seguinte documentação se aplica:

- [Cenários com suporte para expansão de instâncias grandes do HANA com em espera](./hana-supported-scenario.md#scale-out-with-standby)
- [Cenários com suporte para expansão de instâncias grandes do HANA sem espera](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Cenário de recuperação de desastre
Há uma variedade de cenários de recuperação de desastres com suporte. Definimos arquiteturas de desastre como arquiteturas que devem compensar uma região completa do Azure que está saindo da grade. Isso significa que precisamos que o destino de recuperação de desastre seja uma região do Azure diferente como destino para executar sua estrutura SAP. Separamos métodos e configurações na camada DBMS e na camada não DBMS. 

### <a name="dbms-layer"></a>Camada DBMS
Para a camada do DBMS, as configurações que usam os mecanismos de replicação nativa do DBMS, como Always On, Oracle Data Guard, DB2 HADR, SAP ASE Always on ou replicação do sistema HANA têm suporte. É obrigatório que o fluxo de replicação nesses casos seja assíncrono, em vez de síncrono, como em cenários de alta disponibilidade típicos que são implantados em uma única região do Azure. Um exemplo típico de tal configuração de recuperação de desastre do DBMS com suporte é descrito no artigo [SAP Hana disponibilidade nas regiões do Azure](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions). O segundo gráfico nessa seção descreve um cenário com o HANA como exemplo. Os bancos de dados principais com suporte para aplicativos SAP são capazes de ser implantados em um cenário como esse.

Há suporte para usar uma VM menor como instância de destino na região de recuperação de desastre, pois essa VM não experimenta o tráfego de carga de trabalho completo. Fazendo isso, você precisa ter as seguintes considerações em mente:

- Tipos de VM menores não permitem que muitos discos conectados do que VMs menores
- VMs menores têm menos taxa de transferência de rede e armazenamento
- Redimensionar entre famílias de VMs pode ser um problema quando as diferentes VMs são coletadas em um conjunto de disponibilidade do Azure ou quando o redimensionamento deve ocorrer entre a família da série M e a família Mv2 de VMs
- Consumo de CPU e memória para a instância do banco de dados poder receber o fluxo de alterações com atraso mínimo e recursos de CPU e memória suficientes para aplicar essas alterações com atraso mínimo nos dados  

Mais detalhes sobre as limitações de diferentes tamanhos de VM podem ser encontrados [aqui](../../sizes.md) 

Outro método com suporte para implantar um destino de DR é ter uma segunda instância de DBMS instalada em uma VM que executa uma instância de DBMS de não produção de uma instância SAP que não seja de produção. Isso pode ser um pouco mais desafiador, pois você precisa descobrir o que acontece na memória, recursos de CPU, largura de banda de rede e largura de banda de armazenamento para as instâncias de destino específicas que devem funcionar como instância principal no cenário de DR. Especialmente no HANA, é altamente recomendável que você esteja configurando a instância que funciona como destino de DR em um host compartilhado para que os dados não sejam pré-carregados na instância de destino de DR.

Para cenários de DR de instância grande do HANA, verifique estes documentos:

- [Nó único com DR usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Nó único com DR (multipropósito) usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Nó único com DR (multipropósito) usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Alta disponibilidade com HSR e DR com replicação de armazenamento](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Escalar horizontalmente com recuperação de desastre usando replicação de armazenamento](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Nó único com DR usando HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [HSR de nó único para DR (com otimização de custo)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Alta disponibilidade e recuperação de desastre com o HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Alta disponibilidade e recuperação de desastre com HSR (com otimização de custo)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Escale horizontalmente com DR usando HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> O uso de [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) não foi testado para implantações de DBMS na carga de trabalho do SAP. Como resultado, não há suporte para a camada DBMS de sistemas SAP neste momento. Não há suporte para outros métodos de replicação da Microsoft e do SAP que não estão listados. O uso de software de terceiros para replicar a camada DBMS de sistemas SAP entre diferentes regiões do Azure, precisa ter suporte do fornecedor do software e não terá suporte por meio dos canais de suporte da Microsoft e do SAP. 
 
## <a name="non-dbms-layer"></a>Camada não DBMS
Para a camada de aplicativo SAP e compartilhamentos eventuals ou locais de armazenamento que são necessários, os dois principais cenários são aproveitados pelos clientes:

- Os destinos de recuperação de desastre na segunda região do Azure não estão sendo usados para nenhuma finalidade de produção ou de não produção. Nesse cenário, as VMs que funcionam como destino de recuperação de desastre são idealmente não implantadas e a imagem e as alterações nas imagens da camada de aplicativo SAP de produção são replicadas para a região de recuperação de desastre. Uma funcionalidade que pode executar tal tarefa é [Azure site Recovery](../../../site-recovery/azure-to-azure-move-overview.md). Azure Site Recovery dar suporte a cenários de replicação do Azure para o Azure como este. 
- Os destinos de recuperação de desastre são VMs que estão realmente em uso por sistemas que não são de produção. Toda a estrutura do SAP é distribuída entre duas regiões diferentes do Azure com sistemas de produção geralmente em uma região e sistemas de não produção em outra região. Em muitas implantações de clientes, o cliente tem um sistema que não é de produção equivalente a um sistema de produção. O cliente tem instâncias de aplicativo de produção pré-instaladas nos sistemas de não produção da camada de aplicativo. No caso de um failover, as instâncias de não produção seriam desligadas, os nomes virtuais das VMs de produção foram movidos para as VMs de não produção (depois de atribuir novos endereços IP no DNS) e as instâncias de produção pré-instaladas estão sendo iniciadas

### <a name="sap-central-services-clusters"></a>Clusters de serviços centrais SAP
Os clusters de serviços centrais do SAP que estão usando discos compartilhados (Windows), compartilhamentos SMB (Windows) ou compartilhamentos NFS são um pouco mais difíceis de replicar. No lado do Windows, a replicação de armazenamento do Windows é uma solução possível. No Linux rsync, há uma solução viável.



## <a name="non-supported-scenario"></a>Cenário sem suporte
Há uma lista de cenários, que não têm suporte para carga de trabalho do SAP em arquiteturas do Azure. **Sem suporte** significa que a SAP e a Microsoft não poderão oferecer suporte a essas configurações e precisarão adiar para um eventual envolvimento de terceiros que forneciam software para estabelecer tais arquiteturas. Duas das categorias são:

- Dispositivos de software de armazenamento: há uma série de aparelhos de software de armazenamento oferecidos no Azure Marketplace. Alguns dos fornecedores oferecem documentação própria sobre como usar esses dispositivos de software de armazenamento no Azure relacionados a softwares SAP. O suporte a configurações ou implantações que envolvem esses dispositivos de armazenamento de software precisa ser fornecido pelo fornecedor desses dispositivos de software de armazenamento. Esse fato também é manifestado na [Observação de suporte do SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Estruturas de alta disponibilidade: somente pacemaker e cluster de failover do Windows Server são estruturas de alta disponibilidade com suporte para carga de trabalho do SAP no Azure. Como mencionado anteriormente, a solução de SIOS `Datakeeper` é descrita e documentada pela Microsoft. No entanto, os componentes de SIOS `Datakeeper` precisam ter suporte por sios como o fornecedor que fornece esses componentes. A SAP também listou outras estruturas de alta disponibilidade certificadas em várias notas SAP. Alguns deles foram certificados pelo fornecedor de terceiros para o Azure também. No entanto, o suporte para configurações que usam esses produtos precisa ser fornecido pelo fornecedor do produto. Fornecedores diferentes têm integração diferente aos processos de suporte do SAP. Você deve esclarecer o que o processo de suporte funciona melhor para o fornecedor específico antes de decidir usar o produto nas configurações do SAP implantadas no Azure.
- Os clusters de discos compartilhados nos quais os arquivos de banco de dados estão residentes nos discos compartilhados não têm suporte com a exceção de maxDB. Para todos os outros bancos de dados, a solução com suporte é ter locais de armazenamento separados em vez de um compartilhamento SMB ou NFS ou um disco compartilhado para configurar cenários de alta disponibilidade

Outros cenários, que não são suportados, são cenários como:

- Cenários de implantação que introduzem uma latência de rede maior entre a camada de aplicativo SAP e a camada do SAP DBMS na arquitetura comum do SAP, conforme mostrado em NetWeaver, S/4HANA e, por exemplo `Hybris` ,. Isso inclui:
    - Implantar uma das camadas no local enquanto a outra camada é implantada no Azure
    - Implantando a camada de aplicativo SAP de um sistema em uma região do Azure diferente da camada DBMS
    - Implantando uma camada em data centers que estão colocalizados no Azure e na outra camada no Azure, exceto onde esses padrões de arquitetura são fornecidos por um serviço nativo do Azure
    - Implantando dispositivos de rede virtual entre a camada de aplicativo SAP e a camada DBMS
    - Aproveitando o armazenamento hospedado em data centers colocalizados no datacenter do Azure para a camada do SAP DBMS ou o diretório de transporte global do SAP
    - Implantação de duas camadas com dois fornecedores de nuvem diferentes. Por exemplo, implantar a camada DBMS na infraestrutura de nuvem do Oracle e a camada de aplicativo no Azure
- Configurações de cluster pacemaker HANA de várias instâncias
- Configurações de cluster do Windows com discos compartilhados por meio de SOFS ou SMB em seja para bancos de dados SAP com suporte no Windows. Em vez disso, recomendamos o uso da replicação de alta disponibilidade nativa dos bancos de dados específicos e usam pilhas de armazenamento separadas
- Implantação de bancos de dados SAP com suporte no Linux com arquivos de banco de dados localizados em compartilhamentos NFS na parte superior do seja, com exceção de SAP HANA
- Implantação do Oracle DBMS em qualquer outro SO convidado do que Windows e Oracle Linux. Consulte também a [Observação de suporte SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Cenário (s) que não testamos e, portanto, não temos experiência com lista como:

- Azure Site Recovery replicando VMs da camada DBMS. Como resultado, recomendamos aproveitar a funcionalidade de replicação assíncrona nativa do banco de dados para a configuração de recuperação de desastres em potencial
 

## <a name="next-steps"></a>Próximas etapas
Leia as próximas etapas no [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](./planning-guide.md)




  