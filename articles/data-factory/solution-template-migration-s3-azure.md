---
title: Migrar dados do Amazon S3 para o Azure Data Lake Storage Gen2
description: Saiba como usar um modelo de solução para migrar dados do Amazon S3 usando uma tabela de controle externo para armazenar uma lista de partições no AWS S3 com o Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: c1fd4cb248abdc219c6ee5d098e10c329826c160
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361971"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrar dados do Amazon S3 para o Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Use os modelos para migrar petabytes de dados que consistem em centenas de milhões de arquivos do Amazon S3 para Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Se você quiser copiar o volume de dados pequenos do AWS S3 para o Azure (por exemplo, menos de 10 TB), será mais eficiente e fácil usar a [ferramenta de copiar dados de Azure data Factory](copy-data-tool.md). O modelo descrito neste artigo é mais do que o que você precisa.

## <a name="about-the-solution-templates"></a>Sobre os modelos de solução

A partição de dados é recomendada especialmente ao migrar mais de 10 TB de dados. Para particionar os dados, aproveite a configuração ' prefix ' para filtrar as pastas e os arquivos no Amazon S3 por nome e, em seguida, cada trabalho de cópia do ADF pode copiar uma partição de cada vez. Você pode executar vários trabalhos de cópia do ADF simultaneamente para obter uma melhor taxa de transferência.

A migração de dados normalmente exige uma migração de dados histórica única, além de sincronizar periodicamente as alterações do AWS S3 para o Azure. Há dois modelos abaixo, em que um modelo cobre a migração de dados históricos única e outro modelo aborda a sincronização das alterações do AWS S3 para o Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo migrar dados históricos do Amazon S3 para Azure Data Lake Storage Gen2

Este modelo (*nome do modelo: migrar dados históricos do AWS S3 para o Azure data Lake Storage Gen2*) pressupõe que você escreveu uma lista de partições em uma tabela de controle externa no banco de dados SQL do Azure. Portanto, ele usará uma atividade de *pesquisa* para recuperar a lista de partições da tabela de controle externa, iterar em cada partição e fazer com que cada trabalho de cópia do ADF Copie uma partição por vez. Após a conclusão de qualquer trabalho de cópia, ele usa a atividade de *procedimento armazenado* para atualizar o status da cópia de cada partição na tabela de controle.

O modelo contém cinco atividades:
- A **pesquisa** recupera as partições que não foram copiadas para Azure data Lake Storage Gen2 de uma tabela de controle externa. O nome da tabela é *s3_partition_control_table* e a consulta para carregar dados da tabela é *"selecionar PartitionPrefix de S3_partition_control_table em que SuccessOrFailure = 0"*.
- **Foreach** Obtém a lista de partições da atividade de *pesquisa* e itera cada partição para a atividade *TriggerCopy* . Você pode definir o *batchCount* para executar vários trabalhos de cópia do ADF simultaneamente. Definimos 2 neste modelo.
- **ExecutePipeline** executa o pipeline *CopyFolderPartitionFromS3* . O motivo pelo qual criamos outro pipeline para fazer cada trabalho de cópia copiar uma partição é porque ele facilitará a execução do trabalho de cópia com falha para recarregar essa partição específica novamente do AWS S3. Todos os outros trabalhos de cópia que carregam outras partições não serão afetados.
- **Copiar** copia cada partição do AWS S3 para Azure data Lake Storage Gen2.
- **SqlServerStoredProcedure** atualize o status da cópia de cada partição na tabela de controle.

O modelo contém dois parâmetros:
- **AWS_S3_bucketName** é o nome do Bucket no AWS S3 para o qual você deseja migrar dados. Se você quiser migrar dados de vários buckets no AWS S3, poderá adicionar mais uma coluna em sua tabela de controle externo para armazenar o nome do Bucket para cada partição e também atualizar seu pipeline para recuperar dados dessa coluna adequadamente.
- **Azure_Storage_fileSystem** é o nome do sistema de arquivos no Azure data Lake Storage Gen2 em que você deseja migrar dados.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo copiar arquivos alterados somente do Amazon S3 para Azure Data Lake Storage Gen2

