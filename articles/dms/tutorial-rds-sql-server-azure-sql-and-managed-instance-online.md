---
title: 'Tutorial: Migrar o SQL Server em RDS online para o Banco de Dados SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como executar uma migração online do SQL Server em RDS para o banco de dados individual ou uma instância gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 249667dfa8c0491027f0244d4aa5e49d19399ab0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955031"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Tutorial: Migrar o SQL Server em RDS para o Banco de Dados SQL do Azure ou para uma Instância Gerenciada de SQL do Azure online usando o DMS

É possível usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server em RDS para o [Banco de Dados SQL do Azure](/azure/sql-database/) ou uma [Instância Gerenciada de SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) com tempo de inatividade mínimo. Neste tutorial, você vai migrar o banco de dados restaurado **Adventureworks2012** para uma instância do SQL Server em RDS do SQL Server 2012 (ou posterior) para um Banco de Dados SQL ou uma Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Crie um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL. 
> * Migre o esquema de exemplo usando o Assistente de Migração de Dados.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.
> * Baixe um relatório de migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Para saber mais, confira a página de [preços](https://azure.microsoft.com/pricing/details/database-migration/) do Serviço de Migração de Banco de Dados do Azure.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server em RDS para o Banco de Dados SQL ou uma Instância Gerenciada de SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Criar um [banco de dados do SQL Server do RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* [Crie um banco de dados em um Banco de Dados SQL do Azure, no portal do Azure](../azure-sql/database/single-database-create-quickstart.md) ou [Crie um banco de dados em uma Instância Gerenciada de SQL](../azure-sql/managed-instance/instance-create-quickstart.md) e, depois, crie um banco de dados vazio chamado **AdventureWorks2012**. 
* Fazer download e instalar o [AMD](https://www.microsoft.com/download/details.aspx?id=53595) (Assistente de Migração de Dados) v3.3 ou posterior.
* Crie uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager. Se estiver migrando para uma Instância Gerenciada de SQL, crie a instância DMS na mesma rede virtual usada para a Instância Gerenciada de SQL, mas em uma sub-rede diferente.  Como alternativa, se você usar uma rede virtual diferente para o DMS, precisará criar um emparelhamento de rede virtual entre as duas redes virtuais. Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a Internet. 

* Verifique se as regras do Grupo de Segurança de Rede da rede virtual não bloqueiam as seguintes portas de comunicação de entrada do Serviço de Migração de Banco de Dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é porta TCP 1433.
* Para Banco de Dados SQL, crie uma [regra de firewall](../azure-sql/database/firewall-configure.md) no nível do servidor a fim de permitir ao Serviço de Migração de Banco de Dados do Azure acesso ao banco de dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
* Verifique se as credenciais usadas para se conectar à instância do SQL Server do RDS de origem estão associadas a uma conta que é membro da função de servidor “Processadmin” e um membro das funções de banco de dados “db_owner” em todos os bancos de dados que devem ser migrados.
* Verifique se as credenciais usadas para se conectar ao banco de dados de destino têm a permissão CONTROL DATABASE sobre o banco de dados de destino no Banco de Dados SQL e se fazem parte da função sysadmin ao migrar para um banco de dados em uma Instância Gerenciada de SQL.
* A versão do SQL Server de origem do RDS deve ser o SQL Server 2012 e posterior. Para determinar a versão que a instância do SQL Server está executando, consulte o artigo [Como determinar a versão, a edição e o nível de atualização do SQL Server e de seus componentes](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Habilite o CDA (Captura de Dados de Alterações) no banco de dados do SQL Server do RDS e em todas as tabelas do usuário selecionadas para migração.
    > [!NOTE]
    > É possível usar o script abaixo para habilitar o CDA em um banco de dados do SQL Server do RDS.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > É possível usar o script abaixo para habilitar o CDA em todas as tabelas.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Desabilite gatilhos de banco de dados no banco de dados de destino.
    > [!NOTE]
    > Você pode encontrar os gatilhos de banco de dados no banco de dados de destino usando a seguinte consulta:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Para obter mais informações, consulte o artigo [DESABILITAR GATILHO (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo
Use o AMD para migrar o esquema.

> [!NOTE]
> Antes de criar um projeto de migração no AMD, verifique se você já provisionou um banco de dados no Banco de Dados SQL ou na Instância Gerenciada de SQL, conforme mencionado nos pré-requisitos. Para fins deste tutorial, o nome do banco de dados será considerado **AdventureWorks2012**, mas será possível renomeá-lo conforme desejar.

Para migrar o esquema do **AdventureWorks2012**, execute as seguintes etapas:

1. No Assistente de Migração de Dados, selecione o ícone de Novo (+) e, em seguida, em **Tipo de projeto**, selecione **Migração**.
2. Especifique um nome de projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server** e, em seguida, no **tipo de servidor de destino** caixa de texto, selecione **Banco de Dados SQL do Azure**.

    > [!NOTE]
    > Em tipo de servidor de destino, selecione **Banco de Dados SQL do Azure** para migrar ambos, Banco de Dados SQL do Azure e Instância Gerenciada de SQL.

3. Em **escopo migração**, selecione **somente esquema**.

    Depois de executar as etapas anteriores, a interface do AMD deve aparecer como mostrado no gráfico abaixo:

    ![Novo projeto do assistente de migração de dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Selecione **Criar** para criar o cluster.
5. No AMD, especifique os detalhes de conexão de origem para o SQL Server, selecione **Conectar** e selecione o banco de dados **AdventureWorks2012**.

    ![Detalhes de Conexão de fonte de Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Selecione **Próximo**, em **Conectar-se ao servidor de destino**, especifique os detalhes da conexão de destino para o banco de dados no Banco de Dados SQL ou na Instância Gerenciada de SQL, selecione **Conectar** e escolha o banco de dados **AdventureWorksAzure** que você provisionou previamente.

    ![Detalhes de Conexão de destino Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Selecione **próximo** para avançar para a tela **Selecionar objetos**, em que você poderá especificar os objetos de esquema do banco de dados **AdventureWorks2012** que precisam ser implantados.

    Por padrão, todos os discos são selecionados.

    ![Gerar Scripts SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Selecione **script SQL gerar** para criar scripts SQL e, em seguida, examine os scripts de erros.

    ![Script de esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Selecione **Implantar esquema** para implantar o esquema e verifique o destino quanto a eventuais anomalias.

    ![Implantar o esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Escolher uma instância

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do Serviço de Migração de Banco de Dados do Azure. 

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao SQL Server de origem e ao Banco de Dados SQL de destino ou à Instância Gerenciada de SQL.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

6. Selecione um tipo de preço; para esta migração online, selecione o tipo de preço Premium.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

     ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Na tela dos **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

     ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Na tela **Novo projeto de migração**, especifique um nome para o projeto; na caixa de texto **Tipo de servidor de origem**, selecione **AWS RDS para SQL Server** e, na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados SQL do Azure**.

    > [!NOTE]
    > Em tipo de servidor de destino, selecione **Banco de Dados SQL do Azure** para migrar ambos, Banco de Dados SQL e Instância Gerenciada de SQL.

5. Na seção **Escolher o tipo de atividade**, selecione **Migração de dados online**.

    > [!IMPORTANT]
    > Verifique se você selecionou **Migração de dados online**; não há suporte para migrações offline para esse cenário.

    ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

6. Clique em **Salvar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    ![Criar e executar a atividade do Serviço de Migração de Banco de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão da instância do SQL Server de origem.

    Certifique-se de usar um Nome de Domínio Totalmente Qualificado (FQDN) para o nome da instância do SQL Server de origem.

2. Se você não tiver instalado um certificado confiável no servidor de origem, marque a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado autoassinado não oferecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados autoassinados em um ambiente de produção, nem em servidores conectados à Internet.

   ![Detalhes da origem](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Selecione **Salvar** e, na tela **Detalhes do destino de migração**, especifique os detalhes de conexão para o banco de dados de destino no Azure.

    ![Selecionar o destino](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contém o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure seleciona o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Selecione **Salvar**, na tela **Selecionar tabelas**, expanda a lista de tabela e revise a lista de campos afetados.

    O Serviço de Migração de Banco de Dados do Azure seleciona automaticamente todas as tabelas de origem vazias que existem no banco de dados de destino. Se desejar migrar novamente as tabelas que já contêm dados, será necessário selecionar as tabelas nesta tela de forma explícita.

    ![Selecionar tabelas](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Selecione **Salvar** após definir as seguintes **Configurações avançadas de migração online**.

    | Configuração | Descrição |
    | ------------- | ------------- |
    | **Número máximo de tabelas a ser carregado em paralelo** | Especifica o número de tabelas que o DMS executa em paralelo durante a migração. O valor padrão é 5, mas pode ser definido como um valor ideal para atender às necessidades de migração específicas com base em quaisquer migrações de POC. |
    | **Quando a tabela de origem é truncada** | Especifica se o DMS trunca a tabela de destino durante a migração. Essa configuração poderá ser útil se uma ou mais tabelas forem truncadas como parte do processo de migração. |
    | **Definir configurações para dados LOB (objetos grandes)** | Especifica se o DMS migra dados LOB ilimitados ou limita os dados LOB migrados a um tamanho específico.  Quando houver um limite nos dados LOB migrados, todos os dados LOB além desse limite serão truncados. Para migrações de produção, é recomendável selecionar **Permitir tamanho de LOB ilimitado** para impedir a perda de dados. Ao especificar essa opção para permitir o tamanho ilimitado de LOB, marque a caixa de seleção **Migrar dados LOB em um único bloco quando o tamanho do LOB for menor que (KB) especificado** para melhorar o desempenho. |

    ![Definir configurações avançadas de migração online](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Resumo do Aplicativo](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

    A janela de atividade de migração aparece e o **Status** da atividade está **Inicializando**.

    ![Status da atividade: inicializando](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Em execução**.

2. Clique em um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    ![Status da atividade: em andamento](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

    ![Iniciar substituição](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Pare todas as transações de entrada para o banco de dados de origem; aguarde até que o contador **Alterações pendentes** contador mostre **0**.
3. Selecione **Confirmar** e selecione **Aplicar**.
4. Quando o status de migração de banco de dados mostrar **Concluído**, conecte os aplicativos ao novo banco de dados de destino.

    ![Status da atividade: concluído](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre problemas e limitações conhecidos ao realizar migrações online ao Azure, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online](known-issues-azure-sql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados, confira o artigo [O que é o Serviço de Migração de Banco de Dados?](./dms-overview.md).
* Para obter informações sobre o Banco de Dados SQL, confira o artigo [O que é o serviço Banco de Dados SQL?](../azure-sql/database/sql-database-paas-overview.md).
* Para obter informações sobre as Instâncias Gerenciada de SQL, confira o artigo [O que é a Instância Gerenciada de SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).