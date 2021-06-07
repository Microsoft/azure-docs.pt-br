---
title: Copiar incrementalmente várias tabelas usando o PowerShell
description: Neste tutorial, você criará um Azure Data Factory com um pipeline que carrega dados delta de várias tabelas em um banco de dados do SQL Server no Banco de Dados SQL do Azure.
ms.author: yexu
author: dearandyxu
ms.reviewer: douglasl, jburchel
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 231f4033b90b152a5893cff93584d9c9bb794321
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783176"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database-using-powershell"></a>Carregar dados de maneira incremental de várias tabelas no SQL Server para o Banco de Dados SQL do Azure usando o PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, você criará um Azure Data Factory com um pipeline que carrega dados delta de várias tabelas em um banco de dados do SQL Server no Banco de Dados SQL do Azure.    

Neste tutorial, você realizará os seguintes procedimentos:

> [!div class="checklist"]
> * Preparará os armazenamentos de dados de origem e destino.
> * Criar um data factory.
> * Criar um runtime de integração auto-hospedada.
> * Instalar o Integration Runtime. 
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Revise os resultados.
> * Adicionar ou atualizar dados nas tabelas de origem.
> * Executar novamente e monitorar o pipeline.
> * Examinar os resultados finais.

## <a name="overview"></a>Visão geral
Aqui estão as etapas importantes ao criar essa solução: 

1. **Selecione a coluna de marca-d'água**.

    Selecione uma coluna para cada tabela no armazenamento de dados de origem, na qual você poderá identificar os registros novos ou atualizados de cada execução. Normalmente, os dados nessa coluna selecionada (por exemplo, ID ou last_modify_time) seguem crescendo quando linhas são criadas ou atualizadas. O valor máximo dessa coluna é usado como uma marca-d'água.

2. **Prepare um armazenamento de dados para armazenar o valor de marca-d'água**.

    Neste tutorial, você deve armazenar o valor de marca-d'água em um banco de dados SQL.

3. **Crie um pipeline com as seguintes atividades**:
    
    a. Crie uma atividade ForEach que itere em uma lista de nomes de tabela de origem passada como um parâmetro para o pipeline. Para cada tabela de origem, são chamadas as próximas atividades necessárias para o carregamento delta.

    b. Crie duas atividades de pesquisa. Use a primeira atividade de Pesquisa para recuperar o último valor de marca-d'água. Use a segunda atividade de Pesquisa para recuperar o novo valor de marca-d'água. Esses valores de marca-d'água são passados para a atividade de Cópia.

    c. Crie uma atividade de cópia que copie linhas do armazenamento de dados de origem com o valor da coluna de marca-d'água maior do que o antigo valor de marca-d'água e menor que o novo valor de marca-d'água. Em seguida, ela copia os dados delta do armazenamento de dados de origem para um Armazenamento de Blobs do Azure como um novo arquivo.

    d. Crie uma atividade de StoredProcedure que atualize o valor de marca-d'água para o pipeline que for executado da próxima vez. 

    A seguir está diagrama da solução de alto nível: 

    ![Carregar dados incrementalmente](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **SQL Server**. Você usa um banco de dados do SQL Server como o armazenamento de dados de origem neste tutorial. 
* **Banco de dados SQL do Azure**. Você usa um banco de dados no Banco de Dados SQL do Azure como o armazenamento de dados do coletor. Se você não tiver um Banco de Dados SQL, confira [Criar um banco de dados no Banco de Dados SQL do Azure](../azure-sql/database/single-database-create-quickstart.md) para saber as etapas para criar um. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Criar tabelas de origem no banco de dados do SQL Server

1. Abra o [SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) ou o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) e conecte-se ao banco de dados do SQL Server.

2. No **SSMS (Gerenciador de Servidores)** ou no **painel Conexões (Azure Data Studio)** , clique com o botão direito do mouse no banco de dados e escolha **Nova Consulta**.

