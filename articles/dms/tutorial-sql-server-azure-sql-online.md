---
title: 'Tutorial: Usar o Serviço de Migração de Banco de Dados do Azure para migrar online do SQL Server para o banco de dados individual/em pool no Banco de Dados SQL do Azure | Microsoft Docs'
description: Saiba como fazer a migração online do SQL Server local para um banco de dados individual ou em pool no Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/09/2019
ms.openlocfilehash: e5666a64e4160964e2c1b35707a0f064edb72460
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706897"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Tutorial: Migrar o SQL Server para um banco de dados individual ou em pool no Banco de Dados SQL do Azure online usando o DMS

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server local para o [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/) com o mínimo de tempo de inatividade. Neste artigo, você migra o banco de dados **Adventureworks2012** restaurado em uma instância local do SQL Server 2016 (ou posterior) para um banco de dados individual ou um banco de dados em pool no Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Avalie seu banco de dados local usando o Assistente de Migração de Dados.
> - Migre o esquema de exemplo usando o Assistente de Migração de Dados.
> - Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> - Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> - Executar a migração.
> - Monitorar a migração.
> - Baixe um relatório de migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Para saber mais, confira a página de [preços](https://azure.microsoft.com/pricing/details/database-migration/) do Serviço de Migração de Banco de Dados do Azure.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server para um banco de dados individual ou em pool no Banco de Dados SQL do Azure. Para uma migração offline, confira [Migrar o SQL Server para o Banco de Dados SQL do Azure offline usando DMS](tutorial-sql-server-to-azure-sql.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- Baixar e instalar o [SQL Server 2012 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads).
- Habilitar o protocolo TCP/IP, que está desabilitado por padrão durante a instalação do SQL Server Express, seguindo as instruções no artigo [Habilitar ou desabilitar um protocolo de rede de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Crie um banco de dados individual (ou em pool) no Banco de Dados SQL do Azure seguindo os detalhes do artigo [Criar um banco de dados individual no Banco de Dados SQL do Azure usando o portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Se você usar o SSIS (SQL Server Integration Services) e desejar migrar o banco de dados de catálogo de seus projetos/pacotes SSIS (SSISDB) do SQL Server para o Banco de Dados SQL do Azure, o SSISDB de destino será criado e gerenciado automaticamente em seu nome quando você provisionar o SSIS no ADF (Azure Data Factory). Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

- Fazer download e instalar o [AMD](https://www.microsoft.com/download/details.aspx?id=53595) (Assistente de Migração de Dados) v3.3 ou posterior.
- Criar uma VNET (Rede Virtual) do Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma VNET, confira a [Documentação da Rede Virtual](https://docs.microsoft.com/azure/virtual-network/) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da VNET, se você usar ExpressRoute com emparelhamento de rede para Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    > - Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > - Ponto de extremidade de armazenamento
    > - Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a Internet.

- Verifique se as regras do Grupo de Segurança de Rede da VNET não bloqueiam as seguintes portas de comunicação de entrada com o Serviço de Migração de Banco de Dados do Azure: 443, 53, 9354, 445, 12000. Veja mais detalhes sobre a filtragem de tráfego do NSG da VNet do Azure no artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é porta TCP 1433.
- Caso esteja executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez seja preciso habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio de seus firewalls para que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada em seu servidor de origem.
- Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
- Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o servidor do Banco de Dados SQL do Azure para permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-redes da VNET usado para o Serviço de Migração de Banco de Dados do Azure.
- Garantir que as credenciais usadas para se conectar à instância de origem do SQL Server tenham as permissões [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Garantir que as credenciais usadas para se conectar para direcionar a instância do Banco de Dados SQL do Azure tenham a permissão CONTROL DATABASE nos bancos de dados de destino do SQL do Azure.
- A versão do SQL Server de origem precisa ser o SQL Server 2005 ou posterior. Para determinar a versão que a instância do SQL Server está executando, consulte o artigo [Como determinar a versão, a edição e o nível de atualização do SQL Server e de seus componentes](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Os bancos de dados precisam estar no modo de recuperação Bulk-logged ou Completo. Para determinar o modelo de recuperação configurado para sua instância do SQL Server, consulte o artigo [Exibir ou alterar o modelo de recuperação de um Banco de Dados (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017).
- Use os backups de banco de dados completos para os bancos de dados. Para criar um backup de banco de dados completo, confira o artigo [Como criar um backup de banco de dados completo (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)).
- Se alguma tabela não tiver uma chave primária, habilite a CDA (Captura de Dados de Alterações) no banco de dados e nas tabelas específicas.
    > [!NOTE]
    > Você pode usar o script a seguir para encontrar todas as tabelas que não têm chaves primárias.

    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
     ```

    >Se os resultados mostram uma ou mais tabelas com 'is_tracked_by_cdc' como '0', habilite a captura de alteração para o banco de dados e as tabelas específicas usando o processo descrito no artigo [Habilitar e desabilitar a Captura de Dados de Alterações (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017).

- Configure a função de distribuidor para o SQL Server de origem.

    >[!NOTE]
    > Você pode determinar se os componentes de replicação estão instalados usando a consulta abaixo.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    Se o resultado retorna uma mensagem de erro sugerindo a instalação de componentes de replicação, instale os componentes de replicação do SQL Server usando o processo no artigo [Instalar replicação do SQL Server](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017).

    Se a replicação já estiver instalada, verifique se a função de distribuição está configurada no SQL Server de origem usando o comando T-SQL abaixo.

    ```sql
    EXEC sp_get_distributor;
    ```

    Se a distribuição não estiver definida e o servidor de distribuição mostrar NULL para saídas de comando acima, configure a distribuição usando as diretrizes fornecidas no artigo [Configurar Distribuição](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017).

- Desabilite gatilhos de banco de dados no Banco de Dados SQL do Azure de destino.
    >[!NOTE]
    > Você pode encontrar os gatilhos de banco de dados no Banco de Dados SQL do Azure de destino usando a seguinte consulta:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    Para obter mais informações, consulte o artigo [DESABILITAR GATILHO (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>Adicionar um Banco de Dados Local

Antes de poder migrar dados de uma instância do SQL Server local para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, é necessário avaliar o banco de dados do SQL Server para ver se há problemas de bloqueio que possam impedir a migração. Usando o Assistente de Migração de Dados v3.3 ou posterior, siga as etapas descritas no artigo [Realizando uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) para concluir a avaliação de banco de dados local.

Para avaliar um banco de dados local, execute as seguintes etapas:

1. No AMD, selecione o ícone Novo (+) e selecione o tipo de projeto **Avaliação**.
2. Especifique um nome de projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto**Tipo de servidor de destino**, selecione **Banco de Dados SQL do Azure** e, em seguida, selecione **Criar** para criar o projeto.

    Quando você estiver avaliando o banco de dados do SQL Server de origem migrando para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, poderá escolher um ou ambos os seguintes tipos de relatórios de avaliação:

   - Determinar compatibilidade do banco de dados
   - Verificação de paridade de recursos

     Ambos os tipos de relatório são selecionados por padrão.

3. No AMD, na tela **Opções**, selecione **Avançar**.
4. No **selecione fontes** tela, o **conectar a um servidor** caixa de diálogo, forneça os detalhes de conexão ao SQL Server e, em seguida, selecione **conectar**.
5. Na caixa de diálogo **Adicionar origens**, selecione **AdventureWorks2012**, selecione **Adicionar** e, em seguida, selecione **Iniciar Avaliação**.

    > [!NOTE]
    > Se você usa o SSIS, no momento o AMD não dá à avaliação do SSISDB de origem. No entanto, os projetos/pacotes SSIS serão avaliados/validados conforme forem reimplantados no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Quando a avaliação for concluída, os resultados exibem conforme mostrado no gráfico a seguir:

    ![Gerenciar a migração de dados](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    Para bancos de dados individuais ou em pool no Banco de Dados SQL do Azure, as avaliações identificam problemas de paridade de recursos e de bloqueio de migração para implantar em um banco de dados individual ou em pool.

    - A **categoria de paridade de recursos do SQL Server** fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e etapas atenuantes para ajudá-lo a planejar o esforço em seus projetos de migração.
    - A **categoria de problemas de compatibilidade** fornece parcialmente ou recursos sem suporte que refletem os problemas de compatibilidade que podem bloquear a migração de bancos de dados do SQL Server para bancos de dados SQL do Azure locais. Recomendações também são fornecidas para ajudá-lo a resolver esses problemas.

6. Examine os resultados de avaliação para problemas de bloqueio de migração e paridade recurso selecionando as opções específicas.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Depois que você estiver familiarizado com a avaliação e satisfeito de que o banco de dados selecionado é um candidato viável para a migração para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, use o AMD para migrar o esquema para o Banco de Dados SQL do Azure.

> [!NOTE]
> Antes de criar um projeto de migração no AMD, verifique se você já possui um banco de dados SQL do Azure conforme mencionado nos pré-requisitos. Para fins deste tutorial, o nome do Banco de Dados SQL do Azure é considerado **AdventureWorksAzure**, mas você poderá renomeá-lo como desejar.

> [!IMPORTANT]
> Se você usa o SSIS, o AMD não dá suporte no momento à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes SSIS no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Para migrar o esquema **AdventureWorks2012** para um banco de dados individual ou em pool do Banco de Dados SQL do Azure, siga estas etapas:

1. No Assistente de Migração de Dados, selecione o ícone de Novo (+) e, em seguida, em **Tipo de projeto**, selecione **Migração**.
2. Especifique um nome de projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server**e, em seguida, no **tipo de servidor de destino** caixa de texto, selecione **Banco de Dados SQL do Azure**.
3. Em **escopo migração**, selecione **somente esquema**.

    Depois de executar as etapas anteriores, a interface do AMD deve aparecer como mostrado no gráfico abaixo:

    ![Novo projeto do assistente de migração de dados](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. Selecione **Criar** para criar o cluster.
5. No AMD, especifique os detalhes de conexão de origem para o SQL Server, selecione **Conectar**e selecione o banco de dados **AdventureWorks2012**.

    ![Detalhes de Conexão de fonte de Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Selecione **Avançar**; em **Conectar ao servidor de destino**, especifique os detalhes de conexão de destino para o banco de dados SQL do Azure, selecione **Conectar**e selecione o banco de dados **AdventureWorksAzure** que você tinha provisionado previamente no Banco de Dados SQL do Azure.

    ![Detalhes de Conexão de destino Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. Selecione **próximo** para ir para o **selecionar objetos** tela, em que você pode especificar os objetos de esquema no **AdventureWorks2012** banco de dados que precisam ser implantados no Banco de Dados SQL do Azure.

    Por padrão, todos os discos são selecionados.

    ![Gerar Scripts SQL](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. Selecione **script SQL gerar** para criar scripts SQL e, em seguida, examine os scripts de erros.

    ![Script de esquema](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. Selecione **implantar esquema** para implantar o esquema de banco de dados SQL do Azure e, em seguida, depois que o esquema é implantado, verifique o servidor de destino para todas as anomalias.

    ![Implantar o esquema](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>Escolher uma instância

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do Serviço de Migração de Banco de Dados do Azure. 

5. Selecione uma VNet existente ou crie uma.

    A VNet fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao SQL Server de origem e à instância do Banco de Dados SQL do Azure de destino.

    Para mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [Criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. Na tela dos **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

    ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Em **Novo projeto de migração**, especifique um nome de projeto; na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server** e, na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados SQL do Azure**.
5. Na seção **Escolher o tipo de atividade**, selecione **Migração de dados online**.

    ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

6. Clique em **Salvar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    ![Criar e executar a atividade do Serviço de Migração de Banco de Dados](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão da instância do SQL Server de origem.

    Certifique-se de usar um Nome de Domínio Totalmente Qualificado (FQDN) para o nome da instância do SQL Server de origem. Você também pode usar o endereço IP para situações em que a resolução de nome do DNS não é possível.

2. Se você não tiver instalado um certificado confiável no servidor de origem, marque a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões SSL que são criptografadas usando um certificado autoassinado não oferecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no SSL usando certificados autoassinados em um ambiente de produção ou em servidores conectados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Se você usa o SSIS, o DMS não dá suporte no momento à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes SSIS no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Selecione **Salvar**e, na tela **Detalhes de destino da migração**, especifique os detalhes de conexão para o destino do Servidor de Banco de Dados SQL do Azure, que é o Banco de Dados SQL do Azure pré-provisionado no qual o esquema do **AdventureWorks2012**  foi implantado usando o AMD.

    ![Selecionar o destino](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contém o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure seleciona o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. Selecione **Salvar**, na tela **Selecionar tabelas**, expanda a lista de tabela e revise a lista de campos afetados.

    O Serviço de Migração de Banco de Dados do Azure seleciona automaticamente todas as tabelas de origem que existem na instância do Banco de Dados SQL do Azure de destino. Se quiser migrar novamente as tabelas que já contêm dados, você precisará selecionar as tabelas nesta folha de forma explícita.

    ![Selecionar tabelas](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Resumo do Aplicativo](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

- Selecione **Executar migração**.

    A janela de atividade de migração aparece e o **Status** da atividade está **Inicializando**.

    ![Status da atividade: inicializando](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Em execução**.

2. Clique em um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    ![Status da atividade: em andamento](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

    ![Iniciar substituição](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. Pare todas as transações de entrada para o banco de dados de origem; aguarde até que o contador **Alterações pendentes** contador mostre **0**.
3. Selecione **Confirmar**e selecione **Aplicar**.
4. Quando o status de migração de banco de dados mostrar **Concluído**, conecte seus aplicativos ao novo Banco de Dados SQL do Azure de destino.

    ![Status da atividade: concluído](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>Próximas etapas

- Laboratório prático da [migração do SQL usando o Serviço de Migração de Banco de Dados do Azure (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).
- Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados SQL do Azure, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados SQL do Azure](known-issues-azure-sql-online.md).
- Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
- Para saber mais sobre o Banco de Dados SQL do Azure, confira o artigo [O que é o serviço Banco de Dados SQL do Azure?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
