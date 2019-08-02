---
title: Configurações de carga de trabalho do SAP com Zonas de Disponibilidade do Azure | Microsoft Docs
description: Arquitetura e cenários de alta disponibilidade para SAP NetWeaver usando Zonas de Disponibilidade do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ead1dfdce4bf3a803eee46a536dc7062626640d9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234247"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurações de carga de trabalho do SAP com Zonas de Disponibilidade do Azure
As [Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) são um dos recursos de alta disponibilidade que o Azure oferece. Usar as Zonas de Disponibilidade melhora a disponibilidade geral das cargas de trabalho do SAP no Azure. Este recurso já está disponível em algumas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). No futuro, ele estará disponível em mais regiões.

Este gráfico mostra a arquitetura básica de alta disponibilidade do SAP:

![Configuração de alta disponibilidade padrão](./media/sap-ha-availability-zones/standard-ha-config.png)

A camada de aplicativo do SAP é implantada em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) do Azure. Para ter uma alta disponibilidade do SAP Central Services, você pode implantar duas VMs em um conjunto de disponibilidade separado. Use o Cluster de Failover do Windows Server ou o Pacemaker (Linux) como uma estrutura de alta disponibilidade com failover automático para o caso de haver um problema de infraestrutura ou de software. Para saber mais sobre essas implantações, consulte:

