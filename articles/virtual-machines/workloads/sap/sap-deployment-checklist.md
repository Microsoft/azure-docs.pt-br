---
title: Lista de verificação de planejamento e implantação de carga de trabalho do SAP | Microsoft Docs
description: Lista de verificação para planejamento de implantações de carga de trabalho SAP no Azure e implantação das cargas de trabalho
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 944e687c27d46a9cf3250cb21024b4e5a52dc62c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871512"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Cargas de trabalho do SAP no Azure: lista de verificação de planejamento e implantação

Esta lista de verificação foi projetada para clientes que movem aplicativos SAP NetWeaver, S/4HANA e Hybris para a infraestrutura do Azure como um serviço. Durante toda a duração do projeto, um cliente e/ou parceiro SAP deve examinar a lista de verificação. É importante observar que muitas das verificações foram concluídas no início do projeto e durante a fase de planejamento. Depois que a implantação é feita, as alterações simples na infraestrutura implantada do Azure ou em versões de software SAP podem se tornar complexas.

Examine a lista de verificação em principais etapas durante o projeto. Fazer isso permitirá que você detecte pequenos problemas antes que eles se tornem grandes problemas. Você também terá tempo suficiente para refazer a engenharia e testar todas as alterações necessárias. Não considere esta lista de verificação concluída. Dependendo de sua situação, talvez seja necessário executar muitas verificações adicionais.

A lista de verificação não inclui tarefas que são independentes do Azure. Por exemplo, as interfaces de aplicativos SAP são alteradas durante uma mudança para a plataforma do Azure ou para um provedor de hospedagem.

Essa lista de verificação também pode ser usada para sistemas que já estão implantados. Novos recursos, como Acelerador de Gravação e Zonas de Disponibilidade, e novos tipos de VM podem ter sido adicionados desde que você implantou. Portanto, é útil revisar a lista de verificação periodicamente para garantir que você esteja ciente dos novos recursos na plataforma Azure.

## <a name="project-preparation-and-planning-phase"></a>Fase de planejamento e preparação do projeto
Durante essa fase, você planeja a migração da carga de trabalho do SAP para a plataforma Azure. No mínimo, durante essa fase, você precisa criar os seguintes documentos e definir e discutir os seguintes elementos da migração:

1. Documento de design de alto nível. Este documento deve conter:
    - O inventário atual de componentes e aplicativos SAP e um inventário de aplicativos de destino para o Azure.
    - Uma RACI (matriz de atribuição de responsabilidade) que define as responsabilidades e atribuições das partes envolvidas. Inicie em um alto nível e trabalhe em níveis mais granulares durante o planejamento e as primeiras implantações.
    - Uma arquitetura de solução de alto nível.
    - Uma decisão sobre em quais regiões do Azure implantar. Consulte a [lista de regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Para saber quais serviços estão disponíveis em cada região, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).
    - Uma arquitetura de rede para se conectar do local para o Azure. Comece a se familiarizar com a [estrutura de datacenter virtual para o Azure](/azure/architecture/vdc/).
    - Princípios de segurança para executar dados de negócios de alto impacto no Azure. Para saber mais sobre a segurança de dados, comece com a [documentação de segurança do Azure](../../../security/index.yml).
