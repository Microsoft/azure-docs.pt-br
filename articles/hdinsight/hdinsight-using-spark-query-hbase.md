---
title: Usar o Spark para ler e gravar dados do HBase - Azure HDInsight
description: Use o conector do HBase Spark para ler e gravar dados de um cluster Spark para um cluster HBase.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: 344caf4080380f5d9dfdaf452798ada6d1dc9f1c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931225"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Usar o Apache Spark para ler e gravar dados do Apache HBase

Apache HBase costuma ser consultada com sua API de nível inferior (verificações, obtenções e colocações) ou com uma sintaxe SQL usando Apache Phoenix. O Apache também fornece o Apache Spark conector HBase. O conector é uma alternativa conveniente e eficiente para consultar e modificar dados armazenados pelo HBase.

## <a name="prerequisites"></a>Pré-requisitos

* Dois clusters HDInsight separados implantados na mesma [rede virtual](./hdinsight-plan-virtual-network-deployment.md). Um HBase e um Spark com pelo menos o Spark 2,1 (HDInsight 3,6) instalado. Para obter mais informações, consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* O esquema de URI do seu armazenamento primário de clusters. Esse esquema seria wasb://para o armazenamento de BLOBs do Azure, `abfs://` por Azure data Lake Storage Gen2 ou ADL://para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento de BLOB, o URI será `wasbs://` .  Confira também [Transferência segura](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Processo geral

O processo de alto nível para habilitar o cluster Spark para consultar o cluster HBase é o seguinte:

1. Preparar alguns dados de exemplo no HBase.
2. Adquira o arquivo de hbase-site.xml de sua pasta de configuração de cluster do HBase (/etc/HBase/conf) e coloque uma cópia de hbase-site.xml na pasta de configuração do Spark 2 (/etc/spark2/conf). (Opcional: usar o script fornecido pela equipe do HDInsight para automatizar esse processo)
4. Execute `spark-shell` referenciar o conector do HBase Spark por seu Maven coordena na opção `packages`.
5. Defina um catálogo que mapeia o esquema do Spark para HBase.
6. Interagir com os dados do HBase usando as APIs de DataFrame ou RDD.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparar os dados de exemplo no Apache HBase

Nesta etapa, você cria e preenche uma tabela no Apache HBase que você pode consultar usando o Spark.

1. Use o `ssh` comando para se conectar ao cluster HBase. Edite o comando a seguir substituindo pelo `HBASECLUSTER` nome do cluster HBase e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Use o `hbase shell` comando para iniciar o shell interativo do HBase. Digite o seguinte comando em sua conexão de SSH:

    ```bash
    hbase shell
    ```

3. Use o `create` comando para criar uma tabela do HBase com famílias de duas colunas. Digite o seguinte comando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Use o `put` comando para inserir valores em uma coluna especificada em uma linha especificada em uma tabela específica. Digite o seguinte comando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Use o `exit` comando para interromper o shell interativo do HBase. Digite o seguinte comando:

    ```hbase
    exit
    ```
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>Executar scripts para configurar a conexão entre clusters

Para configurar a comunicação entre clusters, siga as etapas abaixo para executar dois scripts em seus clusters. Esses scripts automatizarão o processo de cópia de arquivo descrito na seção "configurar a comunicação manualmente" abaixo. 

* O script executado do cluster HBase será carregado `hbase-site.xml` e as informações de mapeamento de IP do HBase para o armazenamento padrão anexado ao cluster Spark. 
* O script que você executa do cluster Spark configura dois trabalhos cron para executar dois scripts auxiliares periodicamente:  
    1.  Trabalho do HBase cron – baixar novos `hbase-site.xml` arquivos e mapeamento de IP do HBase da conta de armazenamento padrão do Spark para o nó local
    2.  Trabalho do Spark cron – verifica se um dimensionamento do Spark ocorreu e se o cluster é seguro. Nesse caso, edite `/etc/hosts` para incluir o mapeamento de IP do HBase armazenado localmente

__Observação__: antes de continuar, verifique se você adicionou a conta de armazenamento do cluster Spark ao cluster HBase como uma conta de armazenamento secundária. Certifique-se de que você tenha os scripts em ordem, conforme indicado abaixo.


