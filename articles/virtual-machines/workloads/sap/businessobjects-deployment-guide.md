---
title: Implantação da plataforma de BI do SAP BusinessObjects no Azure | Microsoft Docs
description: Planejar, implantar e configurar a plataforma de BI SAP BusinessObjects no Azure
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 3b3c313df5704e49481c66ad682faccd48d180ea
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505895"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Guia de planejamento e implementação da plataforma SAP BusinessObjects BI no Azure

## <a name="overview"></a>Visão geral

A finalidade deste guia é fornecer diretrizes para planejamento, implantação e configuração da plataforma de BI SAP BusinessObjects, também conhecida como plataforma SAP BOBI no Azure. Este guia destina-se a abranger serviços e recursos comuns do Azure que são relevantes para a plataforma SAP BOBI. Este guia não é uma lista completa de todas as opções de configuração possíveis. Ele aborda soluções comuns a cenários típicos de implantação.

Este guia não pretende substituir os guias padrão de instalação e administração da plataforma SAP BOBI, sistema operacional ou qualquer documentação de banco de dados.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Planejar e implementar a plataforma de BI SAP BusinessObjects no Azure

O Microsoft Azure oferece uma ampla gama de serviços, incluindo computação, armazenamento, rede e muitos outros para que as empresas criem seus aplicativos sem ciclos de aquisição longos. As VMs (máquinas virtuais) do Azure ajudam as empresas a implantar recursos de computação sob demanda e escalonáveis para diferentes aplicativos SAP, como aplicativos baseados no SAP NetWeaver, SAP Hybris, SAP BusinessObjects BI Platform, com base em sua necessidade de negócios. O Azure também dá suporte à conectividade entre locais, que permite que as empresas integrem máquinas virtuais do Azure em seus domínios locais, em suas nuvens privadas e em seu cenário de sistema SAP.

Este documento fornece orientação sobre planejamento e implementação de considerações sobre a plataforma de BI do SAP BusinessObjects no Azure. Ele complementa a documentação de instalação do SAP e as notas SAP, que representam os principais recursos para instalações e implantações do SAP BOBI.

### <a name="architecture-overview"></a>Visão geral da arquitetura

A plataforma de BI SAP BusinessObjects é um sistema independente que pode existir em uma única máquina virtual do Azure ou pode ser dimensionada em um cluster de muitas máquinas virtuais do Azure que executam componentes diferentes. A plataforma SAP BOBI consiste em seis camadas conceituais: camada de cliente, camada da Web, camada de gerenciamento, camada de armazenamento, camada de processamento e camada de dados. (Para obter mais detalhes sobre cada camada, consulte o guia do administrador no portal de ajuda da [plataforma de Business Intelligence do SAP BusinessObjects](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) ). Veja a seguir os detalhes de alto nível em cada camada:

- **Camada do cliente:** Ele contém todos os aplicativos cliente de desktop que interagem com a plataforma de BI para fornecer tipos diferentes de relatórios, de análise e de recursos administrativos.
- **Camada da Web:** Ele contém aplicativos Web implantados em servidores de aplicativos Web JAVA. Os aplicativos Web fornecem funcionalidade de plataforma de BI para usuários finais por meio de um navegador da Web.
- **Camada de gerenciamento:** Ele coordena e controla todos os componentes que fazem a plataforma de BI. Ele inclui o CMS (servidor de gerenciamento central) e o servidor de eventos e os serviços associados
- **Camada de armazenamento:** Ele é responsável por manipular arquivos, como documentos e relatórios. Ele também manipula o cache de relatórios para salvar os recursos do sistema quando os relatórios de acesso do usuário.
- **Camada de processamento:** Ele analisa os dados e produz relatórios e outros tipos de saída. É a única camada que acessa os bancos de dados do que contêm os dados do relatório.
- **Camada de dados:** Ele consiste nos servidores de banco de dados que hospedam os bancos de dados do sistema CMS e a auditoria do armazenamento.

