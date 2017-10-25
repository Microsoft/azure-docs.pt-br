---
title: Copiar dados entre o Data Lake Store e o banco de dados SQL do Azure usando o Sqoop | Microsoft Docs
description: "Usar o Sqoop para copiar dados entre o Banco de Dados SQL do Azure e o Repositório Data Lake"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: ac73006f3589bf6d8adb0d7935b694b444d1415c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copiar dados entre o Repositório Data Lake e o banco de dados SQL do Azure usando o Sqoop
Saiba como usar o Apache Sqoop para importar e exportar dados entre o Banco de Dados SQL do Azure e o Repositório Data Lake.

## <a name="what-is-sqoop"></a>O que é o Sqoop?
Os aplicativos de big data são uma opção natural para o processamento de dados semi-estruturados e não estruturados, como logs e arquivos. No entanto, também pode ser necessário processar dados estruturados armazenados em bancos de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e um repositório de Big Data, como o Data Lake Store. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacionais), como o Banco de Dados SQL do Azure no Repositório Data Lake. E, em seguida, transformar e analisar os dados usando cargas de trabalho de big data e exportar os dados de volta para um RDBMS. Neste tutorial, você usa um Banco de Dados SQL do Azure como seu banco de dados relacional para importação/exportação.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md)
* **Cluster HDInsight do Azure** com acesso a uma conta do Repositório Data Lake. Confira [Criar um cluster HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo pressupõe que você tenha um cluster HDInsight Linux com acesso ao Repositório Data Lake.
* **Banco de dados SQL do Azure**. Para saber mais sobre como criar um, confira [Criar um banco de dados SQL do Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Você aprende rapidamente com vídeos?
[Assista a este vídeo](https://mix.office.com/watch/1butcdjxmu114) sobre como copiar dados entre o Repositório do Data Lake e os Blobs de Armazenamento do Azure usando o DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Criar tabelas de exemplo no Banco de Dados SQL do Azure
1. Para começar, crie duas tabelas de exemplo no Banco de Dados SQL do Azure. Use o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para se conectar ao Banco de Dados SQL do Azure e então execute as consultas a seguir.

    **Criar Tabela1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Criar Tabela2**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. Na **Tabela1**, adicione alguns dados de exemplo. Deixe a **Tabela2** vazia. Importaremos dados da **Tabela1** para o Repositório Data Lake. Em seguida, exportaremos dados do Repositório Data Lake Store para a **Tabela2**. Execute o trecho de código a seguir.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Usar o Sqoop de um cluster HDInsight com acesso ao Repositório Azure Data Lake
Um cluster HDInsight já tem os pacotes Sqoop disponíveis. Se você tiver configurado o cluster HDInsight para usar o Repositório Data Lake como armazenamento adicional, poderá usar o Sqoop (sem alterações de configuração) para importar/exportar dados entre um banco de dados relacional (neste exemplo, o Banco de Dados SQL do Azure) e uma conta do Repositório Data Lake.

1. Para este tutorial, vamos supor que você tenha criado um cluster Linux para poder usar o SSH para se conectar ao cluster. Confira [Conectar-se a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Verifique se você pode acessar a conta do Repositório Data Lake do cluster. Execute o comando a seguir do prompt do SSH:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Isso deve fornecer uma lista de arquivos/pastas na conta de Repositório do Data Lake.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importar dados do Banco de Dados SQL do Azure para o Repositório Data Lake
1. Navegue até o diretório onde os pacotes do Sqoop estão disponíveis. Normalmente, será `/usr/hdp/<version>/sqoop/bin`.
2. Importe os dados da **Tabela1** para a conta do Repositório Data Lake. Use a seguinte sintaxe:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Observe que o espaço reservado **sql-database-server-name** representa o nome do servidor onde o banco de dados SQL do Azure está em execução. **sql-database-name** representa o nome do banco de dados real.

    Por exemplo,


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Verifique se os dados foram transferidos para a conta do Repositório Data Lake. Execute o comando a seguir:

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Você deve ver a saída a seguir.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Cada arquivo **part-m-** corresponde a uma linha na tabela de origem, **Table1**. Você pode exibir o conteúdo dos arquivos part-m-* para verificação.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exportar dados do Repositório Data Lake para o Banco de Dados SQL do Azure
1. Exporte os dados da conta do Data Lake Store para a tabela vazia, a **Tabela2**, no Banco de Dados SQL do Azure. Use a sintaxe a seguir.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Por exemplo,


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Verifique se os dados foram carregados na tabela do Banco de Dados SQL. Use o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para se conectar ao Banco de Dados SQL Azure e então execute a consulta a seguir.

        SELECT * FROM TABLE2

    Isso deverá ter a saída a seguir.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Considerações de desempenho ao usar o Sqoop

Para ajustar o desempenho do seu trabalho do Sqoop para copiar dados para o Data Lake Store, consulte [Documento de desempenho do Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Consulte também
* [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