1. Use a [ação de script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) em seu cluster HBase para aplicar as alterações com as seguintes considerações: 


    |Propriedade | Valor |
    |---|---|
    |URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |Tipo(s) de nó|Região|
    |Parâmetros|`-s SECONDARYS_STORAGE_URL`|
    |Persistente|yes|

    * `SECONDARYS_STORAGE_URL` é a URL do armazenamento padrão do lado do Spark. Exemplo de parâmetro: `-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  Use a ação de script em seu cluster Spark para aplicar as alterações com as seguintes considerações:

    |Propriedade | Valor |
    |---|---|
    |URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |Tipo(s) de nó|Cabeçalho, trabalho, Zookeeper|
    |Parâmetros|`-s "SPARK-CRON-SCHEDULE"` (opcional) `-h "HBASE-CRON-SCHEDULE"` adicional|
    |Persistente|yes|


    * Você pode especificar a frequência com que deseja que esse cluster verifique automaticamente se atualizar. Padrão:-s "*/1 * * * *"-h 0 (neste exemplo, o Spark cron é executado a cada minuto, enquanto o HBase cron não é executado)
    * Como o HBase cron não está configurado por padrão, você precisa executar novamente esse script quando executar o dimensionamento para o cluster HBase. Se o cluster do HBase for dimensionado com frequência, você poderá optar por configurar o trabalho do HBase cron automaticamente. Por exemplo: `-h "*/30 * * * *"` configura o script para executar verificações a cada 30 minutos. Isso executará a agenda do HBase cron periodicamente para automatizar o download de novas informações do HBase na conta de armazenamento comum para o nó local.
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>Configurar a comunicação manualmente (opcional, se o script fornecido na etapa acima falhar)

__Observação:__ Essas etapas precisam ser executadas toda vez que um dos clusters passa por uma atividade de dimensionamento.

1. Copie o hbase-site.xml do armazenamento local para a raiz do armazenamento padrão do seu cluster Spark.  Edite o comando a seguir para refletir sua configuração.  Em seguida, na sessão SSH aberta para o cluster HBase, digite o comando:

    | Valor da sintaxe | Novo valor|
    |---|---|
    |[Esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modifique para refletir seu armazenamento.  A sintaxe abaixo é para o armazenamento de BLOBs com a transferência segura habilitada.|
    |`SPARK_STORAGE_CONTAINER`|Substitua pelo nome do contêiner de armazenamento padrão usado para o cluster Spark.|
    |`SPARK_STORAGE_ACCOUNT`|Substitua pelo nome da conta de armazenamento padrão usada para o cluster Spark.|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. Em seguida, saia da conexão SSH para o cluster HBase.

    ```bash
    exit
    ```


3. Conecte-se ao nó principal do cluster Spark usando o SSH. Edite o comando a seguir substituindo pelo `SPARKCLUSTER` nome do seu cluster Spark e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. Insira o comando a seguir para copiar `hbase-site.xml` do armazenamento padrão do seu cluster Spark para a pasta de configuração do Spark 2 no armazenamento local do cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Execute o Shell de Spark referenciando o conector HBase Spark

Depois de concluir a etapa anterior, você deverá ser capaz de executar o Shell do Spark, referenciando a versão apropriada do conector HBase do Spark. Para localizar a versão principal do conector HBase do Spark mais recente para seu cenário de cluster, consulte [SHC Core Repository](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/).

Por exemplo, a tabela a seguir lista duas versões e os comandos correspondentes que a equipe do HDInsight usa atualmente. Você pode usar as mesmas versões para seus clusters se as versões do HBase e do Spark forem as mesmas indicadas na tabela. 


1. Na sessão SSH aberta para o cluster Spark, digite o seguinte comando para iniciar um shell do Spark:

    |Versão do Spark| Versão do HBase HDI  | Versão do SHC    |  Comando  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3,6 (HBase 1,1) | 1.1.1-2.1-s_2.11    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4,0 (HBase 2,0) | 1.1.0.3.1.2.2-1  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. Mantenha essa instância do shell do Spark aberta e continue a [definir um catálogo e uma consulta](#define-a-catalog-and-query). Se você não encontrar os jars que correspondem às suas versões no repositório do SHC Core, continue lendo. 

Você pode criar os jars diretamente do Branch GitHub do [Spark-HBase-Connector](https://github.com/hortonworks-spark/shc) . Por exemplo, se você estiver executando o com o Spark 2,3 e o HBase 1,1, conclua estas etapas:

1. Clone o repositório:

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. Vá para o Branch-2,3:

    ```bash
    git checkout branch-2.3
    ```

3. Compilar a partir da ramificação (cria um arquivo. jar):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. Execute o comando a seguir (certifique-se de alterar o nome. jar que corresponde ao arquivo. jar que você criou):

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. Mantenha essa instância do shell do Spark aberta e continue na próxima seção. 



## <a name="define-a-catalog-and-query"></a>Definir um catálogo e uma consulta

Nesta etapa, você deve definir um catálogo que mapeia o esquema do Apache Spark para Apache HBase.  

1. No Shell do Spark aberto, insira as seguintes `import` instruções:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Digite o comando a seguir para definir um catálogo para a tabela de contatos que você criou no HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    O código:  

    1. Define um esquema de catálogo para a tabela do HBase denominada `Contacts` .  
    1. Identifica o RowKey como `key` e mapeia os nomes de coluna usados no Spark para a família de colunas, o nome da coluna e o tipo de coluna como usado no HBase.  
    1. Define o RowKey em detalhes como uma coluna nomeada ( `rowkey` ), que tem uma família de colunas específica `cf` do `rowkey` .  

1. Insira o comando a seguir para definir um método que fornece um dataframe em toda a `Contacts` tabela no HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Crie uma instância do DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Consulte o DataFrame:

    ```scala
    df.show()
    ```

    Você deve ver duas linhas de dados:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registre uma tabela temporária para que você possa consultar a tabela do HBase usando Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Emitir uma consulta SQL em relação a `contacts` tabela:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Você deve ver os resultados como estes:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Inserir nova linha

1. Para inserir um novo registro de contato, defina uma `ContactRecord` classe:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Crie uma instância de `ContactRecord` e colocá-la em uma matriz:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Salve a matriz de novos dados em HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Examine os resultados:

    ```scala  
    df.show()
    ```

    Você deve ver uma saída como a abaixo:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Feche o Shell do Spark digitando o seguinte comando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Próximas etapas

* [Conector Apache Spark HBase](https://github.com/hortonworks-spark/shc)
