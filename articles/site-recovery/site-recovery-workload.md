---
title: Quais cargas de trabalho posso proteger com o Azure Site Recovery? | Microsoft Docs
description: Descreve as cargas de trabalho que podem ser protegidas usando a recuperação de desastres com o serviço do Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: raynew
ms.openlocfilehash: 6a0e07f338da039be1660e59510b24bcd44eed0c
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491751"
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Quais cargas de trabalho posso proteger com o Azure Site Recovery?

Este artigo descreve quais aplicativos e cargas de trabalho você pode proteger com recuperação de desastre com o serviço [Azure Site Recovery](site-recovery-overview.md).



## <a name="overview"></a>Visão geral

Você precisa de uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) que mantém as cargas de trabalho e os dados em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e para recuperar as condições normais de trabalho assim que possível.

O Site Recovery é um serviço do Azure que contribui para sua estratégia de BCDR. Usando o Site Recovery, você pode implantar a replicação com reconhecimento de aplicativo na nuvem, ou para um site secundário. Se seus aplicativos são baseados no Windows ou no Linux, sendo executado em servidores físicos, VMware ou VMs do Hyper-V, você pode usar o Site Recovery para coordenar a replicação, realizar testes de recuperação de desastres e executar failovers e failback.

O Site Recovery se integra aos aplicativos da Microsoft, incluindo o SharePoint, o Exchange, o Dynamics, o SQL Server e o Active Directory. A Microsoft também trabalha em conjunto com os principais fornecedores, inclusive Oracle, SAP e Red Hat. Você pode personalizar soluções de replicação em uma base por aplicativo.

## <a name="why-use-site-recovery-for-application-replication"></a>Por que usar o Site Recovery para a replicação do aplicativo?

O Site Recovery contribui para a recuperação e a proteção no nível do aplicativo da seguinte maneira:

* Independente do aplicativo, fornecer replicação para quaisquer cargas de trabalho em execução em um computador com suporte.
* Replicação quase síncrona com RPOs de até 30 segundos para atender as necessidades dos aplicativos mais importantes.
* Instantâneos consistentes de aplicativos para os aplicativos simples ou multicamadas.
* Integração com o SQL Server AlwaysOn e parceria com outras tecnologias de replicação em nível de aplicativo, incluindo grupos de disponibilidade do AD replicação, o AlwaysOn do SQL, Exchange banco de dados (DAGs).
* Os planos de recuperação flexíveis permitem a recuperação de uma pilha inteira de aplicativos com um único clique e para incluir scripts externos e ações manuais no plano.
* O gerenciamento avançado de rede no Site Recovery e no Azure simplifica os requisitos de rede do aplicativo, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração com o Gerenciador de Tráfego do Azure para os switchovers de rede de baixo RTO.
* Uma biblioteca de automação avançada que fornece scripts específicos do aplicativo, prontos para a produção, que pode ser baixada e integrada nos planos de recuperação.

## <a name="workload-summary"></a>Resumo de carga de trabalho
O Site Recovery pode replicar qualquer aplicativo em execução em um computador com suporte. Além disso, uma parceria com as equipes de produto para executar testes adicionais específicos do aplicativo.

| **Carga de trabalho** |**Replicar VMs do Azure para o Azure** |**Replicar máquinas virtuais do Hyper-V para um site secundário** | **Replicar VMs do Hyper-V para o Azure** | **Replicar VMs do VMware para um site secundário** | **Replicar VMs do VMware para o Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |S |S |S |S |S|
| Aplicativos Web (IIS, SQL) |S |S |S |S |S|
| System Center Operations Manager |S |S |S |S |S|
| SharePoint |S |S |S |S |S|
| SAP<br/><br/>Replicar site SAP para Azure para não cluster |Y (testado pela Microsoft) |Y (testado pela Microsoft) |Y (testado pela Microsoft) |Y (testado pela Microsoft) |Y (testado pela Microsoft)|
| Exchange (não DAG) |S |S |S |S |S|
| Área de Trabalho Remota/VDI |S |S |S |S |S|
| Linux (sistema operacional e aplicativos) |Y (testado pela Microsoft) |Y (testado pela Microsoft) |Y (testado pela Microsoft) |Y (testado pela Microsoft) |Y (testado pela Microsoft)|
| Dynamics AX |S |S |S |S |S|
| Servidor de arquivos do Windows |S |S |S |S |S|
| Citrix XenApp e XenDesktop |S|N/D |S |N/D |S |

## <a name="replicate-active-directory-and-dns"></a>Replicar o Active Directory e o DNS
As infraestruturas do DNS e do Active Directory são essenciais para a maioria dos aplicativos corporativos. Durante a recuperação de desastre, você precisará proteger e recuperar esses componentes de infraestrutura antes de recuperar suas cargas de trabalho e aplicativos.

