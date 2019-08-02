---
title: Lista de verificação de planejamento e implantação de carga de trabalho do SAP | Microsoft Docs
description: lista de verificação para planejamento e implantações de carga de trabalho SAP no Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: abe04a88f3337ed26b98815b5633fd5d719b7ce3
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234217"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Lista de verificação de planejamento e implantação de carga de trabalho SAP no Azure 

Essa lista de verificação é projetada para clientes que estão movendo seus aplicativos SAP NetWeaver, S/4HANA e Hybris para a Infraestrutura como Serviço do Azure.  Essa lista de verificação deve ser examinada por um cliente e/ou parceiro SAP durante o projeto. É importante que muitas das verificações sejam realizadas no início do projeto e na fase de planejamento. Quando a implantação estiver concluída, alterações elementares à infraestrutura do Azure implantada ou a versões de software SAP poderão se tornar complexas. Examine esta lista de verificação em etapas-chave em todo o projeto.  Pequenos problemas podem ser detectados antes que se tornem grandes problemas e haja tempo suficiente para remodelar e testar todas as alterações necessárias. A lista de verificação de modo algum alega ser completa. Dependendo de sua situação individual, talvez seja necessário realizar mais verificações. 

A lista de verificação montada não inclui tarefas independentes do Azure.  Exemplo: As interfaces do aplicativo SAP mudam durante uma movimentação para a Nuvem Pública do Azure ou para um provedor de hospedagem.    

Esta lista de verificação também pode ser usada para sistemas já implantados. Novos recursos, como o Acelerador de Gravação, as Zonas de Disponibilidade e novos tipos de VM podem ter sido adicionados desde que você implantou.  Portanto, é útil examinar a lista de verificação periodicamente para estar ciente dos novos recursos na plataforma do Azure. 

## <a name="project-preparation-and-planning-phase"></a>Fase de planejamento e preparação do projeto
Nesta fase, uma migração da carga de trabalho SAP para a nuvem pública do Azure está planejada. O conjunto mínimo de entidades e itens discutidos e definidos são:

