---
title: Sobre a recuperação de desastre para aplicativos locais com Azure Site Recovery
description: Descreve as cargas de trabalho que podem ser protegidas usando a recuperação de desastres com o serviço do Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80062832"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Sobre a recuperação de desastre para aplicativos locais

Este artigo descreve os aplicativos e as cargas de trabalho locais que você pode proteger para a recuperação de desastres com o serviço [Azure site Recovery](site-recovery-overview.md) .

## <a name="overview"></a>Visão geral

As organizações precisam de uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) para manter as cargas de trabalho e os dados seguros e disponíveis durante o tempo de inatividade planejado e não planejado. E, recuperar em condições de trabalho regulares.

O Site Recovery é um serviço do Azure que contribui para sua estratégia de BCDR. Usando o Site Recovery, você pode implantar a replicação com reconhecimento de aplicativo na nuvem, ou para um site secundário. Você pode usar Site Recovery para gerenciar a replicação, executar testes de recuperação de desastres e executar failovers e failback. Seus aplicativos podem ser executados em computadores baseados em Windows ou Linux, servidores físicos, VMware ou Hyper-V.

O Site Recovery integra-se com aplicativos da Microsoft, como SharePoint, Exchange, Dynamics, SQL Server e Active Directory. A Microsoft trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP e Red Hat. Você pode personalizar soluções de replicação em uma base por aplicativo.

## <a name="why-use-site-recovery-for-application-replication"></a>Por que usar o Site Recovery para a replicação do aplicativo?

O Site Recovery contribui para a recuperação e a proteção no nível do aplicativo da seguinte maneira:

- Independente de aplicativo e fornece replicação para qualquer carga de trabalho em execução em um computador com suporte.
- Replicação quase síncrona, com RPO (objetivos de ponto de recuperação) tão baixo quanto 30 segundos para atender às necessidades dos aplicativos de negócios mais importantes.
- Instantâneos consistentes de aplicativos para os aplicativos simples ou multicamadas.
- Integração com o SQL Server AlwaysOn e parceria com outras tecnologias de replicação em nível de aplicativo. Por exemplo, Active Directory replicação, SQL AlwaysOn e grupos de disponibilidade de banco de dados do Exchange (DAGs).
- Planos de recuperação flexíveis que permitem recuperar uma pilha inteira de aplicativos com um único clique e para incluir scripts externos e ações manuais no plano.
- Gerenciamento de rede avançado no Site Recovery e no Azure para simplificar os requisitos de rede do aplicativo. Gerenciamento de rede, como a capacidade de reservar endereços IP, configurar o balanceamento de carga e a integração com o Gerenciador de tráfego do Azure para comutadores de rede RTO (objetivos de tempo de recuperação) baixos.
- Uma biblioteca de automação avançada que fornece scripts específicos do aplicativo, prontos para a produção, que pode ser baixada e integrada nos planos de recuperação.

## <a name="workload-summary"></a>Resumo de carga de trabalho

O Site Recovery pode replicar qualquer aplicativo em execução em um computador com suporte. Fizemos uma parceria com as equipes de produto para fazer testes adicionais para os aplicativos especificados na tabela a seguir.

| **Carga de trabalho** |**Replicar VMs do Azure para o Azure** |**Replicar máquinas virtuais do Hyper-V para um site secundário** | **Replicar VMs do Hyper-V para o Azure** | **Replicar VMs do VMware para um site secundário** | **Replicar VMs do VMware para o Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Sim |Sim |Sim |Sim |Sim|
| Aplicativos Web (IIS, SQL) |Sim |Sim |Sim |Sim |Sim|
| System Center Operations Manager |Sim |Sim |Sim |Sim |Sim|
| SharePoint |Sim |Sim |Sim |Sim |Sim|
| SAP<br/><br/>Replicar site SAP para Azure para não cluster |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft)|
| Exchange (não DAG) |Sim |Sim |Sim |Sim |Sim|
| Área de Trabalho Remota/VDI |Sim |Sim |Sim |Sim |Sim|
| Linux (sistema operacional e aplicativos) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft) |Sim (testado pela Microsoft)|
| Dynamics AX |Sim |Sim |Sim |Sim |Sim|
| Servidor de arquivos do Windows |Sim |Sim |Sim |Sim |Sim|
| Citrix XenApp e XenDesktop |Sim|N/D |Sim |N/D |Sim |

## <a name="replicate-active-directory-and-dns"></a>Replicar o Active Directory e o DNS

As infraestruturas do DNS e do Active Directory são essenciais para a maioria dos aplicativos corporativos. Durante a recuperação de desastres, você precisará proteger e recuperar esses componentes de infraestrutura antes de recuperar cargas de trabalho e aplicativos.

