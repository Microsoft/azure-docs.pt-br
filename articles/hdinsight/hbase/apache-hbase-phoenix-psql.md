---
title: Carregamento em massa no Apache Phoenix usando psql – HDInsight do Azure
description: Usar a ferramenta psql para carregar dados de carregamento em massa em tabelas Apache Phoenix no Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d56c9b33d53be0e30769aef8934a2dbf43d06867
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944842"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Carregar dados em massa para o Apache Phoenix usando psql

[Apache Phoenix](https://phoenix.apache.org/) é um banco de dados relacional livre e paralelo em massa criado em [Apache HBase](../hbase/apache-hbase-overview.md). O Phoenix fornece consultas como SQL em HBase. O Phoenix usa drivers JDBC para permitir aos usuários criar, excluir e alterar tabelas SQL, índices, exibições e as sequências e linhas upsert individualmente e em massa. O Phoenix usa compilação nativa noSQL, em vez de usar o MapReduce para compilar consultas, para criar aplicativos de baixa latência sobre HBase. O Phoenix adiciona coprocessadores para oferecer suporte à execução de código fornecido pelo cliente no espaço de endereço do servidor, executando o código colocalizado junto com os dados. Isso minimiza a transferência de dados do cliente/servidor.  Para trabalhar com dados usando Phoenix no HDInsight, primeiro crie tabelas e, em seguida, carregue dados nelas.

## <a name="bulk-loading-with-apache-phoenix"></a>Carregamento em massa com o Apache Phoenix

Há várias maneiras de obter dados em HBase incluindo o uso de APIs de clientes, um trabalho de MapReduce com TableOutputFormat ou inserindo os dados manualmente usando o shell do HBase. O Phoenix fornece dois métodos para carregar dados CSV em tabelas de Phoenix: uma ferramenta de carregamento de cliente chamada `psql` e uma ferramenta de carregamento em massa baseada em MapReduce.

A ferramenta `psql` é de thread único e é mais adequada para carregar megabytes ou gigabytes de dados. Para serem carregados, todos os arquivos CSV devem ter a extensão de arquivo '.csv'.  Você também pode especificar arquivos de script SQL na linha de comando do `psql` com a extensão de arquivo '.sql'.

O carregamento em massa com MapReduce é usado para volumes de dados muito maiores, normalmente em cenários de produção, pois o MapReduce usa vários threads.

Antes de iniciar o carregamento de dados, verifique se o Phoenix está habilitado e se as configurações de tempo limite de consulta estão como o esperado.  Acesse o painel do [Apache Ambari](https://ambari.apache.org/) do cluster HDInsight, selecione HBase e, em seguida, a guia configuração.  Role para baixo para verificar se Apache Phoenix está definido `enabled` como mostrado:

![Configurações de Cluster de HDInsight do Apache Phoenix](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Use `psql` para tabelas de carregamento em massa

1. Crie um arquivo chamado `createCustomersTable.sql` e copie o código abaixo para o arquivo. Em seguida, salve e feche o arquivo.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Crie um arquivo chamado `listCustomers.sql` e copie o código abaixo para o arquivo. Em seguida, salve e feche o arquivo.

    ```sql
    SELECT * from Customers;
    ```

1. Crie um arquivo chamado `customers.csv` e copie o código abaixo para o arquivo. Em seguida, salve e feche o arquivo.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Crie um arquivo chamado `customers2.csv` e copie o código abaixo para o arquivo. Em seguida, salve e feche o arquivo.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Abra um prompt de comando e altere o diretório para o local dos arquivos recém-criados. Substitua CLUSTERname, abaixo, pelo nome real do cluster HBase. Em seguida, execute o código para carregar os arquivos para o cabeçalho do cluster:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Na sua sessão SSH, altere o diretório para o local da ferramenta **psql** . Execute o comando a seguir:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Carregar os dados em massa. O código a seguir criará a tabela **Customers** e, em seguida, carregará os dados.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Após a `psql` conclusão da operação, você verá uma mensagem semelhante à seguinte:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Você pode continuar a usar `psql` o para exibir o conteúdo da tabela Customers. Execute o código abaixo:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Como alternativa, você pode usar o [shell do HBase](./query-hbase-with-hbase-shell.md)ou o [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) para consultar os dados.

1. Carregar dados adicionais. Agora que a tabela já existe, o comando especifica a tabela. Execute o comando a seguir:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Use MapReduce para tabelas de carregamento em massa

Para carregamento de maior taxa de transferência distribuído pelo cluster, use a ferramenta de carregamento do MapReduce. Esse carregador primeiro converte todos os dados em HFiles e, em seguida, fornece o HFiles criado para o HBase.

1. Esta seção continua com a sessão SSH e os objetos criados anteriormente. Crie a tabela de **clientes** e o arquivo de **customers.csv** conforme necessário usando as etapas acima. Se necessário, restabeleça a conexão SSH.

1. Truncar o conteúdo da tabela **Customers** . Em sua sessão SSH aberta, execute os comandos abaixo:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Copie o `customers.csv` arquivo de seu cabeçalho para o armazenamento do Azure.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Altere para o diretório de execução do comando de carregamento em massa do MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Inicie o carregador de MapReduce CSV usando o `hadoop` com o jar do cliente Phoenix:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Quando o carregamento for concluído, você verá uma mensagem semelhante à seguinte:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Para usar o MapReduce com o Azure Data Lake Storage, localize o diretório raiz do Data Lake Storage, que é o valor `hbase.rootdir` em `hbase-site.xml`. No comando a seguir, o diretório raiz do Data Lake Storage é `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Neste comando, especifique as pastas de entrada e saída do Data Lake Storage como parâmetros:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Para consultar e exibir os dados, você pode usar **psql** conforme descrito anteriormente. Você também pode usar o [shell do HBase](./query-hbase-with-hbase-shell.md)ou o [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Recomendações

* Use a mesma mídia de armazenamento para as pastas entradas e saídas, Azure Storage (WASB) ou Azure Data Lake Storage (ADL). Para transferir dados do Azure Storage para o Data Lake Storage, você pode usar o comando `distcp`:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Use nós de trabalho maiores. Os processos de mapeamento da cópia em massa do MapReduce geram grandes quantidades de saída temporária que preenchem o espaço não DFS disponível. Para uma grande quantidade de carregamento em massa, use mais e maiores nós de trabalho. O número de nós de trabalho que você aloca para o cluster afeta diretamente a velocidade de processamento.

* Divida os arquivos de entrada em partes de cerca de aproximadamente 10 GB. O carregamento em massa é uma operação de uso intensivo de armazenamento, portanto dividir seus arquivos de entrada em várias partes resulta em melhor desempenho.

* Evite pontos de acesso de servidor regional. Se a chave de linha está aumentando de forma monotônica, gravações sequenciais HBase podem induzir à regionalização de pontos de acesso do servidor. *Distribuir* a chave de linha reduz gravações sequenciais. O Phoenix fornece uma maneira de distribuir de forma transparente a chave de linha com um byte de distribuição para uma tabela específica, conforme mencionado abaixo.

## <a name="next-steps"></a>Próximas etapas

* [Carregamento de dados em massa com o Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Usar o Apache Phoenix com clusters do Apache HBase baseado em Linux no HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Tabelas Distribuídas](https://phoenix.apache.org/salted.html)
* [Gramática do Apache Phoenix](https://phoenix.apache.org/language/index.html)
