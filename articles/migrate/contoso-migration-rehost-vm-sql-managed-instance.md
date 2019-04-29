---
title: Hospedar novamente um aplicativo Contoso local migrando para VMs do Azure e Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como a Contoso hospeda novamente um aplicativo local em VMs do Azure e usando a Instância Gerenciada do Banco de Dados SQL do Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 832614c46f0269460245d081f20897b591e31fce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60670657"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Migração para Contoso: Hospedar novamente um aplicativo local em uma Instância Gerenciada do Banco de Dados SQL e VM do Azure

Neste artigo, a Contoso migra sua VM de front end de aplicativo SmartHotel360 para uma VM do Azure usando o serviço Azure Site Recovery. A Contoso também migra o banco de dados de aplicativo para Instância Gerenciada do Banco de Dados SQL do Azure.

> [!NOTE]
> Atualmente, a Instância Gerenciada do Banco de Dados SQL do Azure está em versão prévia.

Este artigo é um de uma série de artigos que documenta como a empresa fictícia Contoso migra os recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e uma série de cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários aumentam em complexidade. Os artigos serão adicionados à série ao longo do tempo.


**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão Geral](contoso-migration-overview.md) | Visão geral da estratégia de migração da Contoso, da série de artigos e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md) | A Contoso executa uma avaliação do aplicativo SmartHotel de duas camadas local em execução no VMware. A Contoso avalia as VMs do aplicativo, usando o serviço de [Migrações para Azure](migrate-overview.md). A Contoso avalia o banco de dados do SQL Server do aplicativo, usando o [Assistente de Migração de Dados](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
Artigo 4: Hospedar novamente um aplicativo em uma VM do Azure e uma Instância Gerenciada do Banco de Dados SQL | A Contoso executa uma migração lift-and-shift para Azure no aplicativo SmartHotel local. A Contoso migra a VM front-end do aplicativo, usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A Contoso migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Este artigo
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | A Contoso migra as VMs do aplicativo SmartHotel para as VMs do Azure usando o serviço do Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | A Contoso migra o aplicativo SmartHotel. A Contoso usa o Site Recovery para migrar as VMs do aplicativo. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery. | Disponível
[Artigo 8: Hospedar novamente um aplicativo do Linux em VMs do Azure e no Banco de Dados do Azure para MySQL](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery. Ele migra o banco de dados do aplicativo para Banco de Dados do Azure para MySQL, usando MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em um aplicativo Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | A Contoso migra o aplicativo SmartHotel para um aplicativo Web do Azure e migra o banco de dados de aplicativos para uma instância do SQL Server do Azure. | Disponível
[Artigo 10: Refatorar um aplicativo do Linux em um aplicativo Web do Azure e no Banco de Dados do Azure para MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para um aplicativo Web do Azure em vários sites. O aplicativo Web está integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível
[Artigo 11: Refatorar o Team Foundation Server no Azure DevOps Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local ao migrá-lo para o Azure DevOps Services no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo em contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra o aplicativo SmartHotel para Azure e, em seguida, recria a arquitetura do aplicativo. A Contoso recria a arquitetura da camada do aplicativo Web como um contêiner do Windows e recria a arquitetura do banco de dados do aplicativo, usando o Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel, usando um intervalo de serviços e recursos do Azure, incluindo o Serviço de Aplicativo do Azure, Serviço de Kubernetes do Azure, Azure Functions, Serviços Cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Escalar uma migração para o Azure](contoso-migration-scale.md) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível




Você pode fazer o download do aplicativo SmartHotel360 de amostra usado neste artigo no [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI da Contoso trabalhou em estreita colaboração com os parceiros de negócios da empresa para reconhecer o que a empresa deseja alcançar com essa migração:

- **Lidando com o crescimento da empresa**: A Contoso está crescendo. Como resultado, a pressão aumentou na infraestrutura e sistemas locais da empresa.
- **Aumentar a eficiência**: A Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários. A empresa precisa que a TI seja rápida e não perca tempo nem dinheiro, de modo que a empresa possa entregar os requisitos do cliente de maneira mais rápida.
- **Aumentar a agilidade**:  A TI da Contoso precisa atender melhor às necessidades empresariais. Para que a empresa seja bem-sucedida em uma economia global, é necessário que seja capaz de reagir mais rápido que as mudanças do mercado. A TI da Contoso não deve atrapalhar ou tornar-se um bloqueador de negócios.
- **Escala**: À medida que os negócios da empresa crescem com êxito, a TI da Contoso deve fornecer sistemas que possam crescer no mesmo ritmo.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso identificou metas para essa migração. A empresa usa metas de migração para determinar o melhor método de migração.

- Após a migração, o aplicativo no Azure deverá ter os mesmos recursos de desempenho que o aplicativo tem atualmente no ambiente VMWare local da Contoso. Mover para a nuvem não significa que o desempenho do aplicativo é menos crítico.
- A Contoso não quer investir no aplicativo. O aplicativo é crítico e importante para os negócios, mas a Contoso simplesmente quer mover o aplicativo em sua forma atual para a nuvem.
- Tarefas de administração do banco de dados deverão ser minimizadas após o aplicativo ser migrado.
- A Contoso não deseja usar um Banco de Dados SQL do Azure para esse aplicativo. Ela está procurando alternativas.


## <a name="solution-design"></a>Design da solução

Depois de fixar suas metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que ela usará para a migração.

### <a name="current-architecture"></a>Arquitetura atual 

- A Contoso tem um datacenter principal (**contoso-datacenter**). O datacenter está localizado na cidade de Nova York, no leste dos Estados Unidos.
- A Contoso possui três branches locais adicionais nos Estados Unidos.
- O datacenter principal é conectado à Internet com uma conexão Metro Ethernet em fibra (500 MBps).
- Cada branch é conectado localmente à Internet, usando conexões de classe empresarial com túneis VPN IPsec para o datacenter principal. A configuração permite que toda a rede da Contoso esteja permanentemente conectada e otimiza a conectividade com a Internet.
- O data center principal é totalmente virtualizado com o VMware. A Contoso tem dois hosts de virtualização ESXi 6.5 que são gerenciados pelo vCenter Server 6.5.
- A Contoso usa o Active Directory para gerenciamento de identidade. A Contoso usa servidores DNS na rede interna.
- A Contoso tem um controlador de domínio local (**contosodc1**).
- Os controladores de domínio são executados em VMs do VMware. Os controladores de domínio no locais branches executados em servidores físicos.
- O aplicativo SmartHotel360 é dividido em duas VMs (**WEBVM** e **SQLVM**) que estão localizadas em um host VMware ESXi versão 6.5 (**contosohost1.contoso.com**). 
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.

![Arquitetura atual da Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

### <a name="proposed-architecture"></a>Arquitetura proposta

Nesse cenário, a Contoso deseja migrar seu aplicativo de viagem local de duas camadas da seguinte maneira:

- Migre o banco de dados do aplicativo (**SmartHotelDB**) para uma Instância Gerenciada do Banco de Dados SQL do Azure.
- Migre o **WebVM** de front-end para uma VM do Azure.
- As VMs locais no datacenter da Contoso serão descomissionadas quando a migração for concluída.

![Arquitetura de cenário](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="database-considerations"></a>Considerações do banco de dados

Como parte do processo de criação da solução, a Contoso realizou uma comparação de recursos entre o Banco de Dados SQL do Azure e a Instância Gerenciada do SQL Server. As seguintes considerações ajudaram-nos a optar pela Instância Gerenciada.

- O objetivo da Instância Gerenciada é entregar quase 100% de compatibilidade com a versão mais recente do SQL Server local. A Microsoft recomenda a Instância gerenciada para clientes que estão executando o SQL Server localmente ou na VM IaaS que desejam migrar seus aplicativos para um serviço totalmente gerenciado com o mínimo de alterações no design.
- A Contoso está planejando migrar um grande número de aplicativos do local para IaaS. Muitos deles são fornecidos pelo ISV. A Contoso percebe que usar a Instância Gerenciada ajudará a garantir a compatibilidade do banco de dados para esses aplicativos, em vez de usar o Banco de Dados SQL que pode não ser compatível.
- A Contoso pode simplesmente fazer uma migração lift-and-shift para a Instância Gerenciada usando o DMS (Serviço de Migração de Dados) totalmente automatizado. Com esse serviço em vigor, a Contoso pode reutilizá-lo para futuras migrações de banco de dados.
- O SQL Managed Instance suporta o SQL Server Agent, que é um problema importante para o aplicativo SmartHotel360. A Contoso precisa dessa compatibilidade, caso contrário, será necessário recriar os planos de manutenção exigidos pelo aplicativo.
- Com o Software Assurance, a Contoso pode trocar suas licenças existentes por tarifas com desconto em uma Instância Gerenciada do Banco de Dados SQL usando o Benefício Híbrido do Azure para SQL Server. Isso pode permitir que a Contoso economize até 30% na Instância Gerenciada.
- A Instância Gerenciada é totalmente contida na rede virtual, portanto, fornece um alto nível de isolamento e segurança para os dados da Contoso. A Contoso pode obter os benefícios da nuvem pública, enquanto mantém o ambiente isolado da Internet pública.
- A Instância Gerenciada é compatível com muitos recursos de segurança, incluindo o Always Encrypted, a máscara de dados dinâmicos, segurança de nível de linha e detecção de ameaças.


### <a name="solution-review"></a>Análise de solução

A Contoso avalia o design proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** |  O WEBVM será movido para o Azure sem alterações, simplificando a migração.<br/><br/> A Instância Gerenciada do SQL é compatível com os requisitos técnicos e metas da Contoso.<br/><br/> A Instância Gerenciada fornecerá compatibilidade total com sua implantação atual ao mudar do SQL Server 2008 R2.<br/><br/>  Eles podem aproveitar seus investimentos no Software Assurance e usar o Benefício Híbrido do Azure para o SQL Server e para o Windows Server.<br/><br/> Eles podem reutilizar o Serviço de Migração de Banco de Dados para outras migrações futuras.<br/><br/> A Instância Gerenciada do SQL tem tolerância a falhas interna que a Contoso não precisa configurar. Isso garante que a camada de dados não é mais um ponto único de failover.
**Contras** | O WEBVM está executando o Windows Server 2008 R2.  Embora esse sistema operacional seja compatível com o Azure, ele não é mais uma plataforma com suporte. [Saiba mais](https://support.microsoft.com/en-us/help/956893).<br/><br/> A camada da Web permanece um ponto único de failover com apenas os serviços de provisionamento do WEBVM.<br/><br/> A Contoso precisará continuar dando suporte à camada da Web como uma VM, em vez de mover para um serviço gerenciado, como o Serviço de Aplicativo do Azure.<br/><br/> Para a camada de dados, a Instância Gerenciada poderá não ser a melhor solução se a Contoso desejar personalizar o sistema operacional ou o servidor de banco de dados ou se desejar executar aplicativos de terceiros juntamente com o SQL Server. Executar o SQL Server em uma VM IaaS poderia fornecer essa flexibilidade. 

### <a name="migration-process"></a>Processo de migração

A Contoso migrará as camadas da Web e de dados de seu aplicativo SmartHotel360 para o Azure concluindo estas etapas:

1. A Contoso já tem a infraestrutura do Azure implantada, portanto, basta adicionar alguns componentes específicos do Azure para esse cenário.
2. A camada de dados será migrada usando o Serviço de Migração de Dados. O Serviço de Migração de Dados conecta-se à VM do SQL Server local em uma conexão VPN site a site entre o datacenter da Contoso e o Azure. Em seguida, o Serviço de Migração de Dados migra o banco de dados.
3. A camada Web será migrada usando uma migração lift-and-shift, usando Site Recovery. O processo envolve a preparação do ambiente VMware local, a configuração e habilitação da replicação e a migração das VMs, fazendo com que elas sejam reprovadas no Azure.

     ![Arquitetura de migração](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

### <a name="azure-services"></a>Serviços do Azure

Serviço | DESCRIÇÃO | Custo
--- | --- | ---
[Serviço de Migração do Banco de Dados](https://docs.microsoft.com/azure/dms/dms-overview) | O serviço de migração de banco de dados permite a migração perfeita de várias fontes de banco de dados para as plataformas de dados do Azure com tempo de inatividade mínimo. | Saiba mais sobre [regiões com suporte](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) e [preços do serviço de migração de banco de dados](https://azure.microsoft.com/pricing/details/database-migration/).
[Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Instância Gerenciada é um serviço de banco de dados gerenciado que representa uma instância do SQL Server totalmente gerenciada na nuvem do Azure. Ela usa o mesmo código da última versão do Mecanismo de Banco de Dados do SQL Server e possui os recursos, melhorias de desempenho e patches de segurança mais recentes. | O uso de uma Instância Gerenciada do Banco de Dados SQL em execução no Azure incorre em encargos com base na capacidade. Saiba mais sobre [Preço de Instância Gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço do Site Recovery orquestra e gerencia migração e recuperação de desastre para VMs do Azure e servidores físicos e VMs locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure.  As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. Saiba mais sobre [Encargos e preços do Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

 

## <a name="prerequisites"></a>Pré-requisitos

A Contoso e outros usuários devem atender aos pré-requisitos a seguir para este cenário:

Requisitos | Detalhes
--- | ---
**Inscrever-se na versão prévia da Instância Gerenciada** | É necessário estar inscrito na visualização pública limitada da Instância Gerenciada do Banco de Dados SQL. Adicionalmente, é necessária uma assinatura do Azure para [criar conta](https://portal.azure.com#create/Microsoft.SQLManagedInstance). A inscrição pode levar alguns dias para ser concluída, por isso, inscreva-se antes de começar a implantar esse cenário.
**Assinatura do Azure** | Ao realizar a avaliação no primeiro artigo desta série, é necessário já ter criado a assinatura. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador da assinatura, será necessário trabalhar com o administrador para atribuir-lhe permissões de Proprietário ou de Colaborador.<br/><br/> Se permissões mais granulares forem necessárias, consulte [Use o controle de acesso baseado em função para gerenciar o acesso à recuperação do site](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (locais)** | A instância do vCenter Server local deve estar executando a versão 5.5, 6.0 ou 6.5<br/><br/> Um host ESXi que executa a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware em execução no host ESXi.<br/><br/> As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuração de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e de [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) compatível.
**Serviço de Migração do Banco de Dados** | Para o serviço de migração de banco de dados, você precisa de uma [dispositivo VPN local compatível](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> É necessário poder configurar o dispositivo VPN local. Um endereço IPv4 público externo é necessário. O endereço não pode ser localizado atrás de um dispositivo NAT.<br/><br/> Certifique-se de ter acesso ao seu banco de dados local do SQL Server.<br/><br/> O Firewall do Windows deve poder acessar o mecanismo do banco de dados de origem. Saiba como [configurar o Firewall do Windows para acesso ao Mecanismo de Banco de Dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Se houver um firewall em frente do computador de banco de dados, adicione regras para permitir o acesso ao banco de dados e arquivos via porta SMB 445.<br/><br/> As credenciais usadas para conectar a instância de origem do SQL Server e qual Instância Gerenciada de destino deve ser membro da função de servidor sysadmin.<br/><br/> Você precisa de uma rede compartilhar no banco de dados local que o serviço de migração de banco de dados pode usar para fazer backup de banco de dados de origem.<br/><br/> Certifique-se de que a conta de serviço que está executando a instância do SQL Server de origem tenha permissões de gravação no compartilhamento de rede.<br/><br/> Anote o usuário e senha do Windows que têm permissões de controle total no compartilhamento de rede. O Serviço de Migração de Banco de Dados representa essas credenciais de usuário para carregar arquivos de backup no contêiner do Armazenamento do Azure.<br/><br/> O processo de instalação do SQL Server Express define o protocolo TCP/IP como **Desabilitado** por padrão. Certifique-se de que ele está habilitado.

## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso planeja configurar a implantação:

> [!div class="checklist"]
> * **Etapa 1: Configurar a Instância Gerenciada do Banco de Dados SQL do Azure**: A Contoso precisa de uma Instância Gerenciada criada previamente para a qual o banco de dados do SQL Server local será migrado.
> * **Etapa 2: Preparar o Serviço de Migração de Banco de Dados do Azure**: A Contoso deve registrar o provedor de migração de banco de dados, criar uma instância e criar um projeto do Serviço de Migração de Banco de Dados do Azure. A Contoso também deve configurar um Uniform Resource Identifier (URI) de assinatura de acesso compartilhado (SAS) para o Serviço de Migração de Banco de Dados. Um URI de SAS fornece acesso delegado a recursos na conta de armazenamento da Contoso, portanto, a Contoso pode conceder permissões limitadas a objetos de armazenamento. A Contoso configura um URI SAS, portanto, o Serviço de Migração de Banco de Dados pode acessar o contêiner da conta de armazenamento para o qual o serviço faz o upload dos arquivos de backup do SQL Server.
> *  **Etapa 3: Preparar o Azure para Site Recovery**: A Contoso deve criar uma conta de armazenamento para conter dados replicados para Site Recovery. Também é necessário criar um cofre dos Serviços de Recuperação do Azure.
> * **Etapa 4: Preparar VMware local para Site Recovery**: A Contoso preparará as contas para a descoberta de VMs e a instalação do agente para conectar VMs do Azure após failover.
> * **Etapa 5: Replicar VMs**: Para configurar a replicação, a Contoso configura os ambientes de origem e de destino do Site Recovery, configura uma política de replicação e começa a replicar as VMs para o Armazenamento do Microsoft Azure.
> * **Etapa 6: Migrar o banco de dados usando o Serviço de Migração de Banco de Dados do Azure**: A Contoso migra o banco de dados.
> * **Etapa 7: Migrar a VM usando o Site Recovery**: A Contoso executa um failover de teste para verificar se tudo está funcionando. Em seguida, a Contoso executa um failover completo para migrar as VMs para o Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Etapa 1: Preparar uma Instância Gerenciada do Banco de Dados SQL

Para configurar uma Instância Gerenciada do Banco de Dados SQL do Azure, a Contoso precisa de uma sub-rede que atenda aos seguintes requisitos:

- A sub-rede deve ser dedicada. Deve estar vazia e não pode conter nenhum outro serviço de nuvem. A sub-rede não pode ser uma sub-rede de gateway.
- Após a criação da Instância Gerenciada, a Contoso não deve adicionar recursos à sub-rede.
- A sub-rede não pode ter um grupo de segurança de rede associado a ela.
- A sub-rede deve ter uma tabela de rotas de UDR (rota definida pelo usuário). A única rota atribuída deve ser 0.0.0.0/0 do próximo salto para a Internet. 
- DNS personalizado opcional: Se o DNS personalizado for especificado na rede virtual do Azure, o endereço IP dos resolvedores recursivos do Azure (como 168.63.129.16) deverá ser adicionado à lista. Saiba como [configurar DNS personalizado para uma Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- A sub-rede não deve ter um ponto de extremidade de serviço (armazenamento ou SQL) associado a ela. Os pontos de extremidade de serviço devem ser desabilitados na rede virtual.
- A sub-rede deve ter no mínimo 16 endereços IP. Saiba como [dimensionar a sub-rede da Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration).
- No ambiente híbrido da Contoso, são necessárias configurações de DNS personalizadas. A Contoso configura as configurações de DNS para usar um ou mais dos servidores DNS do Azure da empresa. Saiba mais sobre [personalização de DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Configurar uma rede virtual para a Instância Gerenciada

Os administradores da Contoso configuram a rede virtual da seguinte maneira: 

1. Eles criam uma nova rede virtual (**VNET-SQLMI-EU2**) na região Leste dos EUA 2 primária. Ela adiciona a rede virtual ao grupo de recursos **ContosoNetworkingRG**.
2. Eles atribuem um espaço de endereço de 10.235.0.0/24. Eles garantem que o intervalo não se sobreponha a nenhuma outra rede em sua empresa.
3. Eles adicionar duas sub-redes na rede:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Essa sub-rede é usada para anexar um diretório à Instância Gerenciada.

      ![Instância Gerenciada - Criar rede virtual](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. Após a implantação da rede virtual e das sub-redes, eles emparelham redes da seguinte maneira:

    - Emparelha **VNET-SQLMI-EUS2** com **VNET-HUB-EUS2** (a rede virtual do hub para Leste dos EUA 2).
    - Emparelha **VNET-SQLMI-EUS2** com **VNET-PROD-EUS2** (a rede de produção).

      ![Emparelhamento de rede](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. Eles definem configurações DNS personalizadas. O DNS aponta primeiro para os controladores de domínio do Azure da Contoso. DNS do Azure é secundário. Os controladores de domínio do Azure da Contoso estão localizados conforme a seguir:

    - Localizado na sub-rede **PROD-DC-EUS2**,na rede de produção Leste dos EUA 2 (**VNET-PROD-EUS2**)
    - Endereço **CONTOSODC3**: 10.245.42.4
    - Endereço **CONTOSODC4**: 10.245.42.5
    - O resolvedor de DNS do Azure: 168.63.129.16

      ![Servidores DNS de rede](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Precisa de mais ajuda?*

- Obtenha uma visão geral da [Instância Gerenciada do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Saiba como [criar uma rede virtual para uma Instância Gerenciada do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration).
- Saiba como [configurar emparelhamento](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Saiba como [atualizar configurações de DNS do Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Configurar roteamento

A Instância Gerenciada é colocada em uma rede virtual privada. A Contoso precisa de uma tabela de rotas para a rede virtual comunicar-se com o Serviço de Gerenciamento do Azure. Se a rede virtual não puder comunicar-se com o serviço que a gerencia, a rede virtual ficará inacessível.

A Contoso considera estes fatores:

- A tabela de rotas contém um conjunto de regras (rotas) que especificam como os pacotes enviados da Instância Gerenciada devem ser roteados na rede virtual.
- A tabela de rotas é associada a sub-redes para instâncias gerenciadas são implantadas. Cada pacote que deixa uma sub-rede é tratado com base na tabela de rotas associada.
- Uma sub-rede pode ser associada a apenas uma tabela de rotas.
- Não há nenhuma cobrança adicional para a criação de tabelas de rotas no Microsoft Azure.

  Para configurar o roteamento, os administradores da Contoso fazem o seguinte:

1. Eles criam uma tabela UDR (rota) no grupo de recursos **ContosoNetworkingRG**.

    ![Tabela de rotas](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Para estar em conformidade com os requisitos da Instância Gerenciada, após a implantação da tabela de rotas (**MIRouteTable**), eles adicionam uma rota com o prefixo de endereço 0.0.0.0/0. A opção **Tipo do próximo salto** é definida para **Internet**.

    ![Prefixo de tabela de rota](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. eles associam a tabela de rotas com a sub-rede **SQLMI-DB-EUS2** (na rede **VNET-SQLMI-EUS2**). 

    ![Sub-rede da tabela de rota](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Precisa de mais ajuda?*

Saiba como [configurar rotas para uma Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

### <a name="create-a-managed-instance"></a>Criar uma Instância Gerenciada

Agora os administradores da Contoso podem provisionar uma Instância Gerenciada do Banco de Dados SQL:

1. Como a Instância Gerenciada atende a um aplicativo de negócios, eles implantam a Instância Gerenciada na região Leste dos EUA 2 primária da empresa. Eles adicionam a Instância Gerenciada ao grupo de recursos **ContosoRG**.
2. Eles selecionam um tipo de preço, computação de tamanho e armazenamento para a instância. Saiba mais sobre [Preço de Instância Gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Instância Gerenciada](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Depois que a Instância Gerenciada é implantada, dois novos recursos aparecem no grupo de recursos **ContosoRG**:

    - Um cluster virtual se a Contoso tiver várias Instâncias Gerenciadas.
    - A Instância Gerenciada do Banco de Dados SQL. 

      ![Instância Gerenciada](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Precisa de mais ajuda?*

Saiba como [provisionar uma Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-migration-service"></a>Etapa 2: Preparar o Serviço de Migração de Banco de Dados do Azure

Para preparar o Serviço de Migração de Banco de Dados, os administradores da Contoso precisam fazer algumas coisas:

- Registre o provedor de serviço de migração de banco de dados no Azure.
- Fornece o serviço de migração de banco de dados com acesso ao armazenamento do Azure para carregar os arquivos de backup que são usados para migrar um banco de dados. Para fornecer acesso ao Armazenamento do Azure, eles criam um contêiner de armazenamento de blobs do Azure. Eles geram um URI de SAS para o contêiner de armazenamento de Blobs. 
- Crie um projeto de serviço de migração de banco de dados.

Em seguida, eles concluem as seguintes etapas:

1. Eles registram o provedor de migração do banco de dados sob sua assinatura.
    ![Serviço de migração de banco de dados - Registre-se](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Eles criam um contêiner de armazenamento de blobs. A Contoso gera um URI do SAS para que o Serviço de Migração de Banco de Dados possa acessá-lo.

    ![Serviço de migração de banco de dados - gerar um URI do SAS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Eles criam uma instância de serviço de migração de banco de dados. 

    ![Serviço de migração de banco de dados - Criar instância](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Eles colocam a instância do serviço de migração de banco de dados na sub-rede **PROD-DC-EUS2** da rede virtual **VNET-PROD-DC-EUS2**.
    - O Serviço de Migração de Banco de Dados é inserido aqui, porque o serviço deve estar em uma rede virtual que pode acessar a VM do SQL Server local por meio de um gateway de VPN.
    - A **VNET-PROD-EUS2** é emparelhada para **VNET-HUB-EUS2** e tem permissão para usar gateways remotos. A opção **Usar gateways remotos** garante que o Serviço de migração de banco de dados possa se comunicar conforme necessário.

        ![O serviço de migração de banco de dados – configurar rede](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Precisa de mais ajuda?*

- Saiba como [configurar o serviço de migração de banco de dados](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Saiba como [criar e usar SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Etapa 3: Preparar o Azure para o serviço do Site Recovery

Vários elementos do Azure são necessários para que a Contoso configure o Site Recovery para migração da VM da camada Web (**WEBMV**):

- Uma rede virtual na qual os recursos de failover estão localizados.
- Uma conta de armazenamento para armazenar dados replicados. 
- Um cofre dos Serviços de Recuperação no Azure.

Os administradores da Contoso configuram o Site Recovery da seguinte maneira:

1. Como a VM é um front-end da Web para o aplicativo SmartHotel360, a Contoso falha na VM em sua rede de produção existente (**VNET-PROD-EUS2**) e sub-rede (**PROD-FE-EUS2**). A rede e sub-rede estão localizadas na região Leste dos EUA 2 primária. A Contoso configurou a rede quando [implantou a infraestrutura do Azure](contoso-migration-infrastructure.md).
2. Eles criam uma conta de armazenamento (**contosovmsacc20180528**). A Contoso usa uma conta de uso geral. A Contoso seleciona armazenamento padrão e replicação de armazenamento com redundância local.

    ![Site Recovery - Criar conta de armazenamento](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Com a rede e a conta de armazenamento em vigor, eles criam um cofre (**ContosoMigrationVault**). A Contoso coloca o cofre no grupo de recursos **ContosoFailoverRG**, na região Leste dos EUA 2 primária.

    ![Recovery Services - Criar cofre](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Precisa de mais ajuda?*

Saiba como [configurar Azure para Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 4: Preparar o VMware local para o Site Recovery

Para preparar o VMware para o Site Recovery, os administradores da Contoso devem concluir estas tarefas:

- Prepare uma conta na instância do vCenter Server ou no host vSphere ESXi. A conta automatiza a descoberta de VMs.
- Prepare uma conta que permita a instalação automática do Serviço de Mobilidade nas VMs do VMware que a Contoso deseja replicar.
- Prepare VMs locais para conectar VMs do Azure quando forem criadas após failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. É necessária pelo menos uma conta somente leitura.
- Orquestrar a replicação, o failover e o failback. A Contoso precisa de uma conta que possa executar operações, como criar e remover discos e ativar VMs.

Os administradores da Contoso configuram a conta concluindo estas tarefas:

1. Cria uma função no nível do vCenter.
2. Atribui as permissões necessárias a essa função.

*Precisa de mais ajuda?*

Saiba como [criar e atribuir uma função para descoberta automática](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do Serviço de Mobilidade

O Serviço de Mobilidade deve ser instalado na VM que a Contoso deseja replicar. A Contoso considera esses fatores sobre o Serviço de Mobilidade:

- O Site Recovery pode fazer uma instalação automática por push desse componente quando a Contoso habilita a replicação para a VM.
- Para a instalação automática por push, a Contoso deve preparar uma conta que o Site Recovery usa para acessar a VM.
- Essa conta é especificada quando a replicação é configurada no console do Azure.
- A Contoso deve ter um domínio ou conta local com permissões para instalar na VM.

*Precisa de mais ajuda*

Saiba como [criar uma conta para instalação por push do Serviço de Mobilidade](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover no Azure, a Contoso deseja ser capaz de se conectar às VMs replicadas no Azure. Para conectar VMs replicadas no Azure, os administradores da Contoso devem concluir algumas tarefas na VM local antes da migração: 

1. Para acesso pela Internet, eles habilitam o RDP na VM local antes do failover. Eles certificam-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido em **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.
2. Para acesso pela VPN site a site da Contoso, eles habilitam o RDP no computador local. Eles permitem o RDP em **Firewall do Windows** > **Aplicativos e recursos permitidos** para redes **Privadas e domínios**.
3. Eles definem a política de SAN do sistema operacional na VM local para **OnlineAll**.

Os administradores da Contoso também precisam selecionar esses itens ao executar um failover:

- Não deve haver atualizações do Windows pendentes na VM quando um failover é disparado. Se as atualizações do Windows estiverem pendentes, os usuários da Contoso não poderão entrar na máquina virtual até que a atualização seja concluída.
- Após o failover, os administradores devem selecionar **Diagnósticos de inicialização** para exibir uma captura de tela da VM. Se eles não puderem exibir o diagnóstico de inicialização, deverão verificar se a VM está em execução e, em seguida, revisar as [dicas de soluções de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure"></a>Etapa 5: Replicar as VMs locais para o Azure

Antes de executar uma migração para o Azure, os administradores da Contoso precisam configurar e habilitar a replicação para a VM local.

### <a name="set-a-replication-goal"></a>Definir uma meta de replicação

1. No cofre, embaixo do nome do cofre (**ContosoVMVault**), eles definem uma meta de replicação (**Introdução** > **Site Recovery** > **Preparar infraestrutura**).
2. Eles especificam que os computadores são locais, que são VMs do VMware, replicando-se no Azure.

    ![Preparar a infraestrutura - Metal de proteção](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Para continuar, os administradores da Contoso confirmam que concluíram o planejamento de implantação. Eles selecionam **Sim, eu fiz isso**. Nesta implantação, a Contoso está migrando apenas uma única VM; o planejamento de implantação não é necessário.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Agora, os administradores da Contoso configuram o ambiente de origem. Para configurar seu ambiente de origem, eles baixam um modelo OVF e o usam para implantar o servidor de configuração e seus componentes associados como uma VM do VMware local e altamente disponível. Os componentes do servidor incluem:

- O servidor de configuração que coordena as comunicações entre a infraestrutura local e o Azure. O servidor de configuração gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. O servidor de processo:
    - Recebe dados de replicação.
    - Otimiza a data de replicação usando cache, compactação e criptografia.
    - Envia a data de replicação para Armazenamento do Microsoft Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que serão replicadas. O servidor de processo executa a descoberta automática de VMs do VMware locais.
- Depois que a VM do servidor de configuração é criada e iniciada, a Contoso registra o servidor no cofre.

Para configurar o ambiente de origem, os administradores da Contoso fazem o seguinte:

1. Eles baixam o modelo OVF do portal do Azure (**Preparar Infraestrutura**  >  **Fonte**  >  **Servidor de Configuração**).
    
    ![Adicionar um servidor de configuração](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Eles importam o modelo para o VMware para criar e implantar a VM.

    ![Implantar o modelo de OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Quando eles ligam a VM pela primeira vez, ela é iniciada em uma experiência de instalação do Windows Server 2016. Eles aceitam o contrato de licença e inserem uma senha de administrador.
4. Quando a instalação é concluída, eles entram na VM como o administrador. Na primeira conexão, a Ferramenta de Configuração do Azure Site Recovery é executada automaticamente.
5. Na Ferramenta de Configuração do Site Recovery, eles inserem um nome a ser usado para registrar o servidor de configuração no cofre.
6. A ferramenta verifica a conexão da VM para o Azure. Depois que a conexão é estabelecida, eles selecionam **Entrar** para entrar na assinatura do Azure. As credenciais devem ter acesso ao cofre no qual o servidor de configuração está registrado. 

    ![Registrar o servidor de configuração](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada. Eles entram novamente no computador. O Assistente de Gerenciamento do Servidor de Configuração inicia automaticamente.
8. No assistente, selecionam o NIC que vai receber o tráfego de replicação. Essa configuração não pode ser alterada depois de definida.
9. Eles selecionam a assinatura, grupo de recursos e o cofre dos Serviços de Recuperação no qual registrar o servidor de configuração.

    ![Selecionar o cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Eles baixam e instalam o MySQL Server e o VMware PowerCLI. Em seguida, eles validam as configurações do servidor.
11. Após a validação, eles inserem o FQDN ou o endereço IP da instância do vCenter Server ou do host vSphere. Eles deixam a porta padrão e inserem um nome de exibição para a instância do vCenter Server no Azure.
12. Eles especificam a conta criada anteriormente para que o Site Recovery possa descobrir automaticamente as VMs do VMware disponíveis para replicação. 
13. Eles inserem credenciais para que o Serviço de Mobilidade seja instalado automaticamente quando a replicação é habilitada. Para computadores Windows, a conta precisa de permissões de administrador local nas VMs. 

    ![Configurar vCenter Server](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Quando o registro é concluído, no portal do Azure, eles verificam novamente se o servidor de configuração e o servidor VMware estão listados na página **Origem** no cofre. A descoberta pode levar 15 minutos ou mais. 
8. O Site Recovery conecta-se a servidores VMware usando as configurações especificadas e descobre VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora os administradores da Contoso configuram o ambiente de replicação de destino:

1. Em **Preparar infraestrutura** > **Destino**, selecionam as configurações de destino.
2. O Site Recovery verifica se há uma conta de armazenamento e rede no destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Quando a origem e o destino estão configurados, os administradores da Contoso criam uma política de replicação e associam a política ao servidor de configuração:

1. Em **Preparar infraestrutura** > **Configurações de Replicação** > **Política de Replicação** >  **Criar e Associar**, eles criam a política **ContosoMigrationPolicy**.
2. Usam as configurações padrão:
   - **Limite de RPO**: Padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
   - **Retenção do ponto de recuperação**: Padrão de 24 horas. Esse valor especifica qual é o tempo da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
   - **Frequência de instantâneos consistentes com o aplicativo**: Padrão de 1 hora. Esse valor especifica a frequência com que são criados instantâneos consistentes com o aplicativo.
 
     ![Política de replicação - Criar](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. A política é associada automaticamente ao servidor de configuração. 

    ![Política de replicação - Associar](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Precisa de mais ajuda?*

- É possível ler uma explicação completa dessas etapas em [Configurar recuperação de desastre para VMs do VMware locais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente da fonte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Habilitar a replicação

Agora os administradores da Contoso podem começar a replicar o WebVM.

1. Em **Replicar aplicativo** > **Fonte** > **Replicar**, eles selecionam as configurações de origem.
2. Eles indicam que desejam habilitar máquinas virtuais, selecionar a instância do vCenter Server e definir o servidor de configuração.

    ![Habilitar replicação - Origem](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Eles especificam as configurações de destino, incluindo o grupo de recursos e a rede em que a VM do Azure estará localizada após o failover. Eles especificam a conta de armazenamento na qual os dados replicados serão armazenados.

    ![Habilitar replicação - Destino](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Eles selecionam o **WebVM** para replicação. O Site Recovery instala o Serviço de Mobilidade em cada VM quando a replicação está habilitada. 

    ![Habilitar replicação - Selecionar a VM](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Eles verificam se a política de replicação correta está selecionada e permitem a replicação para **WEBVM**. Eles rastreiam o progresso da replicação em **Trabalhos**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.
6. Em **Essentials**, no portal do Azure, eles podem ver o status para as VMs que estão sendo replicadas para o Azure:

    ![Modo de exibição de infraestrutura](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Precisa de mais ajuda?*

Você pode ler um passo a passo completo dessas etapas em [Habilitar replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database"></a>Etapa 6: Migrar o banco de dados 

Os administradores da Contoso precisam criar um projeto do Serviço de Migração de Banco de Dados e, em seguida, migrar o banco de dados.

### <a name="create-a-database-migration-service-project"></a>Criar um projeto de serviço de migração de banco de dados

1. Eles criam um projeto do Serviço de Migração de Banco de Dados. Eles selecionam o tipo de servidor de origem **SQL Server** e a **Instância Gerenciada do Banco de Dados SQL do Azure** como o destino.

     ![Serviço de migração de banco de dados - novo projeto de migração](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. O Assistente de Migração é aberto.

### <a name="migrate-the-database"></a>Migrar o banco de dados 

1. No Assistente de Migração, eles especificam a VM de origem na qual o banco de dados local está localizado. Eles inserem as credenciais para acessar o banco de dados.

    ![Serviço de migração de banco de dados - detalhes da origem](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. eles selecionam o banco de dados a ser migrado (**SmartHotel.Registration**):

    ![Serviço de migração de banco de dados - selecione bancos de dados de origem](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Para o destino, eles inserem o nome da Instância Gerenciada no Azure e acessam as credenciais.

    ![Serviço de migração de banco de dados - detalhes de destino](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Em **Nova Atividade** > **Executar migração**, eles especificam as configurações para executar a migração:
    - Credenciais de origem e destino.
    - O banco de dados para migrar.
    - O compartilhamento de rede criado na VM local. O Serviço de Migração de Banco de Dados faz backups de origem para esse compartilhamento. 
        - A conta de serviço que executa a instância de origem do SQL Server deve ter permissões de gravação nesse compartilhamento.
        - O caminho do FQDN para o compartilhamento deve ser usado.
    - O URI do SAS que fornece o Serviço de Migração de Banco de Dados com acesso ao contêiner da conta de armazenamento para o qual o serviço faz o upload dos arquivos de backup para migração.

        ![O serviço de migração de banco de dados – definir configurações de migração](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Eles salvam as configurações de migração e, em seguida, executam a migração.
6. Na **Visão geral**, eles monitoram o status da migração.

    ![Serviço de migração de banco de dados - Monitor](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Quando a migração é concluída, eles verificam se os bancos de dados de destino existem na Instância Gerenciada.

    ![O serviço de migração de banco de dados – verificar a migração de banco de dados](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm"></a>Etapa 7: Migração da VM

A Contoso administra um failover de teste rápido e, em seguida, migra a VM.

### <a name="run-a-test-failover"></a>Execute um teste de failover

Antes de migrar o WEBVM, um failover de teste ajuda a garantir que tudo funcione conforme o esperado. Os administradores concluem as seguintes etapas:

1. Eles executam um failover de teste, até o último momento disponível (**Último processamento**).
2. Eles selecionam **Desligar o computador antes do início do failover**. Com essa opção selecionada, o Site Recovery tenta desligar a VM de origem antes de disparar o failover. O failover continuará, mesmo se o desligamento falhar. 
3. O failover de teste é executado: 
    1. Uma verificação de pré-requisitos é executada para garantir que todas as condições necessárias para migração foram cumpridas.
    2. O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação é criado dos dados.
    3.  A VM do Azure é criada usando os dados processados na etapa anterior.
3. Quando o failover é concluído, a VM de réplica do Azure é exibida no portal do Azure. eles verificam se tudo está funcionando corretamente: a VM tem o tamanho apropriado, está conectada à rede correta e está em execução. 
4. Após verificar o failover de teste, eles limparão o failover e registrarão quaisquer observações. 

### <a name="migrate-the-vm"></a>Migração da VM

1. Após verificar se o failover de teste funcionou como esperado, os administradores da Contoso criam um plano de recuperação para migração e adicionam o WEBVM ao plano:

     ![Criar plano de recuperação - Selecionar itens](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Eles executam um failover no plano, selecionando o último ponto de recuperação. Eles especificam que o Site Recovery deve tentar desligar a VM local antes de disparar o failover.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Após o failover, eles verificam se a VM do Azure aparece conforme o esperado no portal do Azure.

   ![Detalhes do plano de recuperação](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Após verificar, eles concluem a migração para encerrar o processo de migração, interromper a replicação da VM e a cobrança do Site Recovery para a VM.

    ![Failover - Completar migração](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Atualizar a cadeia de conexão

Como etapa final do processo de migração, os administradores da Contoso atualizam a cadeia de conexão do aplicativo para apontar para o banco de dados migrado em execução na Instância Gerenciada da Contoso.

1. No portal do Azure, eles encontram a cadeia de conexão selecionando **Configurações**  > **Cadeias de conexão**.

    ![Cadeias de conexão](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Eles atualizam a cadeia de caracteres com o nome de usuário e a senha da Instância Gerenciada do Banco de Dados SQL.
3. Após a configuração da cadeia de caracteres, eles substituem a cadeia de conexão atual no arquivo web.config do seu aplicativo.
4. Após atualizar o arquivo e salvá-lo, eles reiniciam o IIS no WEBVM executando `IISRESET /RESTART` em uma janela do Prompt de comando.
5. Depois que o IIS for reiniciado, o aplicativo usa o banco de dados em execução na Instância Gerenciada do Banco de Dados SQL.
6. Nesse ponto, eles podem desligar o computador do SQLVM local. A migração foi concluída.

*Precisa de mais ajuda?*

- Saiba como [executar um failover de teste](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Saiba como [criar um plano de recuperação](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Saiba como [fazer failover no Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração completa, o aplicativo SmartHotel360 está sendo executado em uma VM do Azure e o banco de dados SmartHotel360 está disponível na Instância Gerenciada do Banco de Dados SQL do Azure.  

Agora, a Contoso precisa realizar as seguintes tarefas de limpeza:  

- Remover a máquina WEBVM do inventário de vCenter Server.
- Remover a máquina SQLVM do inventário de vCenter Server.
- Remova o WEBVM e o SQLVM das tarefas de backup locais.
- Atualizar a documentação interna para mostrar o novo local e endereço IP para WEBVM.
- Remover SQLVM da documentação interna. Como alternativa, a Contoso pode revisar a documentação para mostrar SQLVM como excluído e não mais no inventário de VM.
- Revisar todos os recursos que interagem com as VMs descomissionadas. Atualizar qualquer configuração ou documentação relevante para refletir a nova configuração.

## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente a nova infraestrutura.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso revisa as VMs do Azure e a Instância Gerenciada do Banco de Dados SQL para verificar se há algum problema de segurança com a implementação:

- A equipe revisa os grupos de segurança de rede usados para controlar o acesso da VM. Grupos de segurança de rede ajudam a garantir que apenas o tráfego permitido para o aplicativo possa passar.
- A equipe de segurança da Contoso também está pensando em proteger os dados no disco, usando o Azure Disk Encryption e o Azure Key Vault.
- A equipe habilita a detecção de ameaças na Instância Gerenciada. A detecção de ameaças envia um alerta para o sistema da central de serviços/equipe de segurança da Contoso para abrir um tíquete se uma ameaça for detectada. Saiba mais sobre [detecção de ameaças para Instância Gerenciada ](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Segurança de Instância Gerenciada - Detecção de ameaça](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Para saber mais sobre práticas de segurança para VMs, consulte [Melhores práticas de segurança para cargas de trabalho de IaaS no Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms).

### <a name="bcdr"></a>BCDR

Para a BCDR (continuidade dos negócios e recuperação de desastres), a Contoso executa as seguintes ações:

- Manter dados seguros: A Contoso faz backup dos dados nas VMs usando o serviço de Backup do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Manter os aplicativos e executar: A Contoso replica as VMs do aplicativo no Azure para uma região secundária usando o Site Recovery. [Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).
- A Contoso aprende mais sobre como gerenciar a Instância Gerenciada do SQL, incluindo [backups de banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).


### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- A Contoso possui um licenciamento existente para WEBVM. Para aproveitar os preços do Benefício Híbrido do Azure, a Contoso converte a VM do Azure existente.
- A Contoso habilita o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. O Gerenciamento de Custos é uma solução de gerenciamento de custos com várias nuvens que ajuda a Contoso a usar e gerenciar o Azure e outros recursos da nuvem. Saiba mais sobre [Gerenciamento de Custos do Azure](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso rehosts o aplicativo SmartHotel360 no Azure, migrando a VM de front-end do aplicativo para o Azure usando o serviço de recuperação do site. A Contoso migra o banco de dados local para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.

## <a name="next-steps"></a>Próximas etapas

No próximo artigo da série, o Contoso [rehosts o aplicativo SmartHotel360 em VMs do Azure](contoso-migration-rehost-vm.md) usando o serviço Azure Site Recovery.

