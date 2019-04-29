---
title: Re-hospedo um aplicativo Contoso migrando-o para VMs do Azure e grupo de disponibilidade AlwaysOn do SQL Server | Microsoft Docs
description: Saiba como a Contoso hospeda novamente um aplicativo local migrando-o para as VMs do Azure e o grupo de disponibilidade AlwaysOn do SQL Server
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 535ba0049e91e09de3d1dcf05fc8ede80ef403ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60671433"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Migração para Contoso: Hospedar novamente um aplicativo local em VMs do Azure e o Grupo de Disponibilidade AlwaysOn do SQL Server

Este artigo demonstra como a Contoso re-hospeda o aplicativo SmartHotel360 no Azure. A Contoso migra a VM de front-end do aplicativo para uma VM do Azure e o banco de dados do aplicativo para uma VM do Azure SQL Server em execução em um cluster de failover do Windows Server com grupos de disponibilidade Always On do SQL Server.

Este documento é um de uma série de artigos que mostra como a empresa fictícia Contoso migra os recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram a configuração de uma infraestrutura de migração, avaliando recursos locais para migração e executando diferentes tipos de migrações. Cenários aumentam em complexidade. Adicionaremos mais artigos com o tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar a infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md)  | A Contoso executa uma avaliação do aplicativo SmartHotel360 local em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados. | Disponível
[Artigo 4: Hospedar novamente um aplicativo em uma VM do Azure e uma Instância Gerenciada do Banco de Dados SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | A Contoso executa uma migração lift-and-shift para o Azure no aplicativo SmartHotel360 local. A Contoso migra a VM front-end do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A Contoso migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | A Contoso migra as VMs de aplicativos SmartHotel360 para VMs do Azure usando o serviço do Site Recovery. | Disponível
Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server | A Contoso migra o aplicativo SmartHotel360. A Contoso usa o Site Recovery para migrar as VMs do aplicativo. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Este artigo
[Artigo 7: Hospedar novamente um aplicativo do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para as VMs do Azure, usando o Azure Site Recovery | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e no Servidor MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para as VMs do Azure usando o Azure Site Recovery e migra o banco de dados do aplicativo para uma instância do MySQL Server no Azure usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em aplicativos Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | A Contoso migra o aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativo para uma instância do Azure SQL Server com o Assistente de Migração de Dados | Disponível
[Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e no MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure usando o Gerenciador de Tráfego do Azure, integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível 
[Artigo 11: Refatorar o TFS no Azure DevOps Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure. | Disponível
[Artigo 12: Recriar um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra o aplicativo SmartHotel para o Azure. Em seguida, ela cria novamente a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados com o Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel usando diversas funcionalidades e serviços do Azure, incluindo o Serviço de Aplicativo do Azure, o AKS (Serviço de Kubernetes do Azure), o Azure Functions, os Serviços Cognitivos do Azure e o Azure Cosmos DB. | Disponível
[Artigo 14: Escalar uma migração para o Azure](contoso-migration-scale.md) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível


Neste artigo, a Contoso migra o aplicativo Windows .NET SmartHotel360 de duas camadas executado em VMs do VMware para o Azure. Se você quiser usar este aplicativo, ele é fornecido como código aberto e você pode fazer o download dele no [ GitHub ](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com parceiros comerciais para entender o que eles desejam obter com essa migração:

- **Lidando com o crescimento da empresa**: A Contoso está crescendo e, como resultado, há pressão sobre os sistemas e a infraestrutura locais.
- **Aumentar a eficiência**: A Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários.  Necessidades de negócios IT rápidas e tempo não resíduos ou money, fornecendo, assim, mais rápido nas necessidades do cliente.
- **Aumentar a agilidade**:  A TI da Contoso precisa atender melhor às necessidades empresariais. Ele deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global.  Ela não deve atrapalhar nem se tornar um bloqueador de negócios.
- **Escala**: Uma vez que a empresa cresce com êxito, TI da Contoso deve fornecer sistemas que são capazes de crescer no mesmo ritmo.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas foram usadas para determinar o melhor método de migração:

- Após a migração, o aplicativo no Azure deve ter os mesmos recursos de desempenho que o atual no VMWare.  O aplicativo permanecerá como crítico na nuvem quanto local.
- A Contoso não quer investir nesse aplicativo.  É importante para os negócios, mas em sua forma atual, ela simplesmente deseja movê-lo com segurança para a nuvem.
- O banco de dados local do aplicativo teve problemas de disponibilidade. A Contoso gostaria de implantá-lo no Azure como um cluster de alta disponibilidade, com recursos de failover.
- A Contoso deseja atualizar de sua plataforma atual do SQL Server 2008 R2 para o SQL Server 2017.
- A Contoso não deseja usar um banco de dados SQL do Azure para este aplicativo e está procurando alternativas.


## <a name="solution-design"></a>Design da solução

Depois de fixar suas metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que ela usará para a migração.

### <a name="current-architecture"></a>Arquitetura atual

- O aplicativo é dividido em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5)
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).


### <a name="proposed-architecture"></a>Arquitetura proposta

Neste cenário:

- A Contoso migrará o WEBVM de front-end do aplicativo para uma VM IaaS do Azure.
    - A VM de front-end no Azure será implantada no grupo de recursos ContosoRG (usado para recursos de produção).
    -  Ela estará localizada na rede de produção do Azure (VNET-PROD-EUS2) na região Leste dos EUA primária.
- O banco de dados do aplicativo será migrado para uma VM do Azure SQL Server.
    - Ele estará localizado na rede (PROD-DB-EUS2) do banco de dados do Azure da Contoso na região Leste dos EUA primária.
    - Ele será colocado em um cluster de failover do Windows Server com dois nós, que usa Grupos de Disponibilidade Always On do SQL Server.
    - No Azure, os dois nós de VM do SQL Server no cluster serão implantados no grupo de recursos ContosoRG.
    - Os nós de VM estarão localizados na rede (VNET-PROD-EUS2) de produção do Azure na região Leste dos EUA primária.
    - As VMs executarão o Windows Server 2016 com o SQL Server 2017 Enterprise Edition. A Contoso não tem licenças para esse sistema operacional, portanto, ela usará uma imagem no Azure Marketplace que forneça a licença como um encargo para o compromisso do Azure EA.
    - Além dos nomes exclusivos, as duas VMs usam as mesmas configurações.
- A Contoso implantará um balanceador de carga interno que escuta o tráfego no cluster e o direcione para o nó de cluster adequado.
    - O balanceador de carga interno será implantado no ContosoNetworkingRG (usado para recursos de rede).
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.

![Arquitetura de cenário](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="database-considerations"></a>Considerações do banco de dados

Como parte do processo para criar a solução, a Contoso fez uma comparação de recursos entre o Banco de Dados SQL do Azure e o SQL Server. As seguintes considerações nos ajudaram a optar por uma VM IaaS do Azure executando o SQL Server:

 - Usar uma VM do Azure que executa o SQL Server parece ser uma solução ideal se a Contoso precisa personalizar o sistema operacional ou o servidor de banco de dados ou se ela deseja colocar a executar aplicativos de terceiros na mesma VM.
 - Usando o Assistente de Migração de Dados, a Contoso pode avaliar e migrar com facilidade para um Banco de Dados SQL do Azure.
 

### <a name="solution-review"></a>Análise de solução

A Contoso avalia seu projeto proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** | O WEBVM será movido para o Azure sem alterações, simplificando a migração.<br/><br/> A camada do SQL Server será executada no SQL Server 2017 e no Windows Server 2016. Isso desativa seu sistema operacional atual do Windows Server 2008 R2, e o SQL Server 2017 em execução é compatível com os requisitos técnicos e metas da Contoso. A TI oferece compatibilidade total ao mudar do SQL Server 2008 R2.<br/><br/> A Contoso pode usar seu investimento em Software Assurance usando o Benefício Híbrido do Azure.<br/><br/> Uma implantação do SQL Server de alta disponibilidade no Azure oferece tolerância a falhas para que a camada de dados do aplicativo não seja mais um ponto único de failover.
**Contras** | O WEBVM está executando o Windows Server 2008 R2. O sistema operacional é compatível com o Azure para funções específicas (julho de 2018). [Saiba mais](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> A camada da Web do aplicativo continuará sendo um ponto único de failover.</br><br/> A Contoso precisará continuar dando suporte à camada da Web como uma VM do Azure, em vez de mover para um serviço gerenciado como o Serviço de Aplicativo do Azure.<br/><br/> Com a solução escolhida, a Contoso precisará continuar gerenciando duas VMs do SQL Server, em vez de mover para uma plataforma gerenciada como a Instância Gerenciada do Banco de Dados SQL do Azure. Além disso, com o Software Assurance, a Contoso poderia trocar suas licenças existentes por taxas tarifas com desconto na Instância Gerenciada Banco de Dados SQL do Azure.


### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de Migração de Banco de Dados](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | O AMD é executado localmente do computador com SQL Server local e migra o banco de dados em uma VPN site a site para o Azure. | O AMD é uma ferramenta gratuita e que pode ser baixada.
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O Site Recovery orquestra e gerencia migração e recuperação de desastres para VMs do Azure e VMs e servidores físicos locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure.  As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre encargos e preços.

 

## <a name="migration-process"></a>Processo de migração

Os administradores da Contoso migrarão as VMs de aplicativo para o Azure.

- Eles migrarão a VM front-end para a VM do Azure usando o Site Recovery:
    - Como primeiro passo, eles preparam e configuram os componentes do Azure e preparam a infraestrutura local do VMware.
    - Com tudo preparado, eles podem começar a replicar a VM.
    - Depois que a replicação é habilitada e está funcionando, eles migram a VM ao falhar no Azure.
- Eles migrarão o banco de dados para um cluster do SQL Server no Azure, usando o AMD (Assistente de Migração de Dados).
    - Como primeira etapa, eles precisarão provisionar as VMs do SQL Server no Azure, configurar o cluster e um balanceador de carga interno e configurar os grupos de disponibilidade AlwaysOn.
    - Com isso, eles podem migrar o banco de dados
- Após a migração, eles ativarão a proteção AlwaysOn para o banco de dados.

![Processo de migração](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Pré-requisitos

Veja o que a Contoso precisa fazer para esse cenário.

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | A Contoso já criou uma assinatura em um artigo anterior nesta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.<br/><br/> Se forem necessárias permissões mais granulares, leia [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infraestrutura do Azure** | [ Saiba como ](contoso-migration-infrastructure.md) a Contoso configurou uma infraestrutura do Azure.<br/><br/> Saiba mais sobre os requisitos de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) específicos para o Site Recovery.
**Recuperação de site (local)** | O servidor vCenter local deve estar executando a versão 5.5, 6.0 ou 6.5<br/><br/> Um host ESXi que executa a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware em execução no host ESXi.<br/><br/> As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuração de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e de [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) compatível.<br/><br/> As VMs que você deseja replicar devem atender aos [ requisitos do Azure ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).



## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso executará a migração:

> [!div class="checklist"]
> * **Etapa 1: Proteger um cluster**: Crie um cluster para implantar dois nós de VM do SQL Server no Azure.
> * **Etapa 2: Implantar e configurar o cluster**: Prepare um cluster do SQL Server do Azure.  Os bancos de dados são migrados para esse cluster pré-criado.
> * **Etapa 3: Implantar o balanceador de carga**: Implante um balanceador de carga para balancear o tráfego para os nós do SQL Server.
> * **Etapa 4: Preparar o Azure para Site Recovery**: Cria uma conta de armazenamento do Azure para armazenar dados replicados e área segura do Recovery Services. 
> * **Etapa 5: Preparar VMware local para Site Recovery**: Prepare contas para a instalação de descoberta e agente VM. Prepare VMs locais para que os usuários possam se conectar às VMs do Azure após a migração.
> * **Etapa 6: Replicar VMs**: Habilitar a replicação da VM para o Azure.
> * **Etapa 7: Instalar o DMA**: Baixe e instale o Assistente de Migração de Dados.
> * **Etapa 7: Migrar o banco de dados com o DMA**: Migrar o banco de dados para o Azure.
> * **Etapa 9: Proteger o banco de dados**: Crie um grupo de disponibilidade Always On para o cluster.
> * **Etapa 10: Para migrar a máquina virtual Web**: Execute um failover de teste para verificar se tudo está funcionando como esperado. Em seguida, execute um failover completo para o Azure. 


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Etapa 1: Preparar um cluster de grupo de disponibilidade AlwaysOn do SQL Server

Os administradores da Contoso configuram o cluster da seguinte maneira:

1. Eles criam duas VMs do SQL Server selecionando a imagem do SQL Server 2017 Enterprise Windows Server 2016 no Azure Marketplace. 

    ![SQL VM SKU](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. No **Assistente de criação de máquinas virtuais**  >  **Basics**, eles configuram:

    - Nomes para as VMs: **SQLAOG1** e **SQLAOG2**.
    - Como as máquinas são críticas para os negócios, elas habilitam o SSD para o tipo de disco da VM.
    - Eles especificam as credenciais da máquina.
    - Eles implantam as VMs na região principal EAST US 2, no grupo de recursos ContosoRG.

3. Em **Tamanho**, eles começam com D2s_V3 SKU para as duas VMs. Eles escalarão mais tarde, conforme necessário.
4. Em **Configurações**, eles fazem o seguinte:

    - Como essas VMs são bancos de dados críticos para o aplicativo, eles usam discos gerenciados.
    - Eles colocam as máquinas na rede de produção da região primária EAST US 2 (**VNET-PROD-EUS2**), na sub-rede do banco de dados (**PROD-DB-EUS2**).
    - Criam um novo conjunto de disponibilidade: **SQLAOGAVSET**, com dois domínios de falha e cinco domínios de atualização.

      ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. Em **configurações do SQL Server**, elas limitam a conectividade SQL à rede virtual (privada), na porta padrão 1433. Para autenticação, eles usam as mesmas credenciais que usam no site (**contosoadmin**).

    ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Precisa de mais ajuda?**

- [ Obtenha ajuda ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) provisionando uma VM do SQL Server.
- [ Saiba mais sobre ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) configuração de VMs para diferentes SKUs do SQL Server.

## <a name="step-2-deploy-and-set-up-the-cluster"></a>Etapa 2: Implantar e configurar o cluster

Veja como os administradores da Contoso configuram o cluster:

1. Eles configuram uma conta de armazenamento do Azure para atuar como a testemunha da nuvem.
2. Eles adicionam as VMs do SQL Server ao domínio do Active Directory no datacenter local da Contoso.
3. Eles criam o cluster no Azure.
4. Eles configuram a testemunha da nuvem.
5. Por fim, eles ativam os grupos de disponibilidade do SQL Always On.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Configurar uma conta de armazenamento como testemunha de nuvem

Para configurar uma testemunha de nuvem, a Contoso precisa de uma conta de Armazenamento do Azure que contenha o arquivo de blob usado para a arbitragem de cluster. A mesma conta de armazenamento pode ser usada para configurar a testemunha de nuvem para vários clusters. 

Os administradores da Contoso criam uma conta de armazenamento da seguinte maneira:

1. Eles especificam um nome reconhecível para a conta (**contosocloudwitness**).
2. Eles implantam uma conta geral para todos os fins, com LRS.
3. Eles colocam a conta em uma terceira região - o centro-sul dos EUA. Eles o colocam fora da região primária e secundária para que ele permaneça disponível em caso de falha regional.
4. Eles o colocam em seu grupo de recursos que contém recursos de infraestrutura - **ContosoInfraRG**.

    ![Testemunha da nuvem](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. Quando eles criam a conta de armazenamento, chaves de acesso primárias e secundárias são geradas para ela. Eles precisam da chave de acesso principal para criar a testemunha da nuvem. A chave aparece sob o nome da conta de armazenamento> **Access Keys**.

    ![Chave de acesso](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Adicionar VMs do SQL Server ao domínio Contoso

1. Contoso adiciona SQLAOG1 e SQLAOG2 ao domínio contoso.com.
2. Em seguida, em cada VM, eles instalam o recurso e as ferramentas do cluster de failover do Windows.

### <a name="set-up-the-cluster"></a>Configurar o cluster

Antes de configurar o cluster, os administradores da Contoso obtêm um instantâneo do disco do sistema operacional em cada computador.

![instantâneo](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

1. Em seguida, eles executam um script que eles criaram para criar o cluster de failover do Windows.

    ![Criar cluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

2. Depois de criar o cluster, eles verificam se as VMs aparecem como nós do cluster.

     ![Criar cluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Configurar a testemunha da nuvem

1. Os administradores da Contoso configuram a testemunha de nuvem usando o **Assistente de Configuração de Quorum** no Gerenciador de Cluster de Failover.
2. No assistente, eles selecionam para criar uma testemunha de nuvem com a conta de armazenamento.
3. Depois que a testemunha de nuvem é configurada, ela aparece no snap-in do Gerenciador de Cluster de Failover.

    ![Testemunha da nuvem](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

### <a name="enable-sql-server-always-on-availability-groups"></a>Habilitar grupos de disponibilidade sempre no SQL Server

Agora os administradores da Contoso podem habilitar o Always On:

1. No SQL Server Configuration Manager, eles habilitam **AlwaysOn Availability Groups** para o serviço **SQL Server (MSSQLSERVER)**.

    ![Habilitar AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. Eles reiniciam o serviço para que as alterações entrem em vigor.

Com o AlwaysOn enable, a Contoso pode configurar o grupo de disponibilidade AlwaysOn que protegerá o banco de dados SmartHotel360.

**Precisa de mais ajuda?**

- [ Leia sobre o ](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) cloud witness e configure uma conta de armazenamento para ele.
- [ Obtenha instruções ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) para configurar um cluster e criar um grupo de disponibilidade.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Etapa 3: Implantar o Azure Load Balancer

Agora a Contoso deseja implantar um balanceador de carga interno que fica na frente dos nós do cluster. O balanceador de carga escuta o tráfego e o direciona para o nó apropriado.

![Balanceamento de carga](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

Eles criam o balanceador de carga da seguinte maneira:

1. No portal do Azure> **Rede**  >  **Load Balancer**, eles configuraram um novo balanceador de carga interno: **ILB-PROD-DB-EUS2-SQLAOG**.
2. Eles colocam o balanceador de carga na rede de produção **VNET-PROD-EUS2**, na sub-rede do banco de dados **PROD-DB-EUS2**.
3. Eles atribuem um endereço IP estático: 10.245.40.100.
4. Como um elemento de rede, eles implantam o balanceador de carga no grupo de recursos de rede **ContosoNetworkingRG**.

    ![Balanceamento de carga](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

Depois que o balanceador de carga interno for implantado, eles precisarão configurá-lo. Eles criam um pool de endereços de back-end, configuram um probe de integridade e configuram uma regra de balanceamento de carga.

### <a name="add-a-backend-pool"></a>Adicionar um pool de back-end

Para distribuir o tráfego para as VMs no cluster, os administradores da Contoso configuram um pool de endereços de back-end que contém os endereços IP das NICs das VMs que receberão tráfego de rede do balanceador de carga.

1. Nas configurações do balanceador de carga no portal, a Contoso adiciona um pool de back-end: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. Eles associam o conjunto com o conjunto de disponibilidade SQLAOGAVSET. As VMs no conjunto (**SQLAOG1** e **SQLAOG2**) são adicionadas ao conjunto.

    ![Pool de back-end](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Os administradores da Contoso criam uma investigação de integridade para que o balanceador de carga possa monitorar a integridade do aplicativo. O probe adiciona ou remove dinamicamente as VMs da rotação do balanceador de carga, com base em como elas respondem às verificações de integridade.

Eles criam a sonda da seguinte maneira: 

1. Nas configurações do balanceador de carga no portal, a Contoso cria uma investigação de integridade: **SQLAlwaysOnEndPointProbe**.
2. Eles configuram o probe para monitorar as VMs na porta TCP 59999.
3. Eles definem um intervalo de 5 segundos entre as sondas e um limite de 2. Se duas probes falharem, a VM será considerada não íntegra.

    ![Investigação](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Configurar o balanceador de carga para receber tráfego


Agora os administradores da Contoso configuram uma regra de balanceador de carga para definir como o tráfego é distribuído para as VMs.

- O endereço IP front-end manipula o tráfego de entrada.
- O pool de IP de back-end recebe o tráfego.

Eles criam a regra da seguinte maneira:

1. Nas configurações do balanceador de carga no portal, eles adicionam uma nova regra de balanceamento de carga: **SQLAlwaysOnEndPointListener**.
2. Eles definem um ouvinte de front-end para receber o tráfego do cliente SQL de entrada na TCP 1433.
3. Eles especificam o pool de back-end para o qual o tráfego será roteado e a porta na qual as VMs ouvem o tráfego.
4. Eles habilitam o IP flutuante (retorno de servidor direto). Isso é sempre necessário para o SQL AlwaysOn.

    ![Investigação](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Precisa de mais ajuda?**

- [ Obtenha uma visão geral ](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) do Balanceador de carga do Azure.
- [ Saiba mais sobre ](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) como criar um balanceador de carga.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Etapa 4: Preparar o Azure para o serviço do Site Recovery

Aqui estão os componentes do Azure que a Contoso precisa para implantar o Site Recovery:

- Uma VNet em que as VMs estarão localizadas quando estiverem criando durante o failover.
- Uma conta de armazenamento do Azure para armazenar dados replicados. 
- Um cofre dos Serviços de Recuperação no Azure.

Os administradores da Contoso definem o seguinte:

1.  A Contoso já criou uma rede / sub-rede que pode usar para o Site Recovery quando [ implantou a infraestrutura do Azure ](contoso-migration-rehost-vm-sql-ag.md).

    - O aplicativo do SmartHotel360 é um aplicativo de produção e WEBVM será migrado para a rede de produção do Azure (VNET-PROD-EUS2) na região Leste dos EUA 2 primária.
    - O WEBVM será colocado no grupo de recursos ContosoRG, que é usado para recursos de produção e na sub-rede de produção (PROD-FE-EUS2).

2. Os administradores da Contoso criam uma conta de armazenamento do Azure (contosovmsacc20180528) na região primária.

    - Eles usam uma conta de propósito geral, com armazenamento padrão e replicação de LRS.
    - A conta deve estar na mesma região do cofre.

      ![Armazenamento de recuperação de site](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Com a conta de rede e armazenamento em vigor, eles agora criam um cofre do Recovery Services (**ContosoMigrationVault**) e o colocam no grupo de recursos **ContosoFailoverRG**, na região primária do Leste dos EUA 2.

    ![Cofre dos Serviços de Recuperação](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) como configurar o Azure para o Site Recovery.


## <a name="step-5-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 5: Preparar VMware local para Site Recovery

Veja o que os administradores da Contoso preparam localmente:

- Uma conta no vCenter Server ou no host ESXi do vSphere para automatizar a descoberta de VM.
- Crie uma conta que permite que a instalação automática do serviço de mobilidade de máquinas virtuais do VMware que você deseja replicar.
- Configurações de VM locais, para que a Contoso possa se conectar a máquinas virtuais do Azure replicadas após o failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. 
- Orquestrar a replicação, o failover e o failback.
- Pelo menos uma conta somente leitura é necessária. É necessária uma conta que possa executar operações como criação e remoção de discos e ativação de VMs.

Os administradores da Contoso configuram a conta da seguinte maneira:

1. Eles criam uma função no nível do vCenter.
2. Em seguida, eles atribuem a essa função as permissões necessárias.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado em cada VM.

- O Site Recovery pode fazer uma instalação automática de envio desse componente quando a replicação é habilitada para a VM.
- Você precisa de uma conta que o Site Recovery possa usar para acessar a VM para a instalação push. Especifique essa conta ao configurar a replicação no console do Azure.
- A conta pode ser de domínio ou local, com permissões para instalar na VM.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover, a Contoso deseja poder se conectar às VMs do Azure. Para fazer isso, os administradores da Contoso fazem o seguinte antes da migração:

1. Para acesso pela internet eles:

   - Habilite o RDP na VM local antes do failover
   - Assegure-se de que as regras TCP e UDP sejam incluídas para o perfil **Público**.
   - Verifique se o RDP é permitido no **Firewall do Windows**  >  **Aplicativos permitidos** para todos os perfis.
 
2. Para acesso por VPN site-to-site, eles:

   - Ative o RDP na máquina no local.
   - Permitir o RDP no **Firewall do Windows**  ->  **Aplicativos e recursos permitidos**, para **Domínio e redes particulares**.
   - Defina a política de SAN do sistema operacional na VM local para **OnlineAll**.

Além disso, quando eles executam um failover, precisam verificar o seguinte:

- Não deve haver atualizações do Windows pendentes na VM ao acionar um failover. Se houver, os usuários não poderão fazer logon na VM até que a atualização seja concluída.
- Após o failover, eles podem verificar **Diagnósticos de inicialização** para exibir uma captura de tela da VM. Se isso não funcionar, eles devem verificar se a VM está em execução e revisar essas [ dicas de solução de problemas ](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) como criar e atribuir uma função para a descoberta automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) como criar uma conta para a instalação do serviço de mobilidade por push.


## <a name="step-6-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Etapa 6: Replicar as VMs locais para o Azure com o Site Recovery

Antes que eles possam executar uma migração para o Azure, os administradores da Contoso precisam configurar e habilitar a replicação.

### <a name="set-a-replication-goal"></a>Definir uma meta de replicação

1. No cofre, sob o nome do vault (ContosoVMVault), eles selecionam uma meta de replicação (**Introdução**  >  **Recuperação do site**  >  **Preparar a infraestrutura**.
2. Eles especificam que suas máquinas estão localizadas no local, sendo executadas no VMware e replicadas no Azure.

    ![Meta de replicação](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Para continuar, eles precisam confirmar que eles tenham concluído o planejamento da implantação, selecionando **Sim, eu tenho feito isso**. Neste cenário, a Contoso está apenas migrando uma VM e não precisa de planejamento de implantação.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Os administradores da Contoso precisam configurar seu ambiente de origem. Para fazer isso, eles baixam um modelo OVF e o utilizam para implantar o servidor de configuração do Site Recovery como uma VM VMware local, altamente disponível. Depois que a configuração do servidor tiver sido finalizada, registre-o no cofre.

O servidor de configuração executa vários componentes:

- O componente de servidor de configuração que coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.

Os administradores da Contoso executam as seguintes etapas:


1. No cofre, eles baixam o modelo OVF em **Preparar a infraestrutura** > **Fonte** > **Servidor de configuração**.
    
    ![Baixe o OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. Eles importam o modelo para o VMware para criar e implantar a VM.

    ![Modelo de OVF](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. Ao ligar a máquina virtual pela primeira vez, ela é inicializada como uma experiência de instalação do Windows Server 2016. Eles aceitam o contrato de licença e inserem uma senha de administrador.
4. Após a conclusão da instalação, entram na VM como administrador. No primeiro logon, a Ferramenta de Configuração do Azure Site Recovery é iniciada por padrão.
5. Na ferramenta, ela especifica um nome a ser usado para registrar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão é estabelecida, entram na assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.

    ![Registrar servidor de configuração](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entram na máquina novamente e o Assistente de Gerenciamento do Servidor de Configuração é iniciado automaticamente.
9. No assistente, selecionam o NIC que vai receber o tráfego de replicação. Essa configuração não pode ser alterada depois de definida.
10. Eles selecionam a assinatura, o grupo de recursos e o cofre no qual registrar o servidor de configuração.
        ![cofre](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. Eles Baixe e instale o MySQL Server e VMWare PowerCLI. 
11. Após a validação, eles especificar o FQDN ou endereço IP do host de servidor ou vSphere do vCenter. Eles deixam a porta padrão e especifique um nome amigável para o servidor do vCenter.
12. Eles especificam a conta que criaram para descoberta automática e as credenciais que são usadas para instalar automaticamente o Serviço de Mobilidade. Para computadores do Windows, a conta precisa de privilégios de administrador local nas VMs.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. Após a conclusão do registro, no portal do Azure, eles verificam novamente se o servidor de configuração e o VMware Server estão listados na página **Origem** no cofre. A descoberta pode levar 15 minutos ou mais. 
8. O Site Recovery então conecta-se aos servidores VMware usando as configurações especificadas e descobre VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora os administradores da Contoso especificam configurações de replicação de destino.

1. Em **Preparar infraestrutura** > **Destino**, selecionam as configurações de destino.
2. O Site Recovery verifica se há uma rede e uma conta de armazenamento do Azure no destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Agora os administradores da Contoso podem criar uma política de replicação.

1. Em **Preparar infraestrutura** > **Configurações de Replicação** > **Política de Replicação** >  **Criar e Associar**, criam uma política **ContosoMigrationPolicy**.
2. Usam as configurações padrão:
    - **Limite de RPO**: Padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
    - **Retenção do ponto de recuperação**. Padrão de 24 horas. Esse valor especifica qual é o tempo da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
    - **Frequência de instantâneos consistentes com o aplicativo**. O padrão é de uma hora. Esse valor especifica a frequência com que são criados instantâneos consistentes com o aplicativo.
 
        ![Criar política de replicação](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar política de replicação](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Habilitar a replicação

Agora os administradores da Contoso podem começar a replicar o WebVM.

1. Em **Replicar aplicativo** > **Fonte** > **+Replicar**, ela seleciona as configurações de fonte.
2. Eles indicam que desejam ativar as VMs, selecionar o servidor vCenter e o servidor de configuração.

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Agora, eles especificam as configurações de destino, incluindo o grupo de recursos e a rede virtual, e a conta de armazenamento na qual os dados replicados serão armazenados.

     ![Habilitar a replicação](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Eles selecionam o WebVM para replicação, verificam a política de replicação e habilitam a replicação. O Site Recovery instala o Mobility Service na VM quando a replicação está ativada.
 
    ![Habilitar a replicação](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. Eles rastreiam o progresso da replicação em **Trabalhos**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.
5. Em **Essentials** no portal do Azure, eles podem ver a estrutura das VMs que estão sendo replicadas para o Azure.

    ![Modo de exibição de infraestrutura](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Precisa de mais ajuda?**

- Você pode ler uma explicação completa de todas essas etapas em [Configurar a recuperação de desastres para máquinas virtuais do VMware locais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente da fonte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Você pode saber mais sobre a [ habilitação da replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-7-install-the-database-migration-assistant-dma"></a>Etapa 7: Instalar AMD (Assistente de Migração de Banco de Dados)

Os administradores da Contoso migrarão o banco de dados do SmartHotel360 para a VM do Azure **SQLAOG1** usando o DMA. Eles configuram o DMA da seguinte forma:

1. Eles fazem o download da ferramenta do [ Centro de Download da Microsoft ](https://www.microsoft.com/download/details.aspx?id=53595) para a VM do SQL Server no local (**SQLVM**).
2. Eles executam a instalação (DownloadMigrationAssistant.msi) na VM.
3. Sobre o **concluir** página, selecione **iniciar Microsoft dados Assistente de migração** antes de concluir o assistente.

## <a name="step-8-migrate-the-database-with-dma"></a>Etapa 8: Migrar o banco de dados com o DMA

1. No DMA, eles executam uma nova migração - **SmartHotel**.
2. Eles selecionam o **tipo de servidor de destino** como **SQL Server em máquinas virtuais do Azure**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. Nos detalhes da migração, eles adicionam **SQLVM** como o servidor de origem e **SQLAOG1** como o destino. Eles especificam credenciais para cada máquina.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. Eles criam um compartilhamento local para o banco de dados e informações de configuração. Ele deve estar acessível com acesso de gravação pela conta do SQL Service no SQLVM e no SQLAOG1.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. A Contoso seleciona os logins que devem ser migrados e inicia a migração. Após a conclusão, o DMA mostra a migração como bem-sucedida.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. Eles verificam se o banco de dados está em execução no **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

O DMS se conecta à VM do SQL Server no local através de uma conexão VPN site a site entre o datacenter da Contoso e o Azure e, em seguida, migra o banco de dados.

## <a name="step-7-protect-the-database-with-alwayson"></a>Etapa 7: Proteger o banco de dados com AlwaysOn

Com o banco de dados do aplicativo em execução no **SQLAOG1**, os administradores da Contoso agora podem protegê-lo usando grupos de disponibilidade AlwaysOn. Eles configuram o AlwaysOn usando o SQL Management Studio e, em seguida, atribuem um ouvinte usando o agrupamento do Windows. 

### <a name="create-an-alwayson-availability-group"></a>Crie um grupo de disponibilidade AlwaysOn

1. No SQL Management Studio, eles clicam com o botão direito do mouse em **Always on High Availability** para iniciar o **New Availability Group Wizard**.
2. Em **Especificar Opções**, eles nomeiam o grupo de disponibilidade **SHAOG**. Em **Selecionar bancos de dados**, eles selecionam o banco de dados SmartHotel360.

    ![Grupo de disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. Em **Especificar réplicas**, eles adicionam os dois nós SQL como réplicas de disponibilidade e os configuram para fornecer failover automático com confirmação síncrona.

     ![Grupo de disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. Eles configuram um ouvinte para o grupo (**SHAOG**) e porta. O endereço IP do balanceador de carga interno é adicionado como um endereço IP estático (10.245.40.100).

    ![Grupo de disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. Em **Selecionar Sincronização de Dados**, eles ativam a propagação automática. Com essa opção, o SQL Server cria automaticamente as réplicas secundárias para cada banco de dados no grupo, portanto, a Contoso não precisa fazer o backup e restaurá-las manualmente. Após a validação, o grupo de disponibilidade é criado.

    ![Grupo de disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso correu em um problema ao criar o grupo. Eles não estão usando a segurança integrada do Windows do Active Directory e, portanto, precisam conceder permissões ao logon do SQL para criar as funções do Cluster de Failover do Windows.

    ![Grupo de disponibilidade AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. Depois que o grupo é criado, a Contoso pode vê-lo no SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Configurar um ouvinte no cluster

Como última etapa na configuração da implantação do SQL, os administradores da Contoso configuram o balanceador de carga interno como o ouvinte no cluster e coloca o ouvinte online. Você pode usar um script para realizar essa tarefa.

![Cluster listener](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Verificar a configuração

Com tudo configurado, agora a Contoso tem um grupo de disponibilidade funcional no Azure que usa o banco de dados migrado. Os administradores verificam isso conectando-se ao balanceador de carga interno no SQL Server Management Studio.

![Conexão ILB](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Precisa de mais ajuda?**
- Aprenda sobre como criar um [ grupo de disponibilidade ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) e [ listener ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Manualmente [ configure o cluster para usar o endereço IP do balanceador de carga ](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [ Saiba mais ](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) sobre como criar e usar o SAS.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Etapa 8: Migrar a VM com a recuperação do site

A Contoso administra um failover de teste rápido e, em seguida, migra a VM.

### <a name="run-a-test-failover"></a>Execute um teste de failover

Executar um failover de teste ajuda a garantir que tudo está funcionando conforme o esperado antes da migração. 

1. Eles executam um failover de teste, até o último momento disponível (**Último processamento**).
2. Selecionam **Desligar o computador antes do início do failover**, para que o Site Recovery tente desligar a VM da fonte antes de disparar o failover. O failover continuará mesmo o desligamento falhar. 
3. O failover de teste é executado: 

    - Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para a migração foram cumpridas.
    - O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação será criado nos dados.
    - Uma VM do Azure é criada usando os dados processados na etapa anterior.

3. Após o failover ser concluído, a réplica da VM do Azure aparecerá no portal do Azure. Eles Verifique se a VM é o tamanho apropriado, o que ele está conectado à rede certa, e se ele está em execução. 
4. Depois da verificação, ela limpa o failover, registra e salva as observações. 

### <a name="run-a-failover"></a>Executar um failover

1. Após verificar se o failover de teste funcionou como esperado, os administradores da Contoso criam um plano de recuperação para migração e adicionam o WEBVM ao plano.

     ![Plano de recuperação](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. Ela executa um failover no plano. Ela seleciona o último ponto de recuperação e especifica que o Site Recovery deve tentar desligar a VM local antes de disparar o failover.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Após o failover, eles verificam se a VM do Azure aparece conforme o esperado no portal do Azure.

    ![Plano de recuperação](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. Depois de verificar a VM no Azure, eles concluem a migração para concluir o processo de migração, interromper a replicação para a VM e interromper o faturamento do Site Recovery para a VM.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Atualizar a cadeia de conexão

Como etapa final do processo de migração, os administradores da Contoso atualizam a cadeia de conexão do aplicativo para apontar para o banco de dados migrado em execução no ouvinte SHAOG. Essa configuração será alterada no WEBVM agora em execução no Azure.  Essa configuração está localizada no web.config do aplicativo ASP. 

1. Localize o arquivo em C: \ inetpub \ SmartHotelWeb \ web.config.  Altere o nome do servidor para refletir o FQDN do AOG: shaog.contoso.com.

    ![Failover](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Depois de atualizar o arquivo e salvá-lo, eles reiniciam o IIS no WEBVM. Eles fazem isso usando o IISRESET / RESTART a partir de um prompt cmd.
2. Depois que o IIS for reiniciado, o aplicativo agora está usando o banco de dados em execução no SQL MI.


**Precisa de mais ajuda?**

- [Saiba mais](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) sobre como executar failovers de teste. 
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) como fazer failover para o Azure.

## <a name="clean-up-after-migration"></a>Limpar após a migração

Após a migração, o aplicativo SmartHotel360 está sendo executado em uma VM do Azure, e o banco de dados do SmartHotel360 está localizado no cluster do Azure SQL.

Now, Contoso needs to complete these cleanup steps:  

- Remova as VMs locais do inventário do vCenter.
- Remova as VMs das tarefas de backup locais.
- Atualize a documentação interna para mostrar os novos locais e endereços IP das VMs.
- Revise todos os recursos que interagem com as VMs desatribuídas e atualize quaisquer configurações ou documentação relevantes para refletir a nova configuração.
- Adicione que duas novas VMs (SQLAOG1 e SQLAOG2) devem ser adicionadas ao monitoramento de sistemas de produção.

## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente sua nova infraestrutura.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso analisa as VMs do Azure WEBVM, SQLAOG1 e SQLAOG2 para determinar quaisquer problemas de segurança. 

- A equipe revisa os NSGs (Grupos de Segurança de Rede) para que a VM controle o acesso. Os NSGs são usados para garantir que somente o tráfego permitido para o aplicativo pode passar.
- A equipe considera proteger os dados no disco usando o Azure Disk Encryption e o KeyVault.
- A equipe deve avaliar a criptografia de dados transparente (TDE) e ativá-la no banco de dados SmartHotel360 em execução no novo AOG SQL. [Saiba mais](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) sobre as práticas recomendadas de segurança para máquinas virtuais.


## <a name="bcdr"></a>BCDR

 Para a BCDR (continuidade dos negócios e recuperação de desastres), a Contoso executa as seguintes ações:
- Manter dados seguros: A Contoso faz backup dos dados nas VMs do WEBVM, SQLAOG1 e SQLAOG2 usando o serviço do Backup do Azure. [Saiba mais].
  (https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  - A Contoso também aprenderá como usar o Armazenamento do Azure para fazer backup do SQL Server diretamente no armazenamento de blobs. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-use-storage-sql-server-backup-restore).
  - Manter os aplicativos e executar: A Contoso replica as VMs do aplicativo no Azure para uma região secundária usando o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).


### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

1. A Contoso possui licenciamento existente para seu WEBVM e aproveitará o Benefício Híbrido do Azure.  A Contoso converterá as VMs do Azure existentes para aproveitar esses preços.
2. A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure. 

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso hospedou novamente o aplicativo SmartHotel360 no Azure, migrando a VM front-end do aplicativo para o Azure usando o serviço de recuperação do site. A Contoso migrava o banco de dados de aplicativo para um cluster do SQL Server provisionado no Azure e o protegia em um grupo de disponibilidade AlwaysOn do SQL Server.

## <a name="next-steps"></a>Próximas etapas

No próximo artigo na série, mostrarei como Contoso novo host em seu aplicativo de osTicket de suporte técnico de serviço em execução no Linux e implantada com um banco de dados MySQL.