Você usar o Site Recovery para criar um plano totalmente automatizado de recuperação de desastre para o Active Directory e o DNS. Por exemplo, para fazer failover do SharePoint e do SAP de um site primário para um secundário, você pode configurar um plano de recuperação que realiza o failover primeiro Active Directory. Em seguida, use um plano de recuperação adicional específico do aplicativo para fazer failover dos outros aplicativos que dependem de Active Directory.

[Saiba mais](site-recovery-active-directory.md) sobre a recuperação de desastres para Active Directory e DNS.

## <a name="protect-sql-server"></a>Proteger o SQL Server

O SQL Server fornece uma base de serviços de dados para muitos aplicativos de negócios em um datacenter local. Site Recovery pode ser usado com SQL Server tecnologias de HA/DR para proteger aplicativos empresariais de várias camadas que usam SQL Server.

A Recuperação de Site fornece:

- Uma solução de recuperação de desastre simples e econômica para o SQL Server. Replique diversas versões e edições dos clusters e servidores autônomos do SQL Server para o Azure ou um site secundário.
- Integração com Grupos de Disponibilidade SQL AlwaysOn para gerenciar o failover e o failback com os planos de recuperação do Azure Site Recovery.
- Planos de recuperação completos para todas as camadas em um aplicativo, incluindo os bancos de dados do SQL Server.
- Dimensionamento de SQL Server para cargas de pico com Site Recovery, _estourando_ -os em tamanhos maiores de máquina virtual de IaaS no Azure.
- Teste fácil de recuperação de desastres do SQL Server. Você pode executar failovers de teste para analisar os dados e executar verificações de conformidade sem afetar seu ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre a recuperação de desastres para o SQL Server.

## <a name="protect-sharepoint"></a>Proteger o SharePoint

O Azure Site Recovery ajuda a proteger as implantações do SharePoint, da seguinte forma:

- Elimina a necessidade e os custos associados à infraestrutura de um farm autônomo para a recuperação de desastres. Use Site Recovery para replicar um farm inteiro (camadas da Web, de aplicativo e de banco de dados) para o Azure ou para um site secundário.
- Simplifica a implantação e o gerenciamento de aplicativos. As atualizações implantadas no site primário são replicadas automaticamente. As atualizações estão disponíveis após o failover e a recuperação de um farm em um site secundário. Reduz a complexidade e os custos de gerenciamento associados a manter um farm autônomo atualizado.
- Simplifica o desenvolvimento de aplicativos do SharePoint e teste criando um ambiente de réplica sob demanda com cópia de produção para o teste e a depuração.
- Simplifica a transição para a nuvem usando o Site Recovery para migrar as implantações do SharePoint para o Azure.

[Saiba mais](site-recovery-sharepoint.md) sobre a recuperação de desastres para o SharePoint.

## <a name="protect-dynamics-ax"></a>Proteger o Dynamics AX

O Azure Site Recovery ajuda a proteger sua solução ERP do Dynamics AX:

- Gerenciar a replicação de todo o ambiente do Dynamics AX (camadas da Web e do AOS, camadas de banco de dados, SharePoint) para o Azure ou para um site secundário.
- Simplificando a migração das implantações do Dynamics AX para a nuvem (Azure).
- Simplificando o desenvolvimento de aplicativos Dynamics AX e teste criando uma cópia de produção sob demanda para o teste e a depuração.

[Saiba mais](site-recovery-dynamicsax.md) sobre a recuperação de desastres do Dynamic AX.

## <a name="protect-remote-desktop-services"></a>Proteger Serviços de Área de Trabalho Remota

O Serviços de Área de Trabalho Remota (RDS) permite o Virtual Desktop Infrastructure (VDI), áreas de trabalho baseadas em sessão e aplicativos, que permitem que os usuários trabalhem em qualquer lugar.

Com Azure Site Recovery você pode replicar os seguintes serviços:

- Replique áreas de trabalho virtuais em pool gerenciadas ou não gerenciadas em um site secundário.
- Replique aplicativos e sessões remotas para um site secundário ou o Azure.

A tabela a seguir mostra as opções de replicação:

| **RDS** |**Replicar VMs do Azure para o Azure** | **Replicar máquinas virtuais do Hyper-V para um site secundário** | **Replicar VMs do Hyper-V para o Azure** | **Replicar VMs do VMware para um site secundário** | **Replicar VMs do VMware para o Azure** | **Replicar servidores físicos para um site secundário** | **Replicar servidores físicos para o Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Área de trabalho virtual em pool (não gerenciado)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Área de trabalho virtual em pool (gerenciada e sem UPD)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Aplicativos remotos e sessões da área de trabalho (sem UDP)** |Sim|Sim |Sim |Sim |Sim |Sim |Sim |

