---
title: Apache Sqoop com Apache Hadoop - Azure HDInsight
description: Saiba como usar o Apache Sqoop para importar e exportar entre Apache Hadoop no HDInsight e no banco de dados SQL do Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 2d0d38dee15817e56c2784981365ea331b6a8459
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943150"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Usar o Apache Sqoop para importar e exportar dados entre Apache Hadoop no HDInsight e no banco de dados SQL do Azure

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Apache Sqoop para importar e exportar entre um cluster Apache Hadoop no Azure HDInsight e no banco de dados SQL do Azure ou Microsoft SQL Server. As etapas deste documentam usam o comando `sqoop` diretamente do nó principal do cluster Hadoop. Use o SSH para se conectar ao nó principal e executar os comandos neste documento. Este artigo é uma continuação do [Usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão de [Configurar o ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [Usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Familiaridade com o Sqoop. Para obter mais informações, consulte [Guia do usuário do Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Configuração

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para facilitar o uso, defina as variáveis. Substitua `PASSWORD` , `MYSQLSERVER` e `MYDATABASE` pelos valores relevantes e, em seguida, insira os comandos a seguir:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Exportação do Sqoop

De Hive para SQL.

1. Para verificar se o Sqoop pode ver seu banco de dados, insira o comando abaixo em sua conexão SSH aberta. Esse comando retorna uma lista de bancos de dados.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Digite o seguinte comando para ver uma lista de tabelas para o banco de dados especificado:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Para exportar dados da tabela Hive `hivesampletable` para a `mobiledata` tabela no banco de dado, digite o comando a seguir em sua conexão Open SSH:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Para verificar se os dados foram exportados, use as seguintes consultas de sua conexão SSH para exibir os dados exportados:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Importação do Sqoop

Do SQL para o armazenamento do Azure.

1. Insira o comando abaixo em sua conexão Open SSH para importar dados da `mobiledata` tabela no SQL, para o `wasbs:///tutorials/usesqoop/importeddata` diretório no HDInsight. Os campos nos dados que são separados por um caractere de tabulação, e as linhas serão encerradas por um caractere de nova linha.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Como alternativa, você também pode especificar uma tabela Hive:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Depois que a importação for concluída, digite o seguinte comando em sua conexão SSH aberta para listar os dados no novo diretório:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Use [beeline](./apache-hadoop-use-hive-beeline.md) para verificar se a tabela foi criada no hive.

    1. Conectar

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Execute cada consulta abaixo uma de cada vez e examine a saída:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Saia do beeline com `!exit` .

## <a name="limitations"></a>Limitações

* Exportação em massa-com o HDInsight baseado em Linux, o conector do Sqoop usado para exportar dados para o SQL não dá suporte a inserções em massa.

* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="important-considerations"></a>Considerações importantes

* O HDInsight e o SQL Server devem estar na mesma Rede Virtual do Azure.

    Para obter um exemplo, consulte o documento [Conectar o HDInsight à sua rede local](./../connect-on-premises-network.md).

    Para saber mais sobre como usar o HDInsight com Redes Virtuais do Azure, veja o documento [Estender o HDInsight usando a Rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md). Para saber mais sobre Rede Virtual do Azure, veja o documento [Visão Geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md).

* O SQL Server também deve ser configurado para permitir autenticação do SQL. Para obter mais informações, consulte o documento [Escolher um modo de autenticação](/sql/relational-databases/security/choose-an-authentication-mode).

* Você precisará configurar o SQL Server para aceitar conexões remotas. Para obter mais informações, consulte o documento [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como usar o Sqoop. Para obter mais informações, consulte:

* [Usar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho do Oozie.
* [Analisar dados de atraso de vôo usando o HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Use a consulta interativa para analisar dados de atraso de vôo e, em seguida, use Sqoop para exportar dados para um banco de dado no Azure.
* [Carregar dados no HDInsight](../hdinsight-upload-data.md): localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blob do Azure.