Este modelo (*nome do modelo: copiar dados Delta do AWS S3 para Azure data Lake Storage Gen2*) usa LastModifiedTime de cada arquivo para copiar os arquivos novos ou atualizados somente do AWS S3 para o Azure. Lembre-se de que os arquivos ou pastas já foram particionados com as informações da fatia de tempo como parte do nome do arquivo ou da pasta no AWS S3 (por exemplo,/yyyy/mm/dd/file.csv), você pode ir para este [tutorial](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) para obter a abordagem mais eficaz para o carregamento incremental de novos arquivos. Este modelo pressupõe que você escreveu uma lista de partições em uma tabela de controle externa no banco de dados SQL do Azure. Portanto, ele usará uma atividade de *pesquisa* para recuperar a lista de partições da tabela de controle externa, iterar em cada partição e fazer com que cada trabalho de cópia do ADF Copie uma partição por vez. Quando cada trabalho de cópia começa a copiar os arquivos do AWS S3, ele se baseia na propriedade LastModifiedtime para identificar e copiar somente os arquivos novos ou atualizados. Após a conclusão de qualquer trabalho de cópia, ele usa a atividade de *procedimento armazenado* para atualizar o status da cópia de cada partição na tabela de controle.

O modelo contém sete atividades:
- A **pesquisa** recupera as partições de uma tabela de controle externa. O nome da tabela é *s3_partition_delta_control_table* e a consulta para carregar dados da tabela é *"selecionar PartitionPrefix distintos do s3_partition_delta_control_table"*.
- **Foreach** Obtém a lista de partições da atividade de *pesquisa* e itera cada partição para a atividade *TriggerDeltaCopy* . Você pode definir o *batchCount* para executar vários trabalhos de cópia do ADF simultaneamente. Definimos 2 neste modelo.
- **ExecutePipeline** executa o pipeline *DeltaCopyFolderPartitionFromS3* . O motivo pelo qual criamos outro pipeline para fazer cada trabalho de cópia copiar uma partição é porque ele facilitará a execução do trabalho de cópia com falha para recarregar essa partição específica novamente do AWS S3. Todos os outros trabalhos de cópia que carregam outras partições não serão afetados.
- A **pesquisa** recupera a última hora de execução do trabalho de cópia da tabela de controle externa para que os arquivos novos ou atualizados possam ser identificados por meio de LastModifiedTime. O nome da tabela é *s3_partition_delta_control_table* e a consulta para carregar dados da tabela é *"Select Max (JobRunTime) como lastmodifiedtime de S3_partition_delta_control_table em que PartitionPrefix = ' @ {pipeline (). Parameters. prefixStr} ' e SuccessOrFailure = 1"*.
- **Copy** copia somente arquivos novos ou alterados para cada partição do AWS S3 para Azure data Lake Storage Gen2. A propriedade de *modifiedDatetimeStart* é definida como o tempo de execução da última cópia do trabalho. A propriedade de *modifiedDatetimeEnd* é definida como o tempo de execução do trabalho de cópia atual. Lembre-se de que o tempo é aplicado ao fuso horário UTC.
- **SqlServerStoredProcedure** atualize o status da cópia de cada partição e o tempo de execução da cópia na tabela de controle quando tiver sucesso. A coluna de SuccessOrFailure é definida como 1.
- **SqlServerStoredProcedure** atualize o status da cópia de cada partição e o tempo de execução da cópia na tabela de controle quando ela falhar. A coluna de SuccessOrFailure é definida como 0.

O modelo contém dois parâmetros:
- **AWS_S3_bucketName** é o nome do Bucket no AWS S3 para o qual você deseja migrar dados. Se você quiser migrar dados de vários buckets no AWS S3, poderá adicionar mais uma coluna em sua tabela de controle externo para armazenar o nome do Bucket para cada partição e também atualizar seu pipeline para recuperar dados dessa coluna adequadamente.
- **Azure_Storage_fileSystem** é o nome do sistema de arquivos no Azure data Lake Storage Gen2 em que você deseja migrar dados.

## <a name="how-to-use-these-two-solution-templates"></a>Como usar esses dois modelos de solução

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo migrar dados históricos do Amazon S3 para Azure Data Lake Storage Gen2 

