---
title: Migrar cargas de trabalho do Hive do Azure HDInsight 3.6 para o HDInsight 4.0
description: Saiba como migrar cargas de trabalho Apache Hive no HDInsight 3,6 para o HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 93dc565055c6eb413a0c277a9891e5fcfab50345
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941351"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrar cargas de trabalho do Hive do Azure HDInsight 3.6 para o HDInsight 4.0

Este documento mostra como migrar cargas de trabalho Apache Hive e LLAP no HDInsight 3,6 para o HDInsight 4,0. O HDInsight 4,0 fornece recursos mais recentes de Hive e LLAP, como exibições materializadas e cache de resultados de consultas. Ao migrar suas cargas de trabalho para o HDInsight 4,0, você pode usar muitos recursos mais recentes do hive 3 que não estão disponíveis no HDInsight 3,6.

Este artigo aborda os seguintes assuntos:

* Migração de metadados do hive para o HDInsight 4,0
* Migração segura de tabelas ACID e não ACID
* Preservação de políticas de segurança do hive em versões do HDInsight
* Execução de consulta e depuração do HDInsight 3,6 para o HDInsight 4,0

Uma vantagem do hive é a capacidade de exportar metadados para um banco de dados externo (chamado de metastore do hive). O **metastore do hive** é responsável por armazenar estatísticas de tabela, incluindo o local de armazenamento de tabela, nomes de coluna e informações de índice de tabela. O HDInsight 3,6 e o HDInsight 4,0 exigem esquemas de metastore diferentes e não podem compartilhar um único metastore. A maneira recomendada para atualizar o metastore do Hive com segurança é atualizar uma cópia em vez do original no ambiente de produção atual. Este documento requer que os clusters originais e novos tenham acesso à mesma conta de armazenamento. Portanto, ele não abrange a migração de dados para outra região.

## <a name="migrate-from-external-metastore"></a>Migrar do metastore externo

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. executar a compactação principal em tabelas ACID no HDInsight 3,6

As tabelas de ACID do HDInsight 3,6 e do HDInsight 4,0 compreendem os deltas ACID de forma diferente. A única ação necessária antes da migração é executar a compactação ' principal ' em cada tabela ACID no cluster 3,6. Consulte o [manual de idioma do hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) para obter detalhes sobre a compactação.

### <a name="2-copy-sql-database"></a>2. copiar banco de dados SQL
Crie uma nova cópia do seu metastore externo. Se você estiver usando um metastore externo, uma das maneiras seguras e fáceis de fazer uma cópia do metastore é [restaurar o banco de dados](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) com um nome diferente usando a `RESTORE` função.  Consulte [usar repositórios de metadados externos no Azure HDInsight](../hdinsight-use-external-metadata-stores.md) para saber mais sobre como anexar um metastore externo a um cluster HDInsight.

### <a name="3-upgrade-metastore-schema"></a>3. Atualizar esquema de metastore
Depois que a **cópia** do metastore for concluída, execute um script de atualização de esquema na [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) no cluster existente do HDInsight 3,6 para atualizar o novo metastore para o esquema do hive 3. (Essa etapa não exige que o novo metastore esteja conectado a um cluster.) Isso permite que o banco de dados seja anexado como um metastore do HDInsight 4,0.

Use os valores na tabela abaixo. Substitua pelos `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` valores apropriados para a metastore do hive **cópia**, separados por espaços. Não inclua ". database.windows.net" ao especificar o nome do SQL Server.

|Propriedade | Valor |
|---|---|
|Tipo de script|- Personalizado|
|Nome|Atualização do hive|
|URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo(s) de nó|Head|
|Parâmetros|SENHA DE NOME DE USUÁRIO DATABASENAME SQLSERVERNAME|

> [!Warning]  
> A atualização que converte o esquema de metadados do HDInsight 3,6 para o esquema do HDInsight 4,0 não pode ser revertida.

Você pode verificar a atualização executando a seguinte consulta SQL no banco de dados:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. implantar um novo cluster HDInsight 4,0

1. Especifique o metastore atualizado como o metastore do Hive do novo cluster.

