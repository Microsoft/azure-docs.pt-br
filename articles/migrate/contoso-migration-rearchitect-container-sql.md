---
title: Recriar a arquitetura de um aplicativo Contoso em um contêiner do Azure e o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como a Contoso recria a arquitetura de um aplicativo nos Contêineres do Windows do Azure e no Banco de Dados SQL do Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: cb984bcbe79b69c0614579d66a3b853cd38a7e12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688993"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migração para Contoso: recriar a arquitetura de um aplicativo local para um contêiner do Azure e o Banco de Dados SQL do Azure

Este artigo demonstra como a Contoso migra e arquiteta novamente o aplicativo SmartHotel360 no Azure. A Contoso migra a VM de front-end do aplicativo para um contêiner do Windows do Azure e o banco de dados do aplicativo para um Banco de Dados SQL do Azure.

Este documento é um de uma série de artigos que mostra como a empresa fictícia Contoso migra os recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram a configuração de uma infraestrutura de migração, avaliando recursos locais para migração e executando diferentes tipos de migrações. Cenários aumentam em complexidade. Outros artigos serão adicionados com o tempo.

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
[Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e no MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure usando o Gerenciador de Tráfego do Azure, integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível 
[Artigo 11: Refatorar o TFS no Azure DevOps Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure. | Disponível
Artigo 12: recriar um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure | A Contoso migra o aplicativo SmartHotel para o Azure. Em seguida, ela cria novamente a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados com o Banco de Dados SQL do Azure. | Este artigo
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel usando diversas funcionalidades e serviços do Azure, incluindo o Serviço de Aplicativo do Azure, o AKS (Serviço de Kubernetes do Azure), o Azure Functions, os Serviços Cognitivos do Azure e o Azure Cosmos DB. | Disponível 
[Artigo 14: Escalar uma migração para o Azure](contoso-migration-scale.md) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível

Neste artigo, a Contoso migra o aplicativo SmartHotel360 de formulários XAML do WPF do Windows em execução em VMs do VMware para o Azure. Se você quiser usar este aplicativo, ele é fornecido como código aberto e você pode fazer o download dele no [ GitHub ](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI da Contoso trabalhou em conjunto com parceiros de negócios para entender o que eles desejam obter com essa migração:

- **Lidando com o crescimento da empresa**: A Contoso está crescendo e, como resultado, há pressão sobre os sistemas e a infraestrutura locais.
- **Aumentar a eficiência**: A Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários.  Necessidades de negócios IT rápidas e tempo não resíduos ou money, fornecendo, assim, mais rápido nas necessidades do cliente.
- **Aumentar a agilidade**:  A TI da Contoso precisa atender melhor às necessidades empresariais. Ele deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global.  Ele não deve obter da forma ou se tornar um bloqueador de negócios.
- **Escala**: Uma vez que a empresa cresce com êxito, TI da Contoso deve fornecer sistemas que são capazes de crescer no mesmo ritmo.
- **Custos**: a Contoso quer minimizar os custos de licenciamento.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas foram usadas para determinar o melhor método de migração.

**Metas** | **Detalhes**
--- | --- 
**Solicitações de aplicativo** | O aplicativo no Azure permanecerá tão importante quanto é hoje.<br/><br/> Ele deve ter os mesmos recursos de desempenho que tem atualmente no VMWare.<br/><br/> A Contoso deseja interromper o suporte ao Windows Server 2008 R2, no qual o aplicativo atualmente é executado, e está disposto a investir no aplicativo.<br/><br/> A Contoso deseja sair do SQL Server 2008 R2 e ir para uma plataforma de Banco de Dados PaaS moderna, que minimizará a necessidade de gerenciamento.<br/><br/> A Contoso deseja aproveitar seu investimento em licenciamento do SQL Server e o Software Assurance quando possível.<br/><br/> A Contoso quer ser capaz de aumentar a camada da Web do aplicativo.
**Limitações** | O aplicativo consiste em um aplicativo ASP.NET e um serviço WCF em execução na mesma VM. A Contoso deseja dividir isso em dois aplicativos Web que usam o Serviço de Aplicativo do Azure. 
**Solicitações do Azure** | A Contoso deseja mover o aplicativo para o Azure e executá-lo em um contêiner para estender sua vida útil. Ela não deseja começar completamente do zero para implementar o aplicativo no Azure. 
**DevOps** | A Contoso deseja passar para um modelo de DevOps usando o Azure DevOps Services para o pipeline de builds e de lançamento do código.

## <a name="solution-design"></a>Design da solução

Depois de fixar metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que ela usará para a migração.

### <a name="current-app"></a>Aplicativo atual

- O aplicativo local SmartHotel360 é dividido em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5)
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.


### <a name="proposed-architecture"></a>Arquitetura proposta

- Para a camada de banco de dados do aplicativo, a Contoso comparou o Banco de Dados SQL do Azure com o SQL Server usando [este artigo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Ela decidiu usar o Banco de Dados SQL do Azure por alguns motivos:
    - O Banco de Dados SQL do Azure é um serviço gerenciado de banco de dados relacional. Ele oferece desempenho previsível em vários níveis de serviço, com administração quase zero. As vantagens incluem a escalabilidade dinâmica sem tempo de inatividade, a otimização inteligente interna e a escalabilidade e a disponibilidade globais.
    - A Contoso aproveita o AMD (Assistente de Migração de Dados) leve para avaliar e migrar o banco de dados local para o SQL do Azure.
    - Com o Software Assurance, a Contoso pode trocar as licenças existentes por tarifas com desconto em um Banco de Dados SQL, usando o Benefício Híbrido do Azure para SQL Server. Isso pode proporcionar uma economia de até 30%.
    - O Banco de Dados SQL fornece uma série de recursos de segurança, incluindo o mascaramento de dados dinâmico Always Encrypted e detecção de ameaça de segurança de nível de linha.
- Quanto à camada do aplicativo Web, a Contoso decidiu convertê-la no contêiner do Windows usando o Azure DevOps Services.
    - A Contoso implantará o aplicativo usando o Azure Service Fabric e efetuará pull da imagem de contêiner do Windows do ACR (Registro de Contêiner do Azure).
    - Um protótipo para estender o aplicativo para incluir a análise de sentimento será implementado como outro serviço no Service Fabric, conectado ao Cosmos DB.  Isso lerá as informações de Tweets e exibirá no aplicativo.
- Para implementar um pipeline de DevOps, a Contoso usará o Azure DevOps para SCM (gerenciamento de código-fonte), com repositórios Git.  Builds e versões automatizados serão usados para compilar código e implantá-lo no Registro de Contêiner do Azure e no Azure Service Fabric.

    ![Arquitetura de cenário](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Análise de solução
A Contoso avalia o design proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** | O código do aplicativo SmartHotel360 precisará ser alterado para migração para o Azure Service Fabric. No entanto, o esforço é mínimo, usando as ferramentas do SDK do Service Fabric para as alterações.<br/><br/> Com a mudança para o Service Fabric, a Contoso pode começar a desenvolver microsserviços a serem adicionados ao aplicativo rapidamente ao longo do tempo, sem riscos para a base de código original.<br/><br/> Os Contêineres do Windows oferecem os mesmos benefícios que os contêineres em geral. Eles melhoram a agilidade, a portabilidade e o controle.<br/><br/> A Contoso pode aproveitar seu investimento no Software Assurance usando o Benefício Híbrido do Azure para o SQL Server e para o Windows Server.<br/><br/> Após a migração, ela não precisará mais dar suporte ao Windows Server 2008 R2. [Saiba mais](https://support.microsoft.com/lifecycle).<br/><br/> A Contoso pode configurar a camada da Web do aplicativo com várias instâncias para que ela não seja mais um ponto único de falha.<br/><br/> Ela não será mais dependente do antigo SQL Server 2008 R2.<br/><br/> O Banco de Dados SQL é compatível com os requisitos técnicos da Contoso. Os administradores da Contoso avaliaram o banco de dados local usando o Assistente de Migração de Banco de Dados e o acharam compatível.<br/><br/> O Banco de Dados SQL tem tolerância a falhas interna que a Contoso não precisa configurar. Isso garante que a camada de dados não é mais um ponto único de failover.
**Contras** | Contêineres são mais complexos do que outras opções de migração. A curva de aprendizado em contêineres pode ser um problema para a Contoso.  Eles apresentam um novo nível de complexidade que fornece bastante valor apesar da curva.<br/><br/> A equipe de operações da Contoso precisará se esforçar para entender e dar suporte ao Azure, aos contêineres e aos microsserviços para o aplicativo.<br/><br/> Se a Contoso usar o Assistente de Migração de Dados em vez do Serviço de Migração de Dados para migrar o banco de dados, ela não terá a infraestrutura pronta para migrar bancos de dados em escala.



### <a name="migration-process"></a>Processo de migração

1. A Contoso provisiona o cluster do Azure Service Fabric para Windows.
2. Ela provisiona uma instância do SQL do Azure e migra o banco de dados do SmartHotel360 para ela.
3. A Contoso converte a VM da camada da Web em um contêiner do Docker usando as ferramentas de SDK do Service Fabric.
4. Ela conecta o cluster do Service Fabric e o ACR e implanta o aplicativo usando o Azure Service Fabric.

    ![Processo de migração](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de migração do banco de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Avalia e detecta problemas de compatibilidade que podem afetar a funcionalidade do banco de dados no Azure. DMA avalia a paridade de recursos entre SQL origens e destinos e recomenda melhorias de desempenho e confiabilidade. | É uma ferramenta que pode ser baixada gratuitamente.
[Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) | Oferece um serviço de banco de dados de nuvem relacional inteligente e totalmente gerenciado. | Custo com base em recursos, taxa de transferência e tamanho. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) | Armazena imagens para todos os tipos de implantações de contêiner. | Custo com base em recursos, armazenamento e duração de uso. [Saiba mais](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Cria e opera aplicativos com disponibilidade sempre ativa, escalonáveis e distribuídos | Custos com base no tamanho, localização e duração dos nós de computação. [Saiba mais](https://azure.microsoft.com/pricing/details/service-fabric/).
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Fornece um pipeline de CI/CD (integração contínua/implantação contínua) para o desenvolvimento de aplicativos. O pipeline começa com um repositório Git para gerenciar o código do aplicativo, um sistema de build para produzir pacotes e outros artefatos de build e um sistema Release Management para implantar as alterações nos ambientes de desenvolvimento, teste e produção.

## <a name="prerequisites"></a>Pré-requisitos

Veja do que a Contoso precisa para executar esse cenário:

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | A Contoso criou assinaturas anteriormente nesta série de artigos. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.
**Infraestrutura do Azure** | [Saiba como](contoso-migration-infrastructure.md) a Contoso configurou anteriormente uma infraestrutura do Azure.
**Pré-requisitos de desenvolvedor** | A Contoso precisa das ferramentas a seguir em uma estação de trabalho de desenvolvedor:<br/><br/> - [Visual Studio 2017 Community Edition: Versão 15.5](https://www.visualstudio.com/)<br/><br/> - Carga de trabalho .NET habilitada.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0 ou posterior](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) ou Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) configurado para usar Contêineres do Windows.



## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso executa a migração:

> [!div class="checklist"]
> * **Etapa 1: Provisionar uma instância do Banco de Dados SQL no Azure**: a Contoso provisiona uma instância do SQL no Azure. Após a VM da Web de front-end ser migrada para um contêiner do Azure, a instância do contêiner com o front-end da Web do aplicativo apontará para esse banco de dados.
> * **Etapa 2: Criar um ACR (Registro de Contêiner do Azure)**: a Contoso provisiona um registro de contêiner empresarial para as imagens de contêiner do docker.
> * **Etapa 3: Provisionar o Azure Service Fabric**: provisiona um cluster do Service Fabric.
> * **Etapa 4: Gerenciar certificados do Service Fabric**: a Contoso configura os certificados para o acesso do Azure DevOps Services ao cluster.
> * **Etapa 5: Migrar o banco de dados com o DMA**: migra o banco de dados do aplicativo com o Assistente de Migração do Banco de Dados.
> * **Etapa 6: Configurar o Azure DevOps Services**: a Contoso configura um novo projeto no Azure DevOps Services e importa o código para o repositório Git.
> * **Etapa 7: Converter o aplicativo**: a Contoso converte o aplicativo em um contêiner usando o Azure DevOps e o SDK Tools.
> * **Etapa 8: Configurar build e versão**: a Contoso configura os pipelines de versão e o build para criar e publicar o aplicativo no ACR e no Cluster do Service Fabric.
> * **Etapa 9: Estender o aplicativo**: depois que o aplicativo estiver público, a Contoso o estenderá para usar os recursos do Azure e o publicará novamente no Azure usando o pipeline.



## <a name="step-1-provision-an-azure-sql-database"></a>Etapa 1: Provisionar um Banco de Dados SQL do Azure

Os administradores da Contoso provisionam um banco de dados SQL do Azure.

1. Eles escolhem criar um **Banco de Dados SQL** no Azure. 

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Eles especificam um nome de banco de dados para corresponder ao banco de dados em execução na VM local (**SmartHotel.Registration**). Eles colocam o banco de dados no grupo de recursos ContosoRG. Esse é o grupo de recursos que eles usam para recursos de produção no Azure.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Eles configuram uma nova instância do SQL Server (**sql-smarthotel-eus2**) na região primária.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Eles definem o tipo de preço para corresponder às necessidades de servidor e de banco de dados. E escolhem economizar dinheiro com o Benefício Híbrido do Azure porque já tem uma licença do SQL Server.
5. Para dimensionamento, eles usam a compra baseada em v-Core e definem os limites para os requisitos esperados.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Em seguida, eles criam a instância de banco de dados.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Depois que a instância é criada, eles abrem o banco de dados e anotam os detalhes que serão necessários quando usarem o Assistente de Migração de Banco de dados para a migração.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Precisa de mais ajuda?**

- [Obtenha ajuda ](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) o provisionamento de um Banco de Dados SQL.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) os limites de recursos do VCore.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Etapa 2: Criar um ACR e provisionar um contêiner do Azure

O contêiner do Azure é criado usando os arquivos exportados da VM da Web. O contêiner é hospedado no ACR (Registro de Contêiner do Azure).


1. Os administradores da Contoso criam um Registro de Contêiner no portal do Azure.

     ![Registro de Contêiner](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Eles fornecem um nome para o Registro (**contosoacreus2**) e o colocam na região primária, no grupo de recursos que usam para seus recursos de infraestrutura. Eles habilitam o acesso para usuários administrativos e o definem como um SKU premium para que possam aproveitar a replicação geográfica.

    ![Registro de Contêiner](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Etapa 3: Provisionar o Azure Service Fabric

O contêiner SmartHotel360 será executado no cluster do Azure Service Fabric. Os administradores da Contoso criam o Cluster do Service Fabric da seguinte maneira:

1. Crie um recurso do Service Fabric do Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. Em **Informações básicas**, eles fornecem um nome exclusivo do DS para o cluster e as credenciais para acessar a VM local. Eles colocam o recurso no grupo de recursos de produção (**ContosoRG**) na região Leste dos EUA 2 primária.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. Em **Configuração do tipo de nó**, eles inserem um nome de tipo de nó, as configurações de durabilidade, o tamanho da VM e os pontos de extremidade do aplicativo.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. Em **Criar cofre de chaves**, eles criam um novo cofre de chaves em seu grupo de recursos de infraestrutura para abrigar o certificado.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. Em **Políticas de Acesso**, eles habilitam o acesso às máquinas virtuais para implantar o cofre de chaves.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Eles especificam um nome para o certificado.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. Na página de resumo, eles copiam o link que é usado para baixar o certificado. Eles precisam disso para se conectar ao cluster do Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. Após a validação ser bem-sucedida, eles provisionam o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. No Assistente de Importação de Certificados, eles importam o certificado baixado para computadores de desenvolvimento. O certificado é usado para autenticar o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. Depois que o cluster é provisionado, eles se conectam ao Gerenciador de Cluster do Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Eles precisam selecionar o certificado correto.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. As cargas do Service Fabric Explorer e o Administrador da Contoso podem gerenciar o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-4-manage-service-fabric-certificates"></a>Etapa 4: Gerenciar certificados do Service Fabric

A Contoso precisa de certificados de cluster para permitir o acesso do Azure DevOps Services ao cluster. Os administradores da Contoso configuram isso.

1. Eles abrem o portal do Azure e navegam até o KeyVault.
2. Eles abrem os certificados e copiam a impressão digital do certificado que foi criada durante o processo de provisionamento.

    ![Copiar impressão digital](./media/contoso-migration-rearchitect-container-sql/cert1.png)
 
3. Eles a copiam para um arquivo de texto para consultar posteriormente.
4. Agora, eles adicionam um certificado do cliente que se tornará um certificado do cliente do administrador no cluster. Isso permite que o Azure DevOps Services se conectem ao cluster para a implantação do aplicativo no pipeline de lançamento. Para fazer isso, eles abrem o KeyVault no portal e selecionam **Certificados** > **Gerar/importar**.

    ![Gerar certificado do cliente](./media/contoso-migration-rearchitect-container-sql/cert2.png)

5. Eles inserem o nome do certificado e fornecem um nome diferenciado X.509 no **Assunto**.

     ![Nome do certificado](./media/contoso-migration-rearchitect-container-sql/cert3.png)

6. Após a criação do certificado, eles o baixam localmente em formato PFX.

     ![Baixar certificado](./media/contoso-migration-rearchitect-container-sql/cert4.png)

7. Agora, eles voltam para a lista de certificados no KeyVault e copiam a impressão digital do certificado do cliente recém-criado. Eles o salvam no arquivo de texto.

     ![Impressão digital do certificado do cliente](./media/contoso-migration-rearchitect-container-sql/cert5.png)

8. Para a implantação do Azure DevOps Services, eles precisam determinar o valor Base64 do certificado. Eles fazem isso na estação de trabalho do desenvolvedor local usando o PowerShell. Eles colam a saída para um arquivo de texto para uso posterior.

    ```powershell
        [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx")) 
    ```

     ![Valor de base64](./media/contoso-migration-rearchitect-container-sql/cert6.png)

9. Por fim, eles adicionam o novo certificado ao cluster do Service Fabric. Para fazer isso, no portal, eles abrem o cluster e clicam em **Segurança**.

     ![Adicionar certificado do cliente](./media/contoso-migration-rearchitect-container-sql/cert7.png)

10. Eles clicam em **Adicionar** > **Cliente administrativo** e colam a impressão digital do novo certificado do cliente. Em seguida, clicam em **Adicionar**. Isso pode demorar até 15 minutos.

     ![Adicionar certificado do cliente](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>Etapa 5: Migrar o banco de dados com o DMA

Os administradores da Contoso agora podem migrar o banco de dados do SmartHotel360 usando o AMD.

### <a name="install-dma"></a>Instalar o DMA

1. Eles fazem o download da ferramenta do [ Centro de Download da Microsoft ](https://www.microsoft.com/download/details.aspx?id=53595) para a VM do SQL Server no local (**SQLVM**).
2. Eles executam a instalação (DownloadMigrationAssistant.msi) na VM.
3. Sobre o **concluir** página, selecione **iniciar Microsoft dados Assistente de migração** antes de concluir o assistente.

### <a name="configure-the-firewall"></a>Configurar o firewall

Para se conectar ao Banco de Dados SQL do Azure, os administradores da Contoso configuram uma regra de firewall para permitir o acesso.

1. Nas propriedades **Firewall e redes virtuais** do banco de dados, eles permitem o acesso aos serviços do Azure e adicionam uma regra para o endereço IP do cliente da VM do SQL Server local.
2. Uma regra de firewall do nível do servidor é criada.

    ![Firewall](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Precisa de mais ajuda?

[Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) como criar e gerenciar as regras de firewall para o Banco de Dados SQL do Azure.

### <a name="migrate"></a>Migrar

Agora os administradores da Contoso migram o banco de dados.

1. No DMA, crie um novo projeto (**SmartHotelDB**) e selecione **Migração** 
2. Eles escolhem o tipo de servidor de origem como **SQL Server** e o destino como **Banco de Dados SQL do Azure**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Nos detalhes da migração, eles adicionam **SQLVM** como o servidor de origem e o banco de dados **SmartHotel.Registration**. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Eles recebem um erro que parece estar associado à autenticação. No entanto, após investigar, o problema é o ponto (.) no nome do banco de dados. Como solução alternativa, eles decidiram provisionar um novo banco de dados SQL usando o nome **SmartHotel-Registration** para resolver o problema. Quando eles executam o DMA novamente, são capazes de selecionar **SmartHotel-Registration** e continuar com o assistente.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. Em **Selecionar Objetos**, selecionam as tabelas de banco de dados e gerar um script SQL.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. Depois que o DMS cria o script, eles clicam em **Implantar esquema**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. O DMA confirma que a implantação foi realizada com êxito.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Agora eles iniciam a migração.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Após a migração ser concluída, a Contoso pode verificar se o banco de dados está em execução na instância SQL do Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Eles excluem o banco de dados SQL extra **SmartHotel.Registration** no portal do Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)


## <a name="step-6-set-up-azure-devops-services"></a>Etapa 6: Configurar o Azure DevOps Services

A Contoso precisa criar a infraestrutura e os pipelines de DevOps para o aplicativo.  Para fazer isso, os administradores da Contoso criam um projeto do Azure DevOps, importam seu código e, em seguida, criam e lançam pipelines.

1.   Na conta do Azure DevOps da Contoso, eles criam um projeto (**ContosoSmartHotelRearchitect**) e selecionam o **Git** para controle de versão.
![Novo projeto](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

2. Eles importam o repositório Git que atualmente contém seu código do aplicativo. Ele está em um [repositório público](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) e você pode baixá-lo.

    ![Baixar código do aplicativo](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

3. Depois que o código for importado, eles conectarão o Visual Studio ao repositório e clonarão o código usando o Team Explorer.

4. Depois que o repositório for clonado para o computador do desenvolvedor, eles abrirão o arquivo de solução do aplicativo. O aplicativo Web e o serviço wccf têm, cada um, um projeto separado dentro do arquivo.

    ![Arquivo de solução](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>Etapa 7: Converter o aplicativo em um contêiner

O aplicativo local é um aplicativo tradicional de três camadas:

- Ele contém WebForms e um Serviço WCF conectando ao SQL Server.
- Ele usa o Entity Framework para se integrar aos dados no banco de dados SQL, expondo-os por meio de um serviço WCF.
- O aplicativo WebForms interage com o serviço WCF.

Os administradores da Contoso converterão o aplicativo em um contêiner que usa o Visual Studio e o SDK Tools, da seguinte maneira:


1. Usando o Visual Studio, eles revisam o arquivo de solução aberto (SmartHotel.Registration.sln) no diretório **SmartHotel360-internal-booking-apps\src\Registration** do repositório local.  Dois aplicativos são mostrados. O front-end da Web SmartHotel.Registration.Web e o aplicativo do serviço WCF SmartHotel.Registration.WCF.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container2.png)


2. Eles clicam com o botão direito do mouse no aplicativo Web > **Adicionar** > **Suporte de Orquestrador de Contêineres**.
3. Em **Adicionar Suporte a Orquestra de Contêineres**, eles selecionam **Service Fabric**.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container3.png)
    
4. Eles repetem o processo para o aplicativo SmartHotel.Registration.WCF.
5. Agora, eles verifican como a solução foi alterada.

   - O novo aplicativo é **SmartHotel.RegistrationApplication/**
   - Ele contém dois serviços: **SmartHotel.Registration.WCF** e **SmartHotel.Registration.Web**.

     ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container4.png)

6. O Visual Studio criou o arquivo do Docker e baixou as imagens necessárias localmente para o computador de desenvolvedor.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container5.png)

7. Um arquivo de manifesto (**ServiceManifest.xml**) é criado e aberto pelo Visual Studio. Esse arquivo informa ao Service Fabric como configurar o contêiner quando ele for implantado no Azure.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container6.png)

8. Outro arquivo de manifesto (**ApplicationManifest.xml) contém os aplicativos de configuração para os contêineres.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container7.png)

9. Eles abrem o arquivo **ApplicationParameters/Cloud.xml** e atualizam a cadeia de conexão para conectar o aplicativo ao banco de dados SQL do Azure. A cadeia de conexão pode estar localizada no banco de dados no portal do Azure.

    ![Cadeia de conexão](./media/contoso-migration-rearchitect-container-sql/container8.png)

10. Eles confirmam o código atualizado e enviam por push para o Azure DevOps Services.

    ![Confirmar](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-azure-devops-services"></a>Etapa 8: Criar e lançar pipelines no Azure DevOps Services

Agora, os administradores da Contoso configuram o Azure DevOps Services para realizar o processo de build e de lançamento para acionar as práticas de DevOps.

1. No Azure DevOps Services, eles clicam em **Build e lançamento** > **Novo pipeline**.

    ![Novo pipeline](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

2. Eles selecionam **Git do Azure DevOps Services** e o repositório relevante.

    ![Git e repositório](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

3. Em **Selecionar um modelo**, eles selecionam Fabric com suporte do Docker.

     ![Fabric e Docker](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)
    
4. Eles alteram as imagens de marca de ação para **Criar uma imagem** e configuram a tarefa para usar o ACR provisionado.

     ![Registro](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

5. Na tarefa **Enviar imagens por push**, eles configuram a imagem para ser enviada por push ao ACR e optam por incluir a marca mais recente.
6. Em **Gatilhos**, eles habilitam a integração contínua e adicionam o branch mestre.

    ![Gatilhos](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

7. Eles clicam em **Salvar e enfileirar** para iniciar um build.
8. Depois que o build for bem-sucedido, eles passarão para o pipeline de lançamento. No Azure DevOps Services, eles clicam em **Lançamentos** > **Novo pipeline**.

    ![Pipeline de lançamento](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)    

9. Eles selecionam o modelo **Implantação do Azure Service Fabric** e nomeiam o estágio (**SmartHotelSF**).

    ![Ambiente](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

10. Eles fornecem um nome ao pipeline (**ContosoSmartHotel360Rearchitect**). Para o estágio, eles clicam em **1 trabalho, 1 tarefa** para configurar a implantação do Service Fabric.

    ![Fase e tarefa](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

11. Agora, eles clicam em **Novo** para adicionar uma nova conexão de cluster.

    ![Nova conexão](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

12. Em **Adicionar conexão do serviço do Service Fabric**, eles configuram a conexão e as configurações de autenticação que serão usadas pelo Azure DevOps Services para implantar o aplicativo. O ponto de extremidade do cluster pode estar localizado no portal do Azure, e eles adicionam **tcp://** como um prefixo.
13. As informações do certificado coletadas são de entrada na **Impressão digital do certificado do servidor** e **Certificado do cliente**.

    ![Certificado](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

13. Eles clicam no pipeline > **Adicionar um artefato**.

     ![Artefato](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

14. Eles selecionam o projeto e criam o pipeline usando a versão mais recente.

     ![Compilação](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

15. Observe que o raio no artefato está marcado.

     ![Status do artefato](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

16. Além disso, observe que o gatilho de implantação contínua está habilitado.

    ![Implantação contínua habilitada](./media/contoso-migration-rearchitect-container-sql/pipeline14.png) 

17. Eles clicam em **Salvar** > **Criar um lançamento**.

    ![Versão](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

18. Após a conclusão da implantação, o SmartHotel360 estará executando o Service Fabric.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish4.png)

19. Para conectar-se ao aplicativo, eles direcionam o tráfego ao endereço IP público do Azure Load Balancer na frente dos nós do Service Fabric.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>Etapa 9: Estender o aplicativo e publicar novamente

Depois que o aplicativo e o banco de dados SmartHotel360 estiverem em execução no Azure, a Contoso irá querer estender o aplicativo.

- Os desenvolvedores da Contoso estão criando um protótipo de um novo aplicativo .NET Core, que será executado no cluster do Service Fabric.
- O aplicativo será usado para extrair dados de sentimento do CosmosDB.
- Esses dados serão ser na forma de Tweets que são processados usando uma função sem servidor do Azure e a API de Análise de Texto de Serviços Cognitivos.

### <a name="provision-azure-cosmos-db"></a>Provisionar um Azure Cosmos DB

Como primeira etapa, os administradores da Contoso provisionam um Azure Cosmos DB.

1. Eles criam um recurso do Azure Cosmos DB no Azure Marketplace.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Eles fornecem um nome de banco de dados (**contososmarthotel**), selecionam a API do SQL e colocam o recurso no grupo de recursos de produção, na região Leste dos EUA 2 primária.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. Em **Introdução**, eles selecionam **Data Explorer** e adicionam uma nova coleção.
4. Em **Adicionar Coleção**, eles fornecem IDs e definem a capacidade de armazenamento e a taxa de transferência.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. No portal, eles abrem o novo banco de dados > **Coleção** > **Documentos** e clicam em **Novo Documento**.
6. Eles colam o código JSON a seguir na janela do documento. Esses são dados de exemplo na forma de um único tweet.

    ```json
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel360",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Eles localizam o ponto de extremidade do Cosmos DB e a chave de autenticação. Eles são usados no aplicativo para se conectar à coleção. No banco de dados, clicam em **Chaves** e copiam o URI e a chave primária para o Bloco de Notas.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Atualizar o aplicativo de sentimento

Com o Cosmos DB provisionado, os administradores da Contoso podem configurar o aplicativo para se conectar a ela.

1. No Visual Studio, eles abrem o arquivo ApplicationModern\ApplicationParameters\cloud.xml no Gerenciador de Soluções.

    ![Aplicativo de sentimento](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Eles preenchem os dois parâmetros a seguir:

   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Aplicativo de sentimento](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Republicar o aplicativo

Após estender o aplicativo, os administradores da Contoso o republicam no Azure usando o pipeline.

1. Eles confirmam e enviam o código por push ao Azure DevOps Services. Isso inicia os pipelines de build e de lançamento.

2. Após a conclusão da construção e implantação, o SmartHotel360 estará executando o Service Fabric. O console de Gerenciamento do Service Fabric agora mostra três serviços.

    ![Republicar](./media/contoso-migration-rearchitect-container-sql/republish3.png)

3. Agora eles podem clicar nos serviços para ver que o aplicativo SentimentIntegration está funcionando

    ![Republicar](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Limpar após a migração

Após a migração, a Contoso precisa concluir estas etapas de limpeza:  

- Remova as VMs locais do inventário do vCenter.
- Remova as VMs das tarefas de backup locais.
- Atualize a documentação interna para mostrar os novos locais para o aplicativo SmartHotel360. Mostre o banco de dados como em execução no Banco de Dados SQL do Azure e o front-end como em execução no Service Fabric.
- Revise todos os recursos que interagem com as VMs desatribuídas e atualize quaisquer configurações ou documentação relevantes para refletir a nova configuração.


## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente sua nova infraestrutura.

### <a name="security"></a>Segurança

- Os administradores da Contoso precisam garantir que seu novo banco de dados **SmartHotel-Registration** é seguro. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Em particular, eles devem atualizar o contêiner para usar SSL com certificados.
- Eles devem considerar usar o KeyVault para proteger os segredos para seus aplicativos do Service Fabric. [Saiba mais](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Backups

- A Contoso precisa examinar os requisitos de backup do Banco de Dados SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Os administradores da Contoso devem considerar a implementação de grupos de failover para fornecer failover regional para o banco de dados. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Eles podem aproveitar a replicação geográfica para o SKU premium ACR. [Saiba mais](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- A Contoso precisa considerar a implantação do aplicativo Web na região Leste dos EUA 2 e Centro dos EUA principal quando o Aplicativo Web para Contêineres estiver disponível. Os administradores da Contoso poderiam configurar o Gerenciador de Tráfego para garantir o failover no caso de interrupções regionais.
- O Cosmos DB faz backup automaticamente. [Leia sobre](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) esse processo para saber mais.

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- Depois que todos os recursos estiverem implantados, a Contoso deverá atribuir marcas ao Azure com base no seu [planejamento de infraestrutura](contoso-migration-infrastructure.md#set-up-tagging).
- Todo o licenciamento se baseia no custo dos serviços de PaaS que a Contoso está consumindo. Isso será deduzido do EA.
- A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure.

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso refatorou o aplicativo SmartHotel360 no Azure migrando a VM de front-end do aplicativo para o Service Fabric. O banco de dados do aplicativo foi mirado para um banco de dados SQL do Azure.





