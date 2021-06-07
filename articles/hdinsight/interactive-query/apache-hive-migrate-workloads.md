---
title: Migrar cargas de trabalho do Hive do Azure HDInsight 3.6 para o HDInsight 4.0
description: Saiba como migrar cargas de trabalho Apache Hive no HDInsight 3,6 para o HDInsight 4,0.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 43d616bc82c608918f5e7ee51481a393dd55a284
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566063"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar cargas de trabalho do Hive do Azure HDInsight 3.6 para o HDInsight 4.0

O HDInsight 4,0 tem várias vantagens sobre o HDInsight 3,6. Aqui está uma [visão geral das novidades do HDInsight 4,0](../hdinsight-version-release.md).

Este artigo aborda as etapas para migrar cargas de trabalho do hive do HDInsight 3,6 para o 4,0, incluindo

* Cópia de metastore do Hive e atualização de esquema
* Migração segura para compatibilidade com ACID
* Preservação de políticas de segurança do hive

Os clusters HDInsight novos e antigos devem ter acesso às mesmas contas de armazenamento.

A migração de tabelas do hive para uma nova conta de armazenamento precisa ser feita como uma etapa separada. Consulte [migração do hive entre contas de armazenamento](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>Etapas da atualização

### <a name="1-prepare-the-data"></a>1. preparar os dados

* O HDInsight 3,6 por padrão não oferece suporte a tabelas ACID. No entanto, se as tabelas ACID estiverem presentes, execute a compactação ' principal ' nelas. Consulte o [manual de idioma do hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) para obter detalhes sobre a compactação.

* Se estiver usando [Azure data Lake Storage Gen1](../overview-data-lake-storage-gen1.md), os locais da tabela do hive provavelmente dependerão das configurações do HDFS do cluster. Execute a ação de script a seguir para tornar esses locais portáteis para outros clusters. Consulte [ação de script para um cluster em execução](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

    |Propriedade | Valor |
    |---|---|
    |URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Tipo(s) de nó|Head|
    |Parâmetros||

### <a name="2-copy-the-sql-database"></a>2. copiar o banco de dados SQL

* Se o cluster usar um metastore do Hive padrão, siga este [guia](./hive-default-metastore-export-import.md) para exportar metadados para um metastore externo. Em seguida, crie uma cópia do metastore externo para atualização.

* Se o cluster usar um metastore do Hive externo, crie uma cópia dele. As opções incluem [exportação/importação](../../azure-sql/database/database-export.md) e [restauração pontual](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore).

### <a name="3-upgrade-the-metastore-schema"></a>3. atualizar o esquema do metastore

Esta etapa usa o [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) do HDInsight 4,0 para atualizar o esquema do metastore.

> [!Warning]
> Esta etapa não é reversível. Execute isso somente em uma cópia do metastore.

1. Crie um cluster do HDInsight 4,0 temporário para acessar o hive 4,0 `schematool` . Você pode usar o [metastore do hive padrão](../hdinsight-use-external-metadata-stores.md#default-metastore) para esta etapa.

1. No cluster HDInsight 4,0, execute `schematool` para atualizar o destino hdinsight 3,6 metastore:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Esse utilitário usa o cliente `beeline` para executar scripts SQL no `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > A sintaxe SQL nesses scripts não é necessariamente compatível com outras ferramentas de cliente. Por exemplo, o [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) e o [Editor de consultas no portal do Azure](../../azure-sql/database/connect-query-portal.md) exigem palavra-chave `GO` após cada comando.
    >
    > Se algum script falhar devido a capacidade de recurso ou tempos limite de transação, escale verticalmente o banco de dados SQL.

1. Verifique a versão final com a consulta `select schema_version from dbo.version` .

    A saída deve corresponder à do seguinte comando bash do cluster HDInsight 4,0.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Exclua o cluster do HDInsight 4,0 temporário.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. implantar um novo cluster HDInsight 4,0

Crie um novo cluster HDInsight 4,0, [selecionando o metastore do hive atualizado](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) e as mesmas contas de armazenamento.

* O novo cluster não requer o mesmo sistema de arquivos padrão.

* Se o metastore contiver tabelas que residem em várias contas de armazenamento, você precisará adicionar essas contas de armazenamento ao novo cluster para acessar essas tabelas. Consulte [Adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md).

* Se os trabalhos do hive falharem devido à inacessibilidade do armazenamento, verifique se o local da tabela está em uma conta de armazenamento adicionada ao cluster.

    Use o seguinte comando de Hive para identificar o local da tabela:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. converter tabelas para conformidade com ACID

As tabelas gerenciadas devem estar em conformidade com ACID no HDInsight 4,0. Execute `strictmanagedmigration` no HDInsight 4,0 para converter todas as tabelas não gerenciadas não acid em tabelas externas com a propriedade `'external.table.purge'='true'` . Execute a partir do cabeçalho:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Proteger o hive em versões do HDInsight

O HDInsight se integra opcionalmente com Azure Active Directory usando o Enterprise Security Package do HDInsight (ESP). O ESP usa o Kerberos e o Apache Ranger para gerenciar as permissões de recursos específicos no cluster. As políticas de Ranger implantadas no hive no HDInsight 3,6 podem ser migradas para o HDInsight 4,0 com as seguintes etapas:

1. Navegue até o painel de Service Manager do Ranger no cluster do HDInsight 3,6.
2. Navegue até a política chamada **Hive** e exporte a política para um arquivo JSON.
3. Certifique-se de que todos os usuários referenciados no JSON da política exportada existam no novo cluster. Se um usuário for referenciado na política JSON, mas não existir no novo cluster, adicione o usuário ao novo cluster ou remova a referência da política.
4. Navegue até o painel de **Service Manager do Ranger** no cluster do HDInsight 4,0.
5. Navegue até a política chamada **Hive** e importe a política do Ranger JSON da etapa 2.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>Alterações do hive no HDInsight 4,0 que podem exigir alterações no aplicativo

* Confira [configuração adicional usando o conector do depósito do hive](./apache-hive-warehouse-connector.md) para compartilhar o metastore entre o Spark e o hive para tabelas acid.

* O HDInsight 4,0 usa a [autorização baseada em armazenamento](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server). Se você modificar as permissões de arquivo ou criar pastas como um usuário diferente do hive, provavelmente terá erros de Hive com base nas permissões de armazenamento. Para corrigir, conceda `rw-` acesso ao usuário. Consulte [Guia de permissões do HDFS](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` é substituído por `Beeline` .

Consulte o [comunicado do HDInsight 4,0](../hdinsight-version-release.md) para obter alterações adicionais.

## <a name="further-reading"></a>Leitura adicional

* [Comunicado do HDInsight 4,0](../hdinsight-version-release.md)
* [Aprofundamento no HDInsight 4,0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3-tabelas ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)