1. Crie uma tabela de controle no banco de dados SQL do Azure para armazenar a lista de partições do AWS S3. 

    > [!NOTE]
    > O nome da tabela é s3_partition_control_table.
    > O esquema da tabela de controle é PartitionPrefix e SuccessOrFailure, em que PartitionPrefix é a configuração de prefixo em S3 para filtrar as pastas e os arquivos no Amazon S3 por nome e SuccessOrFailure é o status da cópia de cada partição: 0 significa que essa partição não foi copiada para o Azure e 1 significa que essa partição foi copiada para o Azure com êxito.
    > Há 5 partições definidas na tabela de controle e o status padrão da cópia de cada partição é 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Crie um procedimento armazenado no mesmo banco de dados SQL do Azure para a tabela de controle. 

    > [!NOTE]
    > O nome do procedimento armazenado é sp_update_partition_success. Ele será invocado pela atividade SqlServerStoredProcedure em seu pipeline do ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Vá para o modelo **migrar dados históricos do AWS S3 para Azure data Lake Storage Gen2** . Insira as conexões com sua tabela de controle externa, AWS S3 como o armazenamento de fonte de dados e Azure Data Lake Storage Gen2 como o armazenamento de destino. Lembre-se de que a tabela de controle externo e o procedimento armazenado são referenciados para a mesma conexão.

    ![Captura de tela que mostra a migração de dados históricos do AWS S3 para Azure Data Lake Storage Gen2 modelo.](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Selecione **Usar este modelo**.

    ![Captura de tela que realça o botão usar este modelo.](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Você verá os 2 pipelines e 3 conjuntos de valores foram criados, conforme mostrado no exemplo a seguir:

    ![Captura de tela que mostra os dois pipelines e três conjuntos de valores que foram criados usando o modelo.](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Vá para o pipeline "BulkCopyFromS3" e selecione **depurar**, insira os **parâmetros**. Em seguida, selecione **Concluir**.

    ![Captura de tela que mostra onde selecionar depurar e insira os parâmetros antes de selecionar concluir.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Você vê resultados semelhantes ao exemplo a seguir:

    ![Captura de tela que mostra os resultados retornados.](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para o modelo copiar arquivos alterados somente do Amazon S3 para Azure Data Lake Storage Gen2

1. Crie uma tabela de controle no banco de dados SQL do Azure para armazenar a lista de partições do AWS S3. 

    > [!NOTE]
    > O nome da tabela é s3_partition_delta_control_table.
    > O esquema da tabela de controle é PartitionPrefix, JobRunTime e SuccessOrFailure, em que PartitionPrefix é a configuração de prefixo no S3 para filtrar as pastas e os arquivos no Amazon S3 por nome, JobRunTime é o valor de data e hora quando os trabalhos de cópia são executados e SuccessOrFailure é o status da cópia de cada partição: 0 significa que essa partição não foi copiada no Azure e, por isso, a partição
    > Há 5 partições definidas na tabela de controle. O valor padrão de JobRunTime pode ser o tempo durante o início da migração de dados históricos de um único tempo. A atividade de cópia do ADF copiará os arquivos no AWS S3 que foram modificados pela última vez após esse período. O status padrão da cópia de cada partição é 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Crie um procedimento armazenado no mesmo banco de dados SQL do Azure para a tabela de controle. 

    > [!NOTE]
    > O nome do procedimento armazenado é sp_insert_partition_JobRunTime_success. Ele será invocado pela atividade SqlServerStoredProcedure em seu pipeline do ADF.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Vá para o modelo **copiar dados Delta do AWS S3 para Azure data Lake Storage Gen2** . Insira as conexões com sua tabela de controle externa, AWS S3 como o armazenamento de fonte de dados e Azure Data Lake Storage Gen2 como o armazenamento de destino. Lembre-se de que a tabela de controle externo e o procedimento armazenado são referenciados para a mesma conexão.

    ![Crie uma nova conexão](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Você verá os 2 pipelines e 3 conjuntos de valores foram criados, conforme mostrado no exemplo a seguir:

    ![Revisar o pipeline](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6.  Acesse o pipeline "DeltaCopyFromS3" e selecione **depurar** e insira os **parâmetros**. Em seguida, selecione **Concluir**.

    ![Clique em **Depurar**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Você vê resultados semelhantes ao exemplo a seguir:

    ![Revisar o resultado](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Você também pode verificar os resultados da tabela de controle por uma consulta *"select * from s3_partition_delta_control_table"*, verá a saída semelhante ao exemplo a seguir:

    ![Captura de tela que mostra os resultados da tabela de controle depois de executar a consulta.](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos de vários contêineres](solution-template-copy-files-multiple-containers.md)
- [Mover os arquivos](solution-template-move-files.md)