- [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Uma arquitetura semelhante se aplica à camada do gerenciador de banco de dados (DBMS) dos sistemas SAP NetWeaver, S/4HANA ou Hybris. Você deve implantar a camada do DBMS em um modo ativo/passivo com uma solução de cluster de failover para proteger contra falhas de infraestrutura ou de software. A solução de cluster de failover pode ser uma estrutura de failover específica do DBMS, um Cluster de Failover do Windows Server ou o Pacemaker.

Para implantar a mesma arquitetura usando as Zonas de Disponibilidade do Azure, é preciso fazer algumas alterações na arquitetura descrita anteriormente. Este artigo descreve essas alterações.

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerações sobre a implantação em Zonas de Disponibilidade


Ao usar as Zonas de Disponibilidade, considere o seguinte:

- Não há qualquer garantia em relação às distâncias entre várias Zonas de Disponibilidade dentro de uma região do Azure.
- As Zonas de Disponibilidade não são uma solução ideal de recuperação de desastres (DR). Desastres naturais podem causar danos extensos em algumas regiões do mundo, incluindo danos graves a infraestruturas de energia. As distâncias entre várias zonas podem não ser suficientemente grandes para constituir uma solução de DR apropriada.
- A latência de rede entre Zonas de Disponibilidade não é a mesma em todas as regiões do Azure. Em alguns casos, você pode implantar e executar a camada de aplicativo do SAP em diferentes zonas, pois a latência da rede de uma zona para a VM do DBMS ativo é aceitável. Porém, em algumas regiões do Azure, a latência entre a VM do DBMS ativo e uma instância do aplicativo SAP, quando implantada em diferentes zonas, pode não ser aceitável para os processos empresariais do SAP. Nesses casos, as arquiteturas de implantação precisam ser diferentes com uma arquitetura ativa/ativa para o aplicativo ou uma arquitetura ativa/passiva quando a latência de rede entre zonas é muito alta.
- Ao decidir onde usar as Zonas de Disponibilidade, baseie sua decisão na latência de rede entre as zonas. A latência da rede desempenha um papel importante em duas áreas:
    - A latência entre as duas instâncias de DBMS que precisam ter replicação síncrona. Quanto maior a latência de rede, maior a probabilidade de afetar a escalabilidade da sua carga de trabalho.
    - A diferença na latência de rede entre uma VM que executa uma instância de diálogo do SAP na zona com a instância do DBMS ativo e uma VM semelhante em outra zona. Conforme essa diferença aumenta, a influência sobre o tempo de execução de processos de negócios e de tarefas em lote também aumenta, dependendo de eles serem executados na zona com o DBMS ou em uma zona diferente.

Ao implantar VMs do Azure em Zonas de Disponibilidade e estabelecer soluções de failover na mesma região do Azure, há algumas restrições:

- Use o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) ao implantar em Zonas de Disponibilidade do Azure. 
- O mapeamento de enumerações de zona para as zonas físicas é fixado por assinatura do Azure. Se você estiver usando assinaturas diferentes para implantar seus sistemas de SAP, precisará definir as zonas ideais para cada assinatura.
- Você não pode implantar conjuntos de disponibilidade do Azure em uma zona de disponibilidade do Azure, a menos que use o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). A maneira como você pode implantar a camada do DBMS do SAP e os serviços centrais entre as zonas e, ao mesmo tempo, implantar a camada de aplicativo SAP usando conjuntos de disponibilidade e ainda atingir a proximidade das VMs está documentada no artigo [posicionamento de proximidade do Azure Grupos para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md). Se não estiver aproveitando os grupos de posicionamento de proximidade do Azure, você precisará escolher um ou outro como uma estrutura de implantação para máquinas virtuais.
- Não é possível usar um [Azure Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) para criar soluções de cluster de failover baseadas no Cluster de Failover do Windows Server ou no Linux Pacemaker. Em vez disso, use a [SKU do Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Combinação ideal para as Zonas de Disponibilidade
Antes de decidir como usar as Zonas de Disponibilidade, é preciso determinar:

- A latência de rede entre as três zonas de uma região do Azure. Isso permite que você escolha as zonas com menor latência de rede no tráfego de rede entre zonas.
- A diferença entre a latência entre VMs em uma das zonas, de sua escolha, e a latência de rede em duas zonas de sua escolha.
- Uma determinação indicando se os tipos de VM que você precisa implantar estão disponíveis nas duas zonas selecionadas. Com algumas VMs, especialmente VMs da série M, você pode encontrar situações em que algumas SKUs estão disponíveis em apenas duas das três zonas.

## <a name="network-latency-between-and-within-zones"></a>Latência de rede entre e dentro de zonas
Para determinar a latência entre as diferentes zonas, é preciso:

- Implantar a SKU da VM que você deseja usar para sua instância do DBMS nas três zonas. Certifique-se de que a [Rede Acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) esteja habilitada ao executar essa medição.
- Ao encontrar as duas zonas com menos latência de rede, implante outras três VMs da SKU da VM que você deseja usar como VM da camada de aplicativo nas três Zonas de Disponibilidade. Meça a latência de rede em relação às duas VMs do DBMS nas duas zonas do DBMS que você selecionou. 
- Use **niping** como uma ferramenta de medição. Essa ferramenta de SAP é descrita nas notas de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentre-se nos comandos documentados para medições de latência. Como o **ping** não funciona nos caminhos de código da Rede Acelerada do Azure, não é recomendável usá-lo.

Com base nas medições e na disponibilidade de suas SKUs de VM em diferentes Zonas de Disponibilidade, você precisa tomar as algumas decisões:

- Definir as zonas ideais para a camada do DBMS.
- Determinar se você deseja distribuir sua camada ativa de aplicativo do SAP em uma, duas ou em todas as três zonas, com base nas diferenças de latência de rede na zona versus entre as zonas.
- Determinar se você deseja implantar uma configuração ativa/passiva ou uma configuração ativa/ativa do ponto de vista do aplicativo. (Essas configurações são explicadas mais adiante neste artigo.)

Ao tomar essas decisões, considere também as recomendações de latência de rede do SAP, conforme documentado na nota do SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> As medições e decisões que você toma são válidas para a assinatura do Azure usada ao fazer as medições. Se você usar outra assinatura do Azure, repita as medições. O mapeamento de zonas enumeradas pode ser diferente para outra assinatura do Azure.


> [!IMPORTANT]
> Espera-se que as medições descritas anteriormente forneçam resultados diferentes em todas as regiões do Azure que oferecem suporte a [Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Mesmo quando seus requisitos de latência de rede são os mesmos, talvez seja necessário adotar estratégias de implantação distintas em diferentes regiões do Azure, já que a latência de rede entre as zonas pode diferir. Em algumas regiões do Azure, a latência de rede entre as três zonas diferentes pode ser muito diferente. Em outras regiões, a latência de rede entre as três zonas diferentes pode ser mais uniforme. A declaração de que há sempre uma latência de rede entre 1 e 2 milissegundos não está correta. A latência de rede entre Zonas de Disponibilidade nas regiões do Azure não pode ser generalizada.

## <a name="activeactive-deployment"></a>Implantação ativa/ativa
Essa arquitetura de implantação é chamada ativa/ativa porque você implanta seus servidores de aplicativos SAP ativos em duas ou três zonas. A instância do SAP Central Services que usa a replicação de enfileiramento é implantada entre duas zonas. O mesmo vale para a camada de DBMS, que é implantada nas mesmas zonas que o SAP Central Service.

Ao considerar essa configuração, você precisa encontrar as duas Zonas de Disponibilidade em sua região que oferecem uma latência de rede entre zonas aceitável para sua carga de trabalho e para sua replicação síncrona de DBMS. Também é preciso ter certeza de que o delta entre a latência de rede nas zonas selecionadas e a latência de rede entre zonas não seja muito grande. Isso ocorre porque você não deseja grandes variações, dependendo se uma tarefa é executada na zona com o servidor DBMS ou entre zonas, nos tempos de execução de seus processos de negócios ou tarefas em lote. Algumas variações são aceitáveis, mas não fatores de diferença.

Um esquema simplificado de implantação ativa/ativa entre duas zonas pode ser semelhante a:

![Implantação de zona ativa/ativa](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

As seguintes considerações se aplicam a essa configuração:

- Não usando o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), você trata o zonas de disponibilidade do Azure como domínios de falha e atualização para todas as VMs porque os conjuntos de disponibilidade não podem ser implantados no zonas de disponibilidade do Azure.
- Se você quiser combinar implantações zonais para a camada DBMS e os serviços centrais, mas quiser usar os conjuntos de disponibilidade do Azure para a camada de aplicativo, será necessário usar os grupos de proximidade do Azure, conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para o ideal latência de rede com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Para os balanceadores de carga dos clusters de failover do SAP Central Services e da camada do DBMS, você precisa usar a [SKU padrão do Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Load Balancer básico não funciona entre as zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP com suas sub-redes é ampliada entre as zonas. Não é necessário ter redes virtuais separadas para cada zona.
- Em todas as máquinas virtuais implantadas, você precisa usar [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados em implantações de zona.
- O Armazenamento Premium do Azure e o [Armazenamento SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a qualquer tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que replique esses discos compartilhados ou compartilhamentos entre as zonas. Há suporte para as seguintes tecnologias:
  - No Windows, uma solução de cluster que usa o SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
  - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    No momento, não há suporte nas zonas para a solução que usa o Servidor de Arquivos de Escalabilidade Horizontal da Microsoft, conforme documentado no artigo [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share).
- A terceira zona é usada para hospedar o dispositivo SBD, caso você crie um [cluster do SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais.
- Para obter a consistência do tempo de execução para processos comerciais críticos, você pode tentar direcionar determinados trabalhos e usuários do lote para instâncias de aplicativo que estão em zona com a instância do DBMS ativa usando grupos de servidores do lote SAP, grupos de logon SAP ou grupos de RFC. No entanto, no caso de um failover da zona, seria preciso mover manualmente esses grupos para as instâncias em execução nas VMs da zona com a VM do DB ativo.  
- Talvez você queira implantar instâncias de caixa de diálogo inativas em cada uma das zonas. Isso serve para habilitar um retorno imediato à capacidade do recurso anterior se uma zona usada por parte de suas instâncias do aplicativo estiver fora de serviço.


## <a name="activepassive-deployment"></a>Implantação ativa/passiva
Caso não consiga encontrar um delta aceitável entre a latência de rede dentro de uma zona e a latência do tráfego de rede entre zonas, implante uma arquitetura que tem um caráter ativo/passivo do ponto de vista da camada de aplicativo SAP. Defina uma zona *ativa*, que é a zona em que você implantará a camada de aplicativo completa e onde você tentará executar o DBMS ativo e a instância do SAP Central Services. Com essa configuração, você garante que não haja variações extremas de tempo de execução, dependendo se uma tarefa é executada na zona com a instância do DBMS ativo ou não, em transações comerciais e tarefas em lote.

O layout básico da arquitetura é semelhante a este:

![Implantação de zona ativa/passiva](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

As seguintes considerações se aplicam a essa configuração:

- Não é possível implantar conjuntos de disponibilidade em Zonas de Disponibilidade do Azure. Para compensar isso, você pode usar grupos de posicionamento de proximidade do Azure, conforme documentado no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Ao usar essa arquitetura, monitore o status de perto e tente manter as instâncias de DBMS e SAP Central Services ativas na mesma zona da sua camada de aplicativo implantada. Caso ocorra um failover do SAP Central Service ou da instância do DBMS, certifique-se de que você possa realizar o failover manual de volta para a zona com a camada de aplicativo SAP implantada o mais rápido possível.
- Para os balanceadores de carga dos clusters de failover do SAP Central Services e da camada do DBMS, você precisa usar a [SKU padrão do Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Load Balancer básico não funciona entre as zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP com suas sub-redes é ampliada entre as zonas. Não é necessário ter redes virtuais separadas para cada zona.
- Em todas as máquinas virtuais implantadas, você precisa usar [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados em implantações de zona.
- O Armazenamento Premium do Azure e o [Armazenamento SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a qualquer tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que replique esses discos compartilhados ou compartilhamentos entre as zonas. Há suporte para as seguintes tecnologias:
    - No Windows, uma solução de cluster que usa o SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  No momento, não há suporte nas zonas para a solução que usa o Servidor de Arquivos de Escalabilidade Horizontal da Microsoft, conforme documentado no artigo [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share).
- A terceira zona é usada para hospedar o dispositivo SBD, caso você crie um [cluster do SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais.
- Você deve implantar VMs inativas na zona passiva (de um ponto de vista do DBMS) para poder iniciar os recursos do aplicativo em caso de falha de zona.
    - O [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) atualmente não consegue replicar VMs ativas para VMs inativas entre as zonas. 
- Você deve investir na automação que permite, em caso de falha de zona, iniciar automaticamente a camada de aplicativo SAP na segunda zona.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configuração combinada de alta disponibilidade e recuperação de desastre
A Microsoft não compartilha qualquer informação sobre a distância geográfica entre as instalações que hospedam diferentes Zonas de Disponibilidade do Azure na região do Azure. Ainda assim, alguns clientes estão utilizando as zonas para realizar uma configuração de alta disponibilidade (HA) e de recuperação de desastre combinada que promete um objetivo de ponto de recuperação (RPO) zero. O que significa que você não deve perder nenhuma transação de banco de dados confirmada, mesmo no caso de recuperação de desastre. 

> [!NOTE]
> Recomendamos que você use uma configuração como essa apenas em determinadas circunstâncias. Por exemplo, você pode usá-la quando os dados não podem deixar a região do Azure por motivos de segurança ou conformidade. 

Consulte um exemplo de como essa configuração pode ser:

![Combinação de recuperação de desastres e alta disponibilidade em zonas](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

As seguintes considerações se aplicam a essa configuração:

- Você está assumindo que há uma distância significativa entre as instalações que hospedam uma zona de disponibilidade ou é forçado a permanecer em uma determinada região do Azure. Não é possível implantar conjuntos de disponibilidade em Zonas de Disponibilidade do Azure. Para compensar isso, você pode usar grupos de posicionamento de proximidade do Azure, conforme documentado no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Ao usar essa arquitetura, monitore o status de perto e tente manter as instâncias de DBMS e SAP Central Services ativas na mesma zona da sua camada de aplicativo implantada. Caso ocorra um failover do SAP Central Service ou da instância do DBMS, certifique-se de que você possa realizar o failover manual de volta para a zona com a camada de aplicativo SAP implantada o mais rápido possível.
- Você deve ter instâncias do aplicativo de produção pré-instaladas nas VMs que executam as instâncias do aplicativo QA ativas.
- No caso de uma falha de zona, encerre as instâncias do aplicativo QA e inicie as instâncias de produção. Observe que você precisa usar nomes virtuais para as instâncias do aplicativo para fazer isso funcionar.
- Para os balanceadores de carga dos clusters de failover do SAP Central Services e da camada do DBMS, você precisa usar a [SKU padrão do Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O Load Balancer básico não funciona entre as zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP com suas sub-redes é ampliada entre as zonas. Não é necessário ter redes virtuais separadas para cada zona.
- Em todas as máquinas virtuais implantadas, você precisa usar [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados em implantações de zona.
- O Armazenamento Premium do Azure e o [Armazenamento SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a qualquer tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que replique esses discos compartilhados ou compartilhamentos entre as zonas. Há suporte para as seguintes tecnologias:
    - No Windows, uma solução de cluster que usa o SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  No momento, não há suporte nas zonas para a solução que usa o Servidor de Arquivos de Escalabilidade Horizontal da Microsoft, conforme documentado no artigo [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share).
- A terceira zona é usada para hospedar o dispositivo SBD, caso você crie um [cluster do SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais.





## <a name="next-steps"></a>Próximas etapas
Apresentamos abaixo algumas próximas etapas para implantar em Zonas de Disponibilidade do Azure:

- [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






