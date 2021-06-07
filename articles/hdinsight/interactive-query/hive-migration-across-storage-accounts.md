---
title: Migração de carga de trabalho do hive para nova conta no armazenamento do Azure
description: Migração de carga de trabalho do hive para nova conta no armazenamento do Azure
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744318"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Migração de carga de trabalho do hive para nova conta no armazenamento do Azure

Saiba como usar ações de script para copiar tabelas do hive entre contas de armazenamento no HDInsight. Isso pode ser útil ao migrar para o [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Para copiar manualmente uma tabela individual do hive no HDInsight 4,0, confira o [Hive exportar/importar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Pré-requisitos

* Um novo cluster HDInsight com as seguintes configurações:
  * Seu sistema de arquivos padrão está na conta de armazenamento de destino. Consulte [usar o armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md).
  * Sua versão de cluster deve corresponder ao cluster de origem.
  * Ele usa um novo metastore do Hive DB externo. Consulte [usar repositórios de metadados externos](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).
* Uma conta de armazenamento que é acessível para clusters originais e novos. Consulte [Adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md) e [tipos de armazenamento e recursos](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) para tipos de armazenamento secundários permitidos.

    Aqui estão algumas opções:
  * Adicione a conta de armazenamento de destino ao cluster original.
  * Adicione a conta de armazenamento original ao novo cluster.
  * Adicione uma conta de armazenamento intermediária aos clusters novos e originais.

## <a name="how-it-works"></a>Como ele funciona

Executaremos uma ação de script para exportar tabelas hive do cluster original para um diretório HDFS especificado. Consulte [ação de script para um cluster em execução](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Em seguida, executaremos outra ação de script no novo cluster para importar as tabelas do hive do diretório HDFS.

O script recriará as tabelas para o sistema de arquivos padrão do novo cluster. As tabelas nativas também copiarão seus dados no armazenamento. As tabelas não nativas serão copiadas apenas por definição. Consulte [manipuladores de armazenamento do hive](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) para obter detalhes sobre tabelas não nativas.

O caminho das tabelas externas que não estão no diretório do depósito do hive será preservado. Outras tabelas serão copiadas para o caminho do hive padrão do cluster de destino. Consulte Propriedades do hive `hive.metastore.warehouse.external.dir` e `hive.metastore.warehouse.dir` .

Os scripts não preservarão as permissões de arquivo personalizado no cluster de destino.

> [!NOTE]
>
> Este guia dá suporte à cópia de objetos de metadados relacionados a bancos de dados, tabelas e partições do hive. Outros objetos de metadados devem ser recriados manualmente.
>
> * Para o `Views` , o hive dá suporte ao `SHOW VIEWS` comando a partir do hive 2.2.0 no HDInsight 4,0. Use `SHOW CREATE TABLE` para definição de exibição. Para versões anteriores do hive, consulte o BD SQL do metastore para mostrar exibições.
> * Para `Materialized Views` , use comandos `SHOW MATERIALIZED VIEWS` , `DESCRIBE FORMATTED` e `CREATE MATERIALIZED VIEW` . Consulte [exibições materializadas](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) para obter detalhes.
> * Para `Constraints` o, com suporte a partir do hive 2.1.0 no HDInsight 4,0, use `DESCRIBE EXTENDED` para listar as restrições de uma tabela e use `ALTER TABLE` para adicionar restrições. Consulte [alterar as restrições de tabela](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) para obter detalhes.

## <a name="copy-hive-tables"></a>Copiar tabelas do hive

1. Aplique a ação de script "exportar" no cluster original com os campos a seguir.

    Isso irá gerar e executar scripts do hive intermediário. Eles serão salvos no especificado `<hdfs-export-path>` .

    Opcionalmente, use `--run-script=false` para personalizá-los antes de executar manualmente.

    |Propriedade | Valor |
    |---|---|
    |URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Tipo(s) de nó|Head|
    |Parâmetros|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. Após a conclusão bem-sucedida da exportação, aplique a ação de script "importar" no novo cluster com os campos a seguir.

    |Propriedade | Valor |
    |---|---|
    |URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Tipo(s) de nó|Head|
    |Parâmetros|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Verificação

Baixe e execute o script como usuário raiz [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) no nó primário de cada cluster e compare o conteúdo do arquivo de saída `/tmp/hive_contents.out` . Consulte [conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="cleanup-additional-storage-usage"></a>Limpar o uso de armazenamento adicional

Depois que a migração de armazenamento for concluída e verificada, você poderá excluir os dados no caminho de exportação do HDFS especificado.

A opção é usar o comando HDFS `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>Opção: reduzir o uso de armazenamento adicional

A ação exportar script provavelmente dobra o uso do armazenamento devido ao hive. No entanto, é possível limitar o uso de armazenamento adicional migrando manualmente, um banco de dados ou uma tabela de cada vez.

1. Especifique `--run-script=false` para ignorar a execução do script do hive gerado. Os scripts de exportação e importação do hive ainda seriam salvos no caminho de exportação.

2. Execute trechos de código do hive exportar e importar scripts de banco de dados ou tabela por tabela, limpando manualmente o caminho de exportação após cada tabela ou banco de dados migrado.

## <a name="next-steps"></a>Próximas etapas

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Usar repositórios de metadados externos](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Tipos de armazenamento e recursos](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Ação de script para um cluster em execução](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
