---
title: 'Tutorial: Migrar o SQL Server offline para um Banco de Dados SQL do Azure'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar do SQL Server para o Banco de Dados SQL do Azure offline usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 7bf4ea4beabf9abcc007c101ca5acf79ec919c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105732390"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>Tutorial: Migrar do SQL Server para o Banco de Dados SQL do Azure usando DMS

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server para o [Banco de Dados SQL do Azure](/azure/sql-database/). Neste tutorial, você migra o banco de dados [Adventureworks2016](/sql/samples/adventureworks-install-configure#download-backup-files) restaurado em uma instância local do SQL Server 2016 (ou posterior) para um banco de dados individual ou um banco de dados em pool no Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.

Você saberá como:
> [!div class="checklist"]
>
> - Avaliar seu banco de dados local para quaisquer problemas de bloqueio usando o Assistente de Migração de Dados.
> - Usar o Assistente de Migração de Dados para migrar o esquema de exemplo do banco de dados. 
> - Registrar o provedor de recursos Azure DataMigration.
> - Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> - Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> - Executar a migração.
> - Monitorar a migração.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- Baixar e instalar o [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads).
- Habilitar o protocolo TCP/IP, que está desabilitado por padrão durante a instalação do SQL Server Express, seguindo as instruções no artigo [Habilitar ou desabilitar um protocolo de rede de servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Criar um banco de dados no Banco de Dados SQL do Azure seguindo os detalhes do artigo [Criar um banco de dados no Banco de Dados SQL do Azure usando o portal do Azure](../azure-sql/database/single-database-create-quickstart.md). Para fins deste tutorial, o nome do Banco de Dados SQL do Azure é considerado **AdventureWorksAzure**, mas você poderá renomeá-lo como desejar.

    > [!NOTE]
    > Se você usar o SSIS (SQL Server Integration Services) e desejar migrar o banco de dados de catálogo de seus projetos/pacotes SSIS (SSISDB) do SQL Server para o Banco de Dados SQL do Azure, o SSISDB de destino será criado e gerenciado automaticamente em seu nome quando você provisionar o SSIS no ADF (Azure Data Factory). Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](./how-to-migrate-ssis-packages.md).
  
- Baixe e instale a versão mais recente do [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595).
- Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > - Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > - Ponto de extremidade de armazenamento
    > - Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.
    >
    >Se você não tiver conectividade site a site entre a rede local e o Azure ou se houver uma largura de banda de conectividade site a site limitada, considere usar o Serviço de Migração de Banco de Dados do Azure no modo híbrido (versão prévia). O modo híbrido beneficia-se de um trabalho de migração local junto com uma instância do Serviço de Migração de Banco de Dados do Azure em execução na nuvem. Para criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido, confira o artigo [Criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido usando o portal do Azure](./quickstart-create-data-migration-service-hybrid-portal.md).

- Verifique se as regras de segurança de saída do grupo de segurança de rede da rede virtual não bloqueiam a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG na rede virtual do Azure, confira o artigo [Filtrar o tráfego de rede com os grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que, por padrão, é a porta TCP 1433. Se a instância padrão estiver escutando em alguma outra porta, adicione-a ao firewall.
- Se você estiver executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez precise habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio dos firewalls, de modo que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada no servidor de origem.
- Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
- Crie uma [regra de firewall de IP](../azure-sql/database/firewall-configure.md) no nível do servidor para o Banco de Dados SQL do Azure a fim de permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
- Garantir que as credenciais usadas para se conectar à instância de origem do SQL Server tenham as permissões [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Verifique se as credenciais usadas para se conectar à instância do Banco de Dados SQL do Azure de destino têm a permissão [CONTROL DATABASE](/sql/t-sql/statements/grant-database-permissions-transact-sql) sobre os bancos de dados de destino.

## <a name="assess-your-on-premises-database"></a>Adicionar um Banco de Dados Local

Para migrar os dados de uma instância do SQL Server para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, examine o banco de dados do SQL Server em busca de problemas de bloqueio que possam impedir a migração. Usando o Assistente de Migração de Dados, siga as etapas descritas no artigo [Realizar uma avaliação de migração do SQL Server](/sql/dma/dma-assesssqlonprem) para concluir a avaliação de banco de dados local. Segue um resumo das etapas necessárias:

1. No Assistente de Migração de Dados, selecione o ícone de novo (+) e, em seguida, selecione o **avaliação** tipo de projeto.
2. Especifique um nome do projeto. Da lista suspensa **Tipo de avaliação**, selecione **Mecanismo de Banco de Dados**, na caixa de texto **Tipo do servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados SQL do Azure** e, em seguida, selecione **Criar** para criar o projeto.

    Quando você estiver avaliando o banco de dados do SQL Server de origem migrando para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, poderá escolher um ou ambos os seguintes tipos de relatórios de avaliação:

   - Determinar compatibilidade do banco de dados
   - Verificação de paridade de recursos

    Ambos os tipos de relatório são selecionados por padrão.

3. No Assistente de Migração de Dados, sobre o **opções** tela, selecione **próximo**.
4. No **selecione fontes** tela, o **conectar a um servidor** caixa de diálogo, forneça os detalhes de conexão ao SQL Server e, em seguida, selecione **conectar**.
5. Na caixa de diálogo **Adicionar origens**, selecione **Adventureworks2016**, selecione **Adicionar** e, em seguida, selecione **Iniciar Avaliação**.

    > [!NOTE]
    > Se você usa o SSIS, no momento o AMD não dá à avaliação do SSISDB de origem. No entanto, os projetos/pacotes SSIS serão avaliados/validados conforme forem reimplantados no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](./how-to-migrate-ssis-packages.md).

    Quando a avaliação for concluída, os resultados exibem conforme mostrado no gráfico a seguir:

    ![Gerenciar a migração de dados](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Para os bancos de dados do Banco de Dados SQL do Azure, as avaliações identificam problemas de paridade de recursos e de bloqueio de migração para implantar em um banco de dados individual ou em pool.

    - A **categoria de paridade de recursos do SQL Server** fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e etapas atenuantes para ajudá-lo a planejar o esforço em seus projetos de migração.
    - A categoria **Problemas de compatibilidade** identifica recursos não compatíveis ou parcialmente compatíveis que refletem os problemas de compatibilidade capazes de bloquear a migração dos bancos de dados do SQL Server para o Banco de Dados SQL do Azure. Recomendações também são fornecidas para ajudá-lo a resolver esses problemas.

6. Examine os resultados de avaliação para problemas de bloqueio de migração e paridade recurso selecionando as opções específicas.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Depois que você estiver familiarizado com a avaliação e satisfeito de que o banco de dados selecionado é um candidato viável para a migração para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, use o AMD para migrar o esquema para o Banco de Dados SQL do Azure.

> [!NOTE]
> Antes de criar um projeto de migração no Assistente de Migração de Dados, verifique se você já provisionou um banco de dados no Azure, conforme mencionado nos pré-requisitos. 

> [!IMPORTANT]
> Se você usa o SSIS, o AMD não dá suporte no momento à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes SSIS no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](./how-to-migrate-ssis-packages.md).

Para migrar o esquema **Adventureworks2016** para um banco de dados individual ou um banco de dados em pool do Banco de Dados SQL do Azure, siga estas etapas:

1. No Assistente de Migração de Dados, selecione o ícone de Novo (+) e, em seguida, em **Tipo de projeto**, selecione **Migração**.
2. Especifique um nome de projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server** e, em seguida, no **tipo de servidor de destino** caixa de texto, selecione **Banco de Dados SQL do Azure**.
3. Em **escopo migração**, selecione **somente esquema**.

    Depois de executar as etapas anteriores, a interface do Assistente de Migração de Dados deve aparecer como mostrado no gráfico a seguir:

    ![Novo projeto do assistente de migração de dados](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selecione **Criar** para criar o cluster.
5. No Assistente de Migração de Dados, especifique os detalhes da conexão de origem para o SQL Server, selecione **Conectar** e, em seguida, selecione o banco de dados **Adventureworks2016**.

    ![Detalhes de Conexão de fonte de Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selecione **Avançar**; em **Conectar ao servidor de destino**, especifique os detalhes de conexão de destino para o Banco de Dados SQL do Azure, selecione **Conectar** e selecione o banco de dados **AdventureWorksAzure** que você tinha provisionado previamente no Banco de Dados SQL do Azure.

    ![Detalhes de Conexão de destino Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selecione **Próximo** para ir para a tela **Selecionar objetos**, em que você pode especificar os objetos de esquema no banco de dados **Adventureworks2016** que precisam ser implantados no Banco de Dados SQL do Azure.

    Por padrão, todos os discos são selecionados.

    ![Gerar Scripts SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selecione **script SQL gerar** para criar scripts SQL e, em seguida, examine os scripts de erros.

    ![Script de esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selecione **implantar esquema** para implantar o esquema de banco de dados SQL do Azure e, em seguida, depois que o esquema é implantado, verifique o servidor de destino para todas as anomalias.

    ![Implantar o esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure. Pesquise e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-azure-sql/portal-select-subscription-1.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Pesquise por migração, depois selecione **Registrar** para **Microsoft.DataMigration**.

    ![Registrar provedor de recursos](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Escolher uma instância

1. No menu do portal do Azure ou na **Home page**, selecione **Criar um recurso**. Pesquise e selecione o **Serviço de Migração de Banco de Dados do Azure**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. Na tela básica **Criar Serviço de Migração**:

     - Selecione a assinatura.
     - Crie um grupo de recursos ou escolha um existente.
     - Especifique um nome para a instância do Serviço de Migração de Banco de Dados do Azure.
     - Selecione a localização na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure.
     - Escolha **Azure** como o modo de serviço.
     - Selecione um tipo de preço. Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar a instância básica do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - Selecione **Avançar: Rede**.

4. Na tela de rede **Criar Serviço de Migração**:

    - Selecione uma rede virtual existente ou crie uma. A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao SQL Server de origem e à instância do Banco de Dados SQL do Azure de destino. Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

    ![Configurar uma rede de instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - Selecione **Examinar + Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. No menu do portal do Azure, selecione **Todos os serviços**. Pesquise e selecione os **Serviços de Migração de Banco de Dados do Azure**.

     ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Na tela dos **Serviços de Migração de Banco de Dados do Azure**, selecione a instância do Serviço de Migração de Banco de Dados do Azure que você criou.

3. Selecione **Novo projeto de migração**.

     ![Localizar a instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**; na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados SQL do Azure** e, em **Escolher tipo de atividade de migração**, selecione **Migração de dados**.

    ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Selecionar fonte**, especifique os detalhes da conexão da instância do SQL Server de origem.

    Certifique-se de usar um Nome de Domínio Totalmente Qualificado (FQDN) para o nome da instância do SQL Server de origem. Você também pode usar o endereço IP para situações em que a resolução de nome do DNS não é possível.

2. Se você não tiver instalado um certificado confiável no servidor de origem, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado autoassinado não oferecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados autoassinados em um ambiente de produção, nem em servidores conectados à Internet.

    > [!IMPORTANT]
    > Se você usa o SSIS, o DMS não dá suporte no momento à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes SSIS no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](./how-to-migrate-ssis-packages.md).

   ![Detalhes da origem](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. Selecione **Avançar: selecionar bancos de dados**.

## <a name="select-databases-for-migration"></a>Selecionar bancos de dados para migração

Selecione todos os bancos de dados ou os bancos de dados específicos que deseja migrar para o Banco de Dados SQL do Azure. O DMS informa o tempo de migração esperado dos bancos de dados selecionados. Se os tempos de inatividade de migração forem aceitáveis, dê continuidade à migração. Se o tempo de inatividade da migração não for aceitável, cogite migrar para a [Instância Gerenciada de SQL com tempo de inatividade quase zero](tutorial-sql-server-managed-instance-online.md) ou entre em contato com a [equipe DMS](mailto:DMSFeedback@microsoft.com) para obter outras opções. 

1. Escolha os bancos de dados que deseja migrar da lista de bancos de dados disponíveis. 
1. Analise o tempo de inatividade esperado. Se for aceitável, selecione **Avançar: Selecionar destino >>**

   ![Bancos de dados de origem](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Selecionar destino**, forneça as configurações de autenticação para o Banco de Dados SQL do Azure. 

   ![Selecionar o destino](media/tutorial-sql-server-to-azure-sql/select-target.png)
   
   > [!NOTE]
   > No momento, a autenticação SQL é o único tipo de autenticação com suporte.

1. Selecione **Avançar: Mapear para bancos de dados de destino** e mapeie o banco de dados de origem e de destino para migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. Selecione **Avançar: Configurações de migração**, expanda a lista de tabelas e, em seguida, examine a lista de campos impactados.

    O Serviço de Migração de Banco de Dados do Azure seleciona automaticamente todas as tabelas de origem que existem na instância do Banco de Dados SQL do Azure de destino. Se quiser migrar novamente as tabelas que já contêm dados, você precisará selecionar as tabelas nesta folha de forma explícita.

    ![Selecionar tabelas](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. Selecione **Avançar: Resumo**, examine a configuração de migração e na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

    ![Escolha a opção de validação](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>Execute a migração

- Selecione **Iniciar migração**.

    A janela de atividade de migração aparece e o **Status** da atividade está **Pendente**.

    ![Status da Atividade](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Concluído**.

    ![Status da atividade concluído](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. Verifique os bancos de dados de destino no **Banco de Dados SQL do Azure** de destino.

### <a name="additional-resources"></a>Recursos adicionais

- Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, confira o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](./dms-overview.md).
- Para saber mais sobre o Banco de Dados SQL do Azure, confira o artigo [O que é o serviço Banco de Dados SQL do Azure?](../azure-sql/database/sql-database-paas-overview.md).
