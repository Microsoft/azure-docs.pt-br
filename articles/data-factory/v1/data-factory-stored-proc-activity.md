---
title: Atividade de procedimento armazenado do SQL Server
description: Saiba como é possível usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um banco de dados SQL do Azure ou SQL Data Warehouse do Azure de um pipeline de Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 77842b60108629168f423f25eb03b01079cf55e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61254908"
---
# <a name="sql-server-stored-procedure-activity"></a>Atividade de procedimento armazenado do SQL Server
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade de Hive](data-factory-hive-activity.md)
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade do Recurso de Atualização do Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade Personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [transformar dados usando a atividade de procedimento armazenado no Data Factory ](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Visão geral
Use atividades de transformação de dados em um [pipeline](data-factory-create-pipelines.md) do Data Factory para transformar e processar dados brutos em previsões e informações. A Atividade de Procedimento Armazenado é uma das atividades de transformação que o Data Factory dá suporte. Este artigo se baseia no artigo sobre [atividades de transformação de dados](data-factory-data-transformation-activities.md), que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte no Data Factory.

Use a Atividade de Procedimento Armazenado para invocar um procedimento armazenado em um dos seguintes armazenamentos de dados em sua empresa ou em uma VM (máquina virtual) do Azure:

- Banco de dados SQL do Azure
- Azure SQL Data Warehouse
- Banco de Dados do SQL Server. Se estiver usando o SQL Server, instale o Gateway de Gerenciamento de Dados no mesmo computador que hospeda o banco de dados ou em um computador separado com acesso ao banco de dados. O Gateway de Gerenciamento de Dados é um componente que conecta fontes de dados locais ou em uma VM do Azure a serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter detalhes.

> [!IMPORTANT]
> Ao copiar dados para o Banco de Dados SQL do Azure ou o SQL Server, configure o **SqlSink** na atividade de cópia para invocar um procedimento armazenado usando a propriedade **sqlWriterStoredProcedureName**. Para obter mais informações, consulte [Invocar um procedimento armazenado por meio da atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para saber mais sobre a propriedade, consulte os seguintes artigos sobre conector: [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Não há suporte para invocar um procedimento armazenado ao copiar dados em um SQL Data Warehouse do Azure usando uma atividade de cópia. Mas, você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um SQL Data Warehouse.
>
> Ao copiar dados do Banco de Dados SQL do Azure, do SQL Server ou do SQL Data Warehouse do Azure, configure **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados do banco de dados de origem usando a propriedade **sqlReaderStoredProcedureName**. Para saber mais, consulte os seguintes artigos sobre conector: [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) e [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

O passo a passo a seguir usa a Atividade de Procedimento Armazenado em um pipeline para invocar um procedimento armazenado em um Banco de Dados SQL do Azure.

## <a name="walkthrough"></a>Passo a passo
### <a name="sample-table-and-stored-procedure"></a>Tabela de exemplo e procedimento armazenado
1. Crie a seguinte **tabela** em seu Banco de Dados SQL do Azure usando o Estúdio de Gerenciamento do SQL Server ou qualquer outra ferramenta com a qual você estiver confortável. A coluna datetimestamp é a data e a hora em que a ID correspondente é gerada.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    ID é a única identificada e a coluna datetimestamp é a data e a hora em que a ID correspondente é gerada.
    
    ![Dados de exemplo](./media/data-factory-stored-proc-activity/sample-data.png)

    Neste exemplo, o procedimento armazenado está em um Banco de Dados SQL do Azure. Se o procedimento armazenado está em um SQL Data Warehouse do Azure e Banco de Dados do SQL Server, a abordagem é semelhante. Para um Banco de Dados do SQL Server, você deve instalar um [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md).
2. Crie o **procedimento armazenado** a seguir que insere dados na **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > O **nome** e o **uso de maiúsculas** do parâmetro (DateTime, neste exemplo) devem corresponder àqueles do parâmetro especificado no JSON do pipeline ou da atividade. Na definição do procedimento armazenado, certifique-se de que **\@** seja usado como um prefixo para o parâmetro.

### <a name="create-a-data-factory"></a>Criar um data factory
1. Faça logon no [portal do Azure](https://portal.azure.com/).
2. Clique em **NOVO** no menu à esquerda, clique em **Inteligência + Análise** e em **Data Factory**.

    ![Novo data factory](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Na folha **Novo data factory**, insira **SProcDF** como o Nome. Os nomes do Azure Data Factory são **globalmente exclusivos**. É preciso prefixar o nome do data factory com seu nome, para habilitar a criação bem-sucedida de fábrica.

   ![Novo data factory](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Selecione sua **assinatura do Azure**.
5. Para **Grupo de Recursos**, realize uma das seguintes etapas:
   1. Clique em **Criar novo** e insira um nome para o grupo de recursos.
   2. Clique em **Usar existente** e selecione um grupo de recursos existente.
6. Selecione o **local** do data factory.
7. Selecione **Fixar no painel** para ver o data factory no painel no próximo logon.
8. Clique em **Criar** na folha **Novo data factory**.
9. Você vê o data factory sendo criado no **painel** do portal do Azure. Após o data factory ter sido criado com êxito, você verá a página do data factory, que exibe seu conteúdo.

   ![Home page do Data Factory](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Criar um serviço vinculado do SQL do Azure
Depois de criar o data factory, crie um serviço vinculado do SQL do Azure que vincula seu Banco de Dados SQL do Azure (o qual contém a tabela sampletable e o procedimento armazenado sp_sample) ao data factory.

1. Clique em **Criar e implantar** na folha **Data Factory** de **SProcDF** para iniciar o Editor do Data Factory.
2. Clique em **Novo armazenamento de dados** na barra de comandos e escolha **Banco de Dados SQL do Azure**. Você deve ver o script JSON para criar um serviço vinculado do SQL Azure no editor.

   ![Novo repositório de dados](media/data-factory-stored-proc-activity/new-data-store.png)
3. No script JSON, faça as seguintes alterações:

   1. Substitua `<servername>` pelo nome do servidor do Banco de Dados SQL do Azure.
   2. Substitua `<databasename>` pelo banco de dados no qual você criou a tabela e o procedimento armazenado.
   3. Substitua `<username@servername>` pela conta de usuário que tem acesso ao banco de dados.
   4. Substitua `<password>` pela senha da conta de usuário.

      ![Novo repositório de dados](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado. Confirme se você vê AzureSqlLinkedService no modo de exibição de árvore à esquerda.

    ![modo de exibição de árvore com serviço vinculado](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
É necessário especificar um conjunto de dados de saída para uma atividade de procedimento armazenado, mesmo que o procedimento armazenado não produza dados. Isso ocorre porque é o conjunto de dados de saída que controla o agendamento da atividade (a frequência com que a atividade é executada – por hora, diária, etc.). O conjunto de dados de saída deve usar um **serviço vinculado** que se refere a um Banco de Dados SQL do Azure, ou SQL Data Warehouse do Azure, ou um Banco de Dados SQL Server no qual você quer que o procedimento armazenado seja executado. O conjunto de dados de saída pode servir como uma maneira de passar o resultado do procedimento armazenado para processamento posterior de outra atividade ([atividades de encadeamento](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) no pipeline. No entanto, o Data Factory não trava automaticamente a saída de um procedimento armazenado para esse conjunto de dados. É o procedimento armazenado que grava em uma tabela SQL para a qual o conjunto de dados de saída aponta. Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício** (um conjunto de dados que aponta para uma tabela que realmente não contém a saída do procedimento armazenado). Esse conjunto de dados fictício é usado somente para especificar o agendamento para a execução da atividade de procedimento armazenado.

1. Clique em **... Mais** na barra de ferramentas, clique em **Novo conjunto de dados** e em **SQL Azure**. **Novo conjunto de dados** na barra de comandos e selecione **SQL Azure**.

    ![modo de exibição de árvore com serviço vinculado](media/data-factory-stored-proc-activity/new-dataset.png)
2. Copie/cole o script JSON a seguir no editor de JSON.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados. Confirme se você vê o conjunto de dados no modo de exibição de árvore.

    ![modo de exibição de árvore com serviços vinculados](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Criar um pipeline com atividade de SqlServerStoredProcedure
Agora, vamos criar um pipeline com uma atividade de procedimento armazenado.

Observe as seguintes propriedades:

- A propriedade **type** está definida como **SqlServerStoredProcedure**.
- O **storedProcedureName** nas propriedades de tipo está definido como **usp_sample** (nome do procedimento armazenado).
- A seção **storedProcedureParameters** contém um parâmetro chamado **DateTime**. O nome e o uso de maiúsculas e minúsculas do parâmetro no JSON devem corresponder ao nome e ao uso de maiúsculas e minúsculas do parâmetro na definição de procedimento armazenado. Se você precisar passar nulo para um parâmetro, use a sintaxe: `"param1": null` (todas as letras minúsculas).

1. Clique em **... Mais**  na barra de comandos e clique em **Novo pipeline**.
2. Copie/cole o seguinte snippet de código JSON:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Clique em **Implantar** na barra de ferramentas para implantar o pipeline.

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline
1. Clique em **X** para fechar as folhas do Editor da Fábrica de Dados e para navegar de volta à folha Fábrica de Dados e clique em **Diagrama**.

    ![bloco do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. Na **Exibição de Diagrama**, você tem uma visão geral dos pipelines e dos conjuntos de dados usados neste tutorial.

    ![bloco do diagrama](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Na Exibição do Diagrama, clique duas vezes no conjunto de dados `sprocsampleout`. Você vê as partes no estado Pronto. Deve haver cinco partes porque uma fatia é produzida para cada hora entre a hora de início e de término no JSON.

    ![bloco do diagrama](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Quando uma fatia estiver no estado **Pronto**, execute uma consulta `select * from sampletable` no Banco de Dados SQL do Azure para verificar se os dados foram inseridos na tabela pelo procedimento armazenado.

   ![Dados de saída](./media/data-factory-stored-proc-activity/output.png)

   Consulte [Monitorar o pipeline](data-factory-monitor-manage-pipelines.md) para obter informações detalhadas sobre o monitoramento de pipelines da Azure Data Factory.

## <a name="specify-an-input-dataset"></a>Especificar um conjunto de dados de entrada
No passo a passo, a atividade de procedimento armazenado não tem nenhum conjunto de dados de entrada. Se você especificar um conjunto de dados de entrada, a atividade de procedimento armazenado só será executada quando a fatia do conjunto de dados de entrada estiver disponível (no estado Pronto). O conjunto de dados pode ser um conjunto de dados externo (que não é produzido por outra atividade no mesmo pipeline) ou um conjunto de dados interno que é produzido por uma atividade upstream (a atividade executada antes dessa atividade). Você pode especificar vários conjuntos de dados de entrada para a atividade de procedimento armazenado. Se você fizer isso, a atividade de procedimento armazenado só será executada quando todas as fatias do conjunto de dados de entrada estiverem disponíveis (no estado Pronto). O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Ele só é usado para verificar a dependência antes de iniciar a atividade de procedimento armazenado.

## <a name="chaining-with-other-activities"></a>Encadeando com outras atividades
Se você deseja encadear uma atividade upstream com essa atividade, especifique a saída da atividade upstream como uma entrada dessa atividade. Quando você fizer isso, a atividade de procedimento armazenado só será executada quando a atividade upstream for concluída e o conjunto de dados de saída da atividade upstream estiver disponível (com o status Pronto). É possível especificar conjuntos de dados de saída de várias atividades upstream como conjuntos de dados de entrada da atividade de procedimento armazenado. Quando você fizer isso, a atividade de procedimento armazenado só será executada quando todas as fatias do conjunto de dados de entrada estiverem disponíveis.

No exemplo a seguir, a saída da atividade de cópia é: OutputDataset, que é uma entrada da atividade de procedimento armazenado. Portanto, a atividade de procedimento armazenado só é executada quando a atividade de cópia é concluída e a fatia de OutputDataset está disponível (no estado Pronto). Se você especificar vários conjuntos de dados de entrada, a atividade de procedimento armazenado só será executada quando todas as fatias do conjunto de dados de entrada estiverem disponíveis (no estado Pronto). Os conjuntos de dados de entrada não podem ser usados diretamente como parâmetros para a atividade de procedimento armazenado.

Para obter mais informações sobre atividades de encadeamento, consulte [várias atividades em um pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Da mesma forma, para vincular a atividade de procedimento armazenado com **atividades downstream** (as atividades executadas após a conclusão da atividade de procedimento armazenado), especifique o conjunto de dados de saída da atividade de procedimento armazenado como uma entrada da atividade downstream no pipeline.

> [!IMPORTANT]
> Ao copiar dados para o Banco de Dados SQL do Azure ou o SQL Server, configure o **SqlSink** na atividade de cópia para invocar um procedimento armazenado usando a propriedade **sqlWriterStoredProcedureName**. Para obter mais informações, consulte [Invocar um procedimento armazenado por meio da atividade de cópia](data-factory-invoke-stored-procedure-from-copy-activity.md). Para saber mais sobre a propriedade, consulte os seguintes artigos sobre conector: [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Ao copiar dados do Banco de Dados SQL do Azure, do SQL Server ou do SQL Data Warehouse do Azure, configure **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados do banco de dados de origem usando a propriedade **sqlReaderStoredProcedureName**. Para saber mais, consulte os seguintes artigos sobre conector: [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) e [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>Formato JSON
Aqui está o formato JSON para definir uma Atividade de Procedimento Armazenado:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

A seguinte tabela descreve essas propriedades JSON:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| nome | Nome da atividade |Sim |
| descrição |Texto que descreve qual a utilidade da atividade |Não |
| tipo | Precisa ser definido como: **SqlServerStoredProcedure** | Sim |
| entradas | Opcional. Se você especificar um conjunto de dados de entrada, ele deverá estar disponível (no status 'Pronto') para a atividade de procedimento armazenado a ser executada. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Ele só é usado para verificar a dependência antes de iniciar a atividade de procedimento armazenado. |Não |
| saídas | Você deve especificar um conjunto de dados de saída para uma atividade de procedimento armazenado. O conjunto de dados de saída especifica a **agenda** da atividade de procedimento armazenado (por hora, semana, mês, etc.). <br/><br/>O conjunto de dados de saída deve usar um **serviço vinculado** que se refere a um Banco de Dados SQL do Azure, ou SQL Data Warehouse do Azure, ou um Banco de Dados SQL Server no qual você quer que o procedimento armazenado seja executado. <br/><br/>O conjunto de dados de saída pode servir como uma maneira de passar o resultado do procedimento armazenado para processamento posterior de outra atividade ([atividades de encadeamento](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) no pipeline. No entanto, o Data Factory não trava automaticamente a saída de um procedimento armazenado para esse conjunto de dados. É o procedimento armazenado que grava em uma tabela SQL para a qual o conjunto de dados de saída aponta. <br/><br/>Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício**, que é usado apenas para especificar o agendamento para execução da atividade de procedimento armazenado. |Sim |
| storedProcedureName |Especifique o nome do procedimento armazenado no banco de dados SQL do Azure, no SQL Data Warehouse do Azure ou no banco de dados do SQL Server que é representado pelo serviço vinculado utilizado pela tabela de saída. |Sim |
| storedProcedureParameters |Especifique valores para parâmetros de procedimento armazenado. Se você precisar passar null para um parâmetro, use a sintaxe: "param1": null (todas as letras minúsculas). Veja o exemplo a seguir para saber mais sobre como usar essa propriedade. |Não |

## <a name="passing-a-static-value"></a>Passando um valor estático
Agora, vamos considerar adicionar outra coluna denominada 'Cenário' na tabela que contém um valor estático chamado 'Exemplo de documento'.

![Dados de exemplo 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabela:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Procedimento armazenado:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Agora, passe o parâmetro **Cenário** e o valor da atividade de procedimento armazenado. A seção **typeProperties** no exemplo acima se parece com o seguinte snippet de código:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Conjunto de dados do Data Factory:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline do Data Factory**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