3. Execute o seguinte comando SQL no banco de dados para criar as tabelas `customer_table` e `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Criar tabelas de destino no Banco de Dados SQL do Azure

1. Abra o [SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) ou o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) e conecte-se ao banco de dados do SQL Server.

2. No **SSMS (Gerenciador de Servidores)** ou no **painel Conexões (Azure Data Studio)** , clique com o botão direito do mouse no banco de dados e escolha **Nova Consulta**.

3. Execute o seguinte comando SQL no banco de dados para criar as tabelas `customer_table` e `project_table`:  

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
    ```

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Criar outra tabela no Banco de Dados SQL do Azure para armazenar o valor de marca d'água alta

1. Execute o seguinte comando SQL no banco de dados para criar uma tabela chamada `watermarktable` para armazenar o valor de marca-d'água: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Insira valores de marca d'água iniciais para ambas as tabelas de origem na tabela de marca d'água.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Criar um procedimento armazenado no Banco de Dados SQL do Azure 

Execute o comando a seguir para criar um procedimento armazenado no banco de dados. Esse procedimento armazenado atualiza o valor de marca d'água após cada execução de pipeline. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Criar tipos de dados e procedimentos armazenados adicionais no Banco de Dados SQL do Azure

Execute a consulta a seguir para criar dois tipos de dados e dois procedimentos armazenados no banco de dados. Eles são usados para mesclar os dados das tabelas de origem nas tabelas de destino. 

Para facilitar a jornada de introdução, usamos diretamente esses procedimentos armazenados passando os dados delta por meio de uma variável de tabela e, em seguida, mesclamos esses dados no repositório de destino. Tenha cuidado, pois ele não espera que um número "grande" de linhas delta (mais de 100) seja armazenado na variável de tabela.  

Caso precise mesclar um grande número de linhas delta no repositório de destino, sugerimos que você use a atividade de cópia para copiar todos os dados delta para uma tabela temporária de "preparo" no repositório de destino primeiro e, em seguida, criar seu próprio procedimento armazenado sem o uso da variável de tabela para mesclá-los da tabela de “preparo” para a tabela “final”. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END
```

### <a name="azure-powershell"></a>Azure PowerShell

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções em [Instalar e configurar o Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) entre aspas duplas e, em seguida, execute o comando. Um exemplo é `"adfrg"`.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente.

2. Defina uma variável para o local do data factory. 

    ```powershell
    $location = "East US"
    ```
3. Para criar o grupo de recursos do Azure, execute o seguinte comando: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente.

4. Defina uma variável para o nome do data factory. 

    > [!IMPORTANT]
    >  Atualize o Nome do data factory para que ele seja globalmente exclusivo. Um exemplo seria ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Para criar o data factory, execute o seguinte cmdlet **Set-AzDataFactoryV2**: 
    
    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Observe os seguintes pontos:

* O nome do data factory deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente:

    ```powershell
    Set-AzDataFactoryV2 : HTTP Status Code: Conflict
    Error Code: DataFactoryNameInUse
    Error Message: The specified resource name 'ADFIncMultiCopyTutorialFactory' is already in use. Resource names must be globally unique.
    ```

* Para criar instâncias de Data Factory, a conta de usuário usada para entrar no Azure deve ser um membro das funções colaborador ou proprietário, ou um administrador da assinatura do Azure.

* Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Banco de Dados SQL, Instância Gerenciada de SQL, Armazenamento do Azure etc.) e serviços de computação (Azure HDInsight etc.) usados pelo data factory podem estar em outras regiões.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]

## <a name="create-linked-services"></a>Criar serviços vinculados

Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Nesta seção, você criará serviços vinculados para o banco de dados do SQL Server e o seu banco de dados no Banco de Dados SQL do Azure. 

### <a name="create-the-sql-server-linked-service"></a>Criar um serviço vinculado do SQL Server

Nesta etapa, você vincula seu banco de dados do SQL Server ao data factory.

1. Crie um arquivo JSON chamado **SqlServerLinkedService.json** na pasta C:\ADFTutorials\IncCopyMultiTableTutorial (crie as pastas locais, caso elas ainda não existam) com o conteúdo a seguir. Selecione a seção correta com base na autenticação que você usa para se conectar ao SQL Server.  

    > [!IMPORTANT]
    > Selecione a seção correta com base na autenticação que você usa para se conectar ao SQL Server.

    Se você estiver usando uma autenticação do SQL, copie a seguinte definição de JSON:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Se você estiver usando uma autenticação do Windows, copie a seguinte definição de JSON:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - Selecione a seção correta com base na autenticação que você usa para se conectar ao SQL Server.
    > - Substitua o &lt;integration runtime name> pelo nome do seu Integration Runtime.
    > - Substitua &lt;servername>, &lt;databasename>, &lt;username> e &lt;password> por valores do seu banco de dados do SQL Server antes de salvar o arquivo.
    > - Se precisar usar um caractere de barra (`\`) em sua conta de usuário e nome de servidor, use o caractere de escape (`\`). Um exemplo é `mydomain\\myuser`.

2. No PowerShell, execute o cmdlet a seguir para alternar para a pasta C:\ADFTutorials\IncCopyMultiTableTutorial.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

3. Execute o cmdlet **Set-AzDataFactoryV2LinkedService** para criar o serviço vinculado AzureStorageLinkedService. No exemplo a seguir, você passa valores para os parâmetros *ResourceGroupName* e *DataFactoryName*: 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```console
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Criar um serviço vinculado do Banco de Dados SQL