Você usar o Site Recovery para criar um plano totalmente automatizado de recuperação de desastre para o Active Directory e o DNS. Por exemplo, se você quiser fazer o failover do SharePoint e do SAP de um site primário para um secundário, poderá configurar um plano de recuperação que executa o failover do Active Directory primeiro e, em seguida, um plano de recuperação adicional específico do aplicativo para o failover em outros aplicativos que dependem do Active Directory.

[Saiba mais](site-recovery-active-directory.md) sobre como proteger o Active Directory e o DNS.

## <a name="protect-sql-server"></a>Proteger o SQL Server
O SQL Server fornece uma base de serviços de dados para muitos aplicativos de negócios em um datacenter local.  O Site Recovery pode ser usado junto com as tecnologias HA/DR do SQL Server para proteger os aplicativos corporativos de vários níveis que usam o SQL Server. A Recuperação de Site fornece:

* Uma solução de recuperação de desastre simples e econômica para o SQL Server. Replique diversas versões e edições dos clusters e servidores autônomos do SQL Server para o Azure ou um site secundário.  
* Integração com Grupos de Disponibilidade SQL AlwaysOn para gerenciar o failover e o failback com os planos de recuperação do Azure Site Recovery.
* Planos de recuperação completos para todas as camadas em um aplicativo, incluindo os bancos de dados do SQL Server.
* Escala do SQL Server para as cargas de pico com o Site Recovery "disparando-as" em máquinas virtuais IaaS maiores no Azure.
* Teste fácil de recuperação de desastres do SQL Server. Você pode executar failovers de teste para analisar os dados e executar verificações de conformidade sem afetar seu ambiente de produção.

[Saiba mais](site-recovery-sql.md) sobre como proteger o SQL Server.

## <a name="protect-sharepoint"></a>Proteger o SharePoint
O Azure Site Recovery ajuda a proteger as implantações do SharePoint, da seguinte forma:

* Elimina a necessidade e os custos associados à infraestrutura de um farm autônomo para a recuperação de desastres. Usa o Site Recovery para replicar um farm inteiro (camadas da Web, do aplicativo e do banco de dados) para o Azure ou um site secundário.
* Simplifica a implantação e o gerenciamento de aplicativos. As atualizações implantadas no site primário são automaticamente replicadas e disponibilizadas após o failover e a recuperação de um farm em um site secundário. Também reduz a complexidade do gerenciamento e custos associados à manutenção de um farm autônomo atualizado.
* Simplifica o desenvolvimento de aplicativos do SharePoint e teste criando um ambiente de réplica sob demanda com cópia de produção para o teste e a depuração.
* Simplifica a transição para a nuvem usando o Site Recovery para migrar as implantações do SharePoint para o Azure.

[Saiba mais](site-recovery-sharepoint.md) sobre como proteger o SharePoint.

## <a name="protect-dynamics-ax"></a>Proteger o Dynamics AX
O Azure Site Recovery ajuda a proteger sua solução ERP do Dynamics AX:

* Coordenando a replicação de todo o ambiente Dynamics AX (camadas da Web e AOS, camadas do banco de dados, SharePoint) para o Azure ou para um site secundário.
* Simplificando a migração das implantações do Dynamics AX para a nuvem (Azure).
* Simplificando o desenvolvimento de aplicativos Dynamics AX e teste criando uma cópia de produção sob demanda para o teste e a depuração.

[Saiba mais](site-recovery-dynamicsax.md) sobre como proteger o Dynamic AX.

## <a name="protect-rds"></a>Proteger o RDS
O RDS (Serviços da Área de Trabalho Remota) habilita a infraestrutura da área de trabalho virtual (VDI), áreas de trabalho baseadas em sessão e aplicativos, permitindo que os usuários trabalhem de qualquer lugar. Com o Azure Site Recovery, você pode:

* Replicar as áreas de trabalho virtuais em pool gerenciadas ou não para um site secundário e aplicativos e sessões remotos para um site secundário ou Azure.

* Aqui está o que você pode replicar:

| **RDS** |**Replicar VMs do Azure para o Azure** | **Replicar máquinas virtuais do Hyper-V para um site secundário** | **Replicar VMs do Hyper-V para o Azure** | **Replicar VMs do VMware para um site secundário** | **Replicar VMs do VMware para o Azure** | **Replicar servidores físicos para um site secundário** | **Replicar servidores físicos para o Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Área de trabalho virtual em pool (não gerenciada)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Área de trabalho virtual em pool (gerenciada e sem UPD)** |Não|Sim |Não |Sim |Não |Sim |Não |
| **Aplicativos remotos e sessões da área de trabalho (sem UDP)** |Sim|sim |sim |sim |sim |sim |Sim |

