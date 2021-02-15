---
title: Configurações de carga de trabalho do SAP com Zonas de Disponibilidade do Azure | Microsoft Docs
description: Arquitetura e cenários de alta disponibilidade para SAP NetWeaver usando Zonas de Disponibilidade do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e098256a43add6df026ab136bcd6a6b549c147e7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871308"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurações de carga de trabalho do SAP com Zonas de Disponibilidade do Azure
Além da implantação das diferentes camadas de arquitetura do SAP nos conjuntos de disponibilidade do Azure, a [zonas de disponibilidade do Azure](../../../availability-zones/az-overview.md) introduzida mais recentemente também pode ser usada para implantações de carga de trabalho do SAP. Uma zona de disponibilidade do Azure é definida como: "locais físicos exclusivos em uma região. Cada zona é composta por um ou mais data centers equipados com energia, resfriamento e rede independentes. Zonas de Disponibilidade do Azure não estão disponíveis em todas as regiões. Para regiões do Azure que fornecem Zonas de Disponibilidade, verifique o [mapa de regiões do Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Esse mapa vai mostrar quais regiões fornecem ou são anunciadas para fornecer Zonas de Disponibilidade. 

A partir da arquitetura típica do SAP NetWeaver ou do S/4HANA, você precisa proteger três camadas diferentes:

- Camada de aplicativo SAP, que pode ser de uma a algumas dúzias de VMs. Você deseja minimizar a chance de as VMs serem implantadas no mesmo servidor host. Você também quer que essas VMs em uma proximidade aceitável para a camada de DBMS mantenham a latência de rede em uma janela aceitável
- Camada SAP ASCS/SCS que representa um ponto único de falha na arquitetura do SAP NetWeaver e do S/4HANA. Normalmente, você examina duas VMs que deseja abranger com uma estrutura de failover. Portanto, essas VMs devem ser alocadas em diferentes domínios de falha de infraestrutura e de atualização
- Camada do DBMS do SAP, que representa um ponto único de falha também. Nos casos usuais, ele consiste em duas VMs cobertas por uma estrutura de failover. Portanto, essas VMs devem ser alocadas em diferentes domínios de falha de infraestrutura e de atualização. As exceções são SAP HANA implantações em expansão em que mais de duas VMs podem ser usadas

As principais diferenças entre implantar suas VMs críticas por meio de conjuntos de disponibilidade ou Zonas de Disponibilidade são:

- A implantação com um conjunto de disponibilidade está alinhando as VMs no conjunto em uma única zona ou Datacenter (o que for aplicável para a região específica). Como resultado, a implantação por meio do conjunto de disponibilidade não é protegida por problemas de energia, resfriamento ou rede que afetam as dataceter da zona como um todo. No lado do mais, as VMs são alinhadas com os domínios de atualização e de falha dentro dessa zona ou Datacenter. Especificamente para a camada de ASCS ou de DBMS do SAP onde protegemos duas VMs por conjunto de disponibilidade, o alinhamento com domínios de falha e de atualização impede que ambas as VMs estejam terminando no mesmo hardware de host 
- A implantação de VMs por meio de um Zonas de Disponibilidade do Azure e a escolha de zonas diferentes (o máximo de três possíveis até agora), vai implantar as VMs em diferentes locais físicos e com isso adicionar a proteção adicional contra problemas de energia, resfriamento ou rede que afetam as dataceter da zona como um todo. No entanto, à medida que você implanta mais de uma VM da mesma família de VMs na mesma zona de disponibilidade, não há nenhuma proteção dessas VMs terminando no mesmo host. Como resultado, a implantação por meio de Zonas de Disponibilidade é ideal para a camada de ASCS e de DBMS do SAP, onde normalmente examinamos duas VMs cada uma. Para a camada de aplicativo SAP, que pode ser drasticamente mais do que duas VMs, talvez seja necessário retornar a um modelo de implantação diferente (consulte mais tarde)

Sua motivação para uma implantação em Zonas de Disponibilidade do Azure deve ser que você, além de abranger a falha de uma única VM crítica ou capacidade de reduzir o tempo de inatividade para a aplicação de patches de software em um crítico, queira proteger-se de problemas de infraestrutura maiores que podem afetar a disponibilidade de um ou vários data centers do Azure. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerações sobre a implantação em Zonas de Disponibilidade