1. No entanto, os dados reais das tabelas não ficam acessíveis até que o cluster tenha acesso às contas de armazenamento necessárias.
Verifique se as contas de armazenamento das tabelas do hive no cluster do HDInsight 3,6 estão especificadas como as contas de armazenamento primária ou secundária do novo cluster HDInsight 4,0.
Para obter mais informações sobre como adicionar contas de armazenamento a clusters HDInsight, consulte [Adicionar contas de armazenamento adicionais ao HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. concluir a migração com uma ferramenta de pós-atualização no HDInsight 4,0

As tabelas gerenciadas devem estar em conformidade com ACID no HDInsight 4,0, por padrão. Depois de concluir a migração do metastore, execute uma ferramenta de pós-atualização para tornar as tabelas gerenciadas não ACID anteriores compatíveis com o cluster HDInsight 4,0. Essa ferramenta aplicará a seguinte conversão:

|3,6 |4,0 |
|---|---|
|Tabelas externas|Tabelas externas|
|Tabelas não gerenciadas não ACID|Tabelas externas com a propriedade ' external. Table. Purge ' = ' true '|
|Tabelas gerenciadas ACID|Tabelas gerenciadas ACID|

Execute a ferramenta de pós-atualização do hive do cluster do HDInsight 4,0 usando o Shell SSH:

1. Conecte-se ao seu cabeçalho de cluster usando SSH. Para obter instruções, consulte [conectar-se ao HDInsight usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Abra um shell de logon como o usuário do hive executando `sudo su - hive`
1. Execute o comando a seguir no Shell.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Depois que a ferramenta for concluída, o seu depósito do hive estará pronto para o HDInsight 4,0.

## <a name="migrate-from-internal-metastore"></a>Migrar do metastore interno

Se o cluster HDInsight 3,6 usar um metastore do Hive interno, siga as etapas abaixo para executar um script, que gera consultas de Hive para exportar as definições de objeto do metastore.

Os clusters HDInsight 3,6 e 4,0 devem usar a mesma conta de armazenamento.

> [!NOTE]
>
> * No caso de tabelas ACID, uma nova cópia dos dados abaixo da tabela será criada.
>
> * Esse script oferece suporte somente à migração de bancos de dados, tabelas e partições do hive. Outros objetos de metadados, como exibições, UDFs e restrições de tabela, devem ser copiados manualmente.
>
> * Quando esse script for concluído, supõe-se que o cluster antigo não será mais usado para acessar qualquer uma das tabelas ou bancos de dados referenciados no script.
>
> * Todas as tabelas gerenciadas se tornarão transacionais no HDInsight 4,0. Opcionalmente, mantenha a tabela não transacional exportando os dados para uma tabela externa com a propriedade ' external. Table. Purge ' = ' true '. Por exemplo,
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Conecte-se ao cluster HDInsight 3,6 usando um [cliente Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Na sessão SSH aberta, baixe o arquivo de script a seguir para gerar um arquivo chamado **mytables. HQL**.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Para um cluster HDInsight normal, sem o ESP, basta executar `exporthive_hdi_3_6.sh` .

    * Para um cluster com ESP, kinit e modifique os argumentos para beeline: execute o seguinte, definindo o usuário e o domínio para o usuário do Azure AD com as permissões completas do hive.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Saia da sua sessão SSH. Em seguida, insira um comando scp para baixar as próprias **tabelas. HQL** localmente.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Carregar as próprias **TableName. HQL** no *novo* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Em seguida, use o SSH para se conectar ao *novo* cluster HDInsight 4,0. Execute o seguinte código de uma sessão SSH para este cluster:

    Sem ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    Com ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

A ferramenta de pós-atualização para migração de metastore externa não se aplica aqui, pois tabelas não gerenciadas não ACID do HDInsight 3,6 convertem para tabelas gerenciadas ACID no HDInsight 4,0.

> [!Important]  
> Tabelas gerenciadas no HDInsight 4,0 (incluindo tabelas migradas de 3,6) não devem ser acessadas por outros serviços ou aplicativos, incluindo clusters HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Proteger o hive em versões do HDInsight

Desde o HDInsight 3,6, o HDInsight integra-se com Azure Active Directory usando o Enterprise Security Package do HDInsight (ESP). O ESP usa o Kerberos e o Apache Ranger para gerenciar as permissões de recursos específicos no cluster. As políticas de Ranger implantadas no hive no HDInsight 3,6 podem ser migradas para o HDInsight 4,0 com as seguintes etapas:

1. Navegue até o painel de Service Manager do Ranger no cluster do HDInsight 3,6.
2. Navegue até a política chamada **Hive** e exporte a política para um arquivo JSON.
3. Certifique-se de que todos os usuários referenciados no JSON da política exportada existam no novo cluster. Se um usuário for referenciado na política JSON, mas não existir no novo cluster, adicione o usuário ao novo cluster ou remova a referência da política.
4. Navegue até o painel de **Service Manager do Ranger** no cluster do HDInsight 4,0.
5. Navegue até a política chamada **Hive** e importe a política do Ranger JSON da etapa 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Verificar a compatibilidade e modificar os códigos conforme necessário no aplicativo de teste

Ao migrar cargas de trabalho, como programas e consultas existentes, verifique as notas de versão e a documentação de alterações e aplique as alterações conforme necessário. Se o seu cluster HDInsight 3,6 estiver usando um Spark e metastore do Hive compartilhados, será necessária uma [configuração adicional usando o conector do depósito do hive](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Implantar novo aplicativo para produção

Para alternar para o novo cluster, por exemplo, você pode instalar um novo aplicativo cliente e usá-lo como um novo ambiente de produção, ou pode atualizar seu aplicativo cliente existente e alternar para o HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Alternar o HDInsight 4,0 para a produção

Se as diferenças tiverem sido criadas no metastore durante o teste, você precisará atualizar as alterações logo antes de alternar. Nesse caso, você pode exportar & importar o metastore e, em seguida, atualizar novamente.

## <a name="remove-the-old-production"></a>Remover a produção antiga

Depois de confirmar que a versão está completa e totalmente operacional, você pode remover a versão 3,6 e o metastore anterior. Certifique-se de que tudo seja migrado antes de excluir o ambiente.

## <a name="query-execution-across-hdinsight-versions"></a>Execução de consulta em versões do HDInsight

Há duas maneiras de executar e depurar consultas Hive/LLAP em um cluster HDInsight 3,6. O HiveCLI fornece uma experiência de linha de comando e o modo de exibição [tez/Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) fornece um fluxo de trabalho baseado em GUI.

No HDInsight 4,0, o HiveCLI foi substituído por Beeline. O modo de exibição tez/Hive fornece um fluxo de trabalho baseado em GUI. HiveCLI é um cliente Thrift para Hiveserver 1 e beeline é um cliente JDBC que fornece acesso ao Hiveserver 2. Beeline também pode ser usado para se conectar a qualquer outro ponto de extremidade de banco de dados compatível com JDBC. O beeline está disponível de fora da caixa no HDInsight 4,0 sem nenhuma instalação necessária.

## <a name="next-steps"></a>Próximas etapas

* [Comunicado do HDInsight 4,0](../hdinsight-version-release.md)
* [Aprofundamento no HDInsight 4,0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3-tabelas ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)