[Configurar a recuperação de desastre para RDS usando o Azure Site Recovery](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

[Saiba mais](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sobre como proteger o RDS.

## <a name="protect-exchange"></a>Proteger o Exchange
O Site Recovery ajuda a proteger o Exchange da seguinte maneira:

* Para implantações pequenas do Exchange, como um servidor único ou autônomo, o Site Recovery pode replicar e fazer failover no Azure ou em um site secundário.
* Para as implantações maiores, o Site Recovery integra-se ao Exchange DAGS.
* Os Exchange DAGs são a solução recomendada para recuperação de desastre do Exchange em uma empresa.  Os planos de recuperação do Site Recovery podem incluir DAGs para coordenar o failover do DAG nos sites.

[Saiba mais](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sobre como proteger o Exchange.

## <a name="protect-sap"></a>Proteger o SAP
Use o Site Recovery para proteger sua implantação do SAP como a seguir:

* Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production em execução no local, por meio da replicação de componentes no Azure.
* Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver Production em execução no Azure, por meio da replicação de componentes em outro datacenter do Azure.
* Simplifique a migração na nuvem usando o Site Recovery para migrar sua implantação do SAP para o Azure.
* Simplifique as atualizações, teste e criação de protótipos de projeto SAP criando um clone de produção sob demanda para testar aplicativos SAP.

[Saiba mais](site-recovery-sap.md) sobre como proteger o SAP.

## <a name="protect-iis"></a>Proteger o IIS
Use o Site Recovery para proteger sua implantação do IIS como mostrado abaixo:

O Azure Site Recovery oferece recuperação de desastre replicando os componentes essenciais de seu ambiente em um site remoto frio ou em uma nuvem pública, como o Microsoft Azure. Já que as máquinas virtuais com o servidor Web e o banco de dados estão sendo replicadas no local de recuperação, não há nenhum requisito para fazer backup de arquivos de configuração ou certificados separadamente. As associações e mapeamentos de aplicativo dependentes de variáveis de ambiente que são alterados após o failover podem ser atualizados por meio de scripts integrados aos planos de recuperação de desastre. As máquinas virtuais somente aparecem no site de recuperação no caso de um failover. Além disso, o Azure Site Recovery também ajuda a organizar o failover de ponta a ponta oferecendo os seguintes recursos:

-   O desligamento e a inicialização de máquinas virtuais nas várias camadas.
-   A adição de scripts para permitir a atualização das dependências do aplicativo e associações em máquinas virtuais depois da inicialização. Os scripts também podem ser usados para atualizar o servidor DNS a fim de apontar para o local de recuperação.
-   A alocação de endereços IP para máquinas virtuais antes do failover mapeando as redes primária e de recuperação e, portanto, o uso de scripts que não precisam ser atualizados após o failover.
-   A capacidade de failover em um clique para vários aplicativos Web nos servidores Web, eliminando o escopo de confusão em caso de desastre.
-   A capacidade de testar os planos de recuperação em um ambiente isolado para exercícios de recuperação de desastre.

[Saiba mais](https://aka.ms/asr-iis) sobre como proteger o web farm do IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Proteger o Citrix XenApp e o XenDesktop
Use o Site Recovery para proteger suas implantações Citrix XenApp e XenDesktop, da seguinte maneira:

* Habilite a proteção da implantação do Citrix XenApp e XenDesktop, replicando diferentes camadas de implantação incluindo (servidor AD DNS, servidor do banco de dados SQL, Citrix Delivery Controller, servidor StoreFront, XenApp Master (VDA), Citrix XenApp License Server) no Azure.
* Simplifique a migração na nuvem usando o Site Recovery para migrar sua implantação do Citrix XenApp e XenDesktop para o Azure.
* Simplifique o teste do Citrix XenApp/XenDesktop criando uma cópia de produção sob demanda para teste e depuração.
* Esta solução só é aplicável para áreas de trabalho virtuais do sistema operacional Windows Server e não para áreas de trabalho virtuais de cliente como áreas de trabalho virtuais do cliente ainda não são suportadas para o licenciamento no Azure.
[Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre licenciamento para áreas de trabalho de cliente/servidor no Azure.

[Saiba mais](site-recovery-citrix-xenapp-and-xendesktop.md) sobre como proteger as implantações Citrix XenApp e XenDesktop. Como alternativa, você pode consultar o [whitepaper da Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr) detalhando o mesmo.

## <a name="next-steps"></a>Próximas etapas

[Introdução](azure-to-azure-quickstart.md) à replicação de VM no Azure.