Ao usar as Zonas de Disponibilidade, considere o seguinte:

- Não há qualquer garantia em relação às distâncias entre várias Zonas de Disponibilidade dentro de uma região do Azure.
- As Zonas de Disponibilidade não são uma solução ideal de recuperação de desastres (DR). Desastres naturais podem causar danos extensos em algumas regiões do mundo, incluindo danos graves a infraestruturas de energia. As distâncias entre várias zonas podem não ser suficientemente grandes para constituir uma solução de DR apropriada.
- A latência de rede entre Zonas de Disponibilidade não é a mesma em todas as regiões do Azure. Em alguns casos, você pode implantar e executar a camada de aplicativo do SAP em diferentes zonas, pois a latência da rede de uma zona para a VM do DBMS ativo é aceitável. Porém, em algumas regiões do Azure, a latência entre a VM do DBMS ativo e uma instância do aplicativo SAP, quando implantada em diferentes zonas, pode não ser aceitável para os processos empresariais do SAP. Nesses casos, a arquitetura de implantação precisa ser diferente, com uma arquitetura ativa/ativa para o aplicativo ou uma arquitetura ativa/passiva em que a latência de rede entre zonas é muito alta.
- Ao decidir onde usar as Zonas de Disponibilidade, baseie sua decisão na latência de rede entre as zonas. A latência da rede desempenha um papel importante em duas áreas:
    - A latência entre as duas instâncias de DBMS que precisam ter replicação síncrona. Quanto maior a latência de rede, maior a probabilidade de afetar a escalabilidade da sua carga de trabalho.
    - A diferença na latência de rede entre uma VM que executa uma instância de diálogo do SAP na zona com a instância do DBMS ativo e uma VM semelhante em outra zona. Conforme essa diferença aumenta, a influência sobre o tempo de execução de processos de negócios e de tarefas em lote também aumenta, dependendo de eles serem executados na zona com o DBMS ou em uma zona diferente.

Ao implantar VMs do Azure em Zonas de Disponibilidade e estabelecer soluções de failover na mesma região do Azure, há algumas restrições:

