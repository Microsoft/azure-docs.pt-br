---
title: Copiar de um banco de dados em massa usando-se uma tabela de controle com o Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo de solução para copiar dados em massa de um banco de dados usando uma tabela de controle externo para armazenar uma lista de partição das tabelas de origem usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635304"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Cópia em massa de um banco de dados com uma tabela de controle

Para copiar dados de um data warehouse no servidor Oracle, Netezza, Teradata ou do SQL Server para o Azure SQL Data Warehouse, você precisa carregar grandes quantidades de dados de várias tabelas. Normalmente, os dados precisam ser particionados em cada tabela para que você possa carregar linhas com vários threads em paralelo de uma única tabela. Este artigo descreve um modelo para usar nesses cenários.

 >! Observação Se você quiser copiar dados de um pequeno número de tabelas com o volume de dados relativamente pequeno para o SQL Data Warehouse, é mais eficiente usar a [ferramenta Copiar dados do Azure Data Factory](copy-data-tool.md). O modelo que é descrito neste artigo é mais do que você precisa para esse cenário.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Esse modelo recupera uma lista de partições de banco de dados de origem para copiar de uma tabela externa do controle. Em seguida, ele itera em cada partição no banco de dados de origem e copia os dados para o destino.

O modelo contém três atividades:
- **Pesquisa** recupera a lista de partições de banco de dados-se de uma tabela de controle externo.
- **ForEach** obtém a lista de partição da atividade de pesquisa e itera cada partição para a atividade de cópia.
- **Cópia** copia cada partição do armazenamento de banco de dados de origem para o armazenamento de destino.

O modelo define cinco parâmetros:
- *Control_Table_Name* é sua tabela de controle externo, que armazena a lista de partição para o banco de dados de origem.
- *Control_Table_Schema_PartitionID* é o nome do nome da coluna em sua tabela de controle externo que armazena cada ID da partição. Certifique-se de que a ID de partição é exclusiva para cada partição no banco de dados de origem.
- *Control_Table_Schema_SourceTableName* é sua tabela de controle externo que armazena cada nome de tabela do banco de dados de origem.
- *Control_Table_Schema_FilterQuery* é o nome da coluna na sua tabela de controle externo que armazena a consulta de filtro para obter os dados de cada partição no banco de dados de origem. Por exemplo, se os dados são particionados por ano, a consulta que é armazenada em cada linha pode ser semelhante a ' Selecionar * de fonte de dados onde LastModifytime > = ' 2015-01-01 00:00:00 ' e LastModifytime < = ' 2015-12-31 23:59:59.999 ' '.
- *Data_Destination_Folder_Path* é o caminho onde os dados são copiados para o armazenamento de destino. Esse parâmetro só é visível se o destino que você escolher é o armazenamento baseado em arquivo. Se você escolher o SQL Data Warehouse como o armazenamento de destino, esse parâmetro não é necessário. Mas os nomes de tabela e o esquema no SQL Data Warehouse devem ser o mesmo que aqueles no banco de dados de origem.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Crie uma tabela de controle no SQL Server ou banco de dados SQL para armazenar a lista de partição de banco de dados de origem para cópia em massa. No exemplo a seguir, há cinco partições no banco de dados de origem. Três partições são para o *datasource_table*, e duas servem para o *project_table*. A coluna *LastModifytime* é usado para particionar os dados na tabela *datasource_table* do banco de dados de origem. A consulta que é usada para ler a primeira partição é ' Selecionar * de datasource_table onde LastModifytime > = ' 2015-01-01 00:00:00 ' e LastModifytime < = ' 2015-12-31 23:59:59.999 ' '. Você pode usar uma consulta semelhante para ler dados de outras partições.

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

2. Vá para o **cópia em massa de banco de dados** modelo. Criar uma **New** conexão para a tabela de controle externo que você criou na etapa 1.

    ![Criar uma nova conexão com a tabela de controle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Criar uma **New** conexão à fonte de dados que você estiver copiando dados do.

     ![Criar uma nova conexão com o banco de dados de origem](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Criar uma **New** armazenar de conexão para os dados de destino que você está copiando os dados a serem.

    ![Criar uma nova conexão com o armazenamento de destino](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecione **Use este modelo**.

    ![Usar este modelo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Você vê o pipeline, conforme mostrado no exemplo a seguir:

    ![Revisar o pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecione **Debug**, insira o **parâmetros**e, em seguida, selecione **concluir**.

    ![Clique em * * * * de depuração](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Você verá resultados semelhantes ao exemplo a seguir:

    ![Revisar o resultado](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcional) Se você escolher o SQL Data Warehouse como o destino de dados, você deve inserir uma conexão para o armazenamento de BLOBs do Azure para preparação, conforme exigido pelo Polybase do SQL Data Warehouse. Certifique-se de que o contêiner no armazenamento de BLOBs já foi criado.
    
    ![Configuração do Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
