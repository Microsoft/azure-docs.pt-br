---
title: Cópia em massa de um banco de dados usando a tabela de controle
description: Saiba como usar um modelo de solução para copiar dados em massa de um banco de dados usando uma tabela de controle externa para armazenar uma lista de partições das tabelas de origem usando o Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: eed7a304bdd57846cd038cc9bf9a67e8150ca505
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392452"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Cópia em massa de um banco de dados com uma tabela de controle

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Para copiar dados de um data warehouse no Oracle Server, Netezza, Teradata ou SQL Server para o Azure Synapse Analytics, você precisa carregar enormes quantidades de dados de várias tabelas. Normalmente, os dados precisam ser particionados em cada tabela para que seja possível carregar linhas com vários threads paralelamente de uma só tabela. Este artigo descreve um modelo a ser usado nesses cenários.

 >! Observação Se você quiser copiar dados de um pequeno número de tabelas com volume de dados relativamente pequeno para o Azure Synapse Analytics, será mais eficiente usar a [ferramenta de copiar dados de Azure data Factory](copy-data-tool.md). O modelo descrito neste artigo é mais do que o necessário para esse cenário.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo recupera uma lista de partições do banco de dados de origem para copiar de uma tabela de controle externa. Em seguida, ele itera em cada partição no banco de dados de origem e copia os dados para o destino.

O modelo contém três atividades:
- **Lookup** recupera a lista de partições de banco de dados de uma tabela de controle externa.
- **ForEach** obtém a lista de partições da atividade Lookup e itera cada partição para a atividade Copy.
- **Copy** copia cada partição do armazenamento de banco de dados de origem para o de destino.

O modelo define os seguintes parâmetros:
- *Control_Table_Name* é a tabela de controle externa, que armazena a lista de partições do banco de dados de origem.
- *Control_Table_Schema_PartitionID* é o nome da coluna em sua tabela de controle externa que armazena cada ID da partição. Verifique se a ID da partição é exclusiva para cada partição no banco de dados de origem.
- *Control_Table_Schema_SourceTableName* é a tabela de controle externa que armazena cada nome de tabela do banco de dados de origem.
- *Control_Table_Schema_FilterQuery* é o nome da coluna em sua tabela de controle externa que armazena a consulta de filtro a fim de obter os dados de cada partição no banco de dados de origem. Por exemplo, se você particionou os dados por ano, a consulta armazenada em cada linha pode ser semelhante a ‘select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''.
- *Data_Destination_Folder_Path* é o caminho em que os dados são copiados no armazenamento de destino (aplicável quando o destino escolhido é o "Sistema de Arquivos" ou o "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* é o caminho da pasta raiz para a qual os dados são copiados em seu armazenamento de destino. 
- *Data_Destination_Directory* é o caminho do diretório na raiz para a qual os dados são copiados para o armazenamento de destino. 

Os últimos três parâmetros que definem o caminho no armazenamento de destino ficam visíveis apenas quando o destino escolhido é um armazenamento baseado em arquivos. Se você escolher "Azure Synapse Analytics" como o armazenamento de destino, esses parâmetros não serão necessários. Mas os nomes de tabela e o esquema na análise de Synapse do Azure devem ser iguais aos do banco de dados de origem.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Crie uma tabela de controle em um SQL Server ou Banco de Dados SQL do Azure para armazenar a lista de partições do banco de dados de origem para a cópia em massa. No exemplo a seguir, há cinco partições no banco de dados de origem. Três partições são para *datasource_table* e duas são para *project_table*. A coluna *LastModifytime* é usada para particionar os dados na tabela *datasource_table* do banco de dados de origem. A consulta usada para ler a primeira partição é 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''. Você pode usar uma consulta semelhante para ler dados de outras partições.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Vá até o modelo **Cópia em massa do banco de dados**. Crie uma **Conexão** com a tabela de controle externa criada na etapa 1.

    ![Criar uma nova conexão com a tabela de controle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Crie uma **Conexão** com o banco de dados de origem do qual você está copiando dados.

    ![Criar uma nova conexão com o banco de dados de origem](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Crie uma **Conexão** com o armazenamento de dados de destino para o qual você está copiando dados.

    ![Criar uma nova conexão com o armazenamento de destino](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecione **Usar este modelo**.

6. Você verá o pipeline, conforme mostrado no seguinte exemplo:

    ![Revisar o pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecione **Depurar**, insira os **Parâmetros** e, em seguida, selecione **Concluir**.

    ![Clique em **Depurar**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Você vê resultados semelhantes ao exemplo a seguir:

    ![Revisar o resultado](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Adicional Se você escolheu "Azure Synapse Analytics" como o destino de dados, deverá inserir uma conexão com o armazenamento de BLOBs do Azure para preparo, conforme exigido pelo polybase do Azure Synapse Analytics. O modelo gera automaticamente um caminho de contêiner para o Armazenamento de Blobs. Verifique se o contêiner foi criado após a execução do pipeline.
    
    ![Configuração do Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