1. Crie um arquivo JSON chamado **AzureSQLDatabaseLinkedService.json** na pasta C:\ADFTutorials\IncCopyMultiTableTutorial com o conteúdo a seguir. (Crie a pasta ADF se ela não existir). Substitua &lt;nome do servidor&gt;, &lt;nome do banco de dados&gt;, &lt;nome de usuário&gt; e &lt;senha&gt; pelo nome do banco de dados do SQL Server, pelo nome do banco de dados, pelo nome de usuário e pela senha antes de salvar o arquivo. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
2. No PowerShell, execute o cmdlet **Set-AzDataFactoryV2LinkedService** para criar o serviço vinculado AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados

Nesta etapa, você cria conjuntos de dados para representar a fonte de dados, o destino de dados e o local para armazenar a marca d'água.

### <a name="create-a-source-dataset"></a>Criar um conjunto de dados de origem

1. Crie um arquivo JSON chamado **SourceDataset.json** na mesma pasta, com o seguinte conteúdo: 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    A atividade de Cópia no pipeline usa uma consulta SQL para carregar os dados em vez de carregar a tabela inteira.

2. Execute o cmdlet **Set-AzDataFactoryV2Dataset** para criar o conjunto de dados SourceDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Criar um conjunto de dados de coletor

1. Crie um arquivo JSON chamado **SinkDataset.json** na mesma pasta com o conteúdo a seguir. O elemento de tableName é definido pelo pipeline dinamicamente em runtime. A atividade ForEach no pipeline itera por meio de uma lista de nomes de tabela e passa o nome da tabela para esse conjunto de dados em cada iteração. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

2. Execute o cmdlet **Set-AzDataFactoryV2Dataset** para criar o conjunto de dados SinkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Criar um conjunto de dados para uma marca-d'água

Nesta etapa, você deve criar um conjunto de dados para armazenar um valor de marca d'água alta. 

1. Crie um arquivo JSON chamado **WatermarkDataset.json** na mesma pasta com o seguinte conteúdo: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2. Execute o cmdlet **Set-AzDataFactoryV2Dataset** para criar o conjunto de dados WatermarkDataset.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Aqui está a amostra de saída do cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline

O pipeline usa uma lista de nomes de tabela como um parâmetro. A **atividade ForEach** itera na lista de nomes de tabela e executa as seguintes operações: 

1. Use a **atividade de Pesquisa** para recuperar o valor antigo de marca-d'água (o valor inicial ou o que foi usado na última iteração).

2. Use a **atividade de Pesquisa** para recuperar o novo valor de marca-d'água (o valor máximo da coluna de marca-d'água na tabela de origem).

3. Use a **atividade de Cópia** para copiar os dados entre esses dois valores de marca-d'água do banco de dados de origem para o banco de dados de destino.

4. Use a **atividade StoredProcedure** para atualizar o valor antigo de marca-d'água a ser usado na primeira etapa da próxima iteração. 

### <a name="create-the-pipeline"></a>Criar o pipeline

1. Crie um arquivo JSON chamado **IncrementalCopyPipeline.json** na mesma pasta com o seguinte conteúdo: 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
2. Execute o **Set-AzDataFactoryV2Pipeline** para criar o pipeline IncrementalCopyPipeline.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Veja o exemplo de saída: 

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Executar o pipeline