- Use o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) ao implantar em Zonas de Disponibilidade do Azure. 
- O mapeamento de enumerações de zona para as zonas físicas é fixado por assinatura do Azure. Se você estiver usando assinaturas diferentes para implantar seus sistemas de SAP, precisará definir as zonas ideais para cada assinatura.
- Você não pode implantar conjuntos de disponibilidade do Azure em uma zona de disponibilidade do Azure, a menos que use o [grupo de posicionamento de proximidade do Azure](../../co-location.md). A maneira como você pode implantar a camada do DBMS do SAP e os serviços centrais entre as zonas e, ao mesmo tempo, implantar a camada de aplicativo SAP usando conjuntos de disponibilidade e ainda atingir a proximidade das VMs está documentada no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md). Se não estiver usando grupos de posicionamento de proximidade do Azure, você precisará escolher um ou outro como uma estrutura de implantação para máquinas virtuais.
- Não é possível usar um [Azure Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) para criar soluções de cluster de failover baseadas no Cluster de Failover do Windows Server ou no Linux Pacemaker. Em vez disso, você precisa usar o [SKU Standard Load Balancer do Azure](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>Combinação ideal para as Zonas de Disponibilidade
Se você quiser implantar um sistema SAP NetWeaver ou S/4HANA entre zonas, há duas arquiteturas principais que você pode implantar:

- Ativo/ativo: o par de VMs que executam o ASCS/SCS e o par de VMS que executam a camada DBMS são distribuídos entre duas zonas. O número de VMs que executam a camada de aplicativo SAP é implantado em números pares nas mesmas duas zonas. Se uma VM DBMS ou ASCS/SCS estiver fazendo failover, algumas das transações abertas e ativas poderão ser revertidas. Mas os usuários estão conectados. Na verdade, não importa em qual das zonas a VM do DBMS ativa e as instâncias do aplicativo são executadas. Essa arquitetura é a arquitetura preferida para implantar entre zonas.
- Ativo/passivo: o par de VMs que executam o ASCS/SCS e o par de VMS que executam a camada DBMS são distribuídos entre duas zonas. O número de VMs que executam a camada de aplicativo SAP é implantado em um dos Zonas de Disponibilidade. Você executa a camada de aplicativo na mesma zona que a instância ativa do ASCS/SCS e do DBMS. Você usará essa arquitetura de implantação se a latência de rede entre as diferentes zonas for muito alta para executar a camada de aplicativo distribuída entre as zonas. Em vez disso, a camada de aplicativo SAP precisa ser executada na mesma zona que a instância ativa do ASCS/SCS e/ou DBMS. Se uma VM do ASCS/SCS ou DBMS fizer failover para a zona secundária, você poderá encontrar uma latência de rede maior e com essa redução de taxa de transferência. E você precisa fazer failback da VM anteriormente com failover mais cedo possível para voltar aos níveis de taxa de transferência anteriores. Se ocorrer uma interrupção zonal, a camada de aplicativo precisará passar por failover para a zona secundária. Uma atividade que os usuários enfrentam como desligamento completo do sistema. Em algumas das regiões do Azure, essa arquitetura é a única arquitetura viável quando você deseja usar Zonas de Disponibilidade. Se você não puder aceitar o impacto potencial de um ASCS/SCS ou VMS do DBMS fazendo failover para a zona secundária, talvez seja melhor permanecer com implantações de conjunto de disponibilidade


Portanto, antes de decidir como usar Zonas de Disponibilidade, você precisa determinar:

- A latência de rede entre as três zonas de uma região do Azure. Saber a latência de rede entre as zonas de uma região irá permitir que você escolha as zonas com a menor latência de rede no tráfego de rede entre zonas.
- A diferença entre a latência entre VMs em uma das zonas, de sua escolha, e a latência de rede em duas zonas de sua escolha.
- Uma determinação indicando se os tipos de VM que você precisa implantar estão disponíveis nas duas zonas selecionadas. Com algumas VMs, especialmente VMs da série M, você pode encontrar situações em que algumas SKUs estão disponíveis em apenas duas das três zonas.

## <a name="network-latency-between-and-within-zones"></a>Latência de rede entre zonas e dentro delas
Para determinar a latência entre as diferentes zonas, é preciso:

- Implantar a SKU da VM que você deseja usar para sua instância do DBMS nas três zonas. Certifique-se de que a [Rede Acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) esteja habilitada ao executar essa medição.
- Ao encontrar as duas zonas com menos latência de rede, implante outras três VMs da SKU da VM que você deseja usar como VM da camada de aplicativo nas três Zonas de Disponibilidade. Meça a latência de rede em relação às duas VMs do DBMS nas duas zonas do DBMS que você selecionou. 
- Use **`niping`** como uma ferramenta de medição. Essa ferramenta de SAP é descrita nas notas de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentre-se nos comandos documentados para medições de latência. Como o **ping** não funciona nos caminhos de código da Rede Acelerada do Azure, não é recomendável usá-lo.

Você não precisa executar esses testes manualmente. Você pode encontrar um teste de [latência de zona de disponibilidade](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) de procedimento do PowerShell que automatiza os testes de latência descritos. 

Com base nas medições e na disponibilidade de suas SKUs de VM em diferentes Zonas de Disponibilidade, você precisa tomar as algumas decisões:

- Definir as zonas ideais para a camada do DBMS.
- Determinar se você deseja distribuir sua camada ativa de aplicativo do SAP em uma, duas ou em todas as três zonas, com base nas diferenças de latência de rede na zona versus entre as zonas.
- Determinar se você deseja implantar uma configuração ativa/passiva ou uma configuração ativa/ativa do ponto de vista do aplicativo. (Essas configurações são explicadas mais adiante neste artigo.)

Ao tomar essas decisões, considere também as recomendações de latência de rede do SAP, conforme documentado na nota do SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> As medições e decisões que você toma são válidas para a assinatura do Azure usada ao fazer as medições. Se você usar outra assinatura do Azure, repita as medições. O mapeamento de zonas enumeradas pode ser diferente para outra assinatura do Azure.


> [!IMPORTANT]
> Espera-se que as medições descritas anteriormente forneçam resultados diferentes em todas as regiões do Azure que dão suporte a [Zonas de Disponibilidade](https://azure.microsoft.com/global-infrastructure/geographies/). Mesmo quando seus requisitos de latência de rede forem os mesmos, talvez seja necessário adotar estratégias de implantação distintas em diferentes regiões do Azure, já que a latência de rede entre as zonas pode diferir. Em algumas regiões do Azure, a latência de rede entre as três zonas diferentes pode ser muito diferente. Em outras regiões, a latência de rede entre as três zonas diferentes pode ser mais uniforme. A declaração de que há sempre uma latência de rede entre 1 e 2 milissegundos não está correta. A latência de rede entre Zonas de Disponibilidade nas regiões do Azure não pode ser generalizada.

## <a name="activeactive-deployment"></a>Implantação ativa/ativa
Essa arquitetura de implantação é chamada ativa/ativa porque você implanta seus servidores de aplicativos SAP ativos em duas ou três zonas. A instância do SAP Central Services que usa a replicação de enfileiramento é implantada entre duas zonas. O mesmo vale para a camada de DBMS, que é implantada nas mesmas zonas que o SAP Central Service. Ao considerar essa configuração, você precisa encontrar as duas Zonas de Disponibilidade em sua região que oferecem uma latência de rede entre zonas aceitável para sua carga de trabalho e para sua replicação síncrona de DBMS. Também é preciso ter certeza de que o delta entre a latência de rede nas zonas selecionadas e a latência de rede entre zonas não é muito grande. 

A natureza da arquitetura SAP é que, a menos que você a configure de forma diferente, os usuários e trabalhos em lotes podem ser executados em diferentes instâncias do aplicativo. O efeito colateral desse fato com a implantação ativa/ativa é que os trabalhos em lotes podem ser executados por instâncias do aplicativo SAP independentemente de serem executados na mesma zona com o DBMS ativo ou não. Se a diferença na latência de rede entre as zonas de diferença for pequena em comparação com a latência de rede em uma zona, a diferença em tempos de execução de trabalhos em lotes pode não ser significativa. No entanto, quanto maior a diferença de latência de rede em uma zona em comparação com o tráfego de rede de zona, o tempo de execução dos trabalhos em lotes poderá ser afetado mais se o trabalho tiver sido executado em uma zona em que a instância do DBMS não estiver ativa. Ele está em você como um cliente para decidir quais diferenças aceitáveis no tempo de execução são. E com isso, que é a latência de rede tolerável para o tráfego entre zonas.

As regiões do Azure em que essa implantação ativa/ativa deve ser possível sem grandes diferenças no tempo de execução e na taxa de transferência na camada de aplicativo implantada em diferentes Zonas de Disponibilidade, lista como:

- DOS EUA 2 ocidental (todas as três zonas)
- Leste dos EUA 2 (todas as três zonas)
- EUA Central (todas as três zonas)
- Europa Setentrional (todas as três zonas)
- Europa Ocidental (duas das três zonas)
- Leste dos EUA (duas das três zonas)
- EUA Central do Sul (duas das três zonas)
- Sul do Reino Unido (duas das três zonas)

As regiões do Azure em que essa arquitetura de implantação SAP entre zonas não é recomendada são:

- França Central 
- Norte da África do Sul
- Canadá Central
- Japan East

Dependendo do que você está disposto a tolerar nas diferenças de tempo de execução, outras regiões não listadas também podem ser qualificadas.


Um esquema simplificado de implantação ativa/ativa entre duas zonas pode ser semelhante a:

![Implantação de zona ativa/ativa](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

As seguintes considerações se aplicam a essa configuração:

- Não usando o [grupo de posicionamento de proximidade do Azure](../../co-location.md), você trata o zonas de disponibilidade do Azure como domínios de falha e atualização para todas as VMs porque os conjuntos de disponibilidade não podem ser implantados no zonas de disponibilidade do Azure.
- Se você quiser combinar implantações zonais para a camada DBMS e os serviços centrais, mas quiser usar os conjuntos de disponibilidade do Azure para a camada de aplicativo, você precisará usar os grupos de proximidade do Azure, conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Para os balanceadores de carga dos clusters de failover do SAP Central Services e da camada do DBMS, você precisa usar a [SKU padrão do Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). O Load Balancer básico não funciona entre as zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP com suas sub-redes é ampliada entre as zonas. Não é necessário ter redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais implantadas, você precisa usar o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados em implantações de zona.
- O armazenamento Premium do Azure, o [armazenamento SSD ultra](../../disks-types.md#ultra-disk)ou o seja não dão suporte a nenhum tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que replique esses discos compartilhados ou compartilhamentos entre as zonas. Há suporte para as seguintes tecnologias:
  - No Windows, uma solução de cluster que usa o SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    No momento, não há suporte nas zonas para a solução que usa o Servidor de Arquivos de Escalabilidade Horizontal da Microsoft, conforme documentado no artigo [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](./sap-high-availability-infrastructure-wsfc-file-share.md).
- A terceira zona é usada para hospedar o dispositivo SBD se você criar um [cluster SuSE Linux pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e usar dispositivos SBD em vez do agente de isolamento do Azure. Ou para instâncias de aplicativo adicionais.
- Para obter a consistência do tempo de execução para processos comerciais críticos, você pode tentar direcionar determinados trabalhos e usuários do lote para instâncias de aplicativo que estão em zona com a instância do DBMS ativa usando grupos de servidores do lote SAP, grupos de logon SAP ou grupos de RFC. No entanto, no caso de um failover da zona, seria preciso mover manualmente esses grupos para as instâncias em execução nas VMs da zona com a VM do DB ativo.  
- Talvez você queira implantar instâncias de caixa de diálogo inativas em cada uma das zonas. 

> [!IMPORTANT]
> Neste cenário ativo/ativo, encargos adicionais de largura de banda são anunciados pela Microsoft do 04/01/2020 em. Verifique os [detalhes de preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/)do documento. A transferência de dados entre a camada de aplicativo SAP e a camada de DBMS do SAP é bastante intensiva. Portanto, o cenário ativo/ativo pode contribuir para um pouco mais de custos. Continue verificando este artigo para obter os custos exatos 


## <a name="activepassive-deployment"></a>Implantação ativa/passiva
Caso não consiga encontrar um delta aceitável entre a latência de rede dentro de uma zona e a latência do tráfego de rede entre zonas, implante uma arquitetura que tenha um caráter ativo/passivo do ponto de vista da camada de aplicativo SAP. Defina uma zona *ativa*, que é a zona em que você implantará a camada de aplicativo completa e onde tentará executar o DBMS ativo e a instância do SAP Central Services. Com essa configuração, você garante que não haja variações extremas de tempo de execução, dependendo se uma tarefa é executada na zona com a instância do DBMS ativo ou não, em transações comerciais e tarefas em lote.

As regiões do Azure em que esse tipo de arquitetura de implantação em diferentes zonas podem ser preferíveis são:

- Sudeste Asiático
- Leste da Austrália
- Brazil South
- Centro-Oeste da Alemanha
- Norte da África do Sul
- França Central 
- Canadá Central
- Japan East


O layout básico da arquitetura é semelhante a este:

![Implantação de zona ativa/passiva](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

As seguintes considerações se aplicam a essa configuração:

- Não é possível implantar conjuntos de disponibilidade em Zonas de Disponibilidade do Azure. Para compensar isso, você pode usar grupos de posicionamento de proximidade do Azure, conforme documentado no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Ao usar essa arquitetura, monitore o status de perto e tente manter as instâncias de DBMS e SAP Central Services ativas na mesma zona da sua camada de aplicativo implantada. Caso ocorra um failover do SAP Central Service ou da instância do DBMS, certifique-se de que você possa realizar o failover manual de volta para a zona com a camada de aplicativo SAP implantada o mais rápido possível.
- Para os balanceadores de carga dos clusters de failover do SAP Central Services e da camada do DBMS, você precisa usar a [SKU padrão do Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). O Load Balancer básico não funciona entre as zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP com suas sub-redes é ampliada entre as zonas. Não é necessário ter redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais implantadas, você precisa usar o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados em implantações de zona.
- O armazenamento Premium do Azure, o [armazenamento SSD ultra](../../disks-types.md#ultra-disk)ou o seja não dão suporte a nenhum tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que replique esses discos compartilhados ou compartilhamentos entre as zonas. Há suporte para as seguintes tecnologias:
    - No Windows, uma solução de cluster que usa o SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  No momento, não há suporte nas zonas para a solução que usa o Servidor de Arquivos de Escalabilidade Horizontal da Microsoft, conforme documentado no artigo [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](./sap-high-availability-infrastructure-wsfc-file-share.md).
- A terceira zona é usada para hospedar o dispositivo SBD se você criar um [cluster SuSE Linux pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e usar dispositivos SBD em vez do agente de isolamento do Azure. Ou para instâncias de aplicativo adicionais.
- Você deve implantar VMs inativas na zona passiva (de um ponto de vista do DBMS) para poder iniciar recursos do aplicativo para o caso de uma falha de zona.
    - O [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) atualmente não consegue replicar VMs ativas para VMs inativas entre as zonas. 
- Você deve investir na automação que permite iniciar automaticamente a camada do aplicativo SAP na segunda zona se ocorrer uma interrupção zonal.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configuração combinada de alta disponibilidade e recuperação de desastre
A Microsoft não compartilha qualquer informação sobre a distância geográfica entre as instalações que hospedam diferentes Zonas de Disponibilidade do Azure na região do Azure. Ainda assim, alguns clientes estão utilizando as zonas para realizar uma configuração de alta disponibilidade (HA) e de recuperação de desastre combinada que promete um objetivo de ponto de recuperação (RPO) zero. Um RPO de zero significa que você não deve perder nenhuma transação de banco de dados confirmada mesmo no caso de recuperação de desastre. 

> [!NOTE]
> Recomendamos que você use uma configuração como essa apenas em determinadas circunstâncias. Por exemplo, você pode usá-la quando os dados não podem deixar a região do Azure por motivos de segurança ou conformidade. 

Consulte um exemplo de como essa configuração pode ser:

![Combinação de recuperação de desastres e alta disponibilidade em zonas](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

As seguintes considerações se aplicam a essa configuração:

- Você está pressupondo que há uma distância significativa entre as instalações que hospedam uma zona de disponibilidade ou é forçado a permanecer em uma determinada região do Azure. Não é possível implantar conjuntos de disponibilidade em Zonas de Disponibilidade do Azure. Para compensar isso, você pode usar grupos de posicionamento de proximidade do Azure, conforme documentado no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
- Ao usar essa arquitetura, monitore o status de perto e tente manter as instâncias de DBMS e SAP Central Services ativas na mesma zona da sua camada de aplicativo implantada. Caso ocorra um failover do SAP Central Service ou da instância do DBMS, certifique-se de que você possa realizar o failover manual de volta para a zona com a camada de aplicativo SAP implantada o mais rápido possível.
- Você deve ter instâncias do aplicativo de produção pré-instaladas nas VMs que executam as instâncias do aplicativo QA ativas.
- Em um caso de falha zonal, desligue as instâncias do aplicativo de QA e inicie as instâncias de produção em vez disso. Você precisa usar nomes virtuais para as instâncias do aplicativo para fazer esse trabalho.
- Para os balanceadores de carga dos clusters de failover do SAP Central Services e da camada do DBMS, você precisa usar a [SKU padrão do Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). O Load Balancer básico não funciona entre as zonas.
- A rede virtual do Azure que você implantou para hospedar o sistema SAP com suas sub-redes é ampliada entre as zonas. Não é necessário ter redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais implantadas, você precisa usar o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos não gerenciados em implantações de zona.
- O Armazenamento Premium do Azure e o [Armazenamento SSD Ultra](../../disks-types.md#ultra-disk) não dão suporte a qualquer tipo de replicação de armazenamento entre zonas. O aplicativo (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento CIFS (Windows) ou um compartilhamento NFS (Linux). Você precisa usar uma tecnologia que replique esses discos compartilhados ou compartilhamentos entre as zonas. Há suporte para as seguintes tecnologias:
    - No Windows, uma solução de cluster que usa o SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Para o SUSE Linux, um compartilhamento NFS criado conforme documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  No momento, não há suporte nas zonas para a solução que usa o Servidor de Arquivos de Escalabilidade Horizontal da Microsoft, conforme documentado no artigo [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](./sap-high-availability-infrastructure-wsfc-file-share.md).
- A terceira zona é usada para hospedar o dispositivo SBD se você criar um [cluster SuSE Linux pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e usar dispositivos SBD em vez do agente de isolamento do Azure. 





## <a name="next-steps"></a>Próximas etapas
Apresentamos abaixo algumas próximas etapas para implantar em Zonas de Disponibilidade do Azure:

- [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](./sap-high-availability-infrastructure-wsfc-file-share.md)