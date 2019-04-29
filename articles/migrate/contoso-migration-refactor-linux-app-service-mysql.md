---
title: Refatorar um aplicativo de central de serviços da Contoso em Linux para o Serviço de Aplicativo do Azure e para o MySQL do Azure | Microsoft Docs
description: Saiba como a Contoso refatora um aplicativo Linux local migrando-o para o Serviço de Aplicativo do Azure usando o GitHub para camada da Web e o Banco de Dados SQL do Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4ff3f129838a43bd7684dc10e1653dab969e9c1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60669127"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-multiple-regions-with-azure-app-service-traffic-manager-and-azure-mysql"></a>Migração para Contoso: Refatore um aplicativo da central de serviços do Contoso Linux em várias regiões com o Serviço de Aplicativo do Azure, o Gerenciador de Tráfego e o MySQL do Azure

Este artigo mostra como a Contoso refatora seu aplicativo Linux de central de serviços local de duas camadas (osTicket), migrando-o para o Serviço de Aplicativo do Azure com a integração do GitHub e para o MySQL do Azure.

Este documento é um de uma série de artigos que mostram como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários aumentam em complexidade. Adicionaremos mais artigos com o tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar a infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md)  | A Contoso executa uma avaliação do aplicativo SmartHotel360 local em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados. | Disponível
[Artigo 4: Hospedar novamente um aplicativo em uma VM do Azure e uma Instância Gerenciada do Banco de Dados SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | A Contoso executa uma migração lift-and-shift para o Azure no aplicativo SmartHotel360 local. A Contoso migra a VM front-end do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A Contoso migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | A Contoso migra as VMs de aplicativos SmartHotel360 para VMs do Azure usando o serviço do Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | A Contoso migra o aplicativo SmartHotel360. A Contoso usa o Site Recovery para migrar as VMs do aplicativo. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível 
[Artigo 7: Hospedar novamente um aplicativo do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para as VMs do Azure, usando o Azure Site Recovery | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para as VMs do Azure usando o Azure Site Recovery e migra o banco de dados do aplicativo para uma instância do MySQL Server no Azure usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em aplicativos Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | A Contoso migra o aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativo para uma instância do Azure SQL Server com o Assistente de Migração de Dados | Disponível
Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e no MySQL do Azure | A Contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure usando o Gerenciador de Tráfego do Azure, integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Este artigo
[Artigo 11: Refatorar o TFS no Azure DevOps Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure. | Disponível
[Artigo 12: Recriar um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra o aplicativo SmartHotel para o Azure. Em seguida, ela cria novamente a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados com o Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel360 usando um intervalo de recursos e serviços do Azure, incluindo Serviço de Aplicativo do Azure, AKS (Serviço de Kubernetes do Azure), Azure Functions, Serviços Cognitivos do Azure e Azure Cosmos DB. | Disponível
[Artigo 14: Escalar uma migração para o Azure](contoso-migration-scale.md) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível

Neste artigo, a Contoso migra um aplicativo de central de serviço (osTicket) de duas camadas Linux Apache MySQL PHP (LAMP) para o Azure. Se você quiser usar este aplicativo de software livre, poderá baixá-lo no [GitHub](https://github.com/osTicket/osTicket).


## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com seus parceiros comerciais para entender o que eles desejam obter:

- **Lidando com o crescimento da empresa**: A Contoso está crescendo e movendo-se para novos mercados. Ela precisa de mais agentes de atendimento ao cliente. 
- **Escala**: A solução deve ser criada para que a Contoso possa adicionar mais agentes de atendimento ao cliente à medida que os negócios crescem.
- **Aumento da resiliência**:  No passado, os problemas com o sistema só afetavam os usuários internos. Com o novo modelo de negócios, os usuários externos serão afetados, e a Contoso precisará ter o aplicativo em funcionamento sempre.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem Contoso fixou metas para essa migração a fi de determinar o melhor método de migração:

- O aplicativo deve ser dimensionado além da capacidade e do desempenho locais atuais.  A Contoso está movendo o aplicativo para usar a colocação em escala sob demanda do Azure.
- A Contoso deseja mover a base de código do aplicativo para um pipeline de entrega contínua.  À medida que as alterações do aplicativo são enviadas para o GitHub, a Contoso deseja implantar essas alterações sem tarefas para a equipe de operações.
- O aplicativo deve ser resiliente com recursos para crescimento e failover. A Contoso deseja implantar o aplicativo em duas regiões diferentes do Azure e configurá-lo para dimensionar automaticamente.
- A Contoso deseja minimizar as tarefas de administração do banco de dados após a migração do aplicativo para a nuvem.

## <a name="solution-design"></a>Design da solução
Depois de fixar suas metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que serão usados para a migração.


## <a name="current-architecture"></a>Arquitetura atual

- O aplicativo está em camadas em duas VMs (OSTICKETWEB e OSTICKETMYSQL).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5).
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).

![Arquitetura atual](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>Arquitetura proposta

Aqui está a arquitetura proposta:

- O aplicativo de camada da Web no OSTICKETWEB será migrado criando um Serviço de Aplicativo do Azure em duas regiões do Azure. O Serviço de Aplicativo do Azure para Linux será implementado usando o contêiner do Docker PHP 7.0.
- O código do aplicativo será movido para o GitHub, e o aplicativo Web do Azure será configurado para entrega contínua com o GitHub.
- Os Servidores de Aplicativo do Azure serão implantados na região primária (Leste dos EUA 2) e secundária (EUA Central).
- O Gerenciador de Tráfego será configurado na frente dos dois aplicativos Web do Azure em ambas as regiões.
- O Gerenciador de Tráfego será configurado no modo de prioridade para forçar o tráfego até o Leste dos EUA 2.
- Se o Servidor de Aplicativo do Azure no Leste dos EUA 2 ficar offline, os usuários poderão acessar o aplicativo com failover no EUA Central.
- O banco de dados do aplicativo será migrado para o serviço PaaS do MySQL do Azure usando as ferramentas de Workbench do MySQL. O backup do banco de dados local será realizado localmente, e ele será restaurado diretamente para o MySQL do Azure.
- O banco de dados residirá na região primária Leste dos EUA 2, na sub-rede do banco de dados (PROD-DB-EUS2) na rede de produção (VNET-PROD-EUS2):
- Como a carga de trabalho de produção está sendo migrada, os recursos do Azure para o aplicativo residirão no grupo de recursos de produção **ContosoRG**.
- O recurso do Gerenciador de Tráfego será implantado no grupo de recursos de infraestrutura da Contoso **ContosoInfraRG**.
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.


![Arquitetura de cenário](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>Processo de migração

A Contoso concluirá o processo de migração da seguinte maneira:

1. Como primeira etapa, os administradores da Contoso configuram a infraestrutura do Azure, incluindo o provisionamento dos Serviços de Aplicativos do Azure, a configuração do Gerenciador de Tráfego e o provisionamento de uma instância do MySQL do Azure.
2. Após preparar o Azure, ela migra o banco de dados usando o Workbench do MySQL. 
3. Depois que o banco de dados estiver em execução no Azure, eles configurarão um repositório do GitHub privado para o Serviço de Aplicativo do Azure com entrega contínua e o carregarão com o aplicativo osTicket.
4. No portal do Azure, ela carrega o aplicativo do GitHub para o contêiner do Docker que está executando o Serviço de Aplicativo do Azure. 
5. Ela ajusta as configurações de DNS e configura o dimensionamento automático para o aplicativo.

![Processo de migração](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) | O serviço executa e dimensiona aplicativos usando o serviço PaaS do Azure para sites.  | O preço é baseado no tamanho das instâncias e nos recursos necessários. [Saiba mais](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Gerenciador de Tráfego](https://azure.microsoft.com/services/traffic-manager/) | Um balanceador carga que usa o DNS para direcionar os usuários ao Azure ou a sites e serviços externos. | O preço é calculado com base no número de consultas DNS recebidas e no número de pontos de extremidade monitorados. [Saiba mais](https://azure.microsoft.com/pricing/details/traffic-manager/).
[Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) | O banco de dados baseia-se no mecanismo do servidor MySQL de software livre. Ele fornece um banco de dados MySQL comunitário pronto para empresas e totalmente gerenciado, como um serviço para o desenvolvimento e para a implantação de aplicativos. | O preço é calculado com base nos requisitos de computação, de armazenamento e de backup. [Saiba mais](https://azure.microsoft.com/pricing/details/mysql/).

 
## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que a Contoso precisa para executar esse cenário.

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | A Contoso criou assinaturas anteriormente nesta série de artigos. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador. 
**Infraestrutura do Azure** | A Contoso configura a infraestrutura do Azure conforme descrito em [Infraestrutura do Azure para migração](contoso-migration-infrastructure.md).



## <a name="scenario-steps"></a>Etapas do cenário

É assim que a Contoso concluirá a migração:

> [!div class="checklist"]
> * **Etapa 1: Provisionar Serviços de Aplicativos do Azure**: Os administradores da Contoso provisionarão aplicativos Web nas regiões primárias e secundárias.
> * **Etapa 2: Configurar o Gerenciador de Tráfego**: Irá configurar o Gerenciador de Tráfego na frente dos aplicativos Web para balanceamento de carga e roteamento de tráfego.
> * **Etapa 3: Provisionar MySQL**: No Azure, eles provisionam uma instância do Banco de Dados MySQL no Azure.
> * **Etapa 4: Migrar o banco de dados**: Irá migrar o banco de dados usando o Workbench do MySQL. 
> * **Etapa 5: Configurar o GitHub**: Irá configurar um repositório do GitHub local para o código/sites da Web do aplicativo.
> * **Etapa 6: Implantar os aplicativos web**: Irá implantar os aplicativos web do GitHub.




## <a name="step-1-provision-azure-app-services"></a>Etapa 1: Provisionar Serviços de Aplicativos do Azure

Os administradores da Contoso provisionam dois aplicativos Web (um em cada região) usando os Serviços de Aplicativos do Azure.

1. Ela cria um recurso do aplicativo Web na região primária Leste dos EUA 2 (**osticket-eus2**) do Azure Marketplace.
2. Ela coloca o recurso no grupo de recursos de produção **ContosoRG**.

    ![Aplicativo do Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. Ela cria um novo plano do Serviço de Aplicativo na região primária (**APP-SVP-EUS2**) usando o tamanho padrão.

     ![Aplicativo do Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Eles selecionam um sistema operacional Linux com a pilha de tempo de execução do PHP 7.0, que é um contêiner do Docker.

    ![Aplicativo do Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. Eles criam um segundo aplicativo Web (**osticket-cus**) e um plano de serviço de aplicativo para a região EUA Central.

    ![Aplicativo do Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**Precisa de mais ajuda?**

- Saiba mais sobre os [aplicativos Web do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/overview).
- Saiba mais sobre o [Serviço de Aplicativo do Azure no Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).


## <a name="step-2-set-up-traffic-manager"></a>Etapa 2: Configurar o Gerenciador de Tráfego

Os administradores da Contoso configuram o Gerenciador de Tráfego para direcionar solicitações da Web de entrada para os aplicativos Web em execução na camada da Web do osTicket.

1. Eles criam um recurso do Gerenciador de Tráfego (**osticket.trafficmanager.net**) do Azure Marketplace. Ela usa o roteamento prioritário para que Leste dos EUA 2 esteja no site primário. Ela coloca o recurso no grupo de recursos de infraestrutura (**ContosoInfraRG**). Observe que o Gerenciador de Tráfego é global, e não associado a um local específico

    ![Gerenciador de Tráfego](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. Agora, eles configuram o Gerenciador de Tráfego com pontos de extremidade. Ela adiciona o aplicativo Web do Leste dos EUA 2 como o site primário (**osticket-eus2**) e o aplicativo EUA Central como secundário (**osticket-cus**).

    ![Gerenciador de Tráfego](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. Após adicionar os pontos de extremidade, ela poderá monitorá-los.

    ![Gerenciador de Tráfego](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**Precisa de mais ajuda?**

- Saiba mais sobre o [Gerenciador de Tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview).
- Saiba mais sobre [rotear tráfego para um ponto de extremidade prioritário](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method).
 
## <a name="step-3-provision-azure-database-for-mysql"></a>Etapa 3: Provisionar o Banco de Dados do Azure para MySQL

Os administradores da Contoso provisionam uma instância do banco de dados MySQL na região Leste dos EUA 2 primária.

1. No portal do Azure, ela cria um recurso de Banco de Dados do Azure para MySQL. 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Ela adiciona o nome **contosoosticket** ao banco de dados do Azure. Ela adiciona o banco de dados ao grupo de recursos de produção **ContosoRG** e especifica credenciais para ele.
3. O banco de dados MySQL local é a versão 5.7 e, portanto, ela escolhe essa versão por questões de compatibilidade. Ela usa os tamanhos padrão que correspondem aos requisitos de banco de dados.

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. Nas **Opções de Redundância de Backup**, eles escolhem usar **Com Redundância Geográfica**. Essa opção permite restaurar o banco de dados na região secundária Centro dos EUA em caso de interrupção. Ela só pode configurar essa opção quando provisiona o banco de dados.

    ![Redundância](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. Eles configuram a segurança da conexão. No banco de dados > **Segurança da conexão**, ela configurara regras de firewall para permitir que o banco de dados acesse os serviços do Azure.
5. Ela adiciona o endereço IP do cliente da estação de trabalho local para os endereços IP inicial e final. Isso permite que os aplicativos Web acessem o banco de dados MySQL, juntamente com o cliente do banco de dados que está realizando a migração.

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>Etapa 4: Migrar o banco de dados

Os administradores da Contoso migram o banco de dados usando backup e restauração, com as ferramentas do MySQL. Ela instala o Workbench do MySQL, faz backup do banco de dados de OSTICKETMYSQL e, em seguida, o restaura no servidor do Banco de Dados do Azure para MySQL.

### <a name="install-mysql-workbench"></a>Instalar o MySQL Workbench

1. Eles verificam os [pré-requisitos e downloads do MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Ela instala o Workbench do MySQL para Windows de acordo com as [instruções de instalação](https://dev.mysql.com/doc/workbench/en/wb-installing.html). O computador no qual ela instala deverá ser acessível para a VM OSTICKETMYSQL e para o Azure por meio da Internet.
3. No Workbench do MySQL, ela cria uma conexão MySQL para OSTICKETMYSQL. 

    ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. Ela exporta o banco de dados como **osticket** para um arquivo autossuficiente local.

    ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. Depois que o backup do banco de dados foi feito localmente, ela cria uma conexão com a instância do Banco de Dados do Azure para MySQL.

    ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. Agora, eles podem importar (restaurar) o banco de dados na instância do MySQL no Azure usando o arquivo autossuficiente. Um novo esquema (osticket) é criado para a instância.

    ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. Após a restauração dos dados, ele poderá ser consultado usando o Workbench e será exibido no portal do Azure.

    ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. Por fim, eles precisam atualizar as informações de banco de dados nos aplicativos Web. Na instância do MySQL, ela abre **Cadeias de conexão**. 

     ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. Na lista de cadeias de caracteres, ela localiza as configurações do aplicativo Web e clica para copiá-las.

    ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. Ela abre uma janela do Bloco de Notas e cola a cadeia de caracteres em um novo arquivo e o atualiza para corresponder ao banco de dados do osticket, à instância do MySQL e às configurações das credenciais.

     ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Eles podem verificar o nome do servidor e o logon em **Visão Geral** na instância do MySQL no portal do Azure.

    ![Workbench do MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>Etapa 5: Configurar o GitHub

Os administradores da Contoso criam um novo repositório GitHub privado e configuram uma conexão com o banco de dados do osTicket no MySQL do Azure. Em seguida, ela carrega o aplicativo Web do Azure com o aplicativo.  

1.  Ela navega até o repositório GitHub público do software OsTicket e cria o fork dele na conta do GitHub da Contoso.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. Após criar o fork, ela navega até a pasta **incluir** e localiza o arquivo **ost-config.php**.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. O arquivo é aberto no navegador e ela o edita.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. No editor, eles atualizam os detalhes do banco de dados, especificamente **DBHOST** e **DBUSER**. 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. Depois, ela confirma as alterações.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. Para cada aplicativo Web (**osticket-eus2** e **osticket-cus**), eles modificam as **Configurações de aplicativo** no portal do Azure.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. Ela insere a cadeia de conexão com o nome **osticket** e copia a cadeia de caracteres do bloco de notas para a **área de valor**. Ela seleciona **MySQL** na lista suspensa ao lado da cadeia de caracteres e salva as configurações.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>Etapa 6: Configurar os aplicativos Web

Como a etapa final do processo de migração, os administradores da Contoso configuram os aplicativos Web com os sites do osTicket.



1. No aplicativo Web primário (**osticket-eus2**) eles abrem a **Opção de implantação** e definem a fonte como **GitHub**.

    ![Configurar aplicativo](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. Ela seleciona as opções de implantação.

    ![Configurar aplicativo](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. Após definir as opções, a configuração é exibida como pendente no portal do Azure.

    ![Configurar aplicativo](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. Após a configuração ser atualizada e o aplicativo Web osTicket ser carregado do GitHub para o contêiner do Docker que está executando o Serviço de Aplicativo do Azure, o site será exibido como Ativo.

    ![Configurar aplicativo](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. Eles, então, repetem as etapas acima para o aplicativo Web secundário (**osticket-cus**).
6. Após a configuração do site, ele estará acessível por meio do perfil do Gerenciador de Tráfego. O nome DNS é o novo local do aplicativo osTicket. [Saiba mais](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

    ![Configurar aplicativo](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. A Contoso deseja um nome DNS que seja fácil de lembrar. Ela cria um registro de alias (CNAME) **osticket.contoso.com** que aponta para o nome do Gerenciador de Tráfego, no DNS em seus controladores de domínio.

    ![Configurar aplicativo](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. Ela configura os aplicativos Web **osticket-eus2** e **osticket-cus** para permitir os nomes do host personalizados.

    ![Configurar aplicativo](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>Configurar o dimensionamento automático

Por fim, ela configura a colocação em escala automática do aplicativo. Isso garante que, à medida que os agentes usam o aplicativo, suas instâncias aumentam e diminuem de acordo com as necessidades de negócios. 

1. No Serviço de Aplicativo **APP-SRV-EUS2**, eles abrem a **Unidade de Escala**.
2. Ela configurará uma nova configuração de dimensionamento automático com uma única regra que aumentará a contagem de instâncias em um quando o percentual da CPU para a instância atual estiver acima de 70% durante 10 minutos.

    ![Autoscale](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. Ela configura a mesma configuração em **APP-SRV-CUS** para garantir que o mesmo comportamento seja aplicado se o aplicativo realizar failover na região secundária. A única diferença é que ela define o limite da instância como 1, pois isso é apenas para failovers.

   ![Autoscale](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração completa, o aplicativo osTicket é refatorado para ser executado em um aplicativo Web do Azure com entrega contínua usando um repositório GitHub privado. O aplicativo está em execução em duas regiões para ter maior resiliência. O banco de dados do osTicket está em execução no banco de dados do Azure para MySQL após migração para a plataforma PaaS.

Para a limpeza, a Contoso precisa fazer o seguinte: 
- Remover as VMs VMware do inventário do vCenter.
- Remover as VMs locais de trabalhos de backup locais.
- Atualizar a documentação interna para mostrar novos locais e endereços IP. 
- Examinar todos os recursos que interagem com as VMs locais e atualizar as configurações ou documentação relevantes para refletir a nova configuração.
- Reconfigure o monitoramento para apontar para a URL osticket-trafficmanager.net, para acompanhar se o aplicativo está em funcionamento.

## <a name="review-the-deployment"></a>Revisar a implantação

Com o aplicativo em execução, a Contoso precisa operacionalizar e proteger totalmente sua infraestrutura de novo.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso examinou o aplicativo para determinar quaisquer problemas de segurança. Ela identificou que a comunicação entre o aplicativo osTicket e que a instância de banco de dados do MySQL não está configurada para SSL. Ela precisa fazer isso para fazer com que o tráfego de banco de dados não possa ser atacado. [Saiba mais](https://docs.microsoft.com/azure/mysql/howto-configure-ssl).

### <a name="backups"></a>Backups

- Os aplicativos Web osTicket não contêm dados de estado e, portanto, não é necessário realizar o backup deles.
- Eles não precisam configurar o backup do banco de dados. O Banco de Dados do Azure para MySQL cria backups do servidor e o armazena automaticamente. Ela escolheu usar a redundância geográfica para o banco de dados e, portanto, ele é resiliente e está pronto para a produção. Os backups podem ser usados para restaurar o servidor pontualmente. [Saiba mais](https://docs.microsoft.com/azure/mysql/concepts-backup).


### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- Não há nenhum problema de licenciamento para a implantação PaaS.
- A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure.



