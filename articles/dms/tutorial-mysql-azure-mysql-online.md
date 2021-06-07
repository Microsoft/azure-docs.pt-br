---
title: 'Tutorial: Migrar o MySQL online para o Banco de Dados do Azure para MySQL'
titleSuffix: Azure Database Migration Service
description: Saiba como realizar uma migração online do MySQL local para o Banco de Dados do Azure para MySQL usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 050356dc74641e7e7154d6a3976e6cc8d5f4ce3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063603"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar o MySQL para o Banco de Dados do Azure para MySQL online usando o DMS

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do MySQL local para o [Banco de Dados do Azure para MySQL](../mysql/index.yml) com tempo de inatividade mínimo. Em outras palavras, a migração pode ser feita com o mínimo de tempo de inatividade para o aplicativo. Neste tutorial, você migrará o banco de dados de exemplo **Employees** de uma instância local do MySQL 5.7 para o Banco de Dados do Azure para MySQL usando uma atividade de migração online no Serviço de Migração de Banco de Dados do Azure.

> [!IMPORTANT]
> O cenário de migração online “MySQL para Banco de Dados do Azure para MySQL” será substituído por um cenário de migração offline de alto desempenho e paralelizado em 1º de junho de 2021. Para migrações online, você pode usar essa nova oferta junto com a [Replicação de Dados](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication). Como alternativa, use ferramentas de software livre, como [o MyDumper/o MyLoader](https://centminmod.com/mydumper.html) com a Replicação de Dados para migrações online. 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Migrar o esquema de exemplo usando o utilitário mysqldump.
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

> [!NOTE]
> Comunicação livre de desvio
>
> A Microsoft é compatível com um ambiente diversificado e inclusivo. Este artigo contém referências à palavra _escravo_. O [guia de estilo para comunicação sem desvios](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) da Microsoft reconhece esse termo como uma palavra excludente. A palavra é usada neste artigo para fins de consistência, uma vez que, atualmente, é a palavra que aparece no software. Quando o software for atualizado e esta palavra for removida, este artigo será atualizado para manter o alinhamento.
>


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Baixar e instalar o [MySQL community edition](https://dev.mysql.com/downloads/mysql/) 5.6 ou 5.7. A versão do MySQL local deve corresponder à versão do Banco de Dados do Azure para MySQL. Por exemplo, o MySQL 5.6 só pode migrar para o Banco de Dados do Azure para MySQL 5.6; não pode ser atualizado para 5.7. As migrações para o MySQL 8.0 ou dele não são compatíveis.
* [Criar uma instância no Banco de Dados do Azure para MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Consulte o artigo [Usar o MySQL Workbench para se conectar e consultar dados](../mysql/connect-workbench.md) a fim de obter detalhes sobre como se conectar e criar um banco de dados usando o portal do Azure.  
* Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais por meio do [ExpressRoute](../expressroute/expressroute-introduction.md) ou da [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.

* Verifique se as regras do grupo de segurança de rede da rede virtual não bloqueiam a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o Servidor MySQL de origem, que, por padrão, é a porta TCP 3306.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
* Crie uma [regra de firewall](../azure-sql/database/firewall-configure.md) no nível de servidor para o Banco de Dados do Azure para MySQL a fim de permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
* O MySQL de origem deve estar no MySQL community edition com suporte. Para determinar a versão da instância do MySQL, execute o seguinte comando no utilitário MySQL ou no MySQL Workbench:

    ```
    SELECT @@version;
    ```

* O Banco de Dados do Azure para MySQL dá suporte somente a tabelas do InnoDB. Para converter tabelas do MyISAM para o InnoDB, consulte o artigo [Converter tabelas do MyISAM para o InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

* Habilite registro em log binário no arquivo my.ini (Windows) ou my.cnf (Unix) no banco de dados de origem usando a seguinte configuração:

  * **server_id** = 1 ou superior (relevante apenas para MySQL 5.6)
  * **log-bin** =\<path> (relevante apenas para o MySQL 5.6). Por exemplo: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (é recomendável não usar zero; relevante somente para MySQL 5.6)
  * **Binlog_row_image = full** (relevante apenas para o MySQL 5.6)
  * **log_slave_updates** = 1

* O usuário precisa ter a função ReplicationAdmin com os seguintes privilégios:

  * **CLIENTE DE REPLICAÇÃO**: obrigatório apenas para tarefas de Processamento de Alterações. Em outras palavras, tarefas somente de Carregamento Completo não exigem esse privilégio.
  * **RÉPLICA DE REPLICAÇÃO**: obrigatório apenas para tarefas de Processamento de Alterações. Em outras palavras, tarefas somente de Carregamento Completo não exigem esse privilégio.
  * **SUPER**: obrigatório somente nas versões anteriores a MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de banco de dados, como procedimentos armazenados, índices e esquemas de tabela, é necessário extrair o esquema do banco de dados de origem e aplicar ao banco de dados. Para extrair o esquema, você pode usar mysqldump com o parâmetro `--no-data`.

Supondo que você tenha o banco de dados de **Funcionários** de exemplo do MySQL no sistema local, o comando para fazer a migração de esquema usando mysqldump é:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Por exemplo:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Para importar o esquema no Banco de Dados do Azure para MySQL de destino, execute o seguinte comando:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Por exemplo:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Se você tiver chaves estrangeiras em seu esquema, o carregamento inicial e a sincronização contínua da migração falharão.  Execute o seguinte script no MySQL Workbench para extrair o script de soltar a chave estrangeira e adicionar um script de chave estrangeira.

```sql
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

Execute a chave estrangeira removível (que é a segunda coluna) no resultado da consulta de chave estrangeira removível.

> [!NOTE]
> O DMS do Azure não dá suporte à ação referencial CASCADE, que ajuda a excluir ou atualizar automaticamente uma linha correspondente na tabela filho quando uma linha é excluída ou atualizada na tabela pai. Para obter mais informações, na documentação do MySQL, confira a seção Ações Referenciais do artigo [Restrições de FOREIGN KEY](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html).
> O DMS do Azure exige que você remova as restrições de chave estrangeira do servidor de banco de dados de destino durante o carregamento de dados inicial, e você não pode usar as ações referenciais. Se a sua carga de trabalho depende da atualização de uma tabela filho relacionada por meio dessa ação referencial, recomendamos que você execute [despejo e restauração](../mysql/concepts-migrate-dump-restore.md). 


> [!IMPORTANT]
> Se estiver importando dados usando um backup, remova os comandos CREATE DEFINER manualmente ou usando o comando --skip-definer ao executar um mysqldump. O DEFINER requer superprivilégios para criar e está restrito no Banco de Dados do Azure para MySQL.

Se você tiver um gatilho nos dados (gatilho de inserção ou atualização), ele imporá a integridade de dados no destino antes de nos dados replicados da origem. A recomendação é desabilitar gatilhos em todas as tabelas no destino durante a migração e, em seguida, habilitar os gatilhos depois que a migração é feita.

Para desabilitar gatilhos no banco de dados de destino, use o seguinte comando:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao SQL Server de origem e à instância do Banco de Dados SQL do Azure de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

5. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Na tela **Serviços de Migração de Banco de Dados do Azure**, pesquise o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

     ![Localizar a instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Em **Novo projeto de migração**, especifique um nome de projeto; na caixa de texto **Tipo de servidor de origem**, selecione **MySQL** e, na caixa de texto **Tipo de servidor de destino**, selecione **AzureDbForMySQL**.
5. Na seção **Escolher o tipo de atividade**, selecione **Migração de dados online**

    ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

6. Selecione **Salvar**, observe os requisitos para usar o DMS para migrar dados com êxito e selecione **Criar e executar atividade**.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Adicionar Detalhes da Origem**, especifique os detalhes da conexão da instância do MySQL de origem.

    ![Tela Adicionar Detalhes da Origem](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Selecione **Salvar** e, na tela **Detalhes de destino**, especifique os detalhes de conexão do servidor do Banco de Dados para MySQL de destino, que é a instância previamente provisionada do Banco de Dados do Azure para MySQL na qual o esquema **Employees** foi implantado usando mysqldump.

    ![Tela de detalhes do destino](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Embora você possa selecionar vários bancos de dados nesta etapa, cada instância do Serviço de Migração de Banco de Dados do Azure dá suporte a até quatro bancos de dados para migração simultânea. Além disso, há um limite de dez instâncias do Serviço de Migração de Banco de Dados do Azure por assinatura em cada região. Por exemplo, se você tiver 80 bancos de dados para migrar, só poderá migrar 40 deles simultaneamente para a mesma região, mas somente se tiver criado dez instâncias do Serviço de Migração de Banco de Dados do Azure.

3. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Resumo do Aplicativo](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

    A janela de atividade de migração aparece e o **Status** da atividade está **Inicializando**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Concluído**.

     ![Status da atividade: concluído](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Em **Nome do Banco de Dados**, selecione um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    O carregamento de dados completo mostrará o status de migração da carga inicial e a sincronização de dados incremental mostrará o status da CDC (Captura de Dados de Alterações).

     ![Status da atividade: carregamento completo concluído](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Status da atividade: sincronização de dados incrementais](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

    ![Iniciar substituição](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Pare todas as transações de entrada para o banco de dados de origem; aguarde até que o contador **Alterações pendentes** contador mostre **0**.
3. Selecione **Confirmar** e selecione **Aplicar**.
4. Quando o status de migração de banco de dados mostrar **Concluído**, conecte seus aplicativos ao novo Banco de Dados SQL do Azure de destino.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados do Azure para MySQL, consulte o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados do Azure para MySQL](known-issues-azure-mysql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, confira o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](./dms-overview.md).
* Para obter informações sobre o Banco de Dados do Azure para MySQL, confira o artigo [O que é o Banco de Dados do Azure para MySQL?](../mysql/overview.md).