1. Documento de Design de Alto Nível – esse documento deve conter:
    1. O inventário atual de aplicativos e componentes SAP e inventário de aplicativos de destino no Azure
    2. Crie e trabalhe usando uma RACI (Matriz de Atribuição de Responsabilidade) que define as responsabilidades e as atribuições das diferentes partes envolvidas. Inicie no nível alto e trabalhe para níveis cada vez mais granulares no planejamento e nas primeiras implantações
    2. Uma arquitetura da solução de alto nível
    3. Decisão sobre regiões do Azure nas quais implantar. Para obter uma lista de regiões do Azure, confira [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Para os serviços disponíveis em cada uma das regiões do Azure, leia o artigo [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/)
    4. Arquitetura de rede para se conectar do local ao Azure. Comece a familiarizar-se com o [blueprint do Datacenter Virtual para o Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Princípios de segurança para executar dados de alto impacto empresarial no Azure. Para ler o material, comece com a [Documentação de Segurança do Azure](https://docs.microsoft.com/azure/security/)
2.  Documento de Design técnico – que contém:
    1.  Um diagrama de blocos da solução 
    2.  Dimensionamento de componentes de computação, armazenamento e de rede no Azure. Para dimensionamento do SAP de VMs do Azure, confira a nota de suporte SAP [nº 1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Arquitetura de continuidade dos negócios e recuperação de desastres
    4.  Versões de pacote de suporte SAP, Kernel, BD e SO detalhadas. Não é certo que qualquer versão do sistema operacional compatível com o SAP NetWeaver ou S/4HANA seja compatível com VMs do Azure. Mesmo é verdadeiro para as versões do DBMS. É obrigatório verificar as fontes a seguir para alinhar e, se necessário, atualizar as versões do SAP, do DBMS ou do sistema operacional para que estejam em uma janela compatível do Azure e do SAP. É obrigatório que você esteja dentro de combinações de versão com suporte do Azure e do SAP para obter suporte total da SAP e da Microsoft. Se necessário, você precisa planejar a atualização de alguns componentes de software. Mais detalhes sobre software DBMS, sistema operacional e SAP com suporte estão documentadas nestes locais:
        1.  Nota de suporte SAP [nº 1928533](https://launchpad.support.sap.com/#/notes/1928533). Essa nota define as versões mínimas do sistema operacional com suporte em VMs do Azure. Também define as versões de banco de dados mínimas necessárias para a maioria dos banco de dados não HANA. A nota também apresenta o dimensionamento SAP dos diferentes tipos de VM do Azure com suporte da SAP.
        2.  Nota de suporte SAP [nº 2039619](https://launchpad.support.sap.com/#/notes/2039619). A nota define a matriz de suporte da Oracle no Azure. Perceba que a Oracle apenas dá suporte ao Windows e ao Oracle Linux como SO convidado no Azure para carga de trabalho SAP. Essa instrução de suporte se aplica à camada do aplicativo SAP que executa instâncias SAP também. No entanto, a Oracle não dá suporte à alta disponibilidade para SAP Central Services em Oracle Linux por meio do Pacemaker. Se precisar de alta disponibilidade para ASCS no Oracle Linux, você precisa aproveitar SIOS Protection Suite para Linux. Para dados detalhados de certificação do SAP, verifique a observação de suporte SAP [#1662610 – detalhes de suporte do SIOS Protection Suite para Linux](https://launchpad.support.sap.com/#/notes/1662610). Para Windows, a solução de Failover de Cluster de Failover do Windows compatível com SAP para SAP Central Services tem suporte em conjunto com Oracle como camada DBMS. 
        3.  Nota de suporte SAP [nº 2235581](https://launchpad.support.sap.com/#/notes/2235581) para obter a matriz de suporte para SAP HANA em diferentes versões do sistema operacional
        4.  As VMs do Azure com suporte no SAP HANA e [Instâncias Grandes do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) listadas [aqui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [Matriz de Disponibilidade de Produto SAP](https://support.sap.com/en/)
        6.  Outras notas SAP para outros produtos específicos da SAP  
    5.  Recomendamos usar designs de camada 3 estritos para sistemas de produção SAP. Não é recomendável combinar servidores ASCS + APP na mesma VM.  Usar configurações de cluster de vários SID para o SAP Central Services é compatível com o Windows como SO convidado no Azure. No entanto, não há suporte para configurações de cluster de vários SIDs no SAP Central Services com sistemas operacionais Linux no Azure. A documentação para o caso de SO convidado Windows pode ser encontrada em:
        1.  [Alta disponibilidade de vários SIDs da instância do SAP ASCS/SCS com clustering de failover do Windows Server e disco compartilhado no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Alta disponibilidade de vários SIDs da instância do SAP ASCS com clustering de failover do Windows Server e compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Arquitetura de alta disponibilidade e recuperação de desastre
        1.  Defina, com base em RTO e RPO, como a arquitetura de alta disponibilidade e recuperação de desastre deve ser
        2.  Para alta disponibilidade na mesma zona, verifique o que o DBMS desejado tem a oferecer no Azure. A maioria dos DBMS oferecem métodos síncronos de uma espera ativa síncrona, que recomendamos para sistemas de produção. Também verifique a documentação relacionada do SAP para bancos de dados diferentes, começando com [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para a carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e documentos relacionados
            1.  Usar o Serviço de Cluster de Failover do Windows com a configuração de disco compartilhado para a camada de DBMS, como descrito para o SQL Server [aqui](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017), é um recurso que **NÃO** tem suporte. Em vez disso, soluções como:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Replicação de Sistema do HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Para recuperação de desastres em diferentes regiões do Azure, verifique quais as possibilidades são oferecidas por diferentes fornecedores de DBMS. A maioria dá suporte para replicação assíncrona ou envio de logs
        4.  Para a camada de aplicativo SAP, defina se você executaria seus sistemas de teste de regressão empresarial, que idealmente são réplicas de suas implantações de produção, na mesma região do Azure ou na sua região de recuperação de desastre. No último caso, você pode direcionar esse sistema de regressão empresarial como destino de recuperação de desastre para produção
        5.  Se você decidir não colocar os sistemas não de produção no site de DR, avalie o Azure Site Recovery como método viável para replicar a camada de aplicativo SAP para a região de DR do Azure. Confira também [Configurar a recuperação de desastre para uma implantação de aplicativo do SAP NetWeaver de várias camadas](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Se você optar por usar uma configuração de HA/DR combinada aproveitando [Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview), familiarize-se com as regiões do Azure em que as Zonas de Disponibilidade estão disponíveis e com as restrições que podem ser introduzidas pelas latências de rede maiores entre as duas zonas de disponibilidade  
3.  O Cliente/Parceiro deve criar um inventário de todas as interfaces SAP (SAP e não SAP). 
4.  Design do Design de Serviços de Base – esse design inclui itens como
    1.  Design de DNS e Active Directory
    2.  Topologia de Rede no Azure e a atribuição de diferentes sistemas SAP
    3.  Estrutura de [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) para suas diferentes equipes que gerenciam infraestrutura e aplicativos SAP no Azure
    3.  Topologia do grupo de recursos 
    4.  [Estratégia de marcação](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Convenção de nomenclatura para VMs e outros componentes de infraestrutura e/ou nomes lógicos
5.  Contrato de Suporte Premier da Microsoft – identifique o TAM (gerente de conta técnica) da MS. Para ver os requisitos de suporte da SAP, leia a nota de suporte SAP [nº 2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Defina o número de assinaturas do Azure e a cota central para diferentes assinaturas. [Abra solicitações de suporte para aumentar as cotas de assinaturas do Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) conforme necessário 
7.  Plano de migração de dados e redução de dados para migrar dados do SAP para o Azure. Para sistemas SAP NetWeaver, a SAP tem diretrizes sobre como manter o volume de um grande número de dados limitado. A SAP publicou [este guia aprofundado](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) sobre o gerenciamento de dados em sistemas de ERP SAP. No entanto, algum conteúdo se aplica a sistemas NetWeaver e S/4HANA em geral.
8.  Defina e decida a abordagem de implantação automatizada. A meta da automação por trás de implantações de infraestrutura no Azure é implantar de modo determinista e obter resultados deterministas. Muitos clientes usam o scripts baseados em CLI ou PowerShell. Mas há várias tecnologias de código-fonte aberto que podem ser usadas para implantar a infraestrutura do Azure para SAP e até mesmo instalar o software SAP. Exemplos podem ser encontrados no GitHub:
    1.  [Implantações automatizadas do SAP na nuvem do Azure](https://github.com/Azure/sap-hana)
    2.  [Instalação do SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definir uma cadência regular de análise de design e implantação entre você como cliente, o integrador de sistema, a Microsoft e outras partes envolvidas

 
## <a name="pilot-phase-strongly-recommended"></a>Fase piloto (fortemente recomendada)
 
O piloto pode ser executado antes ou junto com o planejamento e a preparação do projeto. A fase também pode ser usada para testar abordagens e o design feito nas fases de planejamento e preparação. A fase piloto pode ser estendida para uma prova de conceitos real. É recomendável configurar e validar uma solução completa de HA/DR, bem como o design de segurança, durante uma implantação piloto. Em alguns casos de cliente, testes de escalabilidade também podem ser realizados nessa fase. Outros clientes usam a implantação de sistemas de área restrita do SAP como fase piloto. Portanto, vamos supor que você tenha identificado um sistema que você queira migrar para o Azure para executar um piloto.

1. Otimize a transferência de dados no Azure. A transferência de casos de clientes altamente dependente do [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) do local foi mais rápida se o circuito expresso tiver largura de banda suficiente. Com outros clientes, passar pela Internet se mostrou mais rápido
2. No caso de uma migração de plataformas heterogêneas da SAP, que envolve exportação e importação do banco de dados, teste e otimização das fases de exportação e importação. Para migrações grandes que envolvem o SQL Server como a plataforma de destino, as recomendações podem ser encontradas [aqui](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Você poderá usar a abordagem do Monitor de Migração/SWPM caso não precise de uma atualização de versão combinada ou processo [DMO SAP](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) quando combinar a migração com uma atualização de versão do SAP e atender a determinadas combinações de plataforma DBMS de origem e destino conforme documentado, por exemplo, em [DMO (Opção de Migração de Banco de Dados) de SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Exportar para a origem, Exportar upload de arquivo para o Azure e Desempenho de importação.  Maximizar a sobreposição entre exportação e importação
   2.  Avaliar o volume do banco de dados entre a plataforma alvo e de destino para refletir o dimensionamento da infraestrutura    
   3.  Validar e otimizar o tempo 
3. Validação técnica 
   1. Tipos de VM
      1.  Valide os recursos nas notas de suporte do SAP, SAP HANA diretório de hardware e o PAM do SAP novamente para certificar-se de que não houvesse nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte para esses tipos de VM e as versões de SAP e DBMS com suporte
      2.  Valide novamente o dimensionamento do seu aplicativo e a infraestrutura que você implanta no Azure. No caso de mover aplicativos existentes, você geralmente pode derivar os SAPS necessários da infraestrutura que você usa e a [página da Web de parâmetro de comparação SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e compará-los com os números SAPS listados na nota suporte SAP [nº 1928533](https://launchpad.support.sap.com/#/notes/1928533). Também leve em conta [este artigo](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
      3.  Avalie e teste o dimensionamento de suas VMs do Azure em relação à taxa de transferência máxima de armazenamento e à taxa de transferência de rede de diferentes tipos de VM que você escolheu na fase de planejamento. Os dados podem ser encontrados em:
          1.  [Tamanhos das Máquinas Virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar a **taxa de transferência máxima de disco sem cache** para dimensionamento
          2.  [Tamanhos para Máquinas Virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) É importante considerar a **taxa de transferência máxima de disco sem cache** para dimensionamento
   2. Armazenamento
      1.  Usar o [armazenamento de SSD standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) como mínimo para VMs que representam as camadas de aplicativos SAP e para a implantação de DBMS não sensível ao desempenho
      2.  Recomendamos não usar os [discos do Azure HDD Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) em geral
      2.  Usar o [armazenamento Premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) para qualquer VM do DBMS que seja sensível ao desempenho remotamente
      2.  Usar [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)
      3.  Use o Acelerador de Gravação do Azure para unidades de log do DBMS com a série M. Esteja ciente dos limites e do uso do Acelerador de gravação conforme documentado em [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  Para os diferentes tipos DBMS, confira a [documentação genérica de DBMS relacionado a SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e a documentação específica do DBMS
      5.  Para o SAP HANA, mais detalhes estão documentados em [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  Nunca monte discos de dados do Azure em uma VM Linux do Azure usando a ID do dispositivo. Em vez disso, use o identificador universal exclusivo (UUID). Tenha cuidado ao usar, por exemplo, ferramentas gráficas para montar discos de dados do Azure. Verifique novamente as entradas em /etc/fstab para ver se os discos são montados usando o UUID
          1.  Encontre mais detalhes [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Rede
      1.  Testar e avaliar sua infraestrutura de VNet e a distribuição de seus aplicativos SAP entre ou dentro das diferentes redes virtuais do Azure
          1.  Avaliar a abordagem da arquitetura de rede virtual de hub e spoke ou microssegmentação em uma única rede virtual do Azure com base em
              1.  Custos devido à troca de dados entre [VNETs do Azure emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Para custos, confira [Preço de Rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  A vantagem da desconexão rápida do emparelhamento entre redes virtuais do Azure em comparação à alteração de NSG para isolar uma sub-rede dentro de uma rede virtual para casos em que aplicativos ou VMs hospedadas em uma sub-rede da rede virtual tornam-se um risco de segurança
              3.  Registro em log central e auditoria de tráfego de rede entre locais, o mundo exterior e o datacenter virtual criado no Azure
          2.  Avalie e teste o caminho de dados entre a camada de aplicativo SAP e a camada do SAP DBMS. 
              1.  Não há nenhum suporte para posicionamentos de [Dispositivos de Rede Virtual do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou sistema SAP baseado em S/4HANA
              2.  Não há suporte para colocar a camada de aplicativo SAP e o SAP DBMS em diferentes redes virtuais do Azure que não estejam emparelhadas
              3.  Há suporte para [Regras do Azure ASG e NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) para definir rotas entre a camada de aplicativo SAP e a camada do SAP DBMS
          3.  Verifique se a [Rede Acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitada nas VMs usadas na camada do aplicativo SAP e na camada do SAP DBMS. Lembre-se de que diferentes níveis de sistema operacional são necessários para dar suporte à Rede Acelerada no Azure:
              1.  Windows Server 2012 R2 ou versões mais recentes
              2.  SUSE Linux 12 SP3 ou versões mais recentes
              3.  RHEL 7.4 ou versões mais recentes
              4.  Oracle Linux 7.5. Usando o kernel RHCKL, a versão deve ser 3.10.0-862.13.1.el7. É necessário usar o kernel Oracle UEK versão 5
          4.   Teste e avalie a latência de rede entre a VM da camada de aplicativo SAP e a VM do DBMS de acordo com a nota de suporte SAP [nº 500235](https://launchpad.support.sap.com/#/notes/500235) e a nota de suporte SAP [nº 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados em relação à orientação de latência de rede da observação de suporte SAP [nº 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e bom. Exceções aplicam-se a o tráfego entre VMs e unidades de Instância Grande do HANA conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
          5.   Verifique se as implantações de ILB são configuradas para usar o Retorno de Servidor Direto. Essa configuração reduzirá a latência em casos em que ILBs do Azure são usados para configurações de alta disponibilidade na camada de DBMS
          6.   Se você estiver usando Azure Load Balancer em conjunto com os sistemas operacionais convidados do Linux, verifique se o parâmetro de rede do Linux **net. IPv4. TCP _timestamps** está definido como **0**. Contra as recomendações em versões mais antigas do SAP Note [#2382421](https://launchpad.support.sap.com/#/notes/2382421). A nota SAP, enquanto isso, é atualizada para refletir o fato de que o parâmetro precisa ser definido como 0 para trabalhar em conjunto com os balanceadores de carga do Azure.
          7.   Considere usar o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md) para obter a latência de rede ideal.
   4. Implantações de alta disponibilidade e recuperação de desastre. 
      1. Se você implantar a camada de aplicativo SAP sem definir uma zona de disponibilidade específica do Azure, verifique se todas as VMs que estão executando a instância de diálogo SAP ou instâncias de middleware de um único sistema SAP estão implantadas em um [Conjunto de Disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   Caso você não precise de alta disponibilidade para o SAP Central Services e o DBMS, essas VMs poderão ser implantadas no mesmo conjunto de disponibilidade que a camada de aplicativo SAP
      2. Se você proteger o SAP Central Services e a camada DBMS para alta disponibilidade com réplicas passivas, tenha os dois nós para SAP Central Services em um Conjunto de Disponibilidade separado e os dois nós DBMS em outro Conjunto de Disponibilidade
      3. Se você implantar em Zonas de Disponibilidade do Azure, não poderá aproveitar os Conjuntos de Disponibilidade. No entanto, precisaria implantar os nós do Central Services ativo e passivo em duas Zonas de Disponibilidade diferentes, que mostram a menor latência entre zonas.
         1.   Lembre-se de que você precisa usar o [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) para o caso de estabelecer Clusters de Failover do Pacemaker ou do Windows para a camada do SAP Central Services e do DBMS entre zonas de disponibilidade. O [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) não pode ser usado para implantações zonais 
   5. Configurações de tempo limite
      1. Examine os rastreamentos de desenvolvedor do SAP NetWeaver das diferentes instâncias do SAP e verifique se nenhuma interrupção de conexão entre o servidor de enfileiramento e os processos de trabalho do SAP é observada. Essas interrupções de conexão podem ser evitadas definindo estes dois parâmetros de Registro:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 – veja também [este artigo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 – veja também [este artigo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Para evitar tempos limites de GUI entre interfaces da SAP GUI implantadas locais e camadas de aplicativo SAP implantadas no Azure, verifique se os parâmetros a seguir estão definidos no default.pfl ou no perfil da instância:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. Para evitar a interrupção de conexões estabelecidas entre o processo de enfileiramento do SAP e o SAP workprocesse, o parâmetro enfileirar/encni/set_so_keepalive precisa ser definido como "true". Consulte também o [SAP Note #2743751](https://launchpad.support.sap.com/#/notes/2743751)  
      3. Se você usar uma configuração de Cluster de Failover do Windows, verifique se o tempo para reagir em nós não responsivos está definido corretamente para o Azure. O artigo da Microsoft [Ajustando limites de rede de cluster de failover](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) lista parâmetros e como eles afetam as sensibilidades de failover. Supondo que os nós de cluster estejam na mesma sub-rede, os seguintes parâmetros devem ser alterados:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Testar seus procedimentos de alta disponibilidade e recuperação de desastre
   1. Simule situações de failover desligando VMs (SO convidado do Windows) ou colocando os sistemas operacionais no modo de pânico (SO convidado do Linux) para descobrir se suas configurações de failover funcionam como planejado. 
   2. Meça seu tempo necessário para executar um failover. Se os tempos forem excessivos, considere:
      1.   Para o SUSE Linux, usar dispositivos SBD em vez do agente de isolamento do Azure para acelerar o failover
      2.   Para o SAP HANA, se o recarregamento de dados levar muito tempo, considere provisionar mais largura de banda de armazenamento
   3. Testar a sequência de backup/restauração e ajustar se necessário. Assegure que não apenas os tempos de backup sejam suficientes. Teste também a restauração e anote o tempo em atividades de restauração. verifique se os tempos de restauração estão dentro de seus SLAs de RTO quando o RTO se baseia em um banco de dados ou processo de restauração de VM
   4. Testar a funcionalidade e a arquitetura de recuperação de desastres entre regiões
5. Verificações de segurança
   1.  Teste a validade da arquitetura RBAC (acesso baseado em função) do Azure implementada. A meta é separar e limitar o acesso e as permissões de diferentes equipes. Por exemplo, membros da equipe SAP de Base devem poder implantar VMs e atribuir os discos do Armazenamento do Azure a uma determinada rede virtual do Azure. No entanto, a equipe SAP de Base não deve poder criar as próprias redes virtuais ou alterar as configurações de redes virtuais existentes. Por outro lado, os membros da equipe de rede não devem poder implantar VMs em redes virtuais nas quais VMs do DBMS e do aplicativo SAP estejam em execução. Os membros da equipe de rede também não devem poder alterar os atributos de VMs nem mesmo excluir VMs ou discos.  
   2.  Verifique se as regras de [NSG e ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) estão funcionando conforme o esperado e blindam os recursos protegidos
   3.  Verifique se todos os recursos que precisam ser criptografados foram criptografados. Defina e execute processos para fazer backup de certificados, armazenar e acessar esses certificados e restaurar as entidades criptografadas. 
   4.  Use a [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) e/ou para discos do sistema operacional, quando possível, de um ponto de vista de suporte de sistema operacional
   5.  Verifique se não foram usadas camadas de criptografia demais. Faz sentido limitado para usar o Azure Disk Encryption e então sobre um dos métodos de Transparent Database Encryption do DBMS
6. Teste de desempenho
   1.  Em SAP baseado em rastreamento e medidas do SAP, compare os 10 principais relatórios online para a implementação atual, quando aplicável 
   2.  Em SAP baseado em rastreamento e medidas do SAP, compare os 10 principais trabalho em lotes para a implementação atual, quando aplicável 
   3.  Em SAP baseado em rastreamento e medidas do SAP, compare as transferências de dados por meio de interfaces para o sistema SAP. Concentre-se nas interfaces em que você sabe que agora a transferência está indo entre locais diferentes, como ir do local para o Azure 


## <a name="non-production-phase"></a>Fase não de produção 
Nesta fase, vamos supor que, após um piloto ou prova de conceito bem-sucedido, você esteja começando a implantar sistemas SAP não de produção no Azure. Todos os aprendizados e experiências da prova de conceitos devem ser adaptados a tal implantação. Todas as etapas listadas na PoC e critérios se aplicam a esse tipo de implantação também. Nesta fase, você geralmente implanta sistemas de desenvolvimento, sistemas de testes de unidade e sistemas de testes de regressão empresarial no Azure. É recomendável que pelo menos um sistema não de produção em uma linha de aplicativo SAP tenha a configuração de alta disponibilidade completa que o sistema de produção futuro terá. As etapas adicionais que você deve considerar nessa fase são:  

1.  Antes de mover sistemas da plataforma antigos para o Azure, colete dados de consumo de recursos, como o uso da CPU, taxa de transferência de armazenamento e dados IOPS. Especialmente das unidades de camada DBMS, mas também das unidades da camada de aplicativo. Também meça a latência de rede e de armazenamento.
2.  Registre os padrões de tempo de uso de disponibilidade dos sistemas. A meta é descobrir se os sistemas não de produção precisam estar disponíveis 24 horas por dia e sete dias por semana ou se há sistemas não de produção que podem ser desligados durante determinadas fases de uma semana ou mês
3.  Teste e defina se deseja criar as próprias imagens do sistema operacional para suas VMs no Azure ou usar uma imagem da Galeria de imagens do Azure. Se você estiver usando uma imagem da galeria do Azure, verifique se você obteve a imagem correta que reflete o contrato de suporte com o fornecedor do sistema operacional. Para alguns fornecedores de sistema operacional, as galerias do Azure oferecem a opção de levar suas próprias imagens de licença. Para outras imagens do sistema operacional, o suporte está incluído no preço cotado pelo Azure. Se você decidir criar suas próprias imagens de sistema operacional, poderá encontrar documentação nestes artigos:
    1.  Você pode criar uma imagem generalizada de uma VM do Windows implantada no Azure com base [nesta documentação](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Você pode criar uma imagem generalizada de uma VM do Linux implantada no Azure com base [nesta documentação](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Se você usar imagens SUSE e Red Hat Linux da galeria da VM do Azure, precisará usar as imagens para SAP fornecidas pelos fornecedores do Linux na galeria da VM do Azure
4.  Atenda aos requisitos de suporte da SAP sobre contratos de Suporte da Microsoft. Informações podem ser encontradas na nota de suporte SAP [nº 2015553](https://launchpad.support.sap.com/#/notes/2015553). Para Instâncias Grandes do HANA, veja o documento [Requisitos de integração](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Verifique se as pessoas certas recebem [notificações de manutenção planejada](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) de modo que você possa escolher o tempo de inatividade e uma reinicialização das VMs a tempo
5.  Verifique continuamente a documentação do Azure de apresentações da Microsoft em canais como [Channel9](https://channel9.msdn.com/) para nova funcionalidade que possa se aplicar às suas implantações
6.  Confira as notas SAP relacionadas ao Azure, como a nota de suporte [nº 1928533](https://launchpad.support.sap.com/#/notes/1928533) para novas SKUs de VM ou a versão do DBMS ou sistema operacional com suporte recente. Compare novos tipos de VM com tipos de VM mais antigos em termos de preço para que você possa implantar VMs com a melhor relação de preço para desempenho
7.  Valide os recursos nas notas de suporte do SAP, SAP HANA diretório de hardware e o PAM do SAP novamente para certificar-se de que não houvesse nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte nessas VMs e as versões de SAP e DBMS com suporte
8.  Verifique [aqui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) novas SKUs certificadas para HANA no Azure e compare o preço com novas que você planejou e, por fim, altere para obter as unidades com a melhor relação de preço para desempenho 
9.  Adapte seus scripts de implantação para aproveitar os novos tipos VM e incorporar os novos recursos do Azure que você deseja usar
10. Depois de implantar a infraestrutura, teste e avalie a latência de rede entre a VM da camada de aplicativo SAP e a VM do DBMS de acordo com a nota de suporte SAP [nº 500235](https://launchpad.support.sap.com/#/notes/500235) e a nota de suporte SAP [nº 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados em relação à orientação de latência de rede da observação de suporte SAP [nº 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e bom. Exceções aplicam-se a o tráfego entre VMs e unidades de Instância Grande do HANA conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Verifique se nenhuma das restrições mencionadas em [Considerações para implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) e [Configurações e operações de infraestrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) se aplicam à sua implantação
11. Certifique-se de que as VMs sejam implantadas usando o grupo de posicionamento correto de [proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md)
11. Execute todas as outras verificações conforme listado na fase de Prova de Conceitos antes de aplicar a carga de trabalho
12. Conforme a carga de trabalho se aplica, registre o consumo de recursos desses sistemas no Azure e compare-o com os registros que você obteve na sua plataforma antiga. Ajuste o dimensionamento da VM de implantações futuras se você detectar que há diferenças grandes. Lembre-se de que, no caso de redução, o armazenamento e as larguras de banda de rede de uma VM também serão reduzidos:
    1.  [Tamanhos das Máquinas Virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Tamanhos das máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Trabalhe em processos e funcionalidade de cópia do sistema. A meta é tornar mais fácil para você copiar um sistema de desenvolvimento ou um sistema de teste para que as equipes de projeto possam obter novos sistemas rapidamente. Considere [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) como uma ferramenta que realiza essas tarefas.
14. Otimize e aprimore os acessos, as permissões e os processos baseados em função do Azure da equipe para que você tenha uma separação de obrigações por um lado. Por outro lado, você deseja que todas as equipes estejam habilitadas a realizar as respectivas tarefas na infraestrutura do Azure.
15. Exercite, teste e documente procedimentos de alta disponibilidade e recuperação de desastres para permitir que a sua equipe executar essas tarefas. Identifique deficiências e adapte nova funcionalidade do Azure que você esteja integrando em suas implantações

 
## <a name="production-preparation-phase"></a>Fase de preparação de produção 
Nesta fase, você deseja coletar todas as experiências e aprendizados de suas implantações não de produção e aplicá-las a futuras implantações de produção. Além para as fases de antes, você também precisará preparar o trabalho da transferência de dados entre a localização de hospedagem atual e o Azure. 

1.  Trabalhe em atualizações de versão SAP necessárias dos seus sistemas de produção antes de passar para o Azure
2.  Concorde com os proprietários de negócios sobre os testes funcionais e empresariais que precisam ser feitos após a migração do sistema de produção
    1.  Verifique se todos esses testes são executados com os sistemas de origem na localização de hospedagem atual. Você deseja evitar que sejam feitos testes pela primeira vez depois que o sistema for movido para o Azure
2.  Teste o processo de migração de produção para o Azure. No caso de não mover todos os sistemas de produção para o Azure no mesmo período, crie grupos de sistemas de produção que precisam estar na mesma localização de hospedagem. Exercite e teste a migração de dados. Lista de métodos comuns, como:
    1.  Usar métodos do DBMS como backup/restauração em combinação com o SQL Server Always On, Replicação de Sistema HANA ou envio de log para propagar e sincronizar o conteúdo do banco de dados no Azure
    2.  Usar Backup/restauração para bancos de dados menores
    3.  Usar O Monitor de Migração SAP implementado na ferramenta de SWPM da SAP para executar migrações heterogêneas
    4.  Usar o processo [DMO SAP](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se você precisar combinar com uma atualização de versão do SAP. Saiba que nem todas as combinações entre DBMS de origem e destino têm suporte. Mais informações podem ser encontradas nas notas de suporte SAP específicas para as diferentes versões do DMO. Por exemplo, [DMO (Opção de Migração de Banco de Dados) de SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Teste se a transferência de dados pela Internet ou pelo ExpressRoute é melhor na taxa de transferência caso você precise mover backups ou arquivos de exportação SAP. No caso de mover dados pela Internet, talvez seja necessário alterar algumas de suas regras de segurança NSG/ASG que você precisa ter em vigor para futuros sistemas de produção
3.  Antes de mover sistemas da plataforma antigos para o Azure, colete dados de consumo de recursos, como o uso da CPU, taxa de transferência de armazenamento e dados IOPS. Especialmente das unidades de camada DBMS, mas também das unidades da camada de aplicativo. Também meça a latência de rede e de armazenamento.
4.  Valide os recursos nas notas de suporte do SAP, SAP HANA diretório de hardware e o PAM do SAP novamente para certificar-se de que não houvesse nenhuma alteração nas VMs com suporte para o Azure, as versões de sistema operacional com suporte nessas VMs e as versões de SAP e DBMS com suporte 
4.  Adaptar scripts de implantação às alterações mais recentes que você decidiu sobre tipos de VM e funcionalidade do Azure
5.  Após a implantação da infraestrutura e do aplicativo passar por uma série de verificações para validar:
    1.  Se os tipos de VM corretos foram implantados com os atributos e os tamanhos de armazenamento corretos
    2.  Verificar se as VMs estão nas versões de sistema operacional corretas e desejadas e se os patches são uniformes
    3.  Verificar se a proteção das VMs está conforme necessário e uniforme
    4.  Verificar se os patches e as versões de aplicativo corretos foi instalados e implantados
    5.  As VMs foram implantadas em Conjuntos de Disponibilidade do Azure conforme planejado
    6.  Armazenamento Premium do Azure foi usado para os discos sensíveis à latência ou quando o [SLA de VM única de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) é necessário
    7.  Verificar a implantação correta do Acelerador de Gravação do Azure
        1.  Verificar se a VM, os espaços de armazenamento ou conjuntos de distribuição foram criados corretamente entre os discos que precisam de suporte do Acelerador de Gravação do Azure
            1.  Verifique [Configurar RAID de software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Verifique [Configurar o LVM em uma VM Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  [Discos gerenciados do Azure](https://azure.microsoft.com/services/managed-disks/) foram usados exclusivamente
    9.  VMs foram implantadas em Zonas de Disponibilidade e Conjuntos de Disponibilidade corretos
    10. Verificar se a [Rede Acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitada nas VMs usadas na camada do aplicativo SAP e na camada do SAP DBMS
    11. Não posicionamento de [Dispositivos de Rede Virtual do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou sistemas SAP baseado em S/4HANA
    12. Regras de ASG e NSG permitem a comunicação como desejado e planejado e bloqueiam a comunicação quando necessário
    13. Configurações de tempo limite conforme descritas anteriormente foram definidas corretamente
    14. Certifique-se de que as VMs sejam implantadas usando o grupo de posicionamento correto de [proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , conforme descrito no artigo [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md)
    15. Teste e avalie a latência de rede entre a VM da camada de aplicativo SAP e a VM do DBMS de acordo com a nota de suporte SAP [nº 500235](https://launchpad.support.sap.com/#/notes/500235) e a nota de suporte SAP [nº 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados em relação à orientação de latência de rede da observação de suporte SAP [nº 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência de rede deve estar no intervalo moderado e bom. Exceções aplicam-se a o tráfego entre VMs e unidades de Instância Grande do HANA conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Verificar se a criptografia foi implantada quando necessário e com o método de criptografia necessário
    16. Verificar se interfaces e outros aplicativos podem se conectar à infraestrutura implantada recentemente
6.  Crie um guia estratégico para reagir à manutenção planejada do Azure. Defina a ordem dos sistemas e as VMs a serem reinicializadas no caso de manutenção planejada
    

## <a name="go-live-phase"></a>Fase de ativação
Para a fase de Ativação, você precisa seguir os guias estratégicos desenvolvidos nas fases anteriores. Execute as etapas que você testou e treinou. Não aceite as alterações de última hora a configurações e processos. Além disso, aplique as seguintes medidas:

1. Verifique se o monitoramento do portal do Azure e outras ferramentas de monitoramento estão funcionando.  As ferramentas recomendadas são Perfmon (Windows) ou SAR (Linux): 
    1.  Contadores de CPU 
        1.  Tempo médio de CPU – Total (toda a CPU)
        2.  Tempo médio de CPU – cada processador individual (assim, 128 processadores na VM m128)
        3.  Kernel de tempo de CPU – cada processador individual
        4.  Usuário de tempo de CPU – cada processador individual
    5.  Memória 
        1.  Memória livre
        2.  Entrada de Página de Memória/s
        3.  Saída de Página de Memória/s
    4.  Disco 
        1.  Leitura de disco em KB/s – por disco individual 
        2.  Leituras de disco/s – por disco individual
        3.  Leitura de disco em ms/leitura – por disco individual
        4.  Gravação de disco em KB/s – por disco individual 
        5.  Gravação de disco/s – por disco individual
        6.  Gravação de disco em ms/leitura – por disco individual
    5.  Rede 
        1.  Entrada de pacotes de rede/s
        2.  Saída de pacotes de rede/s
        3.  Entrada de rede em KB/s
        4.  Saída de rede em KB/s 
2.  Após a migração dos dados, execute todos os testes de validação acordado com os proprietários do negócio. Somente aceitar resultados de teste de validação em que você tenha os resultados para os sistemas de origem originais
3.  Verificar se as interfaces estão funcionando e se outros aplicativos podem se comunicar com os sistemas de produção implantados recentemente
4.  Verificar o sistema de transporte e de correção por meio do STMS da transação SAP
5.  Executar backups de Banco de Dados depois que o sistema for liberado para produção
6.  Executar backups de VM para as VMs da camada de aplicativo SAP depois que o sistema for liberado para produção
7.  Para sistemas SAP que não fazem parte da atual fase de ativação, mas se comunicam com sistemas SAP que você moveu para o Azure nesta fase de ativação, você precisará redefinir o buffer do nome de host em SM51. Esta etapa descartará os antigos endereços IP em cache associados aos nomes das instâncias do aplicativo que você moveu para o Azure  


## <a name="post-production"></a>Após a produção
Nesta fase, é tudo uma questão de monitorar, operar e administrar o sistema. De um ponto de vista do SAP, as tarefas normais que você precisava realizar em sua antiga localização de hospedagem se aplicam. Tarefas específicas do Azure que você deseja executar são:

1. Analisar faturas do Azure para sistemas de cobrança alta
2. Otimizar a eficiência de preço/desempenho no lado da VM e do armazenamento
3. Otimizar o tempo em que os sistemas podem ficar desligados  


## <a name="next-steps"></a>Próximas etapas
Consulte a documentação:

- [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