A plataforma SAP BI consiste em uma coleção de servidores em execução em um ou mais hosts. É essencial que você escolha a estratégia de implantação correta com base no dimensionamento, na necessidade de negócios e no tipo de ambiente. Para uma instalação pequena, como desenvolvimento ou teste, você pode usar uma única máquina virtual do Azure para servidor de aplicativos da Web, servidor de banco de dados e todos os servidores da plataforma de BI. Caso você esteja usando a oferta de DBaaS (banco de dados como serviço) do Azure, o servidor de banco de dados será executado separadamente de outros componentes. Para uma instalação média e grande, você pode ter servidores em execução em várias máquinas virtuais do Azure.

Na figura abaixo, a arquitetura de implantação em grande escala da plataforma SAP BOBI em máquinas virtuais do Azure é mostrada, onde cada componente é distribuído e colocado em conjuntos de disponibilidade que podem sustentar o failover se houver interrupção do serviço.

![Arquitetura da plataforma de BI SAP BusinessObjects no Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Detalhes da arquitetura

- Balanceador de carga

  Na implantação de várias instâncias do SAP BOBI, os servidores de aplicativos Web (ou camada da Web) estão em execução em dois ou mais hosts. Para distribuir a carga de usuário uniformemente entre servidores Web, você pode usar um balanceador de carga entre usuários finais e servidores Web. No Azure, você pode usar [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) ou [aplicativo Azure gateway](../../../application-gateway/overview.md) para gerenciar o tráfego para seus servidores Web.

- Servidores de aplicativos Web

  O servidor Web hospeda os aplicativos Web da SAP BOBI Platform, como o CMC e o BI Launch Pad. Para obter alta disponibilidade para o servidor Web, você deve implantar pelo menos dois servidores de aplicativos Web para gerenciar a redundância e o balanceamento de carga. No Azure, esses servidores de aplicativos da Web podem ser colocados em conjuntos de disponibilidade ou zonas de disponibilidade para melhorar a disponibilidade.

  Tomcat é o aplicativo Web padrão para a plataforma SAP BI. Para obter alta disponibilidade para o Tomcat, habilite a replicação de sessão usando o [interceptador de associação estática](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) no Azure. Ele garante que o usuário possa acessar o aplicativo Web do SAP BI mesmo quando o serviço Tomcat for interrompido.

  > [!Important]
  > Por padrão, o Tomcat usa IP e porta de multicast para clustering que não tem suporte no Azure (observação SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- Servidores da plataforma de BI

  Os servidores da plataforma de BI incluem todos os serviços que fazem parte do aplicativo SAP BOBI (camada de gerenciamento, camada de processamento e camada de armazenamento). Quando um servidor Web recebe uma solicitação, ele detecta cada servidor da plataforma de BI (especificamente, todos os servidores CMS em um cluster) e equilibra automaticamente suas solicitações. Caso um dos hosts da plataforma de BI falhe, o servidor Web envia automaticamente solicitações para outro host.

  Para obter alta disponibilidade ou redundância para a plataforma de BI, você deve implantar o aplicativo em pelo menos duas máquinas virtuais do Azure. Com base no dimensionamento, você pode dimensionar sua plataforma de BI para ser executada em mais máquinas virtuais do Azure.

- Servidor de repositório de arquivos (FRS)

  O servidor de repositório de arquivos contém todos os relatórios e outros documentos de BI que foram criados. Na implantação de várias instâncias, os servidores da plataforma de BI são executados em várias máquinas virtuais e cada VM deve ter acesso a esses relatórios e a outros documentos de BI. Portanto, um sistema de arquivos precisa ser compartilhado entre todos os servidores da plataforma de BI.

  No Azure, você pode usar [arquivos premium do Azure](../../../storage/files/storage-files-introduction.md) ou [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) para o servidor de repositório de arquivos. Ambos os serviços do Azure têm redundância interna.

  > [!Important]
  > O protocolo SMB para arquivos do Azure está em disponibilidade geral, mas o suporte do protocolo NFS para arquivos do Azure está atualmente em versão prévia. Para obter mais informações, consulte o [suporte do NFS 4,1 para arquivos do Azure está agora em visualização](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- Banco de dados de auditoria & CMS
  
  A plataforma SAP BOBI exige que um banco de dados armazene seu dado do sistema, que é conhecido como banco de dados CMS. Ele é usado para armazenar informações da plataforma de BI, como os detalhes de usuário, servidor, pasta, documento, configuração e autenticação.

  O Azure oferece banco de dados [MySQL](https://azure.microsoft.com/en-us/services/mysql/) e oferta DBaaS (banco de dados como serviço) do banco de dados [SQL do Azure](https://azure.microsoft.com/en-us/services/sql-database/) que pode ser usada para banco de dados CMS e banco de dados de auditoria. Como se trata de uma oferta de PaaS, os clientes não precisam se preocupar com a operação, a disponibilidade e a manutenção dos bancos de dados. O cliente também pode escolher seu próprio banco de dados para o repositório de CMS e de auditoria com base em suas necessidades comerciais.

## <a name="support-matrix"></a>Matriz de suporte

Esta seção descreve a compatibilidade de diferentes componentes do SAP BOBI, como a versão da plataforma de BI do SAP BusinessObjects, o sistema operacional e os bancos de dados no Azure.

### <a name="sap-businessobjects-bi-platform"></a>Plataforma de BI SAP BusinessObjects

O IaaS (infraestrutura como serviço) do Azure permite implantar e configurar a plataforma de BI SAP BusinessObjects na computação do Azure. Ele dá suporte à seguinte versão da plataforma SAP BOBI-

- Plataforma de BI SAP BusinessObjects 4,3
- Plataforma de BI SAP BusinessObjects 4,2 SP04 +
- Plataforma de BI SAP BusinessObjects 4,1 SP05 +

A plataforma SAP BI é executada em diferentes sistemas operacionais e bancos de dados. A compatibilidade da plataforma SAP BOBI entre o sistema operacional e a versão do banco de dados pode ser encontrada na [matriz de disponibilidade do produto](https://apps.support.sap.com/sap/support/pam) para SAP BOBI.

### <a name="operating-system"></a>Sistema operacional

O Azure dá suporte aos seguintes sistemas operacionais para implantação de plataforma de BI do SAP BusinessObjects.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

A versão do sistema operacional listada em [matriz de disponibilidade de produto (PAM) para plataforma de BI do SAP BusinessObjects](https://support.sap.com/pam) tem suporte desde que seja compatível para execução na infraestrutura do Azure.

### <a name="databases"></a>Bancos de dados

A plataforma de BI precisa do banco de dados para o depósito e a auditoria de armazenamento de dados, que podem ser instalados em qualquer banco de dado com suporte listado na [matriz de disponibilidade de produto SAP](https://support.sap.com/pam) que inclui o seguinte-

- Microsoft SQL Server

- [Banco de dados SQL do Azure](https://azure.microsoft.com/en-us/services/sql-database/) (banco de dados com suporte somente para plataforma SAP BOBI no Windows)

  É um mecanismo de banco de dados SQL Server totalmente gerenciado, com base na mais recente edição de empresa estável do SQL Server. O banco de dados SQL do Azure lida com a maioria das funções de gerenciamento de banco de dados, como atualização, aplicação de patches e monitoramento sem envolvimento do usuário. Com o Banco de Dados SQL do Azure, você pode criar uma camada de armazenamento de dados altamente disponível e de alto desempenho para aplicativos e soluções no Azure. Para obter mais detalhes, consulte a documentação [do banco de dados SQL do Azure](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) .

- [Banco de dados do Azure para MySQL](https://azure.microsoft.com/en-us/services/mysql/) (siga as mesmas diretrizes de compatibilidade mencionadas para o MySQL AB no SAP Pam)

  É um serviço de banco de dados relacional da plataforma MySQL Community Edition. Sendo uma oferta de DBaaS (banco de dados como serviço) totalmente gerenciada, ela pode lidar com cargas de trabalho críticas com desempenho previsível e escalabilidade dinâmica. Ele tem alta disponibilidade interna, backups automáticos, aplicação de patches de software, detecção automática de falhas e restauração pontual por até 35 dias, o que reduz substancialmente as tarefas de operação. Para obter mais detalhes, consulte a documentação do [banco de dados do Azure para MySQL](../../../mysql/overview.md) .

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (para obter a versão e a restrição, verifique o SAP Note [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

Este documento ilustra as diretrizes para implantar a **plataforma SAP BOBI no Windows com o banco de dados SQL do Azure e a** **plataforma SAP BOBI no Linux com o banco de dados do Azure para MySQL**. Também é nossa abordagem recomendada para executar a plataforma de BI SAP BusinessObjects no Azure.

## <a name="sizing"></a>Dimensionamento

O dimensionamento é um processo de determinação do requisito de hardware para executar o aplicativo com eficiência. Para a plataforma SAP BOBI, o dimensionamento precisa ser feito usando a ferramenta de dimensionamento SAP chamada de dimensionador [rápido](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer). A ferramenta fornece os SAPS com base na entrada, que precisa ser mapeada para tipos de máquinas virtuais do Azure certificados para SAP. A observação da SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) fornece a lista de produtos SAP com suporte e tipos de VM do Azure, juntamente com SAPS. Para obter mais informações sobre o dimensionamento, consulte [Guia de dimensionamento do SAP BI](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

Para a necessidade de armazenamento da plataforma SAP BOBI, o Azure oferece tipos diferentes de [Managed disks](../../managed-disks-overview.md). Para o diretório de instalação do SAP BOBI, é recomendável usar o disco gerenciado Premium e o banco de dados que é executado em máquinas virtuais, siga as orientações fornecidas na [implantação do DBMS para carga de trabalho do SAP](dbms_guide_general.md).

O Azure dá suporte a duas ofertas de DBaaS para a camada de dados da plataforma SAP BOBI – banco de dados [SQL do Azure](https://azure.microsoft.com/en-us/services/sql-database) (aplicativo de BI em execução no Windows) e [banco de dado do Azure para MySQL](https://azure.microsoft.com/en-us/services/mysql) (aplicativo de BI em execução no Linux e no Windows Com base no resultado de dimensionamento, você pode escolher o modelo de compra que melhor se adapta à sua necessidade.

> [!Tip]
> Para referência rápida de dimensionamento, considere 800 SAPS = 1 vCPU ao mapear o resultado de SAPS da camada de banco de dados da plataforma SAP BOBI para o banco de dados como serviço do Azure (banco de dados SQL do Azure ou banco de dados do Azure para MySQL).

### <a name="sizing-models-for-azure-sql-database"></a>Dimensionando modelos para o banco de dados SQL do Azure

O banco de dados SQL do Azure oferece os três modelos de compra a seguir:

- baseado em vCore

  Ele permite que você escolha o número de vCores, quantidade de memória e a quantidade e velocidade de armazenamento. O modelo de compra baseado em vCore também permite que você use o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para poupar custos. Esse modelo é adequado para clientes que têm flexibilidade de valor, controle e transparência.

  Há três [Opções de camada de serviço](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) sendo oferecidas no modelo vCore que incluem-Uso Geral, comercialmente crítico e hiperescala. A camada de serviço define a arquitetura de armazenamento, o espaço, os limites de e/s e as opções de continuidade de negócios relacionadas à disponibilidade e à recuperação de desastres. A seguir estão os detalhes de alto nível sobre cada opção de camada de serviço-

  1. **Uso geral** camada de serviço é mais adequada para cargas de trabalho de negócios. Ele oferece opções de armazenamento e de computação voltadas para o orçamento, equilibradas e escalonáveis. Para obter mais informações, consulte [Opções e limites de recurso](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5).
  2. **Comercialmente crítico** camada de serviço oferece aos aplicativos de negócios a resiliência mais alta para falhas usando várias réplicas isoladas e fornece o desempenho de e/s mais alto por réplica de banco de dados. Para obter mais informações, consulte [Opções e limites de recurso](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5).
  3. A camada de serviço de **hiperescala** é melhor para cargas de trabalho de negócios com requisitos de armazenamento e escala de leitura altamente escalonáveis. Ele oferece maior resiliência a falhas, permitindo a configuração de mais de uma réplica de banco de dados isolada. Para obter mais informações, consulte [Opções e limites de recurso](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

- Baseado em DTU

  O modelo de compra baseado em DTU oferece uma mistura de computação, memória e recursos de e/s em três camadas de serviço, para dar suporte a cargas de trabalho leves e pesadas de banco de dados. Os tamanhos da computação dentro de cada camada fornecem uma mistura diferente desses recursos, aos quais você pode adicionar recursos de armazenamento. Ele é mais adequado para clientes que desejam opções de recursos simples e pré-configuradas.

  As [camadas de serviço](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) no modelo de compra baseado em DTU são diferenciadas por um intervalo de tamanhos de computação com uma quantidade fixa de armazenamento incluído, o período de retenção fixo de backups e o preço fixo.

- Sem servidor

  O modelo sem servidor dimensiona automaticamente a computação com base na demanda de carga de trabalho e cobra pela quantidade de computação usada por segundo. A camada de computação sem servidor pausa automaticamente os bancos de dados durante períodos inativos quando apenas o armazenamento é cobrado e retoma automaticamente os bancos de dados quando a atividade retorna. Para obter mais informações, consulte [Opções e limites de recurso](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
  
  É mais adequado para uso intermitente e imprevisível com baixa utilização média de computação ao longo do tempo. Portanto, esse modelo pode ser usado para implantação de SAP BOBI de não produção.

> [!Note]
> Para o SAP BOBI, é conveniente usar o modelo baseado em vCore e escolher Uso Geral ou Comercialmente Crítico camada de serviço com base na necessidade de negócios.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Dimensionando modelos para o banco de dados do Azure para MySQL

O banco de dados do Azure para MySQL vem com três tipos de preço diferentes. Eles são diferenciados pela quantidade de computação em vCores, memória por vCore e a tecnologia de armazenamento usada para armazenar a data. Veja a seguir os detalhes de alto nível sobre as opções e para obter mais detalhes sobre atributos diferentes, consulte [camada de preços](../../../mysql/concepts-pricing-tiers.md) do banco de dados do Azure para MySQL.

- Basic

  Ele é usado para as cargas de trabalho de destino que exigem desempenho de e/s e computação leve.

- Uso Geral

  É adequado para a maioria das cargas de trabalho de negócios que exigem computação e memória balanceadas com taxa de transferência de e/s escalonável.

- Otimizado para memória

  Para cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para processamento mais rápido de transações e simultaneidade mais alta.

> [!Note]
> Para o SAP BOBI, é conveniente usar o tipo de preço Uso Geral ou com otimização de memória com base na carga de trabalho de negócios.

## <a name="azure-resources"></a>Recursos do Azure

### <a name="choosing-regions"></a>Escolhendo regiões

A região do Azure é uma coleção de data centers que contém a infraestrutura para executar e hospedar diferentes serviços do Azure. Essa infraestrutura inclui um grande número de nós que funcionam como nós de computação ou nós de armazenamento ou que executam a funcionalidade de rede. Nem toda a região oferece os mesmos serviços.

A plataforma SAP BI contém componentes diferentes que podem exigir tipos específicos de VM, armazenamento, como arquivos do Azure ou Azure NetApp Files ou DBaaS (banco de dados como serviço) para sua camada que pode não estar disponível em determinadas regiões. Você pode encontrar as informações exatas sobre tipos de VM, tipos de armazenamento do Azure ou outros serviços do Azure em [produtos disponíveis por site de região](https://azure.microsoft.com/en-us/global-infrastructure/services/) . Se você já estiver executando seus sistemas SAP no Azure, provavelmente terá sua região identificada. Nesse caso, você precisa primeiro investigar que os serviços necessários estão disponíveis nessas regiões para decidir a arquitetura da plataforma SAP BI.

### <a name="availability-zones"></a>Zonas de disponibilidade

As Zonas de disponibilidade são locais separados fisicamente dentro de uma região do Azure. Cada zona de disponibilidade é composta por um ou mais data centers equipados com energia, resfriamento e rede independentes.

Para obter alta disponibilidade em cada camada para a plataforma SAP BI, você pode distribuir VMs na zona de disponibilidade implementando a estrutura de alta disponibilidade, que pode fornecer o melhor SLA no Azure. Para SLA de máquina virtual no Azure, verifique a versão mais recente dos [SLAs de máquina virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Para a camada de dados, o serviço DBaaS (Azure Database as Service) fornece a estrutura de alta disponibilidade por padrão. Basta selecionar a região e o serviço inerentes recursos de alta disponibilidade, redundância e resiliência para reduzir o tempo de inatividade do banco de dados de interrupções planejadas e não planejadas, sem a necessidade de configurar nenhum componente adicional. Para obter mais detalhes sobre o SLA para oferta de DBaaS com suporte no Azure, verifique a [alta disponibilidade no banco de dados do Azure para MySQL](../../../mysql/concepts-high-availability.md) e [alta disponibilidade para o banco de dados SQL do Azure](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Conjuntos de disponibilidade

O conjunto de disponibilidade é um recurso de agrupamento lógico para isolar recursos de VM (máquina virtual) uns dos outros na implantação. O Azure garante que as VMs colocadas em um conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de hardware ou de software, apenas um subconjunto de suas VMs será afetado e sua solução geral permanecerá operacional. Assim, quando as máquinas virtuais são colocadas em conjuntos de disponibilidade, o controlador de malha do Azure distribui as VMs em diferentes domínios de [falha](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) e de [atualização](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) para impedir que todas as VMs fiquem inacessíveis devido à manutenção da infraestrutura ou falha em um domínio de falha.

A plataforma SAP BI contém muitos componentes diferentes e, ao criar a arquitetura, você tem de garantir que cada um desses componentes seja resiliente a qualquer interrupção. Ele pode ser obtido colocando-se máquinas virtuais do Azure de cada componente nos conjuntos de disponibilidade. Tenha em mente que, ao misturar VMs de diferentes famílias de VM dentro de um conjunto de disponibilidade, você pode ter problemas que impedem que você inclua um determinado tipo de VM nesse conjunto de disponibilidade. Portanto, tenha um conjunto de disponibilidade separado para aplicativo Web, aplicativo de BI para plataforma SAP BI como destacado em visão geral da arquitetura.

Além disso, o número de domínios de atualização e de falha que podem ser usados por um conjunto de disponibilidade do Azure em uma unidade de escala do Azure é finito. Portanto, se você continuar adicionando VMs a um único conjunto de disponibilidade, duas ou mais VMs eventualmente terminarão no mesmo domínio de falha ou de atualização. Para obter mais informações, consulte a seção [conjuntos de disponibilidade do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) do documento planejamento e implementação de máquinas virtuais do Azure para SAP.

Para entender o conceito de conjuntos de disponibilidade do Azure e a maneira como os conjuntos de disponibilidade se relacionam a domínios de falha e atualização, leia o artigo [gerenciar disponibilidade](../../availability.md) .

> [!Important]
> Os conceitos de Zonas de Disponibilidade do Azure e conjuntos de disponibilidade do Azure são mutuamente exclusivos. Isso significa que você pode implantar um par ou várias VMs em uma Zona de Disponibilidade específica ou em um conjunto de disponibilidade do Azure. Mas não em ambos.

### <a name="virtual-machines"></a>Máquinas virtuais

A máquina virtual do Azure é uma oferta de serviço que permite que você implante imagens personalizadas no Azure como instâncias de IaaS (infraestrutura como serviço). Isso simplifica a manutenção e operação de aplicativos, fornecendo computação sob demanda e armazenamento para hospedar, dimensionar e gerenciar aplicativos Web e aplicativos conectados.

O Azure oferece variedades de máquinas virtuais para todas as necessidades de seu aplicativo. Mas, para a carga de trabalho do SAP, o Azure limitou a seleção para diferentes famílias de VM adequadas para carga de trabalho do SAP e SAP HANA carga de trabalho mais especificamente. Para obter mais informações, verifique [quais softwares SAP têm suporte para implantações do Azure](sap-supported-product-on-azure.md).

Com base no dimensionamento da plataforma SAP BI, você precisa mapear seu requisito para a máquina virtual do Azure, que tem suporte no Azure para o produto SAP. A observação SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) é um bom ponto de partida que lista os tipos de VM do Azure com suporte para produtos SAP no Windows e no Linux. Também é preciso ter em mente que, além da seleção de tipos de VM com suporte puramente, você também precisa verificar se esses tipos de VM estão disponíveis em uma região específica. Você pode verificar a disponibilidade do tipo de VM na página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) . Para escolher o modelo de preços, você pode consultar [máquinas virtuais do Azure para carga de trabalho do SAP](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>Armazenamento

O armazenamento do Azure é um serviço de nuvem gerenciado pelo Azure que fornece armazenamento altamente disponível, seguro, durável, escalonável e redundante. Alguns dos tipos de armazenamento têm uso limitado para cenários SAP. Mas vários tipos de armazenamento do Azure são bem adequados ou otimizados para cenários de carga de trabalho SAP específicos. Para obter mais informações, consulte os [tipos de armazenamento do Azure para guia de carga de trabalho do SAP](planning-guide-storage.md) , pois ele realça opções de armazenamento diferentes que são adequadas para o SAP.

O armazenamento do Azure tem tipos de armazenamento diferentes disponíveis para clientes e detalhes para o mesmo pode ser lido no artigo [quais tipos de disco estão disponíveis no Azure?](../../disks-types.md). A plataforma SAP BOBI usa o armazenamento do Azure a seguir para criar o aplicativo-

- Discos gerenciados pelo Azure

  É um volume de armazenamento de nível de bloco gerenciado pelo Azure. Você pode usar os discos para servidores de aplicativos da plataforma SAP BOBI e bancos de dados, quando instalados em máquinas virtuais do Azure. Há diferentes tipos de [Managed disks do Azure](../../managed-disks-overview.md) disponíveis, mas é recomendável usar o [SSDs Premium](../../disks-types.md#premium-ssd) para o aplicativo e o banco de dados da plataforma SAP BOBI.

  No exemplo abaixo, o SSDs Premium é usado para o diretório de instalação da plataforma BOBI. Para o banco de dados instalado na máquina virtual, você pode usar o Managed disks para volumes de log e de logs de acordo com as diretrizes. Bancos de dados de auditoria e CMS são geralmente pequenos e não têm os mesmos requisitos de desempenho de armazenamento que os de outros bancos de dados OLTP/OLAP do SAP.

- Arquivos premium do Azure ou Azure NetApp Files

  Na plataforma SAP BOBI, o servidor de repositório de arquivos (FRS) refere-se aos diretórios de disco em que o conteúdo como relatórios, universos e conexões são armazenados, que são usados por todos os servidores de aplicativos desse sistema. [Os arquivos premium do Azure](../../../storage/files/storage-files-introduction.md) ou o armazenamento de [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) podem ser usados como um sistema de arquivos compartilhado para o FRS de aplicativos SAP BOBI. Como essa oferta de armazenamento não está disponível em todas as regiões, consulte [produtos disponíveis por](https://azure.microsoft.com/en-us/global-infrastructure/services/) site de região para encontrar informações atualizadas.

  Se o serviço não estiver disponível em sua região, você poderá criar um servidor NFS do qual você pode compartilhar o sistema de arquivos para o aplicativo SAP BOBI. Mas você também precisará considerar sua alta disponibilidade.

![Layout de armazenamento da plataforma de BI SAP BusinessObjects no Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Rede

O SAP BOBI é uma plataforma de BI de relatório e análise que não armazena dados corporativos. Portanto, o sistema está conectado a outros servidores de banco de dados, de onde ele busca todos eles e fornece informações aos usuários. O Azure fornece uma infraestrutura de rede, que permite o mapeamento de todos os cenários que podem ser percebidos com a plataforma SAP BI, como conectar-se ao sistema local, sistemas em diferentes redes virtuais e outros. Para obter mais informações, verifique [Microsoft Azure rede para carga de trabalho do SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

Para uma oferta de banco de dados como serviço, qualquer banco de dados criado recentemente (banco de dados SQL do Azure ou banco de dados do Azure para MySQL) tem um firewall que bloqueia todas as conexões externas. Para permitir o acesso ao serviço DBaaS de máquinas virtuais da plataforma de BI, você precisa especificar uma ou mais regras de firewall no nível de servidor para habilitar o acesso ao servidor DBaaS. Para obter mais informações, consulte a seção [regras de firewall](../../../mysql/concepts-firewall-rules.md) para o banco de dados do Azure para MySQL e [controles de acesso à rede](../../../azure-sql/database/network-access-controls-overview.md) do banco de dados SQL do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Implantação da plataforma de BI do SAP BusinessObjects no Linux](businessobjects-deployment-guide-linux.md)
- [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP](planning-guide.md)
- [Implantação de Máquinas Virtuais do Azure para SAP](deployment-guide.md)
- [Implantação do DBMS de Máquinas Virtuais do Azure para SAP](./dbms_guide_general.md)