[Saiba mais](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) sobre a recuperação de desastres para RDS.

## <a name="protect-exchange"></a>Proteger o Exchange

O Site Recovery ajuda a proteger o Exchange da seguinte maneira:

- Para implantações pequenas do Exchange, como um servidor único ou autônomo, o Site Recovery pode replicar e fazer failover no Azure ou em um site secundário.
- Para as implantações maiores, o Site Recovery integra-se ao Exchange DAGS.
- Os Exchange DAGs são a solução recomendada para recuperação de desastre do Exchange em uma empresa.  Os planos de recuperação do Site Recovery podem incluir DAGs para coordenar o failover do DAG nos sites.

Para saber mais sobre a recuperação de desastres do Exchange, consulte [Exchange DAGs](/Exchange/high-availability/database-availability-groups/database-availability-groups) e [recuperação de desastre do Exchange](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Proteger o SAP

Use o Site Recovery para proteger sua implantação do SAP como a seguir:

- Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production em execução no local, por meio da replicação de componentes no Azure.
- Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production em execução no Azure, por meio da replicação de componentes em outro datacenter do Azure.
- Simplifique a migração na nuvem usando o Site Recovery para migrar sua implantação do SAP para o Azure.
- Simplifique as atualizações, teste e criação de protótipos de projeto SAP criando um clone de produção sob demanda para testar aplicativos SAP.

[Saiba mais](site-recovery-sap.md) sobre a recuperação de desastres para SAP.

## <a name="protect-internet-information-services"></a>Proteger Serviços de Informações da Internet

Use Site Recovery para proteger sua implantação do Serviços de Informações da Internet (IIS), da seguinte maneira:

O Azure Site Recovery oferece recuperação de desastre replicando os componentes essenciais de seu ambiente em um site remoto frio ou em uma nuvem pública, como o Microsoft Azure. Como as máquinas virtuais com o servidor Web e o banco de dados são replicadas para o local de recuperação, não há nenhum requisito para um backup separado para arquivos de configuração ou certificados. As associações e mapeamentos de aplicativo dependentes de variáveis de ambiente que são alterados após o failover podem ser atualizados por meio de scripts integrados aos planos de recuperação de desastre. As máquinas virtuais são ativadas no site de recuperação somente durante um failover. Azure Site Recovery também ajuda a orquestrar o failover de ponta a ponta fornecendo os seguintes recursos:

- O desligamento e a inicialização de máquinas virtuais nas várias camadas.
- Adicionar scripts para permitir atualizações de dependências de aplicativo e associações nas máquinas virtuais depois que elas forem iniciadas. Os scripts também podem ser usados para atualizar o servidor DNS a fim de apontar para o local de recuperação.
- Aloque endereços IP para máquinas virtuais pré-failover mapeando as redes primária e de recuperação e use scripts que não precisam ser atualizados após o failover.
- Capacidade de um failover de um clique para vários aplicativos Web que elimina o escopo de confusão durante um desastre.
- A capacidade de testar os planos de recuperação em um ambiente isolado para exercícios de recuperação de desastre.

[Saiba mais](site-recovery-iis.md) sobre a recuperação de desastres para o IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Proteger o Citrix XenApp e o XenDesktop

Use o Site Recovery para proteger suas implantações Citrix XenApp e XenDesktop, da seguinte maneira:

- Habilite a proteção da implantação do Citrix XenApp e XenDesktop. Replique as diferentes camadas de implantação para o Azure: Active Directory, servidor DNS, servidor de banco de dados SQL, controlador de entrega da Citrix, servidor de vitrine, XenApp Master (VDA), servidor de licença Citrix XenApp.
- Simplifique a migração na nuvem usando o Site Recovery para migrar sua implantação do Citrix XenApp e XenDesktop para o Azure.
- Simplifique o teste do Citrix XenApp/XenDesktop criando uma cópia de produção sob demanda para teste e depuração.
- Essa solução só se aplica a áreas de trabalho virtuais do Windows Server e não a áreas de trabalho virtuais do cliente. As áreas de trabalho virtuais do cliente ainda não têm suporte para licenciamento no Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre licenciamento para áreas de trabalho de cliente/servidor no Azure.

[Saiba mais](site-recovery-citrix-xenapp-and-xendesktop.md) sobre a recuperação de desastre para implantações do Citrix XenApp e XenDesktop. Ou, você pode consultar o [White paper da Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](azure-to-azure-quickstart.md) sobre a recuperação de desastres para uma VM do Azure.
