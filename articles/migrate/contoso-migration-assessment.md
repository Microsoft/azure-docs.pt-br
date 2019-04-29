---
title: Avaliar as cargas de trabalho do local para a migração da Contoso para o Azure | Microsoft Docs
description: Saiba como a Contoso avalia os computadores locais para migração para o Azure, usando o Assistente de Migração de Dados e Migrações para Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: raynew
ms.openlocfilehash: 4739308d301291bf88e8ae547ba85f9648339c4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680607"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migração para Contoso: avalie as cargas de trabalho locais para migração para o Azure

Neste artigo, a Contoso avalia o aplicativo SmartHotel360 local para migração para o Azure.

Este artigo é parte de uma série que documenta como a empresa fictícia Contoso migra os recursos locais para a nuvem do Microsoft Azure. A série inclui informações contextuais e cenários de implantação detalhados que ilustram como configurar uma infraestrutura de migração, avaliar a adequação dos recursos locais para migração e executar diferentes tipos de migrações. Cenários aumentam em complexidade. Os artigos serão adicionados à série ao longo do tempo.

Artigo | Detalhes | Status
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos da série. | Disponível
Artigo 3: Avaliar recursos locais para migração para o Azure | A Contoso executa uma avaliação do aplicativo SmartHotel360 local em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados. | Este artigo
[Artigo 4: Hospedar novamente um aplicativo em uma VM do Azure e uma Instância Gerenciada do Banco de Dados SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | A Contoso executa uma migração lift-and-shift para o Azure no aplicativo SmartHotel360 local. Ela migra o front-end do aplicativo, usando o serviço Azure Site Recovery. Ela migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure. | Disponível
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | A Contoso migra as VMs de aplicativos SmartHotel360 para VMs do Azure usando o serviço do Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | A Contoso migra o aplicativo SmartHotel360, usando o Site Recovery para migrar as VMs do aplicativo e o Serviço de migração de banco de dados para migrar o banco de dados do aplicativo para um cluster do SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para VMs do Azure usando o serviço Site Recovery. | Disponível
[Artigo 8: Hospedar novamente um aplicativo do Linux em VMs do Azure e no Banco de Dados do Azure para MySQL](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery. Ele migra o banco de dados do aplicativo para Banco de Dados do Azure para MySQL, usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em um aplicativo Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | A Contoso migra seu aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativos para uma instância do Azure SQL Server com o Assistente de Migração de Banco de Dados. | Disponível
[Artigo 10: Refatorar um aplicativo do Linux em um aplicativo Web do Azure e no Banco de Dados do Azure para MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure usando o Gerenciador de Tráfego do Azure, integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível
[Artigo 11: Refatorar o Team Foundation Server no Azure DevOps Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local para o Azure DevOps Services no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo em contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra o aplicativo SmartHotel para o Azure. Em seguida, ela cria novamente a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados com o Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel usando diversas funcionalidades e serviços do Azure, incluindo o Serviço de Aplicativo do Azure, o AKS (Serviço de Kubernetes do Azure), o Azure Functions, os Serviços Cognitivos do Azure e o Azure Cosmos DB. | Disponível
[Artigo 14: Escalar uma migração para o Azure](contoso-migration-scale.md) | Depois de experimentar combinações de migração, a Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível


## <a name="overview"></a>Visão geral

Como a Contoso considera a migração para Azure, a empresa deseja executar uma avaliação financeira e técnica para determinar se as cargas de trabalho locais são adequadas para migração para a nuvem. Especificamente, a equipe da Contoso deseja avaliar a compatibilidade do banco de dados e de máquina para migração. Além disso, deseja estimar a capacidade e os custos de execução dos recursos da Contoso no Azure.

Para começar e reconhecer melhor as tecnologias envolvidas, a Contoso avalia dois dos aplicativos locais, resumidos na tabela a seguir. A empresa avalia cenários de migração que hospedam novamente e refatoram os aplicativos para migração. Saiba mais sobre a nova hospedagem e refatoração no [visão geral da migração Contoso](contoso-migration-overview.md).

Nome do aplicativo | Plataforma | Camadas do aplicativo | Detalhes
--- | --- | --- | ---
SmartHotel360<br/><br/> (gerencia os requisitos de viagem da Contoso) | Executa no Windows com um banco de dados do SQL Server | Aplicativo de duas camadas. O site ASP.NET front-end é executado em uma VM (**WEBVM**) e o SQL Server é executado em outra VM (**SQLVM**). | As VMs são VMware, executando em um host ESXi gerenciado pelo vCenter Server.<br/><br/> É possível baixar o aplicativo de exemplo do [GitHub](https://github.com/Microsoft/SmartHotel360).
osTicket<br/><br/> (aplicativo da central de serviços da Contoso) | Executa no Linux/Apache com PHP do MySQL (LAMP) | Aplicativo de duas camadas. Um site PHP front-end é executado em uma VM (**OSTICKETWEB**) e o banco de dados MySQL executa em outra VM (**OSTICKETMYSQL**). | O aplicativo é usado por aplicativos de atendimento ao cliente para rastrear problemas para funcionários internos e clientes externos.<br/><br/> É possível baixar o exemplo do [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Arquitetura atual

Este diagrama mostra a infraestrutura local atual da Contoso:

![Arquitetura atual da Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- A Contoso possui um datacenter principal. O datacenter está localizado na cidade de Nova York, no leste dos Estados Unidos.
- A Contoso possui três branches locais adicionais nos Estados Unidos.
- O datacenter principal é conectado à Internet com uma conexão Metro Ethernet em fibra (500 MBps).
- Cada branch é conectado localmente à Internet, usando conexões de classe empresarial com túneis VPN IPsec para o datacenter principal. A configuração permite que toda a rede da Contoso esteja permanentemente conectada e otimiza a conectividade com a Internet.
- O data center principal é totalmente virtualizado com o VMware. A Contoso tem dois hosts de virtualização ESXi 6.5 que são gerenciados pelo vCenter Server 6.5.
- A Contoso usa o Active Directory para gerenciamento de identidade. A Contoso usa servidores DNS na rede interna.
- Executam os controladores de domínio no data center em máquinas virtuais do VMware. Os controladores de domínio no locais branches executados em servidores físicos.

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI da Contoso trabalhou em estreita colaboração com os parceiros de negócios da empresa para reconhecer o que a empresa deseja alcançar com essa migração:

- **Lidando com o crescimento da empresa**: A Contoso está crescendo. Como resultado, a pressão aumentou na infraestrutura e sistemas locais da empresa.
- **Aumentar a eficiência**: a Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários. A empresa precisa que a TI seja rápida e não perca tempo nem dinheiro, de modo que a empresa possa entregar os requisitos do cliente de maneira mais rápida.
- **Aumentar a agilidade**:  A TI da Contoso precisa atender melhor às necessidades empresariais. Para que a empresa seja bem-sucedida em uma economia global, é necessário que seja capaz de reagir mais rápido que as mudanças do mercado. A TI da Contoso não deve atrapalhar ou tornar-se um bloqueador de negócios.
- **Escala**: À medida que os negócios da empresa crescem com êxito, a TI da Contoso deve fornecer sistemas que possam crescer no mesmo ritmo.

## <a name="assessment-goals"></a>Objetivos da avaliação

A equipe de nuvem da Contoso identificou metas para as avaliações de migração:

- Após a migração, os aplicativo no Azure deverão ter os mesmos recursos de desempenho que os aplicativos têm atualmente no ambiente VMWare local da Contoso. Mover para a nuvem não significa que o desempenho do aplicativo é menos crítico.
- A Contoso precisa reconhecer a compatibilidade dos aplicativos e bancos de dados com os requisitos do Azure. A Contoso também precisa reconhecer as opções de hospedagem no Azure.
- A administração do banco de dados da Contoso deverá ser minimizada depois que os aplicativos forem movidos para a nuvem.  
- A Contoso quer reconhecer não apenas as opções de migração, mas também os custos associados à infraestrutura após a migração para a nuvem.

## <a name="assessment-tools"></a>Ferramentas de avaliação

A Contoso usa ferramentas da Microsoft para a avaliação de migração. As ferramentas são alinhadas com os objetivos da empresa e deverão fornecer à Contoso todas as informações necessárias.

Tecnologia | DESCRIÇÃO | Custo
--- | --- | ---
[Assistente de migração de dados](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | A Contoso usa o Assistente de Migração de Dados para avaliar e detectar problemas de compatibilidade que possam afetar a funcionalidade de banco de dados no Azure. O Assistente de Migração de Dados avalia a paridade de recursos entre origens e destinos de SQL. O assistente recomenda melhorias de desempenho e confiabilidade. | O Assistente de Migração de Dados é uma ferramenta gratuita que pode ser baixada.
[Migrações para Azure](https://docs.microsoft.com/azure/migrate/migrate-overview) | A Contoso usa o serviço de Migrações para Azure para avaliar as VMs do VMware. As Migrações para Azure avaliam a adequação de migração das máquinas. Também fornecem estimativas de custos e dimensionamento para execução no Azure.  | A partir de maio de 2018, as Migrações para Azure serão um serviço gratuito.
[Mapa do Serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | As Migrações para Azure usam Mapa do Serviço para mostrar dependências entre os computadores que a empresa deseja migrar. | Mapa do serviço é parte dos logs do Azure Monitor. Atualmente, a Contoso pode usar Mapa do Serviço por 180 dias sem incorrer em encargos.

Nesse cenário, a Contoso baixa e executa o Assistente de Migração de Dados para avaliar o banco de dados do SQL Server local para o aplicativo de viagem. A Contoso usa as Migrações para Azure com o mapeamento de dependência para avaliar as VMs do aplicativo antes da migração para Azure.

## <a name="assessment-architecture"></a>Arquitetura de avaliação

![Arquitetura de avaliação da migração](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- A Contoso é um nome fictício que representa uma organização empresarial típica.
- A Contoso tem um datacenter local (**contoso-datacenter**) e controladores de domínio locais (**CONTOSODC1**, **CONTOSODC2**).
- As VMs do VMware estão localizadas em hosts VMware ESXi executando a versão 6.5 (**contosohost1**, **contosohost2**).
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com** em execução em uma VM).
- O aplicativo de viagem SmartHotel360 possui estas características:
    - O aplicativo é em camadas em duas VMs do VMware (**WEBVM** e **SQLVM**).
    - As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com**.
    - As máquinas virtuais estiverem executando o Windows Server 2008 R2 Datacenter com SP1.
- O ambiente VMware é gerenciado pelo vCenter Server (**vcenter.contoso.com**) em execução em uma VM.
- Aplicativo da central de serviços do osTicket:
    - O aplicativo é em camadas em duas VMs (**OSTICKETWEB** e **OSTICKETMYSQL**).
    - As VMs estão executando Ubuntu Linux Server 16.04-LTS.
    - **OSTICKETWEB** está executando Apache 2 e PHP 7.0.
    - **OSTICKETMYSQL** está executando MySQL 5.7.22.

## <a name="prerequisites"></a>Pré-requisitos

A Contoso e outros usuários devem atender aos seguintes pré-requisitos para a avaliação:

- Permissões de Proprietário ou Colaborador para a assinatura do Azure ou para um grupo de recursos na assinatura do Azure.
- Uma instância do vCenter Server local executando a versão 6.5, 6.0 ou 5.5.
- Uma conta somente leitura no vCenter Server ou permissões para criar uma.
- Permissões para criar uma VM na instância do vCenter Server usando um modelo .ova.
- Pelo menos um host ESXi executando a versão 5.5 ou posterior.
- Pelo menos duas VMs VMware locais, uma delas executando um banco de dados do SQL Server.
- Permissões para instalar agentes de migrar do Azure em cada VM.
- As VMs devem ter conectividade direta com a Internet.  
    - Você pode restringir o acesso à Internet aos [URLs obrigatórios](https://docs.microsoft.com/azure/migrate/concepts-collector).  
    - Se suas VMs não tiverem conectividade com a Internet, o [Gateway de Log Analytics do Azure](../azure-monitor/platform/gateway.md) deverá ser instalado nelas e o tráfego de agentes será direcionado por meio dela.
- O FQDN da VM que está executando a instância do SQL Server, para a avaliação do banco de dados.
- O Firewall do Windows em execução na VM do SQL Server deve permitir conexões externas na porta TCP 1433 (padrão). Esta configuração permite que o Assistente de Migração de Dados seja conectado.

## <a name="assessment-overview"></a>Visão geral da avaliação

Veja como a Contoso realiza a avaliação:

> [!div class="checklist"]
> * **Etapa 1: Baixar e instalar o Assistente de Migração de Dados**: a Contoso prepara o Assistente de Migração de Dados para avaliação do banco de dados do SQL Server local.
> * **Etapa 2: Avaliar o banco de dados usando o Assistente de Migração de Dados**: a Contoso executa e analisa a avaliação do banco de dados.
> * **Etapa 3: Preparar para avaliação de VM usando as Migrações para Azure**: a Contoso configura contas locais e ajusta as configurações do VMware.
> * **Etapa 4: Descobrir VMs locais usando as Migrações para Azure**: a Contoso cria uma VM coletora das Migrações para Azure. Em seguida, a Contoso executa o coletor para descobrir VMs para avaliação.
> * **Etapa 5: Preparar-se para análise de dependência usando as Migrações para Azure**: a Contoso instala agentes de Migrações para Azure nas VMs, de modo que a empresa possa ver o mapeamento de dependência entre as VMs.
> * **Etapa 6: Avaliar as VMs usando as Migrações para Azure**: a Contoso verifica as dependências, agrupa as VMs e executa a avaliação. Quando a avaliação estiver pronta, a Contoso analisará a avaliação em preparação para migração.

## <a name="step-1-download-and-install-data-migration-assistant"></a>Etapa 1: Baixar e instalar o Assistente de Migração de Dados

1. A Contoso baixa o Assistente de Migração de Dados do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - O Assistente de Migração de Dados pode ser instalado em qualquer computador que possa conectar a instância do SQL Server. A Contoso não precisa executá-lo no computador SQL Server.
    - O Assistente de Migração de Dados não deve ser executado no computador host do SQL Server.
2. A Contoso executa o arquivo de instalação baixado (DownloadMigrationAssistant.msi) para iniciar a instalação.
3. Na página **Concluir**, a Contoso seleciona **Inicialização do Assistente de Migração de Dados da Microsoft** antes de concluir o assistente.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel360"></a>Etapa 2: Executar e analisar a avaliação do banco de dados do SmartHotel360

Agora, a Contoso pode executar uma avaliação para analisar o banco de dados do SQL Server local para o aplicativo SmartHotel360.

1. No Assistente de Migração de Dados, a Contoso seleciona **Nova** > **Avaliação** e, em seguida, fornece à avaliação um nome de projeto.
2. Para **Tipo de servidor de origem**, a Contoso seleciona **SQL Server em Máquinas Virtuais do Azure**.

    ![Assistente de Migração de Dados - Selecionar fonte](./media/contoso-migration-assessment/dma-assessment-1.png)

   > [!NOTE]
   >    Atualmente, o Assistente de Migração de Dados não dá suporte à avaliação para migrar para uma Instância Gerenciada do Banco de Dados SQL do Azure. Como solução alternativa, a Contoso usa o SQL Server em uma VM do Azure como o suposto destino da avaliação.

3. Em **Selecionar versão de destino**, a Contoso seleciona o SQL Server 2017 como a versão de destino. A Contoso precisa selecionar essa versão porque é a versão usada pela Instância Gerenciada do Banco de Dados SQL.
4. A Contoso seleciona relatórios para ajudá-lo a descobrir informações sobre compatibilidade e novos recursos:
   - Os **Problemas de Compatibilidade** observam alterações que podem interromper a migração ou que exigem um ajuste menor antes da migração. Este relatório mantém a Contoso informada sobre quaisquer recursos atualmente em uso que estejam preteridos. Os problemas são organizados por nível de compatibilidade.
   - **Recomendação de novos recursos** notas novos recursos na plataforma do SQL Server de destino que pode ser usado para o banco de dados após a migração. As novas recomendações de recursos estão organizadas sob os títulos **Desempenho**, **Segurança** e **Armazenamento**.

     ![Assistente de Migração de Dados - Problemas de compatibilidade e novos recursos](./media/contoso-migration-assessment/dma-assessment-2.png)

2. Em **Conectar um servidor**, a Contoso insere o nome da VM que está executando o banco de dados e as credenciais para acessá-lo. A Contoso seleciona **Certificado do Servidor Confiável** para garantir que a VM possa acessar o SQL Server. Em seguida, a Contoso seleciona **Conectar**.

    ![Assistente de Migração de Dados - Conectar um servidor](./media/contoso-migration-assessment/dma-assessment-3.png)

3. Em **Adicionar origem**, a Contoso adiciona o banco de dados que deseja avaliar e, em seguida, seleciona **Avançar** para iniciar a avaliação.
4. A avaliação é criada.

    ![Assistente de Migração de Dados - Criar avaliação](./media/contoso-migration-assessment/dma-assessment-4.png)

5. Em **Examinar Resultados**, a Contoso exibe os resultados da avaliação.

### <a name="analyze-the-database-assessment"></a>Analisar a avaliação do banco de dados

Resultados são exibidos como elas estão disponíveis. Se a Contoso corrigir problemas, será necessário selecionar **Reiniciar Avaliação** para executar novamente a avaliação.

1. No relatório **Problemas de compatibilidade** a Contoso verifica se há algum problema em cada nível de compatibilidade. Os níveis de compatibilidade mapeiam para as versões do SQL Server da seguinte forma:

   - 100: SQL Server 2008/Banco de Dados SQL do Azure
   - 110: SQL Server 2012/Banco de Dados SQL do Azure
   - 120: SQL Server 2014/Banco de Dados SQL do Azure
   - 130: SQL Server 2016/Banco de Dados SQL do Azure
   - 140: SQL Server 2017/Banco de Dados SQL do Azure

     ![Assistente de Migração de Dados - Relatório de problemas de compatibilidade](./media/contoso-migration-assessment/dma-assessment-5.png)

2. No relatório **Recomendações de recurso**, a Contoso exibe recursos de desempenho, segurança e armazenamento que a avaliação recomenda após a migração. É recomendável uma variedade de recursos, incluindo OLTP in-memory, índices columnstore, Stretch Database, Always Encrypted, Máscara de Dados Dinâmicos e criptografia de dados transparente.

    ![Assistente de Migração de Dados - Relatório de recomendações de recursos](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > A Contoso deve [habilitar a criptografia de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) para todos os bancos de dados do SQL Server. Isso é ainda mais crítico quando um banco de dados está na nuvem do que quando está hospedado no local. A criptografia de dados transparente deverá ser habilitada somente após a migração. Se a criptografia de dados transparente já estiver habilitada, a Contoso deverá mover o certificado ou a chave assimétrica para o banco de dados mestre do servidor de destino. Saiba como [mover um banco de dados protegido por criptografia de dados transparente para outra instância do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. A Contoso pode exportar a avaliação no formato JSON ou CSV.

> [!NOTE]
> Para avaliações em larga escala:
> - Execute várias avaliações simultaneamente e exiba o estado das avaliações na página **Todas Avaliações**.
> - Consolide as avaliações em um [Banco de dados do SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017).
> - Consolide as avaliações em um [relatório do Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>Etapa 3: Preparar para avaliação de VM usando as Migrações para Azure

A Contoso precisa criar uma conta do VMware que as Migrações para Azure possam usar para descobrir automaticamente as VMs para avaliação, verificar direitos para criar uma VM, anotar as portas que precisam ser abertas e definir o nível das configurações de estatísticas.

### <a name="set-up-a-vmware-account"></a>Configurar uma conta do VMware

A descoberta de VM exige uma conta somente leitura no vCenter Server que tenha as propriedades a seguir:

- **Tipo de usuário**: Pelo menos um usuário somente leitura.
- **Permissões**: para o objeto de datacenter, marque a caixa de seleção **Propagar para Objetos Filho**. Para **Função**, selecione **Somente leitura**.
- **Detalhes**: o usuário é atribuído no nível do datacenter, com acesso a todos os objetos no datacenter.
- Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, datastores, VMs e redes).

### <a name="verify-permissions-to-create-a-vm"></a>Verificar permissões para criar uma VM

A Contoso verifica se há permissões para criar uma VM importando um arquivo no formato .ova. Saiba como [criar e atribuir uma função com privilégios](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Verificar portas

A avaliação da Contoso usa o mapeamento de dependência. O mapeamento de dependência requer que um agente seja instalado nas VMs que serão avaliadas. O agente deve poder conectar-se ao Azure da porta TCP 443 em cada VM. Saiba mais sobre [requisitos de conexão](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).

### <a name="set-statistics-settings"></a>Definir configurações de estatísticas

Antes da Contoso iniciar a implantação, será necessário definir as configurações de estatísticas do vCenter Server como nível 3.

> [!NOTE]
> - Após definir o nível, a Contoso deverá aguardar pelo menos um dia antes de executar a avaliação. Caso contrário, a avaliação pode não funcionar conforme o esperado.
> - Se o nível for maior que 3, a avaliação funcionará, mas:
>    - Dados de desempenho para discos e redes não são coletados.
>    - Em relação ao armazenamento, as Migrações para Azure recomendam um disco padrão no Azure, com o mesmo tamanho do disco local.
>    - Para rede, para cada adaptador de rede local, é recomendável um adaptador de rede no Azure.
>    - Para computação, as Migrações para Azure analisam o tamanho da memória e os núcleos da VM e recomenda uma VM do Azure com a mesma configuração. Se houver vários discos qualificados, será recomendado aquele com o menor custo.
> - Para obter mais informações sobre o dimensionamento usando o nível 3, consulte [Dimensionamento](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

Para definir o nível:

1. No cliente Web vSphere, a Contoso abre a instância do vCenter Server.
2. A Contoso selecione **Gerenciar** > **Configurações** > **Geral** > **Editar**.
3. Em **Estatísticas**, a Contoso define as configurações de nível de estatística para **Nível 3**.

    ![Nível de estatísticas do vCenter Server](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>Etapa 4: Descobrir VMs

Para descobrir as VMs, a Contoso cria um projeto de migração do Azure. A Contoso baixa e configura a VM do coletor. Em seguida, a Contoso executa o coletor para descobrir as VMs locais.

### <a name="create-a-project"></a>Criar um projeto

1. No [portal do Azure](https://portal.azure.com), a Contoso pesquisa **Migrações para Azure**. Em seguida, a Contoso cria um projeto.
2. A Contoso especifica um nome de projeto (**ContosoMigration**) e a assinatura do Azure. Ela cria um novo grupo de recursos do Azure (**ContosoFailoverRG**).
    > [!NOTE]
    > - É possível criar um projeto de Migrações para Azure somente na região Centro-Oeste dos EUA ou Leste dos EUA.
    > - Você pode planejar uma migração para qualquer local de destino.
    > - O local do projeto é usado apenas para armazenar os metadados coletados das VMs locais.

    ![Migrações para Azure - Criar projeto de migração](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>Baixar o dispositivo coletor

As Migrações para Azure cria uma VM local conhecida como o *dispositivo do coletor*. A VM descobre VMs do VMware locais e envia metadados sobre as VMs para o serviço de Migrações para Azure. Para configurar o dispositivo do coletor, a Contoso baixa um modelo OVA e, em seguida, importa-o para a instância do vCenter Server no local para criar a VM.

1. No projeto de Migrações para Azure, a Contoso seleciona **Introdução** > **Descobrir e Avaliar** > **Descobrir Computadores**. A Contoso baixa o arquivo de modelo OVA.
2. A Contoso copia a chave e ID do projeto. O projeto e a ID são necessários para configurar o coletor.

    ![Migrações para Azure - Baixar o arquivo OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificar o dispositivo coletor

Antes de implantar a VM, a Contoso verifica se o arquivo OVA é seguro:

1. No computador onde o arquivo foi baixado, a Contoso abrirá uma janela do Prompt de Comando do administrador.
2. A Contoso executa o comando a seguir para gerar o hash para o arquivo OVA:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    **Exemplo**

    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder os valores de hash listados [aqui](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#continuous-discovery).

### <a name="create-the-collector-appliance"></a>Criar o dispositivo coletor

Agora, a Contoso pode importar o arquivo baixado para a instância do vCenter Server e provisionar a VM do dispositivo coletor:

1. No console do vSphere Client, a Contoso seleciona **Arquivo** > **Implantar o modelo de OVF**.

    ![Cliente Web vSphere - Implantar o modelo de OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. Em Implante o assistente de modelo de OVF, a Contoso seleciona **Origem** e, em seguida, especifica o local do arquivo OVA.
3. Em **Nome e Local**, a Contoso especifica um nome de exibição para a VM do coletor. Em seguida, ele seleciona o local do inventário no qual hospedar a VM. A Contoso também especifica o host ou cluster no qual executar o coletor.
4. Em **Armazenamento**, a Contoso especifica o local de armazenamento. Em **Formato de Disco**, a Contoso seleciona como quer provisionar o armazenamento.
5. Em **Mapeamento de Rede**, a Contoso especifica a rede na qual conectar a VM do coletor. A rede precisa de conectividade com a Internet para poder enviar metadados para o Azure.
6. A Contoso analisa as configurações e, em seguida, seleciona **Ligar após implantação** > **Concluir**. Uma mensagem que confirma a conclusão com êxito aparece quando o dispositivo é criado.

### <a name="run-the-collector-to-discover-vms"></a>Executar o coletor para descobrir VMs

Agora, a Contoso executa o coletor para descobrir VMs. Atualmente, o coletor dá suporte apenas em **Inglês (Estados Unidos)** como o idioma do sistema operacional e idioma da interface do coletor.

1. No console do Cliente vSphere, a Contoso seleciona **Abrir Console**. A Contoso especifica as preferências de idioma, fuso horário e senha para a VM do coletor.
2. Na área de trabalho, a Contoso seleciona o atalho **Executar coletor**.

    ![Console do cliente vSphere - Atalho do coletor](./media/contoso-migration-assessment/collector-shortcut.png)

3. No Coletor de Migrações para Azure, a Contoso seleciona **Configurar pré-requisitos**. A Contoso aceita os termos de licença e lê as informações de terceiros.
4. O coletor verifica se a VM tem acesso à Internet, se o horário está sincronizado e se o serviço do coletor está em execução. (O serviço de coletor é instalado por padrão na VM.) A Contoso também instala o PowerCLI do VMware.

    > [!NOTE]
    > Supõe-se que a VM tenha acesso direto à Internet sem usar um proxy.

    ![Coletor de Migrações para Azure - verificar os pré-requisitos](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. Em **Especificar detalhes do vCenter Server**, a Contoso insere o nome (FQDN) ou endereço IP da instância do vCenter Server e as credenciais somente leitura usadas para a descoberta.
6. A Contoso seleciona um escopo para descoberta de VM. O coletor somente pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específico. O escopo não deve conter mais de 1.500 VMs.

    ![Especificar detalhes do vCenter Server](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. Em **Especificar projeto de migração**, a Contoso insere a chave e ID do projeto de Migrações para Azure que foram copiados do portal. Para obter a chave e ID do projeto, a Contoso pode acessar a página de **Visão Geral** > **Descobrir Computadores**.  

    ![Especificar projeto de migração](./media/contoso-migration-assessment/collector-connect-azure.png)

8. Em **Exibir andamento da coleta**, a Contoso pode monitorar a descoberta e verificar se os metadados coletados das VMs estão no escopo. O coletor fornece um tempo aproximado de descoberta.

    ![Exibir andamento da coleta](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Quando a coleta é concluída, a Contoso verifica se as VMs aparecem no portal:

1. No projeto de Migrações para Azure, a Contoso seleciona **Gerenciar** > **Computadores**. A Contoso verifica se as VMs que deseja descobrir são mostradas.

    ![Migrações para Azure - Computadores descobertos](./media/contoso-migration-assessment/discovery-complete.png)

2. Atualmente, os computadores não têm os agentes de Migrações para Azure instalados. A Contoso deve instalar os agentes para exibir dependências.

    ![Migrações para Azure - a instalação do agente é necessária](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>Etapa 5: Preparar-se para análise de dependências

Para exibir dependências entre as VMs que deseja acessar, a Contoso baixa e instala agentes nas VMs do aplicativo. A Contoso instala agentes em todas as VMs para os aplicativos, tanto para Windows quanto para Linux.

### <a name="take-a-snapshot"></a>Tirar um instantâneo

Para manter uma cópia das VMs antes de modificá-las, a Contoso faz um instantâneo antes que os agentes sejam instalados.

![Instantâneo da máquina](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM

1. Em **Computadores**, a Contoso seleciona o computador. Na coluna **Dependências**, a Contoso seleciona **Exige instalação**.
2. No painel **Descobrir Computadores**, Contoso:
    - Baixa o MMA (Microsoft Monitoring Agent) e Dependency Agent para cada VM do Windows.
    - Baixa o MMA e Dependency Agent para cada VM do Linux.
3. A Contoso copia a chave e ID do workspace. A Contoso precisa da chave e ID do workspace quando instala o MMA.

    ![Download do agente](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Instalar os agentes em máquinas virtuais do Windows

A Contoso executa a instalação em cada VM.

#### <a name="install-the-mma-on-windows-vms"></a>Instale o MMA em VMs do Windows

1. Contoso clica duas vezes no agente baixado.
2. Em **Pasta de Destino**, a Contoso mantém a pasta de instalação padrão e, em seguida, seleciona **Avançar**.
3. Em **Opções de configuração do agente**, a Contoso seleciona **Conectar o agente ao Azure Log Analytics** > **Avançar**.

    ![Configuração do Microsoft Monitoring Agent - Opções de configuração do agente](./media/contoso-migration-assessment/mma-install.png)

4. No **Azure Log Analytics**, a Contoso cola a chave e ID do workspace que copiou do portal.

    ![Configuração do Microsoft Monitoring Agent - Azure Log Analytics](./media/contoso-migration-assessment/mma-install2.png)

5. Em **Pronto para Instalar**, a Contoso instala o MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Instale o agente de dependência em VMs do Windows

1. A Contoso clica duas vezes no Dependency Agent baixado.
2. A Contoso aceita os termos de licença e aguarda a conclusão da instalação.

    ![Configuração do Dependency Agent - Instalação](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Instalar os agentes em VMs do Linux

A Contoso executa a instalação em cada VM.

#### <a name="install-the-mma-on-linux-vms"></a>Instale o MMA em VMs do Linux

1. A Contoso instala a biblioteca ctypes do Python em cada VM usando o comando a seguir:

    `sudo apt-get install python-ctypeslib`
2. A Contoso deve executar o comando para instalar o agente MMA como raiz. Para tornar-se raiz, a Contoso executa o comando a seguir e insere a senha raiz:

    `sudo -i`
3. Contoso instala o MMA:
   - A Contoso insere a ID do workspace e digita o comando.
   - Os comandos são para a versão de 64 bits.
   - A ID do espaço de trabalho e a chave primária está localizada no espaço de trabalho do Log Analytics no portal do Azure. Selecione **Configurações** e, em seguida, selecione a guia **Fontes Conectadas**.
   - Execute os comandos a seguir para fazer o download do agente do Log Analytics, validar a soma de verificação e instalar e integrar o agente:

     ```
     wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
     ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>Instalar o Dependency Agent em VMs do Linux

Depois que o MMA é instalado, a Contoso instala o Dependency Agent nas VMs do Linux:

1. O Dependency Agent é instalado em computadores Linux usando InstallDependencyAgent-Linux64.bin, um script de shell que tem um binário autoextraível. A Contoso executa o arquivo usando sh ou adiciona permissões de execução ao próprio arquivo.

2. A Contoso instala o Dependency Agent do Linux como raiz:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Etapa 6: Executar e analisar a avaliação de VMs

Agora, a Contoso pode verificar dependências de máquina e criar um grupo. Em seguida, executa a avaliação para o grupo.

### <a name="verify-dependencies-and-create-a-group"></a>Verifique as dependências e crie um grupo

1. Para determinar quais computadores analisar, a Contoso seleciona **Exibir Dependências**.

    ![Migrações para Azure - Exibir dependências de computador](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Para SQLVM, o mapa de dependências mostra os seguintes detalhes:

    - Processar grupos ou processos que tenham conexões de rede ativas em execução no SQLVM durante o período de tempo especificado (uma hora, por padrão).
    - Conexões TCP de entrada (cliente) e de saída (servidor) de e para todas as máquinas dependentes.
    - Computadores dependentes que têm agentes de Migrações para Azure instalados são mostrados como caixas separadas.
    - Computadores que não têm agentes instalados mostram informações de porta e endereço IP.

3. Para computadores com o agente instalado (WEBVM), a Contoso seleciona a caixa do computador para exibir mais informações. As informações incluem o FQDN, sistema operacional e endereço MAC.

    ![Migrações para Azure - Exibir dependências de grupo](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. A Contoso seleciona as VMs para adicionar ao grupo (SQLVM e WEBVM). A Contoso usa Ctrl+Clique para selecionar várias VMs.
5. A Contoso seleciona **Criar Grupo** e, em seguida, insere um nome (**smarthotelapp**).

    > [!NOTE]
    > Para exibir dependências mais granulares, você pode expandir o intervalo de tempo. É possível selecionar uma duração específica ou selecionar datas de início e término.

### <a name="run-an-assessment"></a>Ler uma avaliação

1. Em **Grupos**, a Contoso abre o grupo (**smarthotelapp**) e, em seguida, seleciona **Criar avaliação**.

    ![Migrações para Azure - Criar uma avaliação](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Para exibir a avaliação, a Contoso seleciona **Gerenciar** > **Avaliações**.

A Contoso usa as configurações de avaliação padrão, mas é possível [personalizar configurações](how-to-modify-assessment.md).

### <a name="analyze-the-vm-assessment"></a>Analisar a avaliação de VMs

Uma avaliação de Migrações para Azure inclui informações sobre a compatibilidade do local com o Azure, o tamanho correto sugerido para VM do Azure e os custos mensais estimados do Azure.

![Migrações para Azure - Relatório de avaliação](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Revisar classificação de confiança

![Migrações para Azure - Exibição de avaliação](./media/contoso-migration-assessment/assessment-display.png)

Uma avaliação tem uma classificação de confiança de 1 estrela a 5 estrelas (1 estrela é a menor e 5 é a maior).
- A classificação de confiança é atribuída a uma avaliação baseada na disponibilidade de pontos de dados necessários para calcular a avaliação.
- A classificação ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure.
- A classificação de confiança é útil quando você realiza *dimensionamento com base no desempenho*. As Migrações para Azure podem não ter pontos de dados suficientes para dimensionamento com base na utilização. Para um dimensionamento *como local*, a classificação de confiança sempre é de 5 estrelas, pois as Migrações para Azure têm todos os pontos de dados necessários para dimensionar a VM.
- Dependendo do percentual dos pontos de dados disponíveis, é fornecida a classificação de confiança para a avaliação:

   Disponibilidade de pontos de dados | Classificação de confiança
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

#### <a name="verify-azure-readiness"></a>Verificar a preparação para o Azure

![Migrações para Azure - Preparação para avaliação](./media/contoso-migration-assessment/azure-readiness.png)  

O relatório de avaliação mostra as informações resumidas na tabela. Para mostrar o dimensionamento com base no desempenho, a Migrações para Azure precisa das seguintes informações. Se as informações não puderem ser coletadas, a avaliação de dimensionamento pode não ser exata.

- Dados de utilização de CPU e memória.
- IOPS de leitura/gravação e a taxa de transferência para cada disco anexado à VM.
- Informações de entrada/saída de rede para cada adaptador de rede conectado à VM.

Configuração | Indicação | Detalhes
--- | --- | ---
**Preparação da VM do Azure** | Indica se a VM está pronta para migração. | Possíveis estados:</br><br/>- Pronto para o Azure<br/><br/>- Pronto com condições <br/><br/>- Não pronto para o Azure<br/><br/>- Preparação desconhecida<br/><br/> Se uma VM não estiver pronta, as Migrações para Azure mostrará algumas etapas de correção.
**Tamanho da VM do Azure** | Para VMs prontas, as Migrações para Azure fornecem uma recomendação de tamanho da VM do Azure. | As recomendações de dimensionamento dependem das propriedades da avaliação:<br/><br/>- Se você usou o dimensionamento com base no desempenho, o dimensionamento considerará o histórico de desempenho das VMs.<br/><br/>- Se você utilizar *como local*, o dimensionamento é baseado no tamanho da VM local e os dados de utilização não são usados.
**Ferramenta sugerida** | Como as máquinas do Azure estão executando os agentes, as Migrações para Azure analisam os processos em execução dentro da máquina. Ele identifica se a máquina é uma máquina de banco de dados.
**Informações da VM** | O relatório mostra as configurações da VM local, incluindo o sistema operacional, o tipo de inicialização e as informações de disco e armazenamento.

#### <a name="review-monthly-cost-estimates"></a>Revisar estimativas de custo mensal

Essa exibição mostra o custo total de computação e armazenamento da execução das VMs no Azure. Também mostra detalhes para cada máquina.

![Migrações para Azure - Custos do Azure](./media/contoso-migration-assessment/azure-costs.png)

- As estimativas de custo são calculadas usando as recomendações de tamanho de uma máquina.
- Os custos mensais estimados de computação e armazenamento são agregados para todas as VMs no grupo.

## <a name="clean-up-after-assessment"></a>Limpeza após a avaliação

- Quando a avaliação termina, a Contoso retém o dispositivo das Migrações para Azure para uso em futuras avaliações.
- A Contoso desativa a VM do VMware. A Contoso a usará novamente quando avaliar VMs adicionais.
- A Contoso mantém o projeto **Migração da Contoso** no Azure. O projeto atualmente é implantado no grupo de recursos **ContosoFailoverRG** grupo de recursos na região do Azure do Leste dos EUA.
-  A VM do coletor tem uma licença de avaliação de 180 dias. Se esse limite expirar, a Contoso precisará baixar o coletor e configurá-lo novamente.

## <a name="conclusion"></a>Conclusão

Nesse cenário, a Contoso avalia o banco de dados do aplicativo SmartHotel360 usando a ferramenta de Avaliação de Migração de Dados. Ele avalia as VMs locais usando o serviço de Migrações para Azure. A Contoso revisa as avaliações para garantir que os recursos locais estejam prontos para migração para o Azure.

## <a name="next-steps"></a>Próximas etapas

No próximo artigo da série, a Contoso hospeda novamente o aplicativo SmartHotel360 no Azure usando uma migração lift-and-shift. A Contoso migra o WEBVM front-end para o aplicativo usando o Azure Site Recovery. Ele migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados. [Introdução](contoso-migration-rehost-vm-sql-managed-instance.md) com essa implantação.