1. Crie um arquivo de parâmetro chamado **Parameters.json** na mesma pasta com o seguinte conteúdo:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
2. Execute o pipeline IncrementalCopyPipeline usando o cmdlet **Invoke-AzDataFactoryV2Pipeline**. Substitua os espaços reservados com seus próprios nomes de grupo de recursos e de data factory.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Entre no [portal do Azure](https://portal.azure.com).

2. Clique em **Todos os serviços**, pesquise com a palavra-chave *Data factories* e selecione **Data factories**. 

3. Procure seu data factory na lista de data factories e selecione-o para abrir a página **Data factory**. 

4. Na página **Data Factory**, selecione **Criar e Monitorar** para iniciar o Azure Data Factory em uma guia separada.

5. Na página **Vamos começar**, selecione **Monitorar** no lado esquerdo. 
![A captura de tela mostra a página Introdução para Azure Data Factory.](media/doc-common-process/get-started-page-monitor-button.png)    

6. Você pode ver todas as execuções de pipeline e seus status. Observe que, no exemplo a seguir, o status da execução de pipeline é **Com Êxito**. Para verificar os parâmetros passados para o pipeline, selecione o link da coluna **Parâmetros**. Se houver um erro, você verá um link na coluna **Erro**.

    ![A captura de tela mostra execuções de pipeline para um data factory incluindo seu pipeline.](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
7. Ao selecionar o link na coluna **Ações**, você verá todas as execuções de atividade para o pipeline. 

8. Para voltar à exibição **Execuções de Pipeline**, selecione **Todas as Execuções de Pipeline**. 

## <a name="review-the-results"></a>Revise os resultados

No SQL Server Management Studio, execute as seguintes consultas no banco de dados SQL de destino para verificar se os dados foram copiados das tabelas de origem para as tabelas de destino: 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados. 

## <a name="add-more-data-to-the-source-tables"></a>Adicionar mais dados às tabelas de origem

Execute a consulta a seguir feita no banco de dados do SQL Server de origem para atualizar uma linha existente na customer_table. Insera uma nova linha em project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Executar novamente o pipeline

1. Agora, execute novamente o pipeline ao executar o seguinte comando do PowerShell:

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Monitore as execuções de pipeline seguindo as instruções da seção [Monitorar o pipeline](#monitor-the-pipeline). Quando o status do pipeline for **Em Andamento**, você verá outro link de ação em **Ações** para cancelar a execução do pipeline. 

3. Selecione **Atualizar** para atualizar a lista até que a execução do pipeline seja bem-sucedida. 

4. Opcionalmente, clique no link **Exibir execuções de atividade** em **Ações** para ver todas as execuções de atividade associadas a esta execução de pipeline. 

## <a name="review-the-final-results"></a>Examine os resultados finais

No SQL Server Management Studio, execute as seguintes consultas no banco de dados de destino para verificar se os dados atualizados/novos foram copiados das tabelas de origem para as tabelas de destino. 

**Consulta** 
```sql
select * from customer_table
```

**Saída**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Observe os novos valores de **Name** e **LastModifytime** para **PersonID** para o número 3. 

**Consulta**

```sql
select * from project_table
```

**Saída**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Observe que a entrada **NewProject** foi adicionada a project_table. 

**Consulta**

```sql
select * from watermarktable
```

**Saída**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observe que os valores de marca d'água para ambas as tabelas foram atualizados.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizará os seguintes procedimentos: 

> [!div class="checklist"]
> * Preparará os armazenamentos de dados de origem e destino.
> * Criar um data factory.
> * Criar um Integration Runtime (IR) auto-hospedado.
> * Instalar o Integration Runtime.
> * Criar serviços vinculados. 
> * Criar os conjuntos de dados de origem, de coletor e de marca-d'água.
> * Crie, execute e monitore um pipeline.
> * Revise os resultados.
> * Adicionar ou atualizar dados nas tabelas de origem.
> * Executar novamente e monitorar o pipeline.
> * Examinar os resultados finais.

Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Carregar incrementalmente os dados do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure usando a tecnologia Controle de Alterações](tutorial-incremental-copy-change-tracking-feature-powershell.md)
