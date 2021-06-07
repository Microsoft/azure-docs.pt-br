---
title: Usar Livy Spark para enviar trabalhos ao cluster do Spark no Microsoft Azure HDInsight
description: Saiba como usar a API REST do Apache Spark para enviar trabalhos do Spark remotamente para um cluster do Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ff63f4fbadd7cb9e7584e2aa045583a35e0363fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930122"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Use a API REST do Apache Spark para enviar trabalhos remotos para um cluster do HDInsight Spark

Saiba como usar [Apache Livy](https://livy.incubator.apache.org/), a API REST do Apache Spark, que é usado para enviar trabalhos remotos para um cluster do Azure HDInsight Spark. Para obter a documentação detalhada, consulte [Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

Você pode usar a Livy para executar shells interativos do Spark ou enviar trabalhos em lotes a serem executados no Spark. Este artigo aborda como usar a Livy para enviar trabalhos em lotes. Os snippets nesse artigo usam cURL para fazer chamadas à API REST para o ponto de extremidade da Livy Spark.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Enviar um trabalho em lotes do Apache Livy Spark

Antes de enviar um trabalho em lotes, você deve carregar o jar do aplicativo no armazenamento de cluster associado ao cluster. Você pode usar [AzCopy](../../storage/common/storage-use-azcopy-v10.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes que podem ser usados para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Exemplos

* Se o arquivo JAR estiver no armazenamento de cluster (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Se quiser passar o nome do arquivo jar e o nome da classe como parte de um arquivo de entrada (nesse exemplo, input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obter informações sobre lotes Livy Spark em execução no cluster

Sintaxe:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Exemplos

* Se quiser recuperar todos os lotes Livy spark em execução no cluster:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Se você quiser recuperar um lote específico com uma determinada ID de lote

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Excluir um trabalho em lotes do Livy Spark

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Exemplo

Excluindo um trabalho em lotes com a ID de lote `5` .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark e alta disponibilidade

O Livy fornece alta disponibilidade para trabalhos Spark em execução no cluster. Aqui estão alguns exemplos.

* Se o serviço Livy falhar depois que você enviar um trabalho remotamente para um cluster Spark, o trabalho continuará a ser executado em segundo plano. Quando o Livy for backup, ele restaurará o status do trabalho e enviará o relatório de volta.
* Os notebooks Jupyter para HDInsight são alimentados por Livy no back-end. Se um bloco de anotações estiver executando um trabalho do Spark e o serviço Livy for reiniciado, o bloco de notas continuará a executar as células de código.

## <a name="show-me-an-example"></a>Mostrar um exemplo

Nesta seção, vamos examinar exemplos sobre como usar a Livy Spark para enviar um trabalho em lotes, monitorar o progresso do trabalho e excluir o trabalho. O aplicativo que usamos neste exemplo é o desenvolvido no artigo [Criar um aplicativo Scala autônomo para ser executado no cluster Spark no HDInsight](apache-spark-create-standalone-application.md). As etapas aqui pressupõem:

* Você já copiou o JAR do aplicativo para a conta de armazenamento associada ao cluster.
* Você está com a rotação instalada no computador em que está tentando essas etapas.

Execute as seguintes etapas:

1. Para facilitar o uso, defina as variáveis de ambiente. Este exemplo se baseia em um ambiente do Windows, revise as variáveis conforme necessário para seu ambiente. Substitua `CLUSTERNAME` e `PASSWORD` pelos valores apropriados.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Verifique se o Livy Spark está em execução no cluster. Podemos fazer isso obtendo uma lista de lotes em execução. Se você estiver executando um trabalho usando Livy pela primeira vez, a saída deverá retornar zero.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    Você deverá obter uma saída semelhante ao seguinte snippet:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observe que a última linha da saída informa **total:0**, o que sugere que não há lotes em execução.

1. Agora vamos enviar um trabalho em lotes. O snippet a seguir usa um arquivo de entrada (input.txt) para passar o nome do jar e o nome de classe como parâmetros. Se você estiver executando essas etapas em um computador Windows, o uso de um arquivo de entrada é a abordagem recomendada.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Os parâmetros no arquivo **input.txt** são definidos da seguinte maneira:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Você deverá ver uma saída semelhante ao seguinte snippet:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observe que a última linha da saída informa **state:starting**. Assim como, **id:0**. Aqui, **0** é a ID do lote.

1. Agora você pode recuperar o status desse lote específico usando a ID do lote.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Você deverá ver uma saída semelhante ao seguinte snippet:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Agora a saída mostra **state:success**, o que sugere que o trabalho foi concluído com êxito.

1. Se quiser, você pode excluir o lote.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Você deverá ver uma saída semelhante ao seguinte snippet:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    A última linha da saída mostra que o lote foi excluído com êxito. A exclusão de um trabalho, enquanto ele está em execução, também elimina o trabalho. Se você excluir um trabalho que foi concluído, com êxito ou não, essa ação excluirá por completo as informações sobre o trabalho.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Atualizações para a configuração do Livy começando com a versão do HDInsight 3.5

Os clusters HDInsight 3.5 e posteriores, por padrão, desabilitam o uso de caminhos de arquivos locais para acessar arquivos de dados de amostra ou jars. Incentivamos o uso do caminho `wasbs://` em vez disso, para acessar os jars ou os arquivos de dados de exemplo do cluster.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Enviar trabalhos da Livy para um cluster em uma rede virtual do Azure

Se você se conectar a um cluster do HDInsight Spark de dentro de uma Rede Virtual do Azure, você poderá se conectar diretamente à Livy no cluster. Nesse caso, a URL de ponto de extremidade da Livy é `http://<IP address of the headnode>:8998/batches`. Aqui, **8998** é a porta na qual a Livy é executada em um nó de cabeçalho do cluster. Para obter mais informações sobre como acessar serviços em portas não públicas, consulte [Portas usadas pelos serviços de Apache Hadoop no HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Próximas etapas

* [Documentação API REST Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)