2.  Documento de design técnico. Este documento deve conter:
    - Um diagrama de bloco para a solução.
    - O dimensionamento dos componentes de computação, armazenamento e rede no Azure. Para o dimensionamento do SAP de VMs do Azure, consulte [SAP 
    -  Observação #1928533] ( https://launchpad.support.sap.com/#/notes/1928533) .
    - Continuidade dos negócios e arquitetura de recuperação de desastres.
    - Informações detalhadas sobre as versões do sistema operacional, DB, kernel e do pacote de suporte do SAP. Não é necessariamente verdade que todas as versões do sistema operacional com suporte do SAP NetWeaver ou S/4HANA têm suporte em VMs do Azure. O mesmo vale para as versões do DBMS. Verifique as seguintes fontes para alinhar e, se necessário, atualizar as versões do SAP, as versões do DBMS e as versões do sistema operacional para garantir o suporte do SAP e do Azure. Você precisa ter combinações de versão com suporte do SAP e do Azure para obter suporte completo da SAP e da Microsoft. Se necessário, você precisa planejar a atualização de alguns componentes de software. Mais detalhes sobre o software SAP, so e DBMS com suporte estão documentados aqui:
        - [#1928533 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1928533). Esta observação define as versões mínimas do sistema operacional com suporte em VMs do Azure. Ele também define as versões mínimas do banco de dados necessárias para a maioria dos bancos de dados não HANA. Por fim, ele fornece o dimensionamento SAP para tipos de VM do Azure com suporte SAP.
        - [#2015553 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2015553). Esta observação define as políticas de suporte ao armazenamento do Azure e à relação de suporte necessárias com a Microsoft.
        - [#2039619 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2039619). Esta observação define a matriz de suporte Oracle para o Azure. O Oracle dá suporte apenas ao Windows e Oracle Linux como sistemas operacionais convidados no Azure para cargas de trabalho do SAP. Essa instrução de suporte também se aplica à camada de aplicativo SAP que executa instâncias SAP. No entanto, a Oracle não dá suporte à alta disponibilidade para os serviços centrais do SAP no Oracle Linux por meio do pacemaker. Se precisar de alta disponibilidade para ASCS em Oracle Linux, você precisará usar o SIOS Protection Suite para Linux. Para obter dados de certificação SAP detalhados, consulte Nota [de suporte da sap #1662610-detalhes de suporte para o sios Protection Suite para Linux](https://launchpad.support.sap.com/#/notes/1662610). Para o Windows, a solução de clustering de failover do Windows Server com suporte SAP para serviços centrais do SAP tem suporte em conjunto com a Oracle como a camada do DBMS.
        - [#2235581 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2235581). Esta observação fornece a matriz de suporte para SAP HANA em diferentes versões do sistema operacional.
        - As VMs do Azure com suporte SAP HANA e as [instâncias grandes do Hana](./hana-overview-architecture.md) são listadas no [site do SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [Matriz de disponibilidade de produto SAP](https://support.sap.com/en/).
        - [Nota de suporte da SAP #2555629-SAP HANA 2,0 camadas dinâmicas – suporte a hipervisor e nuvem](https://launchpad.support.sap.com/#/notes/2555629)
        - [Nota de suporte da SAP #1662610-detalhes de suporte para o SIOS Protection Suite para Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - Notas SAP para outros produtos específicos do SAP.     
    - O uso de configurações de cluster de vários SID para serviços centrais do SAP tem suporte em sistemas operacionais convidados Windows, SLES e RHEL no Azure. Tenha em mente que o RADIUS pode aumentar o mais ASCS/SCS que você coloca em um cluster de vários SID. Você pode encontrar a documentação para o respectivo cenário de sistema operacional convidado nestes artigos:
        - [Alta disponibilidade de vários SIDs da instância do SAP ASCS/SCS com clustering de failover do Windows Server e disco compartilhado no Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [Alta disponibilidade de vários SIDs da instância do SAP ASCS com clustering de failover do Windows Server e compartilhamento de arquivos no Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Alta disponibilidade para SAP NetWeaver em VMs do Azure em SUSE Linux Enterprise Server para aplicativos SAP guia de vários SIDs](./high-availability-guide-suse-multi-sid.md)
        - [Alta disponibilidade para SAP NetWeaver em VMs do Azure em Red Hat Enterprise Linux para aplicativos SAP guia de vários SIDs](./high-availability-guide-rhel-multi-sid.md)
    - Arquitetura de alta disponibilidade e recuperação de desastres.
        - Com base no RTO e no RPO, defina o que a arquitetura de alta disponibilidade e recuperação de desastre precisará ser parecida.
        - Para alta disponibilidade em uma zona, verifique o que o DBMS desejado tem a oferecer no Azure. A maioria dos pacotes DBMS oferece métodos síncronos de uma espera ativa síncrona, que é recomendável para sistemas de produção. Verifique também a documentação relacionada ao SAP para bancos de dados diferentes, começando com [considerações para implantação de DBMS de máquinas virtuais do Azure para cargas de trabalho do SAP](./dbms_guide_general.md) e documentos relacionados.
           O uso do clustering de failover do Windows Server com uma configuração de disco compartilhado para a camada DBMS como, por exemplo, [descrito para SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server), não tem suporte. Em vez disso, use soluções como:
           - [AlwaysOn do SQL Server](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
           - [Replicação de sistema do HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Para a recuperação de desastres em regiões do Azure, examine as soluções oferecidas por diferentes fornecedores de DBMS. A maioria deles oferece suporte à replicação assíncrona ou ao envio de logs.
        - Para a camada de aplicativo SAP, determine se você executará seus sistemas de teste de regressão de negócios, que, idealmente, são réplicas de suas implantações de produção, na mesma região do Azure ou em sua região de DR. No segundo caso, você pode direcionar esse sistema de regressão de negócios como o destino de DR para suas implantações de produção.
        - Se você decidir não posicionar os sistemas de não produção no site de recuperação de desastres, procure Azure Site Recovery como um método para replicar a camada do aplicativo SAP na região de recuperação de desastres do Azure. Para obter mais informações, consulte uma [configuração de recuperação de desastre para uma implantação de aplicativo SAP NetWeaver de várias camadas](../../../site-recovery/site-recovery-sap.md).
        - Se você decidir usar uma configuração de HADR combinada usando [zonas de disponibilidade do Azure](../../../availability-zones/az-overview.md), familiarize-se com as regiões do Azure onde zonas de disponibilidade estão disponíveis. Além disso, leve em conta as restrições que podem ser introduzidas por maiores latências de rede entre duas Zonas de Disponibilidade.  
3.  Um inventário de todas as interfaces SAP (SAP e não SAP).
4.  Design de serviços de base. Esse design deve incluir os seguintes itens:
    - Design de Active Directory e DNS.
    - Topologia de rede no Azure e atribuição de diferentes sistemas SAP.
    - Estrutura do [Azure RBAC (controle de acesso baseado em função)](../../../role-based-access-control/overview.md) para equipes que gerenciam aplicativos SAP e infraestrutura no Azure.
    - Topologia do grupo de recursos.
    - [Estratégia de marcação](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - Convenções de nomenclatura para VMs e outros componentes de infraestrutura e/ou nomes lógicos.
5.  Contrato profissional ou Suporte Premier da Microsoft. Identifique seu TAM (gerente técnico de conta) da Microsoft se você tiver um contrato de suporte Premier com a Microsoft. Para obter os requisitos de suporte do SAP, consulte [#2015553 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2015553).
6.  O número de assinaturas do Azure e a cota de núcleo para as assinaturas. [Abra solicitações de suporte para aumentar as cotas de assinaturas do Azure](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) , conforme necessário.
7.  Data de redução de dados e plano de migração de dados para migrar dados SAP para o Azure. Para sistemas SAP NetWeaver, a SAP tem diretrizes sobre como limitar o volume de grandes quantidades de dados. Consulte [este guia SAP sobre o](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) gerenciamento de dados em sistemas SAP ERP. Parte do conteúdo também se aplica aos sistemas NetWeaver e S/4HANA em geral.
8.  Uma abordagem de implantação automatizada. O objetivo da automação de implantações de infraestrutura no Azure é implantar de forma determinística e obter resultados determinísticos. Muitos clientes usam scripts baseados no PowerShell ou na CLI. Mas há várias tecnologias de código-fonte aberto que você pode usar para implantar a infraestrutura do Azure para SAP e até mesmo instalar o software SAP. Você pode encontrar exemplos no GitHub:
    - [Implantações automatizadas do SAP na nuvem do Azure](https://github.com/Azure/sap-hana)
    - [Instalação do SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Defina uma cadência regular de design e de análise de implantação entre você como cliente, integrador de sistemas, Microsoft e outras partes envolvidas.


## <a name="pilot-phase-strongly-recommended"></a>Fase piloto (altamente recomendável)
 
Você pode executar um piloto antes ou durante o planejamento e a preparação do projeto. Você também pode usar a fase piloto para testar abordagens e designs feitos durante a fase de planejamento e preparação. E você pode expandir a fase piloto para torná-la uma prova de conceito real.

Recomendamos que você configure e valide uma solução HADR completa e um design de segurança durante uma implantação piloto. Alguns clientes executam testes de escalabilidade durante essa fase. Outros clientes usam implantações de sistemas de área restrita do SAP como uma fase piloto. Supomos que você já identificou um sistema que deseja migrar para o Azure para o piloto.

1. Otimize a transferência de dados para o Azure. A opção ideal é altamente dependente do cenário específico. A transferência do local por meio [do Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) é mais rápida se o circuito do ExpressRoute tiver largura de banda suficiente. Em outros cenários, a transferência pela Internet é mais rápida.
2. Para uma migração de plataforma SAP heterogênea que envolve uma exportação e importação de dados, teste e otimize as fases de exportação e importação. Para grandes migrações nas quais SQL Server é a plataforma de destino, você pode encontrar [recomendações](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Você pode usar o Migration monitor/SWPM se não precisar de uma atualização de versão combinada. Você pode usar o processo do [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) ao combinar a migração com uma atualização de versão do SAP. Para fazer isso, você precisa atender a determinados requisitos para a combinação de plataforma DBMS de origem e de destino. Esse processo está documentado na [opção de migração de banco de dados (DMO) de SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exportar para origem, exportar upload de arquivo para o Azure e importar o desempenho. Maximize a sobreposição entre exportação e importação.
   2.  Avalie o volume do banco de dados nas plataformas de destino e destino para fins de dimensionamento de infraestrutura.
   3.  Validar e otimizar o tempo.
1. Validação técnica.
   1. Tipos de VM.
        - Examine os recursos nas notas de suporte do SAP, no diretório SAP HANA hardware e no PAM do SAP novamente. Certifique-se de que não haja nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte para esses tipos de VM e as versões de SAP e DBMS com suporte.
        - Valide novamente o dimensionamento do seu aplicativo e a infraestrutura que você implanta no Azure. Se você estiver movendo aplicativos existentes, muitas vezes poderá derivar os SAPS necessários da infraestrutura que você usa e a [página da Web de benchmark do SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e compará-los com os números de SAPs listados na nota de suporte do [SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Além disso, mantenha [Este artigo sobre as classificações de SAPs](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) em mente.
        - Avalie e teste o dimensionamento de suas VMs do Azure em relação à taxa de transferência máxima de armazenamento e à taxa de transferência de rede dos tipos de VM que você escolheu durante a fase de planejamento. Você pode encontrar os dados aqui:
           -  [Tamanhos das máquinas virtuais do Windows no Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar a taxa de *transferência máxima do disco não armazenado em cache* para o dimensionamento.
           -  [Tamanhos das máquinas virtuais do Linux no Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar a taxa de *transferência máxima do disco não armazenado em cache* para o dimensionamento.
   2. Armazenamento.
        - Verificar o documento [tipos de armazenamento do Azure para carga de trabalho SAP](./planning-guide-storage.md)
        - No mínimo, use o [armazenamento de SSD standard do Azure](../../disks-types.md#standard-ssd) para VMs que representam as camadas do aplicativo SAP e para a implantação de DBMSs que não são sensíveis ao desempenho.
        - Em geral, não recomendamos o uso de [discos HDD standard do Azure](../../disks-types.md#standard-hdd).
        - Use o [armazenamento Premium do Azure](../../disks-types.md#premium-ssd) para qualquer VM DBMS que seja sensível ao desempenho remotamente.
        - Use o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).
        - Use o Acelerador de Gravação do Azure para unidades de log do DBMS com a série M. Lembre-se de Acelerador de Gravação limites e uso, conforme documentado em [acelerador de gravação](../../how-to-enable-write-accelerator.md).
        - Para os diferentes tipos de DBMS, verifique a [documentação genérica do DBMS relacionada ao SAP](./dbms_guide_general.md) e a documentação específica do DBMS para a qual o documento genérico aponta.
        - Para obter mais informações sobre SAP HANA, consulte [SAP Hana configurações de infraestrutura e operações no Azure](./hana-vm-operations.md).
        - Nunca monte discos de dados do Azure em uma VM Linux do Azure usando a ID do dispositivo. Em vez disso, use o identificador universal exclusivo (UUID). Tenha cuidado ao usar, por exemplo, ferramentas gráficas para montar discos de dados do Azure. Verifique as entradas em/etc/fstab para certificar-se de que o UUID é usado para montar os discos. Você pode encontrar mais detalhes neste [artigo](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Rede.
        - Teste e avalie sua infraestrutura de rede virtual e a distribuição de seus aplicativos SAP entre ou dentro das diferentes redes virtuais do Azure.
        -  Avalie a abordagem da arquitetura de rede virtual Hub e spoke ou a abordagem de microsegmentação em uma única rede virtual do Azure. Base desta avaliação em:
               1. Custos de troca de dados entre [redes virtuais emparelhadas do Azure](../../../virtual-network/virtual-network-peering-overview.md). Para obter informações sobre os custos, consulte [preços de rede virtual](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Vantagens de uma rápida desconexão do emparelhamento entre redes virtuais do Azure em vez de alterar o grupo de segurança de rede para isolar uma sub-rede em uma rede virtual. Essa avaliação é para casos em que os aplicativos ou as VMs hospedadas em uma sub-rede da rede virtual se tornaram um risco de segurança.
                3. Registro em log central e auditoria de tráfego de rede entre o local, o mundo exterior e o datacenter virtual criado no Azure.
        - Avalie e teste o caminho de dados entre a camada de aplicativo SAP e a camada do DBMS SAP.
            -  O posicionamento de [dispositivos virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de sistemas SAP baseados em SAP NetWeaver, Hybris ou S/4HANA não tem suporte.
            -  Não há suporte para o posicionamento da camada de aplicativo SAP e do DBMS do SAP em diferentes redes virtuais do Azure que não são emparelhadas.
            -  Você pode usar [as regras grupo de segurança de aplicativos e grupo de segurança de rede](../../../virtual-network/network-security-groups-overview.md) para definir rotas entre a camada de aplicativo SAP e a camada do DBMS SAP.
        - Verifique se a [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitada nas VMs usadas na camada de aplicativo SAP e na camada do DBMS SAP. Lembre-se de que diferentes níveis de sistema operacional são necessários para dar suporte à Rede Acelerada no Azure:
            - Windows Server 2012 R2 ou posterior.
            - SUSE Linux 12 SP3 ou posterior.
            - RHEL 7,4 ou posterior.
            - Oracle Linux 7.5. Se você estiver usando o kernel RHCKL, a versão 3.10.0-862.13.1. EL7 será necessária. Se você estiver usando o kernel do Oracle UEK, a versão 5 será necessária.
        - Teste e avalie a latência de rede entre as VMs da camada de aplicativo SAP e as VMs do DBMS de acordo com as notas de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados em relação às diretrizes de latência de rede no [#1100926 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado ou bom. As exceções se aplicam ao tráfego entre as VMs e as unidades de instância grande do HANA, conforme documentado neste [artigo](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
        - Verifique se as implantações ILB estão configuradas para usar o retorno de servidor direto. Essa configuração reduzirá a latência quando ILBs do Azure forem usadas para configurações de alta disponibilidade na camada do DBMS.
        - Se você estiver usando Azure Load Balancer junto com os sistemas operacionais convidados do Linux, verifique se o parâmetro de rede do Linux **net.IPv4.tcp_timestamps** está definido como **0**. Essa recomendação entra em conflito com recomendações em versões mais antigas do [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421). A nota SAP agora é atualizada para declarar que esse parâmetro precisa ser definido como **0** para funcionar com os balanceadores de carga do Azure.
        - Considere usar [grupos de posicionamento de proximidade do Azure](../../co-location.md) para obter a latência de rede ideal. Para obter mais informações, consulte [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
   4. Implantações de alta disponibilidade e recuperação de desastres.
        - Se você implantar a camada de aplicativo SAP sem definir uma zona de disponibilidade do Azure específica, certifique-se de que todas as VMs que executam instâncias de diálogo do SAP ou instâncias de middleware de um único sistema SAP sejam implantadas em um [conjunto de disponibilidade](../../manage-availability.md).
        - Se você não precisar de alta disponibilidade para os serviços centrais do SAP e o DBMS, poderá implantar essas VMs no mesmo conjunto de disponibilidade que a camada de aplicativo SAP.
        - Se você proteger os serviços SAP central e a camada do DBMS para alta disponibilidade usando a replicação passiva, coloque os dois nós para os serviços centrais do SAP em um conjunto de disponibilidade separado e os dois nós DBMS em outro conjunto de disponibilidade.
        - Se você implantar o no Zonas de Disponibilidade do Azure, não poderá usar conjuntos de disponibilidade. Mas você precisa se certificar de implantar os nós de serviços centrais ativos e passivos em duas Zonas de Disponibilidade diferentes. Use Zonas de Disponibilidade que tenham a menor latência entre eles.
          Tenha em mente que você precisa usar o [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) para o caso de uso do estabelecimento de clusters de failover do Windows ou do pacemaker para o DBMS e a camada de serviços centrais do SAP em zonas de disponibilidade. Você não pode usar [Load Balancer básicas](../../../load-balancer/load-balancer-overview.md) para implantações zonais.
   5. Configurações de tempo limite.
        - Verifique os rastreamentos de desenvolvedor do SAP NetWeaver das instâncias do SAP para garantir que não haja quebras de conexão entre o servidor de enfileiramento e os processos de trabalho do SAP. Você pode evitar essas quebras de conexão definindo estes dois parâmetros de registro:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Para obter mais informações, consulte [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Para obter mais informações, consulte [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Para evitar tempos limite de GUI entre as interfaces GUI SAP locais e as camadas de aplicativo SAP implantadas no Azure, verifique se esses parâmetros estão definidos no default. PFL ou no perfil de instância:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Para evitar a interrupção de conexões estabelecidas entre o processo de enfileiramento do SAP e os processos de trabalho do SAP, você precisa definir o parâmetro **enfileirar/encni/set_so_keepalive** como **true**. Consulte também [SAP note #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Se você usar uma configuração de cluster de failover do Windows, certifique-se de que o tempo para reagir em nós não responsivos esteja definido corretamente para o Azure. O artigo [ajustando limites de rede de cluster de failover](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) lista parâmetros e como eles afetam o failover diferenciação. Supondo que os nós de cluster estejam na mesma sub-rede, você deve alterar esses parâmetros:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. Configurações ou patches do sistema operacional
        - Para executar o HANA no SAP, leia estas notas e documentações:
            -   [Nota de suporte da SAP #2814271 SAP HANA falha no backup no Azure com erro de soma de verificação](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Nota de suporte SAP #2753418-degradação do desempenho potencial devido ao fallback do timer](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Nota de suporte da SAP #2791572 degradação do desempenho devido ao VDSO ausente para Hyper-V no Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Nota de suporte da SAP #2382421-otimizando a configuração de rede no HANA e no nível do sistema operacional](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Nota de suporte da SAP #2694118 Red Hat Enterprise Linux HA Add-On no Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Nota de suporte SAP #1984787-SUSE LINUX Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Nota de suporte SAP #2002167-Red Hat Enterprise Linux 7. x: instalação e atualização](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [Nota de suporte SAP n° 2292690 – Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [Nota de suporte SAP #2772999-Red Hat Enterprise Linux 8. x: instalação e configuração](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Nota de suporte da SAP #2777782-SAP HANA DB: configurações de so recomendadas para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Nota de suporte SAP #2578899-SUSE Linux Enterprise Server 15: Nota de instalação](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Nota de suporte SAP #2455582-Linux: executando aplicativos SAP compilados com GCC 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Nota de suporte SAP #2729475-HWCCT falhou com o erro "o hipervisor não tem suporte" em VMs do Azure certificadas para SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Teste seus procedimentos de alta disponibilidade e recuperação de desastres.
   1. Simule situações de failover desligando VMs (sistemas operacionais convidados do Windows) ou colocando sistemas operacionais em modo de pane (sistemas operacionais convidados do Linux). Esta etapa ajudará você a descobrir se suas configurações de failover funcionam conforme projetado.
   1. Meça quanto tempo leva para executar um failover. Se os tempos forem muito longos, considere:
        - Para o SUSE Linux, use dispositivos SBD em vez do agente de limite do Azure para acelerar o failover.
        - Por SAP HANA, se a recarga de dados levar muito tempo, considere o provisionamento de mais largura de banda de armazenamento.
   3. Teste a sequência de backup/restauração e o tempo e faça correções, se necessário. Verifique se os tempos de backup são suficientes. Você também precisa testar as atividades de restauração e restauração de tempo. Certifique-se de que os tempos de restauração estejam em seus SLAs de RTO onde quer que o RTO dependa de um processo de restauração de VM ou banco de dados.
   4. Teste a arquitetura e a funcionalidade de DR entre regiões.
1. Verificações de segurança.
   1. Teste a validade da arquitetura do Azure RBAC (controle de acesso baseado em função) do Azure. O objetivo é separar e limitar o acesso e as permissões de equipes diferentes. Por exemplo, os membros da equipe SAP devem ser capazes de implantar VMs e atribuir discos do armazenamento do Azure em uma determinada rede virtual do Azure. Mas a equipe da SAP não deve ser capaz de criar suas próprias redes virtuais ou alterar as configurações de redes virtuais existentes. Os membros da equipe de rede não devem ser capazes de implantar VMs em redes virtuais nas quais o aplicativo SAP e as VMs do DBMS estão em execução. Nem os membros dessa equipe podem alterar atributos de VMs ou até mesmo excluir VMs ou discos.  
   1.  Verifique se o [grupo de segurança de rede e as regras de ASC](../../../virtual-network/network-security-groups-overview.md) funcionam conforme o esperado e proteja os recursos protegidos.
   1.  Verifique se todos os recursos que precisam ser criptografados foram criptografados. Defina e implemente processos para fazer backup de certificados, armazenar e acessar esses certificados e restaurar as entidades criptografadas.
   1.  Use [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) para discos do sistema operacional, quando possível, de um ponto de vista de suporte do so.
   1.  Certifique-se de que você não está usando muitas camadas de criptografia. Em alguns casos, faz sentido usar Azure Disk Encryption junto com um dos métodos Transparent Data Encryption DBMS para proteger diferentes discos ou componentes no mesmo servidor.  Por exemplo, em um servidor do SAP DBMS, o Azure Disk Encryption (ADE) pode ser habilitado no disco de inicialização do sistema operacional (se o sistema operacional oferecer suporte a ADE) e os discos de dados não usados pelos arquivos de persistência de dados do DBMS.  Um exemplo é usar o ADE no disco que contém as chaves de criptografia do DBMS TDE.
1. Teste de desempenho. No SAP, com base no rastreamento e nas medições do SAP, faça estas comparações:
   - Quando aplicável, compare os 10 principais relatórios online à sua implementação atual.
   - Quando aplicável, compare os 10 principais trabalhos de lote com sua implementação atual.
   - Compare transferências de dados por meio de interfaces no sistema SAP. Concentre-se nas interfaces em que você sabe que a transferência está indo entre locais diferentes, como do local para o Azure.


## <a name="non-production-phase"></a>Fase de não produção 
Nesta fase, presumimos que, após um piloto bem-sucedido ou uma prova de conceito (POC), você esteja começando a implantar sistemas SAP de não produção no Azure. Incorpore tudo o que você aprendeu e experimentou durante a VDC para essa implantação. Todos os critérios e etapas listados para POCs também se aplicam a essa implantação.

Durante essa fase, normalmente você implanta sistemas de desenvolvimento, sistemas de teste de unidade e sistemas de teste de regressão de negócios para o Azure. Recomendamos que pelo menos um sistema de não produção em uma linha de aplicativo SAP tenha a configuração de alta disponibilidade completa que o sistema de produção futuro terá. Aqui estão algumas etapas adicionais que você precisa concluir durante esta fase:  

1.  Antes de mover os sistemas da plataforma antiga para o Azure, colete dados de consumo de recursos, como uso da CPU, taxa de transferência de armazenamento e dados de IOPS. Especialmente colete esses dados das unidades de camada DBMS, mas também colete-os das unidades de camada de aplicativo. Também meça a latência de rede e de armazenamento.
2.  Registre os padrões de tempo de uso de disponibilidade de seus sistemas. O objetivo é descobrir se os sistemas de não produção precisam estar disponíveis o dia todo, todos os dias ou se há sistemas de não produção que podem ser desligados durante determinadas fases de uma semana ou mês.
3.  Teste e determine se você deseja criar suas próprias imagens de sistema operacional para suas VMs no Azure ou se deseja usar uma imagem da Galeria de imagens compartilhadas do Azure. Se você estiver usando uma imagem da Galeria de imagens compartilhadas, certifique-se de usar uma imagem que reflita o contrato de suporte com o fornecedor do sistema operacional. Para alguns fornecedores de sistema operacional, a Galeria de imagens compartilhadas permite que você traga suas próprias imagens de licença. Para outras imagens do so, o suporte está incluído no preço citado pelo Azure. Se você decidir criar suas próprias imagens de sistema operacional, poderá encontrar documentação nestes artigos:
    -   [Criar uma imagem generalizada de uma VM do Windows implantada no Azure](../../windows/capture-image-resource.md)
    -   [Criar uma imagem generalizada de uma VM Linux implantada no Azure](../../linux/capture-image.md)
3.  Se você usar imagens SUSE e Red Hat Linux da Galeria de imagens compartilhadas, será necessário usar as imagens do SAP fornecidas pelos fornecedores do Linux na Galeria de imagens compartilhadas.
4.  Certifique-se de atender aos requisitos de suporte do SAP para contratos de suporte da Microsoft. Consulte [#2015553 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/2015553). Para instâncias grandes do HANA, consulte [requisitos de integração](./hana-onboarding-requirements.md).
4.  Verifique se as pessoas certas recebem [notificações de manutenção planejada](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) para que você possa escolher os melhores tempos de inatividade.
5.  Verifique com frequência as apresentações do Azure em canais, como o [Channel 9](https://channel9.msdn.com/) , para novas funcionalidades que podem se aplicar às suas implantações.
6.  Verifique as notas SAP relacionadas ao Azure, como [Nota de suporte #1928533](https://launchpad.support.sap.com/#/notes/1928533), para novas SKUs de VM e versões de sistema operacional e DBMS com suporte recentemente. Compare o preço dos novos tipos de VM com relação aos tipos de VM mais antigos, para que você possa implantar VMs com a melhor relação preço/desempenho.
7.  Verifique novamente as notas de suporte do SAP, o SAP HANA o diretório de hardware e o PAM do SAP. Certifique-se de que não houve nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte nessas VMs e as versões de SAP e DBMS com suporte.
8.  Verifique [o site do SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para obter novos SKUs com certificação do Hana no Azure. Compare os preços de novos SKUs com aqueles que você planejou usar. Eventualmente, faça as alterações necessárias para usar aquelas que têm a melhor taxa de preço/desempenho.
9.  Adapte seus scripts de implantação para usar novos tipos de VM e incorpore novos recursos do Azure que você deseja usar.
10. Após a implantação da infraestrutura, teste e avalie a latência de rede entre as VMs da camada de aplicativo SAP e as VMs do DBMS, de acordo com as notas de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados em relação às diretrizes de latência de rede no [#1100926 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado ou bom. As exceções se aplicam ao tráfego entre as VMs e as unidades de instância grande do HANA, conforme documentado neste [artigo](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Certifique-se de que nenhuma das restrições mencionadas em [considerações para a implantação de DBMS de máquinas virtuais do Azure para cargas de trabalho do SAP](./dbms_guide_general.md#azure-network-considerations) e [SAP Hana configurações de infraestrutura e operações no Azure](./hana-vm-operations.md) se aplicam à sua implantação.
11. Verifique se suas VMs estão implantadas no [grupo correto de posicionamento de proximidade do Azure](../../co-location.md), conforme descrito em [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
11. Execute todas as outras verificações listadas para a fase de prova de conceito antes de aplicar a carga de trabalho.
12. À medida que a carga de trabalho se aplica, registre o consumo de recursos dos sistemas no Azure. Compare esse consumo com registros de sua plataforma antiga. Ajuste o dimensionamento de VM de implantações futuras se você vir que tem grandes diferenças. Tenha em mente que, quando você diminuir, o armazenamento e as larguras de banda de rede das VMs também serão reduzidas.
    - [Tamanhos das máquinas virtuais do Windows no Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Tamanhos das máquinas virtuais do Linux no Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimente a funcionalidade de cópia do sistema e os processos. O objetivo é facilitar a cópia de um sistema de desenvolvimento ou de um sistema de teste, portanto, as equipes de projeto podem obter novos sistemas rapidamente. 
14. Otimize e aprimore o acesso, as permissões e os processos baseados em função do Azure da sua equipe para verificar se você tem separação de tarefas. Ao mesmo tempo, verifique se todas as equipes podem executar suas tarefas na infraestrutura do Azure.
15. Execute, teste e documente os procedimentos de alta disponibilidade e recuperação de desastres para permitir que sua equipe executar essas tarefas. Identifique deficiências e adapte novas funcionalidades do Azure que você está integrando às suas implantações.


## <a name="production-preparation-phase"></a>Fase de preparação de produção 
Nesta fase, colete o que você experimentou e aprendeu durante suas implantações de não produção e aplique-as às implantações futuras de produção. Você também precisa preparar o trabalho da transferência de dados entre o local de hospedagem atual e o Azure.

1.  Conclua as atualizações de versão do SAP necessárias de seus sistemas de produção antes de mudar para o Azure.
1.  Concorde com os proprietários de negócios em testes funcionais e de negócios que precisam ser conduzidos após a migração do sistema de produção.
1.  Verifique se esses testes foram concluídos com os sistemas de origem no local de hospedagem atual. Evite realizar testes pela primeira vez depois que o sistema for movido para o Azure.
1.  Teste o processo de migração de sistemas de produção para o Azure. Se você não estiver movendo todos os sistemas de produção para o Azure durante o mesmo período de tempo, crie grupos de sistemas de produção que precisam estar no mesmo local de hospedagem. Teste a migração de dados. Estes são alguns métodos comuns:
    - Use métodos do DBMS como backup/restauração em combinação com SQL Server Always On, replicação de sistema do HANA ou envio de logs para propagar e sincronizar o conteúdo do banco de dados no Azure.
    - Use backup/restauração para bancos de dados menores.
    - Use o monitor de migração do SAP, que é integrado ao SAP SWPM, para executar migrações heterogêneas.
    - Use o processo do [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se você precisar combinar sua migração com uma atualização de versão do SAP. Tenha em mente que nem todas as combinações de DBMS de origem e DBMS de destino têm suporte. Você pode encontrar mais informações nas notas de suporte específicas do SAP para as diferentes versões do DMO. Por exemplo, a [opção de migração de banco de dados (DMO) de SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Teste se a taxa de transferência de dados é melhor através da Internet ou do ExpressRoute, caso você precise mover backups ou arquivos de exportação do SAP. Se estiver movendo dados pela Internet, talvez seja necessário alterar algumas das regras do grupo de segurança de aplicativo/grupo de segurança de rede que você precisará ter em vigor para sistemas de produção futuros.
1.  Antes de mover os sistemas de sua plataforma antiga para o Azure, colete os dados de consumo de recursos. Dados úteis incluem uso de CPU, taxa de transferência de armazenamento e dados de IOPS. Especialmente colete esses dados das unidades de camada DBMS, mas também colete-os das unidades de camada de aplicativo. Também meça a latência de rede e de armazenamento.
1.  Verifique novamente as notas de suporte do SAP e as configurações necessárias do sistema operacional, o SAP HANA o diretório de hardware e o PAM do SAP. Certifique-se de que não houve nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte nessas VMs e as versões de SAP e DBMS com suporte.
1.  Atualize os scripts de implantação para levar em conta as decisões mais recentes que você fez nos tipos de VM e na funcionalidade do Azure.
1.  Depois de implantar a infraestrutura e os aplicativos, valide isso:
    - Os tipos corretos de VM foram implantados, com os atributos corretos e os tamanhos de armazenamento.
    - As VMs estão nas versões e patches corretos e desejados do sistema operacional e são uniformes.
    - As VMs são protegidas conforme necessário e de forma uniforme.
    - As versões e os patches de aplicativos corretos foram instalados e implantados.
    - As VMs foram implantadas em conjuntos de disponibilidade do Azure como planejadas.
    - O armazenamento Premium do Azure é usado para discos sensíveis à latência ou onde o [SLA de uma única VM de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) é necessário.
    - O Acelerador de Gravação do Azure está implantado corretamente.
        - Certifique-se de que, dentro das VMs, espaços de armazenamento ou conjuntos de distribuição foram criados corretamente em discos que precisam de Acelerador de Gravação.
        - Verifique a [configuração do RAID de software no Linux](/previous-versions/azure/virtual-machines/linux/configure-raid).
        - Verifique a [configuração do LVM em VMs do Linux no Azure](/previous-versions/azure/virtual-machines/linux/configure-lvm).
    - Os [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) são usados exclusivamente.
    - As VMs foram implantadas nos conjuntos de disponibilidade e Zonas de Disponibilidade corretos.
    - A [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) é habilitada nas VMs usadas na camada de aplicativo SAP e na camada do DBMS SAP.
    - Não há soluções de [virtualização de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de sistemas SAP com base no SAP NetWeaver, Hybris ou S/4HANA.
    - As regras de grupo de segurança de aplicativos e de grupos de segurança de rede permitem a comunicação conforme desejado e planejada e bloqueiam a comunicação quando necessário.
    - As configurações de tempo limite são definidas corretamente, conforme descrito anteriormente.
    - As VMs são implantadas no grupo correto de [posicionamento de proximidade do Azure](../../co-location.md), conforme descrito em [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
    - A latência de rede entre as VMs da camada de aplicativo SAP e as VMs do DBMS é testada e validada, conforme descrito nas notas de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados em relação às diretrizes de latência de rede no [#1100926 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado ou bom. As exceções se aplicam ao tráfego entre as VMs e as unidades de instância grande do HANA, conforme documentado neste [artigo](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
    - A criptografia foi implementada quando necessário e com o método de criptografia apropriado.
    - Interfaces e outros aplicativos podem se conectar à infraestrutura implantada recentemente.
1.  Crie um guia estratégico para reagir à manutenção planejada do Azure. Determine a ordem na qual os sistemas e as VMs devem ser reinicializados para manutenção planejada.
    

## <a name="go-live-phase"></a>Fase de ativação
Durante a fase de ativação, não se esqueça de seguir os guias estratégicos desenvolvidos durante as fases anteriores. Execute as etapas que você testou e praticado. Não aceite alterações de último minuto em configurações e processos. Conclua também estas etapas:

1. Verifique se o monitoramento do portal do Azure e outras ferramentas de monitoramento estão funcionando. Recomendamos o monitor de desempenho do Windows (Perfmon) para Windows e SAR para Linux.
    - Contadores de CPU.
        - Tempo médio de CPU, total (todas as CPUs)
        - Tempo médio de CPU, cada processador individual (128 processadores em VMs m128)
        - Tempo do kernel da CPU, cada processador individual
        - Tempo de usuário da CPU, cada processador individual
    - Memória.
        - Memória livre
        - Página de memória em/segundo
        - Saída de página de memória/segundo
    - Disk.
        - Leitura de disco em KBps, por disco individual
        - Leituras de disco/segundo, por disco individual
        - Leitura de disco em microssegundos/leitura, por disco individual
        - Gravação de disco em KBps, por disco individual
        - Gravação de disco/segundo, por disco individual
        - Gravação de disco em microssegundos/leitura, por disco individual
    - Network.
        - Pacotes de rede em/segundo
        - Saída de pacotes de rede/segundo
        - KB de rede em/segundo
        - KB de rede de saída/segundo
1.  Após a migração de dados, execute todos os testes de validação acordados com os proprietários da empresa. Aceite os resultados do teste de validação somente quando houver resultados para os sistemas de origem originais.
1.  Verifique se as interfaces estão funcionando e se outros aplicativos podem se comunicar com os sistemas de produção implantados recentemente.
1.  Verifique o sistema de transporte e de correção por meio da transação SAP STMS.
1.  Execute backups de banco de dados depois que o sistema for liberado para produção.
1.  Execute backups de VM para as VMs da camada de aplicativo SAP depois que o sistema for liberado para produção.
1.  Para sistemas SAP que não fazem parte da fase atual de ativação, mas que se comunicam com os sistemas SAP movidos para o Azure durante essa fase de ativação, você precisa redefinir o buffer de nome de host em SM51. Isso removerá os endereços IP antigos armazenados em cache associados aos nomes das instâncias do aplicativo que você moveu para o Azure.  


## <a name="post-production"></a>Após a produção
Esta fase trata do monitoramento, da operação e da administração do sistema. De um ponto de vista do SAP, as tarefas usuais que você precisaria concluir em seu local de hospedagem antigo se aplicam. Conclua também estas tarefas específicas do Azure:

1. Examine as notas fiscais do Azure para sistemas de alta carga.
2. Otimize a eficiência de preço/desempenho no lado da VM e no lado do armazenamento.
3. Otimize os horários em que você pode desligar os sistemas.  


## <a name="next-steps"></a>Próximas etapas
Consulte estes artigos:

- [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver](./planning-guide.md)
- [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver](./deployment-guide.md)
- [Considerações sobre a implantação de DBMS de máquinas virtuais do Azure para cargas de trabalho do SAP](./dbms_guide_general.md)