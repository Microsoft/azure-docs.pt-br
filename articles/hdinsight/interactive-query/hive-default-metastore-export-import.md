---
title: Migrar metastore do Hive padrão para metastore externo no Azure HDInsight
description: Migrar metastore do Hive padrão para metastore externo no Azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566108"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Migrar o BD metastore do Hive padrão para o BD do metastore externo

Este artigo mostra como migrar metadados de um [BD de metastore padrão](../hdinsight-use-external-metadata-stores.md#default-metastore) para o hive para um banco de dados SQL externo no HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Por que migrar para o BD de metastore externo

* O BD do metastore padrão é limitado ao SKU básico e não pode tratar cargas de trabalho de escala de produção.

* O BD do metastore externo permite que o cliente dimensione horizontalmente os recursos de computação do hive adicionando novos clusters HDInsight compartilhando o mesmo BD do metastore.

* Para a migração do HDInsight 3,6 para 4,0, é obrigatório migrar metadados para o BD do metastore externo antes de atualizar a versão do esquema do hive. Consulte [migrando cargas de trabalho do hdinsight 3,6 para o hdinsight 4,0](./apache-hive-migrate-workloads.md).

Como o BD do metastore padrão tem capacidade de computação limitada, recomendamos baixa utilização de outros trabalhos no cluster durante a migração de metadados.

Bancos de fontes e de destino devem usar a mesma versão do HDInsight e as mesmas contas de armazenamento. Se estiver atualizando versões do HDInsight de 3,6 para 4,0, conclua as etapas neste artigo primeiro. Em seguida, siga as etapas de atualização oficial [aqui](./apache-hive-migrate-workloads.md).

## <a name="prerequisites"></a>Pré-requisitos

Se estiver usando [Azure data Lake Storage Gen1](../overview-data-lake-storage-gen1.md), os locais da tabela do hive provavelmente dependerão das configurações do HDFS do cluster para Azure data Lake Storage Gen1. Execute a ação de script a seguir para tornar esses locais portáteis para outros clusters. Consulte [ação de script para um cluster em execução](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

A ação é semelhante à substituição de symlinks por seus caminhos completos.

|Propriedade | Valor |
|---|---|
|URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Tipo(s) de nó|Head|
|Parâmetros|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Migrar com exportação/importação usando SqlPackage

HDInsight An cluster criado somente após 2020-10-15 dá suporte à exportação/importação de SQL para o banco de armazenamento de metastore padrão do hive usando o `sqlpackage` .

1. Instale o [SqlPackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) no cluster.

2. Exporte o banco de BD do metastore padrão para o arquivo BACPAC executando o comando a seguir.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Salve o arquivo BACPAC. Abaixo está uma opção.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Importe o arquivo BACPAC para um novo banco de dados com as etapas listadas [aqui](../../azure-sql/database/database-import.md).

5. O novo banco de dados está pronto para ser [configurado como um BD de metastore externo em um novo cluster HDInsight](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Migrar usando o script do hive

Os clusters criados antes de 2020-10-15 não dão suporte à exportação/importação do banco de BD padrão do metastore.

Para esses clusters, siga o guia [copiar tabelas Hive entre contas de armazenamento](./hive-migration-across-storage-accounts.md), usando um segundo cluster com um [metastore do hive DB externo](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). O segundo cluster pode usar a mesma conta de armazenamento, mas deve usar um novo sistema de arquivos padrão.

### <a name="option-to-shallow-copy"></a>Opção para cópia "superficial"
O consumo de armazenamento dobraria quando as tabelas forem "profundas" copiadas usando o guia acima. Você precisa limpar manualmente os dados no contêiner de armazenamento de origem.
Em vez disso, podemos copiar as tabelas se elas não forem transacionais. Todas as tabelas do hive no HDInsight 3,6 não são transacionais por padrão, mas somente tabelas externas são não transacionais no HDInsight 4,0. As tabelas transacionais devem ser copiadas em profundidade. Siga estas etapas para copiar superficialmente tabelas não transacionais:

1. Execute o script [Hive-DDLs.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) no cabeçalho primário do cluster de origem para gerar o DDL para cada tabela do hive.
2. O DDL é gravado em um script do hive local chamado `/tmp/hdi_hive_ddls.hql` . Execute isso no cluster de destino que usa um metastore do Hive DB externo.

## <a name="verify-that-all-hive-tables-are-imported"></a>Verificar se todas as tabelas do hive foram importadas

O comando a seguir usa uma consulta SQL no BD do metastore para imprimir todas as tabelas do hive e seus locais de dados. Compare as saídas entre clusters novos e antigos para verificar se nenhuma tabela está ausente no novo banco de BD do metastore.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Leitura adicional

* [Migrar cargas de trabalho do HDInsight 3,6 para o 4,0](./apache-hive-migrate-workloads.md)
* [Migração de carga de trabalho do hive entre contas de armazenamento](./hive-migration-across-storage-accounts.md)
* [Conectar-se ao beeline no HDInsight](../hadoop/connect-install-beeline.md)
* [Solucionar problemas de erro de permissão Criar tabela](./interactive-query-troubleshoot-permission-error-create-table.md)